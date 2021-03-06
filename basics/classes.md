# Defining and using custom classes in Python

When I was getting started in Python I learned to make classes for
tkinter GUI's before I understood how they work. Everything I did with
classes worked, but I didn't understand how. Hopefully you'll first
learn to understand classes, and then learn to use them.

This tutorial assumes that you know [how functions work](using-functions.md)
and [how to create your own functions](defining-functions.md). If you
don't I highly recommend learning that first, and then moving to classes.

## Why should I use custom classes in my projects?

Python comes with a lot of classes that you are already familiar with.

```python
>>> str
<class 'str'>
>>> int
<class 'int'>
>>> list
<class 'list'>
>>> dict
<class 'dict'>
>>>
```

Calling these classes as if they were functions makes a new **instance**
of them. For example, `str()` makes a `str` instance, also known as a
string.

```python
>>> str()
''
>>> int()
0
>>> list()
[]
>>> dict()
{}
>>>
```

We can also get an instance's class with `type()`:

```python
>>> type('')
<class 'str'>
>>> type(0)
<class 'int'>
>>> type([])
<class 'list'>
>>> type({})
<class 'dict'>
>>>
```

Let's say you make a program that processes data about websites. With a
custom class, you're not limited to `str`, `int` and other classes
Python comes with. Instead you can define a Website class, and make
Websites and process information about websites directly. Defining your
own object types like this is called **object-orientated programming**.

## First class

In Python, `pass` does nothing.

```python
>>> pass
>>>
```

Let's use it to define an empty class.

```python
>>> class Website:
...     pass
...
>>> Website
<class '__main__.Website'>
>>>
```

Note that I named the class `Website`, not `website`. This way we know
that it's a class. Built-in classes use lowercase names (like `str`
instead of `Str`) because they are faster to type, but use CapsWord
names for your classes.

Now we can make a Website instance by calling the class.

```python
>>> stackoverflow = Website()
>>> stackoverflow
<__main__.Website object at 0x7f36e4c456d8>
>>> type(stackoverflow)
<class '__main__.Website'>
>>>
```

We can say that `stackoverflow` is "a Website instance", "a Website
object" or "a Website". All of these mean the same thing.

Now we can attach more information about stackoverflow to our Website.

```python
>>> stackoverflow.url = 'http://stackoverflow.com/'
>>> stackoverflow.founding_year = 2008
>>> stackoverflow.free_to_use = True
>>>
```

We can also access the information easily.

```python
>>> stackoverflow.url
'http://stackoverflow.com/'
>>> stackoverflow.founding_year
2008
>>> stackoverflow.free_to_use
True
>>>
```

As you can see, our Website is mutable, like lists are, not immutable
like strings are. We can change the website in-place without creating a
new Website.

`url`, `founding_year` and `free_to_use` are not variables, they are
**attributes**. More specifically, they are **instance attributes**.
The biggest difference is that we need to use a dot for setting and
getting values of attributes, but we don't need that with variables.

Modules also use instance attributes for accessing their content. For
example, when we do `random.randint`, `random` is a module instance and
`randint` is one of its attributes.

If we make another Website, does it have the same `url`, `founding_year`
and `free_to_use`?

```python
>>> effbot = Website()
>>> effbot.url
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'Website' object has no attribute 'url'
>>>
```

It doesn't. We'd need to define the attributes for effbot also.

The attributes are stored in a dictionary called `__dict__`. It's not
recommended to use it for code that needs to be reliable, but it's a
handy way to see which attributes the instance contains.

```python
>>> stackoverflow.__dict__
{'free_to_use': True,
 'founding_year': 2008,
 'url': 'http://stackoverflow.com/'}
>>> effbot.__dict__
{}
>>>
```

## Class attributes

What happens if we set an attribute of the `Website` class to some value
instead of doing that to an instance?

```python
>>> Website.is_online = True
>>> Website.is_online
True
>>>
```

Seems to be working, but what happened to the instances?

```python
>>> stackoverflow.is_online
True
>>> effbot.is_online
True
>>>
```

What was that? Setting `Website.is_online` to a value also set
`stackoverflow.is_online` and `effbot.is_online` to that value!

Actually, `is_online` is still not in stackoverflow's or effbot's
`__dict__`. stackoverflow and effbot get that attribute directly from
the `Website` class.

```python
>>> stackoverflow.__dict__
{'free_to_use': True,
 'founding_year': 2008,
 'url': 'http://stackoverflow.com/'}
>>> effbot.__dict__
{}
>>>
```

`Website.is_online` is `Website`'s class attribute, and in Python you can
access class attributes through instances also, so in this case
`stackoverflow.is_online` points to `Website.is_online`. That can be
confusing, which is why it's not recommended to use class attributes like
this. Use instance attributes instead, e.g. `stackoverflow.is_online = True`.

## Functions and methods

Let's define a function that prints information about a website.

```python
>>> def website_info(website):
...     print("URL:", website.url)
...     print("Founding year:", website.founding_year)
...     print("Free to use:", website.free_to_use)
...
>>> website_info(stackoverflow)
URL: http://stackoverflow.com/
Founding year: 2008
Free to use: True
>>>
```

Seems to be working. We should be able to get information about all
websites, so maybe we should attach the `website_info` function to the
Website class?

```python
>>> Website.info = website_info
>>> Website.info(stackoverflow)
URL: http://stackoverflow.com/
Founding year: 2008
Free to use: True
>>>
```

It's working, but `Website.info(stackoverflow)` is a lot of typing, so
wouldn't `stackoverflow.info()` be much better?

```python
>>> stackoverflow.info()
URL: http://stackoverflow.com/
Founding year: 2008
Free to use: True
>>>
```

What the heck happened? We didn't define a `stackoverflow.info`, it just
magically worked!

`Website.info` is our `website_info` function, so `stackoverflow.info`
should also be the same function. But `Website.info` takes a `website`
argument, which we didn't give it when we called `stackoverflow.info()`!

But is `stackoverflow.info` the same thing as `Website.info`?

```python
>>> Website.info
<function website_info at 0x7f36e4c39598>
>>> stackoverflow.info
<bound method website_info of <__main__.Website object at 0x7f36e4c456d8>>
>>>
```

It's not.

Instead, `stackoverflow.info` is a **method**. If we set a function as a
class attribute, the instances will have a method with the same name.
Methods are "links" to the class attribute functions. So
`Website.info(stackoverflow)` does the same thing as `stackoverflow.info()`,
and when `stackoverflow.info()` is called it automatically gets
`stackoverflow` as an argument.

In other words, **`Class.method(instance)` does the same thing as
`instance.method()`**. This also works with built-in classes, for
example `'hello'.lower()` is same as `str.lower('hello')`.

## Defining methods when defining the class

Maybe we could define a method when we make the class instead of adding
it later?

```python
>>> class Website:
...     def info(self):     # self will be stackoverflow
...         print("URL:", self.url)
...         print("Founding year:", self.founding_year)
...         print("Free to use:", self.free_to_use)
...
>>> stackoverflow = Website()
>>> stackoverflow.url = 'http://stackoverflow.com/'
>>> stackoverflow.founding_year = 2008
>>> stackoverflow.free_to_use = True
>>> stackoverflow.info()
URL: http://stackoverflow.com/
Founding year: 2008
Free to use: True
>>>
```

It's working. The `self` argument in `Website.info` was `stackoverflow`.
You could call it something else too such as `me`, `this` or `instance`,
but use `self` instead. Other Python users have gotten used to it, and
the official style guide recommens it also.

We still need to set `url`, `founding_year` and `free_to_use` manually.
Maybe we could add a method to do that?

```python
>>> class Website:
...     def initialize(self, url, founding_year, free_to_use):
...         self.url = url
...         self.founding_year = founding_year
...         self.free_to_use = free_to_use
...     def info(self):
...         print("URL:", self.url)
...         print("Founding year:", self.founding_year)
...         print("Free to use:", self.free_to_use)
...
>>> stackoverflow = Website()
>>> stackoverflow.initialize('http://stackoverflow.com/', 2008, True)
>>> stackoverflow.info()
URL: http://stackoverflow.com/
Founding year: 2008
Free to use: True
>>>
```

That works. The attributes we defined in the initialize method are also
available in the info method. We could also access them directly from
`stackoverflow`, for example with `stackoverflow.url`.

But we still need to call `stackoverflow.initialize`. In Python, there's
a "magic" method that runs when we create a new Website by calling the
Website class. It's called `__init__` and it does nothing by default. If
our `__init__` method takes other arguments than self we can call the
class with arguments and they will be given to `__init__`. Like this:

```python
>>> class Website:
...     def __init__(self, url, founding_year, free_to_use):
...         self.url = url
...         self.founding_year = founding_year
...         self.free_to_use = free_to_use
...     def info(self):
...         print("URL:", self.url)
...         print("Founding year:", self.founding_year)
...         print("Free to use:", self.free_to_use)
...
>>> stackoverflow = Website('http://stackoverflow.com/', 2008, True)
>>> stackoverflow.info()
URL: http://stackoverflow.com/
Founding year: 2008
Free to use: True
>>>
```

Classes have many other magic methods too, but I'm not going to cover
them in this tutorial.

## When should I use classes?

Don't do this:

```python
class MyProgram:

    def __init__(self):
        print("Hello!")
        word = input("Enter something: ")
        print("You entered " + word + ".")


program = MyProgram()
```

You should avoid using things like `print` and `input` in the `__init__`
method. The `__init__` method should be simple and it should just set
things up.

Usually you shouldn't use a class if you're only going to make one
instance of it, and you don't need a class either if you're only going
to have one method. In this example `MyProgram` has only one method and
only one instance.

Make functions instead, or just write your code without any functions if
it's short enough for that. This program does the same thing and it's
much more readable:

```python
print("Hello!")
word = input("Enter something: ")
print("You entered " + word + ".")
```

## Summary

- Object-orientated programming is programming with custom data types.
  In Python that means using classes and instances.
- Use CapsWords for class names and lowercase_words_with_underscores for
  other names. This makes it easy to see which objects are classes and
  which objects are instances.
- Calling a class as if it was a function makes a new instance of it.
- `foo.bar = baz` sets `foo`'s attribute `bar` to `baz`.
- Use class attributes for functions and instance attributes for other
  things.
- Functions as class attributes can be accessed as instance methods.
  They get their instance as the first argument. Call that `self` when
  you define the method.
- `__init__` is a special method, and it's ran when a new instance of a
  class is created. It does nothing by default.
- Don't use classes if your code is easier to read without them.

***

If you have trouble with this tutorial please [tell me about
it](../contact-me.md) and I'll make this tutorial better. If you
like this tutorial, please [give it a
star](../README.md#how-can-i-thank-you-for-writing-and-sharing-this-tutorial).

You may use this tutorial freely at your own risk. See
[LICENSE](../LICENSE).

[Previous](modules.md) | [Next](../advanced/datatypes.md) |
[List of contents](../README.md#basics)
