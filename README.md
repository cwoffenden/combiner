# Single File Library Amalgamation Tool for C and C++

[![Static Type Checker](https://github.com/cwoffenden/combiner/actions/workflows/mypy.yml/badge.svg)](//github.com/cwoffenden/combiner/actions/workflows/mypy.yml)

This `combine.py` script _amalgamates_ C and C++ sources, with the idea to create a single source file that's easy to integrate into projects.

To use, just create a source file that lists the files to combine using `#include` directives, along with any relevant flags, e.g.:
```c
#define DISABLE_FEATURE

#include "my-header.c"
#include "my-file-1.c"
#include "my-file-2.c"

```
Then tell the script where to find the sources, this descriptor file, and where to write the output:
```sh
python3 combine.py -r sources/root/folder -o my-lib.c input.c
```
The included files, plus their included files recursively, will be combined into the single output file (or `stdout` if no output is supplied). You can specify files to exclude (using the `-x` option) for headers you wish to keep standalone (using the `-k` option). The options are documented in the help:
```
% ./combine.py --help
usage: combine.py [-h] [-r ROOT] [-x EXCLUDE] [-k KEEP] [-p] [-o OUTPUT] input

Amalgamate Tool

positional arguments:
  input                 input file

optional arguments:
  -h, --help            show this help message and exit
  -r ROOT, --root ROOT  file root search path
  -x EXCLUDE, --exclude EXCLUDE
                        file to completely exclude from inlining
  -k KEEP, --keep KEEP  file to exclude from inlining but keep the include
                        directive
  -p, --pragma          keep any "#pragma once" directives (removed by
                        default)
  -o OUTPUT, --output OUTPUT
                        output file (otherwise stdout)

example: ./combine.py -r ../my/path -r ../other/path -o out.c in.c
```
See the Zstandard project for [examples of using the script](//github.com/facebook/zstd/tree/dev/build/single_file_libs), along with sample code showing the generated single-file libraries used. Here, in the [decompression library example](//github.com/facebook/zstd/blob/dev/build/single_file_libs/examples/simple.c), all the optimisation flags and build choices are added into [`zstddeclib-in.c`](//github.com/facebook/zstd/blob/dev/build/single_file_libs/zstddeclib-in.c), the resulting file `#include`d, then a single call to `ZSTD_decompress()` is all it takes to use.
