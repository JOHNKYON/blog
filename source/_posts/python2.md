---
title: 'Data Model, Special Methods'
tags:
  - Python
  - Engineer
  - Programming
categories:
  - Computer Science
date: 2018-12-06 22:39:46
---


In the last {% post_link python1 blog %}, we had a slight feeling about the magical things that special methods can do. This time, we'll talk deeper into how to use those special methods.

## 1.2 How to Use Special Methods

> Specially, we have to be clear that, special methods are designed to be called by Python interpreter, and not by YOU.

In most cases, we don't write something like **my\_object.\_\_len\_\_()**. Just like we mentioned, everything in Python is trying to stay in consistency, so when we write **len(my\_object)**, and the **my\_object** is a user_defined class, Python will call the **\_\_len\_\_()** instance method you implemented. Again, such consistency is the key point that Python can get rid of a huge mess.

Even more, for some built-in types like **list, str, bytearray**, and so on, the interpreter may take some short-cut when a special function is called. For example, the CPython implementation of **len()** actually returns the value of **ob_size** field in the **PyVarObject** C struct, that represents the variable-size built-in object in memory. Of course this is much faster than calling a method.

More often than not, the calling of special methods is implicit. Like **for i in x:**, indeed it cause the invocation of **iter(x)**, which in turn may call **\_\_iter\_\_()** if it's available. (This also indicates that, if we implemented our own **\_\_iter\_\_()** in our user-defined class, we may use **for i in my_object:**), see an example!

```python
>>> class MyClass:
...     def __init__(self, n):
...         self.a = []
...         for i in range(0, n):
...             self.a.append(i)
...     
...     def __iter__(self):
...         curr = 0
...         while curr < len(self.a):
...             yield self.a[curr]
...             curr += 1
... 
>>> my_object = MyClass(3)
>>> 
>>> for ele in my_object:
...     print(ele)
... 
0
1
2


```
Simply by implementing a **\_\_iter\_\_()**, we made our user-defined class supporting the most frequently used built-in syntax.

Normally, your code should not call those special methods directly too often. Unless you're doing metaprogramming, you should be implementing those special methods much more often than invoking them explicitly. Perhaps the only special methods that you may call frequently is the **\_\_init\_\_()**, to invoke the initializer of a superclass in your own **\_\_init\_\_()** implementation.

If you want to use a special method, it is recommended that call them by calling the built-in functions (e.g, **len, iter, str**, etc). There functions does the job you want them to do, normally with some extra benefits. And for some built-in class, they can be faster (like the **len()** we introduced just now).

Besides, please avoid adding custom special methods arbitrarily. The name like **\_\_foo()\_\_** may haven't been occupied now, but who knows about the future?

## 1.2.1 Emulating Numeric Types

Several special methods allow user object to respond to operators such as +. We'll go through a simple example to see how special methods works.

We'll implement a class to represent two-dimensional vectors (and more dimensions in the future). The built-in **complex** type can be used to represent two dimensional vectors, but when talking about more dimensions, we'll need our own class, and we want them to support built-in operators. Here're how we do it.

We will start by designing the API for such a class by writing a simulated console session that we use later as a doctest. Here's what we want.

```python
>>> a = Vector(1, 2)
>>> b = Vector(2, 1)
>>> a + b
Vector(3, 3)
```
The **abs** built-in function returns the absolute value of integers and floats, and magnitude of **complex** numbers. So to be consistent, our API should use **abs** to calculate the magnitude of a vector:

```python
>>> v = Vector(3, 4)
>>> abs(v)
5.0
```
And also the * operator for scalar multiplication (Notice, this is not a dot multiplication for two vectors)

```python
>>> v * 3
Vector(9, 12)
```

Also notice that, the printed information is under user definition. It's pretty simple to make it, just implement a special method called **\_\_repr\_\_()**.

So, all in all, in order to make all the functions above work, the special methods we need to implement are: **\_\_repr\_\_(), \_\_abs\_\_(), \_\_add\_\_()**, and  **\_\_mul\_\_()**


```python
class Vector:
    
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y
        
    def __repr__(self):
        return "Vector(%r, %r)" % (self.x, self.y)
    
    def __abs__(self):
        return hypot(self.x, self.y)
    
    def __bool__(self):
        return bool(abs(self))
    
    def __add__(self, other):
        x = self.x + other.x
        y = self.y + other.y
        return Vector(x, y)
    
    def __mul__(self, scalar):
        return Vector(self.x * scalar, self.y * scalar)

```

Although there 6 special methods in the code, but none of them will be invoked by in it's own code( except for **\_\_init\_\_()**). Even some other code may want to use these methods, they don't explicitly call them, just like what we can see in the console listings. Then let's discuss the code for each special method.

### 1.2.2 String representation.

The **\_\_repr\_\_()** is called by the **repr** built-in to get the string presentation of the instance for inspection. If we didn't implement it, the string returned would look like something like this:

```python
>>> Vector(1,1)
<Vector object at 0x10e380400>
```

The string returned by **\_\_repr\_\_()** should be unambiguous, and, if possible, match the source code necessary to re-create the object being represented. This is why the our **\_\_repr\_\_()** would return a string looks calling the constructor of the class.

There's a similar special method called **\_\_str\_\_()**, which is called by the **str()** constructor and implicitly used by the print function. Basically, **\_\_str\_\_()** should return a string suitable for display to end users.

When you only implement one of the both, implement **\_\_repr\_\_()**, because Python will call **\_\_repr\_\_()** when **\_\_str\_\_()** is not available as a fallback.

### 1.2.3 Arithmetic Operators
We've already seen how to implement our own + and * by implementing **\_\_add\_\_** and **\_\_mul\_\_**. Note that, in both cases, the original object is not changed, and the method returned a new instance of the class. The principal of infix operators is to create new objects and leave the parameters untouched.

### 1.2.4 Boolean Value of a Custom Type
Although we have boolean type in Python, almost any object can be applied to a boolean context, such as a expression controlled by **if** or **while**. To do so, python will call **bool(x)**, and it only returns either **True** or **False**.

By default, an instance of our self-defined class is always **True**, unless we implemented the **\_\_bool\_\_** or **\_\_len\_\_**. **bool(x)** basically calls **x.\_\_bool\_\_()** and returns the result. If it's not implemented, Python would try to invoke **x.\_\_len\_\_()** instead. If it returns 0, then **False**; Otherwise, **True**.

### More Special Methods.
The special methods are list in [Python official site](https://docs.python.org/3/reference/datamodel.html#specialnames). Check them!