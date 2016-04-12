---
layout: post
title: python - special methods
description: python - special methods
modified: 2016-04-11
tags: [python]
comments: true
image:
  feature: abstract-11.png
---
[Python class predefines some special methods](https://docs.python.org/3/reference/datamodel.html).

### Special methods

- Instance creation and destruction: __new__, __init__, __del__
- String/bytes representation: __repr__, __str__, __format__, __bytes__
- Conversion to number: __abs__, __bool__, __complex__, __int__, __float__, __hash__, __index__
- Emulating collections: __len__, __getitem__, __setitem__, __delitem__, __contains__
- Iteration: __iter__, __reversed__, __next__
- Emulating callables: __call__
- Context management: __enter__, __exit__
- Attribute management: __getattr__, __getattribute__, __setattr__, __delattr__, __dir__
- Attribute descriptors: __get__, __set__, __delete__
- Class services: __prepare__, __instancecheck__, __subclasscheck__

### Special methods for operators

- Unary numeric operators: __neg__ -, __pos__ +, __abs__ abs()
- Rich comparison operators: __lt__ >, __le__ <=, __eq__ ==, __ne__ !=, __gt__ >, __ge__ >=
- Arithmetic operators: __add__ +, __sub__ -, __mul__ *, __truediv__ /, __floordiv__ //, __mod__ %, __divmod__ divmod() , __pow__ ** or pow(), __round__ round()
- Reversed arithmetic operators: __radd__, __rsub__, __rmul__, __rtruediv__, __rfloordiv__, __rmod__, __rdivmod__, __rpow__
- Augmented assignment arithmetic operators: __iadd__, __isub__, __imul__, __itruediv__, __ifloordiv__, __imod__, __ipow__
- Bitwise operators: __invert__ ~, __lshift__ <<, __rshift__ >>, __and__ &, __or__ |, __xor__ ^
- Reversed bitwise operators: __rlshift__, __rrshift__, __rand__, __rxor__, __ror__
- Augmented assignment bitwise operators: __ilshift__, __irshift__, __iand__, __ixor__, __ior__

### Example of redefining special methods 

You could override or define them in your classes as below. This exmple came from the book, "fluent python". 

```python
from math import hypot 

class Vector:
	def __init__(self, x = 0, y = 0):
		self.x = x
		self.y = y
	def __repr__(self):
		return 'Vector(%r, %r)' % (self.x, self.y)
	def __abs__(self):
		return hypot(self.x, self.y)
	def __bool__(self):
		return bool(abs(self))
	def __add__(self, other):
		x = self.x + other.x
		y = self.y + other.y
		return Vector(x, y)
	def __mul__(self, scalar):
		return Vector(self.x*scalar, self.y*scalar)
	def __str__(self):
		return "Vector(%r, %r)" % (self.x, self.y)
```

### Test code

You could test them as follows. 

```
#__init__ invoked
v1 = Vector()
v2 = Vector(3, 4)
#__str__ invoked
print str(v2) # Vector(3, 4)
#__add__ invoked
v3 = v1 + v2 
print str(v3) # Vector(3, 4)
#__abs__ invoked
print abs(v3) # 5.0
#__mul__ invoked
v4 = v3*3
print str(v4) # Vector(9, 12)
#__bool__ invoked
print bool(v4) #true
```

