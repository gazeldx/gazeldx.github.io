---
layout: post
title:  "Differences Between Dynamically-Typed-Languages and Strongly-Typed-Languages"
date:   2024-11-16 08:55:25
categories: ["Languages"]
---

# Overloading
* In strongly typed languages, overloading is very common, since at compile time, the compiler can check the types of the arguments.
But in dynamically typed languages, overloading is not supported by dynamically typed languages. 
since the arguments passed can be any type, and no exception until during runtime it could cause an exception if the argument type does not match. 

* One of Python's way is to use [singledispatch](https://docs.python.org/3/library/functools.html#functools.singledispatch) to implement similar features.
Another way is like Ruby, do it manually, like using `*arguments`, and check each argument's type by using `type(arg)`. 

* Ruby do it by using multiple arguments and checking those arguments https://stackoverflow.com/questions/9373104/why-doesnt-ruby-support-method-overloading

* JavaScript do it in similar ways as Ruby.

# Interface
* Interface includes a set of abstract methods which need to be implemented in classes.
If a method is not implemented in class, the interface designer likes to through an exception (or IDE support warning it already) in compile time in strongly typed languages like Java.

## How does Ruby implement interface? 
Like this:

```ruby
module ThisIsJustACommonModule
  def some_method_1(arg1)
    raise NotImplementedError, "You must implement the `some_method_1` method"
  end
end
```

Include this module in your class, and define `some_method_1` is your class. That is all. If you didn't implement it, there is no compile time error (compile is not a thing Rubists concern), only runtime error.

# How does Python implement interface?
https://stackoverflow.com/questions/2124190/how-do-i-implement-interfaces-in-python

```python
from abc import ABC, abstractmethod

class AccountingSystem(ABC):
    @abstractmethod
    def create_purchase_invoice(self, purchase):
        pass

    @abstractmethod
    def create_sale_invoice(self, sale):
        log.debug('Creating sale invoice', sale)


# Create a normal subclass and override all abstract methods:

class GizmoAccountingSystem(AccountingSystem):
    def create_purchase_invoice(self, purchase):
        submit_to_gizmo_purchase_service(purchase)

    def create_sale_invoice(self, sale):
        super().create_sale_invoice(sale)
        submit_to_gizmo_sale_service(sale)

```
So Python does it by applying inheriting from `ABC` (Abstract Base Class) and using `@abstractmethod` decorator in your class.

If the `abstract methods` are not implemented, there will be an exception to fail the instantiating of the class.

# How does JavaScript implement interface?
* Interface means that some methods must be included in some classes, JavaScript doesn't need this.
If you want those methods contained in some classes, just define them. If you didn't define them, there will be a method not found exception.
Until during runtime, no one knows the exceptions. This is the way how dynamically typed languages do it.

* Language versions
```
2024 Nov
C++ 23 (20 wildly used)
Java 23
Golang 23
Node.js 23
C# 9
Python 3.13
Ruby 3.3.6
```
