
Specifies the syntax and semantics of the assembly language on each architecture. 

|Component|Description|Example|
|---|---|---|
|`Instructions`|The instruction to be processed in the `opcode operand_list` format. There are usually 1,2, or 3 comma-separated operands.|`add rax, 1`, `mov rsp, rax`, `push rax`|
|`Registers`|Used to store operands, addresses, or instructions temporarily.|`rax`, `rsp`, `rip`|
|`Memory Addresses`|The address in which data or instructions are stored. May point to memory or registers.|`0xffffffffaa8a25ff`, `0x44d0`, `$rax`|
|`Data Types`|The type of stored data.|`byte`, `word`, `double word`|

There are two main ISAs that are widely used
- Complex Insutrction Set Computer (CISC) used in intel and AMD processors.
- Reduced Instruction Set Computer (RISC) used in ARM and Apple processors.

# CISC

Favors more complex instructions to be run at a time to reduce the overall number of instructions. Relies as much as possible on the CPU by combining minor instructions into more complex ones.

The main reasons behind such a design:
- Enables more instructions to be executed at once by designing the processor to run more advanced instructions in its core.
- Before, memory and transistors were limited so it was preferred to write shorter programs.

The processor's design becomes more complicated, as it is designed to execute a vast amount of complex instructions.

# RISC

Spitting instructions into minor instructions, and os the CPU is designed only to handle simple instructions.  Supports a limited instruction-set. 
