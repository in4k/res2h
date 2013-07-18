res2h
========

Load plain binary data from arbitrary files and dump them to a raw hex C/C++ array for compiling in your software. Inspired by [bin2h](http://code.google.com/p/bin2h/) with added functionality.
It should at least work in Windows, Ubuntu and Raspbian.

License
========

[BSD-3-Clause](http://opensource.org/licenses/BSD-3-Clause), see LICENSE.md

Building
========

**Use CMake:**
<pre>
cd res2h
cmake .
make
</pre>

G++ 4.7 and boost-filesystem are needed to compile. For installing G++ 4.7 see [here](http://lektiondestages.blogspot.de/2013/05/installing-and-switching-gccg-versions.html).

Install the boost-filesystem development package with:
```
sudo apt-get libboost<VERSION>-dev-filesystem
```

Usage
========
res2h <infile/directory> <outfile/directory> [options]
If infile is a directory, outfile must be a directory too. All therein contained files will be converted.
Valid options:
-s Recurse into subdirectories.
-c Use .c files and arrays for storing the data definitions, instead of using .cpp files.
-h <headerfile> Puts all declarations in the file "headerfile" using "extern" and includes that header file in all the source files.
-u <sourcefile> Create utility functions and arrays in a .c/.cpp file.

e.g. "res2h a.x b_x.cpp -h bla.h" creates those files:
- **a_x.cpp:**

```
//this file was auto-generated from a.x by res2h

#include "bla.h"

const size_t a_x_size = 123;
const unsigned char a_x_data[a_x_size] = {
    0x11,0x22,...
};
```

- **bla.h:**

```
//this file was auto-generated by res2h

extern const size_t a_x_size;
extern const unsigned char a_x_data[];
```

e.g.  "res2h a.x b_x.cpp -h bla.h **-u bla.cpp**" would create a_x.cpp too, and:
- **bla.h:**

```
//this file was auto-generated by res2h

extern const size_t a_x_size;
extern const unsigned char a_x_data[];

struct Res2hEntry {
    const std::string relativeFileName;
    const size_t size;
    const unsigned char * data;
};

//this contains all the resources with their names and data
extern const std::vector<const Res2hEntry> res2hVector;

//this maps the relative file name of resource to its data
//usage e.g: Res2hEntry resource = res2hMap["a.x"];
extern const std::map<const std::string, const Res2hEntry> res2hMap;
```

- **bla.cpp:**

```
//this file was auto-generated by res2h

#include "bla.h"

const std::vector<const Res2hEntry> res2hVector = {
 {"a.x", a_x_size, a_x_data}
};

const std::map<const std::string, const Res2hEntry> res2hMap = {
 std::pair<const std::string, const Res2hEntry>("a.x", {"a.x", a_x_size, a_x_data})
};
```

e.g.  "res2h a.x b_x.cpp **-c** -h bla.h -u bla.cpp" would create a_x.cpp too, and:
- **bla.h:**

```
//this file was auto-generated by res2h

extern const size_t a_x_size;
extern const unsigned char a_x_data[];

struct Res2hEntry {
    const char * relativeFileName;
    const size_t size;
    const unsigned char * data;
};

//this contains all the resources with their names and data
extern const size_t res2hVector_size;
extern const Res2hEntry res2hVector[];
```

- **bla.cpp:**

```
//this file was auto-generated by res2h

#include "bla.h"

const size_t res2hVector_size = 1;
const const Res2hEntry res2hVector[res2hVector_size] = {
 {"a.x", a_x_size, a_x_data}
};
```

I found a bug or have suggestion
========

The best way to report a bug or suggest something is to post an issue on GitHub. Try to make it simple, but descriptive and add ALL the information needed to REPRODUCE the bug. **"Does not work" is not enough!** If you can not compile, please state your system, compiler version, etc! You can also contact me via email if you want to.