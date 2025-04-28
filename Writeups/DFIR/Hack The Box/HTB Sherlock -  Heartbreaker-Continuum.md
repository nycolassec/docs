#Difficult : Easy
#Tools  : VirusTotal, exiftool, readpe
***
#### Info
Neste cenário recebemos  um arquivo executável para prosseguirmos com a nossas investigações.
![[Pasted image 20250328084326.png]]
****
#### 1 - To accurately reference and identify the suspicious binary, please provide its SHA256 hash.
Podemos obter seu hash `SHA256` diretamente do terminal.
```sh
sha256sum Superstar_MemberCard.tiff.exe
```
![[Pasted image 20250328084504.png]]

**Answer** : 12DAA34111BB54B3DCBAD42305663E44E7E6C3842F015CCCBBE6564D9DFD3EA3
***
#### 2 - When was the binary file originally created, according to its metadata (UTC)?
Podemos obter também pelo terminal com `exiftool` ou `readpe` ou pelo `VirusTotal`. Após enviar o o hash do arquivo no ``VirusTotal``, podemos ir na aba `Details` e na seção `History`.
![[Pasted image 20250328085209.png]]

**Answer** : 2024-03-13 10:38:06
***
#### 3 - Examining the code size in a binary file can give indications about its functionality. Could you specify the byte size of the code in this binary?
Podemos obter através do ``exiftool``.
```sh
exiftool -CodeSize ./Superstar_MemberCard.tiff.exe
#Code Size                       : 38400
```

**Answer** : 38400
***
#### 4 - It appears that the binary may have undergone a file conversion process. Could you determine its original filename?
No `VirusTotal` podemos obte-lo em `Details` e `Manifest Resource`.
![[Pasted image 20250328085923.png]]

**Answer** : newILY.ps1
***
#### 5 - Specify the hexadecimal offset where the obfuscated code of the identified original file begins in the binary.
Primeiro recuperamos todas as strings legíveis do arquivo.
```sh
strings Superstar_MemberCard.tiff.exe > strings
```
![[Pasted image 20250328090722.png]]

O código pega o valor da variável `sCrt`, inverte seu valor e converte de `base64` para string. Então podemos pesquisar pela variável `sCrt`.
```sh
hexdump -Cv ./Superstar_MemberCard.tiff.exe | grep -i "sCrt"
```
![[Pasted image 20250328094932.png]]
A definição do código ofuscado é iniciada após 4 bytes.

**Answer** : 2C74
***
#### 6 - The threat actor concealed the plaintext script within the binary. Can you provide the encoding method used for this obfuscation?
Como já vimos, o código foi codificado em `base64`.

**Answer** : base64
***
#### 7 - What is the specific cmdlet utilized that was used to initiate file downloads?
Podemos pegar o código `base64` e converter para `string`, assim saberemos o comando ofuscado. Como no código ele invertido, precisamos inverter de volta e depois decodificar.
```sh
coded="==gCNU2Yy9mRtASZzJXdjVmUtAicpREdldmchRHJggGdhBVLg0WZ0lULlZ3btVmUK0QZjJ3bG1CIlNnc1NWZS1CIoRXYQR3YhJHd4V0dkACa0FGUtASblRXStUmdv1WZSpQDK0Q..." # Deve ser atribuido todo o código em base64

echo $coded | rev | base64 -d 
```
![[Pasted image 20250328100158.png]]

**Answer** : Invoke-WebRequest
***
#### 8 - Could you identify any possible network-related Indicators of Compromise (IoCs) after examining the code? Separate IPs by comma and in ascending order.
Fiz um regex básico para filtrar endereços IPs e poder analisar seu propósito
```sh
echo $coded | rev | base64 -d | grep -E "([0-9]{1,3}\.){3}[0-9]{1,3}"
```
![[Pasted image 20250328100857.png]]
O endereço `44.206.187.144` está servindo o Malware, e o endereço `35.169.66.138` está sendo acessado via `sftp` como o parâmetro `-hostkey=*`, assim o host não é verificado.

**Answer** : 35.169.66.138,44.206.187.144
***
#### 9 - The binary created a staging directory. Can you specify the location of this directory where the harvested files are stored?
Analisando o código, podemos ver onde ele salva o arquivo baixado.
![[Pasted image 20250328101333.png]]

**Answer** : C:\Users\Public\Public Files
***
#### 10 - What MITRE ID corresponds to the technique used by the malicious binary to autonomously gather data?
Vemos então nas técnicas de `Collection` e ver se alguma técnica é feita de forma autônoma.
![[Pasted image 20250328102024.png]]

**Answer** : T1119
***
#### 11 - What is the password utilized to exfiltrate the collected files through the file transfer program within the binary?
Aqui podemos observar a chamada ao ``sftp`` onde ele passa a sua senha.

**Answer** : M8&C!i6KkmGL1-#
***

![[Pasted image 20250328102709.png]]
