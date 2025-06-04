Um analista de nosso SOC informou um indício de um ataque de força bruta em um servidor Windows da empresa. Ele informou que existem alertas referentes a 2 endereços IPs da internet.
### Info
Recebemos dois arquivos de log de Windows para fazermos nossas investigações. Recebemos os arquivos em `.evtx` mas usei o `EvtxeCmd` para passa-los para `.csv`
![[Pasted image 20250528165758.png]]
***
### 1 - Parece que sofremos um escaneamento de portas. Qual foi o endereço IP que realizou o scan?
No arquivo `sec.csv`, filtrei pelo `ID` de evento 3, que indica conexão de rede, então verifiquei o campo `Payload` dos eventos. O hoste de IP `103.76.96.137` tentou fazer conexão de rede com várias portas.
![[Pasted image 20250528184307.png]]

**Answer :** `103.76.96.137`
***
### 2 - Além do escaneamento, aconteceu algum ataque? Se sim, qual o protocolo/serviço foi atacado?
Após o brute force o IP `103.76.96.72` se conectou a porta `3389`, referente ao `RDP`.
![[Pasted image 20250528184811.png]]

**Answer :** `RDP`
***
### 3 - Qual o endereço IP do atacante que atacou rdp?
Como pontuado na questão anterior, o IP `103.76.96.72` fez várias conexões ao `RDP`

**Answer :**  `103.76.96.72`
***
### 4 - Qual o ID da técnica utilizada pelo atacante no MITRE? (Formato: Txxxx)
A técnica usada foi brute force, com uma pesquisa básica podemos achar sua `ID`.
![[Pasted image 20250528185202.png]]
### 5 - Quantas falhas de logon ocorreram no período de log extraído?
Agora partimos para o aquivo `sec.csv` e filtramos pela ID de evento `4625`, que seria a ID de falha de logon.
![[Pasted image 20250528191231.png]]

**Answer :** `124`
***
### 6 - Parece que o atacante conseguiu se autenticar no sistema… Qual foi a hora exata em que isso ocorreu? \[Formato hh:mm]

> [!warning] Atenção
> Algumas ferramentas podem exibir a hora no fuso horário UTC+3 ou ajustar automaticamente de acordo com o fuso horário local (dependendo do estado brasileiro em que você estiver). Considere isso ao analisar os horários nos logs.

Podemos filtrar pela ID de evento `4624`, que seria o ID de logon bem sucedido e pelo IP do atacante. Como indicado, devemos considerar nosso horário `-3`.
![[Pasted image 20250528191712.png]]

**Answer :** `17:58`
***
### 7 - Os dois endereços IP que geraram os alertas são do mesmo provedor/empresa. Qual o país deles?
No site do [whois](www.whois.com/whois/103.76.96.72) ou em outro site IP geolocation podemos achar essa informação.
![[Pasted image 20250528192353.png]]

**Answer :** `Alemanha`
***
### 8 - Qual o endereço de e-mail de “abuse” onde poderíamos notifica-los do ataque?
No site do [whois](www.whois.com/whois/103.76.96.72) ou em outro site IP geolocation podemos achar essa informação.
![[Pasted image 20250528192543.png]]

**Answer :** `abuse@lewtelnet.de`
***
### 9 - Pretendemos bloquear os acessos originados desse provedor/empresa. De acordo com o whois, qual o endereço de rede podemos cadastrar na regra de firewall? (Formato CIDR Notation: a.b.c.d/x )
No site do [whois](www.whois.com/whois/103.76.96.72) ou em outro site IP geolocation podemos achar essa informação.
![[Pasted image 20250528192721.png]]

**Answer :** `103.76.96.0/22`
***
![[Pasted image 20250528192932.png]]
