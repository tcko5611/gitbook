# Python



##  Basic

### Variables

#### Basic: int, float

* int : 10
* float : 10.0

#### String

* str : 'aa'

#### format

* string

  ```text
  '{2}, {1}, {0}'.format(*'abc') to 'c, b, a'
  ```

* argument

  ```text
  >>> coord = {'latitude': '37.24N', 'longitude': '-115.81W'}
  >>> 'Coordinates: {latitude}, {longitude}'.format(**coord)
  'Coordinates: 37.24N, -115.81W'
  ```

* float

  ```text
  >>> '{:+f}; {:+f}'.format(3.14, -3.14)  # show it always
  '+3.140000; -3.140000'
  ```

#### functions

* str.split\(s\), len\(str\), str.strip\(\), str.rstrip\(\), str.lstrip\(\)
* str.find\(sub\), str.rfind\(sub\), str.index\(sub\), str.rindex\(sub\)
* s1.join\(s2\)

#### list

* a = \[1, 2, 3\]
  * a.insert\(2, 4\) \# \[1,2,4,3\]
  * a.append\(5\) \# \[1, 2,4,3,5\]
  * a.pop\(\) \# 5
  * a.index\(4\) \# 2
  * a.remove\(3\) \# \[1, 2, 4, 5\]
  * a.reverse\(\) \# \[5, 4, 2, 1\]
  * a.sort\(\) \# \[1, 2, 4, 5\]
  * del a\[0\] \# \[2, 4, 5\]
  * for b in a :...

#### dict

* a = {'a': 10, 'b': 20}
  * for k, v in a.items\(\): de...
  * for k in a.keys\(\): ...
  * for k in a.keys\(\): ...
  * a\['c'\] = 10

#### tuple

* a = \('a', 'b'\)

#### set

* a = {1, 2}
  * for b in a :...

### Input

* read from command line
  * use sys.argv

    ```text
    import sys
    print "This is the name of the script: ", sys.argv[0]
    print "Number of arguments: ", len(sys.argv)
    print "The arguments are: " , str(sys.argv)
    ```

  * use argparse module

    ```text
    import argparse
    parser = argparse.ArgumentParser(description='import file')
    parser.add_argument('-file', metavar='datafile', type=str, required=True,
                        help='plot data file')
    args = parser.parse_args()
    print(args.file)
    ```
* read from stdin

  ```text
  import sys
  for line in sys.stdin:
    new_list = [int(elem) for elem in line.split()]
    list_of_lists.append(new_list)
  ```

* read from file

  ```text
  with open(fname) as f:
    content = f.readlines()
  with open(fname) as f:
    for line in f:
      ...
  ```

### Output

* output to stdout

  ```text
  print('{} {}'.format('aaa','bbb')) # aaa bbb
  print('{:4d}{:4d}'.format(123,456)) # 123 456
  print('{:6.2f}'.format(3.141592654)) #  3.14
  ```

* output to file

  ```text
  f = open(fname, 'w')
  f.write(str)
  ```

### if statements

```text
if x < 0:
  x = 0
  print('Negative changed to zero')
elif x == 0:
  print('Zero')
elif x == 1:
  print('Single')
else:
  x =0
```

### for statements

```text
for w in words:
  print(w, len(w))
```

### Module

fibo.py

```text
# Fibonacci numbers module

def fib(n):    # write Fibonacci series up to n
    a, b = 0, 1
    while a < n:
        print(a, end=' ')
        a, b = b, a+b
    print()

def fib2(n):   # return Fibonacci series up to n
    result = []
    a, b = 0, 1
    while a < n:
        result.append(a)
        a, b = b, a+b
    return result
```

code

```text
import fibo
>>> fibo.fib(1000)
0 1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987
>>> fibo.fib2(100)
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89]
>>> fibo.__name__
'fibo'
```

### Class

```text
base, form = uic.loadUiType('mainwindow.ui')
class MainWindow(base, form):
  def __init__(self, parent = None, jsonFile = None):
    super(MainWindow, self).__init__(parent)
  ....
```

**init** and **call**

```text
class Foo:
    def __init__(self, a, b, c):
        # ...

x = Foo(1, 2, 3) # __init__
```

**call**

```text
class Foo:
    def __call__(self, a, b, c):
        # ...

x = Foo()
x(1, 2, 3) # __call__
```

### compile python file

```text
#! /depot/Python-3.5.2/bin/python
import sys
import py_compile
#import argparse
if __name__ == '__main__' :
#    parser = argparse.ArgumentParser(description='compile python file')
#    parser.add_argument('-i', '--input', metavar='input_file', type=str, required=True, \
#                        help = 'input python file'    )
#    parser.add_argument('-o', '--output', metavar='output_file', type=str, required=True,# \
#                        help = 'output compiled python file')
#    args = parser.parse_args()
    py_compile.compile(sys.argv[1], sys.argv[2])
```

