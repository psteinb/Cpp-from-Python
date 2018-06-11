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

+++?image=img/laptop-at-night.jpeg

<div style="color: white; background-color: rgb(0, 102, 102,0.75);border-radius: 25px;padding: 20px;">
<h2>Reinvent the wheel over-and-over?</h2>
</div>

+++?image=img/diverse-shoes.jpeg&size=auto 80%

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
   "My documentation of mymath“, /* module documentation, may be NULL */
   -1, /* size of per-interpreter state of the module,
          or -1 if the module keeps state in global variables. */
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

### Not Enough?


