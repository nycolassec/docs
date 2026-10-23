
![[Pasted image 20260421145441.png]]
No código em questão se deve salvar os registradores antes de chamar os procedimentos ?

Essa é uma excelente pergunta! Ela mostra que você já está pensando na **segurança dos dados** e na integridade dos registradores.

A resposta curta para o seu programa específico é: **Não, você não precisa.**

Mas a resposta completa envolve entender por que às vezes salvamos e por que, no seu caso, isso seria até um erro. Vamos por partes:

---

### 1. Por que você NÃO deve salvar agora?

No seu código, o objetivo da função `soma` é justamente alterar o valor de `rax`.

- Você coloca `15` em `rax`.
    
- Chama `soma`.
    
- A `soma` coloca o resultado ($15 + 27$) em `rax`.
    
- A função `exit` usa esse **novo valor**.
    

Se você salvasse o `rax` na pilha antes de chamar a soma e o restaurasse depois, você estaria "jogando fora" o resultado do cálculo e voltando o `rax` para `15`. Ou seja, você anularia o trabalho da sua própria função.

### 2. Quando você PRECISA salvar?

Você só salva registradores na pilha (usando `push` e `pop`) em duas situações:

1. **Proteção de dados:** Você tem um valor importante em um registrador, precisa chamar uma função que vai "atropelar" esse registrador, mas você vai precisar do valor original logo após a função voltar.
    
2. **Seguir as Regras (ABI):** Quando você escreve funções que outras pessoas (ou bibliotecas como a `libc`) vão usar.
    

---

### 3. As Regras de "Quem limpa a bagunça?"

No mundo x86-64, os registradores são divididos em dois grupos principais. É como uma regra de etiqueta entre o **Chamador** (quem dá o `call`) e o **Chamado** (a função):

#### A. Preservados pelo Chamador (Caller-Saved / Voláteis)

Registradores: `RAX`, `RCX`, `RDX`, `RSI`, `RDI`, `R8` até `R11`.

- **A regra:** O "Chamado" pode fazer o que quiser com eles. Se o "Chamador" (você no `_start`) tem algo importante neles, **você** deve dar o `push` antes de chamar a função e o `pop` depois.
    

#### B. Preservados pelo Chamado (Callee-Saved / Não-Voláteis)

Registradores: `RBX`, `RSP`, `RBP`, `R12` até `R15`.

- **A regra:** Se a sua função (como a `soma`) quiser usar um desses, **ela** é obrigada a dar um `push` no início e um `pop` no final para devolver o registrador do jeitinho que o encontrou.
    

---

### 4. Analisando seu código com a "Etiqueta Assembly"

Olha o que acontece na sua procedure `soma`:

Snippet de código

```
soma:
    add rax, rbx   ; rax é "Caller-Saved", pode mudar. Tudo ok!
    ret
```

Se você estivesse usando a regra de etiqueta rigorosa, e a sua `soma` precisasse usar o `r12` (que é preservado pelo chamado), ela teria que ser assim:

Snippet de código

```
soma_complexa:
    push r12       ; Salva o valor original do r12
    mov r12, 50    ; Agora posso usar o r12 à vontade
    add rax, r12
    pop r12        ; Devolve o r12 original para quem chamou
    ret
```

### Resumo para seu treino:

Como você é o dono de todo o código (`_start`, `soma` e `exit`), você sabe exatamente o que cada função faz. Você decidiu que o `rax` seria o meio de transporte do resultado. Portanto, **não salvar é a escolha correta**.

**Onde você começará a salvar?**

Quando você começar a usar o `printf` ou `scanf`. Essas funções da `libc` destroem os valores de quase todos os registradores "Caller-Saved" (`rax`, `rcx`, `rdx`, etc). Se você tiver algo importante ali, terá que dar um `push` antes do `call printf`.