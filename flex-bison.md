# Flex, Bison



## Example for simple parser

Suppose we want to parse the following text

```text
sNaZZle 1.3
type foo
type bar
type bas
0 0 10 5        foo
20 10 30 20     foo
7 8 12 13       bas
78 124 100 256  bar 
end
```

And we to use parser as exprParse, then we can use the following code to do:

### main.cpp

```text
#include <cstdio>
#include <iostream>
using namespace std;
extern int exprparse(); /* parse function from bison */
extern FILE *exprin; /* scanner input */

int main(int, char**) {
  /* open file for exprin */
  FILE *myfile = fopen("test.txt", "r");
  // Make sure it is valid:
  if (!myfile) {
    cout << "I can't open a.snazzle.file!" << endl;
    return -1;
  }
  // Set Flex to read from it instead of defaulting to STDIN:
  exprin = myfile;

  // Parse through the input:
  exprparse();

}
```

### scanner.ll

```text
%{
  #include <cstdio>

  #include "tokens.h"  // to get the token types from Bison
  int line_num = 1;
  #define yylval exprlval // define for tokens.h will not declare yylval
%}
%%
[ \t]           ;
sNaZZle { return SNAZZLE; }
type { return TYPE; }
end { return END; }
\n { ++line_num; return ENDL; }
[0-9]+\.[0-9]+    { yylval.fval = atof(yytext); return FLOAT; }
[0-9]+            { yylval.ival = atoi(yytext); return INT; }
[a-zA-Z0-9]+      { yylval.sval = strdup(yytext); return STRING; }
. ;
%%
/* this function neeed to define return non-zero when no more input will needed */
int yywrap()
{
  return 1;
}
```

Use the following command to generate the scanner we want

```text
flex --ouput=scanner.cpp --prefix=expr scanner.ll
```

### paser.yy

```text
%{
  #include <cstdio>
  #include <iostream>
  using namespace std;

  // Declare stuff from Flex that Bison needs to know about:
  extern int yylex();
  extern int yyparse();
  extern FILE *yyin;

  void yyerror(const char *s);
  extern int line_num;
%}

// Bison fundamentally works by asking flex to get the next token, which it
// returns as an object of type "yystype".  Initially (by default), yystype
// is merely a typedef of "int", but for non-trivial projects, tokens could
// be of any arbitrary data type.  So, to deal with that, the idea is to
// override yystype's default typedef to be a C union instead.  Unions can
// hold all of the types of tokens that Flex could return, and this this means
// we can return ints or floats or strings cleanly.  Bison implements this
// mechanism with the %union directive:
%union {
  int ival;
  float fval;
  char *sval;
}

// Define the "terminal symbol" token types I'm going to use (in CAPS
// by convention), and associate each with a field of the %union:
%token SNAZZLE TYPE
%token END ENDL
%token <ival> INT
%token <fval> FLOAT
%token <sval> STRING

%%
// This is the actual grammar that bison will parse, but for right now it's just
// something silly to echo to the screen what bison gets from flex.  We'll
// make a real one shortly:
snazzle:
header template body_section footer {
  cout << "done with a  snazzle file!\n";
}
;

header:
SNAZZLE FLOAT ENDLS {
  cout << "reading a snazzle file version: " << $2 << endl;
}
;

template:
typelines
;

typelines:
typelines typeline
| typeline
;

typeline:
TYPE STRING ENDLS {
  cout << "new defined snazzle type: " << $2 << endl;
  free($2);
}
;

body_section:
body_lines
;

body_lines:
body_lines body_line
| body_line
;

body_line:
INT INT INT INT STRING ENDLS {
  cout << "new snazzle: " << $1 << $2 << $3 << $4 << $5 << endl;
  free($5);
}
;

footer:
END ENDLS
;

ENDLS:
ENDLS ENDL
| ENDL
;
%%
/* define this file when error occur */
void yyerror(const char *s) {
  cout << "EEK, parse error!  In Line number: " << line_num
       << "! msg :" << s << endl;
  // might as well halt now:
  exit(-1);
}
```

Use the following command to generate parser cpp file

```text
bison --defines=tokens.h --output parser.cpp --name-prefix=expr parser.yy
```

## include syntax in file

For file like veriloga or c, there is a include statement that need to read another file in that point, we can use the following scheme to read the cotain of include file.

```text
%{
#include <math.h>
#include <vector>
#include <iostream>
  using namespace std;
  /* vector to store buffer states, act like stack*/
  std::vector<YY_BUFFER_STATE> include_stack; 
%}
%x incl /* incl condition */
%%

include { BEGIN(incl); /* start incl condition */ }

[a-z]+ {ECHO; /* just echo test code */ 
[^a-z\n]*\n? { ECHO; /* just echo test code */ }
<incl>[ \t]* /* eat the whitespace */
<incl>[^ \t\n]+ { /* get the file name in yytext */
  /* push YY_CURRENT_BUFFER to stack */ 
  include_stack.push_back(YY_CURRENT_BUFFER);
  yyin = fopen(yytext, "r");
  if (!yyin) {
    cout << "error for open file:" << yytext << endl;
    exit(1);
  }
  /* swith to new BUFFER */
  yy_switch_to_buffer(yy_create_buffer(yyin, YY_BUF_SIZE));
  /* from the beginning of the buffer set condition to initial */
  BEGIN(INITIAL);
 }
<<EOF>> {
  /* when encounter end of file, check if stack has buffer, if not then complete,
     else get the next state from stack and clear the last buffer
  */
  if (include_stack.empty()) {
    yyterminate();
  }
  else {
    yy_delete_buffer(YY_CURRENT_BUFFER);
    yy_switch_to_buffer(include_stack.back());
    include_stack.pop_back();
  }
  }
%%
int main (int argc, char *argv[])
{
  yyin = fopen("test.txt", "r");
  while(yylex());
  return 0;
}
```

