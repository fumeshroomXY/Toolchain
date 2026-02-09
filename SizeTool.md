# Size Tool
`size` tells you **how much memory** your compiled program uses.

After you build a program, you may ask:
- “How big is my code?”
- “How much RAM will this use?”
- **“Will this fit in flash?”**

`size` answers those questions immediately.

## When do you actually use `size`?
```bash
size firmware.elf
```
To check:
- flash usage (`text + data`)
- RAM usage (`data + bss`)

Example output:
```markdown
   text    data     bss     dec     hex filename
   1024     128     256    1408     580 program
```
`text`
- Executable code
- Lives in **flash / ROM**

`data`
- Initialized global / static variables
- Stored in **flash**, **copied to RAM at startup**

`bss`
- Uninitialized global / static variables
- **Uses RAM only**
- Does **not** occupy space in the file

This is why:
- your `.elf` might be small
- but your program still runs out of RAM


`dec`
- Decimal total:
```bash
text + data + bss
```

`hex`
- Same total, but in hexadecimal
