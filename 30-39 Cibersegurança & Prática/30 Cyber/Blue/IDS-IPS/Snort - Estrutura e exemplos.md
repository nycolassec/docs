## Exemplos de regras
Sabendo de como funciona a estrutura de uma regra do Snort, podemos criar regras diversas.
#### Alertando PING
```c
alert icmp any any -> 192.168.1.0/24 any (msg: "ICMP traffic detected"; sid: 1000001; rev: 1;)
```
#### Alertando SYN Scan
```c
alert tcp any any -> 192.168.1.0/24 any (msg: "SYN Scan detected"; flags: S; detection_filter: track by_src, count 40, seconds 60 ; sid: 1001001; rev: 1;)
```
- `flags: S;` - Somente pacotes **SYN** (Início do handshake).
- `detection_filter: track by_src, count 40, seconds 60;` - Alerta apenas se um mesmo host de origem enviar **40 SYNs** em **60 segundos**.
#### Alertando XMAS Scan
```c
alert tcp any any -> 192.168.1.0/24 any (msg: "XMAS Scan detected"; flags: FPU; detection_filter: track by_src, count 40, seconds 60 ; sid: 1000011; rev: 1;)
```
- `flags: FPU;` - Somete pacotes com as flags **FIN**, **PSH** e **URG**, típico de um XMAS scan.
#### Alertando conteúdo em pacotes
Podemos validar se existe um determinado pattern em um pacote.
```c
alert tcp any any -> 192.168.1.0/24 any (msg: "Malicious search"; content: "malware"; sid: 1000011; rev: 1;)
```
- `content: "malware";` - Ele vai procurar a string "malware" em todo o pacote.