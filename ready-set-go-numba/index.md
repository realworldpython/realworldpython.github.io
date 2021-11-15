# mandelbrot-numba

**This repository is based on the PyData Global 2020 & PyCon Sweden 2020 talk on `numba` which can be accessed [here](https://realworldpython.github.io/pycon-sweden-2020/).**

This repository contains the Mandelbrot Set Generator code with a GUI and zoom feature.  
It is a good starting point for new `numba` users to witness the various ways in which one can achieve code speed-up using `numba`.

Each implementation has a dedicated branch and the recommended learning sequence that should be followed is provided below:

| Branch | Data Layer | Key feature | `parallel` execution | Link |
|--|--|--|--|--|
| `python-list` | Native `list` | - | - | [Link](https://github.com/animator/mandelbrot-numba/tree/python-list) |
| `njit-python-list` | Native `list` | `@numba.njit` decorator | - | [Link](https://github.com/animator/mandelbrot-numba/tree/njit-python-list) |
| `njit-parallel-python-list` | Native `list` | `@numba.njit` decorator | Yes | [Link](https://github.com/animator/mandelbrot-numba/tree/njit-parallel-python-list) |
| `numpy-array` | NumPy array | - | - | [Link](https://github.com/animator/mandelbrot-numba/tree/numpy-array) |
| `njit-numpy-array` | NumPy array | `@numba.njit` decorator | - | [Link](https://github.com/animator/mandelbrot-numba/tree/njit-numpy-array) |
| `njit-parallel-numpy-array` | NumPy array | `@numba.njit` decorator | Yes | [Link](https://github.com/animator/mandelbrot-numba/tree/njit-parallel-numpy-array) |
| `numpy-vectorize` | NumPy array | `@numpy.vectorize` decorator | - | [Link](https://github.com/animator/mandelbrot-numba/tree/numpy-vectorize) |
| `numba-vectorize` | NumPy array | `@numba.vectorize` decorator | - | [Link](https://github.com/animator/mandelbrot-numba/tree/numba-vectorize) |
| `parallel-numba-vectorize` | NumPy array | `@numba.vectorize` decorator | Yes | [Link](https://github.com/animator/mandelbrot-numba/tree/parallel-numba-vectorize) |


To execute the code just run:

```
$ python3 mandelbrot.py
```

A Tkinter GUI will pop-up. You can use `left` mouse button to drag and select an area to zoom into. `Right` click to reset the canvas.

![](mandelbrot.gif)

Don't forget to install the following dependencies:

```
$ pip3 install numba numpy Pillow
```

`Tk` comes pre-installed with Python.


# mandelbrot-numba

numba Pillow

Main repository for the project & blog - [Link](https://realworldpython.github.io)

# python-list

Mandelbrot set computation with the following characteristics:

- Python `list` of `list` of RBG tuples to store the computed RBG pixel values.

# njit-python-list

Mandelbrot set computation with the following characteristics:

- Python `list` of `list` of RBG tuples to store the computed RBG pixel values.
- `@njit` decorator

# njit-parallel-python-list

Mandelbrot set computation with the following characteristics:

- Python `list` of `list` of RBG tuples to store the computed RBG pixel values.
- `@njit` decorator
- `parallel` option set as `True` with explicit `prange` (parallel `for` loop)

# numpy-array

Mandelbrot set computation with the following characteristics:

- NumPy array to store the computed RBG pixel values.

# njit-numpy-array

Mandelbrot set computation with the following characteristics:

- NumPy array to store the computed RBG pixel values
- `@njit` decorator

# njit-parallel-numpy-array

Mandelbrot set computation with the following characteristics:

- NumPy array to store the computed RBG pixel values.
- `@njit` decorator
- `parallel` option set as `True` with explicit `prange` (parallel `for` loop)

# numpy-vectorize

Mandelbrot set computation with the following characteristics:

- NumPy array to store the computed RBG pixel values.
- Using `numpy.vectorize` to create a vectorized function which uses broadcasting rules insted of `for` loops.

# numba-vectorize

Mandelbrot set computation with the following characteristics:

- NumPy array to store the computed RBG pixel values.
- Using `numba.vectorize` to create a numpy `ufunc` which uses broadcasting rules insted of `for` loops.

# parallel-numba-vectorize

Mandelbrot set computation with the following characteristics:

- NumPy array to store the computed RBG pixel values.
- Using `numba.vectorize` to create a numpy `ufunc` which uses broadcasting rules insted of `for` loops.
- The `ufunc` also executes in parallel.