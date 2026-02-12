[A Linker Script Example](https://github.com/fumeshroomXY/Toolchain/blob/main/linker.ld)

This is a GNU LD linker script for an embedded ARM Cortex-M microcontroller.

It defines **memory layout, section placement, startup symbols, stack/heap, and checksum**.

I’ll explain what the linker script achieves clearly in parts.

# Memory Block – Define Physical Memory
```markdown
MEMORY
{
  MFlash256 (rx) : ORIGIN = 0x08000000, LENGTH = 0x40000
  RamLoc32  (rwx): ORIGIN = 0x20000000, LENGTH = 0x4000
}
```
This defines the MCU memory map:
| Region | Start Address | Size   | Purpose           |
| ------ | ------------- | ------ | ----------------- |
| Flash  | 0x08000000    | 256 KB | Program storage   |
| RAM    | 0x20000000    | 16 KB  | Data, stack, heap |

- `rx` → read + execute (Flash)
- `rwx` → read + write + execute (RAM)

# Symbol Definitions (Memory Boundaries)
```markdown
__top_RamLoc32 = 0x20000000 + 0x4000 ;
```
These define:
- Base address of Flash/RAM
- Top address of Flash/RAM

Used by:
- startup code
- stack initialization
- heap setup


For example:
```markdown
_vStackTop = __top_RamLoc32
```
So stack grows downward from top of RAM.

# ENTRY(main)
```markdown
ENTRY(main)
```
This tells the linker: The program entry symbol is `main`.

Normally Cortex-M entry is `Reset_Handler`, but here checksum logic references `main`, so this may be vendor-specific.

# SECTIONS – Where Code and Data Go (Important!)
## .text (Flash)
```markdown
.text : { ... } > MFlash256
```
Placed in Flash.

Contains:
### 1. Vector Table
```markdown
KEEP(*(.isr_vector))
```
This is the interrupt vector table. It is placed at the very beginning of Flash:
```markdown
0x08000000
```
### 2. Section Table (for startup copy)
This builds a table:
```markdown
LONG(LOADADDR(.data));
LONG(ADDR(.data));
LONG(SIZEOF(.data));
```
This tells startup code:
| What            | Meaning                       |
| --------------- | ----------------------------- |
| LOADADDR(.data) | where data is stored in Flash |
| ADDR(.data)     | where data should live in RAM |
| SIZEOF(.data)   | how many bytes to copy        |


Startup uses this to:
- Copy `.data` from Flash → RAM
- Zero `.bss`

### 3. Code & Read-Only Data
Second .text block:
```markdown
*(.text*)
*(.rodata*)
```
All **functions, const variables, string literals** are stored in Flash.

## ARM Exception Tables
```markdown
.ARM.extab
.ARM.exidx
```
Used for:
- exception unwinding
- C++ / Newlib support

Stored in Flash.

## .data (RAM, but loaded from Flash)
```markdown
.data : { ... } > RamLoc32 AT>MFlash256
```
- `>` → runtime location
- `AT>` → load location

Meaning:
- Initial values stored in Flash
- At reset → copied into RAM

Contains:
- global initialized variables

## .bss (RAM)
```markdown
.bss : { ... } > RamLoc32 AT> RamLoc32
```
- Stored in RAM, Not stored in Flash
- Zero-initialized at startup

Contains:
- uninitialized globals
- static variables

## Heap & Stack Symbols
```markdown
PROVIDE(_pvHeapStart = ...);
PROVIDE(_vStackTop = __top_RamLoc32);
```
Defines:
| Symbol       | Meaning          |
| ------------ | ---------------- |
| _vStackTop   | Initial SP value |
| _pvHeapStart | Start of heap    |


Stack grows downward. Heap grows upward.

# Checksum Calculation
```markdown
__valid_user_code_checksum = 0 -
    (_vStackTop
    + (main + 1)
    + (NMI_Handler + 1)
    ...
    );
```
This is vendor-specific (common in NXP LPC MCUs).

Purpose:
- MCU checks vector table checksum at boot.
- If wrong → bootloader mode.

# Image Size Calculation
```markdown
_image_start = LOADADDR(.text);
_image_end = LOADADDR(.data) + SIZEOF(.data);
_image_size = _image_end - _image_start;
```
This gives:
- Total firmware image size in Flash
