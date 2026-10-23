Embora os termos sejam frequentemente usados como sinônimos em linguagens de alto nível (como Python ou C), no nível do **Assembly**, existe uma diferença conceitual importante sobre como elas lidam com dados.

Aqui está a distinção clara entre os dois:

---

## 1. Procedures (Procedimentos)

Um procedimento é focado na **organização**. Ele é um bloco de código que executa uma tarefa específica e repetitiva, mas que geralmente não precisa de informações externas variáveis para funcionar.

- **Objetivo:** Refatorar o código para deixá-lo mais limpo.
    
- **Comunicação:** Geralmente não recebe argumentos complexos nem retorna um resultado calculado. Ele apenas "faz o trabalho dele".
    
- **Exemplo:** Um procedimento que limpa todos os registradores ou um que imprime um cabeçalho fixo no terminal.
    
- **Fluxo:** Você dá um `call`, ele executa e você dá um `ret`.
    

## 2. Functions (Funções)

Uma função é focada no **processamento de dados**. Ela é projetada para receber valores (argumentos), realizar um cálculo ou transformação e devolver um resultado para quem a chamou.

- **Objetivo:** Processar informações e retornar um valor.
    
- **Comunicação:** Requer uma "Convenção de Chamada" (Calling Convention). Isso significa que existem regras sobre quais registradores usar para passar os dados de entrada e onde colocar o resultado.
    
- **Exemplo:** Uma função que recebe dois números e retorna a soma, ou uma que recebe o endereço de uma string e retorna o seu tamanho.
    
- **Fluxo:** O chamador coloca valores nos registradores (ex: `rdi`, `rsi`), dá um `call`, a função processa e coloca a resposta em um registrador padrão (geralmente o `rax`).
    

---

## Comparação Direta

|**Característica**|**Procedure**|**Function**|
|---|---|---|
|**Foco Principal**|Organização e legibilidade.|Processamento e lógica.|
|**Argumentos**|Raramente usa ou usa valores fixos.|Recebe argumentos dinâmicos.|
|**Retorno**|Apenas volta para o fluxo principal.|Retorna um valor calculado (geralmente em `rax`).|
|**Complexidade**|Mais simples de implementar.|Exige seguir protocolos de registradores.|

---

## Exemplo Visual em Código

Veja a diferença de "mentalidade" no código:

Snippet de código

```
; --- ISSO É UM PROCEDIMENTO ---
; Apenas limpa os registradores principais
limpar_tudo:
    xor rax, rax
    xor rbx, rbx
    ret

; --- ISSO É UMA FUNÇÃO ---
; Recebe um número em RDI, dobra o valor e coloca em RAX
dobrar_valor:
    mov rax, rdi    ; Pega o argumento que veio em RDI
    add rax, rax    ; Soma ele com ele mesmo
    ret             ; O resultado está pronto em RAX
```

No caso da **função**, quem chamou (`_start`, por exemplo) precisa saber que deve colocar o número em `rdi` antes de chamar e que deve buscar a resposta em `rax` depois que ela terminar. No **procedimento**, você apenas chama e ele faz o que tem que fazer.

**Em resumo:** Todo mundo é uma sub-rotina. Se ela apenas organiza o código, chamamos de **procedimento**. Se ela recebe dados e devolve um resultado, chamamos de **função**.