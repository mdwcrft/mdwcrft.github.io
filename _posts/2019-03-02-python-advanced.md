---
title: "Python #2: Advanced Functions"
date: 2019-02-01
tags: []
excerpt: "Decorators, generators, I/O, RegEx, Modules"
---


# Advanced Functions
- Do not use mutable objects as default values in a function (`y=[]`) as the object itself will be altered each time the function is run.  
- Functions that mutate their input values or change the state of other program areas are said to have **side effects** - this is generally discouraged as they can cause bugs.  
- A local function variable can be explicitly defined as global using `global <var>`.  

### *Args & **Kwargs
Args and Kwargs are used in functions to represent a variable number of inputs, they can be combined with normal input variables.  
```python
def my_func_mix(x, *args, y=5, **kwargs):
    print(args)
    print(kwargs)

my_func_mix(5, 1,2,3, y=10, z=4)
# (1, 2, 3)
# {'z': 4}
```

### Error Handling
Error handling or exception control is dealing with errors that may occur in a program without crashing the program itself. This can be done using `if <condition>: raise Exception` but more common is `try catch`.  
```python
try:
    x = 4/0
    print('Calculation successful')
except ZeroDivisionError:
    print('Zero Division Error')
except IndexError:
    print('Index Error')
except Exception as e:
    print('NO: {}'.format(e))

# Zero Division Error
```



### Decorators
Decorators are functions whose primary purpose is to wrap another function, they are denoted by **@**.  
```python
import datetime as dt

def wrapper(f):
    def callf(*args, **kwargs):
        start = dt.datetime.now()
        r = f(*args, **kwargs)
        end = dt.datetime.now()
        print(end - start)
        return r
    return callf

@wrapper
def add_five(x):
    return x+5

add_five(10)
# 0:00:01
# 15
```

### Generators
If a function uses the **yield** keyword then it is a generator, yield acts like `return`. Generators are different to functions as they have a `__next__` method which will return the next yield value when called. Generators act like lists in many ways but are more memory efficient as they generate values when needed.    
```python
def countdown(n):
    print('Counting down...')
    while n >= 0:
        print(n)
        yield n   # Like a return
        n -=1

g = countdown(10)

g.__next__()
# Counting down...
# 10
```
Run a second time the output will change;  
```python
g.__next__()
# 9
```


### Comprehensions
List and generator comprehensions are techniques to apply some function to every value in a list / generator.  
List comprehension:  
```python
my_list = [1,2,3,4,5]

def timestwo(x):
    return x*2

[timestwo(i) for i in my_list if i % 2 == 0]
# [4, 8]
```  
Generator comprehension:
```python
g = (2*i for i in my_list)
list(g)  # Explicitly tell generator to show as list
# [2, 4, 6, 8, 10]
```

### Lambda
Lambda is the Python keyword for an in-line function. The syntax is `lambda <input> : <output>`.  
```python
square = lambda x: x**2
square(5)
# 25
```

### Maps
Maps allow us to apply a function to every item in an iterable object  
```python
my_list = [1,2,3,4,5]
list(map(lambda x: x+3, my_list))
# [4, 5, 6, 7, 8]
```

### Zip
Zip is used to combine two lists into a dictionary type object  
```python
keys = ['Sea', 'Soil', 'Grass']
vals = ['blue', 'brown', 'green']

list(zip(keys, vals))
# [('Sea', 'blue'), ('Soil', 'brown'), ('Grass', 'green')]

dict(list(zip(keys, vals)))
# {'Sea': 'blue', 'Soil': 'brown', 'Grass': 'green'}
```


# File I/O

```python
import os

# Current directory
os.getcwd()
# 'C:\\Users\\admin\\code\\Python'

# List files in current directory
os.listdir()
# ['.ipynb_checkpoints', 'test_script.py.txt', 'Untitled.ipynb']

# List all jupyter notebooks in cwd
for file in os.listdir():
    if file.endswith('.ipynb'):
        print('{} is a Jupyter notebook'.format(file))
# Untitled.ipynb is a Jupyter notebook
```

Reading and writing to files can be done line wise or all at once
```python
# Read file all in one ('r')
filepath = r'C:\\Users\\admin\\Documents\\test.txt'
with open(filepath, 'r') as f:
    lines = f.readlines()
print(lines)
f.close()
# ['The quick brown fox\n', 'jumped over the lazy dog']

# Read file line wise
file_path = r'C:\\Users\\admin\\Documents\\test.txt'
f = open(file_path, 'r')
for line in f:
    print(line)
f.close()
# The quick brown fox
# jumped over the lazy dog

# Creates or overwrite file ('w')
file_path = r'C:\\Users\\admin\\Documents\\test2.txt'
f = open(file_path, 'w')
f.writelines('hello\n')
f.writelines('goodbye')
f.close()

# Append to file ('a')
file_path = r'C:\\Users\\admin\\Documents\\test.txt'
f = open(file_path, 'a')
f.writelines('and went to market\n')
```


# Documentation / Metadata

### Program structure
Special functions can be used to find the structure of the program and objects  
```python
# Check whether object is callable:
callable(len)
# True

# Check type (returns function as it is not bound to an instance of object)
type(Dog.bark)
# function

# Returns method as d is an instance of object Dog
d=Dog()
type(d.bark)
# method

type(os)
# module
```



### Documentation string
When a function is defined, the first line is often a string which is called the *documentation string*.  
```python
def add_five(x):
    '''
    This is the documentation string
    '''
    return x + 5

help(add_five)
# Help on function add_five in module __main__:

# add_five(x)
#     This is the documentation string
```

### Reference counting
References to an object can be counted using `sys.getrefcount(<var>)`. If an object has no references it will be collected by the **garbage collector** and freed from memory. An object with a single real reference will show a reference count of 2-3 as the `getrefcount` method itself creates references.  
```python
my_str = 'ggg'

import sys
sys.getrefcount(my_str)

# 2
```


`dir(my_object)` - Shows all methods available for my_object


# Regular Expressions
Regular Expressions (RegEx) are special codes used to search a string for matching characters or patterns. They can involve normal strings but also include characters for general cases of a type of element. The special characters are:    

~~~
General Matching
----------------
\d: digit
\w: word (digits count as words)
\s: space or tab

\D: Not digit
\W: Not word
\S: Not space or tab

Quanitifiers
------------
*  : 0 or more
+  : 1 or more
?  : 0 or 1
{n}: Exactly n

Wildcards
---------
. - Matches anything

Character sets
--------------
[ab]  : Match a or b
[a-b] : Match anything from a to b
[] :
~~~

An example for finding phone numbers of the form *07392-244-112* or *07221 222 456*:  

```python
import re

# Define RegEx
pattern = re.compile(r'\d{5}[-\s]\d{3}[-\s]\d{3}')

# Print list of results from finditer()
list(pattern.finditer(document))
```


# Modules

### PYODBC
PYODBC is a module that allows communication with an SQL server through Python. The following is a short intro with the basic commands to set up a connection and send queries to the server.  
```python
import pyodbc

# Create connection function
def connect():
    conn = pyodbc.connect(Trusted_Connection = 'yes',
                          driver = '{SQL Server}',
                          server = '.',
                          database = 'KubrickMonster')
    return conn

# Define connection alias
conn = connect()
```  

Once the connection is established any number of queries can be sent using a *cursor*. A cursor is a temporary working area to hold the query results.  

```python
# Define cursor for query
cursor = conn.cursor()

# Write SQL query as a Python string
query = '''
create table dbo.monsters(
    name varchar(20),
    home varchar(20),
    strength int)
'''

# Execute query
cursor.execute(query)

# Commit to server
conn.commit()
```  

Data can be inserted into a table from a Python list of tuples (for example) using special pyodbc placeholders `?`.  
```python
cursor = conn.cursor()

data = [('King Rat', 'London Underground', 8),
        ('Slenderman', 'Behind you', 1)]

for d in data:
    # ? is used as placeholder
    query = '''insert into dbo.monsters values (?,?,?)'''
    cursor.execute(query, d[0], d[1], d[2])
    
conn.commit()
```  

To retrieve data from a table we must use either `cursor.fetchall()` or `cursor.fetchone()`.  
When finished with a connection we should use conn.close() to close the connection.  
```python
cursor = conn.cursor()

query = '''select * from dbo.monsters'''
cursor.execute(query)
# Fetch all results
results = cursor.fetchall()
conn.commit()

# Close connection
conn.close()
# Print results
results
```