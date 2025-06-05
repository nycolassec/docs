![[Pasted image 20250604204839.png]]

Difficult : #Easy OS : #Windows #Info : As is common in real life Windows pentests, you will start the Fluffy box with credentials for the following account: j.fleischman / J0elTHEM4n1990!
***
# Port Scanning
```sh
nmap 10.10.11.69 -Pn -sS -sC -sV -A -oN scan_out
```
![[Pasted image 20250605072955.png]]
Aqui já temos várias informações importantes como o domínio, nome do controlador de domínio e alguns serviços que estão rodando na máquina como o `LDAP/389`,`LDAPs/636`, `SMB/445` e o `WINRM/5985`.
***
# SSL enum
Como ele está rodando o `LDAPs` podemos ver o seu certificado e tentar obter mais informações.
```sh
openssl s_client -showcerts -connect 10.10.11.69:636
```
![[Pasted image 20250605073710.png]]

Temos agora também o nome da `CA` do domínio, podemos adiciona-los ao nosso arquivo de host.
![[Pasted image 20250605074205.png]]
***
# LDAPs enum
Podemos usar as credenciais que recebemos para fazer uma busca no domínio.
```sh
ldapsearch -x -H ldap://10.10.11.69:389 -D 'j.fleischman' -w 'J0elTHEM4n1990!' -b 'dc=fluffy,dc=htb' '(objectClass=person)' cn MemberOf userPrincipanName
```
![[Pasted image 20250605075155.png]]

Identificamos outros usuários e um grupo não padrão, o `Service Account Managers`.
***
# SMB enum
Temos somente um usuário, podemos tentar enumerar o `SMB` com ela.
![[Pasted image 20250605080407.png]]

Temos acesso a um compartilhamento não padrão, o `IT`. Vamos nos conectar ver seu conteúdo. Temos vários arquivos, todos são executáveis de um programa menos o `Upgrade_Notice.pdf`.
![[Pasted image 20250605080733.png]]

```sh
open Upgrade_Notice.pdf
```
Nesse pdf tem algumas vulnerabilidades descritas, a `CVE-2025-24071` diz respeito a construirmos um arquivo `zip/rar` com um `SMB path` malicioso, podendo então performar um `spoofing`.
***
# User
podemos então começar o nosso ataque.
### CVE-2025-24071


















