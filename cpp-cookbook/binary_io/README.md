# Read/Write Files

## Endianness

Todo


## A Number of Options

There are many options to read binary files in C/C++:

* The standard C function `read`.
* The standard C function `fread`
* C++ `ifstream`, very similar to `fread`.
* Memory mapping.

We dicuss each in details

### The UNIX C `read()`

The function is available in the unix header file `<unistd.h>`. It makes a **system call**. To open/create files, we also need to use functions from `<fcntl.h>`.

```
Family read() -> open, close, read, write, lseek
```

Programming details:

```c++

#include <fcntl.h>  // manipulate file descriptor

int open(const char* path, int oflag, ... /* mode_t mode */ ); 
// One of the following five oflags must be specified: 
//     O_RDONLY, O_WRONLY, O_RDWR, O_EXEC, O_SEARCH.
// Some commonly used oflags, O_APPEND, O_CREAT, O_NONBLOCK etc. Refer to APUE for 
// a full list of oflags.
// Triple dots ... is only used when a new file is being created.

int create(const char *path, mode_t mode);

#include <unistd.h>

int close(int fd);

off_t lseek(int fd, off_t offset, int whence);
// whence can be SEEK_SET, SEEK_CUR, or SEEK_END

ssize_t read (int fd, void *buf, size_t nbytes);
ssize_t write(int fd, void *buf, size_t nbytes);

// all functions return -1 on error, even with ssize_t return type
```

### The C Function `fread()`

`fread()` is part of the C library (`libc`), and provides buffered reads. It is usually implemented by calling `read()` in order to fill its buffer. 

```
Family fread() -> fopen, fclose, fread, fwrite, fgetc, fputc, fseek
```

Programming details:

```c++
#include <cstdio>

FILE * fopen ( const char * filename, const char * mode );
// modes: r, w, a, r+, w+, a+.
// return NULL on error

size_t fread ( void * ptr, size_t size, size_t count, FILE * stream );
// size: size of each element to be read
// count: number of elements.

size_t fwrite ( const void * ptr, size_t size, size_t count, FILE * stream );

int fclose ( FILE * stream );  // return EOF on error
```


### The C++ `fstream`

This is similar to `fread`, but with a more object-oriented flavor.

Programming details:

```c++
#include <fstream>

ifstream();
explicit ifstream( const char* filename, ios_base::openmode mode = ios_base::in );
// another popular option for mode is std::ofstream::binary

void ifstream::open(const   char* filename,  ios_base::openmode mode = ios_base::in);
void ifstream::open(const string& filename,  ios_base::openmode mode = ios_base::in);

istream& read (char* s, streamsize n);

istream& seekg (streampos pos);
istream& seekg (streamoff off, ios_base::seekdir way);
// way could be ios_base::beg, ios_base::end, or ios_base::cur (member variables)

streampos tellg();  // return the current position

ofstream();
explicit ofstream (const char* filename, ios_base::openmode mode = ios_base::out);
// another popular option for mode is std::ofstream::binary

ostream& write (const char* s, streamsize n);
```

Demo:

```c++
ifstream in(name,ios::binary);
vector<int> numbers(512);
in.read(reinterpret_cast<char *>(&numbers[0]), sizeof(int) * 512);
in.close();
```

```c++
// get length of file:
std::ifstream is("test.txt", std::ifstream::binary);
if (is) {
    is.seekg(0, is.end);
    int length = is.tellg();
    is.seekg(0, is.beg);
}
```



