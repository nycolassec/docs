## Info
 
<br>
 
![](<../../images/Pasted image 20250428190631.png>)

Após as rotas estiverem configuradas, podemos começar a configuração da `VPN`.
***
***
## Site A

#### Phase 1
Em `VPN > IPsec`, selecionamos `Add P1`.
 
<br>
 
![](<../../images/Pasted image 20250428171723.png>)


**``Description:``** Aqui podemos colocar o propósito do túnel, é ideal que coloquemos o mesmo nome nos dois sites.

==IKE Endpoint Configuration==
**``Key Exchange version:``** Caso os dois hosts suportem, devemos usar sempre ``IKEv2``
**`Internet Protocol:`** Qual protocolo de internet será usado.
**`Interface:`** Qual interface será usada para contatar o site remoto.
**`Remote Gateway:`** O endereço público do gateway remoto do site de destino.

<br>

![](<../../images/Pasted image 20250428172338.png>)

==Phase 1 Proposal (Authentication)==
**`Authentication Method:`** Qual será o método de autenticação usado, pode ser `PSK` ou `Certificate`
**`My identifier`**: O que irá identificar esse host.
**`Peer Identifier:`** o que irá identificar o nosso par.
**`Pre-shared Key:`** Qual será a chave utilizada para autenticação, podemos colocar uma de nossa posse, ou gerar automaticamente. 

<br>
 
![](<../../images/Pasted image 20250428172443.png>)

==Phase 1 Proposal (Encryption Algorithm)==
Aqui podemos escolher as criptografias que irão ser aplicadas ao tráfego.
 
<br>
 
![](<../../images/Pasted image 20250428173001.png>)

==Expiration and Replacement==
**`Life Time:`** Por quanto tempo a Associação de Segurança (SA) será válida até que seja necessário renovar as chaves. O padrão de 28800 está bom para o exemplo.
 
<br>
 
![](<../../images/Pasted image 20250428173411.png>)

>Times - 
> O demais campos serão calculados automaticamente.

==Advanced Options==
**`Child SA Close Action:`** Devemos definir para `Restart/Reconnect`, para que as entradas da fase 2 se reconectem, caso sejam desconectadas.
**`Dead Peer Detection:`** Verifica se o `peer` ainda está ativo, enviando mensagens informacionais. Podemos marcar e deixar padrão.
 
<br>
 
![](<../../images/Pasted image 20250428174449.png>)

***
#### Phase 2
Agora vamos em `VPN > IPsec` clicar em `Show Phase e Entries` e depois `Add P2`
 
<br>
 
![](<../../images/Pasted image 20250428174813.png>)

==General Information | Networks==
**`Description:`** Uma descrição das redes envolvidas na fase 2.
**`Mode:`** Qual será o modo do túnel, nesse caso IPv4
**`NAT/BINAT:`** Caso seja necessário podemos especificar o endereço a ser traduzido.
**`Local Network:`** A sub rede local que será inclusa no túnel
**`Remote Network:`** A sub rede remota que fará parte do túnel
 
<br>
 
![](<../../images/Pasted image 20250428180211.png>)

==Phase 2 Proposal (SA/Key Exchange)==
**`Protocol:`** O protocolo a ser usado para `Key Exchange`
**`Encryption Algorithms:`** Quais algoritmos de criptografia serão usados
**`PFS key group:`** Opcional mais necessário para evitar alguns tipos de ataque.
 
<br>
 
![](<../../images/Pasted image 20250428180603.png>)

==Expiration and Replacement==
**`Life Time:`** O tempo em segundos até que a `SA` expire e seja necessário renova-la. Os outros campos são calculados com base nesse campo.
 
<br>
 
![](<../../images/Pasted image 20250428180923.png>)

***
***
## Site B
#### Phase 1
Em `VPN > IPsec`, selecionamos `Add P1`. As configurações serão as mesmas que o `Site A` mas com algumas alterações.

>Pre Shared Key - 
>A Pre Shared Key deve ser a mesma em ambos os sites

==IKE Endpoint Configuration==
**`Remote Gateway:`** O `IP` público do `peer`
 
<br>
 
![](<../../images/Pasted image 20250428181508.png>)

==Expiration and Replacement==
**`Life Time:`** Deve ser um valor `10%` superior ao `Site A`
 
<br>
 
![](<../../images/Pasted image 20250428181715.png>)

==Advanced Options==
**`Child SA Start Action:`** Aqui vamos definir para que ele não inicie a conexão por conta própria, apenas responda a conexão.
**`Child SA Close Action:`** Vamos definir para que ele não tente fazer uma reconexão.
 
<br>
 
![](<../../images/Pasted image 20250428182201.png>)

***
#### Phase 2
Agora vamos em `VPN > IPsec` clicar em `Show Phase e Entries` e depois `Add P2`. As configurações são as mesmas que o `Site A`, mas com algumas diferenças.

==General Information | Networks==
**`Description:`** Algo que refira a `LAN`do peer.
**`Remote Subnet:`** A sub rede no `Site A`
 
<br>
 
![](<../../images/Pasted image 20250428182846.png>)

==Expiration and Replacement==
**`Life Time:`** Deve ser uma valor `10%` superior ao do `Site A`.
 
<br>
 
![](<../../images/Pasted image 20250428182946.png>)

***
***
## Conectando
Podemos conferir o `Status` no `Sie A`
 
<br>
 
![](<../../images/Pasted image 20250428183238.png>)

Então podemos nos conectar.
 
<br>
 
![](<../../images/Pasted image 20250428183415.png>)

***
***

Após essas configurações, podemos Criar as regras para `IPSEC`









