#Difficult : Easy
***
#### Info
Para realizarmos nossas investigações recebemos arquivos de configuração do ``Kubernetes``.
![[Pasted image 20250410093207.png]]

Embora eu não esteja muito familiarizado com ``Kurbernets``, identifiquei que as configurações dos ``pods`` começavam com `kubectl.kubernetes.io/last-applied-configuration`. Então comecei buscando por ele.
***
#### 1 - At which NodePort is the `ssh-deployment` Kubernetes service exposed for external access?
Comecei fazendo uma busca por `kubectl.kubernetes.io/last-applied-configuration` para ver as configurações gerais.
```powershell
Select-String -Path *.yaml -Pattern 'kubectl.kubernetes.io/last-applied-configuration' -Context 0,8
```
![[Pasted image 20250410094443.png]]

**Answer : `31337`**
***
#### 2 - What is the ClusterIP of the kubernetes cluster?
Podemos ver em `clusterIP`.
```powershell
Select-String -Path *.yaml -Pattern 'kubectl.kubernetes.io/last-applied-configuration' -Context 0,8
```
![[Pasted image 20250410094443.png]]

**Answer : `10.43.191.212`**
***
#### 3 - What is the flag value inside ssh-config configmap?
Podemos ver uma definição de `FLAG` nas configurações.
```powershell
Select-String -Path *.yaml -Pattern 'kubectl.kubernetes.io/last-applied-configuration' -Context 0,8
```
![[Pasted image 20250410094642.png]]

**Answer : `HTB{1d2d2b861c5f8631f841b57f327f46f8}`**
***
#### 4 - What is the value of password (in plaintext) which is found inside ssh-deployment via secret?
Podemos ver a senha definida em `USER_PASSWORD`.
```powershell
Select-String -Path *.yaml -Pattern 'kubectl.kubernetes.io/last-applied-configuration' -Context 0,8
```
![[Pasted image 20250410094915.png]]

Após encontra-lo, usei um decode para descobrir a senha.
```powershell
[System.Text.Encoding]::UTF8.GetString( [System.Convert]::FromBase64String("U3VwZXJDcmF6eVBhc3N3b3JkMTIzIQ==") )
```
![[Pasted image 20250410095516.png]]

**Answer : `SuperCrazyPassword123!`**
***
#### 5 - What is the name of the malicious pod?
Existe um `pod` que faz uma conexão reversa com um `IP`. Então o classifiquei como o `malicious pod`.
```powershell
Select-String -Path *.yaml -Pattern 'kubectl.kubernetes.io/last-applied-configuration' -Context 0,8
```
![[Pasted image 20250410095846.png]]

**Answer : `metrics-server-557ff575fx-4q62x`**
***
#### 6 - What is the image attacker is using to create malicious pod?
Aqui olhamos as configurações do `pod` em `"image"`.
```powershell
Select-String -Path *.yaml -Pattern 'kubectl.kubernetes.io/last-applied-configuration' -Context 0,8
```
![[Pasted image 20250410095846.png]]

**Answer : `alpine`**
***
#### 7 - Whats the attacker IP?
Aqui olhamos para qual `IP` é feita a conexão com o `nc`.
```powershell
Select-String -Path *.yaml -Pattern 'kubectl.kubernetes.io/last-applied-configuration' -Context 0,8
```
![[Pasted image 20250410095846.png]]

**Answer : `10.10.14.11`**
***

![[Pasted image 20250410100246.png]]