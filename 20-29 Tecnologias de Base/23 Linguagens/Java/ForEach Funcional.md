```java
package lambda;

import java.util.Arrays;
import java.util.List;

public class ForEachFuncional {
    public static void main(String[] args) {
        List<String> nomes = Arrays.asList("Nycolas", "Davi", "Atylas");

        //Forma tradicional
        for(String nome: nomes){
            IO.print("\nTradicional - " + nome);
        }

        IO.println();

        // Lambda
        nomes.forEach(nome -> IO.print("\nLambda - " + nome));

        IO.println("\n");

        // Method Reference
        nomes.forEach(IO::println);
    }
}

```