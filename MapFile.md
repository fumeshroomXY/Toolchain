In embedded C, the map file is generated during the **linking stage**, and it is one of the most important debugging tools for memory issues.

# What is a Map File?
A **map file (.map)** is a text report created by the **linker** that shows:

- Memory layout
- Section placement (.text, .data, .bss…)
- Symbol addresses
- Function sizes
- Which object file each symbol comes from
- Which library modules were pulled in

It tells you exactly: Where everything ended up in memory.

# How to generate
Example:
```bash
arm-none-eabi-gcc main.o uart.o -T linker.ld -Wl,-Map=output.map -o output.elf
```
The option:
```
-Wl,-Map=output.map
```
tells the linker to generate a map file.

# Why is it VERY important in Embedded?
Because embedded systems have:
- Limited Flash (e.g., 256KB)
- Limited RAM (e.g., 64KB)

You must know:
- How much Flash is used?
- How much RAM is used?
- Which function is taking too much space?
- Why did memory overflow?

The map file answers all of these.
