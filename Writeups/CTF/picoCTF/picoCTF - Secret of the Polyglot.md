#Category : Forensics #Difficult : Easy
***
#### Properties
Recebemos um `pdf` para continuar com nossas investigações.
![[Pasted image 20250521134135.png]]
Aqui vemos que esse `PDF` é reconhecido como um arquivo `PNG` pelo comando `file`.

Podemos então podemos começar abrindo o arquivo como um `PDF`.
```sh
atril ./flag2of2-final.pdf
```
![[Pasted image 20250521134721.png]]
Temos a primeira parte da `flag`.

E agora podemos abrir com um `JPG`.
```sh
ristretto ./flag2of2-final.pdf
```
![[Pasted image 20250521134846.png]]
E temos então a primeira parte da flag.

***
![[Pasted image 20250521133821.png]]