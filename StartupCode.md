In embedded C, **startup code execution** refers to what happens **before `main()` runs**.

Unlike desktop programs, there is **no OS** to prepare the environment.  
The MCU boots directly into your firmware, so you must set everything up manually (usually via startup assembly/C code).

# What happens when MCU powers on?
When power is applied or reset occurs:
## CPU reads the vector table
At a fixed memory address (usually Flash start, e.g., `0x00000000` or `0x08000000`), the CPU expects:
```markdown
Address 0x00000000 → Initial Stack Pointer (SP)
Address 0x00000004 → Reset Handler address
```
So the CPU:
- Loads SP with the first word
- Jumps to the Reset_Handler function

This Reset_Handler is the entry point of your startup code.

# What does Reset_Handler do?
The Reset_Handler performs **runtime initialization** before `main()`:

**Typical responsibilities:**
## 1. Initialize stack pointer
Usually already done automatically from vector table.

## 2. Copy `.data` section (Flash → RAM)
Startup code copies:
```markdown
from: Flash (.data load address)
to:   RAM (.data execution address)
```

## 3. Zero initialize `.bss`
Startup code clears:
```markdown
.bss section → memset to 0
```
## 4. Initialize C library (optional)
It may call:
```markdown
__libc_init_array();
```
This runs:
- C++ constructors
- static initializers

## 5. SystemInit (clock config)
main() might use timers, configure UART, use delay, use RTOS.

All of those depend on **correct clock frequency**. So before anything serious runs, we configure the clock tree.


## 6. Call main()
```markdown
main();
```
Embedded systems usually never exit. Often:
```c
main();

while (1) {
}
```
or trigger system reset.


# Q&A
## 1. Why embedded systems usually never exit?
### There Is No Operating System to Return To
In an embedded system:

There is usually **no OS**.

So when `main()` returns…

👉 Return to where?

There is no caller above `main()` except the startup code.
And the startup code has nowhere meaningful to go.

### Embedded Systems Are Designed to Run Forever
Embedded devices are usually: Washing machines, Routers, Medical devices, Cars, IoT sensors, Industrial controllers

They are expected to: Power on → run continuously → never stop

Their job is to continuously:
- Monitor inputs
- Control outputs
- Handle interrupts
- Respond to events

So exiting makes no sense in normal operation.
