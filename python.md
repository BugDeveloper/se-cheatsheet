# Python cheatsheet

---

# Variable names

`_my_var` - single underscore. This is a convention to indicate "internal use" or "private" objects. Objects named this way will not get imported by a statement such as: `from module import *`.

`__my_var` - double underscore. Used to "mangle" class attributes – useful in inheritance chains. Will be able from unside as `_{class_name}__my_var`.

`__my_var__` - double double underscore. Used for system-defined names that have a special meaning to the interpreter. Name mangling is intended to give classes an easy way to define “private” instance variables and methods, without having to worry about instance variables defined by derived classes, or mucking with instance variables by code outside the class. Note that the mangling rules are designed mostly to avoid accidents; it still is possible for a determined soul to access or modify a variable that is considered private.

---

# Short notes

## Getters and setters

In python by implementing gettes and setters you don't break backward compatability (unlike java), so you you shouldn't do it unless you have a specific reason and logic to put there.

```
class Line:
    def __init__(self, length):
        # Setter usage for validation purposes
        self.length = length

    @property
    def length(self):
        return self._length

    @length.setter
    def length(self, length):
        if length < 0:
            raise ValueError('Length must be positive.')
        self._length = length
```

## Lambdas

`lambda x: x ** 2` - short expression. Just parameters and return statement after `:`.

## Python do-while emulation

```
while True:
    name = input('Print your name: ')
    if is_correct(name):
        break
print(f'Hello, {name}')
```

## Loop-else

### While:

```
val = 10
l = [1, 2, 3]
idx = 0

while idx < len(l):
    if l[idx] == val:
        print('Value in list')
        break
    idx += 1
else:
    print('Value not in list')
```

### For:

```
for i in range(1, 8):
    print(i)
    if i % 7 == 0:
        print('Multiple of 7 found')
        break
else:
    print('Multiple of 7 not found')
```

## For-loop unpacking

```
for i, j in [(1, 2), (3, 4), (5, 6)]:
    print(i, j)
```
### Result

```
1 2
3 4
5 6
```

## Unpacking Iterables

Swapping values:

`a, b = b, a`

`a, b, c = [1, 2, 'Hello'] # a = 1, b = 2, c = 'Hello'`
`a, b, c = 'XYZ' # a = 'X', b = 'Y', c = 'Z'`

### The `*` unpacking operator

Unpacking iterable values

```
l = [1, 2, 3, 4, 5, 6]

a0, b0 = l[0], l[1:]

a1, *b1 = l

a0 == a1 # True
b0 == b1 # True
```

```
l1 = [1, 2, 3]
l2 - 'XYZ'
l = [*l1, *l2] # l = [1, 2, 3, 'X', 'Y', 'Z']
```

```
d1 = {'p': 1, 'y': 2, 't': 3}
d2 = {'t': 4, 'h': 5, 'o': 6, 'n': 7}

l = [*d1, *d2] # ['p', 'y', 't', 't', 'h', 'o', 'n'] order is not guaranteed 
s = {*d1, *d2} # {'p', 'y', 't', 'h', 'o', 'n'} order is not guaranteed
```

### The `**` unpacking operator

Unpacking key-value pairs

```
d1 = {'p': 1, 'y': 2, 't': 3}
d2 = {'t': 4, 'h': 5, 'o': 6, 'n': 7}

d = {**d1, **d2} # {'p': 1, 'y': 2, 't': 4, 'h': 5, 'o': 6, 'n': 7} order is not guaranteed
```

Note that the value of `t` in `d2` "overwrote" the first value of `t` found in `d1`

### Nested Unpacking

```
l = [1, 2, [3, 4]]

a, b, (c, d) = l

# a = 1, b = 2, c = 3, d = 4
```

```
a, *b, (c, d, e) = [1, 2, 3, 'XYZ']

# a = 1, b = [2, 3], c = 'X', d = 'Y', e = 'Z'
```

```
a, *b, (c, *d) = [1, 2, 3, 'python']
# a = 1, b = [2, 3], c = 'p', d = ['y', 't', 'h', 'o', 'n']
```


# Variables and Memory


## Addresses

In Python, we can find out the memory address referenced by a variable by using the  `id()` function.
This will return a base-10 number. We can convert this base-10 number to hexadecimal, by using the `hex()` function.

## Reference counting

`my_var = 10`

`sys.getrefcount(my_var)` - `getrefcount` creates another reference.

`address = id(my_var)`

`ctypes.c_long.from_address(address).value` - does not affect reference count.

## Garbage collector

- Can be controlled programmatically using the gc module
- By default it is turned on
- You may turn it off if you’re sure your code does not create circular references – but beware!!
- Runs periodically on its own (if turned on)
- You can call it manually, and even do your own cleanup

### For python < 3.4

If even one of the objects in the circular reference has a destructor the destruction order of the objects may be important but the GC does not know what that order should be so the object is marked as uncollectable and the objects in the circular reference are not cleaned up which leads to memory leak.

## Typing

We can use the built-in `type()` function to determine the type of the object currently referenced by a variable.

## Mutability

- An object whose internal state can be changed, is called **Mutable**
- An object whose internal state cannot be changed, is called **Immutable**

### Examples

| Immutable                          | Mutable              |
| ---------------------------------- |:--------------------:|
| Numbers (int, float, Boolean, etc) | Lists                |
| Strings                            | Sets                 |
| Tuples                             | Dictionaries         |
| Frozen Sets                        | User-Defined Classes |
| User-Defined Classes               |                      |


Mutable objects are not safe from unintended side-effects.

```
def process(lst):
    lst.append(100)

my_list = [1, 2, 3]
process(my_list)
print(my_list) # [1, 2, 3, 100]
```

```
a = [1, 2, 3]
b = a
b.append(4)
print(b) # [1, 2, 3, 4]
```


## Variable equality
- `is` - memory address edentity operator
- `==` - data equality operator

## The `None` object

`None` is a real object that is manager by Python memory manager. Furthermore, the memory manager will always use a shared reference when assigning a variable to None.

```
a = None
b = None

a is b # True
```


## Everything is an object
- Operators (+, -, ==, is, ...)
- Functions (function)
- Classes (class)
- Types (type)

### As a consequence:
- Any object can be assigned to a variable
- Any object can be passed to a function
- Any object can be returned from a function

---

# Python optimisations

## Interning

**Small integers show up often.** So at startup, CPython pre-loads a global list of integers in the range `[-5, 256]`. Any time an integer is referenced in that range, Python will use the cached version of that object.

When we write
`a = 10`
Python just has to point to the existing reference for 10.

But if we write
`a = 257`
Python does not use that global list and a new object is created every time.

## String interning

As the Python code is compiled, **identifiers** are interned:
- variable names
- function names
- class names
- etc

### Identifiers:
- must start with - or a letter
- can only contain letters, numbers and underscore

Python, both internally, and in the code you write, deals with lots and lots of dictionary type lookups, on string keys, which means a lot of string equality testing.

- `==` - compares strings character by character.
- `is` - compares strings by memory address (integer equality), which is much cheaper

### Force string interning
```
import sys

a = sys.intern('the quick brown fox')
b = sys.intern('the quick brown fox')

a is b # True
```

#### Why?

- dealing with a large number of strings that could have high repetition e.g. tokenizing a large corpus of text (NLP)
- lots of string comparisons

## Peephole

### Constant expressions
- numeric calculations, `24 * 60` Python will pre-calculate and replace by `1440`
- short sequences with length < 20:
  - `(1, 2) * 5` -> `(1, 2, 1, 2, 1, 2, 1, 2, 1, 2)`
  - `'abc' * 3` -> `'abcabcabc'`
  - `'hello' + 'world'` -> `'hello world'`
  - **but not** `'the quick brown fox' * 10` (more than 20 characters)

### Membership tests

#### Mutables are replaced by immutables
`if _ in [1, 2, 3]` -> `if _ in (1, 2, 3)`

- lists -> tuples
- sets -> frosensets

##### P.S. Sets membership is much faster that list or tuple
So instead of writing `if _ in [1, 2, 3]` or `if _ in (1, 2, 3)` write `if _ in {1, 2, 3}`

---

# Numeric types

## Integer

Integer in python uses a variable number of bits. Can use 4 bytes (32 bits), 8 bytes (64 bits), 12 bytes (96 bits), etc. Theoretically limited only by the amount of memory available.

`int / int -> float`

- `//` is called **floor** division
- `%` is called the **modulo** operator

And they always satisfy: `n == d * (n // d) + (n % d)`

### Floor

`floor(3.14) == 3`
`floor(1.9999) == 1`
`floor(2) == 2`
But watch for **negative** numbers!
`floor(-3.1) == -4`

**Floor is quite the same as truncation!**

### Constructors and Bases

An integer number is an object – an instance of the int class.

The int class provides multiple constructors
```
int(10)
int(-10)
int(10.9) # truncation: 10
int(-10.9) # truncation: 10.9
int(True) # 1
int(Decimal('10.9')) # truncation: 10
int('10') # a == 10
```
#### Number base

```
int('1010', 2) # 10
int('A12F', base=16) # 41263
int('a12f', base=16) # 41263
int('1010', base=2) # 10
int('534', base=8) # 348
int('A', base=11) # 10
int('B', 11) # ValueError: invalid literal for int() with base 11: 'B'
```

#### Reverse Process: changing an integer from base 10 to another base

built-in functions

```
bin(10) # '0b1010'
oct(10) # '0o12'
hex(10) # 0xa
```

The prefixes in the strings help document the base of the number.

```
a = 0b1010 # a == 10
a = 0o12 # a == 10
a = 0xA # a == 10
```

#### Other bases: base change algorithm
`n` - >= 0 number in base 10

`b` - base >= 2

```
if n == 0:
    return [0]

digits = []

while n > 0:
    m = n % b
    n = n // b
    digits.insert(0, m)
```

This algorithm returns a list of the digits in the specified base b (a representation of n10 in base b) Usually we want to return an encoded number where digits higher than 9 use letters such as A..Z We simply need to decide what character to use for the various digits in the base.

##### Encoding

```
map = '0123456789ABC'
digits = [4, 11, 3, 12]
encoding = ''.join([map[d] for d in digits]) # '4B3C'
```

## Rational numbers

Rational number are represented in Python using the Fraction class.

```
from fractions import Fraction
x = Fraction(3, 4)
y = Fraction(22, 7)
z = Fraction(6, 10)
```

- Fraction are automatically reduced: `Fraction(6, 10) # Fraction(3, 5)`
- Negative sign, if any, is always attached to the numerator: `Fraction(1, -4) # Fraction(-1, 4)`

### Constructors

```
Fraction(numerator=0, denominator=1)
Fraction(other_fraction)
Fraction(float)
Fraction(decimal)
Fraction(string)
```

```
Fraction('10') # Fraction(10, 1)
Fraction('0.125') # Fraction(1, 8)
Fraction('22/7') # Fraction(22, 7)
```

### Constraining the denominator

`x = Fraction(math.pi) # Fraction(884279719003555, 281474976710656)`

We can use `limit_denominator(max_denominator=1000000)` instance method i.e. to find the closest rational with a denominator that does not exceed max_denominator.

`x.limit_denominator(10) # Fraction(22, 7)`

## Floats

64 float bits are used as follows:

```
sign -> 1 bit
exponend -> 11 bits -> range(-1022, 1023)
significant digits -> 52 bits -> 15-17 significant digits
```

### Representation: binary

Numbers in a computer are represented using bits, not decimal digits so instead of powers of 10 we use powers of 2.

E.g. 28.0 float is stored as follows:

| Sign 1 bits    | Exponent 11 bits      | Mantisa 52 bits   |
| -------------- |:---------------------:| -----------------:|
| 0              | 10000000011           | 110...00          |
| +              | 4 = 131 - 127         | 1/2 = 1 / (2 + 4) |
| 0 -> +, 1 -> - | pow of 2 (127 biased) | fractional part   |


So, some numbers that do have a finite decimal representation, do not have a finite binary representation, and some do.

`0.75 == '0b0.11'`
`0.1 == '0b0_0011_0011_0011...'`

### Equality testing

```
x = 0.1 + 0.1 + 0.1
format(x, '.25f') # 0.3000000000000000444089210

y = 0.3
format(y, '.25f') # 0.2999999999999999888977698

x == y # False
```

Using roundings will not necessarilly solve the problem either.
`round(0.1, 1) + round(0.1, 1) + round(0.1, 1) == round(0.3, 1) # False`

But it can be used to round the entirety of both sides of the equality comparison.

`round(0.1 + 0.1 + 0.1, 5) == round(0.3, 5) # True`


To test for "equality" of two different floats, you could do the following methods:

- Round both sides of the equation: `round(a, 5) == round(b, 5)`
- Absolute tolerance
```
def is_equal(x, y, eps=0.001)
   return math.fabs(x-y) < eps
```
- Relative tolerance (does not work well for numbers close to zero)
```
x = 0.1 + 0.1 + 0.1
y = 0.3

a = 10000.1 + 10000.1 + 10000.1
b = 30000.3

rel_tol = 0.001% = 0.00001 = 1e-5
xy_tol = rel_tol * max(|x|, |y|)
ab_tol = rel_tol * max(|a|, |b|)


is_equal(x, y, xy_tol) # True
is_equal(a, b, ab_tol) # True
```

#### Combination of relative and ubsolute tolerance

The best way:

`tol = max(rel_tol * max(|x|, |y|), abs_tol)`

Built-in solution:

`math.isclose(a, b, *, rel_tol=1e-09, abs_tol=0.0)`

`abs_tol = 1e-5` is a good decision

### Coersing to Integers

- Truncation
- Floor
- Ceiling
- Rounding

### Truncation
`trunc(float)` - returns integer portion of the number

Python int constructor uses truncation when get float as an argument.

### Floor
`math.floor(float)` - always returns lower int value

```
math.floor(10.6) # 10
math.floor(-10.3) # -11
```

`a // b == floor(a / b)` - floor division

### Ceiling
`math.ceil(float)` - always returns greater int value

```
math.ceil(10.3) # 11
math.ceil(-10.6) # -10
```

### Rounding
`round(x, n=0)` - round to the closest value, with `n` precision

```
round(x) -> int
round(x, n) -> same type as x
round(x, 0) -> same type as x
```

#### Important stuff
- `n` can be negative: `round(18.2, -1) == 20`
- if there is no closest precision `round` uses _banker's rounding_
```
round(1.25, 1) -> 1.2 towards 0
round(1.35, 1) -> 1.4 away from 0

round(-1.25, 1) -> -1.2 towards 0
round(-1.35, 1) -> -1.4 away from 0
```

#### Banker's Rounding

_IEEE 754 standard:_ rounds to the nearest value, with ties rounded to the nearest value with an even least significant digit.

#### Intuitive Rounding away from 0

`copysign(x, y)` - returns the magnitude (absolute value) of x but with the sign of y

```
rounded = int(x + math.copysign(0.5, x))
```

## Decimals

Decimal class is used to avoid approximation issues with floats:
`0.1 == '0b0_0011_0011_0011...'`

_Why not just use the Fraction class?_

Fraction operations are too comples and requires a lot of memory.

### Why do we care?

Exact representation is highly desirable in finance, banking and many over fields.

1,000,000,000 transactions with $100.01

```
100.01 -> 100.0100000000000051159076975
sum -> 100.01 * 1e9 -> 100009998761.146392822265625
```
_$1238.85 off!_

### Context

Decimals have a _context_ that controls certain aspects:
- precision during arithmetic operations
- rounding algorithm

This context can be global or local:
- global `decimal.getcontext()`
- local `decimal.localcontext(ctx=None)` creates a new context, copied from `ctx` or from defaul if not specified

```
ctx = decimal.getcontext()
ctx.prec -> the precision (int)
ctx.rounding -> the rounding mechanism
```

#### Global
```
decimal.getcontext().rounding = decimal.ROUND_HALF_UP
```

#### Local
```
with decimal.localcontext() as ctx:
   ctx.prec = 2
   ctx.rounding = decimal.ROUND_HALF_UP

   //decimal operations performed here
   //will use the ctx context
```

### Constructors and contexts

_integer and string_
```
Decimal(10) -> 10
Decimal('0.1') -> 0.1
```

_tuples_
```
Decimal(1, (3, 1, 4, 1, 5), -4) -> -3.1415
IEEE format Decimal(s, (d1, d2, d3, ...), exp)
```


_floats?_ Yes, but this lead to potencial problems

`Decimal(0.1) -> 0.100000000000000005551`

#### Context Precision

- Context precision affects mathematical operations
- Context precision does not affect the constructor

```
import decimal
from decimal import Decimal

decimal.getcontext().prec = 2

a = Decimal('0.12345') a -> 0.12345
b = Decimal('0.12345') b -> 0.12345
c = a + b # a + b = 0.2469 c -> 0.25
```

### Mathematical operations

For integers `//` operator performs floor division.

For Decimals however, it performs truncated division.


We _can_ use the math module, but Decimal objects will first be cast to floats - so we lose the whole precision mechanism that made us use Decimal objects in the first place!

> So it's good idea to use mathimatical functions defined in Decimal class instead of other ones.

### Perfomance

Decimal vs float comparison

- not as easy to code: construction via strings or tuples
- not all mathematical functions that exist in the math module have a Decimal counterpart
- more memory overhead
- performance: much slower than floats (relatively)

## Booleans

Python has a concrete bool class that is used to represent Boolean values. However, the bool class is a subclass of the int class

`issubclass(bool, int) # True`

Two constants are defined: `True` and `False`. It is _singleton_ objects of type bool.

### is vs ==

Because True and False are singleton objects, they will always retain their same memory address throughout the lifetime of your application.

So comparison of any Boolean expression to `True` of `False` can be performed using either the `is` or the `==` operator.

`True` is actually stored as `1` in memory and `False` as `0`.

But `True` and `1` are different objects in memory.

`True is 1` -> False

### Booleans as Integers

```
True > False # True
(1 == 2) == False # True
(1 == 2) == 0 # True
True + True + True # 3
-True # -1
100 * False # 0
```

### The Boolean constructor

The Boolean constructor `bool(x)` returns `True` when x is `True` and `False` overwise.

`bool(-100)` -> True

Many classes itself contain a definition of how to cast instances of them to a Boolean.

### Objects Truth Values

Every object has a `True` truth value, except:
- `None`
- `False`
- `0` in any numeric type
- empty sequesnces
- empty mappings
- classes which implemented `__bool__` or `__len__` method returns `False` or `0`

### Under the hood

When we call `bool(x)` python actially executes `x.__bool__()` or `x.__len__()` if `__bool__` is not defined. If neither is defined, then `True`.

### Operator Precedence

| From highest to lowest |
| ---------------------- |
| < > <= >= == != in is  |
| not                    |
| and                    |
| or                     |

### Boolean operators

- `X or Y` - if `X` is _truthy_, returns `X`, otherwise returns `Y`
- `X and Y` - if `X` is _falsy_, returns `X`, otherwise returns `Y`

#### Or

There is always a possibility to avoid multiple if checks by getting _truthy_ args when needed.

`a = s1 or s2 or s3 or 'default'`

`a` is equal to the first _truthy_ value from left to right. This also shows ability to have default value in any bool equasion.

#### And

`a = s1 and s2 and s3 and 'default'`

`a` is equal to the first _falsy_ value from left to right. This also shows ability to have default value in any bool equasion.


##### Zero division

`x = a and total / a`

In case of zero division the result will be equal to 0.

##### Average

`avg = n and sum / n`

##### First string character
You want to return the first character of a string s, or an empty string if the string is None or empty

`return (s and s[0]) or ''`

### Comparison operators

#### Chained comparisons

`a == b == c` -> `a == b and b == c`

`a < b < c` -> `a < b and b < c`

`a < b < c < d` -> `a < b and b < c and c < d`

# Function parameters

## `*args`

`a, b, *c = 10, 20, 'a', 'b' # a = 10, b = 20, c = ['a', 'b']`

Something similar happens when positional arguments are passed to a function:

```
def func1(a, b, *c):
    pass

func1(10, 20, 'a', 'b') # a = 10, b = 20, c = ('a', 'b')
```

### Unpacking arguments

```
def func1(a, b, c):
    pass

l = [10, 20, 30]

func1(l) # throws exception

func(*l) # a = 10, b = 20, c = 30
```

## Keyword arguments

```
def func(a, b, *args, d):
    pass
```

In this case, `*args` effectively exhausts all positional arguments
and `d` must be passed as a keyword (named) argument.

In fact we can force no positional arguments at all:

```
def func(*, d):
    pass

func(d=5)
```

`*` indicates the "end" of positional arguments

## `**kwargs`

```
def func(*, d, **kwargs):
    pass

func(d=1, a=2, b=3) # d = 1, kwargs = {'a': 2, 'b': 3}

func(d=1) # d = 1, kwargs = {}
```

## Sum up

Arguments:

- `a, b, c=10` - positional parameters can have default values non-defaulted params are mandatory args user may specify them using keywords
- `*args` - scoops up any additional positional args
- `*` - indicates no more positional args
- `kw1, kw2=100` - specific keyword-only args can have default values non-defaulted params are mandatory args user must specify them using keywords
if used, `*` or `*args` must also be used
- `**kwargs` - scoops up any additional keyword args


## Beware default values

### What happens at run-time... 

When a module is loaded all code is executed immediately

```
def func(a=10):
    print(a) 
func()
```
- `def func(a=10):` the function object is created, and func references it
- `print(a)` the integer object 10 is evaluated/created and is assigned as the default for `a`
- `func()` the function is executed

### So what?

```
from datetime import datetime

def log(msg, *, dt=datetime.utcnow()):
   print('{0}: {1}'.format(dt, msg)
```

```
log('message 1') # 2017-08-21 20:54:37.706994 : message 1
log('message 2') # 2017-08-21 20:54:37.706994 : message 2
```

### Solution

We set a default for `dt` to `None`. Inside the function, we test to see if `dt` is still `None` if `dt` is `None`, set it to the current date/time otherwise, use what the caller specified for `dt`

```
from datetime import datetime
def log(msg, *, dt=None):
    dt = dt or datetime.utcnow()
    print('{0}: {1}'.format(dt, msg)
```

In general, always beware of using a mutable object (or a callable) for an argument default.

# First-class functions

## Docstrings and annotations

### Docstings

```
def fact(n):
   """Calculates n! (factorial function)
    Inputs:
        n: non-negative integer
Returns:
the factorial of n
        """
   ...
```

Docstrings are stored in the `__doc__` property.

```
help(fact)

# Output:
#     fact(n)
#         Calculates n! (factorial function)
#         Inputs:
#            n: non-negative integer
#         Returns:
#            the factorial of n
```

### Function Annotations

Additional documenting way, stored in __annotations__ property as dictionary.

```
def my_func(a: <expression>, b: <expression>) -> <expression>:
    pass
```

```
def my_func(a: 'a string', b: 'a positive integer') -> 'a string':
    return a * b

help(my_func) 

# Output:
    # my_func(a: 'a string', b: 'a positive integer') -> 'a string'
```

Annotations can be any expression

```
def my_func(a: str, b: [1, 2, 3]) -> str:
    return a*b
```

## Lambda Expressions

Actually are usual functions but without name

```
lambda x: x ** 2
lambda x, y: x + y
lambda : 'hello'
lambda s: s[::-1].upper()
```

```
lambda s: s[::-1].upper()
```

Lambda is NOT equvalent to closure.

```
my_func = lambda x: x**2

# Identical to 

def my_func(x):
   return x**2
```

Lambda's body is limited to a single expression

## Function Introspection

### `dir()` function

dir() is a built-in function that, given an object as an argument, will return a list of valid
attributes for that object

### Function Attributes: `__name__`, `__defaults__`, `__kwdefaults__`

- `__name__` - name of function
- `__defaults__` - tuple containing positional parameter defaults 
- `__kwdefaults__` - dictionary containing keyword-only parameter defaults


```
def my_func(a, b=2, c=3, *, kw1, kw2=2):
    pass

my_func.__name__ # my_func
my_func.__defaults__ # (2, 3)
my_func.__kwdefaults__ # {'kw2': 2}
```

### Function Attribute: `__code__`


```
def my_func(a, b=1, *args, **kwargs):
   i = 10
   b = min(i, b)
   return a * b

my_func.__code__ # <code object my_func at 0x00020EEF ... >
```

This `__code__` object itself has various properties, which include:

- `co_varnames` - parameter and local variables

```
my_func.__code__.co_varnames # ('a', 'b', 'args', 'kwargs', 'i') 
#parameter names first, followed by local variable names
```

- `co_argcount` - number of parameters, does not count \*args and \*\*kwargs!

```
my_func.__code__.co_argcount # 2 
```

### The `inspect` Module

```
from inspect import *

ismethod(obj)
isfunction(obj)
isroutine(obj)

inspect.getsource(func)
inspect.getmodule(func)
inspect.getcomments(func)

# ...and much more
```

#### Callable Signatures

`inspect.signature(my_func)` - returns `Signature` instance

`Signature` contains attribute `paramaters`. A `dict` where `keys` are parameter names and `values` with such parameters as `name`, `default`, `annotation`, `kind`.

## Callables

Everything what can be called with `()` operator.

```
callable(print) # True
callable(10) # False
```

Instance is callable if it implements `__call__` method.

## Map, filter, zip

Higher order function - func that takes another func as parameter and/or returns a func.

### The `map` function

`map(func, *iterables)`

`*iterables ` - a variable number of iterable objects

`func` - some function that takes as many arguments as there are iterable objects passed to `iterables`


`map` returns an `iterator` that calculates the function appliend to each element of the iterables.

#### Examples

```
l = [2, 3, 4]

list(map(lambda x: x**2, l)) # [4, 9, 16]
```

```
l1 = [1, 2, 3]
l2 = [10, 20, 30]

list(map(lambda x, y: x + y, l1, l2)) # [11, 22, 33]
```

### The `filter` function

`filter(func, iterable)`

`iterable` - a single iterable
`func` - some func that takes a single argument

`filter` - returns an `iterator` that contains all the elements of the iterable for which the function called on it is Truthy

If the function is `None`, it simply returns the elements of `iterable` that are Truthy.

#### Examples

```
l = [0, 1, 2, 3, 4]
list(filter(lambda n: n % 2 == 0, l)) # [0, 2, 4]
```

### The `zip` function

`zip(*iterables)`

```
zip(
    [1, 2, 3],
    [10, 20, 30],
    ['a', 'b', 'c']
)

# (1, 10, 'a'), (2, 20, 'b'), (3, 30, 'c')
```

If lengths of iterables are not equal, than the length of the result will be equal to the length of the shorter iterable
```
l1 = range(100)
l2 = 'abcd'
list(zip(l1, l2))

# [(0, 'a'), (1, 'b'), (2, 'c'), (3, 'd')]
```

### List Comprehension

`[<expression1> for <varname> in <iterable> if <expression2>]`

`[x for x in l if x % 2 == 0]`


## Reducing functions

These are functions that recombine an iterable recursively, ending up with a single return value.

### Example

#### Finding the maximum value in an iterable

```
l = [5, 8, 6, 10, 9]

max_value = lambda a, b: a if a > b else b

def max_sequence(sequence):
   result = sequence[0]
   for e in sequence[1:]:
       result = max_value(result, e)
   return result
```

#### Finding the minimum

```
l = [5, 8, 6, 10, 9]

min_value = lambda a, b: a if a < b else b

def min_sequence(sequence): result = sequence[0]
for e in sequence[1:]:
       result = min_value(result, e)
   return result
```

### The `reduce` function

In fact we could write

```
def _reduce(fn, sequence):
   result = sequence[0]
   for x in sequence[1:]:

result = fn(result, x) return result
```

```
_reduce(lambda a, b: a if a > b else b, l) # maximum 
_reduce(lambda a, b: a if a < b else b, l) # minimum
```

### The `functools` module

Python implements a `reduce` function that will handle any iterable, but works similarly to what we just saw

```
from functools import reduce
l = [5, 8, 6, 10, 9]

reduce(lambda a, b: a if a > b else b, l) # max 10
reduce(lambda a, b: a if a < b else b, l) # min 5
reduce(lambda a, b: a + b, l) # sum 38
```

#### Built-in Reducing Functions

- `min`
- `max`
- `sum`
- `any` - True if any item is True
- `all` - True if all items is True

#### The `reduce` initializer

The reduce function has a third (optional) parameter: `initializer`. If it is specified, it is essentially like adding it to the front of the iterable. It is often used to provide some kind of default in case the iterable is empty.

```
l = []

reduce(lambda x, y: x + y, l) # empty sequence exception
reduce(lambda x, y: x + y, l, 1) # 1 returned

l = [1, 2, 3]
reduce(lambda x, y: x + y, l, 100) # 106 returned
```

## Partial functions

```
def my_func(a, b, c):
   print(a, b, c)

def fn(b, c):
   return my_func(10, b, c)

f = lambda b, c: my_func(10, b, c)

fn(20, 30) # 10, 20, 30
f(20, 30) # 10, 20, 30
```

### Better way
```
from functools import partial

def my_func(a, b, c):
   print(a, b, c)

f = partial(my_func, 10) 

f(20, 30) # 10, 20, 30
```

#### Handling more complex arguments

```
def my_func(a, b, *args, k1, k2, **kwargs):
   print(a, b, args, k1, k2, kwargs)

f = partial(my_func, 10, k1='a')

def pow(base, exponent):
   return base ** exponent

square = partial(pow, exponent=2)
cube = partial(pow, exponent=3)
```

## The `operator` module

### Arithmetic Functions

- `add(a, b)`
- `mul(a, b)`
- `pow(a, b)`
- `mod(a, b)`
- `floordiv(a, b)`
- `neg(a)`

### Comparison and Boolean Operators
- `lt(a, b)`
- `le(a, b)`
- `is_(a, b)`
- `gt(a, b)`
- `ge(a, b)`
- `is_not(a, b)`
- `eq(a, b)`
- `ne(a, b)`
- `and_(a, b)`
- `or_(a, b)`
- `not_(a, b)`

### Sequence/Mapping Operators

- `concat(s1, s2)`
- `contains(s, val)` 
- `countOf(s, val)` 
- `getitem(s, i)` 
- `setitem(s, i, val)` 
- `delitem(s, i)`

### Item Getters

The `itemgetter` function returns a callable

`getitem(s, i)` takes two parameters, and returns a value: `s[i]`

`itemgetter(i)` returns a callable which takes one parameter: a sequence object

```
from operator import itemgetter

f = itemgetter(1, 3)

f([1, 2, 3, 4]) # (2, 4)

f('python') # ('y', 'h')
```

### Attribute Getters

The `attrgetter` function is similar to `itemgetter`, but is used to retrieve object attributes.

```
my_obj = MyClass()
my_obj.a # 10
my_obj.b # 20


f = attrgetter('a', 'c')

f(my_obj) # (10, 30)
```

## Global and local scopes

### The Global Scope

The global scope is essentially the module scope. It spans a single file only. There is no concept of a truly global (across all the modules in our entire app) scope in Python.

The only exception to this are some of the built-in globally available objects, such as:

- `True`
- `False`
- `None`
- `dict`
- `print`

Scopes include each other from top to bottom:

- `Built-in`
- `Module`
- `Local`
- `Local nested scope`
- ...

```
# example.py

a = 10
def func() {
    print(a)
}
```

Python always looking for a variables from bottom to top. If we will call `func` from the `example.py` Python will look for `print` and `a` in following order:

 1. Looking in `local` scope (scope of the function).
 2. Looking in `module` scope (scope of the `example.py`) and find `a`.
 3. Looking in `built-it` scope and find `print`.

### The Local Scope

Variables defined inside a function are not created until the function is **called**. Every time the function is called, a new scope is **created**.

### The `global` keyword

```
a = 0

def my_func():
    a = 100

my_func() # 100
print(a) # 0
```

```
a = 0

def my_func():
   global a
   a = 100

my_func()

print(a) # 100
```

### Global and Local Scoping

When Python encounters a function definition at **compile-time** it will **scan** for any labels (variables) that have values **assigned** to them (anywhere in the function) if the label has not been specified as global, then it will be local.

## Nonlocal Scopes

```
def outer_func():
       # some code
    def inner_func():
         # some code
   inner_func()

outer_func()
```

The inner function has access to its **enclosing** scope – the scope of the outer function. That scope is neither local (to inner_func) nor global – it is called a **nonlocal** scope.

### The `nonlocal` keyword

```
def outer_func():
   x = 'hello'
   def inner_func():
       x = 'python'
    inner_func()
    print(x)

outer_func()
```

```
def outer_func():
   x = 'hello'
   def inner_func():
       nonlocal x
       x = 'python'
       inner_func()
    print(x)

outer_func()
```

Whenever Python is told that a variable is `nonlocal` it will look for it in the enclosing local scopes chain until it **first** encounters the specified variable name.

**Beware:** It will only look in local scopes, it will not look in the global scope.

## Closures

```
def outer():
    x = 'python'
    def inner():
        print("{0} rocks!".format(x))
    return inner

fn = outer()

fn() # python rocks!
```

When we return `inner` in the example above, we actually returning the **closure**. Not only the function by itself by also the scope.

When we called `fn` at that time Python determined the value of `x` in the extended scope. But notice that `outer` had finished running before we called `fn` – it's scope was "gone".

```
def outer():
x = 'python'
   def inner():
       print(x)
return inner
```

In the code above the value of x is shared between two scopes:
 - outer
 - closure

### Python Cells and Multi-Scoped Variables

The label x is in two different scopes but always reference the same "value". `x` in this case called **a free variable**.

Python does this by creating a **cell** as an intermediary object. Both variables `outer.x` and `inner.x` reference to **cell* and **cell** reference to the actual value.

```
def outer():
    a = 100
    x = 'python'
    def inner():
        a = 10 # local variable
        print("{0} rocks!".format(x)) return inner

fn = outer()

fn.__code__.co_freevars # ('x', )
fn.__closure__ # (<cell at 0xA500: str object at 0xFF100>, )
```

```
def outer():
    x = 'python'
    print(hex(id(x)) # 0xFF100
    def inner():
       print(hex(id(x)) # 0xFF100
       print("{0} rocks!".format(x))
    return inner
fn = outer()
fn()
```

### Multiple Instances of Closures

Every time we run a function, a new scope is created. If that function generates a closure, a new closure is created every time as well.

```
def counter():
    closure count = 0
    def inc():
        nonlocal count
        count += 1 
        return count
    return inc

f1 = counter()
f2 = counter()

f1() # 1
f1() # 2
f1() # 3

f2() # 1
```

### Shared Extended Scopes
```
def outer():
   count = 0
   def inc1():
       nonlocal count
       count += 1
       return count
    def inc2():
        nonlocal count
        count += 1
        return count
   return inc1, inc2

f1, f2 = outer()

f1() # 1
f2() # 2
```

## Decorators
```
def counter(fn):
   count = 0
   def inner(*args, **kwargs):
        nonlocal count
        count += 1
        print('Function {0} was called {1} times'.format(fn.__name__, count) return fn(*args, **kwargs)
    return inner

def add(a, b=0):
    return a + b
```

```
@counter
def add(a, b):
    return a + b
```

is the same as writing

```
add = counter(add)
```

```
result = add(1, 2) # will print "Function add was called 1 times"
result == 3 # True
```

`add.__name__ # inner`

`mult`'s name "changed" when we decorated it they are not the same function after all. We also lost all metadata.

### The `functools.wraps` function

The functools module has a wraps function that we can use to fix the metadata of our inner function in our decorator.

In fact, the `wraps` function is itself a decorator but it needs to know what was our "original" function – in this case `fn`.

```
from functools import wraps

def counter(fn):
    count = 0
    @wraps(fn)
    def inner(*args, **kwargs):
        nonlocal count
        count += 1
        print(count)
           return fn(*args, **kwargs)
       return inner
```

### Decorator Factories

If we want to tweak some variable of decorator without changing its source, we need to create a factory for decorator. E.g. if we want to measure func perfomance for N times.

```
def timed(n):
  def dec(fn):
    from time import perf_counter

    @wraps(fn)
    def inner(*args, **kwargs):
        total_elapsed = 0
        for i in range(n):
            start = perf_counter()
            result = fn(*args, **kwargs)
            total_elapsed += (perf_counter() - start)
        avg_elapsed = total_elapsed / n
        print(avg_elapsed)
        return result

    return inner
return dec
```

```
@timed(10)
def my_func():
    ...
```

## Tuples as data structures

### Tuples vs lists:
Sequences can be:
- Homogeneous (all items are the same type)
- Heterogeneous (items of different type)

### Immutability of Tuples

- Elements cannot be added or removed the order of 
- Elements cannot be changed

Works well for representing data structures:
```
point = (10, 20)
circle = (0, 0, 10)
city = ('London', 'UK', 8_780_000)
```

Can be unpacked:
```
city_name, country, population = city
```

**The position of the data has meaning**

### Dummy Variables

```
city, _, population = ('Beijing', 'China', 21_000_000)
```

By convention, we use the `_` (*underscore*) to indicate this is a variable we don't care about.

It's also used in extended unpacking too (`*_`):

```
record = ('DJIA', 2018, 1, 19, 25987.35, 26071.72, 25942.83, 26071.72)
symbol, year, month, day, *_, close = record
```
## Named Tuples

They subclass tuple, and add a layer to assign property names to the positional elements

`from collections import namedtuple`

`namedtuple` is a *class factory*

```
class_name = 'Point2D'
field_names = ['x', 'y']

Point2D = namedtuple(class_name, field_names)
pt = Point2D(10, 20)
```

Ways of providing the list of field names to the namedtuple function:
```
namedtuple('Point2D', ['x', 'y']) namedtuple('Point2D', ('x', 'y'))
namedtuple('Point2D', 'x, y')
namedtuple('Point2D', 'x y')
```

Since named tuples are also regular tuples, we can still handle them just like any other tuple

- by index
- slice
- iterate

```
Point2D = namedtuple('Point2D', 'x y')
pt1 = Point2D(10, 20)
x, y = pt1
x = pt1[0]

for e in pt1:
    print(e)

pt1.x # -> 10
pt1.y # -> 20

pt1._asdict() # -> {'x': 10, 'y': 20}
```

Since `pt1` *is a tuple* it is immutable:
`pt1.x = 100 # will not work!`

### Modifying a Named Tuple

Named tuples have a very handy instance method `_replace`

```
Stock = namedtuple('Stock', 'symbol year month day open high low close')

djia = Stock('DJIA', 2018, 1, 25, 26_313, 26_458, 26_260, 26_393)

djia = djia._replace(day=26, high=26_459, close=26_394)
```

*Note that the memory address of `djia` has now changed*

### Extending a Named Tuple

```
Point2D = namedtuple('Point2D', 'x y')
Point3D = namedtuple('Point3D', Point2D._fields + (z',)
```

### DocStrings and Default Values

#### DocStrings
```
Point2D = namedtuple('Point2D', 'x y')
Point2D.__doc__ = 'Represents a 2D Cartesian coordinate.'
Point2D.x.__doc__ = 'x coordinate'
Point2D.y.__doc__ = 'y coordinate'
```

#### Default Values

- Using a Prototype
```
Vector2D = namedtuple('Vector2D', 'x1 y1 x2 y2 origin_x origin_y')
vector_zero = Vector2D(0, 0, 0, 0, 0, 0)
# To construct a new instance of Vector2D we now use vector_zero._replace instead

v1 = vector_zero._replace(x1=10, y1=10, x2=20, y2=20)
```

- Using `__defaults__`

```
Vector2D = namedtuple('Vector2D', 'x1 y1 x2 y2 origin_x origin_y')
Vector2D.__new__.__defaults__ = (0, 0)

v1 = Vector2D(10, 10, 20, 20)
v1 # -> Vector2D(x1=10, y1=10, x2=20, y2=20, origin_x=0, origin_y=0)
```

*Named Tuples are really usefull when it comes to returning multiple values, since code editor can hint.*

## What is a Module?

`globals()` gives listing of current global namespaces

`locals()` gives listing of current local namespaces

```
import sys
sys.modules # returns a dict with all imported modules
sys.__dict__ # return dict of namespaces
sys.path # returns places where python looks for modules

# e.g.
import math as r_math

'math' in sys.modules # True
'math' in globals() # False
'r_math' in globals() # True
```

```
import importlib

# the same as import math as math2
math2 = importlib.import_module('math') # import module by string
```

### Commonality
`from math import whatever`

**In all cases the `math` module is loaded into memory. Statements affects only what names are placed into namespace.**

### What python do when importing a module?
- Checks `sys.modules` cache to see if a module has been already imported. If so it uses reference from there, otherwise:
- Creates a new model object `types.ModuleType`
- Loads the source code from file
- Adds an entry to `sys.modules` with name as key 
- Compiles and executes the source code

## Extra
`python -i script.py # runs script in interactive mode`
