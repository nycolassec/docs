Para usar o `printf` com múltiplos argumentos, você só precisa seguir a "fila" de registradores definida pela **System V ABI** (o padrão do Linux x86-64).

### 📋 A Ordem dos Registradores

Pense no `printf` como uma função que consome registradores na seguinte ordem:

1. **`rdi`**: O "chefe" (o endereço da sua String de Formatação).
    
2. **`rsi`**: O primeiro valor (para o primeiro `%d`, `%s`, etc).
    
3. **`rdx`**: O segundo valor (para o segundo `%d`, `%s`, etc).
    
4. **`rcx`**: O terceiro valor.
    
5. **`r8`**: O quarto valor.
    
6. **`r9`**: O quinto valor.
    

---

### 💻 Exemplo Prático

Imagine que você quer imprimir: `"A soma de [A] + [B] é [C]"`. Você teria 3 variáveis.

Snippet de código

```c
section .data
    ; String com três espaços reservados (%d)
    msg db "Valores: %d e %d | Soma: %d", 10, 0

section .text
    extern printf
    global _start

_start:
    ; Vamos carregar os valores para os registros
    mov rsi, 10        ; Primeiro argumento (depois da string)
    mov rdx, 20        ; Segundo argumento
    mov rcx, 30        ; Terceiro argumento (a soma)
    
    ; Agora configuramos a chamada
    mov rdi, msg       ; Endereço da string de formato sempre em RDI
    mov rax, 0         ; IMPORTANTE: printf é variádica, rax=0 indica 0 pontos flutuantes

    ; Alinhamento da pilha (16 bytes)
    sub rsp, 8
    call printf
    add rsp, 8

_exit:
    mov rax, 60
    xor rdi, rdi
    syscall
```

---

### ⚠️ Três Regras de Ouro para Múltiplos Argumentos:

1. **`mov rax, 0`**: Sempre que chamar `printf` (ou `scanf`), coloque `0` em `rax`. Isso avisa ao `printf` que você não está passando números decimais (float/double) nos registradores `xmm`. Se esquecer disso, o programa pode dar crash de forma imprevisível.
    
2. **A Contagem de `%`**: O número de registros que você preenche (`rsi`, `rdx`, `rcx`...) deve ser exatamente igual ao número de `%` que você colocou na sua string. Se colocar três `%d` e só preencher `rsi` e `rdx`, o `printf` vai imprimir um lixo de memória para o terceiro valor.
    
3. **Preservação**: Lembre-se que o `printf` **vai destruir** o valor de `rax` (ele retorna o número de caracteres impressos lá) e de outros registradores voláteis. Se você precisar do valor da soma depois do print, salve-o no `rbx` ou na pilha antes do `call`.