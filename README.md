# Cython-Asyncio-Types-Tool
An `_asynciomodule.c` recasting mechanism that allows cython and asyncio
to `Future` and `Task` type objects to unlock there true speed and potential.

## Supported Versions
As many as uvloop supports which is 3.9-3.16, while 3.16 has yet to get a stable
release, it is supported nevertheless...


## Example

```cython
# cython: freethreading_compatible = True
# future_ext.pyx
# NOTE: freethreading_compatible should work by default.

from asyncio_types cimport Future

cdef class CyFuture(Future):
    """test to see if cython cdef extension futures work correctly"""
    cdef:
        str item
    
    def __init__(self, *, loop=None) -> None:
        super().__init__(loop=loop)
        self.item = ""
    
    # part of testing that newer objects can be applied
    cpdef set_item(self, str item):
        self.item = item

    cpdef str get_item(self):
        return self.item

```

```python
# you can really use asyncio if you want to.
# from asyncio import run
from winloop import run

from future_ext import CyFuture

# So far 3.11 has been tested and proven to work.

async def test():
    print("making the object...")
    item = CyFuture()
    print("CyFuture made")
    print(item)
    item.set_item("Hi I am an item")
    print("testing await")
    item.set_result(None)
    await item
    print("IT WORKS!!!")
    assert item.get_item() == "Hi I am an item"

if __name__ == "__main__":
    run(test())

```

