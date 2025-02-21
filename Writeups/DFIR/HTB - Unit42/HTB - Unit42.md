##  Questions

###### How many Event logs are there with Event ID 11?
```powershell
Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=11} | Measure-Object -Line
```
![[Image-1.png]]

**Answer:** 56

###### Whenever a process is created in memory, an event with Event ID 1 is recorded with details such as command line, hashes, process path, parent process path, etc. What is the malicious process that infected the victim's system?

Primeiro verifiquei quantos logs de id 1 tinham :
```powershell
Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=1}
```
![[Image-2.png]]

Como eram poucos os enumerei manualmente, até achar o log com o processo pai não comum :
```powershell
(Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=1})[1].Properties[20]
```
![[Image-3.png]]

**Answer:** Preventivo24.02.14.exe.exe

###### Which Cloud drive was used to distribute the malware?

Primeiro verificamos quais eventos de ID 22(eventos de consultas DNS) temos :
```powershell
(Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=22} -Oldest) | Format-Table -AutoSize -Wrap
```
![[Image-4.png]]
Também podemos ver em que momento foram criados.

Como temos 3 podemos enumerar manualmente.
```powershell
(Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=22})[1,2].Properties | Format-Table -AutoSize -Wrap
```
![[Image-5.png]]

os eventos de índice 1 e 2 fazem referência consultas ao ``dropbox``. Vendo o horário em que a requisição foi feita, podemos fazer relação se algum arquivo foi criado nesse horário com os log de id 11(log de criação de arquivo).

```powershell
(Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=11;StartTime="14/02/2024 00:41:26";EndTime="14/02/2024 00:41:27"} -Oldest) | Format-Table -AutoSize -Wrap
```
![[Image-6.png]]
Aqui temos 4 eventos de criação de arquivo que foram feitas nesse horário.

```powershell
(Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=11;StartTime="14/02/2024 00:41:26";EndTime="14/02/2024 00:41:27"})[0].Properties | Format-Table -AutoSize -Wrap
```
![[Image-7.png]]
O evento de índice 1 indica o download do arquivo malicioso.

```powershell
(Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=11;StartTime="14/02/2024 00:41:26";EndTime="14/02/2024 00:41:27"})[1,2].Properties | Format-Table -AutoSize -Wrap
```
![[Image-8.png]]
os logs de índice 1 e 2 indicam o download de parts de um arquivo.

Assimilando o horário de criação desses arquivos com o horário de consulta dns, podemos saber de onde veio.

**Answer:** dropbox

###### The initial malicious file time-stamped (a defense evasion technique, where the file creation date is changed to make it appear old) many files it created on disk. What was the timestamp changed to for a PDF file?

Podemos pesquisar pelo id de evento 2
```powershell
Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=2;} | Measure-Object -Line
```
![[Image-9.png]]
Como são 16, seria demorado para verificarmos manualmente.

Vamos criar um script para podemos saber qual é o log certo.
```powershell
for($i = 0;$i -lt 16;$i++){

$logs = (Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=2;})[$i].Properties[5].Value | Select-String -Pattern 'pdf';

$logs ? (Write-Host "$logs - Id do evento : $i") : ($null)

}
```
![[Image-10.png]]
Nesse script verificamos se o nome do arquivo modificado contém `pdf` e jogamos em uma variável. Depois verificamos se a variável não está vazia, caso não esteja vazia printamos o nome do arquivo e o id do evento. Agora sabemos o id do evento certo.

```powershell
(Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=2;})[7].Properties[6] | Format-Table -AutoSize -Wrap
```
O índice 6 se refere a data para o qual foi modificada.

**Answer:** 2024-01-14 08:10:06

###### The malicious file dropped a few files on disk. Where was "once.cmd" created on disk? Please answer with the full path along with the filename.

Podemos filtrar pelos eventos de id 11 e pelo nome `once.cmd`
```powershell
for($i = 0;$i -lt 56;$i++){

	$logs = (Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=11;})[$i].Properties[5].Value | Select-String -Pattern 'once.cmd';
	
	$logs ? (Write-Host "$logs - Id do evento : $i") : ($null)
}
```
![[Image-11.png]]
Obtivemos 2 resultados. Agora podemos conferir qual a imagem que os criou.

Como são só 2 eventos podemos fazer manualmente.
```powershell
(Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=11;})[34].Properties
```
![[Image-12.png]]

O evento de índice 34 foi criado pelo malware.

**Answer:** ``C:\Users\CyberJunkie\AppData\Roaming\Photo and Fax Vn\Photo and vn 1.1.2\install\F97891C\WindowsVolume\Games\once.cmd``

The malicious file attempted to reach a dummy domain, most likely to check the internet connection status. What domain name did it try to connect to?

Já vimos isso antes, podemos ver as tentativas de resolução DNS.
```powershell
(Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=22})[0].Properties | Format-Table -AutoSize -Wrap
```
![[Image-13.png]]

**Answer:** `www.example.com`

###### Which IP address did the malicious process try to reach out to?
Podemos buscar pelo evento de id 3.

Como só tem 1 podemos vê-lo manualmente
```powershell
(Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=3}).Properties | Format-Table -AutoSize -Wrap
```
![[Image-14.png]]

**Answer:** `93.184.216.34`

###### The malicious process terminated itself after infecting the PC with a backdoored variant of UltraVNC. When did the process terminate itself?

Podemos verificar pelo id de evento 5.
```powershell
(Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=5}) | Format-Table -AutoSize -Wrap
```
![[Image-15.png]]
Como só tem uma podemos verificar manualmente.

```powershell
(Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=5}).Properties | Format-Table -AutoSize -Wrap
```
![[Image-16.png]]

**Answer:** `2024-02-14 03:41:58`























