The `nm` tool is used to **list symbols** from object files and executables.

`nm` reads:

- `.o` (object files)
- `.a` (static libraries)
- `.elf` (final executable)

and prints the **symbol table**.

# Why is `nm` useful in embedded systems?
## 1. Debug "undefined reference" errors
If linking fails:
```bash
undefined reference to `SystemInit`
```
You can check:
```bash
arm-none-eabi-nm startup.o
```
If `SystemInit` is missing or marked as undefined (`U`), that explains the error.

## 2. Check where a variable is placed
Example:
```c
int global_var = 10;
static int local_var;
```
After compiling:
```bash
arm-none-eabi-nm main.o
```
You might see:
```markdown
00000000 T main
00000004 D global_var
00000000 b local_var
```
**Understanding the symbol letters**:
| Letter | Meaning             | Embedded Meaning                 |
| ------ | ------------------- | -------------------------------- |
| T      | Text (code section) | Function in Flash                |
| D      | Initialized data    | Global variable in RAM           |
| B      | BSS section         | Zero-initialized variable in RAM |
| U      | Undefined           | Declared but not defined         |
| W      | Weak symbol         | Can be overridden                |
| R      | Read-only data      | const data in Flash              |

Uppercase → global
Lowercase → local (static)

# Useful flags
```bash
nm file.elf
-n     # sort by address
-S     # show symbol size
-g     # show only external symbols
```
