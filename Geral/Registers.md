# Registers - 1

## What is Register?
Registers are the variables in the assembly language. The size of each register in x86 architecture is 32 bits. Registers are located in the processor and each processor can have its own proprietary registers.
## General Purpose Registers
General purpose registers can be divided into 3 subcategories:
- Data Registers
- Pointer Registers
- Index Registers

![[regi1.png]]

## Data Registers
Data registers are the ones that contain data.

**EAX (Accumulator Register)**
The most basic register used in arithmetic operations is the "EAX" register. It stores the results of arithmetic operations and the return values of functions. For example, it is used for operations such as addition and multiplication.

**EBX (Base Register)**
It is the register that holds the base address of the program.

**ECX (Counter Register)**
The “**ECX**” is the register that is used as counter. It is used in loop and string operations.

**EDX (Data Register)**
It is a register that is generally used for holding data. It is also used in I/O (Input/Output) operations.
## Pointer Registers
Pointer registers are the ones that hold memory addresses.

**EBP (Base Pointer)**
EBP is the one that holds the lowest address of the Stack and is used for local variables.

**ESP (Stack Pointer)**
It is the register that holds the top address of the stack. Displays the last element that entered the Stack.

**EIP (Instruction Pointer)**
The EIP register may be the most important register as it holds the address of the next instruction to be executed in the program flow. In other words, if the value of this register is changed, the flow of the program can be interfered with.
## Index Registers
Index registers are used for index information storage.

**ESI (Source Index)**
It is the register that holds the source index information for string operations. It holds the address of where the data will be read.

**EDI (Destination Index)**
It is the register that holds the destination index information for string operations. It holds the address of where the data will be written.
***
## Segment Registers
Segment registers are the registers used to hold addresses of specific segments in memory.

**Stack Segment (SS)**
It is the segment register that holds the base location address of the stack.

**Code Segment (CS)**
It is the register which is known as “**.text**” and it holds the address of the code segment used for data access.

**Data Segment (DS)**
It is the register which is also known as “**.data**” and it holds the address of the data segment which is the default variable location for data access.

**Extra Segment (ES)**
It is the register that holds the address of the extra segment used during string operations.
## EFLAGS Register - Status Flags
The EFLAGS register, unlike other registers, is a special register where each bit has a different meaning. The values of the bits of this register allow the CPU operations to be controlled and monitored.

**Adjust Flag (AF)**
It is the flag that is set when there is a transfer from the 3rd bit to the 4th bit in arithmetic operations.

**Carry Flag (CF)**
It is the flag that is set in arithmetic operations when the value of the register is more than the maximum value or if the value is less than the minimum value of the register. For example, the description over 4 bits is as follows:

**Example 1**: 
Initially: “**CF = 0**”
1111 + 0001 = 0000 (CF = 1)
0000 - 0001 = 1111 (CF = 0)

**Example 2**: 
Initially: “**CF = 0**”
0000 - 0001 = 1111 (CF = 1)
**Example 3**: 

Initially: “**CF = 0**”
0111 + 0001 = 1000 (CF = 0)
1000 - 0001 = 0111 (CF = 0)

**Direction Flag (DF)**
It is the flag that determines the direction in the transport and comparison of string data. The string operation is performed from left to right in case of “**DF = 0**”, and from right to left in case of “**DF = 1**”.

**Interrupt Flag (IF)**
It is the flag that determines whether external interruptions are taken into consideration and therefore it is the flag that determines whether the necessary operation is implemented. Keyboard entry would be a good example to the external interruption. Interruptions are ignored in case of "**IF = 0**" and considered disabled, and are applied to the process in case the "**IF = 1**" state.

**Overflow Flag (OF)**
The overflow flag is set when a positive value for the signed integer is too large to be represented in the register, or when a negative value is too small.

**Parity Flag (PF)**
It is the flag that shows the total number of "**1**" bits in the results of arithmetic operations. If the total number of “1” bits is even, it becomes “PF = 1”. If the total number of “**1**” bits is odd, it becomes “**PF = 0**”.

**Example 1**: 
10111100 + 00010001 = 11001101 (PF = 0)

**Example 2**: 
10111100 + 00010000 = 11001100 (PF = 1)

**Sign Flag (SF)**
It is the flag that indicates whether the result of an arithmetic operation is negative or positive. If the result of the arithmetic operation takes a negative value, the sign flag is set as “**SF = 1**”. In case of a positive result, the sign flag will be in the state of “**SF = 0**”.

**Trap Flag (TF)**
It is the flag that allows the processor to set the operating mode as single-step mode. The debugger program sets this flag to run each command one by one. In this way, each command executed by the processor at the assembly level can be executed step by step.

**Zero Flag (ZF)**
It is the flag set depending on whether the result of the arithmetic or the comparison operations is "**0**" (zero). If the result of the operation is “**0**”, then the  zero flag is set as “**ZF = 1**”. If the operation result is other than zero, then the zero flag is not set “**ZF = 0**”.  
  
In this part of the training, we have covered the segment registers and status flags, which are the continuation of register. In the next part of the training, "**X86 Assembly Language and CPU Instructions**" will be covered.
***
## CPU Instructions - 1
Instructions are the commands in the assembly language. There are a lot different type of instructions for different tasks and purposes. Some may have similar duties. It is vital to know the instructions in order to follow the program flow in the assembly language. Below are some of the instructions and their tasks:
## Arithmetic Instructions
Arithmetic instructions are those that perform arithmetic operations between operands. For example, the four arithmetic operations are examples of such transactions.

**ADD**
The “**ADD**” is the instruction that enables to perform the addition. For example:
ADD    ESP, 0x8
With the above instruction, 8 is added to the value of the ESP register.

**SUB**
The "**SUB**" is the instruction that enables the subtraction. For example:
SUB    ESP, 0x4
With the above instruction, 4 is subtracted from the value of the ESP register.

**MUL**
The "**MUL**" is the instruction that enables multiplication to be performed.

**DIV**
The "**DIV**" is the instruction that enables division to be performed.

**INC**
The “**INC**” is an instruction that allows you to increment the value of the operand by 1. For example:
INC    EBX
With the above instruction, the value of the EBX register is incremented by 1.

**DEC**
The “**DEC**” is an instruction that allows you to decrement the value of the operand by 1. For example:
DEC    EBX
With the above instruction, the value of the EBX register is decremented by 1.

**Branch Instructions**
Branch instructions are those that performs the comparison and/or branching. They are vital with respect to follow the program flow in the assembly language. Below are some of these instructions:

**JMP**
The “**JMP**” is the instruction that allows branching unconditionally. It takes the memory address as an operand. For example:
JMP    0x56556020
With the above instruction, the program flow branches to the memory address given as operand (branching).

**JZ/JE**
The “**JZ**” and “**JE**” instructions are among the conditional branching instructions. They stand for: 
JZ = Jump if Zero
JE = Jump if Equal
JE and JZ instructions take memory address as operand. For example:
JE     0x5555555551b5 <main+277>
With the above instruction, the condition for branching to the memory address in the operand is that the zero flag is set to "1" in the form of "**ZF=1**".

**JNZ/JNE**
The “**JNZ**” and “**JNE**” instructions are also among the conditional branching instructions. They stand for: 
JNZ = Jump if not Zero
JNE = Jump if not Equal
JNE ve JNZ instructions take memory address as operand. For example:
JNE    0x565561e7 <main+78>
The condition for branching to the memory address in the operand with the above instruction is that the zero flag is set to "0" in the form of "**ZF=0**".

**CALL**
The “**CALL**” instruction is the instruction used for function call. It takes a function address as an operand. For example:
CALL   0x56556199 \<function1>
With the above instruction, the function named “**function1**” is called. There are 2 basic operations with this instruction:
- The address of the instruction after the CALL instruction in the program flow is pushed to the stack (Return address)
- The value of the EIP register is set as the function address, so that the program flow branches to the corresponding function.

**CMP**
The “**CMP**” instruction is used for comparison operations. Takes 2 values to compare as operands. For example:
CMP    EDX, EAX
The above instruction compares the EAX and EDX registers. Depending on the result of the comparison, “**Zero Flag(ZF)**” and “**Carry Flag(CF)**” may be changed. The below table shows the conditions that will cause these changes.
![[assembly3 1.png]]



![[cpu1.gif]]

































