#Difficult : Easy
#OS : Linux
***
## Enumeração de portas
Por padrão começamos a fazer a enumeração das portas abertas, para ter uma noção dos serviços rodando.

```
nmap 10.10.11.55 -Pn -sC -sV -A -oN nmap_output
```
![[Pasted image 20250327195253.png]]
Foi identificado duas portas.
`80` - HTTP | `22` - SSH

Ao acessa-lo no navegador somo redirecionados para `titanic.htb`, então vamos adiciona-lo em nosso `/etc/hosts`.
```sh
echo "10.0.11.15        titanic.htb" >> /etc/hosts
```

Ao acessar novamente, somos apresentados a um site.
![[Pasted image 20250327195924.png]]
***
## Enumeração de sub domínio
Embora tenhamos um site para explorar, podemos verificar se temos mais algum site disponível.
```sh
ffuf -u http://titanic.htb -H "Host: FUZZ.titanic.htb" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -fw 20
```
![[Pasted image 20250327200125.png]]

Descobrimos um subdomínio `dev.titanic.htb`. Vamos adiciona-lo ao arquivo de hosts.
```sh
10.0.11.15        titanic.htb dev.titanic.htb
```

Vamos acessa-lo para ter uma noção do que temos.
![[Pasted image 20250327200432.png]]
Temos acesso ao ``Gitea``, um serviço de ``git`` sel-hosted. Vamos deixa-lo de lado por enquanto.
****
## LFI (Local File Inclusion)
Em `titanic.htb` podemos criar e baixar um ticket clicando em `Book Your Trip`.
![[Pasted image 20250327200903.png]]

Recebemos um arquivo ``JSON`` com as informações que inserimos.
![[Pasted image 20250327200930.png]]
![[Pasted image 20250327201656.png]]

Vamos interceptar as requisições. A primeira requisição manda nossos dados. 
![[Pasted image 20250327202226.png]]

Após a primeira requisição um requisição é enviada requisitando o arquivo.
![[Pasted image 20250327202424.png]]
O parâmetro `ticket` tem como valor um arquivo, então está sujeito a ``LFI``.

Podemos fazer um teste com o `curl`.
```sh
curl -X GET "http://titanic.htb/download?ticket=../../../../../../../etc/passwd"
```
![[Pasted image 20250327202914.png]]

Vendo a localização do `shell` e da `home` dos usuários, chego a conclusão que os únicos  usuários possíveis são : `root` | `developer`.
***
## Cracking Gitea HASH
Embora tenha um ``LFI``, não achei nenhum arquivo interessante por enquanto. Podemos voltar ao `Gitea` e ver o que temos indo em ``Explore``.
![[Pasted image 20250327203615.png]]

Temos 




