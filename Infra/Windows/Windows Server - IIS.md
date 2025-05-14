## Instalação
Em **`Server Manager > Tools > Add Roles and Features > Server Roles`** marcamos o `Web Server (IIS)`.
![[Pasted image 20250430135417.png]]

Dependendo de como vamos configurar, marcamos as `Web Server Role (IIS)` que forem necessárias, por agora podemos deixar padrão.
![[Pasted image 20250430135750.png]]
***
## Configuração padrão
Vamos começar com algumas configurações padrão como mudar o diretório `root` para `C:\intranet\`, definir para ser acessado em `www.intranet.com`, colocar uma autenticação básica e restringir o acesso a grupos, usuários, domínios ou `IPs`.

**`Server Manager > Tools > Internet Information Services (IIS) Manager`**
![[Pasted image 20250430142030.png]]
### Mudar diretório root
Selecionamos o `Site` e vamos em `Basic Settings...`.
![[Pasted image 20250430142153.png]]

E então podemos mudar o `Physical path`.
![[Pasted image 20250430142404.png]]

Podemos então criar um arquivo `index.html` com o conteúdo a ser mostrado.
### Mudar hostname do site
Selecionamos o `Site` e vamos em `Bindings...`.
![[Pasted image 20250430143044.png]]

Selecionamos então qual `Binding` queremos modificar, selecionamos `Edit` e colocamos o hostname pelo qual ele será acessado, podemos então confirmar com `OK`
![[Pasted image 20250430143203.png]]
## Autenticação básica e URL Authorization
A `Web Server Role` de `Basic Authentication` e `URL Authentication` não vem por padrão, mas podemos adiciona-las em **`Server Manager > Tools > Add Roles and Features > Server Roles > Web Server`**
![[Pasted image 20250430144248.png]]

Agora selecionamos o `Site` e vamos em `Authentication` e `Open Feature`.
![[Pasted image 20250430145005.png]]

Então habilitamos o `Basic Authentication` e desabilitamos o `Anonymous Authentication`.
![[Pasted image 20250430150837.png]]

Agora ao acessarmos o site, devemos colocar um usuário válido do sistema para acessar o site.
![[Pasted image 20250430150926.png]]

Para restringir por usuário ou grupo, vamos em `Authorization Rules` e `Open Feature`.
![[Pasted image 20250430151522.png]]

E então definimos as regras de acordo com a necessidade.
![[Pasted image 20250430151611.png]]

Como neguei o acesso ao usuário `atylas`, ele não consegue acessar o site.
![[Pasted image 20250430151735.png]]
### Restrição de domínios e IPs
Aqui devemos adicionar a `Web Server Role` de nome `IP and Domain Restrictions`.
![[Pasted image 20250430152811.png]]

Selecionamos `IP and Domain Restrictions` e `Open features`.
![[Pasted image 20250430153135.png]]

Então criamos nossas regras de acordo com o necessitado
![[Pasted image 20250430153331.png]]

Em `Edit Feature Settings...` definimos para negar o acesso caso não exista uma regra para um determinado `IP`.
![[Pasted image 20250430153520.png]]






