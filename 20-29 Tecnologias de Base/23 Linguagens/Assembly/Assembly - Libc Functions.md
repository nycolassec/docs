## 1. Importando o Poder Estrangeiro (`extern`)

No Assembly, se você quer usar algo que não escreveu, precisa avisar ao montador. Usamos a diretiva `extern`.

Snippet de código

```
global _start
extern scanf, printf  ; "Vou usar essas funções que estão em outro lugar"
```

## 2. Onde Guardar os Dados? (`.data` e `.bss`)

Para usar o `scanf` (que lê entrada), precisamos de duas coisas:

1. **O Formato:** Uma string dizendo que queremos um número inteiro (`%d`). Isso vai na seção `.data`.
    
2. **O Espaço:** Um lugar vazio na memória para guardar o número que o usuário digitar. Usamos a seção `.bss` (espaço reservado).
    

Snippet de código

```
section .data
    prompt    db "Digite o número máximo: ", 0
    inFormat  db "%d", 0        ; Formato para número inteiro

section .bss
    userInput resb 8            ; Reserva 8 bytes para o número do usuário
```

---

## 3. A Regra de Ouro: Convenção de Chamada

Diferente dos seus procedimentos internos, as funções da Libc são exigentes. Você deve seguir a **Convenção de Chamada (Calling Convention)** do sistema (no Linux x64):

1. **Argumentos nos Registradores:**
    
    - 1º argumento vai em `RDI`.
        
    - 2º argumento vai em `RSI`.
        
    - 3º argumento vai em `RDX`.
        
2. **Alinhamento da Pilha (Stack Alignment):** A CPU espera que a Pilha esteja alinhada em **16 bytes** antes de um `call`.
    

---

## 4. Criando o Procedimento `getInput`

Aqui está como montamos a chamada para o `scanf`. Note o truque do `rsp`:

Snippet de código

```
getInput:
    sub rsp, 8          ; AJUSTE DE PILHA: O 'call' empurrou 8 bytes, 
                        ; subtraímos mais 8 para fechar 16 (alinhamento).

    mov rdi, inFormat   ; 1º argumento: O formato "%d"
    mov rsi, userInput  ; 2º argumento: O endereço onde o número será salvo
    call scanf          ; Chama a função da Libc

    add rsp, 8          ; Restaura a pilha para o estado original
    ret
```

---

## 5. Integrando no Programa Principal

Agora, em vez de comparar o Fibonacci com um número fixo (como `10`), comparamos com o que está guardado em `userInput`.

Snippet de código

```
_start:
    call printMessage   ; Exibe o convite para o usuário
    call getInput       ; Pega o número do usuário e salva em [userInput]
    call initFib        ; Prepara os números (0 e 1)

loopFib:
    add rax, rbx
    xchg rax, rbx
    
    ; COMPARAR COM A ENTRADA DO USUÁRIO
    cmp rbx, [userInput] ; Compara o número atual com o valor na memória
    js loopFib           ; Se for menor, continua o loop
    
    call Exit
```

> **Nota Crítica:** Usamos `[userInput]` entre colchetes. Sem os colchetes, você estaria comparando com o **endereço** da memória; com os colchetes, você compara com o **valor** que está lá dentro.

---

## 6. O Desafio do Linker (Ligação Dinâmica)

Como você está usando funções de uma biblioteca externa (`libc`), o comando comum de compilação não funciona. Você precisa dizer ao sistema onde encontrar essa biblioteca no momento em que o programa rodar.

O comando no terminal seria:

1. **Montar:** `nasm -f elf64 programa.s`
    
2. **Ligar:** `ld programa.o -o programa -lc --dynamic-linker /lib64/ld-linux-x86-64.so.2`
    

- `-lc`: Liga com a biblioteca C (libc).
    
- `--dynamic-linker`: Aponta para o "carregador" que encontra as bibliotecas no Linux.
    

### Resumo Visual do Fluxo:

1. **Imprime:** "Até quanto quer contar?"
    
2. **Captura:** `scanf` pega o texto "100", converte para número e joga em `userInput`.
    
3. **Processa:** O loop de Fibonacci roda e para assim que atingir o valor em `userInput`.
    