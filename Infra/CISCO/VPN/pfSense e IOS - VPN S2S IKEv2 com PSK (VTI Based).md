## Info
![[Pasted image 20250508110407.png]]

As interfaces virtuais serão endereçadas na rede `10.0.30.0/24`. Não há a necessidade de pré-definir as rotas para as redes internas, pois elas serão definidas a partir do túnel. Tanto o `SITE-A` quanto o `SITE-B` devem conseguir alcançar a interface externa do seu `peer`.
***
## SITE - A : PFsense
>[!warning] Configurações
> As configurações devem ser as mesmas entre o `SITE-A` e `SITE-B`
#### Phase 1
==VPN / IPsec / Tunnels==
![[Pasted image 20250428171723.png]]
Selecionamos `Add P1`.

**General Information**
![[Pasted image 20250508131014.png]]

**IKE Endpoint Configuration**
![[Pasted image 20250508131110.png]]

**Phase 1 Proposal (Authentication)**
![[Pasted image 20250508131321.png]]

**Phase 1 Proposal (Encryption Algorithm)**
![[Pasted image 20250508131335.png]]

**Advanced Options** | **Expiration and Replacement**
Podem permanecer como padrão. Então clicamos em `Save`
![[Pasted image 20250508131751.png]]
#### Phase 2
==VPN / IPsec / Tunnels==
Selecionamos `Show Phase 2 Entries` e depois `Add P2`.
![[Pasted image 20250508132052.png]]

**General Information**
![[Pasted image 20250508132141.png]]
**`Mode : Routed (VTI)`** - Indica que vamos rotear através de uma interface virtual, a qual será criada automaticamente.

**Networks**
![[Pasted image 20250508132346.png]]
Aqui colocamos o `IP` das interface virtuais do `SITE-A` e `SITE-B`.

**Phase 2 Proposal (SA/Key Exchange)**
![[Pasted image 20250508132612.png]]
Aqui são as mesmas propriedades no `transform-set` do `router`.

**Expiration and Replacement** | **Keep Alive**
Podemos deixar com os valores padrão. Então selecionamos `Save`.
![[Pasted image 20250508132912.png]]
#### VTI
==Interfaces / Interface Assignments==
Nossa interface foi criada, mas devemos adiciona-la selecionando `Add` e `Save`. Nesse caso, ela será direcionada para a interface `OPT1`.
![[Pasted image 20250508133221.png]]

==Interfaces / Interface Assignments / OPT1==
Apenas marcamos `Enable`. E selecionar `Save`.
![[Pasted image 20250508133630.png]]
#### Rules
Agora devemos configurar as nossas regras para direcionar o tráfego para a interface `IPSEC`

==Firewall / Rules / IPsec==
Então selecionamos `Add`.
![[Pasted image 20250508133921.png]]

**Edit Firewall Rule**
Então criamos uma regra que permita o `ICMP` das redes internas.
![[Pasted image 20250508134256.png]]

**Source** | **Destination** | **Extra Options**
![[Pasted image 20250508134535.png]]
Então selecionamos `Save`
#### Routes
Agora temos que indicar para o `PFsense` qual o tráfego que deve ser encaminhado para a `VPN`.

==System / Routing / Static Routes==
Então selecionamos `Add`
![[Pasted image 20250508145027.png]]

**Edit Route Entry**
![[Pasted image 20250508145139.png]]
`Destination network` : A rede interna que queremos alcançar
`Gateway` : A interface virtual que criamos para a `VPN`
***
## SITE-B : Cisco Router
>[!warning] Configurações
> As configurações devem ser as mesmas entre o `SITE-A` e `SITE-B`
#### Configuração ``IKEv2`` (**Phase 1**)
```
crypto ikev2 proposal IKEv2-PROPOSAL
	encryption aes-cbc-256
	integrity sha512
	group 19
	exit

crypto ikev2 policy IKEv2-POLICY
	proposal IKEv2-PROPOSAL
	exit

crypto ikev2 keyring VPN-KEYRING
	peer SITE-A
		address 172.16.10.1
		pre-shared-key SeletivaNacional
		exit
	exit

crypto ikev2 profile IKEv2-PROFILE
	match identity remote address 172.16.10.1 255.255.255.255
	authentication remote pre-share
	authentication local pre-share
	keyring local VPN-KEYRING
	exit
```
#### Configuração `IPSec` (**Phase 2**)
```
crypto ipsec transform-set ESP-TRANSFORM esp-aes 256 esp-sha512-hmac
	mode tunnel
	exit

crypto ipsec profile IPSEC-PROFILE
	set transform-set ESP-TRANSFORM
	set ikev2-profile IKEv2-PROFILE
	exit
```

Agora configuramos o túnel virtual
```
interface tunnel 10
	ip address 10.0.30.2 255.255.255.252
	tunnel source ethernet 0/0
	tunnel destination 172.16.10.1
	tunnel mode ipsec ipv4
	tunnel protection ipsec profile IPSEC-PROFILE
	exit
```

Rota para a rede remota interna.
```
ip route 10.0.10.0 255.255.255.0 Tunnel 10
```
***
## Resultado
Ao final, deve ser visível o tráfego pelo protocolo `ESP` entre as redes, ao utilizar o comando `PING` para alguma das redes internas.
![[Pasted image 20250508150833.png]]