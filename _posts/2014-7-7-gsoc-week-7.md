---
layout: post
title: Start implementing python bindings
excerpt: "GSoC 2014 Week 7."
modified: 2014-7-7
tags: [week7, gsoc2014]
comments: true
---

This week I started implementing python bindings for the Monkey library. There are at least 3 ways to do it:

-   [ctypes](https://docs.python.org/2/library/ctypes.html)
-   [swig](http://www.swig.org/tutorial.html)
-   [cython](http://docs.cython.org/index.html)

*ctypes* has no requirements, is very simple to use but comes with a performance penalty.

*swig* and *cython* are similar in a way, but, although using *swig* it is possible to easily extend support to other languages than python, I decided to stick with *cython* as it offers a more close bound to C language and handles memory allocations in a better way.

Following the implementation of the Monkey library, I seek something similar to this:

{% highlight python %}
import monkey

s = monkey.Server()
s.configure(port=2001, documentroot='htdocs/')
s.start()

# ...

s.stop()

{% endhighlight %}

There are two steps to achieve this:

1. Write a *pxd* file (it imports data types and definitions from the c library)
2. Write a *pyx* file (python (interface) implementation of the library)

The *pyx* file looks like mixed python and C code and compiles to a shared object (monkey.so) which will be recognised as a python module by the interpreter. It compiles with aid of a *setup.py* file:

{% highlight python %}
from distutils.core import setup
from distutils.extension import Extension
from Cython.Build import cythonize

setup(ext_modules = cythonize(
    [
        Extension("monkey", ["monkey.pyx"], libraries=["monkey"])
    ]
    )
)
{% endhighlight %}

{% highlight bash %}
$ python setup.py build_ext -i
{% endhighlight %}
