#Difficult : Easy
#Tools : Wireshark, Tshark, PowerShell
#### Info
![[Pasted image 20250320132424.png]]
Recebemos dois arquivos para fazer nossas investigações, um arquivo de alerta em formato json e um arquivo de tráfego de rede.

Pelo aleta ser em json podemos jogar seu conteúdo dentro de um objeto no PowerShell, e trabalhar nesse objeto.
```
$Alerts = ConvertFrom-Json -InputObject $(Get-Content -Path .\meerkat-alerts.json)

$Alerts
```
![[Pasted image 20250320132736.png]]

Como esses alertas tem sempre **``172.31.6.44``** como IP de destino, então assumo que seja o servidor atacado.
***
#### 1 - We believe our Business Management Platform server has been compromised. Please can you confirm the name of the application running?

Ao olhar o tráfego sempre confiro primeiro as estatísticas para ter uma noção maior das conversas.
![[Pasted image 20250320133624.png]]

 E ver quais endereços tiveram mais carga de envio. Podemos ver algumas conversas, mas uma interessante é a do IP **`138.199.59.221`** que aparece 3 vezes.
![[Pasted image 20250320133913.png]]

Podemos então seguir o fluxo dessa conversa.
![[Pasted image 20250320134134.png]]

Várias tentativas de login foram executadas : 
![[Pasted image 20250320134428.png]]![[Pasted image 20250320134442.png]]

Até que o envio de um arquivo de nome ``rce_api_extensions.zip`` me chamou a atenção.
![[Pasted image 20250320134655.png]]

Com se trata de um RCE(remote code execution) podemos pesquisar nos alertas.
```
$Alerts.alert.signature | Select-String rce
```
![[Pasted image 20250320135022.png]]
 Até aqui já encontramos o nome do Aplicativo, mas podemos confirmar pesquisando o número da CVE.

![[Pasted image 20250320135247.png]]

**Answer** : BonitaSoft
***
#### 2 - We believe the attacker may have used a subset of the brute forcing attack category - what is the name of the attack carried out?

A técnica de Brute Force detém 4 sub-tecnicas : 
- **Password Guessing** : Seria a tentativa de adivinhar a senha, podendo ou não usar uma lista de senhas comuns ou mecanismos repetitivos.
- **Password Cracking** : Aqui o adversário tenta quebrar um hash que já foi obtido.
- **Password Spraying** : Aqui o atacante tenta uma combinação de vários usuários com uma mesma senha.
- **Credential Stuffing** : Aqui o atacante tenta usuários e senhas obtidos através de bancos de dados de credenciais vazadas, com a expectativa de algum usuário reutilizar sua senha.

Se olharmos a tentativa de login, podemos fazer um filtro para localizar todas as tentativas.
![[Pasted image 20250320134428.png]]

```
http.request.uri == "/bonita/loginservice" && http.user_agent == "python-requests/2.28.1"
```
Ao olharmos os pacotes, podemos ver uma variedade de usuários, que não estão utilizando senhas comuns, ou com a mesma senha, o que indicaria um **Credential Stuffing**.]

**Answer** : Credential Stuffing
***
#### 3 - Does the vulnerability exploited have a CVE assigned - and if so, which one?

Já encontramos essa resposta pesquisando no arquivo de alertas.
```
$Alerts.alert.signature | Select-String rce
```
![[Pasted image 20250320135022.png]]

**Answer** : CVE-2022-25237
***
#### Which string was appended to the API URL path to bypass the authorization filter by the attacker's exploit?

No site onde o exploit é explicado : https://rhinosecuritylabs.com/application-security/cve-2022-25237-bonitasoft-authorization-bypass/, eles falam qual o parâmetro e valor utilizados para bypass.
![[Pasted image 20250320154404.png]]

Também podemos observar esse parâmetro na requisição em que o atacante envia a payload.

![[Pasted image 20250320154703.png]]

**Answer** : i18ntranslation
***
#### 5 - How many combinations of usernames and passwords were used in the credential stuffing attack?

Aqui podemos utilizar o ``Tshark``, pois com a sua saída no terminal podemos filtrar com o PowerShell.
```
tshark -Y '(http.request.uri == "/bonita/loginservice") && (http.user_agent == "python-requests/2.28.1")' -r meerkat.pcap -T fields -e text | Sort-Object -Unique | Measure-Object
```
![[Pasted image 20250320172438.png]]

O resultado foi 57, mas podemos descartar a tentativa de ``install:isntall``, pois é a credencial padrão do programa e é testado por padrão no exploit da Rhino em  : https://github.com/RhinoSecurityLabs/CVEs/blob/master/CVE-2022-25237/CVE-2022-25237.py.

**Answer** : 56
***
#### 6 - Which username and password combination was successful?

Aqui podemos seguir o fluxo TCP das tentativas de login até chegar na parte onde o exploit é enviado.
![[Pasted image 20250320173133.png]]
Essa é a última requisição, antes do payload ser enviado.

**Answer** : seb.broom\@forela.co.uk:g0vernm3nt
***
#### 7 - If any, which text sharing site did the attacker utilise?

Aqui podemos ver as requisições http em **Statistics > HTTP > Requests**. Notamos que foi usado o comando ``wget`` para baixar algum arquivo de ``pastes.io``
![[Pasted image 20250320173441.png]]

**Answer** : pastes.io
***
#### 8 - Please provide the filename of the public key used by the attacker to gain persistence on our host.

Aqui podemos acessar o site pelo qual o atacante baixou o arquivo para persistência : https://pastes.io/raw/bx5gcr0et8
![[Pasted image 20250320174908.png]]

Então também podemos acessar essa outra url que ele utilizou : https://pastes.io/raw/hffgra4unv
![[Pasted image 20250320175031.png]]
Com ele é uma chave RSA, este é o arquivo.

**Answer** : hffgra4unv
***
#### 9 - Can you confirm the file modified by the attacker to gain persistence?

Podemos simplesmente olhar o script baixado, e observar onde ele escreve.
![[Pasted image 20250320174908.png]]

**Answer** : /home/ubuntu/.ssh/authorized_keys
***
#### 10 - Can you confirm the MITRE technique ID of this type of persistence mechanism?

Aqui é mais uma questão de pesquisa. Podemos acessar o site : https://attack.mitre.org/tactics/enterprise/ e escolher Persistence.
![[Pasted image 20250320175540.png]]

E então acharmos a que se assemelha ao cenário. Nesse caso ``.004 - SSH`` Authorized Keys

**Answer** : T1098.004
***

![[Pasted image 20250320175825.png]]