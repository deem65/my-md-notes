Programs are collections of instructions that manipulate data to produce a desired result.

Computer programs (both in source code format and compiled) are technically data too, since they can be moved, processed, and stored. However, in the context of a computer program, we typically use the term “code” to mean the program itself, and “data” to mean the information that the program works with to produce a result.

In programming, a single piece of data is called a *value* or *data value*.
Examples of values include: 
- Numbers: `5` or `-6.2`.
- Characters: `H` or `$`.
- Text: `Hello` or `H`. 
Values placed in single-quotes are interpreted by the compiler as character values.  
Values placed in double-quotes are interpreted by the compiler as text values.  
Numeric values are not quoted.
### Random Access Memory
When a program is run, the operating system loads the program into RAM.
The OS also reserves some additional RAM for the program to use while it is running. 
In some older programming languages (like Applesoft BASIC), you could directly access these boxes (e.g. you could write a statement to “go get the value stored in membox number 7532”).
### Objects and variables
In C++, direct memory access is discouraged. Instead, we access memory indirectly through an object. 

An **object** represents a region of storage (typically RAM or a CPU register) that can hold a value. Objects also have associated properties (that we’ll cover in future lessons).

How the compiler and operating system work to assign memory to objects is beyond the scope of this lesson. But the key point here is that rather than say “go get the value stored in mailbox number 7532”, we can say, “go get the value stored by this object” and let the compiler figure out where and how to retrieve the value. This means we can focus on using objects to store and retrieve values, and not have to worry about where in memory those objects are actually being placed.

Although objects in C++ can be unnamed (anonymous), more often we name our objects using an identifier. 

An object with a name is called a **variable**.