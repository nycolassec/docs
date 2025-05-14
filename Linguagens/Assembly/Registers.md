The most critical registers we need are the General registers. In these, there are further subdivisions into Data registers, Pointer registers, and Index registers.
#### Data registers

| **32-bit Register** | **64-bit Register** | **Description**                                                                                                      |
| ------------------- | ------------------- | -------------------------------------------------------------------------------------------------------------------- |
| `EAX`               | `RAX`               | `Accumulator` é usado em entrada/saída e para operações aritméticas                                                  |
| `EBX`               | `RBX`               | `Base` é usada no endereço indexado                                                                                  |
| `ECX`               | `RCX`               | `Counter` é usado para girar instruções e contagem de loops                                                          |
| `EDX`               | `RDX`               | `Data` é usado ​​para E/S e em operações aritméticas para multiplicar e dividir operações envolvendo grandes valores |
#### Pointer registers

| **32-bit Register** | **64-bit Register** | **Description**                                                                                                  |
| ------------------- | ------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `EIP`               | `RIP`               | `Instruction Pointer` armazena o endereço de deslocamento da próxima instrução a ser executada                   |
| `ESP`               | `RSP`               | O ponteiro da `Stack` aponta para o topo da `Stack`                                                              |
| `EBP`               | `RBP`               | O `Base pointer` também é conhecido como `Stack Base Pointer` ou `Frame Pointer` que aponta para a base da pilha |
#### Index registers

| **Register 32-bit** | **Register 64-bit** | **Description**                                                                 |
| ------------------- | ------------------- | ------------------------------------------------------------------------------- |
| `ESI`               | `RSI`               | `Source Index` é usado como um ponteiro de uma fonte para operações de string.  |
| `EDI`               | `RDI`               | `Destination` é usado como um ponteiro para um destino para operações de string |




























