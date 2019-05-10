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
i = 0
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

---

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
print(my_list) # -> [1, 2, 3, 100]
```

```
a = [1, 2, 3]
b = a
b.append(4)
print(b) # -> [1, 2, 3, 4]
```


## Variable equality
- `is` - memory address edentity operator
- `==` - data equality operator

## The `None` object

`None` is a real object that is manager by Python memory manager. Furthermore, the memory manager will always use a shared reference when assigning a variable to None.

```
a = None
b = None

a is b # -> True
```


### Everything is an object
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
- can only contai letters, numbers and underscore

Python, both internally, and in the code you write, deals with lots and lots of dictionary type lookups, on string keys, which means a lot of string equality testing.

- `==` - compares strings character by character.
- `is` - compares strings by memory address (integer equality), which is much cheaper

### Force string interning
```
import sys

a = sys.intern('the quick brown fox')
b = sys.intern('the quick brown fox')

a is b # -> True
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

And they alwahy satisfy: `n == d * (n // d) + (n % d)`

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
int(10.9) # -> truncation: 10
int(-10.9) # -> truncation: 10.9
int(True) # -> 1
int(Decimal('10.9')) # -> truncation: 10
int('10') # -> a == 10
```
#### Number base

```
int('1010', 2) # -> 10
int('A12F', base=16) # -> 41263
int('a12f', base=16) # -> 41263
int('1010', base=2) # -> 10
int('534', base=8) -> 348
int('A', base=11) -> 10
int('B', 11) # -> ValueError: invalid literal for int() with base 11: 'B'
```

#### Reverse Process: changing an integer from base 10 to another base

built-in functions

```
bin(10) # -> '0b1010'
oct(10) # -> '0o12'
hex(10) # -> 0xa
```

The prefixes in the strings help document the base of the number.

```
a = 0b1010 # a -> 10
a = 0o12 # a -> 10
a = 0xA # a -> 10
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
encoding = ''.join([map[d] for d in digits]) # -> '4B3C'
```

## Rational numbers

Rational number are represented in Python using the Fraction class.

```
from fractions import Fraction
x = Fraction(3, 4)
y = Fraction(22, 7)
z = Fraction(6, 10)
```

- Fraction are automatically reduced: `Fraction(6, 10) # -> Fraction(3, 5)`
- Negative sign, if any, is always attached to the numerator: `Fraction(1, -4) # -> Fraction(-1, 4)`

### Constructors

```
Fraction(numerator=0, denominator=1)
Fraction(other_fraction)
Fraction(float)
Fraction(decimal)
Fraction(string)
```

```
Fraction('10') # -> Fraction(10, 1)
Fraction('0.125') # -> Fraction(1, 8)
Fraction('22/7') # -> Fraction(22, 7)
```

### Constraining the denominator

`x = Fraction(math.pi) # -> Fraction(884279719003555, 281474976710656)`

We can use `limit_denominator(max_denominator=1000000)` instance method i.e. to find the closest rational with a denominator that does not exceed max_denominator.

`x.limit_denominator(10) # -> Fraction(22, 7)`

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
format(x, '.25f') # -> 0.3000000000000000444089210

y = 0.3
format(y, '.25f') # -> 0.2999999999999999888977698

x == y # -> False
```

Using roundings will not necessarilly solve the problem either.
`round(0.1, 1) + round(0.1, 1) + round(0.1, 1) == round(0.3, 1) # -> False`

But it can be used to round the entirety of both sides of the equality comparison.

`round(0.1 + 0.1 + 0.1, 5) == round(0.3, 5) # -> True`


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


is_equal(x, y, xy_tol) # -> True
is_equal(a, b, ab_tol) # -> True
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
math.floor(10.6) # -> 10
math.floor(-10.3) # -> -11
```

`a // b == floor(a / b)` - floor division

### Ceiling
`math.ceil(float)` - always returns greater int value

```
math.ceil(10.3) # -> 11
math.ceil(-10.6) # -> -10
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

`issubclass(bool, int) # -> True`

Two constants are defined: `True` and `False`. It is _singleton_ objects of type bool.

### is vs ==

Because True and False are singleton objects, they will always retain their same memory address throughout the lifetime of your application.

So comparison of any Boolean expression to `True` of `False` can be performed using either the `is` or the `==` operator.

`True` is actually stored as `1` in memory and `False` as `0`.

But `True` and `1` are different objects in memory.

`True is 1` -> False

### Booleans as Integers

```
True > False # -> True
(1 == 2) == False # -> True
(1 == 2) == 0 # -> True
True + True + True # -> 3
-True # -> -1
100 * False # -> 0
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