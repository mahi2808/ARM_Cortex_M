01. ARM vs Cortex-M
What is ARM?
# ARM is a company that designs CPU architectures and processor cores.
Examples:
ARM7
ARM9
Cortex-M4
Cortex-A53

ARM provides the design, and companies like:
STM
NXP
TI
Microchip
use that design in their chips.

# Why ARM became popular?
Because ARM processors are:
Low power
Low cost
High performance
Simple instruction set (RISC)

# What is a Processor Core?
Think of a microcontroller as:
MCU
├── CPU Core
├── Flash
├── SRAM
├── GPIO
├── UART
├── SPI
├── CAN
└── Timers
The CPU Core executes instructions.
ARM designs this CPU core.

# What is Cortex-M?
Cortex-M is ARM's microcontroller CPU family.
Examples:
Cortex-M0
Cortex-M3
Cortex-M4
Cortex-M7
Real Example
STM32F103 → Cortex-M3
STM32F407 → Cortex-M4
STM32H743 → Cortex-M7

# Cortex Families
Cortex-A → Linux, Android
Cortex-R → Real-Time Systems
Cortex-M → Microcontrollers

# Why Cortex-M for Embedded?
Fast interrupt handling
Low power
Small memory footprint
Deterministic behavior
Suitable for RTOS

# Key Point
ARM = Architecture Designer
Cortex-M = ARM CPU Core for Microcontrollers
STM32 = Microcontroller containing Cortex-M core

# RISC Architecture
What is RISC?
RISC = Reduced Instruction Set Computer
Idea:
Small number of simple instructions
Most instructions execute in 1 clock cycle
Simpler hardware
Faster execution
Lower power consumption
Example
Instead of one complex instruction: A = B + C + D
RISC may execute: 
LOAD B
LOAD C
ADD
LOAD D
ADD
STORE A
Many simple instructions instead of one complex instruction.

# Advantages
Faster execution
Easier CPU design
Lower power consumption
Lower silicon area
Ideal for embedded systems
ARM is a RISC Processor

That's one major reason ARM dominates:
Automotive
IoT
Industrial
Consumer electronics

# Harvard Architecture
What is Harvard Architecture?
Harvard Architecture uses separate memory paths for:
Instruction Memory (Program)
Data Memory

This means: CPU can fetch instruction and CPU can access data at the same time.

# Block Diagram
          CPU
         /   \
        /     \
 Program      Data
 Memory       Memory

Example
Suppose CPU executes: A = B + C;
At the same time: Instruction fetched from Flash B and C read from SRAM

Because instruction and data paths are separate, both operations can happen simultaneously.

# Advantages
- Faster execution
- Better performance
- Reduced bottleneck
- Efficient memory access

# Von Neumann vs Harvard
| Von Neumann                    | Harvard               |
| ------------------------------ | --------------------- |
| Single memory path             | Separate memory paths |
| Instruction and data share bus | Separate buses        |
| Slower                         | Faster                |
| Simpler design                 | Better performance    |

# Cortex-M
Cortex-M uses Modified Harvard Architecture

Meaning:
Separate instruction and data buses
Better performance
Optimized for embedded systems
# Real MCU Example
Flash  -> Program storage
SRAM   -> Variable storage
CPU can access both efficiently
# Key Takeaway
Harvard Architecture = Separate paths for instruction and data.
Result: Higher speed and better performance.

02. Memory Organization
# Why Memory Organization?
The CPU needs to know where:
- Program Code
- Variables
- Peripherals
- Interrupt Information
are stored.
To organize this, Cortex-M uses a Memory Map.

# What is Address Space?
Every memory location has an address.

Example:
0x00000000
0x00000001
0x00000002
...

The CPU accesses memory using these addresses.

# Cortex-M Address Space
Most Cortex-M devices have: 4 GB Address Space
Because: 
32-bit Address Bus
2^32 = 4,294,967,296 Bytes
≈ 4 GB

# Important:
Address Space ≠ Physical Memory
A microcontroller may have:
512 KB Flash
128 KB SRAM
but still supports a 4 GB address range.

Cortex-M Memory Map
0x00000000 ───────────────────
            Code Region
            (Flash)

0x20000000 ───────────────────
            SRAM Region

0x40000000 ───────────────────
            Peripheral Region

0xE0000000 ───────────────────
            System Region

Remember these 4 addresses.

Code Region
Starts at: 0x00000000
Contains:
- Program Code
- Vector Table
- Constants
Usually mapped to: Flash Memory

Example:
int main(void)
{
}
Stored in Flash.

SRAM Region
Starts at: 0x20000000
Contains:
- Global Variables
- Local Variables
- Stack
- Heap

Example:
int count = 10;
Stored in SRAM during execution.

Peripheral Region
Starts at: 0x40000000
Contains peripheral registers:

GPIO
UART
SPI
I2C
CAN
ADC
Timers

Example:
GPIOA->ODR
Actually accesses a memory address in this region.

System Region
Starts at: 0xE0000000
Contains:
NVIC
SysTick
Debug Units
System Control Block
Used for core/system functions.

# Key Takeaway
0x00000000 -> Flash / Code
0x20000000 -> SRAM
0x40000000 -> Peripherals
0xE0000000 -> System Registers

# Vector Table
What is Vector Table?

# Vector Table is a table that contains addresses of:
- Initial Stack Pointer (MSP)
- Reset Handler
- Exception Handlers
- Interrupt Service Routines (ISRs)

# Think of it as:
Directory of all exceptions and interrupts
Where is it Stored?
By default:
0x00000000

which is in the:
Code Region (Flash)

# First Two Entries
Address        Content
0x00000000 -> Initial MSP Value
0x00000004 -> Reset Handler Address

# These are the most important entries.
Simplified Vector Table
0x00000000 -> Initial MSP
0x00000004 -> Reset Handler
0x00000008 -> NMI Handler
0x0000000C -> Hard Fault Handler
0x00000010 -> MemManage Handler
0x00000014 -> Bus Fault Handler
...

# Interrupt Handlers
What Happens After Power ON?
Step 1
CPU reads: 0x00000000
Loads value into: MSP (Main Stack Pointer)

Step 2
CPU reads: 0x00000004
Gets address of: Reset_Handler()

Step 3
CPU jumps to: Reset_Handler()

Step 4
Eventually: 
main() is called.

# Why Vector Table?
Without it, CPU would not know:
- Where stack starts
- Which function to execute after reset
- Which ISR to execute during interrupts
Key Takeaway
Power ON
↓
Read MSP from 0x00000000
↓
Read Reset Handler from 0x00000004
↓
Execute Reset Handler
↓
main()

This complete sequence is one of the most important Cortex-M concepts.

03. CPU Registers
# Registers are small memory locations inside the CPU used for very fast data storage.
# Types of Cortex-M Registers
R0 - R12  → General Purpose Registers
R13       → SP (Stack Pointer)
R14       → LR (Link Register)
R15       → PC (Program Counter)
xPSR      → Program Status Register

# R0 - R12 (General Purpose Registers)
Used for:
- Data storage
- Arithmetic operations
- Function arguments
- Temporary values

Example: a = b + c;
CPU may store:
R0 = b
R1 = c
R2 = result

# SP (Stack Pointer) - R13
Points to the top of the stack.
Types:
MSP → Main Stack Pointer
PSP → Process Stack Pointer

Used for:
- Function calls
- Local variables
- Interrupt handling

# LR (Link Register) - R14
Stores the return address of a function.

Example:
func1();
Before jumping to func1():
LR = address of next instruction
After function finishes: 
BX LR
returns to caller.

# PC (Program Counter) - R15
Contains address of next instruction to execute.
Example:
PC = 0x08000100
CPU fetches instruction from: 0x08000100
then PC automatically moves to next instruction.

# xPSR
Stores CPU status information.

Contains:
- Zero Flag (Z)
- Carry Flag (C)
- Negative Flag (N)
- Overflow Flag (V)
- Exception Number

Example: 5 - 5 = 0
Then: Zero Flag = 1

# Most Important Registers
SP → Where stack is
LR → Where to return
PC → What to execute next
xPSR → CPU status

04. Stack (MSP & PSP)
# What is Stack?
Stack is a memory area in SRAM used to temporarily store:
- Function return addresses
- Local variables
- CPU registers
- Interrupt context

# Why Stack is Needed?
Example:
main()
{
    func1();
}

When func1() is called, CPU must remember:
Where to return after func1() finishes.
This information is stored on the stack.

# Stack Works on LIFO
Last In First Out

Example:
Push A
Push B
Push C

Pop -> C
Pop -> B
Pop -> A

# PUSH Operation
Stores data onto the stack.

Example:
MSP = 0x20001000
PUSH R0
After PUSH:
MSP = 0x20000FFC
Stack grows downward.

# POP Operation
Removes data from stack.

Example:
POP R0
After POP:
MSP = 0x20001000

# Stack Growth Direction
In Cortex-M:
Stack grows from High Address

↓

to

↓

Low Address

Example:
0x20001000  ← Stack Start
0x20000FFC
0x20000FF8
0x20000FF4

# MSP (Main Stack Pointer)

Used after:
Power ON
Reset
Interrupts
Exceptions
CPU automatically loads MSP during reset.

# PSP (Process Stack Pointer)
Usually used by:
RTOS Tasks
Each task can have its own PSP.

MSP vs PSP
| MSP                 | PSP                       |
| ------------------- | ------------------------- |
| Main Stack Pointer  | Process Stack Pointer     |
| Default after reset | Used by tasks             |
| Used by exceptions  | Used by applications/RTOS |

# Function Call Example
main()
{
    func1();
}

Flow:
1. Save return address
2. Jump to func1()
3. Execute func1()
4. BX LR
5. Return to main()
The stack helps preserve execution flow.

# Key Takeaway
Stack = Temporary storage area
PUSH = Store data
POP = Retrieve data
MSP = Main Stack
PSP = Task Stack
Stack grows downward

# MSP vs PSP (Deep Understanding)
MSP = Main Stack Pointer
PSP = Process Stack Pointer
Now let's understand why Cortex-M has two stack pointers.

# Why Two Stack Pointers?
Without PSP:
Application
Interrupts
RTOS Tasks
would all use the same stack.

This can cause:
- Stack corruption
- Difficult debugging
- Poor task isolation

So Cortex-M provides:
MSP
PSP

# MSP
Used for:
- Reset
- Exceptions
- Interrupts
- Handler Mode
After reset:
CPU automatically uses MSP

# PSP
Used for:
- Application code
- RTOS Tasks
Usually enabled by software.

# Example in RTOS
Suppose:
Task1
Task2
Task3

Each task gets:
Own PSP
Own Stack

Example:
Task1 PSP = 0x20001000
Task2 PSP = 0x20002000
Task3 PSP = 0x20003000

# Interrupt Occurs
While Task1 is running: Task1 uses PSP
Interrupt occurs: CPU automatically switches to MSP
Interrupt executes using MSP.
After interrupt: CPU returns to PSP and Task1 continues.

# Key Takeaway
MSP → Interrupts, Exceptions, Reset
PSP → Tasks, Applications

05. Reset Sequence
# What is Reset Sequence?
Reset Sequence is the series of steps executed by Cortex-M after:
- Power ON
- External Reset
- Watchdog Reset
- Software Reset

# What is Reset Sequence?
Reset Sequence is the series of steps executed by Cortex-M after:
- Power ON
- External Reset
- Watchdog Reset
- Software Reset

Step 1: Reset Occurs
Power ON / Reset
CPU enters reset state.

Step 2: Load MSP
CPU reads: 0x00000000 from Vector Table.
Loads: MSP = Initial Stack Pointer Value

Example:
MSP = 0x20020000

Step 3: Load PC
CPU reads: 0x00000004 from Vector Table.
Loads: PC = Reset_Handler Address

Example:
PC = 0x08000100

Step 4: Execute Reset_Handler()
CPU starts executing: Reset_Handler()

Step 5: Initialize .data Section
Variables with initial values:
int count = 10;
are copied: Flash → SRAM

Step 6: Initialize .bss Section
Variables without initialization:
int count;
are cleared: Set to 0

Step 7: System Initialization
Typically: SystemInit(); executes.
Configures:
- Clock
- PLL
- FPU
- Basic MCU settings

Step 8: Call main()
main();
Application starts.

Complete Flow
Reset
  ↓
Load MSP
  ↓
Load PC
  ↓
Execute Reset_Handler
  ↓
Initialize .data
  ↓
Initialize .bss
  ↓
SystemInit()
  ↓
main()

# Important Question
Where is Reset_Handler address stored?
At Vector Table address 0x00000004
Where is Initial MSP stored?
At Vector Table address 0x00000000

06. Interrupts
What is an Interrupt?
An interrupt is a signal that temporarily stops normal program execution and forces the CPU to execute a special function called an ISR.

# Why Interrupts?
# Without interrupts:

while(1)
{
    if(button_pressed)
    {
        // Handle button
    }
}
CPU continuously checks the button.
This is called: Polling

# With interrupts:

Button Press
      ↓
Interrupt Generated
      ↓
ISR Executed

CPU does not need to continuously check.

# Interrupt Flow
Main Program Running
        ↓
Interrupt Occurs
        ↓
CPU Saves Context
        ↓
ISR Executes
        ↓
CPU Restores Context
        ↓
Main Program Continues

# What is ISR?
ISR = Interrupt Service Routine

Example:
void EXTI0_IRQHandler(void)
{
    // Interrupt handling code
}
Executed automatically when interrupt occurs.

# Context Saving
Before entering ISR:
CPU automatically saves:
R0
R1
R2
R3
R12
LR
PC
xPSR
onto the stack.
This is called: Context Saving

# After ISR
CPU restores:
R0
R1
R2
R3
R12
LR
PC
xPSR
and continues normal execution.

# Interrupt Latency
Time between:
Interrupt Occurs
        ↓
First ISR Instruction Executes

called: Interrupt Latency
Lower latency is better.

# Nested Interrupts
Example:

Interrupt A Running
Then: Higher Priority Interrupt B Occurs
CPU can: Pause A
Execute B
Resume A
This is called: Nested Interrupt

# Key Takeaway
Interrupt = Event
ISR = Interrupt Handler
CPU Saves Context
ISR Executes
CPU Restores Context
Program Continues

07. NVIC (Nested Vectored Interrupt Controller)
What is NVIC?
NVIC is a hardware block inside Cortex-M that manages interrupts.

# Responsibilities:
- Enable Interrupts
- Disable Interrupts
- Set Priority
- Handle Nested Interrupts
- Manage Pending Interrupts

# Why NVIC?
Suppose:
UART Interrupt
CAN Interrupt
Timer Interrupt
occur at the same time.

Question:
Which ISR should execute first?
NVIC decides.

# Interrupt Priority
Example:
CAN    Priority = 1
UART   Priority = 2
Timer  Priority = 3

Lower number:
Higher Priority
So:
CAN executes first

# Interrupt States
Inactive: Interrupt not requested
Pending: Interrupt occurred Waiting for CPU service
Active: ISR currently executing
Example
Button Press
      ↓
Interrupt Generated
      ↓
Pending State
      ↓
CPU Accepts Interrupt
      ↓
Active State
      ↓
ISR Executes

# Nested Interrupt

Example:
UART ISR Running
Then: CAN Interrupt Occurs
Assume: CAN Priority > UART
Then: Pause UART ISR
Execute CAN ISR
Resume UART ISR
This is called: Nested Interrupt Vectoring

# NVIC knows:
Which ISR belongs to which interrupt
using the: Vector Table

# Important Points
NVIC is part of Cortex-M Core
Not part of GPIO/UART/CAN
Manages all interrupts

08. SysTick Timer
# What is SysTick?
SysTick is a 24-bit timer built into the Cortex-M core.
It is part of: System Control Block (Core)
Not a peripheral timer like TIM1, TIM2, etc.

# Why SysTick?
Used for:
- Delays
- Time Base Generation
- RTOS Tick
- Periodic Interrupts

# How SysTick Works?
Example:
Load Value = 1000
SysTick counts:
1000
999
998
...
1
0
When it reaches:

0
it generates: SysTick Interrupt
Then reloads and starts again.

# SysTick Registers
CTRL:
Enable SysTick 
Interrupt Enable
Clock Source

LOAD:
Reload Value

VAL: 
Current Counter Value

Example
Suppose:
CPU Clock = 1 MHz
Then:
1 Tick = 1 µs

If:
LOAD = 999
Interrupt occurs every:
1000 µs
= 1 ms

# SysTick in RTOS
Very Important:
FreeRTOS uses SysTick

Example:
Every 1 ms
SysTick interrupt occurs.
Scheduler executes.
Decides: Which task should run next

# Flow in RTOS
SysTick Interrupt
        ↓
Scheduler Runs
        ↓
Task Switch
        ↓
Next Task Runs

#Key Difference
SysTick = Cortex-M Core Timer
TIM1/TIM2 = MCU Peripheral Timers

09. Clock System
# Why Clock is Required?
A microcontroller cannot execute anything without a clock.
The clock provides timing for:
CPU
UART
SPI
I2C
CAN
Timers
ADC

# SysTick
What Happens Without Clock?
No Clock
   ↓
No Instruction Execution
   ↓
MCU Dead

# Clock Sources
Internal Oscillator (HSI)
Built inside MCU.

# Advantages:
No external component
Low cost
Fast startup

# Disadvantages:
Less accurate

# External Oscillator (HSE)
External crystal connected to MCU.

# Advantages:
High accuracy
Stable frequency

# Disadvantages:
Extra cost
Extra PCB space

# PLL (Phase Locked Loop)
Used to increase clock frequency.

Example:
HSE = 8 MHz
PLL multiplies: 8 MHz × 9
Result: 72 MHz

# System Clock (SYSCLK)
Main clock for CPU.
Example:
SYSCLK = 72 MHz
Means: CPU executes at 72 MHz

# Clock Tree
Typical flow:
HSI/HSE
    ↓
   PLL
    ↓
 SYSCLK
    ↓
AHB Bus
    ↓
APB Bus
    ↓
Peripherals

# AHB Clock
Used for:
CPU
DMA
SRAM
Flash
High-speed bus.

# APB Clock
Used for:
UART
SPI
I2C
CAN
Timers
Peripheral bus.

# Example
Suppose: Crystal = 8 MHz
PLL: ×9
Result: SYSCLK = 72 MHz

Then:
AHB = 72 MHz
APB1 = 36 MHz
APB2 = 72 MHz
(depends on prescalers)

# Why Clock Knowledge is Important?
Because these depend on clock:
UART Baud Rate
CAN Bit Timing
Timer Frequency
PWM Frequency
SysTick Period
ADC Speed

# Wrong clock:
Wrong UART Baud Rate
Wrong CAN Speed
Wrong Timer Output

# Key Takeaway
HSI = Internal Clock
HSE = External Clock
PLL = Frequency Multiplier
SYSCLK = CPU Clock
AHB = High-Speed Bus
APB = Peripheral Bus

10. Exceptions
# What is an Exception?
An exception is a special event that temporarily stops normal program execution and transfers control to an exception handler.

# In Cortex-M:
Interrupts are also exceptions.

# Types of Exceptions
Reset Occurs when:
Power ON
External Reset
Software Reset
Watchdog Reset

Handler: Reset_Handler()

# NMI (Non-Maskable Interrupt)
Highest priority exception.
Cannot be disabled.
Used for: Critical Hardware Faults
Example:
Clock Failure
Power Failure

# Hard Fault
Occurs when CPU detects a serious error.
Examples:
Invalid Memory Access
Execution Error
Escalated Fault

Handler: HardFault_Handler()

# MemManage Fault
Memory protection violation.
Example: Accessing forbidden memory

# Bus Fault
Occurs during memory transactions.
Examples: Invalid Bus Access
Peripheral Access Error

# Usage Fault
Occurs when CPU executes invalid operations.
Examples: Divide By Zero
Invalid Instruction
Invalid State Transition

# SVCall (Supervisor Call)
Generated by software.
Instruction: SVC
Used by:
RTOS
System Services

# PendSV
Special exception used mainly by RTOS.
Purpose: Context Switching
Example:
Task1
 ↓
Task2
RTOS often performs the switch inside: PendSV_Handler()

# SysTick
Used for:
RTOS Tick
Periodic Timing

# Exception Priority
Highest: Reset
Then: NMI
Then: Hard Fault
Then remaining configurable exceptions.

# Key Takeaway
Reset     → Startup
NMI       → Critical Event
HardFault → Serious CPU Fault
MemManage → Memory Error
BusFault  → Bus Error
UsageFault→ CPU Usage Error
SVCall    → Software Request
PendSV    → Context Switch
SysTick   → Periodic Tick

11. DMA (Direct Memory Access)
# What is DMA?
DMA is a hardware module that transfers data between memory and peripherals without CPU involvement.

# Why DMA?
Without DMA:
Peripheral
    ↓
CPU
    ↓
Memory
CPU must move every byte.

Example:
UART receives 1000 bytes
CPU copies all 1000 bytes
CPU becomes busy.

With DMA
Peripheral
    ↓
DMA
    ↓
Memory
CPU only starts the transfer.
DMA does the rest.

# Main Benefit
Less CPU Load
Higher Performance
Faster Data Transfer

# DMA Transfer Types
1. Peripheral → Memory
Example:
UART Receive
ADC Conversion

Flow:
UART
 ↓
DMA
 ↓
RAM

2. Memory → Peripheral
Example:
UART Transmission
SPI Transmission

Flow:
RAM
 ↓
DMA
 ↓
UART

3. Memory → Memory
Example:
Buffer Copy

Flow:
RAM
 ↓
DMA
 ↓
RAM

# DMA Working
Step 1
CPU configures DMA.
Source Address
Destination Address
Data Length

Step 2
CPU enables DMA.

Step 3
DMA starts transfer.

Step 4
CPU continues other work.

Step 5
DMA completes transfer.

Step 6
DMA generates interrupt.

# Example
Without DMA:
UART receives 1000 bytes
CPU handles 1000 transfers

With DMA:
CPU configures DMA once
DMA transfers 1000 bytes
CPU free for other tasks

# DMA Interrupt
After transfer:
Transfer Complete Interrupt
can notify CPU.

# Key Takeaway
DMA = Direct Memory Access
Transfers data without CPU
Reduces CPU load
Improves performance
Generates interrupt when complete

# 

12. Bootloader Basics
# What is a Bootloader?
A bootloader is a small program that runs before the main application.
Its job is to:
Load Application
Update Firmware
Verify Firmware
Jump to Application

# Why Bootloader?
Without bootloader:
Firmware update requires debugger
With bootloader:
Firmware can be updated via:
UART
CAN
USB
Ethernet

# Flash Layout
Example:
Flash Memory
┌──────────────┐
│ Bootloader   │
├──────────────┤
│ Application  │
└──────────────┘

# Power ON Sequence
Reset
  ↓
Bootloader Starts
  ↓
Check Update Request?
  ↓
No
  ↓
Jump to Application

OR

Yes
  ↓
Receive New Firmware

# Bootloader Tasks
Firmware Update
Receive:
UART
CAN
USB
data and store it in Flash.

# Firmware Verification
Check:
CRC
Checksum
to ensure firmware is valid.

# Jump to Application
After verification:
Bootloader transfers control to Application

# How Bootloader Jumps to Application?
Suppose:
Application starts at: 0x08010000

Bootloader:
Step 1
Read Application MSP: 0x08010000

Step 2
Load MSP.

Step 3
Read Reset_Handler address: 0x08010004

Step 4
Load PC with Application Reset_Handler.

Step 5
Application starts.

# Bootloader Flow
Read Application MSP
        ↓
Set MSP
        ↓
Read Application Reset_Handler
        ↓
Jump to Reset_Handler
        ↓
Application Starts

# Important Point
Application has its own:
Vector Table
MSP
Reset_Handler
just like after a normal reset.

# Types of Bootloaders
UART Bootloader
CAN Bootloader
USB Bootloader
Ethernet Bootloader

# Key Takeaway
Bootloader runs first
Can update firmware
Verifies firmware
Loads Application MSP
Jumps to Application Reset_Handler

13. Power Management
# Why Power Management?
Embedded systems often run on:
Battery
Vehicle Battery
Backup Power

# Reducing power consumption:
Increases battery life
Reduces heat
Improves efficiency

# Power Modes
- Run Mode
Normal operation.
CPU Running
Peripherals Running
Highest power consumption.

- Sleep Mode
CPU stops.
Most peripherals continue running.
CPU OFF
Peripherals ON
Wake-up is very fast.

- Deep Sleep Mode
More blocks are disabled.
CPU OFF
Many Clocks OFF
Lower power consumption.

- Standby Mode
Almost entire MCU powered down.
CPU OFF
RAM OFF (device dependent)
Most peripherals OFF
Lowest power consumption.

# Wake-Up Sources
MCU can wake up from:
External Interrupt
RTC Alarm
Wake-Up Pin
Communication Event

# Example:
Button Press
      ↓
Wake MCU

# Example
Battery-powered sensor:
Sleep for 10 seconds
      ↓
Wake Up
      ↓
Read Sensor
      ↓
Transmit Data
      ↓
Sleep Again
This saves significant power.

# Key Takeaway
Run Mode      → Highest Power
Sleep Mode    → CPU OFF
Deep Sleep    → More Blocks OFF
Standby Mode  → Lowest Power
Wake-Up Source → Brings MCU back to Run Mode

14. Debugging
# What is Debugging?
Debugging is the process of finding and fixing software issues.

# Why Debugging?
Example:
int a = 10;
int b = 0;
int c = a / b;
Program crashes.
Debugging helps identify:
Where the problem occurred
Why it occurred

# JTAG
JTAG = Joint Test Action Group
Used for:
Programming
Debugging
Boundary Scan
Requires multiple pins.

# SWD
SWD = Serial Wire Debug
ARM's alternative to JTAG.
Uses only:
SWDIO
SWCLK

- Advantages:
Fewer Pins
Faster Setup
Widely Used

# Breakpoint
A breakpoint temporarily stops CPU execution.
Example:
func1();   // Breakpoint here
func2();
When CPU reaches breakpoint:
Execution Stops
Developer can inspect:
Variables
Registers
Memory

 # Watchpoint
Monitors a variable or memory address.
Example:
count++;
When count changes:
Debugger Stops
Useful for tracking unexpected modifications.

# Step Execution
Step Into
Enter Function
Example:
func1();
Debugger enters func1().

# Step Over
Execute Function
Do Not Enter

# Step Out
Finish Current Function
Return To Caller

# Fault Analysis
Common fault:
Hard Fault
Typical reasons:
Invalid Pointer
Stack Overflow
Divide By Zero
Invalid Memory Access

# Debug Flow
Program Runs
      ↓
Breakpoint Hit
      ↓
Inspect Variables
      ↓
Find Issue
      ↓
Fix Code
      ↓
Run Again

# Key Takeaway
JTAG → Debug Interface
SWD → ARM Debug Interface
Breakpoint → Stop Execution
Watchpoint → Monitor Variable
HardFault → Common Crash Indicator

15. Startup File
# What is Startup File?
The startup file is the first code executed after reset.
Usually: startup_stm32xxxx.s or startup_xxx.s
It contains:
Vector Table
Reset_Handler
Default Exception Handlers
Default Interrupt Handlers

# Why Startup File?
After reset, CPU only knows:
0x00000000 → MSP
0x00000004 → Reset_Handler
The startup file provides these entries.

# Vector Table
Located at beginning of Flash.
Example:
0x00000000 → Initial MSP
0x00000004 → Reset_Handler
0x00000008 → NMI_Handler
0x0000000C → HardFault_Handler
This table is defined in the startup file.

# Reset_Handler
After reset:
CPU loads MSP
CPU loads PC
CPU jumps to Reset_Handler()

# Reset_Handler performs:
Initialize .data
Initialize .bss
Call SystemInit()
Call main()

# Exception Handlers
Startup file provides default handlers:
NMI_Handler()
HardFault_Handler()
MemManage_Handler()
BusFault_Handler()
UsageFault_Handler()

# Interrupt Handlers
Examples:
USART1_IRQHandler()
CAN_RX_IRQHandler()
TIM2_IRQHandler()
Usually declared as: Weak Functions

# What is Weak Function?
Example:
__weak void USART1_IRQHandler(void)
{
}
If you create your own:
void USART1_IRQHandler(void)
{
    // User Code
}
Your version replaces the weak version.

# Startup Flow
Reset
  ↓
Load MSP
  ↓
Load PC
  ↓
Reset_Handler
  ↓
Initialize .data
  ↓
Initialize .bss
  ↓
SystemInit()
  ↓
main()

# Key Takeaway
Startup File contains:
Vector Table
Reset_Handler
Exception Handlers
Interrupt Handlers
Weak Functions

16. Linker Script
# What is a Linker Script?
A linker script tells the linker:
Where code should be stored
Where variables should be stored
Memory layout of the MCU
Usually: STM32xxxx_FLASH.ld

# Why Linker Script?
The compiler creates object files.
The linker must know:
Flash Size
RAM Size
Code Location
Variable Location
This information comes from the linker script.

# Memory Definition
Example:
FLASH = 512 KB
RAM   = 128 KB

# Memory Regions
Flash
Stores:
Program Code
Constants
Vector Table

# RAM
Stores:
Variables
Stack
Heap

# Important Sections
- .text
Contains:
Program Code
Functions
Instructions
Stored in: Flash

- .data
Contains:
int count = 10;
Initialized global/static variables.
Stored: Flash (initial value) Copied to RAM at startup

- .bss
Contains:
int count;
Uninitialized global/static variables.
Stored in: RAM
Initialized to: 0 by Reset_Handler.

# Stack
Used for:
Function Calls
Local Variables
Interrupt Context

Usually grows:
High Address
↓
Low Address

# Heap
Used for:
malloc()
calloc()
realloc()
Dynamic memory allocation.

# Memory Layout
Example:
FLASH
├── Vector Table
├── .text
└── .data Initial Values

RAM
├── .data
├── .bss
├── Heap
└── Stack

# Startup Connection
During Reset_Handler:
Copy .data
Flash → RAM
and:
Clear .bss
Set all bytes to 0

# Why Important?
Because linker script decides:
Memory Addresses
Stack Size
Heap Size
Flash Usage
RAM Usage

# Key Takeaway
.text → Code
.data → Initialized Variables
.bss  → Uninitialized Variables
Stack → Function Calls
Heap  → Dynamic Memory
Linker Script → Memory Layout Manager

- FLASH
├── Vector Table
├── .text
└── Initial .data values

- RAM
├── .data
├── .bss
├── Heap
└── Stack

17. Cortex-M Important Concepts Revision
# This topic connects all ARM Cortex-M concepts together.
- Power ON to main()
Power ON / Reset
        ↓
Read MSP from 0x00000000
        ↓
Load MSP
        ↓
Read Reset_Handler from 0x00000004
        ↓
Load PC
        ↓
Execute Reset_Handler
        ↓
Initialize .data
        ↓
Initialize .bss
        ↓
SystemInit()
        ↓
main()

# Remember
MSP loaded first
PC loaded second

- Interrupt Flow
Main Running
      ↓
Interrupt Occurs
      ↓
CPU saves context
      ↓
Switch to MSP
      ↓
ISR Executes
      ↓
Restore Context
      ↓
Continue Main Program

# Saved automatically:
R0
R1
R2
R3
R12
LR
PC
xPSR

- MSP vs PSP
MSP
- Reset
- Exceptions
- Interrupts

PSP
- RTOS Tasks
- Applications

# Important
Interrupts always use MSP
even when tasks use PSP.

- .data vs .bss
.data
int count = 10;
Stored in Flash
Copied to RAM

.bss
int count;
Stored in RAM
Initialized to 0

# Startup File vs Linker Script
Startup File
Contains:
Vector Table
Reset_Handler
Exception Handlers
Interrupt Handlers

Linker Script
Contains:
Flash Layout
RAM Layout
.text
.data
.bss
Stack
Heap

# DMA Flow
CPU Configures DMA
        ↓
DMA Transfers Data
        ↓
CPU Free
        ↓
DMA Complete
        ↓
DMA Interrupt

# Bootloader Flow
Bootloader Starts
        ↓
Check Firmware
        ↓
Read Application MSP
        ↓
Load MSP
        ↓
Read Application Reset_Handler
        ↓
Jump to Application

# FreeRTOS Context Switch
SysTick Interrupt
        ↓
Scheduler Runs
        ↓
PendSV Set Pending
        ↓
PendSV Handler
        ↓
Save Task Context
        ↓
Load Next Task Context

- Remember
SysTick decides
PendSV switches

# Final Revision (Must Remember)
0x00000000 → Initial MSP
0x00000004 → Reset_Handler
MSP → Interrupts
PSP → RTOS Tasks
.text → Flash
.data → Flash → RAM
.bss → RAM → 0
DMA → Transfers without CPU
PendSV → Context Switch
SysTick → RTOS Tick
Startup File → Vector Table
Linker Script → Memory Layout

- Hard Fault Analysis
What is Hard Fault?
A Hard Fault occurs when the CPU encounters a serious error and cannot continue normal execution.
Handler:HardFault_Handler() { }

# Common Reasons
1. Invalid Memory Access
Example:
int *ptr = (int *)0xFFFFFFFF;
*ptr = 10;
CPU tries to access invalid memory.
→ Hard Fault

# 2. Stack Overflow
Example:
void func()
{
    char buf[10000];
}

or infinite recursion:
func()
{
    func();
}
Stack exceeds its limit.
→ Hard Fault

# 3. Divide By Zero
If enabled:
int a = 10;
int b = 0;
a = a / b;
→ Usage Fault
→ May escalate to Hard Fault

# 4. Jumping to Invalid Address
Example:
void (*ptr)(void);
ptr = (void *)0x12345678;
ptr();
→ Hard Fault

# How to Debug?
When CPU enters:
HardFault_Handler()
{
    while(1);
}

Check:
PC  → Where crash happened
LR  → Where it came from
MSP → Stack contents

# Very Common Real-Life Causes
NULL Pointer Access
Array Out of Bounds
Stack Overflow
Corrupted Function Pointer
Wrong Memory Address

# NVIC Priority
Why Priority is Needed?
Suppose these interrupts occur together:
CAN
UART
Timer

Question:
Which ISR should run first?
NVIC uses priority to decide.

# Priority Rule
In Cortex-M:
Lower Number = Higher Priority

Example:
CAN   = Priority 1
UART  = Priority 3
Timer = Priority 5

Execution order:
CAN
 ↓
UART
 ↓
Timer

# Example
UART ISR Running
Priority = 3
During execution:
CAN Interrupt Occurs
Priority = 1
Result: CAN preempts UART
because: 1 < 3

# Same Priority
Example:
UART = Priority 2
CAN  = Priority 2
UART already running.
CAN occurs.
Result: CAN waits
UART finishes first
No preemption.

# Priority vs Subpriority
NVIC can divide priority into:
Preemption Priority
Subpriority

Preemption Priority
Decides: Can interrupt another ISR?

Subpriority
Decides: Which pending interrupt runs first?
if preemption priorities are equal.

# Example
CAN
Preemption = 1
Subpriority = 0

UART
Preemption = 1
Subpriority = 1

# Both occur together.
Result: CAN runs first
because: Subpriority 0 > Subpriority 1
(when preemption priority is same).

# Important Rule
Lower priority number = Higher interrupt priority
This confuses many engineers initially.

# Quick Revision
Priority 0 → Highest
Priority 1 → Higher
Priority 5 → Lower
Priority 15 → Lowest

- Memory Map
# What is Memory Map?
A Memory Map defines how the MCU address space is organized.
In Cortex-M:
Every memory region
Every peripheral
Every register
has a unique address.

# Why Important?
Because Cortex-M uses:
Memory-Mapped I/O

which means: Peripherals are accessed like memory.

Example: GPIOA_ODR = 0x01;
Actually: CPU writes to a memory address assigned to GPIOA_ODR.

# Cortex-M Memory Map
Typical layout:
0x00000000 - Code Region
0x20000000 - SRAM Region
0x40000000 - Peripheral Region
0xE0000000 - System Control Region

# Code Region
Starts at: 0x00000000
Contains:
Vector Table
Program Code
Constants
Usually mapped to Flash.

# SRAM Region
Starts at: 0x20000000
Contains:
.data
.bss
Stack
Heap

# Peripheral Region
Starts at: 0x40000000
Contains:
GPIO
UART
SPI
I2C
CAN
Timers

# Example
Suppose:
GPIOA Base Address 0x40020000
Writing: GPIOA->ODR = 0x01;
actually means: Write to memory address inside peripheral region.

# System Control Region 
Starts at: 0xE0000000
Contains:
NVIC
SysTick
SCB
Debug Registers
These belong to the Cortex-M core.

# Key Concept
Memory-Mapped I/O
Peripherals look like memory.

# Example:
RAM Address
0x20000000
and
GPIO Register
0x40020000
Both are accessed using normal load/store instructions.

# Vector Table (Deep Revision)
What is a Vector Table?
The Vector Table is a table of addresses that tells the CPU:
Which handler to execute for each exception/interrupt.

# Where is it Located?
Normally: Beginning of Flash
Address: 0x00000000
(usually mapped to Flash)

# First Two Entries
Most important entries:
0x00000000 → Initial MSP Value
0x00000004 → Reset_Handler Address

After reset:
Load MSP
      ↓
Load PC
      ↓
Execute Reset_Handler

# Example Vector Table
Address      Contents
0x00000000   Initial MSP
0x00000004   Reset_Handler
0x00000008   NMI_Handler
0x0000000C   HardFault_Handler
0x00000010   MemManage_Handler
0x00000014   BusFault_Handler
0x00000018   UsageFault_Handler

# External Interrupts
After system exceptions:
USART1_IRQHandler
CAN_RX_IRQHandler
TIM2_IRQHandler
EXTI_IRQHandler
etc.
Their addresses are also stored in the Vector Table.

# How Interrupt Works?
Example:
UART Interrupt Occurs

CPU:
Looks in Vector Table
        ↓
Finds USART1_IRQHandler address
        ↓
Loads PC
        ↓
Executes ISR

# Why Important for Bootloader?
Suppose:
Bootloader → 0x08000000
Application → 0x08010000

Application has its own:
Vector Table
MSP
Reset_Handler

Bootloader may update:
SCB->VTOR
to point to:
0x08010000
so interrupts use the application's vector table.

# VTOR Connection
We learned:
SCB → VTOR Register
VTOR tells CPU:
Where the Vector Table is located.

# Final Revision
Vector Table
↓
Contains handler addresses
↓
Entry 0 → MSP

Entry 1 → Reset_Handler
↓
Used during Reset
↓
Used during Interrupts
↓
Location controlled by VTOR