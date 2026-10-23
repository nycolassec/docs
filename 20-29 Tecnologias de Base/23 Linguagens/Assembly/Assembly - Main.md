***
## Assembler e link do código
#### Linux
```bash

#### 32 bits ####
nasm -f elf32 -g -F dwarf program.asm -o program.o # Faz o assembler do código
ld -m elf_i386 program.o -o program # Faz o link das bibliotecas necessárias

#### 64 bits ####
nasm -f elf64 -g -F dwarf program.asm -o program.o # Faz o assembler do código
ld -m elf_x86_64 program.o -o program # Faz o link das bibliotecas necessárias
```
#### Windows
```powershell
#### 32 bits ####
nasm -f win32 -g -F cv8 program.asm -o program.obj # Faz o assembler do código
gcc -m32 program.obj -o program.exe # Faz o link das bibliotecas necessárias

#### 64 bits ####
nasm -f win64 -g -F cv8 program.asm -o program.obj # Faz o assembler do código
gcc program.obj -o program.exe # Faz o link das bibliotecas necessárias
```
***
## Hello World
Tabela de syscalls do linux [Linux System Call Table](https://www.chromium.org/chromium-os/developer-library/reference/linux-constants/syscalls/#i686_4)
#### Hello World
```c
section .text
global _start

_start:
	mov eax, 0x04
	mov ebx, 0x01
	mov ecx, msg
	mov edx, len
	int 0x80
	
	mov eax, 0x01
	mov ebx, 0x0
	int 0x80
	
	section .data
	msg db 'Hello world', 0x0a
	len equ $ - msg
```
#### Comparação simples
```c
section .text
  global _start

_start:
  mov eax, 0x05
  mov ebx, 0x05
  cmp eax, ebx
  je label1
  jmp label2

label1:
  mov eax, 0x04
  mov ebx, 0x01
  mov ecx, msg1
  mov edx, len1
  int 0x80
  jmp end

label2:
  mov eax, 0x04
  mov ebx, 0x01
  mov ecx, msg2
  mov edx, len2
  int 0x80
  jmp end

section .data
  msg1 db 'São iguais', 0x0a
  len1 equ $ - msg1
  msg2 db 'Não são iguais', 0x0a
  len2 equ $ - msg2

end:
  mov eax, 0x01
  mov ebx,
```
***
```c
global _start

section .data
    num1    dq 37
    num2    dq 5

section .bss
    buf     resb 20

section .text

int_to_str:
    mov  rcx, buf + 19
    mov  byte [rcx], 0x0a
    dec  rcx
    mov  rbx, 10
.loop:
    xor  rdx, rdx
    div  rbx
    add  dl, '0'
    mov  [rcx], dl
    dec  rcx
    test rax, rax
    jnz  .loop
    inc  rcx
    mov  rsi, rcx
    mov  rdx, buf + 20
    sub  rdx, rcx
    ret

_start:
    ; soma
    mov  rax, [num1]
    add  rax, [num2]

    ; converte e printa
    call int_to_str
    mov  rax, 1
    mov  rdi, 1
    syscall

    ; exit
    mov  rax, 0x3c
    xor  rdi, rdi
    syscall
```
***
## Disassemble
```sh
objdump -M intel --no-show-raw-insn --no-addresses -d

objdump -sj .data helloWorld
```