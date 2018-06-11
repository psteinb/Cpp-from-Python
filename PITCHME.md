---

## C++ from Python
- A Report from the trenches -

Peter Steinbach (steinbach@scionics.de, twitter.com/psteinb_ ), C++UG Dresden, June 12, 2018 

---

## Agenda

1. __Why__
2. __How__ (simple)
3. __How__ (for real)

---

## __Why__ other languages?

+++?image=img/laptop-at-night.jpeg&size=cover

<div style="color: white; background-color: rgb(0, 102, 102,0.75);border-radius: 25px;padding: 20px;">
<h2>Reinvent the wheel over-and-over?</h2>
</div>

+++?image=img/diverse-shoes.jpeg&size=cover

### Teams/Communities are diverse.

+++?image=img/weights.jpeg

### C++ is for the heavy lifting or latency bound tasks!

+++

## Python is dynamic

```python
$ python3                  
Python 3.6.5 (default, Apr  4 2018, 15:01:18) 
# ...
>>> i = 42
>>> print(i)
42
>>> i = "forty-two"
>>> print(i)
```

Note:
- Chiasm currently: py2 vs. py3
- py = dynamically typed

+++

## Python Manages Memory

```python
>>> tlist = [1,3,5,6]
>>> def foo(alist):
        value = list()
        value.extend(alist)
        alist.extend([11,22])
        return alist
>>> alist = foo(tlist)
>>> print(alist)
[1, 3, 5, 6, 11, 22]
```

Note:
- py = managed memory (reference counting)

+++

## Python Is Modular

```python
>>> import numpy
>>> a = numpy.zeros((10,))
>>> b = numpy.ones((10,))
>>> print(a+b)
array([ 1.,  1.,  1.,  1.,  1.,  1.,  1.,  1.,  1.,  1.])
```

Note:
- batteries included
- `numpy` very popular in data science and HPC

---

## How to interface?
(simple version)

+++

### A simple goal

```python
>>> import mymath
>>> mymath.add(2,3)
5.0
```

+++

### C/C++ Extension API to CPython

```c
//mymath.c
#include "Python.h"

static struct PyModuleDef mymathmodule = {
   PyModuleDef_HEAD_INIT,
   “mymath", /* name of module */
   "My documentation of mymath“, 
   /* module documentation, may be NULL */
   -1, /* size of per-interpreter module state,
          or -1 if the module keeps 
          state in global variables. */
   MyMethods
};

PyMODINIT_FUNC PyInit_mymath(void){
    return PyModule_Create(&mymathmodule);
}
```

Note:
*Only creating the module*

+++

### Extension API: Methods

```c
//mymath.c continued

static PyMethodDef MyMethods[] = {
{"add", module_function, METH_VARARGS, "Adds two numbers"},
{NULL, NULL, 0, NULL}
};

```

Note:
Populate the methods table

+++

### Extension API: Doing Math!

```c
//mymath.c continued

static PyObject* module_function(PyObject *self, PyObject *args){
    float a, b, c;
    if (!PyArg_ParseTuple(args, "ff", &a, &b)){
        return NULL;
    }
    
    c = a + b;
    
    return Py_BuildValue("f", c);
}
```

Note:
Finally we do some work!

+++?image=img/902px-Punishment_sisyph.jpg&size=auto 80%

<div style="color: white; margin-top: 60%;margin-left: 80%;font-size: 14px">
Titian, <a href="https://commons.wikimedia.org/wiki/File:Punishment_sisyph.jpg">Sisyphus</a>
</div>

+++

### Not Enough? Exporting a `struct`

```c
typedef struct {
    PyObject_HEAD
    PyObject *first; /* first name */
    PyObject *last;  /* last name */
    int number;
} PersonObject;

```

+++

### Doing Garbage Collection

```c
static void
Custom_dealloc(CustomObject *self)
{
    Py_XDECREF(self->first);
    Py_XDECREF(self->last);
    Py_TYPE(self)->tp_free((PyObject *) self);
}

static PyObject *
Custom_new(PyTypeObject *type, PyObject *args, PyObject *kwds)
{
    CustomObject *self;
    self = (CustomObject *) type->tp_alloc(type, 0);
    if (self != NULL) {
        self->first = PyUnicode_FromString("");
        if (self->first == NULL) {
            Py_DECREF(self);
            return NULL;
        }
        self->last = PyUnicode_FromString("");
        if (self->last == NULL) {
            Py_DECREF(self);
            return NULL;
        }
        self->number = 0;
    }
    return (PyObject *) self;
}
```

+++?image=img/old_tools.jpeg&size=cover

### Better Tools? 

Note:
- pure C API very verbose
- some information encoded in function name
- very C like interface

---

## How to interface?
(the 21st century version)

+++?image=img/pybind11-logo.png&size=70% auto

<div style="color: white; margin-top: 50%;font-size: 1.5em">

[github.com/pybind/pybind11](https://github.com/pybind/pybind11)

</div>

Note:
- 3.5k stars
- 500 forks

+++

### pybind11

> pybind11 is a lightweight header-only library that exposes C++ types in Python and vice versa

@ul

- Python 2.7, 3.x, and PyPy (PyPy2.7 >= 5.7) support
- header-only without dependencies
- Clang/LLVM 3.3 or newer, GCC 4.8 or newer, Microsoft Visual Studio 2015 Update 3 or newer, Intel C++ compiler 17 or newer, Cygwin/GCC
- BSD 3-clause license

@ulend

+++

### `mymath` revisited

```c++
//pybind11_math.cpp
#include <pybind11/pybind11.h>

int add(int i, int j) {
    return i + j;
}

namespace py = pybind11;

PYBIND11_MODULE(mymath,m) {
    m.doc() = "pybind11 math module";
    m.def("add", &add,"A function which adds two numbers");
}

```

+++

### PyBind11 embraces C++ developers

```cmake
cmake_minimum_required(VERSION 2.8.12)
project(mymath)

add_subdirectory(pybind11)
#the above adds cmake functionality specific to pybind11
pybind11_add_module(cmake_example src/main.cpp)
```

see [github.com/pybind/cmake_example](https://github.com/pybind/cmake_example)


Note:
- template project brings `setup.py` along
- easy integration of python-side unit testing etc

---?image=img/happy.jpeg&size=cover

### Awesome!

Note:
- pybind leverages a lot of the common day tasks 

## Discussion and Summary

+++

### What I didn't discuss

+++

###
