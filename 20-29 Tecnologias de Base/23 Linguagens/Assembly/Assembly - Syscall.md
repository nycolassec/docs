# O que são Syscalls no Assembly

Mesmo escrevendo instruções diretamente para a CPU, um programa **não consegue acessar hardware ou recursos do sistema operacional diretamente** (tela, arquivos, rede etc.).

Quem faz essa intermediação é o **kernel do sistema operacional**.

A forma de pedir algo ao kernel é através de uma **syscall (system call)**.

➡️ Pense assim:

|Camada|Função|
|---|---|
|Assembly|seu código|
|Syscall|pedido de serviço|
|Kernel|executa o serviço|
|Hardware|executa fisicamente|

Exemplo de serviços:
- escrever texto na tela
- abrir arquivo
- ler arquivo
- criar processo
- encerrar programa
- enviar dados pela rede
***
Algumas syscalls são providas pela kernel do linux, podemos acessa-las no arquivo:
```sh
cat /usr/include/x86_64-linux-gnu/asm/unistd_64.h
```

Para sabermos como utilzar cada uma podemos usar o comando man mais o número da syscall
```sh
man -s 2 write
```

---

# Por que syscalls existem?

Sem syscall, você teria que:

- acessar diretamente memória de vídeo
- lidar com drivers
- tratar interrupções de hardware
- controlar buffers de saída

Isso tornaria o código extremamente complexo.

A syscall abstrai tudo isso.

---

# Estrutura de uma syscall no Linux x86_64

Uma syscall segue uma convenção fixa de registradores.

## Registradores usados

| Função            | Registrador |
| ----------------- | ----------- |
| número da syscall | rax         |
| argumento 1       | rdi         |
| argumento 2       | rsi         |
| argumento 3       | rdx         |
| argumento 4       | rcx         |
| argumento 5       | r8          |
| argumento 6       | r9          |

Depois de configurar os registradores:

syscall

executa a chamada ao kernel.

---

# Exemplo: syscall write (imprimir na tela)

Protótipo em C:

ssize_t write(int fd, const void *buf, size_t count);

Argumentos:

|argumento|significado|
|---|---|
|fd|onde escrever|
|buf|endereço do texto|
|count|tamanho do texto|

Valor comum de fd:

|fd|destino|
|---|---|
|0|stdin|
|1|stdout|
|2|stderr|

---

# Código explicado

section .data  
message db "Fibonacci Sequence:", 0x0a

Cria uma string na memória.

0x0a = newline (\n)

O label `message` é um ponteiro para o endereço da string.

---

mov rax, 1

1 = número da syscall write

---

mov rdi, 1

primeiro argumento:

fd = 1 → stdout (terminal)

---

mov rsi, message

segundo argumento:

ponteiro para a string

rsi contém o endereço de memória da string.

---

mov rdx, 20

terceiro argumento:

quantidade de bytes a imprimir.

---

syscall

executa a syscall:

kernel recebe:

rax = 1 → write  
rdi = 1 → stdout  
rsi = endereço da string  
rdx = tamanho

resultado:

texto aparece no terminal.

---

# Parte do Fibonacci

xor rax, rax  
xor rbx, rbx  
inc rbx

Inicialização:

|registrador|valor|
|---|---|
|rax|0|
|rbx|1|

primeiros números da sequência de Fibonacci.

---

Loop:

loopFib:  
add rax, rbx  
xchg rax, rbx  
cmp rbx, 10  
js loopFib

### passo a passo

### 1

add rax, rbx

rax = rax + rbx

gera próximo número da sequência.

---

### 2

xchg rax, rbx

troca valores.

equivalente a:

temp = rax  
rax = rbx  
rbx = temp

isso mantém os dois últimos valores da sequência.

---

### 3

cmp rbx, 10

faz:

rbx - 10

mas não salva resultado, apenas altera flags.

---

### 4

js loopFib

jump if sign (resultado negativo).

Ou seja:

continua loop enquanto rbx < 10.

---

# Syscall exit

Problema:

sem syscall exit, o programa termina de forma incorreta → segmentation fault.

Por isso usamos:

mov rax, 60  
mov rdi, 0  
syscall

60 = syscall exit

argumento:

|registrador|valor|
|---|---|
|rdi|código de saída|

0 = execução correta.

---

# Fluxo completo do programa

1. define string
2. chama syscall write
3. calcula fibonacci
4. chama syscall exit

---

# Por que isso é importante para Pentest e Exploit Dev

Você está estudando HTB e shellcode, então isso é fundamental porque:

shellcodes usam syscalls diretamente.

Exemplo clássico:

execve("/bin/sh")

usa syscall:

rax = 59

sem libc.

---

# Resumo técnico

syscall = interface direta com o kernel.

estrutura:

mov rax, syscall_number  
mov rdi, arg1  
mov rsi, arg2  
mov rdx, arg3  
syscall

exemplo write:

mov rax, 1  
mov rdi, 1  
mov rsi, message  
mov rdx, length  
syscall

exemplo exit:

mov rax, 60  
mov rdi, 0  
syscall

---

Se quiser, posso também te mostrar:

- diagrama visual de syscall
- lista das syscalls mais usadas em shellcode
- como transformar números em ASCII para imprimir Fibonacci
- por que o exercício diz que imprimir o número diretamente não funciona