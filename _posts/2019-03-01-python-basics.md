---
title: "Python #1: Basics"
date: 2019-02-01
tags: []
excerpt: "Types, Dictionaries, Sets, OOP"
---

This is the first in a series of Python notes I made during the [Kubrick](https://kubrickgroup.com/) Data Engineering training course.    
[#1: Basics]({{ site.url }}{{ site.baseurl }}/python-basics)  
[#2: Advanced]({{ site.url }}{{ site.baseurl }}/python-advanced)  
[#3: Scraping]({{ site.url }}{{ site.baseurl }}/python-scraping)  
[#4: Pandas]({{ site.url }}{{ site.baseurl }}/python-pandas)  
[#5: Matplotlib]({{ site.url }}{{ site.baseurl }}/python-matplotlib)  

---
# Sets
Sets are used to store an unordered collection of unique objects. This means they cannot be indexed and will aggregate non-unique objects into one. It is also mutable, like a list and unlike a tuple.

```python
my_set = set([1,1,1,2])
my_set
# {1, 2}

my_set[0]
# TypeError: 'set' object does not support indexing
```  

Sets can be altered and manipulated with special methods  
```python
s1 = set([1,2,3])
s2 = set([2,3,4])

# .add() adds to the set
s1.add(4)
s
# {1, 2, 3, 4}

# .union() finds distinct values from both sets
s1.union(s2)
# OR
s1 | s2
# {1, 2, 3, 4}

# .intersection finds distinct elements in common
s1.intersection(s2)
# OR 
s1 & s2
# {2, 3, 4}

# .difference finds elements in s1 not in s2
s1.difference(s2)  
# OR 
s1 - s2
# {1}

# .issubset checks for subset
s1.issubset({0,1,2,3,4})
# True

# .isdisjoint check if any elements are in common
s1.isdisjoint({4,5,6})
# True
```

# Dictionaries
Dictionaries are 'mapping objects'; associative arrays with objects indexed by keys. They are also mutable, like a list or set.  

```python
my_dict = {  'FirstName':'John'
            ,'LastName':'Appleseed'
            ,'Age':50}

my_dict.keys()
# dict_keys(['FirstName', 'LastName', 'Age'])

my_dict.values()
# dict_values(['John', 'Appleseed', 50])

my_dict.items()
# dict_items(['FirstName':'John', 'LastName':'Appleseed', 'Age':50])

my_dict['Age']
# OR
my_dict.get('Age', 'Message if not found')
# 50

del my_dict['Age']
my_dict
# {'FirstName':'John', 'LastName':'Appleseed'}

my_dict.update({'LastName':'Johnson'})
my_dict
# {'FirstName':'John', 'LastName':'Johnson'}
```

# Object Oriented Programming
Object oriented programming (OOP) in Python is a way of writing code that is focussed on 'objects' and relationships. The three principles of OOP are:  
- Inheritance  
- Polymorphism  
- Data encapsulation  

**Classes** allow us to define templates for new object types. A class can have **attributes** and **methods** of different types.  
```python
class Dog():
    # __init__ is run every time a new instance is created & handles initialisation
    def __init__(self, age): 
        # Attributes are qualities of a object, they can be explicit or derived from an input and are defined in the __init__ function of the object
        self.colour = 'Brown'
        self.age = age

    # Methods are functions specific to a class    
    def bark(self):
        print('Woof!')
        
    def age_in_dog_years(self):
        return self.age * 7

d = Dog(15)]
d.bark()  # Methods have curly brackets
# Woof!
d.colour  # Attributes have no brackets
# 'brown'
```

### Inheritance
'Child' classes can 'inherit' qualities from one or more 'base/super' classes.  
```python
# State the base class as input
class SmallDog(Dog):
    def __init__(self, age, colour):
        Dog.__init__(self, age)  # State the base class & inputs
        self.colour = colour  # New derived attribute
        
    # Something only a 'SmallDog' can do    
    def yap(self):
        print('yap yap yap')
        
    # Overwrite the Dog.bark() method
    def bark(self):
        print('yip yip')

sd = SmallDog('Blonde', 4)
sd.bark()
# yip yip
```  

### Polymorphism
Polymorphism refers to the ability to use an instance without referring to its type.  
```python
def add_stuff(x,y):
    return x + y
```  

The function above will work with any `x` and `y` data types that contain the `__add__()` method. This includes ints, floats, strings, & lists.

### Data encapsulation
Some attributes may need to be hidden from the end user (passwords for example), this can be done using data encapsulation. Any attribute can be set as private using the `__` prefix.  
```python
class BankAccount():
    def __init__(self, password):
        self.__password = password  # __password is a private attribute
        self.balance = 0
        
    def deposit(self, amount):
        self.balance += amount

ba = BankAccount('password123')
ba.__password
# AttributeError: 'BankAccount' object has no attribute '__password'
```

### Static & Class methods
**Static** methods are not reliant on the class or an instance, they could be run as independent functions.  
**Class** methods are not reliant on any particular instance, they are entirely related to a class.  

```python
class Dog():
    counter = 0 # counter is a class variable
    
    def __init__(self):
        self.colour = 'Brown'

    @staticmethod  # Not reliant on instance or class, could be run independently (No self)
    def bark(self):
        print('Woof!')
        
    @classmethod   # Not reliant on instance, related entirely to the class
    def increment_counter(cls):
        cls.counter += 1
```

Using the `.increment_counter()` method will return the counter value whatever instance it is applied to.  
```python
d1 = Dog()
d2 = Dog()
d1.increment_counter()
print(d1.counter)
print(d2.counter)
# 1
# 1
```

### Properties
A property is a type of attribute that computes its value only once it is accessed. It is defined using @property.
```python
class Circle():
    def __init__(self, radius):
        self.radius = radius
        
    @property
    def area(self):
        a = self.radius**2*3.14159
        return a
```

---
# Objects & Types
Objects in python are data points stored in memory. Every object has an identity, type, & value which can all be accessed with functions.  

When an object is created its ID and type are fixed, the value can only be changed if it is a mutable object  
- **Immutable**: int, float, tuple, complex, set  
- **Mutable**: list, dictionary  

When variables assigned to immutable objects are given a new value they will create new objects in memory with new ID's. Mutable objects can have their value change and keep the same ID.  

```python
x = 3.2

id(x)
# 140719644607552

type(x)
# float
```

If two variables are assigned to exactly the same value then they will point to the same object in memory despite having different names, the ID's will then be the same. This only happens for immutable objects. 'is' is used to compare ID's whereas '==' is used to compare values.    
```python
a = 5
b = 5
if a is b:
    print('IDs are equal')

if a == b:
    print('Values are equal')
```  

Mutable and immutable objects also behave differently when explicitly assigning a variable value to another with '='. For `a=b`:  
- If 'b' is immutable then 'a' is a copy of 'b'  
- If 'b' is mutable then 'a' refers to the same object 'b', to make a new object use `.copy()`  

```python
# Using '=' makes variables reference the same object
list1 = [1,2,3]
list2 = list1

list2.append(999)

print(list1)
print(list2)
# [1, 2, 3, 999]
# [1, 2, 3, 999]

# Using .copy() creates a new object with the same values
list1 = [1,2,3]
list2 = list1.copy() 

list2.append(999)

print(list1)
print(list2)
# [1, 2, 3]
# [1, 2, 3, 999]
```  

The case where .copy() does not work this way is if you're working with a 'list-of-lists', in which case .copy() will make a **shallow copy** which copies the references to the inner objects rather than the values of those objects. To do a full value copy, use the **deep copy** module.  