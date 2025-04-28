#Difficult : Easy
#Category : Malware Analysis
***
#### 1 - Which encoding method does the browser extension use to obscure target URLs, making them more difficult to detect during analysis?
Podemos obter a resposta no arquivo `crypto.js`.
![[Pasted image 20250416084918.png]]

**Answer : `base64`**
***
#### 2 - Which website does the extension monitor for data theft, targeting user accounts to steal sensitive information?
Em `app.js` temos uma função que envia o `username` e `password`, o target está em `base64`, então podemos apenas usar um `decode`.
```bash
cat app.js
```
![[Pasted image 20250416090443.png]]

```bash
echo "d3d3LmZhY2Vib29rLmNvbQ==" | base64 -d
```
![[Pasted image 20250416090639.png]]

**Answer : `www.facebook.com`**
***
#### 3 - Which type of HTML element is utilized by the extension to send stolen data?
Em `app.js` temos uma função `sendToServer()` que cria uma `tag` de imagem com o `src` contendo os dados roubados.
```bash
cat app.js
```
![[Pasted image 20250416091449.png]]

**Answer : `<img>`**
***
#### 4 - What is the first specific condition in the code that triggers the extension to deactivate itself?
Aqui como é uma condição, filtrei por `if`, para ter uma noção maior sobre o que a `extension` verifica.
```bash
grep -Ri "if" ./
```
![[Pasted image 20250416091833.png]]

**Answer : `navigator.plugins.length === 0`**
***
#### 5 - Which event does the extension capture to track user input submitted through forms?
Aqui filtrei por `addEventListener`, pois é a função do `JavaScript` que escuta eventos. Então identifiquei o evento `submit` que é acionado quando enviamos um formulário.
```bash
grep -Ri "addEventListener" ./
```
![[Pasted image 20250416093251.png]]

**Answer : `submit`**
***
#### 6 - Which API or method does the extension use to capture and monitor user keystrokes?
Como identificado antes, também temos uma função que escuta o pressionamento de teclas.
```bash
grep -Ri "addEventListener" ./
```
![[Pasted image 20250416093251.png]]

**Answer : `keydown`**
***
#### 7 - What is the domain where the extension transmits the exfiltrated data?
Aqui filtrei por `http` para tentar achar os domínio possíveis. Achei então o domínio que é anexado no `src` da `img` para roubar os dados.
```bash
grep -Ri "http" ./
```
![[Pasted image 20250416093707.png]]

**Answer : `Mo.Elshaheedy.com`**
***
#### 8 - Which function in the code is used to exfiltrate user credentials, including the username and password?
Em `app.js` temos uma função que recebe o `username` e o `password` do formulário, para exfiltração.
```bash
cat app.js
```
![[Pasted image 20250416094149.png]]

**Answer : `exfiltrateCredentials(username, password);`**
***
#### 9 - Which encryption algorithm is applied to secure the data before sending?
Em `crypto.js`, temos uma função que criptografa os dados antes de serem encodados em `base64`.
![[Pasted image 20250416094430.png]]

**Answer : `aes`**
***
#### 10 - What does the extension access to store or manipulate session-related data and authentication information?
Em `manifest.json` temos quais as permissões que a `extension` exige, uma delas é o `cookies`, que é usado para manipular os `cookies` da sessão.
![[Pasted image 20250416094938.png]]

**Answer : `cookies`**
***

![[Pasted image 20250416095033.png]]