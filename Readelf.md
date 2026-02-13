`readelf` is a tool used to inspect the internal structure of ELF files.


It works on:
- `.o` (object files)
- `.elf` (final executable)

`readelf` shows:
- ELF header
- Sections
- Segments
- Symbol tables
- Relocation entries
- Program headers

# Most Important readelf Options (Embedded Use)
## View ELF Header
```bash
arm-none-eabi-readelf -h program.elf
```
Shows:
- Architecture (ARM)
- Entry point address
- 32-bit or 64-bit
- Endianness
- File type (EXEC, REL, etc.)

Example output:
```markdown
Class: ELF32
Machine: ARM
Entry point address: 0x08000129
...
```
In Cortex-M, entry point should match `Reset_Handler`.

## View Sections
```bash
arm-none-eabi-readelf -S program.elf
```
Shows:
- `.text` → code (Flash)
- `.data` → initialized data (copied to RAM)
- `.bss` → zero-initialized data (RAM)
- `.rodata` → const data
- `.isr_vector` → interrupt table

Example:
```markdown
[Nr] Name       Type      Addr       Off    Size
[ 1] .text      PROGBITS  08000000   ...
[ 2] .data      PROGBITS  20000000   ...
[ 3] .bss       NOBITS    20000100   ...
```

This tells you:
- Flash starts at `0x08000000`
- RAM starts at `0x20000000`

## View Program Headers (Memory Segments)
```bash
arm-none-eabi-readelf -l program.elf
```
Shows how sections are grouped into loadable segments.

## View Symbol Table
```bash
arm-none-eabi-readelf -s program.elf
```
Similar to `nm`, but more detailed.

Shows:

- Symbol value (address)
- Size
- Type (FUNC, OBJECT)
- Binding (LOCAL, GLOBAL, WEAK)
- Section index

Example:
```markdown
Num: Value     Size Type    Bind   Ndx Name
  12: 08000129   52 FUNC    GLOBAL  1   main
```

## View Relocation Entries
```bash
arm-none-eabi-readelf -r main.o
```
Very important during linking stage.

Relocation entries show: “This address must be fixed by the linker.”

## 
