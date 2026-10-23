Sem considerar os ponteiros a linguagem C tem 4 tipos de dados.

###### char
Armazena valores inteiros e caracteres. Ocupa 1 byte na memória portanto pode armazenar valores de -128 a 127; Utilizado para armazenar caracteres (conforme a tabelas ASCII) e também pode guardar valores inteiros.
###### int
Armazena valores inteiros. Armazena 2 ou 4 bytes de memória, dependendo da máquina usada;
Int de 2 bytes pode guardar de -32768 a 32767, um int de 4 bytes pode guardar valores de -2147483648 a 2147483647.
###### float
Armazena números reais. Ocupa 4 bytes da memória; Armazenam valores numéricos com parte fracionária (ponto flutuante); Valores com 6 dígitos de precisão.
###### double
Armazena números reais com maior precisão. Ocupa 8 bytes de memória;

### Modificadores
Existem 4 modificadores;
- Unsigned - Determina que o tipo não terá sinal.
- Signed - Determina que o tipo terá sinal. Pode ser usado em algumas implementações onde char por padrão não tem sinal.
- Short - Usado principalmente para estabelecer um limite de 2 bytes para o tipo int;
- Long - Usado para estabelecer um limite de 4 bytes para o tipo int,  e também para aumentar a precisão do tipo double.

### Int implícito
Caso declaremos as variáveis assim:
```c
unsigned guarda_valor;
short guarda_valor;
long short guarda_valor;
unsigned long guarda_valor;
```
Todos serão considerados como inteiros;
### Formatos de leitura e escrita
As letras após o %, tem o objetivo de determinar o formato do dado que será lido ou escrito.
- %c - char : Para um único caractere
- %d ou %i - int : Para um inteiro em base decimal
- %o - int : Para inteiro em base decimal
- %x - int : Para um inteiro em base hexadecimal (letras minúsculas)
- %X - int : Para um inteiro em base hexadecimal (letras maiúsculas)
- %f - float : Para números reais (o mesmo para double)
- %e - float : Para notação científica com "e" minúsculo (o mesmo para double)
- %E - float : Para notação científica com "E" maiúsculo (o mesmo para double )
### Formato de leitura e escrita nos modificadores
- %hd - short int : short inteiro em base decimal
- %ld - long int : long inteiro em base decimal
- %hu - unsigned short : short inteiro positivo
- %u - unsigned int : inteiro positivo
- %lu - unsigned long : long inteiro positivo












