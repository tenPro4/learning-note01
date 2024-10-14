[Git Reference](https://github.com/gitdagray/python-course.git)
[Youtube Reference](https://www.youtube.com/watch?v=qwAFL1597eM&t=20695s&pp=ygUOcHlob24gdHV0b3JpYWw%3D)

- [Tips](#tips)
  - [Enum](#enum)
  - [Exit program](#exit-program)
  - [f\_strings](#f_strings)
  - [import specific module all elements](#import-specific-module-all-elements)
  - [Find indexs from array](#find-indexs-from-array)
  - [1 line for loop](#1-line-for-loop)
- [Value Type related example(00:48:00)](#value-type-related-example004800)
- [Trim in python](#trim-in-python)
- [Get character by index](#get-character-by-index)
- [Stack](#stack)
- [Lists](#lists)
- [Tuple](#tuple)
- [Dictionary](#dictionary)
- [Sets](#sets)
- [Loops](#loops)
- [Functions](#functions)
- [Recursion](#recursion)
- [Global Scope](#global-scope)
- [modules](#modules)
- [lambda](#lambda)
- [Slicing operation](#slicing-operation)
- [Class](#class)
- [Exception \& Errors](#exception--errors)
- [pip install \& venv](#pip-install--venv)
- [File operation](#file-operation)



## Tips

### Enum
```python
class EnumClass(Enum): # Enum need import
    Paper=1
    Rock=2
```

### Exit program
```python
sys.exit("Exit message here") #sys need import
```

### f_strings
```python
message = f"\n{val1} is sum of {val2} and {val3}"
```

### import specific module all elements
```python
from account_module import *

Dave = BankAccount(1000,"Dave")
```

### Find indexs from array
```python
my_list = [1, 2, 3, 4, 5, 5, 6, 2]
value_to_find = 2

indices = [index for index, value in enumerate(my_list) if value == value_to_find]
print(indices)  # Output: [1, 7]
```

### 1 line for loop
```python
import random

salary_list = [random.randint(5000,10000) for _ in range(100000)]
```

## Value Type related example(00:48:00)
```python
first = "Dave"

#print value type,output:<class 'str'>
print(type(first))

#classify value type,output: True or False
print(isinstance(first,str))

# constructor function
pizza = str("Pepperoni")

# Casting a number to a string
decade = str(1980)

# Multiple Lines,same like <pre> in web
multiline = '''
Hey,how are you?
            All good?

Yes
'''
```

## Trim in python
```python
spaceWord = "   djdks   "

# Remove white space
print(spaceWord.strip())
```

## Get character by index
```python

hello = "hello"

# get first character. output: h
print(hello[1])

# get last character. output: 0
print(hello[-1])

#get range characters
print(hello[1:])
```

## Stack
```python
stack = [-1]

stack.append(4)
stack.append(3)
stack.append(6)

# stack
# -----
# | -1 |
# |  4 |
# |  3 |
# |  6 |
# -----

stack.pop()

# stack
# -----
# | -1 |
# |  4 |
# |  3 |
# -----

#print last index
print(stack[-1])
#output: 3
```


## Lists
```python

users = ["h1","h2","h3"]

## add item list
users += "h4"
#or add data group
users.extend(["h5","h6"])
#or add base index
users.insert(0,"h0")
#or add to last index
users.append("h7")

## get index
print(users.index("h3"))

users.remove("h0")
#or
delete users[0]
# or clear all items
users.clear()

# return and remove last index of item
users.pop()

#sort
users.sort()
#sort by capitlize
users.sort(key=str.lower)

numbers = [22,35,11,2,23]

#output:23,2,11,35,22
numbers.reverse()

#output:2,11,22,23,35
numbers.sort(reverse=True)
#or
sorted(numbers,reverse=True)

#copy array
clone = numbers.copy()
#or
clone = list(numbers)
#or
clone = numbers[:]
```

## Tuple
Different tuple to list is, the ordering and data update are disabled inside tuple.
```python

mt = tuple(('Dave',42,True))
#or
mt = ('Dave',42,True)

#get value from tuple
mt2 = (22,33,41,21)
(one,two,*rest) = mt2

#output:22
print(one)
#output:23
print(two)
#output:41,21
print(rest)
```

## Dictionary
key-pair list
```python
band = {
    "vocals":"Plant",
    "guitar":"Page"
}
#or
band = dict(vocals="Plant",guitar="Page")

# get item
print(band["vocals"])
#or
print(band.get("vocals"))

#list all keys
print(band.keys())
#list all values
print(band.values())

#list keys and items
print(band.items())

#update value
band["vocals"] = "Coverdale"
#or
band.update({"bass":"JPJ"})

#remove items
band.pop("bass")
#or
band.popitem() # tuple
#or
del band["bass"]

#empty dict
band.clear()
#or
del band

#copy dict
band2 = band #wrong example,create a reference
#correct example:
band2 = band.copy()
#or
band2 = dict(band)
```

## Sets
No duplicate data allowed
```python

nums = {1,2,3,4}
#or
nums = set((1,2,3,4))

#add
nums.add(5)

#add elements, this can use with lists,tuples and dictionaries too
nums2 = {5,6,7}
nums.update(nums2)

# Merge two sets to create new set
newnums = nums.union(nums2)

# Get only duplicate
nums.intersection_update(nums2)

# Get all except duplicates
nums.symmetric_difference_update(nums2)
```

## Loops
```python

value =1

while value <= 10:
    value +=1
    print(value)
else:
    print("Value is now equal 11")

names = ["D","B","C"]
for x in names:
    print(x)

for x in "word":
    print(x)

for x in range(4):   # x start at 0,stop before 4,output:0,1,2,3
    print(x)

for x in range(2,4):   # x start at 2,stop before 4
    print(x)

#start at 5, stop before 100, each loop increase 6
for x in range(5,100,6)
    print(x)
```

## Functions
```python
def hello_world():
    print("hw")

def sum(num1=0,num2=0):
    if(type(num1) is not int or type(num2) is not int):
        return 0
    
    return num1+num2

def multi_items(*args):
    print(args)

def multi_named_items(**kwargs):    # kwargs can be any name
    print(args)

hello_world()
total = sum(1,2)

multi_items("D","BN","K")

multi_named_items(first="D",last="G")
```

## Recursion
```python
# call function inside looping until condition meet
def add_one(num):
    if(num >= 9):
        return num+1

    total = num+1
    print(total)

    return add_one(total)
```

## Global Scope
```python
count = 1

def any_function():
    name = "h"

    # this is internal scope variable, which wont affect count outside of function
    count = 2 

    # this will overwrite and update count outside of function
    global count
    count = 2

    def child_any_function():
        #this will overwrite and update name variable of parent function
        nonlocal name
        name = "c"
    
    child_any_function()
```

## modules
[All python module index](https://docs.python.org/3/py-modindex.html)
Reference Timeframe: 4.49:00

## lambda
```python
#lambda version
squared = lambda num : num * num

#function version
def squared(num): return num*num

#lamba 2
sum = lambda a,b : a+b

#function 2
def add(a,b): return a+b

# lambda 3
def funcBuilder(x):
    return lambda num : num + x

addFunc1 = funcBuilder(10)
addFunc2 = funcBuilder(20)

#output: 17
print(addFunc1(7))

#output: 22
print(addFunc2(2))


#lambda 4

nums = [1,2,3,4,5,6,7,8]

squared_nums = map(lambda num: num*num,nums)
print(list(squared_items))

# output:
# [1,4,9,16...]

#lambda 5 filter

odd_nums = filter(lambda num : num %2 != 0,nums)
print(list(odd_nums))

# output:
# [1,3,5...]

# lambda 6 reduce
from functools import reduce

total = reduce(lambda acc,curr : acc+curr,nums)

# last params 0 mean default value for acc
total = reduce(lambda acc,curr : acc+curr,nums,0)
```

## Slicing operation
The slicing syntax is [start:stop:step].
If no values are provided for start, stop, and step, the defaults are used:

**start**: The beginning of the sequence (index 0).
**stop**: The end of the sequence.
**step**: The step size (1).Negative value mean moving backwards

```python
strs = "abcdefg"

#output: abcdefg
result = strs[::]

#output: gfedcba
result = strs[::-1]

#output: bc
result = strs[1:3]

#output: abc
result = strs[:3]
```

## Class
```python
class Vehicle:
    def __init__(self,make,model):
        self.make = make
        self.model = model

    def __repr__(self):
        return f'{self.make} {self.model}'

    def moves(self):
        print('move...')

    def get_make_model:
        return f"{self.make} {self.model}"

my_car = Vehicle('Tesla','Model 3')

print(my_car.make,my_car.model)
print(my_car.get_make_model())

# this will print __repr__ output
print(mycar)

# inherit from vehicle class
class Airplan(Vehicle):
    pass

my_plane = Airplan('Cessa','Skyhawk')
print(my_plane.get_make_model())

# ===========================

# you can rewrite specific function from inherit class
class Airplan(Vehicle):
    def moves(self):
        print("fly...")

# adding new param to init with super()
class Airplan(Vehicle):
    #instead doing this:
    # def __init__(self,make,model,p_id):
    #     self.make = make
    #     self.model = model
    #     self.p_id = p_id

    # recommend doing this:
    def __init__(self,make,model,p_id):
        super().__init__(make,model)
        self.p_id = p_id


my_plane = Airplan('Cessa','Skyhawk',1)
my_plane.moves()    # output: fly...


## Polymorphism
for v in (my_car,my_plane):
    v.get_make_model()
```

## Exception & Errors
```python

try:
    #something
except:
    print('error..')    

# Catch specific error
try:
    #something
except NameError:
    print('error..')    
except ZeroDivisionError:
    print('error2..')
except Exception as error:
    print(error)
else:
    print('no errors')
finally:
    print('finally...')

# or you can raise error (same throw Exception in netcore)
try:
    x = '123'

    if type(x) not int:
        raise Exception('custom exception...')
except:
    print('error...')

# ====================

# Custom Exception Class
class CustomException(Exception):
    pass

try:
    raise CustomException("custom msg...")
except:
    print('eerrror')
```

## pip install & venv

```bash
# create venv space
py -m venv .venv

# activate venv
source .venv/Scripts/activate

# deactivate venv
deactivate

# update pip to latest
py -m pip install -U pip

# list all install modules
pip list
```

## File operation
r = Read
a = Append
w = Write
x = Create

```python
f = open("names.txt","r")
# or
f = open("names.txt")
print(f.read())
print(f.read(4)) # read first 4 characters

print(f.readline()) # read first line
print(f.readline()) # read second line if repeat call readline()
# or
for line in f
    print(line)

# it important to close the file when done finish
f.close()

# Append - create the file if it doesn't exist
f = open('names.txt',"a")
# write content to file. be note it was inserting but not replacing existing content. Only in 'Append' mode.
f.write("Neil")
f.close

# Write (overwrite) - it also create the file if it doesn't exist
f = open('names.txt',"w")
f.write("Only me,existing content get off")
f.close()

# Creates files, but return error if file exists
import os

if not.os.path.exists("dave.txt"):
    # create if not exist
    f = open("dave.txt","x")
    f.close()
else: # if file exist then remove it
    os.remove("dave.txt")

# Rewrite other_name content to names.txt
with open("other_name.txt") as f:
    content = f.read()

with open("names.txt","w") as f:
    f.write(content)
```