# pycdump

Unmarshal, disasm and pretty-print Python 3.7 bytecode.

## Intro

This repository documents some experiments in python bytecode analysis (`pyc` files).

The work is inspired by Ned Batchelder blog post http://nedbatchelder.com/blog/200804/the_structure_of_pyc_files.html

Some of the changes from 2 -> 3 are documented in https://www.python.org/dev/peps/pep-3147/.
The documentation is a bit out of date. In recent version of Python for instance, `imp` (referenced in the 
PEP) has been deprecated in favour
of `importlib`. 
On `3.7.3`:

````
>>> import importlib 
>>> hasattr(importlib, 'get_tag')
False
````

Things are bound to further change in the future https://www.python.org/dev/peps/pep-0552/


## File structure

According to documentation a `pyc` file is composed of a 16 byte header - 4 32-bit words - and a variable size payload.

 - 4B magic number: used to differentiate which version of Python generated the file. See https://docs.python.org/3/library/importlib.html#importlib.util.MAGIC_NUMBER
 - 4B bit field (PEP0552). At least on 3.7.3 this is left empty.
 - 4B modification date: the source file timestamp 
 - 4B file size: the source file size
 
From byte 16 onwards the payload stores a marshalled code object (https://docs.python.org/3/c-api/code.html). 

Code objects provide these attributes (and a couple more):
- co_argcount     number of arguments (not including * or ** args)
- co_code         string of raw compiled bytecode
- co_consts       tuple of constants used in the bytecode
- co_filename     name of file in which this code object was created
- co_firstlineno  number of first line in Python source code
- co_flags        bitmap: 1=optimized | 2=newlocals | 4=*arg | 8=**arg
- co_lnotab       encoded mapping of line numbers to bytecode indices
- co_name         name with which this code object was defined
- co_names        tuple of names of local variables
- co_nlocals      number of local variables
- co_stacksize    virtual machine stack space required
- co_varnames     tuple of names of arguments and local variables

co_consts is a nested data structure that can contain code object instances 

## Compile a py to pyc

```$xslt
python -m compileall example.py 
```

## Disasm & dump

```$xslt
python dump.py example.pyc
```
