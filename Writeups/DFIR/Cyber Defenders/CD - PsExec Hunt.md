#Category : Network Forensics #Difficult : Easy
***
#### Info
Recebemos um arquivo de tráfego de rede para prosseguir com as nossas investigações.
![[Pasted image 20250519151755.png]]
***
#### 1 - To effectively trace the attacker's activities within our network, can you identify the IP address of the machine from which the attacker initially gained access?
Aqui analisei as estatísticas das conversas `IPv4` e vi que há duas conversas que fogem muito da linha de base, os dois partindo do mesmo `IP`.
![[Pasted image 20250519153004.png]]

**Answer :** `10.0.0.130`
***
#### 2 - To fully understand the extent of the breach, can you determine the machine's hostname to which the attacker first pivoted?
Podemos alternar para a aba `TCP` e começar seguindo o fluxo do tráfego do invasor.
![[Pasted image 20250519153328.png]]

Nesse fluxo temos uma mensagem de `NTLMSSP_CHALLENGE` que inclui o `target name` dentre outras informações.
![[Pasted image 20250519153914.png]]

**Answer :** `SALES-PC`
***
#### 3 - Knowing the username of the account the attacker used for authentication will give us insights into the extent of the breach. What is the username utilized by the attacker for authentication?
No pacote a seguir temos o a especificação do usuário `ssales`.
![[Pasted image 20250519154914.png]]

**Answer :** `ssales`
***
#### 4 - After figuring out how the attacker moved within our network, we need to know what they did on the target machine. What's the name of the service executable the attacker set up on the target?
Em alguns pacotes a seguir o invasor cria um arquivo `PSEXESVC.exe`.
![[Pasted image 20250519154528.png]]

**Answer :** `PSEXESVC`
***
#### 5 - We need to know how the attacker installed the service on the compromised machine to understand the attacker's lateral movement tactics. This can help identify other affected systems. Which network share was used by PsExec to install the service on the target machine?
Antes da criação do arquivo na máquina, o último diretório acessado foi `\\10.0.0.133\ADMIN$`.
![[Pasted image 20250519155205.png]]

**Answer :** `ADMIN$`
***
#### 6 - We must identify the network share used to communicate between the two machines. Which network share did PsExec use for communication?
Aqui podemos filtrar pelos pacotes `smb Tree Connect Requests `, que indicam a conexão do cliente com um compartilhamento de rede (`tree`) do `smb`.
![[Pasted image 20250519173633.png]]

Temos então o uso do compartilhamento especial `IPC$`, ele serve para comunicação entre processos e é muito utilizado para ataques, pois facilita a entrega de arquivos.

**Answer :** `IPC$`
***
#### 7 - Now that we have a clearer picture of the attacker's activities on the compromised machine, it's important to identify any further lateral movement. What is the hostname of the second machine the attacker targeted to pivot within our network?
Aqui podemos filtrar pelas tentativas de negociação do `smb`. E vemos que o invasor tentou se comunicar com outra máquina.
![[Pasted image 20250519174255.png]]

Podemos seguir o fluxo, e verificar o hostname na resposta da `NTLMSSP_CHALLENGE`.
![[Pasted image 20250519174701.png]]

**Answer :** `marketing-pc`
***
![[Pasted image 20250519174740.png]]