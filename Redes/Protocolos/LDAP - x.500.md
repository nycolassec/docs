#ldap #x500 #pam #nss
## Definição
---
LDAP ( **L**ightweight **D**irectory **A**ccess **P**rotocol ) é um protocolo de acesso e gerenciamento de informações em um **serviço/estrutura** de diretório (como o **x.500**). Muito usado para autenticação, autorização e armazenar informação sobre usuários, grupos e outros recursos de rede, como impressoras e servidores.

Então em resumo o LDAP seria o **x.500 + TCP/IP**, pois o x.500 só podia ser acessado localmente mas com o LDAP podemos utiliza-lo em uma rede TCP/IP, então o LDAP serve como um banco de dados para consultar e armazenar informações organizadas hierarquicamente no padrão **x.500**.

==O LDAP é definido pela RFC 4519.==

## Estrutura
---
![[Redes-Protocolos-x.500.png]]

Podemos observar que a estrutura do x.500 é como se fosse uma árvore binária onde cada nó filho pode ter mais filhos.
- **dc** : Seria o **domain component**, representando o nome do nosso domínio, sendo o nível mais alto da nossa "árvore".
- **c** : Significa **country** indicando o país referente.
- **o** : Seria as **organizations** indicando as filiais da empresa.
- **ou** : Indica **organizational unit** que seria os setores das filiais.
- **cn** : Seria o **common name** que identifica um objeto, pode ser um usuário, um asset, um dispositivo e etc.
Existem outros atributos e objetos do padrão x.500, mas aqui demonstrei apenas de forma resumida.

## Nomenclaturas usuais
---
Aqui estão algumas nomenclaturas que costumam causar algumas confusões.
### Attributes
**cn** -> Common Name -> Pode ser aplicado a qualquer coisa com um nome (Usuário, Dispositivo)
**c** -> Country -> Indica o País
**ou** -> Organizational Unit -> Indica um setor ou um departamento
### ObjectClasses
Conjunto de atributos. Cada classe define um conjunto de atributos obrigatórios e opcionais, como a classe Person que precisa ter os atributos **cn** e **sn** definidos.
**Person** -> cn + sn
![[Redes-Protocolos-RFC-LDAP-Person-Object.png]]

**Country** -> c
![[Redes-Protocolos-RFC-LDAP-Country-Object.png]]
### Schemas
Plugins (attributes + ObjectClasses) adicionam novos Atributos e Classes que não vem por padrão.
Como o **PosixAccount** que é um **ObjectClass** que traz vários atributos para a criação de uma conta no padrão **Unix**. Ou seja se não vamos usar o LDAP para autenticação não precisamos importar o **PosixAccount**.

## Processo
---
![[Redes-Protocolos-Processo-LDAP.png]]
Como o **LDAP** é um protocolo de pesquisa e não de autenticação usamos a **libpam** e a **libnss** para fazer essa interpretação com os dados buscados pelo ldap. 
- **libpam** - Essa biblioteca implementa o framework PAM( Pluggable Authentication Modules), que é uma arquitetura modular para a autenticação de usuários. Com ele podemos configurar em um único arquivo para usar diferentes métodos de autenticação (login, ssh, sudo e etc) em vez de configurar cada um manualmente.
- **libnss** - Essa biblioteca implementa o NSS (Name Service Switch),que fornece um mecanismo para acessar várias fontes de informações de rede, como a resolução de nomes de host, informações sobre usuários, grupos e outros dados relacionados ao sistema, em um formato uniforme.

