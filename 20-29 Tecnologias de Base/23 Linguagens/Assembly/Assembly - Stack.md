# Stack (pilha) em Assembly x86_64

A **stack** é uma região da memória RAM usada para armazenar dados **temporários** durante a execução de um programa.

Ela é muito usada para:

- salvar valores de registradores
- passar parâmetros para funções
- armazenar endereços de retorno (CALL/RET)
- variáveis locais
- preservar contexto antes de syscalls

---

# Estrutura da Stack

A stack funciona como uma pilha de pratos:

- você só pode adicionar ou remover do topo
- o último elemento inserido é o primeiro a sair

Esse comportamento é chamado de:

> **LIFO (Last In, First Out)**

---

# Registradores importantes

### RSP (Stack Pointer)

Aponta para o topo da stack.

rsp → último valor inserido

### RBP (Base Pointer)

Aponta para a base da stack (referência fixa do frame da função).

rbp → base da stack frame

---

# Instruções principais

## PUSH

Copia um valor para o topo da stack.

push rax

O processador:

1. diminui o endereço de rsp
2. grava o valor de rax nesse novo endereço

Ou seja:

ANTES:  
RAX = 5  
  
STACK:  
(empty)  
  
DEPOIS:  
STACK:  
5  ← rsp

---

## POP

Remove o valor do topo da stack e coloca em um registrador.

pop rax

O processador:

1. lê o valor no endereço rsp
2. coloca o valor no registrador
3. incrementa rsp

---

# Ordem importa (LIFO)

Exemplo:

push rax  
push rbx

Stack:

rbx ← topo (rsp)  
rax

Para restaurar corretamente:

pop rbx  
pop rax

Se inverter:

pop rax  
pop rbx

os valores serão trocados.

---

# Por que salvar registradores antes de syscall ou função?

Funções e syscalls frequentemente modificam registradores.

Exemplo:

mov rax, 1  
syscall

Depois da syscall, **rax pode ter outro valor**.

Se você precisa preservar o valor original:

push rax  
syscall  
pop rax

Assim você mantém o estado do programa consistente.

Isso é fundamental em:

- shellcode
- ROP chains
- exploits
- funções recursivas
- ABI compliance

---

# Explicação do código do material

global _start  
  
section .text  
_start:  
    xor rax, rax  
    xor rbx, rbx  
    inc rbx

Inicializa:

rax = 0  
rbx = 1

---

## Salvando registradores

push rax  
push rbx

Stack agora:

rbx ← topo  
rax

Os valores ainda permanecem nos registradores.

push faz **cópia**, não remove.

---

## Restaurando registradores

pop rbx  
pop rax

Valores retornam aos registradores:

rbx = 1  
rax = 0

A stack volta ao estado inicial.

---

# Visualização da memória stack

Exemplo do gdb mostrado no material:

0x00007fffffffe408│ 0x0000000000000001 ← rsp  
0x00007fffffffe410│ 0x0000000000000000

Topo da stack contém:

rbx = 1  
rax = 0

Exatamente na ordem esperada.

---

# Relação com exploração de binários (muito importante para você)

A stack é alvo comum em ataques:

### Buffer Overflow

Sobrescrever valores na stack:

[ buffer ][ saved rbp ][ return address ]

Permite controlar:

- RIP
- fluxo do programa
- execução de shellcode

---

# Resumo técnico

Stack:

- memória LIFO
- controlada por rsp
- usada para contexto temporário
- essencial para funções e syscalls

Instruções:

push → salva valor na stack  
pop  → recupera valor da stack

Regra crítica:

> A ordem do pop deve ser inversa ao push