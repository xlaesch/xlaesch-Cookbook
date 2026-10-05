
# Registers

Each CPU core has a set of registers, being the fastest component in any computer. There are two types of registers
- Data registers for storing instructions/syscall arguments (`rax`, `rbx`, `rcx`, `rdx`, etc.). 
- Pointer registers used to store specifc important address pointers. The main pointer registers are the base stack pointer `rbp` which points to the beginning of the Stack, the Current Stack Pointer `rsp` , which points to the current location within the Stack. and the instruction pointer `rip`, which holds the address of the next instruction.

# Sub-Registers

Each 64-bit register can be further divided into a smaller sub-registers containing the lower bits, at one byte 8-bits, 2 bytes and 4 bytes. Each sub-register can be used and accessed on its own, so we don't have to consume the full 64 bites if we have a smaller amount of data.

Sub-registers can be accessed as:

| Size in bits | Size in bytes | Name                                   | Example |
| ------------ | ------------- | -------------------------------------- | ------- |
| `16-bit`     | `2 bytes`     | the base name                          | `ax`    |
| `8-bit`      | `1 bytes`     | base name and/or ends with `l`         | `al`    |
| `32-bit`     | `4 bytes`     | base name + starts with the `e` prefix | `eax`   |
| `64-bit`     | `8 bytes`     | base name + starts with the `r` prefix | `rax`   |

# Memory Addresses

x86 64-bit processors ahve 64-bit wide addresses. However, RAM is segmented into various regions, like the Stack, heap, etc. Each memory region has specific read, write, execute permissions that specify wheter we can read from it, write to it, or call an address to it. Whenever an instructions goes through the Instruction Cycle, the first step is to fetch the instruction from the address it's located at. In the x86 there are

|Addressing Mode|Description|Example|
|---|---|---|
|`Immediate`|The value is given within the instruction|`add 2`|
|`Register`|The register name that holds the value is given in the instruction|`add rax`|
|`Direct`|The direct full address is given in the instruction|`call 0xffffffffaa8a25ff`|
|`Indirect`|A reference pointer is given in the instruction|`call 0x44d000` or `call [rax]`|
|`Stack`|Address is on top of the stack|`add rsp`|

# Endinanness

Order of its bytes in which they are stored or retrieved from memory. 
- Little-endian, the little-end bytes of the address is filled first so right-to-left
- Big-endian processors, the big-end byte is filled/retrieved first left-to-right.

Little-endian byte order is used in with Intel/AMD x86 in most OSes, so the shellcode is always represented right-to-left. 

# Data Type

x86 supportsmany types of data sizes.

| Component             | Length            | Example              |
| --------------------- | ----------------- | -------------------- |
| `byte`                | 8 bits            | `0xab`               |
| `word`                | 16 bits - 2 bytes | `0xabcd`             |
| `double word (dword)` | 32 bits - 4 bytes | `0xabcdef12`         |
| `quad word (qword)`   | 64 bits - 8 bytes | `0xabcdef1234567890` |
Whenever we use a variable with a certain data type both operands should be of the same size.

