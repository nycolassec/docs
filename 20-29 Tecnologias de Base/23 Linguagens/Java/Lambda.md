#### Lambda
Funções lambida sao funções que sao escritas se a definição de um nome e que podem nao conter um corpo desde de que retornem um valor em uma única sentença.
### Exemplo
Primeiro criamos uma interface funcional, que suporta apenas uma funcao abstrata, se quisermos colocar mais uma funcao podemos usar a palavra reservada `default`, o que indica que a funcao ja tera uma implementacao, ou usar a palavra reservada `static`, pois isso indicaria que a funcao nao podera ser instanciada.

```java
package lambda;

@FunctionalInterface
public interface Calculo {
	double executar(double a, double b);

	default String legal(){
		return "Legal";
	}

	static String ola(){
		return "Legal";
	}
}
```

Agora podemos instancia-la com uma funcao lambida.

```java
package lambda;

public class CalculoTest {
	public static void main(String[] args) {
		Calculo calculo = (a, b) -> a + b;
		calculo = (a, b) -> {return a + b;};
	}
}
```

### Interfaces funcionais
o java detem uma lista de interfaces funcionais que podem ser instanciadas por uma lambida : 
[Interfaces funcionais java](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html)
```java
// Functional Function nativa do Java
BinaryOperator<Integer> calculo2 = (a ,b) -> a + b ;
IO.println("Functional Function nativa do Java : "+ calculo2.apply(0, 5));
```

![[Captura de Tela 2025-10-22 às 15.28.44.png]]