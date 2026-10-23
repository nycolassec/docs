## Info
Temos dois computadores:
- **`dc`** : 10.0.30.10
- **`pc-01`** : 10.0.30.100

Neste cenário temos os usuários `nycolas`, `atylas` e `lucas` no grupo `Developers`. Cada usuário deve ter uma pasta home em `\\10.0.30.10\home\<nome do usuário>`, ela será mapeada na unidade `R:` automaticamente.

Cada usuários vai poder usar no máximo `150MB` de espaço, e não poderá conter arquivos com a extensão `EXE`, `BAT` e `PS1`.
***
## Compartilhando pastas
Primeiro criamos a pasta `C:\home` e as pastas dos usuários dentro delas. Então vamos em `Properties > Sharing > Share`.
![[Pasted image 20250428082841.png]]

Adicionamos o grupo `Developers` como `Read/Write` e confirmamos em `Share`.
![[Pasted image 20250428083130.png]]
***
## Mapeamento automático de pastas
Primeiro abrimos o `Group Policy Management`.
![[Pasted image 20250425115836.png]]

E criamos uma nova `GPO` linkada no domínio chamada `Drive Map`. E editamos ela.
![[Pasted image 20250425115929.png]]

Podemos automatizar o mapeamento de pastas : `User Configuration > Preferences > Windows Settings > Drive Maps`, em `New > Mapped Drive`.
![[Pasted image 20250428081236.png]]

Podemos usar `Action: Update`, `Location \\10.0.30.10\home\%USER%`, `Label as: home`, `Recconect: Check`, `Drive Letter` de sua escolha.
![[Pasted image 20250428081739.png]]

Agora podemos definir o `Level targeting` em `Common`.
![[Pasted image 20250428082117.png]]

Então selecionar `Security Group` e o grupo `Developers`.
![[Pasted image 20250428082241.png]]
***
## Cotas
Podemos ir em `Tools > File Server Resource Manager` para definir as restrições de espaço.
![[Pasted image 20250428083930.png]]

Agora podemos criar um template em `Quota Management > Quota Templates`.
![[Pasted image 20250428085412.png]]

Agora em `Quota Management > Quotas`, aplicamos essa cota para cada pasta.
![[Pasted image 20250428085656.png]]
***
## File Screening
Podemos ir em `Tools > File Server Resource Manager > File Screening Management` para definir as restrições de arquivos. Podemos criar um novo template em `File Screen Templates > Create File Screening Template`.
![[Pasted image 20250428090344.png]]

Agora em `File Screening Management > File Screens > Create File Screening`
![[Pasted image 20250428090747.png]]





















