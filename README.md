# Cython-Asyncio-Types-Tool
An `_asynciomodule.c` generator tool for recast hacking objects from 
low level asyncio objects with the fastest performance possible. 
Winloop will likely see this trick utilized and maybe uvloop in a later update.


## Supported Versions
As many as uvloop supports which is 3.9-3.16 (3.16) has yet to get a stable
release but is supported nevertheless...

## Dependencies
- `msgspec`
- `toml-w`
- `typer`
- `libclang` (preferably it comes with a dynamically linked library file) /
  `clang` will also work if you can handle the configuration hell.
- `cython` obviously and (faster tempita library to go along with it)


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

