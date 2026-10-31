refactor

A **stream** in C++ is an abstraction for data flowing between your program and some source or destination. 
Instead of writing completely different code for every kind of input and output, C++ provides a common stream system. Data might come from the keyboard, a file, or a string in memory, and it might go to the terminal, a file, or another string. The underlying destination changes, but many of the concepts used to interact with that data remain the same.

At a high level, think of a stream as a **channel through which data flows**:

```text
INPUT STREAM

source  ------>  stream  ------>  program


OUTPUT STREAM

program ------>  stream  ------>  destination
```

For example:

```text
keyboard ------> std::cin  ------> program
program  ------> std::cout ------> terminal

file     ------> std::ifstream ---> program
program  ------> std::ofstream ---> file
```

This abstraction is useful because your program doesn't always need to care about the low-level details of where the data physically comes from or where it eventually goes.

---

# The C++ Stream Family

C++ has a number of stream classes for different purposes.

Some of the most important are:

```text
std::istream          input stream
std::ostream          output stream
std::iostream         input + output stream

std::ifstream         input from files
std::ofstream         output to files
std::fstream          input + output with files

std::istringstream    input from a string
std::ostringstream    output to a string
std::stringstream     input + output with a string
```

The important thing is that these aren't completely unrelated systems.

They belong to the same general stream architecture.

For example:

```cpp
std::cout << 42;
```

and:

```cpp
file << 42;
```

use the same general idea: insert an integer into an output stream.

The destination is different.

---

# `<iostream>`

The first stream library most C++ programmers encounter is `<iostream>`.

```cpp
#include <iostream>
```

It provides the standard stream objects used for console-oriented input and output.

The main ones are:

```cpp
std::cin
std::cout
std::cerr
std::clog
```

Their purposes are roughly:

```text
std::cin    standard input
std::cout   standard output
std::cerr   standard error
std::clog   standard logging/error output
```

The two you'll use most often when learning C++ are:

```cpp
std::cin
std::cout
```

---

# `std::cout`

`std::cout` is the standard output stream.

When you write:

```cpp
std::cout << "Hello\n";
```

you're inserting data into an output stream.

The flow is roughly:

```text
"Hello"
   |
   v
std::cout
   |
   v
standard output
   |
   v
usually your terminal
```

The operator:

```cpp
<<
```

is called the **stream insertion operator** in this context.

You can insert many different types:

```cpp
int health{ 100 };
double speed{ 4.5 };
char grade{ 'A' };

std::cout << health << '\n';
std::cout << speed << '\n';
std::cout << grade << '\n';
```

The stream handles converting those C++ values into a textual representation.

For example, the integer:

```cpp
100
```

is a numeric value stored in memory.

The characters:

```text
"100"
```

are a textual representation of that value.

`std::cout` handles that conversion for you.

---

# `std::cin`

`std::cin` is the standard input stream.

For example:

```cpp
int age{};

std::cin >> age;
```

The operator:

```cpp
>>
```

is called the **stream extraction operator** in this context.

If the user enters:

```text
25
```

you can think of the process roughly as:

```text
keyboard
   |
   v
characters "25"
   |
   v
std::cin
   |
   v
parse as int
   |
   v
25
   |
   v
age
```

This means streams don't simply move bytes around. Formatted streams can also perform **conversion between textual representations and C++ types**.

---

# `std::cerr`

`std::cerr` represents the standard error stream.

Instead of:

```cpp
std::cout << "Could not open file\n";
```

you might write:

```cpp
std::cerr << "Could not open file\n";
```

The distinction is useful because normal output and error output can be treated separately.

Conceptually:

```text
                     ---> std::cout ---> normal output
program
                     ---> std::cerr ---> errors
```

They may both appear in the same terminal, but they are separate streams.

This matters particularly for command-line programs.

A user might redirect normal output into a file while leaving errors visible in the terminal.

---

# `std::clog`

There is also:

```cpp
std::clog
```

which is intended for logging and diagnostic information.

Conceptually:

```text
std::cout    normal output
std::cerr    errors
std::clog    logging / diagnostics
```

In practice, you'll encounter `std::cout` and `std::cerr` much more frequently.

---
# Writing Functions That Accept Streams

Imagine you wrote:

```cpp
void printHealth(int health)
{
    std::cout << "Health: " << health << '\n';
}
```

This function is permanently tied to `std::cout`.

But you could instead write something like:

```cpp
void printHealth(std::ostream& out, int health)
{
    out << "Health: " << health << '\n';
}
```

Now you can do:

```cpp
printHealth(std::cout, 100);
```

but you could also potentially pass a file output stream:

```cpp
std::ofstream file{ "log.txt" };

printHealth(file, 100);
```

The same function can output to different destinations because it operates on the abstraction:

```cpp
std::ostream
```

instead of specifically depending on:

```cpp
std::cout
```

This is one of the major advantages of the stream system.

---

# File Streams

C++ provides file streams through:

```cpp
#include <fstream>
```

The main types are:

```cpp
std::ifstream
std::ofstream
std::fstream
```

Their purposes are:

```text
ifstream    file input
ofstream    file output
fstream     file input + output
```

---

# `std::ofstream`

An output file stream lets you write data to a file.

```cpp
#include <fstream>

int main()
{
    std::ofstream file{ "hello.txt" };

    file << "Hello\n";
    file << 123 << '\n';
}
```

Compare:

```cpp
std::cout << "Hello\n";
```

with:

```cpp
file << "Hello\n";
```

The operation is almost identical.

Only the destination changes:

```text
std::cout

program
   |
   v
output stream
   |
   v
terminal
```

versus:

```text
std::ofstream

program
   |
   v
output stream
   |
   v
file
```

---

# `std::ifstream`

An input file stream lets you read data from files.

For example, imagine `numbers.txt` contains:

```text
10 20
```

You could write:

```cpp
#include <fstream>

int main()
{
    std::ifstream file{ "numbers.txt" };

    int x{};
    int y{};

    file >> x >> y;
}
```

Compare:

```cpp
std::cin >> x >> y;
```

with:

```cpp
file >> x >> y;
```

Again, the same general extraction system is being used.

The source changed:

```text
std::cin

keyboard
   |
   v
input stream
   |
   v
program
```

versus:

```text
std::ifstream

file
   |
   v
input stream
   |
   v
program
```

---

# `std::fstream`

`std::fstream` supports both reading and writing.

Conceptually:

```text
              read
file --------------------> program

              write
file <-------------------- program
```

So the broad relationship is:

```text
std::ifstream   read

std::ofstream  write

std::fstream   read + write
```

---

# String Streams

Streams don't even need to communicate with something external to the program.

C++ provides streams that operate on strings in memory through:

```cpp
#include <sstream>
```

Important types include:

```cpp
std::istringstream
std::ostringstream
std::stringstream
```

---

# `std::ostringstream`

An output string stream lets you construct formatted text in memory.

For example:

```cpp
#include <sstream>

int health{ 75 };
int armor{ 50 };

std::ostringstream output;

output << "Health: " << health
       << ", Armor: " << armor;
```

You can then retrieve the resulting string:

```cpp
std::string text{ output.str() };
```

which contains:

```text
Health: 75, Armor: 50
```

The flow is:

```text
C++ values
    |
    v
std::ostringstream
    |
    v
formatted characters
    |
    v
std::string
```

This is useful when you want stream-style formatting without actually printing anything.

---

# `std::istringstream`

An input string stream does the opposite.

Suppose you have:

```cpp
std::string text{ "100 50" };
```

You can treat that string like an input stream:

```cpp
#include <sstream>

std::istringstream input{ text };

int health{};
int armor{};

input >> health >> armor;
```

Now:

```text
health = 100
armor  = 50
```

Conceptually:

```text
"100 50"
    |
    v
std::istringstream
    |
    +----> 100
    |
    `----> 50
```

This can be useful when parsing formatted text.

---

# The Same Interface Across Different Streams

This is where the design starts becoming clear.

Consider:

```cpp
std::cout << x;
```

```cpp
file << x;
```

```cpp
stringStream << x;
```

These might mean:

```text
x ---> terminal

x ---> file

x ---> string
```

But from the perspective of your C++ code, all three are forms of:

```text
value ---> output stream
```

Likewise:

```cpp
std::cin >> x;
```

```cpp
file >> x;
```

```cpp
stringStream >> x;
```

can mean:

```text
keyboard ---> x

file ---> x

string ---> x
```

The source changes, but the concept remains:

```text
input stream ---> value
```

This is the core idea behind C++ streams.

---

# Streams Are Usually Buffered

Streams commonly use **buffers**.

A buffer is an area of memory used to temporarily hold data.

Instead of:

```text
program ---> destination
```

the actual process may be closer to:

```text
program
   |
   v
stream
   |
   v
buffer
   |
   v
destination
```

For example:

```cpp
std::cout << "Hello";
```

doesn't necessarily mean those characters are immediately displayed by the terminal at that exact moment.

They may temporarily sit in an output buffer.

Eventually, that buffer is **flushed**.

Flushing means:

> Send pending buffered output toward its destination.

---

# Why Buffering Exists

Interacting with external resources can be relatively expensive.

For example, imagine sending:

```text
H
```

then separately:

```text
e
```

then:

```text
l
```

then:

```text
l
```

then:

```text
o
```

Performing expensive lower-level I/O for every character would be inefficient.

Instead, buffering allows data to accumulate:

```text
H
e
l  ---> [ Hello ] ---> destination
l
o
```

This allows multiple pieces of data to be transferred together.

The basic principle appears throughout systems programming:

```text
many tiny operations
        |
        v
usually expensive


batching operations
        |
        v
often more efficient
```

You'll encounter buffers far beyond C++ streams.

They appear in:

- Files
- Networking
- Graphics
- Audio
- Compression
- Encoding
- Databases
- Operating system APIs

---

# Flushing Streams

You can explicitly request that an output stream flush its buffer:

```cpp
std::cout << std::flush;
```

You can also use:

```cpp
std::endl
```

which effectively performs:

```text
newline
+
flush
```

That's why:

```cpp
std::cout << "Hello\n";
```

is generally preferred over:

```cpp
std::cout << "Hello" << std::endl;
```

when you only need a newline.

Think:

```text
'\n'          newline

std::flush    flush

std::endl     newline + flush
```

---

# Streams Perform Formatting

Streams don't simply transport values.

They can also control how those values are represented.

For example:

```cpp
#include <iomanip>
#include <iostream>

double pi{ 3.1415926535 };

std::cout << std::fixed
          << std::setprecision(2)
          << pi
          << '\n';
```

produces:

```text
3.14
```

The underlying value of `pi` hasn't changed.

Only its textual representation has changed.

Likewise:

```cpp
int x{ 255 };

std::cout << std::dec << x << '\n';
std::cout << std::hex << x << '\n';
std::cout << std::oct << x << '\n';
```

can produce:

```text
255
ff
377
```

All three represent the same underlying integer.

So streams can perform:

```text
C++ value
    |
    v
formatting
    |
    v
textual representation
```

---

# Streams Have State

A stream is an object, and that object maintains information about whether its operations are succeeding.

For example:

```cpp
int x{};

std::cin >> x;
```

If the user enters:

```text
123
```

the extraction succeeds.

If the user enters:

```text
hello
```

the stream cannot interpret the input as an integer.

The extraction fails.

The stream remembers that failure.

Important stream states include:

```text
good    everything is okay

fail    an operation failed

eof     the end of the input was reached

bad     a serious stream error occurred
```

You can query them:

```cpp
stream.good();
stream.fail();
stream.eof();
stream.bad();
```

For example:

```cpp
int x{};

if (std::cin >> x)
{
    std::cout << "Successfully read " << x << '\n';
}
else
{
    std::cerr << "Invalid input\n";
}
```

The expression:

```cpp
std::cin >> x
```

performs the extraction and then allows the resulting stream state to be checked.

Streams therefore aren't simply pipes that blindly transport data.

They also keep track of whether the data operations are working.

---

# Streams Convert Between Representations

One of the most important things streams do is convert between:

```text
C++ values
```

and:

```text
external/textual representations
```

For output:

```text
int
 |
 v
123
 |
 v
"123"
 |
 v
output stream
```

For input:

```text
input stream
 |
 v
"123"
 |
 v
parse
 |
 v
123
 |
 v
int
```

This distinction becomes increasingly important as you learn systems programming.

The integer:

```cpp
123
```

and the characters:

```text
'1' '2' '3'
```

are not the same thing.

One is a numeric value.

The other is a representation of that numeric value as text.

Streams can handle conversions between the two.

---

# Binary vs Text I/O

Streams aren't limited to formatted text.

File streams can also be opened in **binary mode**:

```cpp
std::ofstream file{
    "data.bin",
    std::ios::binary
};
```

This becomes useful when you want to work with raw bytes rather than normal textual formatting.

For example, an image, executable, compressed file, or custom binary format isn't simply a collection of human-readable numbers and words.

It consists of bytes structured according to some format.

This distinction is important:

```text
TEXT I/O

123
   |
   v
"123"
   |
   v
three textual characters
```

versus conceptually:

```text
BINARY I/O

123
   |
   v
raw binary representation
```

For binary operations, streams provide functions such as:

```cpp
stream.read(...);
stream.write(...);
```

rather than relying exclusively on:

```cpp
>>
<<
```

For example:

```cpp
file.write(data, size);
```

can write a sequence of bytes.

This becomes particularly relevant when working with binary file formats.

---

# `<<` and `>>` Work Because of Operator Overloading

The operators:

```cpp
<<
>>
```

aren't inherently stream operators.

For example:

```cpp
int x{ 4 };

int y{ x << 1 };
```

Here:

```cpp
<<
```

is the bitwise left-shift operator.

But:

```cpp
std::cout << x;
```

uses an overloaded version of `operator<<` designed for output streams.

Similarly:

```cpp
std::cin >> x;
```

uses an overloaded `operator>>` for input streams.

This is why streams can work naturally with many types.

---

# Streams Can Work With Your Own Types

You can eventually define stream behavior for your own classes.

Suppose:

```cpp
class Vector2
{
public:
    float x{};
    float y{};
};
```

You could define an output operator so this works:

```cpp
Vector2 position{ 10.0f, 20.0f };

std::cout << position;
```

and produces:

```text
(10, 20)
```

The same object could then potentially be sent into another output stream:

```cpp
file << position;
```

because you're defining how a `Vector2` interacts with an **output stream**, not necessarily how it interacts specifically with the terminal.

That's another example of why the abstraction matters.

---

# Stream Chaining

You've probably seen:

```cpp
std::cout << "Health: " << health << '\n';
```

This works because stream insertion operations return the stream again.

Conceptually:

```cpp
std::cout << "Health: "
```

returns `std::cout`, allowing:

```cpp
<< health
```

which returns the stream again, allowing:

```cpp
<< '\n'
```

So:

```cpp
std::cout << a << b << c;
```

can roughly be understood as:

```text
std::cout
   |
   +-- insert a
   |
   +-- insert b
   |
   `-- insert c
```

The same idea applies to:

```cpp
std::cin >> x >> y >> z;
```

---

# Streams Are an Abstraction Over Lower-Level I/O

C++ streams are relatively high-level.

Eventually, especially in systems programming, you'll encounter lower-level APIs.

The stack can be imagined roughly as:

```text
HIGH LEVEL

C++ program
     |
     v
stream abstraction
     |
     v
formatting / parsing
     |
     v
buffering
     |
     v
standard library implementation
     |
     v
operating system I/O
     |
     v
file / terminal / device

LOW LEVEL
```

At the operating-system level, you may encounter things such as:

```text
handles
file descriptors
raw buffers
byte counts
system calls
error codes
devices
sockets
```

Streams hide much of that complexity behind a common C++ interface.

Sometimes that's exactly what you want.

Other times, particularly when you need precise control or very high performance, you may use lower-level APIs instead.

---

# The Main Stream Categories

A useful way to organize the major streams in your head is:

```text
C++ STREAMS
|
+-- Console / Standard Streams
|   |
|   +-- std::cin
|   +-- std::cout
|   +-- std::cerr
|   `-- std::clog
|
+-- File Streams
|   |
|   +-- std::ifstream
|   +-- std::ofstream
|   `-- std::fstream
|
`-- String Streams
    |
    +-- std::istringstream
    +-- std::ostringstream
    `-- std::stringstream
```

And underneath those are the broader concepts:

```text
std::istream     input

std::ostream     output

std::iostream    input + output
```

---

# The Bigger Mental Model

Instead of memorizing every individual stream class separately, think about the system like this:

```text
                       STREAM

SOURCE  -----------> [ DATA FLOW ] -----------> DESTINATION
                           |
                           |
                           +-- buffering
                           |
                           +-- formatting
                           |
                           +-- parsing
                           |
                           +-- type conversion
                           |
                           +-- state/error tracking
                           |
                           `-- common interface
```

Different streams simply connect that system to different places.

For input:

```text
keyboard ----> std::cin -----------+
                                     |
file --------> std::ifstream ------->+----> your program
                                     |
string ------> std::istringstream --+
```

For output:

```text
                                     +----> std::cout --------> terminal
                                     |
your program ------------------------+----> std::ofstream ----> file
                                     |
                                     +----> std::ostringstream -> string
```

That is the main idea to understand.

A stream is not synonymous with a terminal, a file, or a string. A **stream is the abstraction through which your program reads or writes data**.

Once you understand that, `<iostream>` becomes just one part of a much larger system:

```text
iostream  -> standard input/output
fstream   -> file input/output
sstream   -> string input/output
```

All of them build on the same fundamental idea: **data flowing through a consistent interface between your C++ program and some source or destination**.