# Objdump
`objdump` lets you **look inside a compiled program** and **see what the compiler and linker actually produced**.

## What problem does it solve?
When you run:
```bash
gcc main.c -o prog
```
You get a file called prog. But:
- You **can’t read it** (it’s binary)
- You don’t know:
  - what code is inside
  - what functions are really there
  - how big each part is
  - how memory is laid out

`objdump` answers those questions. It translates binary information into **human-readable text**.

## What can `objdump` show you?

### Disassembly: the actual CPU instructions
```bash
objdump -d prog
or
objdump -d add.o
```
Instead of:
```c
main() { return 0; }
```
You see:
```bash
0000000000401136 <main>:
 401136: 55                push   %rbp
 401137: 48 89 e5          mov    %rsp,%rbp
```
This is how you check:
- inlining
- optimization effects
- whether code was removed

### Source + assembly
```bash
objdump -S prog
```
- Interleaves **C source code** with assembly
- Requires `-g` during compilation

Extremely useful for:
- learning assembly
- understanding compiler optimizations

### Section headers: how the program is divided internally
```bash
objdump -h prog
```
This answers: **“Which parts are code, which are data, and how big are they?”**
```markdown
Idx Name      Size      VMA       LMA       File off  Algn
 0 .text     00001234  00000000  00000000  00001000  2**4
```
You’ll see things like:
- `.text` → code
- `.data` → initialized variables
- `.bss` → uninitialized variables

This is **how memory is organized**.

### Symbol table: what names (functions / variables) exist
```bash
objdump -t prog
```
This answers: **“What functions and global variables ended up in the final program?”**
- function names
- global variables
- section placement


