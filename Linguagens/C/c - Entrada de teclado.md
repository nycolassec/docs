Para fazermos uma entrada de teclado podemos usar a função scanf, então passamos o tipo que será digitado, e em qual endereço de memória ele deve ser armazenado com o operador &.

Para strings não passamos o operador &, mas somente o arrray de char. Quando pressionamos o Enter, ele é direcionado para o próximo fluxo de entrada. Então antes de criarmos o próximo fluxo de entrada, devemos limpar o buffer com o fflush.

```c
#include <stdlib.h>
#include <stdio.h>

int main(void){
	char name[15];
	printf("Qual o seu nome: ");
	scanf("%s", name);
	printf("Bom dia %s\n", name);
	fflush(stdin);

	char letter;
	printf("Escolha uma letra: ");
	scanf("%c", &letter);
	printf("Letra %c escolhida\n", letter);
	fflush(stdin);

	int age;
	printf("Qual é a sua idade: ");
	scanf("%d", &age);
	printf("Você já  tem %d anos ? Tá veio já.", age);
	fflush(stdin);

	return 0;
}
```