# Re Module

## Regular expression

```text
import argparse
parser = argparse.ArgumentParser(description='import file')
parser.add_argument('-file', metavar='datafile', type=str, required=True,
help='plot data file')
args = parser.parse_args()
print(args.file)
```

## match\(\) and search\(\) difference

match checks for a match only at the beginning of the string, while search checks for a match anywhere in the string

```text
#! /depot/Python-3.5.2/bin/python
string_with_newlines = """something
someotherthing"""

import re

print (re.match('some', string_with_newlines)) # matches
print (re.match('someother', string_with_newlines)) # won't match
print (re.match('^someother', string_with_newlines, re.MULTILINE)) # also won't match          
print (re.search('someother', string_with_newlines)) # finds something
print (re.search('^someother', string_with_newlines, re.MULTILINE)) # also finds something      

m = re.compile('thing$', re.MULTILINE)

print( m.match(string_with_newlines)) # no match
print (m.match(string_with_newlines, pos=4)) # matches
print (m.search(string_with_newlines, re.MULTILINE)) # also matches
```

## group

```text
>>> m = re.match(r"(\w+) (\w+)", "Isaac Newton, physicist")
>>> m.group(0)       # The entire match
'Isaac Newton'
>>> m.group(1)       # The first parenthesized subgroup.
'Isaac'
>>> m.group(2)       # The second parenthesized subgroup.
'Newton'
>>> m.group(1, 2)    # Multiple arguments give us a tuple.
('Isaac', 'Newton')
```

