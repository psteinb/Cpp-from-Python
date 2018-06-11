---

## C++ from Python

### A Report from the trenches

Peter Steinbach (steinbach@scionics.de, @psteinb_), C++UG Dresden, June 12, 2018 

---

### Agenda

1. __Why__
2. __How__ (simple)
3. __How__ (for real)

---

### __Why__ other languages?


+++?image=img/laptop-at-night.jpeg

&nbsp;
&nbsp;

<div style="color: white; background-color: grey;">
Reinvent the wheel over-and-over?<br/>
Invalidate other people's work?
</div>

+++?image=img/weights.jpeg

<div style="color: white">
C++ is for the heavy lifting!
</div>

+++?image=img/diverse-shoes.jpeg&size=auto 80%

<div style="color: white">
Teams are diverse.
</div>

+++

# Python

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

# Python continued

```python
>>> tlist = [1,3,5,6]
>>> def foo(alist):
        alist.extent([11,22])
        return alist
>>> alist = foo(tlist)
>>> print(alist)
[1, 3, 5, 6, 11, 22]
```

Note:
- py = managed memory (reference counting)

+++

# Python modular

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
