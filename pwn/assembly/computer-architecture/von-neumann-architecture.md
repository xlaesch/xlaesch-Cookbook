
Most modern computers use the Von Neumann Architecture. This architecture executes machine code to perform specific algorithms. It mainly consists of the following elements:

- Central Processing Unit (CPU), consisting of:
	- Control Unit (CU)
	- Arithmetic/Logic Unit (ALU)
	- Registers
- Memory Unit
- Input/Output Devices
    - Mass Storage Unit
    - Keyboard
    - Display


# Memory

Where temporary data and instructions of currently running programs are located. There are two types of memory:

## Cache

Located within the CPU and is extremely fast compared to RAM, running at same clock speed as the CPU. Limited in size. Within cache there are 3 levels
- L1 Cache in KB being the fastest. Located in each CPU core.
- L2 Cache in MB dedicated to each core.
- L3 Cache in MB being the slowest of all 3 but faster than RAM.

## RAM

Much larger coming in GBs and TBs. Accessing data from RAM addresses takes many more instructions. With 32 bit addresses, memory addresses were limited to 2^32 bytes which is only 4GB. With 64-bit addresses we can 2^64 bytes which is 18.5 exabytes. 

When a program is run all of its data and instructions are moved from the storage unit to RAM to be accessed when needed by the CPU. 

RAM is split into four main segments. 

|Segment|Description|
|---|---|
|`Stack`|Has a Last-in First-out (LIFO) design and is fixed in size. Data in it can only be accessed in a specific order by push-ing and pop-ing data.|
|`Heap`|Has a hierarchical design and is therefore much larger and more versatile in storing data, as data can be stored and retrieved in any order. However, this makes the heap slower than the Stack.|
|`Data`|Has two parts: `Data`, which is used to hold variables, and `.bss`, which is used to hold unassigned variables (i.e., buffer memory for later allocation).|
|`Text`|Main assembly instructions are loaded into this segment to be fetched and executed by the CPU.|

Each application allocated its Virtual Memory when it is run meaning each application has its own  stack, heap, data, and text segments.

# IO Storage

The processor can access and control; IO devices using Bus Interfaces, which act as 'highways' to transfer data and addresses using electrical charges.

Each Bus has a capacity of bits it can carry at once, usually in multiple of 4-bits all the way to 128-bits. 

Storage unit stores permanent data, like OS files and entire applications. It is the slowest to access. 