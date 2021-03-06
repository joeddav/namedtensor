# NamedTensor

## Introduction

A proposal for a named tensor for PyTorch described in the blog post:

http://nlp.seas.harvard.edu/NamedTensor

Currently the library targets the PyTorch ecosystem and Python >= 3.6.

## Usage

```python
from namedtensor import ntorch
```

### Building tensors.

All pytorch builders have an extra keyword argument names.

```python
x = ntorch.randn(10, 10, 20, names=("batch", "h", "w"))
x = ntorch.ones(10, 10, 20, names=("batch", "h", "w"))
```


### Standard functions

All functions that keep dimensionality work in the same way.

```python
x = x.log()
x = x.float()
x = ntorch.exp(x)
```

### View and Tranposition

View, tranpose, and friends are deprecated in favor of named
access and movement.


```python
x = x.stack(("w", "h"), "stackdim")

# Roundtrip

x = x.split("stackdim", ("w", "h"), w=20)
```

Transposition:

```python
x = x.transpose("batch", "w", "h")

# or early dim stay in place

x = x.transpose("w", "h")
```

### Dim replacements:

Any function with a `dim` argument now can be accessed based on the
dimension name.

```python
x = x.narrow("w", 0, 10)
x = x.softmax("w")
```

This is true of reductions functions as well, where the named
dimension is eliminated.

```python
x = x.mean("w")
x, argmax = x.max("w")
```

### Tensor contractions

Matrix operations also use the dimension arguments.
We can replace einsum based on persistent names.

```python

x = ntorch.randn(10, 10, 20, names=("batch", "h", "w"))
y = ntorch.randn(10, 20, 30, names=("batch", "w", "c"))
x.dot("w", y)
```

This also makes indexing much easier to read.

```python

x = ntorch.ones(10, 10, 20, names=("batch", "time", "vocab"))
y = ntorch.randn(20, 30, names=("vocab", "embsize"))
y.index_select("vocab", x)
```


### NN Modules

This api part is a work in progress. But many units are implemented to
work with named tensor.

```python

linear = ntorch.nn.Linear(20, 25)
x = linear(x)

# or

linear.rename(wout="w")
x = linear(x)

```

## Other Goodies
* Named NN
* Named Distributions libary

## Documentation

http://nlp.seas.harvard.edu/namedtensor/

## Author

* Alexander Rush (srush@seas.harvard.edu, @harvardnlp)

## Contributors

* Yuntian Deng
* Francisco Rivera
* Jiafeng Chen
* Celine Liang
* Miro Furtado
