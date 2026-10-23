The most critical registers we need are the General registers.
In these, there are further subdivisions into Data registers, Pointer registers, and Index registers.
#### Data registers

| **32-bit Register** | **64-bit Register** | **Description**                                                                                                      |
| ------------------- | ------------------- | -------------------------------------------------------------------------------------------------------------------- |
| `EAX`               | `RAX`               | `Accumulator` é usado em entrada/saída e para operações aritméticas                                                  |
| `EBX`               | `RBX`               | `Base` é usada no endereço indexado                                                                                  |
| `ECX`               | `RCX`               | `Counter` é usado para girar instruções e contagem de loops                                                          |
| `EDX`               | `RDX`               | `Data` é usado ​​para E/S e em operações aritméticas para multiplicar e dividir operações envolvendo grandes valores |
#### Pointer registers

| **32-bit Register** | **64-bit Register** | **Description**                                                                                                    |
| ------------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------ |
| `EIP`               | `RIP`               | `Instruction Pointer` armazena o endereço de deslocamento da próxima instrução a ser executada                     |
| `ESP`               | `RSP`               | O ponteiro da `Stack` aponta para o topo da `Stack`                                                                |
| `EBP`               | `RBP`               | O `Base pointer` também é conhecido como `Stack Base Pointer` ou `Frame Pointer` que aponta para a base da `stack` |
#### Index registers

| **Register 32-bit** | **Register 64-bit** | **Description**                                                                 |
| ------------------- | ------------------- | ------------------------------------------------------------------------------- |
| `ESI`               | `RSI`               | `Source Index` é usado como um ponteiro de uma fonte para operações de string.  |
| `EDI`               | `RDI`               | `Destination` é usado como um ponteiro para um destino para operações de string |

#### sequência dos registradores passados
refeência  :[Chromium OS Docs - Linux System Call Table](https://chromium.googlesource.com/chromiumos/docs/+/master/constants/syscalls.md#x86_64-64_bit)

| %rax | arg0 (%rdi) | arg1 (%rsi) | arg2 (%rdx) | arg3 (%r10) | arg4 (%r8) | arg5 (%r9) |
| :--: | ----------- | ----------- | ----------- | ----------- | ---------- | ---------- |
***
## Instructions
#### Unary Instructions
| Instruction | Description    | Example                                         |
| ----------- | -------------- | ----------------------------------------------- |
| `inc`       | Increment by 1 | `inc rax` -> `rax++` or `rax += 1` -> `rax = 2` |
| `dec`       | Decrement by 1 | `dec rax` -> `rax--` or `rax -= 1` -> `rax = 0` |

#### Binary Instructions
| Instruction | Description                                                | Example                                 |
| ----------- | ---------------------------------------------------------- | --------------------------------------- |
| `add`       | Add both operands                                          | `add rax, rbx` -> `rax = 1 + 1` -> `2`  |
| `sub`       | Subtract Source from Destination (_i.e `rax = rax - rbx`_) | `sub rax, rbx` -> `rax = 1 - 1` -> `0`  |
| `imul`      | Multiply both operands                                     | `imul rax, rbx` -> `rax = 1 * 1` -> `1` |

#### Bitwise Instructions
| Instruction | Description                                                         | Example                                                 |
| ----------- | ------------------------------------------------------------------- | ------------------------------------------------------- |
| `not`       | Bitwise NOT (invert all bits, 0->1 and 1->0)                        | `not rax` -> `NOT 00000001` -> `11111110`               |
| `and`       | Bitwise AND (if both bits are 1 -> 1, if bits are different -> 0)   | `and rax, rbx` -> `00000001 AND 00000010` -> `00000000` |
| `or`        | Bitwise OR (if either bit is 1 -> 1, if both are 0 -> 0)            | `or rax, rbx` -> `00000001 OR 00000010` -> `00000011`   |
| `xor`       | Bitwise XOR (if bits are the same -> 0, if bits are different -> 1) | `xor rax, rbx` -> `00000001 XOR 00000010` -> `00000011` |





















