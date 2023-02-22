# `conan new cmake_lib` does not preserve `include` paths

I am using conan release `2.0.0`. I start a new project with `conan new cmake_lib`. `conan create` works as expected. So far so good.

Now, I would like the headers for my library to have a *folder prefix* when being included.

For example, with this include structure:

```comment
include/
  hello/
    hello.h
```

the *consumer* source files would `#include` like this:

```c++
#include "hello/hello.h"
```

When I modify the `include` paths to do this, building the package works but using the package fails (e.g. `test_package` fails) because the `include` paths are not kept.

How can I fix this?

## Steps to Reproduce

Using conan release `2.0.0`, do the following to reproduce this problem.

### Create new cmake_lib

From the command line, execute the following:

```shell
cd path/to/project
conan new -d name=hello -d version=1.0.0 cmake_lib
```

This should generate the following in `path/to/project`:

```comment
include/
  hello.h
src/
  hello.cpp
test_package/
  src/
    example.cpp
  CMakeLists.txt
  conanfile.py
CMakeLists.txt
conanfile.py
```

Running `conan create` in this folder is successful.

### Add include *folder prefix*

First, move the header file to its new prefixed location.

The `include` folder should now look like:

```comment
include/
  hello/
    hello.h
```

Next, update `*.cpp` files to use the folder prefix; change each include from

```c++
#include "hello.h"
```

to

```c++
#include "hello/hello.h"
```

Now, `conan build` succeeds. Unfortunately, `test_package` now fails:

```comment
hello/1.0.0 (test package): RUN: cmake --build ".../hello/test_package/build/apple-clang-14-x86_64-gnu17-release" -- -j8
[ 50%] Building CXX object CMakeFiles/example.dir/src/example.cpp.o
.../hello/test_package/src/example.cpp:1:10: fatal error: 'hello/hello.h' file not found
#include "hello/hello.h"
         ^~~~~~~~~~~~~~~
1 error generated.
```

Looking at the local package, I see that include paths are not kept:

```comment
~/.conan2/p/hello2422085b23957/
  p/
    include/
      hello.h
    lib/
      libhello.a
```

## Project Final Result

This repo serves as a working example of the problem I am encountering.

To use it to reproduce the error, clone this repo and then run the included script: `scripts/conan/create`.
