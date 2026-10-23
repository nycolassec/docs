## 1. O Problema: "Código Espaguete"

No início do exemplo, o código faz quatro coisas misturadas em um único bloco:

1. Imprime uma mensagem.
    
2. Configura números iniciais.
    
3. Faz um cálculo (Fibonacci).
    
4. Sai do programa.
    

Se você quisesse imprimir a mensagem de novo, teria que copiar e colar as mesmas 5 linhas de código. Isso é ineficiente e difícil de ler.

## 2. A Solução: Procedimentos

Um procedimento (ou sub-rotina) é um bloco de código que você define uma vez e "chama" (call) quantas vezes quiser.

### As duas instruções mágicas: `CALL` e `RET`

Para que um procedimento funcione, o computador precisa saber **de onde ele veio** para poder **voltar para lá** quando terminar.

- **`CALL <nome>`**:
    
    - Ele salva o endereço da _próxima_ instrução na **Pilha (Stack)**.
        
    - Ele pula para o rótulo (label) do procedimento.
        
- **`RET` (Return)**:
    
    - Ele olha para o topo da Pilha, pega o endereço que foi salvo lá e pula de volta para ele.
        

---

## 3. Na Prática: Antes vs. Depois

### Como era:

O código executava linha por linha de cima para baixo. Se você quisesse repetir algo, teria que usar `jmp`, mas o `jmp` não sabe "voltar" sozinho para onde parou.

### Como ficou com Procedimentos:

Agora o início do seu código (`_start`) parece um "sumário" ou um menu:

Snippet de código

```
_start:
    call printMessage   ; Vai lá, imprime e volta
    call initFib        ; Vai lá, configura e volta
    call loopFib        ; Vai lá, calcula e volta
    call Exit           ; Vai embora e fecha o programa
```

Cada um desses blocos termina com um `ret`, exceto o `Exit` (porque se o programa fechou, não há para onde voltar).

---

## 4. Pontos Importantes para Lembrar

- **A Pilha (Stack) é a memória:** O `call` usa a memória (pilha) para guardar o caminho de volta. Se você mexer na pilha dentro do procedimento e não limpar ela, o `ret` vai tentar voltar para um lugar errado e o programa vai travar (crash).
    
- **Execução Linear:** Se você esquecer o `ret` no final de um procedimento, o processador não para! Ele vai continuar "escorregando" e executando as linhas de baixo (mesmo que elas pertençam a outro procedimento) até encontrar um `ret` ou um erro.
    
- **Refatoração:** Esse processo de transformar um código bagunçado em funções organizadas é chamado de _refatoração_.
    

**Resumindo:** Use `call` para ir e `ret` para voltar. Isso deixa seu código limpo, fácil de entender e profissional.