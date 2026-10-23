# Desafios de Assembly x86-64 para Iniciantes

Este documento contém 10 exercícios progressivos para praticar as bases de Assembly utilizando NASM e Linux.

---

## 1. O Somador de Status
Crie um programa que coloque o número `15` em `rax` e o número `27` em `rbx`. Some os dois e use o resultado como o **status de saída** do programa.
* **Dica:** O status de saída é o valor passado para `rdi` antes do syscall `exit` (60).
* **Como verificar:** Após rodar, use `echo $?` no terminal.

## 2. Troca Sem Truques
Coloque o valor `0xAAAA` em `rax` e `0xBBBB` em `rbx`. Troque os valores entre os dois registradores **sem usar** a instrução `xchg`.
* **Dica:** Utilize um terceiro registrador (como `rcx`) como armazenamento temporário.

## 3. O Teste do Bit (Par ou Ímpar)
Defina um número em `rax`. Use operações lógicas (`AND` ou `TEST`) para verificar se o número é par ou ímpar.
* **Regra:** Se for par, saia com status `0`. Se for ímpar, saia com status `1`.
* **Dica:** Números ímpares possuem o bit menos significativo (bit 0) sempre igual a 1.

## 4. Contador Regressivo
Crie um loop que comece com o valor `10` em um registrador e decremente o valor até chegar a `0`. Quando chegar a zero, o programa deve encerrar.
* **Dica:** Use as instruções `dec` e `jnz` (Jump if Not Zero).

## 5. O Quadrado Mágico (Procedures)
Crie um procedimento chamado `CalcularQuadrado`.
* Ele deve receber um número via `rdi`.
* Deve calcular o quadrado desse número ($n \times n$).
* O resultado deve ser retornado no registrador `rax`.
* **Dica:** Use a instrução de multiplicação `imul`.

## 6. O Maior de Dois (Lógica Condicional)
Coloque dois números diferentes em `rax` e `rbx`. Compare-os e mova apenas o maior valor para `rdi` antes de encerrar.
* **Dica:** Use `cmp` seguido de pulos **condicionais** como `jg` (Jump if Greater) ou `jl` (Jump if Less).

## 7. Somatório de 1 a N
Peça para o usuário digitar um número pequeno (ex: 5) via `scanf`. O programa deve somar todos os números de 1 até o número digitado ($1+2+3+4+5 = 15$).
* **Dica:** Use um registrador como acumulador para a soma e outro como contador para o loop.

## 8. Multiplicação Hacker (Bitwise)
Multiplique o valor contido em `rax` por 8, mas **proibido usar a instrução `imul`**.
* **Dica:** Deslocar bits para a esquerda (`shl`) dobra o valor. Para multiplicar por 8 ($2^3$), você precisa deslocar 3 vezes.

## 9. O Porteiro (Comparação)
Defina um número secreto na seção `.data`. Peça para o usuário digitar um número via `scanf`.
* Se os números forem iguais, imprima "Acesso Liberado" usando `printf`.
* Se forem diferentes, imprima "Acesso Negado".
* **Dica:** Lembre-se do alinhamento de 16 bytes da pilha antes de chamar funções da `libc`.

## 10. Mini Conversor de Temperatura
Crie um programa que receba uma temperatura em Celsius e converta para Fahrenheit usando a fórmula: $F = (C \times 9 / 5) + 32$.
* Receba o input via `scanf` e mostre o resultado via `printf`.
* **Dica:** Realize as operações na ordem: Multiplicação -> Divisão (`idiv`) -> Adição.