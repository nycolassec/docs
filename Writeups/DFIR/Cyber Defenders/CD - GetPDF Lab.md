#Difficult : Medium
#Category : Malware Analysis
#Info : Para termos um maior entendimento da estrutura de um `PDF` podemos ler o [artigo](https://www.infosecinstitute.com/resources/hacking/pdf-file-format-basic-structure/) da `infosec` sobre o assunto
***
#### 1 - How many URL path(s) are involved in this incident?
Aqui podemos filtrar pelas requisições `HTTP` com o método `GET`, assim saberemos quais `URLs` fora acessadas.
![[Pasted image 20250422102436.png]]
Temos `7` requisições, mas `/favicon.ico` se repete, então são `6`.

**Answer : `6`**
***
#### 2 - What is the URL which contains the JS code?
Filtrei por requisições que tinham o `content_type: applications/javascript`, como não recebi nenhum pacote filtrei por arquivos que continha a tag `script`.
![[Pasted image 20250422105205.png]]

Então decidi seguir seu `Stream HTTP`, até que encontrei o código `javascript` ofuscado. Esse código foi requisitado de `forensic_challenge/` do host `/blog.honeynet.org.my`.
![[Pasted image 20250422105518.png]]

Podemos então exporta-lo dos objetos `HTTP` para analisarmos seu conteúdo.
![[Pasted image 20250422110008.png]]

**Answer  :`http://blog.honeynet.org.my/forensic_challenge/`**
***
#### 3 - What is the URL hidden in the JS code?
Podemos editar o arquivo que baixamos e deixar apenas o código `javascript`.
![[Pasted image 20250422110930.png]]

Podemos pegar esse código ofuscado e enviar para alguma ferramenta como o [deobfuscate.io](https://deobfuscate.io) E trocar o conteúdo do arquivo pelo código desofuscado.
![[Pasted image 20250422111641.png]]

Podemos modificar o script para conseguir fazer ele mostrar qual é a `url` desofuscada. Podemos comentar `var DepanNegw = window;` e `GaDemee = DepanNegw[GaDemee];` pois não estamos no navegador, e trocar `GaDemee(ZeJexn);` por `console.log(ZeJexn);`, pois queremos mostrar e não executar.
![[Pasted image 20250422112459.png]]

Podemos então executar o arquivo cm o `nodejs`.
![[Pasted image 20250422112621.png]]

**Answer : `http://blog.honeynet.org.my/forensic_challenge/getpdf.php`**
***
#### 4 - What is the MD5 hash of the PDF file contained in the packet?
Nos objetos `HTTP` podemos exportar o arquivo `PDF` para a análise.
![[Pasted image 20250422113131.png]]

Então podemos tirar seu hash `MD5`.
![[Pasted image 20250422113317.png]]

**Answer  :`659cf4c6baa87b082227540047538c2a`**
***
#### 5 - How many object(s) are contained inside the PDF file?
Podemos usar o `pdfid` para obtermos informações sobre o `PDF`.
![[Pasted image 20250422114454.png]]

**Answer : `19`**
***
#### 6 - How many filtering schemes are used for the object streams?
Os filtros normalmente são utilizados para comprimir dados no documento, mas também podem ser utilizados por atacantes para adicionar complexidade quando formas analisa-lo. Podemos usar o `pdf-parser` para identificar esses filtros. Ele irá retornar o filtro e os dados, então podemos usar o `grep`

















