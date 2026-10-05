
The Central Processing Unit (CPU) is the main processing unit within a computer. The manner in which a CPU processes its instructions depends on its Instruction Set Architecture (ISA). 

# Clock Speed and Cycle

Each CPU hash a clock speed that indicates its overall speed. Every tick of the clock runs a clock cycle that processes a basic instruction done by the CU or ALU. It is counted in cycles per second (Hertz). 

# Instruction Cycle

The cycle it takes the CPU to process a single machine instruction. Each instruction is made up of four stages:

|**Instruction**|**Description**|
|---|---|
|`1. Fetch`|Takes the next instruction's address from the `Instruction Address Register` (IAR), which tells it where the next instruction is located.|
|`2. Decode`|Takes the instruction from the IAR, and decodes it from binary to see what is required to be executed.|
|`3. Execute`|Fetch instruction operands from register/memory, and process the instruction in the `ALU` or `CU`.|
|`4. Store`|Store the new value in the destination operand.|
All of the stages are done my the CU except for arithmetic instructions that are done by the ALU.

Each instruction can take multiple clock cycles to finish depending on the CPU architecture, once the single instruction ends the CU increments to the next instruction. 


![[instruction-cycle.png]]

For example, if we were to execute the assembly instruction `add rax, 1`, it would run through an instruction cycle:

1. Fetch the instruction from the `rip` register, `48 83 C0 01` (in binary).
2. Decode '`48 83 C0 01`' to know it needs to perform an `add` of `1` to the value at `rax`.
3. Get the current value at `rax` (by `CU`), add `1` to it (by the `ALU`).
4. Store the new value back to `rax`.

In the past, processors processed instruction sequentially as seen above. Modern processors can process multiple instructions in parallel by having multiple instruction/clock cycles running at the same time. Made possible by having a multi-thread and multi-core design.

# Processors

Each processors understands a different set of instructions. A single ISA may also have several syntax interpretations.