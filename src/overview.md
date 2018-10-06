# Overview of embedded programming and debugging

Here you will get an overview of how programming, the chip that is, and debugging of embedded hardware is connected and utilized.
Further into this book we will touch on everything from simply flashing a binary onto a chip to single stepping through each instruction, inspecting memory and register, instructing the CPU where to halt execution and wait for input, look for changes in specific memory locations, etc.

## Why programmers and debuggers

### What are debuggers and programmers?

Commonly, a debugger and a programmer is the same piece of hardware, which have the specific function to act as bridges to talk from a PC to a micro controller.
In contrary to developing a program and running it on a PC, we need both to move the compiled program to the micro controller and instruct the micro controller to execute the program - this transfer of programs and tool to command the micro controllers are called *debuggers*.
While these tools are very powerful and gives the user complete access and jurisdiction over a micro controller, even to the point that the user can rewrite arbitrary memory manually if desired, they are also our main tool to understand and debug the inner workings of the system as we are used to in normal PC development.

**SHOULD WE ADD IMAGES OF COMMON HARDWARE TOGETHER WITH SOME DESCRIPTION?**


## Interfaces / Protocols

When it comes to talking to micro controllers, there are two common interfaces JTAG (Joint Test Action Group) and SWD (Serial Wire Debug).

### JTAG

While [JTAG] has a long history, the main feature of JTAG, which SWD does not have, is the possibility to daisy-chain multiple micro processors, FPGAs, memory chips, simply anything which supports JTAG, and access them over the same JTAG buss. The drawback is that this requires more signals than SWD, and these signals are:

* TDI (Test Data In)
* TDO (Test Data Out)
* TCK (Test Clock)
* TMS (Test Mode Select)
* TRST (Test Reset) which is optional

In the following image you can see a common communication example with JTAG when there are multiple devices:

![Alt text](./JTAG_example.svg)

[JTAG]: https://en.wikipedia.org/wiki/JTAG

### SWD

[SWD] is quite similar to JTAG, as it uses the same protocol but it is limited to two electrical signals and cannot connect multiple chips.
While JTAG is vastly more versatile, SWD's strength is in its simplicity as many systems only has one micro controller to program and in these cases the reduction of signals while still having full debugability is a major win.
In SWD the following signals are available:

* SWDIO (Serial Wire Debug Input / Output)
* SWCLK (Serial Wire Clock)

In the following image you can see a common communication example with SWD communicating to a single micro controller:

![Alt text](./SWD_example.svg)

[SWD]: https://en.wikipedia.org/wiki/JTAG#Serial_Wire_Debug

### Which one to use?

When designing a system, we should select the interface which is most convenient for the system, and there is a simple criteria to start with: Does the systems need to program and debug more than one target? If yes, JTAG is a good choice, else SWD with its minimal amount of signals is a good choice.


## Common debugger platforms

### [OpenOCD]

OpenOCD (Open On-Chip Debugger) is a very popular piece of software which provides an interface for a debugger software, such as GDB, to connect to in order to provide in-system programming and boundary-scan testing for embedded devices.
However, OpenOCD is no hardware which is connected to, for example, a micro controller it needs this extra hardware known as a debug adapter to translate the debug commands into electrical signals which the system can understand.
And as we talked about JTAG and SWD earlier, OpenOCD supports both of these signaling schemes through supported debug adapters which are commonly connected to the PC via USB and can be found from very cheap on for example eBay, or built into evaluation boards from micro controller manufacturers, to very expensive, special purpose, adapters.

[OpenOCD]: http://openocd.org/

### [Segger JLink]

**I know nothing about JLink, Ryan, please add here :)**

[Segger JLink]: https://www.segger.com/products/debug-probes/j-link/

### [Black Magic Probe]

The BMP (Black Magic Probe) is different from OpenOCD, where it is hardware, commonly the same as a debug adapter, which runs an GDB server over serial inside it, so there is no need to install OpenOCD - in a sense, it is OpenOCD combined with a debug adapter in the same dongle.
The BMP is open hardware and runs open source software inside it as well, plus using GDB with the BMP is no different to OpenOCD, down to a few commands, so now there is a plethora of adapters to use.

[Black Magic Probe]: https://github.com/blacksphere/blackmagic/wiki

### Debugger used in this book

In coherence with the Discovery Book, this book uses the STM32F3Discovery platform for all examples, which has an STLink v2 debug adapter which we will use OpenOCD to talk to.

## Debugging Tools

There are currently two main debugging softwares on the PC side, the Gnu Debugger (GDB) and LLVM's Debugger (LLDB), however at the time of writing this book LLDB does not have the required support to debug the targets we are going learn on, and hence GDB will be the tool of this book.
