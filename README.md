# Compiler & Computer Architecture Laboratory

A from-scratch compiler and systems laboratory for studying how high-level source code is transformed into executable machine instructions.

The project begins with a small C++-like programming language and progressively builds a complete translation pipeline:

```text
Source Code
    ↓
Lexer
    ↓
Tokens
    ↓
Parser
    ↓
Abstract Syntax Tree (AST)
    ↓
Semantic Analysis
    ↓
Intermediate Representation (IR)
    ↓
Optimization
    ↓
x86-64 Code Generation
    ↓
Assembly
    ↓
Assembler / Linker
    ↓
Executable
    ↓
CPU
```

The goal is not to recreate GCC or Clang. The goal is to understand and implement the major stages of compilation while being able to trace a small program from source code all the way to machine-level execution.

---

## Project Goals

This project is designed to answer:

> **How does a programming language become machine instructions, and how do compiler decisions affect the final program?**

The laboratory will investigate:

- Lexical analysis
- Parsing
- Abstract syntax trees
- Type and semantic analysis
- Symbol tables
- Intermediate representations
- Compiler optimization
- x86-64 assembly generation
- Assembly and linking
- Machine instructions
- CPU execution
- Memory and registers
- Debugging
- Performance measurement
- Compiler correctness

A separate educational CPU emulator will also be developed to make instruction execution visible and traceable.

---

## Example

The custom language may eventually support code such as:

```text
let x = 10;
let y = 20;
let result = x + y;
print(result);
```

The compiler should allow the translation to be inspected at each stage.

### Source

```text
let result = x + y;
```

### Tokens

```text
KEYWORD_LET
IDENTIFIER(result)
ASSIGN
IDENTIFIER(x)
PLUS
IDENTIFIER(y)
SEMICOLON
```

### AST

```text
VariableDeclaration
├── Name: result
└── Value
    └── Addition
        ├── Identifier: x
        └── Identifier: y
```

### Intermediate Representation

Conceptually:

```text
%t0 = load x
%t1 = load y
%t2 = add %t0, %t1
store %t2, result
```

### x86-64 Assembly

The final generated assembly will depend on the compiler's chosen calling convention, memory layout, and register allocation.

Conceptually:

```asm
mov eax, [x]
add eax, [y]
mov [result], eax
```

### Final Execution

The assembler converts the assembly into machine instructions, the linker produces an executable, and the CPU executes those instructions.

The project should make it possible to investigate every transformation rather than treating compilation as a black box.

---

# Language Scope

The project will initially implement a deliberately small language rather than attempting to support full C++.

The language may include:

### Variables

```text
let x = 10;
let y = 20;
```

### Arithmetic

```text
let result = x + y;
let difference = x - y;
let product = x * y;
```

### Comparisons

```text
x > y
x == y
x != y
```

### Boolean Values

```text
let enabled = true;
```

### Conditional Statements

```text
if (x > 10) {
    print(x);
}
```

### Loops

```text
while (x < 10) {
    x = x + 1;
}
```

### Functions

```text
function add(a, b) {
    return a + b;
}
```

### Basic Arrays / Memory

Eventually:

```text
let values = [10, 20, 30];
```

### Comments

```text
// calculate the result
```

The exact language specification will evolve as the compiler is implemented.

---

# Architecture

```text
                    ┌─────────────────┐
                    │   Source Code   │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │     Lexer       │
                    └────────┬────────┘
                             ↓
                         Tokens
                             ↓
                    ┌─────────────────┐
                    │     Parser      │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │      AST        │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │    Semantic     │
                    │    Analysis     │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │      IR         │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │   Optimizer     │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │ x86-64 Codegen  │
                    └────────┬────────┘
                             ↓
                        Assembly
                             ↓
                    ┌─────────────────┐
                    │ Assembler/Linker│
                    └────────┬────────┘
                             ↓
                        Executable
                             ↓
                           CPU
```

---

# Compiler Components

## 1. Lexer

Converts characters into tokens.

Input:

```text
let x = 10;
```

Output:

```text
LET
IDENTIFIER(x)
ASSIGN
INTEGER(10)
SEMICOLON
```

Responsibilities:

- Recognize keywords
- Recognize identifiers
- Recognize numbers
- Recognize operators
- Recognize punctuation
- Track line/column positions
- Report invalid characters

---

## 2. Parser

Consumes tokens and determines their structure.

For:

```text
x + y * 2
```

the parser must understand operator precedence:

```text
      +
     / \
    x   *
       / \
      y   2
```

The parser produces the AST.

---

## 3. Abstract Syntax Tree

The AST represents the logical structure of the program.

Example:

```text
      =
     / \
    x   +
       / \
      y   2
```

The AST becomes the main representation used by later compiler stages.

---

## 4. Semantic Analysis

Checks whether the program makes sense.

Examples:

```text
let x = 10;
let x = true;
```

Depending on the language rules, this may produce an error.

Other checks include:

- Undefined variables
- Duplicate declarations
- Type mismatches
- Invalid function calls
- Incorrect argument counts
- Invalid return statements
- Scope violations

---

## 5. Intermediate Representation

The compiler converts the AST into a lower-level representation.

Example:

```text
let result = x + y;
```

could become:

```text
%t0 = load x
%t1 = load y
%t2 = add %t0, %t1
store %t2, result
```

The IR creates a layer between the source language and machine architecture.

---

## 6. Optimization

The optimizer transforms the IR while preserving program behavior.

Initial optimization passes may include:

### Constant Folding

```text
10 + 20
```

becomes:

```text
30
```

### Constant Propagation

```text
x = 10;
y = x + 5;
```

can potentially become:

```text
y = 15;
```

### Dead Code Elimination

```text
x = 10;
x = 20;
print(x);
```

The first assignment may be unnecessary.

### Algebraic Simplification

```text
x + 0
```

can become:

```text
x
```

### Unreachable Code Removal

Code that can never execute can potentially be removed.

Each optimization should be measurable.

---

# 7. x86-64 Code Generation

The compiler converts IR into x86-64 assembly.

For example:

```text
%t0 = load x
%t1 = load y
%t2 = add %t0, %t1
```

could conceptually become:

```asm
mov eax, [x]
add eax, [y]
```

The code generator must eventually deal with:

- Registers
- Stack memory
- Function calls
- Calling conventions
- Local variables
- Control flow
- Comparisons
- Branches
- Return values

---

# 8. Assembly, Linking, and Execution

The compiler's generated assembly is passed to the assembler.

```text
Assembly
   ↓
Assembler
   ↓
Object File
   ↓
Linker
   ↓
Executable
```

The final executable can then be inspected and executed.

---

# CPU Emulator

A separate educational CPU emulator will be developed alongside the compiler.

The emulator will not attempt to reproduce the entire x86-64 architecture.

Instead, it will provide a controlled CPU model containing concepts such as:

```text
Registers
Memory
Program Counter
Stack Pointer
Instruction Pointer
Arithmetic
Loads
Stores
Comparisons
Jumps
Calls
Returns
Halt
```

Example instruction set:

```text
MOV
LOAD
STORE
ADD
SUB
MUL
CMP
JMP
JE
JNE
CALL
RET
HALT
```

The emulator will provide instruction-by-instruction tracing:

```text
PC=004
Instruction: ADD R1, R2

R1: 10
R2: 20

After:
R1: 30
```

This creates a second way to study what the CPU is doing.

---

# Performance Laboratory

The project will include experiments comparing different compiler implementations.

Measurements may include:

- Execution time
- Instruction count
- Generated assembly size
- Binary size
- Memory usage
- Compilation time
- Optimization impact

Example experiment:

```text
Optimization: Constant Folding

                  Before      After
IR instructions       5          2
Generated instructions  6          3
Runtime              X ms       Y ms
```

Actual numbers will only be documented after measurement.

---

# Technology Stack

### Primary

- C++
- x86-64 Assembly
- Linux
- CMake
- Git
- GitHub

### Supporting

- Python for analysis and benchmarking
- GDB for debugging
- Shell/Bash for tooling and automation

---

# Repository Structure

```text
compiler-computer-architecture-lab/
├── README.md
├── docs/
│   ├── language-spec.md
│   ├── architecture.md
│   ├── compiler-pipeline.md
│   ├── optimization.md
│   ├── assembly.md
│   └── cpu-emulator.md
├── compiler/
│   ├── lexer/
│   ├── parser/
│   ├── ast/
│   ├── semantic/
│   ├── ir/
│   ├── optimizer/
│   ├── codegen/
│   └── driver/
├── emulator/
├── examples/
├── tests/
├── benchmarks/
├── scripts/
└── CMakeLists.txt
```

---

# Branch Strategy

```text
main
develop

feature/lexer
feature/parser-ast
feature/semantic-analysis
feature/ir
feature/optimization
feature/x86-codegen
feature/cpu-emulator
feature/benchmarking

experiment/optimization-*
experiment/assembly-*
experiment/architecture-*
```

### Branch Rules

`main`
- Stable portfolio-ready code
- Releases and demonstrations

`develop`
- Integration branch
- Combines completed features before release

`feature/*`
- Focused implementation work

`experiment/*`
- Performance tests
- Alternative implementations
- Architecture experiments
- Compiler research

---

# Development Roadmap

## Phase 1 — Language Specification

- Define syntax
- Define types
- Define variables
- Define expressions
- Define control flow
- Define functions
- Define errors

## Phase 2 — Lexer

- Token definitions
- Character scanning
- Keyword recognition
- Identifier recognition
- Numeric literals
- Operators
- Error reporting
- Lexer tests

## Phase 3 — Parser / AST

- Expression parsing
- Operator precedence
- Statements
- Blocks
- Functions
- AST construction
- Parser tests

## Phase 4 — Semantic Analysis

- Symbol table
- Scope handling
- Type checking
- Function validation
- Semantic errors

## Phase 5 — IR

- IR instruction set
- Temporary values
- Variables
- Control flow
- IR printer
- IR tests

## Phase 6 — Optimization

- Constant folding
- Constant propagation
- Dead code elimination
- Algebraic simplification
- Common subexpression elimination
- Optimization measurements

## Phase 7 — x86-64 Code Generation

- Register usage
- Stack layout
- Arithmetic
- Comparisons
- Branches
- Functions
- Returns
- Assembly generation

## Phase 8 — Executables

- Assembly output
- Assembler integration
- Linker integration
- Executable generation
- End-to-end tests

## Phase 9 — CPU Emulator

- Registers
- Memory
- Instructions
- Program counter
- Execution loop
- Instruction tracing
- Debugging interface

## Phase 10 — Performance Laboratory

- Benchmark suite
- Optimization comparisons
- Instruction analysis
- Runtime measurements
- Memory measurements
- Technical report

---

# Testing Strategy

Testing will exist at multiple levels.

### Unit Tests

Individual compiler components:

```text
Lexer
Parser
AST
Semantic Analyzer
IR
Optimizer
Code Generator
```

### Integration Tests

Verify multiple stages together:

```text
Source → Lexer → Parser → AST → IR
```

### End-to-End Tests

Verify:

```text
Source
 ↓
Compiler
 ↓
Assembly
 ↓
Executable
 ↓
Expected Output
```

### Regression Tests

Every compiler bug that is fixed should receive a regression test.

---

# Documentation Strategy

Every major milestone should document:

```text
What was built?
Why was it needed?
How does it work?
What code was written?
What input was tested?
What output was produced?
What problems occurred?
How were they fixed?
What design decision was made?
What was learned?
What evidence was produced?
```

The most important demonstrations will trace the same program through multiple representations.

---

# Portfolio Evidence

Potential evidence includes:

- Custom language specification
- Compiler architecture
- Lexer implementation
- Parser implementation
- AST visualization
- Semantic analysis
- IR design
- Optimization passes
- Generated x86-64 assembly
- CPU emulator
- Instruction tracing
- Benchmark results
- Debugging discoveries
- Compiler correctness tests
- Performance experiments

Only measured results will be used as quantitative resume evidence.

---

# Definition of Done

The project is considered portfolio-ready when it has:

- [ ] Defined language specification
- [ ] Working lexer
- [ ] Working parser
- [ ] AST representation
- [ ] Semantic analysis
- [ ] Symbol table
- [ ] Intermediate representation
- [ ] Multiple optimization passes
- [ ] x86-64 code generation
- [ ] Executable generation
- [ ] Educational CPU emulator
- [ ] Automated tests
- [ ] Benchmark suite
- [ ] Architecture documentation
- [ ] Reproducible build instructions
- [ ] Demonstration programs
- [ ] Known limitations documented
- [ ] Performance experiments documented
- [ ] Interview-ready explanation of the complete pipeline

---

# Current Status

**Status:** Planning

The implementation will begin with the language specification and lexer before expanding into parsing, semantic analysis, IR, optimization, code generation, and the CPU laboratory.

---

# Long-Term Goal

The final project should allow a developer to take a small program and answer:

> **What happens to this code between the moment I write it and the moment the CPU executes it?**

The compiler, emulator, experiments, and documentation together provide the answer.
