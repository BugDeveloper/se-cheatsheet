# Python cheatsheet

---

## Variable names

`_my_var` - single underscore. This is a convention to indicate "internal use" or "private" objects. Objects named this way will not get imported by a statement such as: `from module import *`.

`__my_var` - double underscore. Used to "mangle" class attributes – useful in inheritance chains. Will be able from unside as `_{class_name}__my_var`.

`__my_var__` - double double underscore. Used for system-defined names that have a special meaning to the interpreter. Name mangling is intended to give classes an easy way to define “private” instance variables and methods, without having to worry about instance variables defined by derived classes, or mucking with instance variables by code outside the class. Note that the mangling rules are designed mostly to avoid accidents; it still is possible for a determined soul to access or modify a variable that is considered private.

---

## Short notes

##### Getters and setters

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

#### Lambdas

`lambda x: x ** 2` - short expression. Just parameters and return statement after `:`.

#### Python do-while emulation

```
i = 0
while True:
	name = input('Print your name: ')
	if is_correct(name):
		break
print(f'Hello, {name}')
```

#### Loop-else

##### While:

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

##### For:

```
for i in range(1, 8):
	print(i)
	if i % 7 == 0:
		print('Multiple of 7 found')
else:
	print('Multiple of 7 not found')
```

#### For-loop unpacking

```
for i, j in [(1, 2), (3, 4), (5, 6)]:
	print(i, j)
```
##### Result

```
1 2
3 4
5 6
```

---

## Variables and Memory


#### Addresses

In Python, we can find out the memory address referenced by a variable by using the  `id()` function.
This will return a base-10 number. We can convert this base-10 number to hexadecimal, by using the `hex()` function.

#### Reference counting

`my_var = 10`

`sys.getrefcount(my_var)` - `getrefcount` creates another reference.

`address = id(my_var)`

`ctypes.c_long.from_address(address).value` - does not affect reference count.

#### Garbage collector

- Can be controlled programmatically using the gc module
- By default it is turned on
- You may turn it off if you’re sure your code does not create circular references – but beware!!
- Runs periodically on its own (if turned on)
- You can call it manually, and even do your own cleanup

##### For python < 3.4

If even one of the objects in the circular reference has a destructor the destruction order of the objects may be important but the GC does not know what that order should be so the object is marked as uncollectable and the objects in the circular reference are not cleaned up which leads to memory leak.

#### Typing

We can use the built-in `type()` function to determine the type of the object currently referenced by a variable.

#### Mutability

- An object whose internal state can be changed, is called **Mutable**
- An object whose internal state cannot be changed, is called **Immutable**

##### Examples

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


#### Variable equality
- `is` - memory address edentity operator
- `==` - data equality operator

#### The `None` object

`None` is a real object that is manager by Python memory manager. Furthermore, the memory manager will always use a shared reference when assigning a variable to None.

```
a = None
b = None

a is b # -> True
```


#### Everything is an object
- Operators (+, -, ==, is, ...)
- Functions (function)
- Classes (class)
- Types (type)

##### As a consequence:
- Any object can be assigned to a variable
- Any object can be passed to a function
- Any object can be returned from a function

---

## Python optimisations

#### Interning

**Small integers show up often.** So at startup, CPython pre-loads a global list of integers in the range `[-5, 256]`. Any time an integer is referenced in that range, Python will use the cached version of that object.

When we write
`a = 10`
Python just has to point to the existing reference for 10.

But if we write
`a = 257`
Python does not use that global list and a new object is created every time.

#### String interning

As the Python code is compiled, **identifiers** are interned:
- variable names
- function names
- class names
- etc

##### Identifiers:
- must start with - or a letter
- can only contai letters, numbers and underscore

Python, both internally, and in the code you write, deals with lots and lots of dictionary type lookups, on string keys, which means a lot of string equality testing.

- `==` - compares strings character by character.
- `is` - compares strings by memory address (integer equality), which is much cheaper

##### Force string interning
```
import sys

a = sys.intern('the quick brown fox')
b = sys.intern('the quick brown fox')

a is b # -> True
```

###### Why?

- dealing with a large number of strings that could have high repetition e.g. tokenizing a large corpus of text (NLP)
- lots of string comparisons

#### Peephole

##### Constant expressions
- numeric calculations, `24 * 60` Python will pre-calculate and replace by `1440`
- short sequences with length < 20:
  - `(1, 2) * 5` -> `(1, 2, 1, 2, 1, 2, 1, 2, 1, 2)`
  - `'abc' * 3` -> `'abcabcabc'`
  - `'hello' + 'world'` -> `'hello world'`
  - **but not** `'the quick brown fox' * 10` (more than 20 characters)

##### Membership tests

###### Mutables are replaced by immutables
`if _ in [1, 2, 3]` -> `if _ in (1, 2, 3)`

- lists -> tuples
- sets -> frosensets

###### P.S. Sets membership is much faster that list or tuple
So instead of writing `if _ in [1, 2, 3]` or `if _ in (1, 2, 3)` write `if _ in {1, 2, 3}`
