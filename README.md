# Assembly ↔ Programming Languages

A hands-on computer architecture and Assembly project focused on understanding how high-level programming concepts relate to low-level x86-64 Assembly.

The project uses **C++, Python, and Go** as comparison languages and provides practical examples showing how common programming concepts can be represented, understood, and traced at the Assembly level.

The primary purpose of this project is not to build a compiler. It is to **develop and demonstrate the ability to read, understand, and explain Assembly code** by connecting low-level instructions to concepts that are familiar in higher-level programming languages.

---

## Project Goals

The main goal is to build a personal reference and demonstration of Assembly knowledge through practical examples.

The project focuses on understanding:

- x86-64 Assembly syntax
- Registers
- Memory
- Stack and stack frames
- Instructions
- Arithmetic operations
- Comparisons
- Conditional branching
- Loops
- Functions
- Function arguments
- Return values
- Arrays
- Pointers
- Structs
- Strings
- Bitwise operations
- Type representation
- Function calls
- Calling conventions
- Compiler-generated Assembly
- Machine-level program flow

Each concept will be demonstrated using Assembly alongside equivalent examples in:

- **C++**
- **Python**
- **Go**

---

# Project Philosophy

The project follows a simple idea:

> **Learn what Assembly is doing by connecting it to code that I already understand.**

For example, instead of only studying:

```asm
mov eax, 10
add eax, 5
```

the project connects it to:

### C++

```cpp
int x = 10;
x = x + 5;
```

### Python

```python
x = 10
x = x + 5
```

### Go

```go
x := 10
x = x + 5
```

Then the Assembly can be examined to understand how the operation is represented at a lower level.

The goal is not to claim that each language
