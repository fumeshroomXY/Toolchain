# Objcopy
`objcopy` takes a compiled file and **transforms it into another binary form**.


The program logic **never** changes. **Only the container changes**.  
Same code. Same instructions. Different packaging.

## What problem does `objcopy` solve?
After compiling, you often have a file like:
```bash
program.elf
```
But:
- a microcontroller **doesn’t want an ELF**
- a **bootloader** wants raw bytes
- you only want part of the file
- you want to **remove debug info**

`objcopy` reshapes the file into what you actually need.

## The most common real use
ELF → raw binary
```bash
objcopy -O binary program.elf program.bin
```
From `program.elf`, you lose:
- symbol names
- section names
- debug info
- metadata

What remains:
- **pure bytes**

## Other very common uses
### Remove debug information
```bash
objcopy --strip-debug program.elf program_stripped.elf
```
### Remove everything not needed for execution
```bash
objcopy --strip-all program.elf
```

### Keep only specific sections
```bash
objcopy -O binary \
  --only-section=.text \
  --only-section=.data \
  program.elf firmware.bin
```

### Convert to Intel HEX
```bash
objcopy -O ihex program.elf program.hex
```
Some flash tools **prefer HEX** over BIN.
