---
{"dg-publish":true,"dg-path":"Cheat sheets/Python cheat sheet.md","permalink":"/cheat-sheets/python-cheat-sheet/","tags":["language/python"]}
---


# General

- comments use `#`
- variables and functions are named using snake_case
- variables don't need to be explicitly declared, just assign to them
- declare/assign multiple variables at once:

```python
i = j = 0

a, b = 3, 4
a, b = b, a
```

- print a variable's type:

```python
print(type(a))
```

- `None` is the equivalent of `null`

- the `pass` keyword does nothing, can be used as a placeholder or in places where empty code isn't allowed (like an if block)

```python
if x >= 10:
    pass
else:
    print('x is less than 10')
```

## Equality

- `==` compares by value, `is` compares by reference
    - don't use `is` for primitives, as it can give inconsistent answers because of implementation caching details
- the opposites are `!=` and `is not` respectively
- use `is` or `is not` to compare with `None`, since `None` is a singleton

```python
a = [1, 2, 3]
b = [1, 2, 3]

print(a == b) # true
print(a is b) # false
```

- `nan != nan`, just like JavaScript

## Type Checking

```python
a: int

def add(a: float, b: float):
    return a + b
```

- `str` for string, `bool` for Boolean
- collection types are declared like this: `list[int]` (Python 3.9+)
- mappings must have key and value types declared: `dict[str, float]` (Python 3.9+)
- fixed size tuples should specify the type of each element: `tuple[int, str, str]` (Python 3.9+)
    - for variable size tuples use one type plus an ellipsis: `tuple[int, ...]`
- union types are supported using `|` (Python 3.10+) or `Union[X, Y]`
- declare optionals using `Optional[X]` (same as `X | None`)
    - `Optional` must be imported from `typing`
- type checking needs to be enabled in VSCode (`python.analysis.typeCheckingMode`)

## Assertions

- will throw a runtime error if not satisfied

```python
x = 'abc'
assert x is not None

x = None
assert x is not None # errors
```

# Data Types

## Booleans

- boolean `True` and `False` are capitalized
- to invert a boolean, use `foo = not foo`

## Numbers

- `int` and `float` are separate types
    - declare a `float` by adding a decimal (ex. `7.0`) or the float constructor (`float(7)`)
- `max` and `min` built-in functions return the max/min of two or more numbers
- `//` divides two numbers and floors the result to return an integer
- you can negate variables, ex. `foo = -bar`
- `x++` and `x--` aren't supported - use `x += 1` or `x -= 1` instead
- `nan` is the equivalent of `NaN`

## Lists (Arrays)

- lists are 0-indexed
- add items using `append` - takes one at a time

```python
mylist = []
mylist.append(1)
mylist.append(2)
mylist.append(3)

print(mylist[1]) # 2
# print(mylist[10]) # error
```

- use `len(list)` to get the length of a list

```python
print(len(mylist)) # 3
```

- an error is thrown if you try to access an item past the end of the list

```python
test = [1, 2]
# print(test[3]) # IndexError!
```

- use `in` to check if an item exists in a list

```python
if 3 in [1, 3, 5]: print('Number found')
```

### Join and repeat

- join lists using `+`

```python
even_numbers = [2, 4]
odd_numbers = [1, 3]

print(odd_numbers + even_numbers) # [1, 3, 2, 4]
```

- repeat lists using `*`

```python
numbers = [1, 2]
print(numbers * 3) # [1, 2, 1, 2, 1, 2]
```

- join an iterable containing only strings
    - will error if the iterable contains other types (not automatically cast)

```python
mylist = ['a', 'b', 'c']
print('-'.join(mylist)) # 'a-b-c'
```

### Slice

- works on strings and lists

```python
string = "Hello world!"

print(string[3:7]) # lo w
print(string[-1]) # !
print(string[-4:]) # rld!
print(string[0:5:2]) # Hlo ([start:stop:step])
print(string[::-1]) # !dlrow olleH (reverses the string)
```

### Spread

- spread lists using `*`

```python
def divide(a, b):
	return a / b

nums_list = [6, 2]

print(divide(*nums_list)) # 3.0
```

### Filter

- filter functions take one argument and return a boolean
- returns a generator, use the list constructor to turn it into a list

```python
scores = [66, 90, 68, 59, 76, 60, 88, 74, 81, 65]

def is_A_student(score):
    return score > 75

over_75 = list(filter(is_A_student, scores))
```

- using lambda functions:

```python
dromes = ("demigod", "rewire", "madam",
          "freer", "anutforajaroftuna", "kiosk")

palindromes = list(filter(lambda word: word == word[::-1], dromes))
```

### Map

- returns a generator, use the list constructor to turn it into a list

```python
my_pets = ['alfred', 'tabitha', 'william', 'arla']
uppered_pets = list(map(str.upper, my_pets))
```

- the `map` function can take multiple arguments to combine multiple iterables

```python
circle_areas = [3.56773, 5.57668, 4.00914, 56.24241, 9.01344, 32.00013]
# `circle_areas` and `range(1, 7)` are the two iterables passed to `round`
result = list(map(round, circle_areas, range(1, 7)))
```

- `map` will stop executing if there are no more elements in any of the iterables

```python
result = list(map(round, circle_areas, range(1, 3)))
print(result) # [3.6, 5.58]
```

### Zip

- each position will contain a tuple of the input elements
- returns a generator, use the list constructor to turn it into a list

```python
my_strings = ['a', 'b', 'c', 'd', 'e', 'f', 'g']
my_numbers = [1, 2, 3, 4, 5]

results = list(zip(my_strings, my_numbers))
print(results) # [('a', 1), ('b', 2), ('c', 3), ('d', 4), ('e', 5)]
```

- identical to either of the following:

```python
def my_zip(x, y):
    return (x, y)
results = list(map(my_zip, my_strings, my_numbers))

results = list(map(lambda x, y: (x, y), my_strings, my_numbers))
```

### Reduce

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]

def custom_sum(first, second):
    return first + second

result = reduce(custom_sum, numbers)
print(result) # 15
```

- provide an initial value as the third argument

```python
resultWithInitial = reduce(custom_sum, numbers, 10)
print(resultWithInitial) # 25
```

## Strings

- single & double quotes are identical except for escaping
- repeat strings using `*` : `'hello' * 2` = `'hellohello'`

### String functions

```python
string = "Hello world!"

print(len(string)) # 12
print(string.index('o')) # 4
print(string.count('l')) # 3

print(string.upper()) # HELLO WORLD!
print(string.lower()) # hello world!

print(string.startswith('Hello')) # True
print(string.endswith('asdf')) # False

print(string.split(' ')) # ['Hello', 'world!']
```

### String formatting

- `%s`: String (or any object with a string representation, like numbers)
- `%d`: Integers
- `%f`: Floating point numbers
- `%.<number of digits>f`: Floating point numbers with a fixed amount of digits to the right of the dot
- `%x/%X`: Integers in hex representation (lowercase/uppercase)

```python
name = "John"
age = 23
pi = 3.14159
myhex = 255
print("Hello, %s!" % name) # Hello, John!
print("%s is %d years old." % (name, age)) # John is 23 years old.
print("%f %.2f" % (pi, pi)) # 3.141590 3.14
print("%x %X" % (myhex, myhex)) # ff FF
```

### Formatted string literals (f-strings)

- insert objects, expressions, or format specifiers in the curly brackets

```python
a = 5
b = 10

f'{a} + {b} = {a + b}' # '5 + 10 = 15'
```

#### Format specifiers

<div class="rich-link-card-container"><a class="rich-link-card" href="https://realpython.com/python-format-mini-language/#understanding-the-python-format-mini-language" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://files.realpython.com/media/Pythons-Format-Specification-Mini-Language_Watermarked.96cec7483e05.jpg')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Python's Format Mini-Language for Tidy Strings – Real Python</h1>
		<p class="rich-link-card-description">
		In this tutorial, you'll learn about Python's format mini-language. You'll learn how to use the mini-language to create working format specifiers and build nicely formatted strings and messages in your code.
		</p>
		<p class="rich-link-href">
		https://realpython.com/python-format-mini-language/#understanding-the-python-format-mini-language
		</p>
	</div>
</a></div>

```
format_spec     ::=  [[fill]align][sign]["z"]["#"]["0"][width]
                     [grouping_option]["." precision][type]
fill            ::=  <any character>
align           ::=  "<" | ">" | "=" | "^"
sign            ::=  "+" | "-" | " "
width           ::=  digit+
grouping_option ::=  "_" | ","
precision       ::=  digit+
type            ::=  "b" | "c" | "d" | "e" | "E" | "f" | "F" | "g" |
                     "G" | "n" | "o" | "s" | "x" | "X" | "%"
```

- *fill*: any character, used for padding
- *align*: how the fill characters are aligned
    - `<`: left (default for strings)
    - `>`: right (default for numbers)
    - `^`: center
    - `=`: between the sign and digits (default for numbers if 0 precedes the width)
- *sign*: whether to show a sign for numbers
    - `+`: show sign for positive and negative numbers
    - `-`: show sign only for negative numbers
    - ` `: leading space for positive numbers and sign for negative numbers
- *width*: total number of characters in the string
- *grouping_option*: sets the thousands separator for numbers
    - use the `n` type instead for locale-specific thousands separators
- *precision*: digits after the decimal point for `f` and `F` types
- *type*: change the output type - different types are allowed based on the type of the expression result
    - integers:
        - `d`: base 10 integer (the default)
        - `n`: same as `d` with thousands separators
        - `b`: binary
        - `o`: octal
        - `x` or `X`: hex (lowercase or uppercase)
        - `c`: Unicode character
    - decimal:
        - `e` or `E`: scientific notation
        - `f` or `F`: fixed-point
        - `g` or `G`: fixed-point for small numbers, scientific notation for large numbers
        - `n`: same as `g` with thousands separators
        - `%`: displays as a percentage

##### Examples

- pad a string:

```python
str = 'Hello'
print(f'{str:.>10}') # '.....Hello'
print(f'{str:.<10}') # 'Hello.....'
```

- pad a number:

```python
int = 7
print(f'{int:4}') # '   7'
print(f'{int:04}') # '0007'
```

- format a float with X decimal places:

```python
float = 300.0
print(f'${float:.2f}') # '$300.00'
```

- format a number with thousands separators

```python
bigNumber = 90000
print(f'${bigNumber:,}') # '90,000'
```

- combine number formatters:
    - the padding is for the total number of characters in the string, not just the number of digits

```python
int = 3000
print(f'{int:010,.2f}') # '003,000.00'
```

- you can also interpolate expressions inside the format specifier

```python
str = 'Hello'
width = 10
print(f'{str:.>{width}}') # '.....Hello'
```

### Regular Expressions

```python
import re
pattern = re.compile(r"(\d{1,2}:\d{2}) (AM|PM)")
```

- `re.search` returns a Match object or None

```python
result = re.search(pattern, "The time is 5:20 PM.")
print(result)
if result:
    print("Groups: %s" % ', '.join(result.groups()))

print(re.search(pattern, "This is a test.")) # Groups: 5:20, PM
```

- `re.match` checks only from the beginning

```python
print(re.match(pattern, "11:20 AM is when the bus will arrive.")) # Match object
print(re.match(pattern, "The bus will arrive at 11:20 AM.")) # None
```

```python
print(re.findall(pattern, "The shuttle departs at 8:20 AM, 8:40 AM, and 9:00 AM.")) # [('8:20', 'AM'), ('8:40', 'AM'), ('9:00', 'AM')]
```

## Tuples

- **tuples are immutable**

```python
x = (1, 2, 3)
print(type(x)) # <class 'tuple'>
print(x[0]) # 1
```

- tuples can be unpacked into individual variables

```python
a, b, c = x
print(b) # 2
```

- tuples can be converted to [[Development/Cheat sheets/Python cheat sheet#Lists\|lists]] using `list(tuple)`

## Dictionaries (Maps/Objects)

- keys must be quoted, and members must be accessed using bracket syntax

```python
phonebook = {
    "John": 123,
    "Jack": 456,
    "Jill": 789
}

del phonebook['John']

popped = phonebook.pop('Jack')
print(popped) # 456
print(phonebook) # {'Jill': 789}
```

- iterate over a dictionary using `for key, value in dict.items()`
    - key order is guaranteed to be preserved as of Python 3.7, otherwise the OrderedDict class can be used

```python
for name, number in phonebook.items():
    print("Phone number of %s is %d" % (name, number))
```

- spread dictionaries using `**`

```python
def divide(a, b):
	return a / b

nums_dict = { "b": 2, "a": 6 }

# this works because of named arguments - if we renamed either of the
# keys in the dictionary this would break
print(divide(**nums_dict)) # 3.0
```

## Sets

- insertion order is not preserved
- create a set from a list using the `set()` constructor

```python
print(set("one two three two four one".split())) # {'two', 'one', 'four', 'three'}

a = {"Jake", "John", "Eric"}
b = {"John", "Jill"}
```

- intersection: finds all items that are in both sets

```python
print(a.intersection(b)) # {'John'}
print(b.intersection(a)) # {'John'}
```

- symmetric difference: finds all items that are in just one set

```python
print(a.symmetric_difference(b)) # {'Jake', 'Jill', 'Eric'}
print(b.symmetric_difference(a)) # {'Jake', 'Jill', 'Eric'}
```

- difference: finds all items that are only in the left set

```python
print(a.difference(b)) # {'Jake', 'Jill', 'Eric'}
print(b.difference(a)) # {'Jill'}
```

- union: combines the sets

```python
print(a.union(b)) # {'Jake', 'John', 'Jill', 'Eric'}
print(b.union(a)) # {'Jake', 'John', 'Jill', 'Eric'}
```

# Functions

- declared using `def`, body indented, don't forget the `:` after the arguments
- must use `return` to return values

```python
def sum_two_numbers(a, b=2):
    # b has a default value
    return a + b

print(sum_two_numbers(5)) # 7
```

- to write to global variables inside a function, mark them with the `global` keyword

```python
number = 1

def increment_number():
    global number
    number += 1

increment_number()
```

- arguments can be provided by name in any order

```python
print(sum_two_numbers(b=3, a=4)) # 7
```

- declare a varargs (rest) argument using `*name` to wrap extra positional args in a [[Development/Cheat sheets/Python cheat sheet#Tuples\|tuple]]

```python
def rest_function(first, second, third, *therest):
    print("The rest... %s" % list(therest))

print(rest_function(1, 2, 3, 4, 5)) # The rest... [4, 5]
```

- use a `*` (with no name) to separate keyword-only arguments

```python
def keyword_only_argument(a, *, b):
    return a + b

keyword_only_argument(1, b=2)
keyword_only_argument(1, 2) # errors
```

- declare a [[Development/Cheat sheets/Python cheat sheet#Dictionaries\|dictionary]] argument with `**name` to wrap extra keyword args

```python
def arguments_dict(first, second, third, **options):
    if options.get("action") == "sum":
        print("The sum is: %d" % (first + second + third))
    
    if options.get("number") == "first":
        return first

result = arguments_dict(1, 2, 3, action="sum", number="first")
print("Result: %d" % result) # Result: 1
```

- functions can return multiple values, which are wrapped in a tuple

```python
def next_three_numbers(start):
    return start + 1, start + 2, start + 3

# tuples are a type of their own
xyz = next_three_numbers(5)
print(type(xyz))
```

## Lambdas

- must contain a single expression

```python
lambda x, y: x + y
```

## Closures

- functions can be returned from other functions (functions are first class objects)
- nested functions have read-only access to variables in their enclosing scope - use the `nonlocal` keyword to make them writable

```python
def print_msg(number):
    def modifyNumber():
        nonlocal number
        number = 3
    modifyNumber()
    print(number)

print_msg(9) # prints 3 - without nonlocal it would print 9
```

## Decorators

- decorators take a function, and return a new function that calls the provided function plus additional behavior

```python
def double_out(old_function):
    def new_function(*args, **kwds):
        return 2 * old_function(*args, **kwds)
    return new_function

def check(old_function):
    def new_function(*args, **kwds):
        for arg in args:
            if arg < 0:
                raise ValueError("Negative Argument")
        return old_function(*args, **kwds)
    return new_function

@double_out
@check
def add(num1, num2):
    return num1 + num2
# calling this is the same as calling double_out(check(add2))(num1, num2)

try:
    print(add(-2, 3))
except ValueError:
    print("Caught ValueError")
```

## Generators

```python
import random

def lottery():
    # returns 6 numbers between 1 and 40
    for _ in range(6):
        yield random.randint(1, 40)

    # returns a 7th number between 1 and 15
    yield random.randint(1, 15)

for random_number in lottery():
    print("And the next number is... %d!" % (random_number))
```

# Flow Control

## if and Comparisons

- `None`, `''`, `0`, `()` (empty tuple), `[]` (empty list), `{}` (empty dictionary) are all falsy
- use `and`, `or`, `not` to chain conditions

```python
name = "John"
age = 23

if name == "John" and age == 23:
    print("Your name is John, and you are also 23 years old.")

if name == "John" or name == "Rick":
    print("Your name is either John or Rick.")

# one-line shorthand
if name == "John": print("Hello John!")

if name in ["John", "Rick"]:
    print("Your name is either John or Rick.")
elif name == "Bob":
    print("Your name is Bob.")
else:
    print("Your name is not John, Rick, or Bob.")

print(bool(""), bool(0), bool([]), bool({})) # all False

print(not False) # True
```

### Ternary conditionals

- `a if condition else b`

```python
beverage = 'coffee' if is_morning() else 'water'
```

## for

- `break` and `continue` work the same way as in JavaScript

### Sequences

```python
for x in mylist:
    print(x)
```

- if you need the index, use `enumerate()`

```python
for i, x in enumerate(mylist):
    print('The value at %d is %s' % (i, x))
```

### Ranges

- `range()` is exclusive, starts at 0 if only one number is provided, third number is the step
    - if you don't need the current number (eg. `x`), use `_` as the variable name

```python
for x in range(5):
    print(x) # prints 0, 1, 2, 3, 4

for x in range(3, 6):
    print(x) # prints 3, 4, 5

for x in range(3, 8, 2):
    print(x) # prints 3, 5, 7

for x in range(5, 0, -1):
    print(x) # prints 5, 4, 3, 2, 1
```

- to iterate backwards along a list:

```python
list = ['e', 'd', 'c', 'b', 'a']

for x in range(len(list) - 1, -1, -1):
    print(list[x]) # prints a, b, c, d, e
```

## List Comprehensions

```python
sentence = "the quick brown fox jumps over the lazy dog"
words = sentence.split()

word_lengths = [len(word) for word in words if word != "the"]
print(word_lengths) # [5, 5, 3, 5, 4, 4, 3]
```

- equivalent to

```python
word_lengths = []
for word in words:
    if word != "the":
        word_lengths.append(len(word))
print(word_lengths)
```

## while

```python
count = 0
while count < 5:
    print(count)
    count += 1

count = 0
while True:
    print(count)
    count += 1
    if count >= 5:
        break

count = 0
    while count < 5:
        print(count)
        count += 1
    else:
        # executes if the condition fails, but not if break is used
        print("count value reached %d" % count)
```

# Exception Handling

```python
the_list = ["a", "b", "c", "d"]

for i in range(5):
    try:
        print(the_list[i])
    except IndexError:
        print("Index %d does not exist" % i)
    except:
        # will catch anything not already caught
        print("Unknown error")
```

# Classes

```python
class MyClass:
    variable = "apple"

    def function(self):
        print("This is a message inside the class.")

myobjectx = MyClass()
myobjecty = MyClass()

myobjectx.variable = "banana"

print(myobjectx.variable) # banana
print(myobjecty.variable) # apple
myobjectx.function() # This is a message inside the class.
```

# Modules and Packages

- imports **do not** have to be at the top level, can be within branching code
- types of import statements

```python
# imports into variable `re`
import re

# imports into variable `regular_expressions`
import re as regular_expressions

# imports `compile` directly into current namespace
from re import compile

# imports all objects from `re` into the current namespace
from re import *
```

- list all objects in a module using `dir`

```python
print(dir(re))
```

- print help for a module or object (primarily for use in the REPL)

```python
import re

help(re)
help(re.compile)
```

# Built-in modules

## random

```python
import random

random.random() # random float between 0 and 1
random.uniform(a, b) # random float between a and b (inclusive)
random.randint(a, b) # random int between a and b (inclusive)

random.choice(seq) # random item from the sequence seq
```
