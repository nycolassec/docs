## Interactive Loops
Let's start by discussing `Loops`. A loop in assembly is a set of instructions that repeat for `rcx` times.

Let's take the following example:
```c
global _start

section .text
_start:
    mov rax, 2
    mov rcx, 5
loop:
    imul rax, rax
    Loop loop
```
***
## Unconditional Branching
The basic `jmp` instruction is unconditional, which means that it will always jump to the specified location, regardless of the conditions. This contrasts with `Conditional Branching` instructions that only jump if a specific condition is met, which we'll discuss next.

|**Instruction**|**Description**|**Example**|
|---|---|---|
|`jmp`|Jumps to specified label, address, or location|`jmp loop`|
```c
global  _start

section .text
_start:
    xor rax, rax    ; initialize rax to 0
    xor rbx, rbx    ; initialize rbx to 0
    inc rbx         ; increment rbx to 1
    mov rcx, 10
loopFib:
    add rax, rbx    ; get the next number
    xchg rax, rbx   ; swap values
    jmp loopFib

```
***
## Conditional Branching
Unlike Unconditional Branching Instructions, `Conditional Branching` instructions are only processed when a specific condition is met, based on the Destination and Source operands. A conditional jump instruction has multiple varieties as `Jcc`, where `cc` represents the Condition Code

| **Instruction** | **Condition** | **Description**                                    |
| --------------- | ------------- | -------------------------------------------------- |
| `jz`            | `D = 0`       | Destination `equal to Zero`                        |
| `jnz`           | `D != 0`      | Destination `Not equal to Zero`                    |
| `js`            | `D < 0`       | Destination `is Negative`                          |
| `jns`           | `D >= 0`      | Destination `is Not Negative` (i.e. 0 or positive) |
| `jg`            | `D > S`       | Destination `Greater than` Source                  |
| `jge`           | `D >= S`      | Destination `Greater than or Equal` Source         |
| `jl`            | `D < S`       | Destination `Less than` Source                     |
| `jle`           | `D <= S`      | Destination `Less than or Equal` Source            |

| Flag | Nome          | O que indica                   |
| ---- | ------------- | ------------------------------ |
| ZF   | Zero Flag     | resultado foi 0                |
| SF   | Sign Flag     | resultado negativo             |
| CF   | Carry Flag    | overflow em operações unsigned |
| OF   | Overflow Flag | overflow signed                |
| PF   | Parity Flag   | paridade do resultado          |
Conditional instructions are not restricted to `jmp` instructions only but are also used with other assembly instructions for conditional use as well, like the `CMOVcc` and `SETcc` instructions.

For example, if we wanted to perform a `mov rax, rbx` instruction, but only if the condition is `= 0`, then we can use the `CMOVcc` or `conditional mov` instruction, such as `cmovz rax, rbx` instruction. Similarly, if we wanted to move if the condition is `<`, then we can use the `cmovl rax, rbx` instruction, and so on for other conditions. The same applies to the `set` instruction, which sets the operand's byte to `1` if the condition is met or `0` otherwise. An example of this is `setz rax`.

```c
global  _start

section .text
_start:
    xor rax, rax    ; initialize rax to 0
    xor rbx, rbx    ; initialize rbx to 0
    inc rbx         ; increment rbx to 1
    mov rcx, 10
loopFib:
    add rax, rbx    ; get the next number
    xchg rax, rbx   ; swap values
    dec rcx         ; decrement rcx counter
    jnz loopFib     ; jump to loopFib until rcx is 0
```

