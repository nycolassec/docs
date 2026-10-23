## Header
Uma das formas mais simples é a corrupção do cabeçalho, se for esse o caso, podemos verificar em uma [tabela online](https://en.wikipedia.org/wiki/List_of_file_signatures) das **file signatures** de cada tipo de arquivo.
![[Pasted image 20250627163124.png]]
## Zip
Em arquivos `zip` primeiro tentamos ver as informações do arquivo.
```sh
$ zipdetails secret.zip
#
# FATAL: Offset 0xD0: 'Local Header Offset' field in 'Central Directory Header' is invalid
#        value @ 0x0 should decode to signature for 'Local File Header' (0x04034B50). Got 0x04030050 . 
#        Try running with --walk' or '--scan' options . 
#
```
 
 Aqui ele diz que o cabeçalho do arquivo é inválido. Podemos verificar na  [tabela online](https://en.wikipedia.org/wiki/List_of_file_signatures) das **file signatures**, a **file signature** de um zip comum começa com `50 4b 03 04`.
![[Pasted image 20250627164303.png]]

Agora podemos ver qual é a assinatura no arquivo.
```sh
$ hexeditor secret.zip
```
![[Pasted image 20250627165104.png]]

Podemos fazer a modificação e tentar um abrir novamente.
![[Pasted image 20250627165203.png]]

```sh
$ zipdetails secret.zip
0000 LOCAL HEADER #1       04034B50 (67324752)
0004 Extract Zip Spec      14 (20) '2.0'
0005 Extract OS            00 (0) 'MS-DOS'
0006 General Purpose Flag  0009 (9)
     [Bit  0]              1 'Encryption'
     [Bit  3]              1 'Streamed'
0008 Compression Method    0063 (99) 'AES Encryption'
000A Modification Time     58515720 (1481725728) 'Sat Feb 17 07:57:00 2024'
```
Agora temos um zip funcional, e podemos continuar com a análise.

















