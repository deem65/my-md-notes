A computer program is a sequence of instructions that tell the computer what to do. 
### Comments
*Comments* are notes in the source code of the program for programmers to see. 
- Comments are ignored by the compiler. 
- The `//` symbol begins a C++ single line comment. 
- The `/*` and `*/` pair of symbols denotes C-style multiline comments.
- Typically, comments are used for three things:
	1. Describe **what** the library, function, or program does. These are typically placed at the top of the file or library, or immediately preceding the function. 
	2. Describe **how** the code is going to accomplish its goal. These comments give the user an option to understand how the code works without having to read through it. 
	3. Describe **why** the code is doing something **at the statement level.**
		- A bad **statement** comment explains **what** the code is doing. If you ever write code that is so complex that needs a comment to explain **what** a statement is doing, you probably need to rewrite the statement. 
### Statements
A *statement* is a type of instruction that causes the program to perform some action.
- Statements are the smallest independent unit of computation in C++. 
- Statements typically end in a ; in the C++ language.
- There are different kind of statements in C++:
	- Decatenation statements
	- Jump statements
	- Expression statements
	- Compound statements
	- Selection statements (conditionals)
	- Iteration statements (loops)
	- Try blocks
### Functions
A *function* is a collection of statements which get executed sequentially.
- **Rule**: Every C++ program must contain a function called `main()`.
- When the program is run, `main()` is executed.
- Programs typically terminate once the last statement inside `main()` has been executed.
- Program may terminate beforehand in some circumstances, or do cleanup afterwards.
- Functions are typically written to perform some useful action, usually based on their name.
- **Term**: The name of a function, object, type, or template etc... is called its *identifier*.
### Characters and text
A *character* is a written symbol or mark, such as a letter, digit, punctuation mark and so on.
A sequence of characters is called *text*.
- Conventionally the term text is also used to mean *plain text*, which is text that contains only characters that appear on the standard keyboard, with no special format or styling.
- C++ programs are written as plain text.
- **Term**: *Control characters* are characters which have special meaning to the computer system, for example backspace or tab.
### Syntax
*Syntax* in a programming language (and similarly in general) is the set of rules used to describe how your program should be written for it to be considered valid by your compiler.
- The compiler is responsible to make sure you're following these syntactical rules. 
- If the compiler catches a syntax error, it will halt compilation and issue a *_syntax error*
- Syntax in programming is strictly defined and upheld.
### Libraries
A *library* file is a collection of **precompiled code** that has been “packaged up” for reuse in other programs. 
- The Standard Library is a library that ships with C++. 
- It contains additional functionality to use in your programs.
- Precompiled code has been processed and compiled into an intermediate format ahead of time to make sure builds execute faster.

`end 20/09/2026`
