#Difficult : Hard #Category : Network Forensics
***
#### 1 - Multiple systems were targeted. Provide the IP address of the highest one.
Podemos obter essa reposta em **Statistics -> Conversations -> IPv4**, e observar o `IP` mais alto que inicia a conversa.
![[Pasted image 20250415083335.png]]

**Answer : `10.0.5.15`**
***
#### 2 - What protocol do you think the attack was carried over?
Podemos notar em **Statistics -> Conversations -> TCP**, que todas as conversas se direcionam a porta `80` o que indica `HTTP`.
![[Pasted image 20250415083626.png]]

**Answer : `http`**
***
#### 3 - What was the URL for the page used to serve malicious executables (don't include URL parameters)?
Podemos filtrar pelo `content_type`, para `application`. Caso não tenhamos resultado podemos exportar os objetos do tráfego e analisar a origem.
```
http.content_type contains "application"
```
![[Pasted image 20250415085049.png]]

**Answer : `http://sploitme.com.cn/fg/load.php`**
***
#### 4 - What is the number of the packet that includes a redirect to the french version of Google and probably is an indicator for Geo-based targeting?
Aqui podemos filtrar pelos `status code` de redirecionamento como `301`, `302`, `307` ou `308`. E também tentar filtrar o `location` para algo como `french`, `fr` ou `france`.
```
http.response.code == 302 and http.location contains "fr"
```
![[Pasted image 20250415085447.png]]

**Answer `299`**
***
#### 5 - What was the CMS used to generate the page `shop.honeynet.sg/catalog/`? (Three words, space in between)
Aqui filtrei pelos pacotes `http` que tem a string `shop.honeynet.sg/catalog/`. Como esses pacotes fazem parte do mesmo fluxo, então seguir o `HTTP Stream`.
![[Pasted image 20250415091410.png]]

A grande maioria do `Stream` é um código `HTML`, então copiei esse código em algum arquivo `.htm` e abri no navegador. Aqui podemos ver o nome do `CMS`.
![[Pasted image 20250415091643.png]]

**Answer : `osCommerce Online Merchant`**
***
#### 6 - What is the number of the packet that indicates that 'show.php' will not try to infect the same host twice?
Aqui filtrei pelas `URLs` que contém `show.php`. E olhando cada `Stream` tentei identificar um padrão.
![[Pasted image 20250415094305.png]]

Os pacotes `174` e `366` tem o mesmo endereço de origem, mas apenas o pacote `174` contém uma `payload`. Sendo assim o pacote `366` bloqueou o envio para o mesmo host.

**Answer : `366`**
***
#### 7 - One of the exploits being served targets a vulnerability in "msdds.dll". Provide the corresponding CVE number.
Com um a pesquisa simples podemos achar a `CVE` relacionada.
![[Pasted image 20250415094932.png]]

**Answer : `CVE-2005-2127`**
***
#### 8 - What is the name of the executable being served via `http://sploitme.com.cn/fg/load.php?e=8` ?
Fiz uma pesquisa simples, mas o pacote com o recurso de parâmetro `e=8` não existe.
```
http.request.full_uri contains "load.php?e="
```
![[Pasted image 20250415095810.png]]

Então comecei a investigar o `Stream` dos outros pacotes pra identificar um padrão de envio. Percebo então que o `load.php` está relacionado ao `show.php`.
![[Pasted image 20250415100120.png]]

Então pesquisei pelos arquivos `show.php`com o seu parâmetro. Temos um pacote com `s=84c...` que não apareceu quando pesquisamos por `load.php`.
```
http.request.full_uri contains "show.php?s="
```
![[Pasted image 20250415100313.png]]

Então segui o `Stream` do pacote `496`, pois ele traz a resposta do pacote com  o parâmetro `?s=84c..`. Temos como resposta um binário. Podemos exporta-lo.
![[Pasted image 20250415101410.png]]

Em **File > Export Objects > HTTP...**
![[Pasted image 20250415101724.png]]
![[Pasted image 20250415101845.png]]

O arquivo armazena um código `Javascript` que executa um código ofuscado.
![[Pasted image 20250415103405.png]]

Então removi o código `HTML` e troquei a função `eval()` por `console.log()`, para que ele não execute mas mostre o código ofuscado, e o renomeei para `84c.js` para que eu consiga o executar com o `nodejs`.
```bash
js 84c.js > deobfuscated.js
cat deobfuscated.js
```
![[Pasted image 20250415103941.png]]

Agora podemos pegar esse código desofuscado e colocar no `CyberCheff` com o modo `JavaScript Beautify` para que o código seja mais legível.
![[Pasted image 20250415152651.png]]

Temos alguns `shellcodes`, podemos desofuscar cada um até achar o que contém `e=8`. Após encontrar salvei em um arquivo `download.BIN`.
![[Pasted image 20250415152905.png]]

Devemos usar o `scdbg`, então mandei o binário para uma máquina `Windows`. Aqui temos a execução de um executável `e.exe`.
```powershell
.\scdbg.exe /f ..\download.BIN
```
![[Pasted image 20250415153029.png]]

**Answer : `e.exe`**
***
#### 9 - One of the malicious files was first submitted for analysis on VirusTotal at 2010-02-17 11:02:35 and has an MD5 hash ending with '78873f791'. Provide the full MD5 hash.
Aqui podemos exportar todos os objetos, então usar um `for loop` com o `md5sum` e então comparar com `78873f791`.
![[Pasted image 20250415133704.png]]
```bash
for file in *; do md5sum ${file}; done | grep -i '78873f791'
```
![[Pasted image 20250415133851.png]]

**Answer `52312bb96ce72f230f0350e78873f791`**
***
#### 10 -  What is the name of the function that hosted the shellcode relevant to `http://sploitme.com.cn/fg/load.php?e=3`?
Como já temos o código `JavaScript` desofuscado podemos apenas ir testando os `shellcodes` e notar qual se refere a `e=3`.

![[Pasted image 20250415135752.png]]

**Answer: `aolwinamp`**
***
#### 11 - Deobfuscate the JS at `shop.honeynet.sg/catalog/` and provide the value of the 'click' parameter in the resulted URL.
Como já temos esse arquivo baixado, podemos apenas retirar a parte do `JavaScript`
```bash
cat catalog | grep '<script'
```
![[Pasted image 20250415142935.png]]

Novamente no `CyberCheff` podemos ver o código desofuscado. No final temos um  método `document.write(m)`, podemos trocar para `console.log()` para conseguirmos ver o código.
![[Pasted image 20250415143054.png]]

Então usei o `vim` para colar o código modificado.
```bash
vim catalog_script.js
```
![[Pasted image 20250415143905.png]]

Então executei com o ``nodejs``.
```bash
js catalog_Script.js
```
![[Pasted image 20250415143838.png]]

**Answer : `84c090bd86`**
***
#### 12 - Deobfuscate the JS at `rapidshare.com.eyu32.ru/login.php` and provide the value of the 'click' parameter in the resulted URL.

>[!info] Mais e mais desofuscação 🥲🥲🥲🥲

Já temos esse arquivo baixado, podemos ver seu conteúdo para a análise.
```bash
cat login.php
```
![[Pasted image 20250415144655.png]]
Novamente podemos pegar o código `JavaScript` e substituir o `eval()` por `console.log()`.
```bash
vim login_script.js
```
![[Pasted image 20250415145630.png]]

E então podemos executar com o `nodejs`. Desta vez está em `URL`, podemos joga-lo no `CyberCheff`.
![[Pasted image 20250415145758.png]]
![[Pasted image 20250415145852.png]]
**Answer : `3feb5a6b2f`**
***
#### 13 - What was the version of 'mingw-gcc' that compiled the malware?
Podemos voltar ao tráfego de rede e observar o tráfego de `load.php`, considerando que é ele quem envia o malware. Podemos seguir o tráfego do primeiro pacote.
```
http.request.full_uri contains "load.php"
```
![[Pasted image 20250415151023.png]]

Podemos então pesquisar por `mingw`.
![[Pasted image 20250415151202.png]]


**Answer : `3.4.5`**
***
#### 14 - The shellcode used a native function inside 'urlmon.dll' to download files from the internet to the compromised host. What is the name of the function?
Novamente usamos o `scdbg`, mas agora devemos focar na função que faz o download do arquivo.
```powershell
.\scdbg.exe /f ..\download.BIN
```
![[Pasted image 20250415153449.png]]

**Answer : `URLDownloadToFile`**
***

![[Pasted image 20250415154604.png]]