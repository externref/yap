---
title: Function overloading in Python
description: Using namespaces and magic methods to implement a basic function overriding in python
date: 2024-09-24
authors: 
  - externref
categories:
  - python
  - oop
comments: true
---

# Function overloading in Python
Function overloading refers to the ability to define multiple functions with the same name but different signatures, meaning they have different numbers or types of parameters. The correct version of the function is chosen automatically by the compiler or interpreter, depending on the number and type of arguments passed during the function call.<!-- more -->

Languages like Java and C++ support this as a feature natively. While python does not support function overloading natively since it's a dynamically typed language, its possible to implement the same using various modules and utilities.

This is my implementation of overloading.
!!! warning
    This is just a basic idea and works for non-union fixed types.

## Implementation

```python
from __future__ import annotations

import inspect
import typing

bin: dict[str, OverloadNamespace] = {}


class OverloadNamespace:
    overloads: dict[tuple[type, ...], typing.Callable[..., typing.Any]]
    fallback: typing.Callable[..., typing.Any]

    def __init__(self, name: str) -> None:
        self.overloads = {}
        self.fallback = self._fallback
        bin[name] = self

    def __call__(self, *args: typing.Any, **kwds: typing.Any) -> typing.Any:
        types = [type(arg) for arg in args]
        types.extend([type(kwrg) for kwrg in kwds])
        try:
            return self.overloads[tuple(types)](*args, **kwds)
        except KeyError:
            return self.fallback(*args, **kwds)

    @staticmethod
    def _fallback(*_, **__) -> None:
        raise NotImplementedError
```

The `OverloadNamespace` class is a callable that acts as a medium between the function name and the call signature. The arguments are passed into the `__call__` dunder method which matches the provided data types by comparing it to the type tuples stored in the `overloads` dictionary. The matched signature is returned and is called with the provided args/kwargs passed in the call. If no matching signature is found, the `fallback` function is called.

## Using the OverloadNamespace class

This class is not meant to be used manually, it's utilised by decorators that modify the function and return an instance of the `OverloadNamespace` class using the same name as the function's provided name. 

### Overload 
```python
def overload(*args) -> typing.Callable[..., OverloadNamespace] | OverloadNamespace:
    """Decorator used to create overloads of functions with same name. Returns a [OverloadNamespace]"""
    if len(args) == 1 and inspect.isfunction(args[0]):
        return overload_using_types(args[0])

    def inner(func: typing.Callable[..., typing.Any]) -> OverloadNamespace:
        sig = inspect.signature(func)
        assert len(args) == len(
            sig.parameters
        ), "Number of types and args in function is not same."

        namespace = (
            bin[func.__name__]
            if bin.get(func.__name__)
            else OverloadNamespace(func.__name__)
        )
        namespace.overloads[tuple(args)] = func
        return namespace

    return inner

def overload_using_types(func: typing.Callable[..., typing.Any]) -> OverloadNamespace:
    args = inspect.signature(func).parameters
    types = tuple(arg.annotation for arg in args.values())

    namespace = (
        bin[func.__name__]
        if bin.get(func.__name__)
        else OverloadNamespace(func.__name__)
    )

    namespace.overloads[types] = func
    return namespace
```

The `overload` decorator checks the types of arguments using the decorator value or typehints and returns the namespace class.

**Example usage**

```python
# types in decorator
@overload(int, int)
def sum(a, b):
    return a+b

# or as typehints
@overload 
def sum(a: float, b: float):
    return int(a+b)+1

sum(1,2) # 3
sum(1.23, 2.0) # 4
``` 


### Fallback

Fallback function are used as the function to be called when no argument patterns match the call pattern.

```python
def fallback(
    func: typing.Callable[..., typing.Any],
) -> OverloadNamespace:
    """Fallback function to be called if no overloads match to the provided arguments."""
    namespace = (
        bin[func.__name__]
        if bin.get(func.__name__)
        else OverloadNamespace(func.__name__)
    )
    namespace.fallback = func
    return namespace


@fallback 
def sum(*args):
    return sum(args)

sum(1,2,3,4) # 10
```
