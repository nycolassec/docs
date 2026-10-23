### Info
Para evitar que qualquer interface seja conectada as portas do Switch podemos definir quais endereços *MAC* serão aceitos. Podemos fazer isso de forma estática ou dinâmica, pedindo ao Switch que armazene e aceite somente os primeiros *MACs* encaminhados pela porta escolhida. Chamamos isso de *Port Security*

<br>

![](<../../images/Pasted image 20250515094607.png>)

| PC   | IP         | MAC               | If   |
| ---- | ---------- | ----------------- | ---- |
| PC-0 | 10.0.10.10 | 00:50:79:66:68:17 | e0/0 |
| PC-1 | 10.0.10.11 | 00:50:79:66:68:18 | e0/1 |
| PC-2 | 10.0.10.12 | 00:50:79:66:68:14 | e0/2 |
| PC-3 | 10.0.10.13 | 00:50:79:66:68:15 | e0/3 |
***
### Switch
A porta `e0/0` aceitará somente o MAC do `PC-0`, a `e0/1` aceitará até 3 MACs dinamicamente, a `e0/2` aceitará estaticamente o MAC do `PC-2` e `PC-1` e a porta `e0/3` aceitará o MAC de `PC-3` e mais 2 MACs aprendidos dinamicamente.

```
interface e0/0
	switchport mode access
	switchport port-security
	switchport port-security maximum 1
	switchport port-security mac-address 00:50:79:66:68:17
	switchport port-security violation restrict
	exit

interface e0/1
	switchport mode access
	switchport port-security
	switchport port-security maximum 3
	switchport port-security mac-address sticky
	switchport port-security violation shutdown
	exit

interface e0/2
	switchport mode access
	switchport port-security
	switchport port-security maximum 2
	switchport port-security mac-address 00:50:79:66:68:14
	switchport port-security mac-address 00:50:79:66:68:18
	switchport port-security violation shutdown
	exit

interface e0/3
	switchport mode access
	switchport port-security
	switchport port-security maximum 3
	switchport port-security mac-address sticky
	switchport port-security mac-address 00:50:79:66:68:15
	switchport port-security violation shutdown
	exit

end
```
***
### Verificando
```
show port-security
```

![](<../../images/Pasted image 20250515094301.png>)

```
show port-security address
```
![](<../../images/Pasted image 20250515094421.png>)


