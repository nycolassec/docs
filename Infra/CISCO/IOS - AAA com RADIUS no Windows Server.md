## Info
Nosso objetivo será configurar o `Switch` para autenticar usando o serviço  de `Radius` configurado no `Windows Server`.

>[!warning] Configuração
>Não será feita uma configuração extensiva, mas sim uma base funcional para futuros incrementos.


![[Pasted image 20250430154446.png]]
- **`Windows Server`**: 10.0.10.1
- **`Switch CISCO`**: 10.0.10.15
***
## Windows Server
Teremos os usuários `atylas` e `davi` que não estarão em nenhum grupo administrativo, e os usuários `nycolas` e `marcos` que estarão no grupo `CiscoAdmin` o qual poderá logar no `Switch` com máximo privilégio(`15`).
### Instalação
Em **`Server Manager > Manage > Add Role and Features > Server Roles`** selecionamos `Network Policy and Access Services`.
![[Pasted image 20250430155201.png]]

Para configurar vamos em  **`Server Manager > Tools > Network Policy Manager`**
![[Pasted image 20250430155917.png]]
### Adicionando um cliente
Para adicionar um cliente vamos em **`Radius Clients and Server > RADIUS Clients`** e selecionamos `New`.
![[Pasted image 20250430162041.png]]

Em **`New RADIUS Client > Settings`** definimos um `Friendly Name` o `IP address` do cliente e a `Shared secret`.
![[Pasted image 20250430162602.png]]
A `Shared secret` será usada como `key` nas configurações do `Switch`.

Em **`New RADIUS Client > Advanced`** definimos o `Vendor Name` para `Cisco` e confirmamos em `OK`.
![[Pasted image 20250430162855.png]]
### Adicionando uma política
Podemos adicionar políticas em **`Policies > Network Policies`** e selecionamos `New`
![[Pasted image 20250430163050.png]]

Primeiro definimos um nome para a nossa política,
![[Pasted image 20250430163305.png]]

Em `Epecify Conditions` selecionamos `Add` vamos em `User Groups` e escolher o grupo `CiscoAdmin`. Podemos restringir po `IP`, `Day and Time`, `Authentication Type` e mais.
![[Pasted image 20250430163645.png]]

Em `Apecify Access Permission` selecionamos `Access granted`, pois queremos liberar o acesso, se quiséssemos bloquear iriamos em `Access Denied`.
![[Pasted image 20250430164058.png]]

Em `Configure Authentication Methods`, podemos selecionar `Unencrypted authentication (PAP, SPAP)`.
![[Pasted image 20250430164301.png]]

Em `Configure Constraints` podemos deixar como padrão.
![[Pasted image 20250430164352.png]]

Em `Configure Settings` vamos em `Standard` e removemos o `Framed-Protocol` e editamos o `Service-Type` para `Login`.
![[Pasted image 20250430165559.png]]

Ainda em `Configure Settings` vamos em `Vendor Specific` e selecionamos `Add...` e escolhemos `RADIUS Standard` e selecionamos `Add...`
![[Pasted image 20250430165856.png]]

Em `Attribute Information` Selecionamos `Add...` novamente, em `Vendor-Specific Attribute Information` escolhemos `Cisco` e `Yes . It conforms` e então vamos em `Configure Attribute...`
![[Pasted image 20250430170916.png]]

Em `Configure VSA (RFC Compilant)` definimos `Vendor-assigned attribute number: 1`, `Attribute value: String` e `Atribute value: shell:priv-lvl=15` e confirmamos com `OK`
![[Pasted image 20250430171207.png]]
Após adicionarmos esse atributo, podemos avançar com `Next` e finalizar com `Finish`.

### Firewall
Temos que liberar as portas `1812` e `1813` para o `Authentication` e `Accounting` do `Radius`.
```powershell
New-NetFirewallRule -DisplayName "Allow_RADIUS_1812_UDP_IN" -Direction Inbound -Protocol UDP -LocalPort 1812 -Action Allow

New-NetFirewallRule -DisplayName "Allow_RADIUS_1813_UDP_IN" -Direction Inbound -Protocol UDP -LocalPort 1813 -Action Allow
```

***
## Switch CISCO
Primeiro fazemos algumas configurações iniciais, como um usuário local como backup.
```ios
SW(config)# username local_user algorithm-type scrypt secret cisco
```

Primeiro criamos um `new model AAA` e definimos o nome, `IP address`, `key` e as portas utilizadas do servidor `Radius`.
```ios
SW(config)# aaa new-model

SW(config)# radius server WIN-SERVER
SW(config-radius-server)# address ipv4 10.0.10.1 auth-port 1812 acct-port 1813
SW(config-radius-server)# key cisco
SW(config-radius-server)# exit
```
- **`auth-port`**: Define a porta para `Authentication`
- **`acct-port`**: Define a porta para `Accounting`
- **`key`**: É a `Shared Secret` que definimos no `Windows Server`

Agora criamos um grupo de servidores e adicionamos o `WIN-SERVER` nele.
```ios
SW(config)# aaa group server radius RADIUS-GROUP
SW(config-sg-radius)# server name WIN-SERVER
SW(config-sg-radius)# exit
```

Agora iremos definir o grupo que irá fazer a autenticação e autorização.
```ios
SW(config)# aaa authentication login default group RADIUS-GROUP local
SW(config)# aaa authorization exec default group RADIUS-GROUP local
SW(config)# aaa accounting exec default start-stop group RADIUS-GROUP
```
- **`local`**: Também colocamos `local` em `authorization` e `authentication` pois queremos ter a autenticação local como backup.

Agora definimos a autenticação `AAA` nas linhas `console` e `vty`.
```ios
SW(config)#line console 0
SW(config-line)#login authentication default
SW(config-line)#exit

SW(config)#line vty 0 4
SW(config-line)#login authentication default
SW(config-line)#exit

SW(config)# do wr
```

Podemos adicionar também algumas configurações que não são obrigatórias, mas nos ajudarão se houver algum problema. Eles adicionam mais informações na comunicação do `Switch` e o servidor `Radius`.
```ios
SW(config)# radius-server attribute 6 on-for-login-auth
SW(config)# radius-server attribute 8 include-in-access-req
SW(config)# radius-server vsa send authentication
```
- **`attribute 6 on-for-login-auth`**: Indica o tipo de serviço solicitado, os valores comuns são:
	- **1 (Login)**: Para acesso administrativo (EXEC)
	- **2 (Framed)**: Para acesso de usuário (PPP, VPN)
	- **5 (Administrative)**: Para acesso privilegiado
	- **7 (Authenticate Only)**: Apenas para verificação de credenciais
- **`attribute 8 include-in-access-req`**: Inclui o atributo `Framed-IP-Address` durante a solicitação de autenticação.
- **`vsa send authentication`**: Habilita o envio de `VSs` na autenticação.
	- **VSAs (Vendor-Specific Attributes)** são atributos personalizados que permitem a fabricantes (como a Cisco) adicionar informações **específicas de seu equipamento** nas mensagens RADIUS.