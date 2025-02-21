## Implantar e gerenciar controladores de domínio do Active Directory Domain Services

> [!info] Podemos usar o comando `Test-ComputerSecureChannel -Repair` para recuperar a relação de segurança.

**Qual é o repositório central de todos os objetos de domínio, como contas de usuário, contas de computador e grupos ?**
O banco de dados do AD DS.

---

**O que são componentes lógicos do AD DS?**
São estruturas usadas para implementar um design do AD DS.

**Quais são os tipos de componentes lógicos do AD DS?**
Partição, Esquema, Domínio, Árvore de domínio, Floresta, OU e Contêiner.

**Defina o tipo lógico do AD DS "Partição".**
É uma parte do banco de dados do AD DS.

**Defina o tipo lógico do AD DS "Esquema".**
É o conjunto de definições dos tipos de objeto e atributos que você usa para definir os objetos criados no AD DS.

**Defina o tipo lógico do AD DS "Domínio".**
É um contêiner administrativo lógico para objetos como usuários e computadores.

**Defina o tipo lógico do AD DS "Árvore de domínio".**
É uma coleção hierárquica de domínios que compartilham um domínio raiz comum e um namespace DNS contíguo.

**Defina o tipo lógico do AD DS "Floresta".**
É uma coleção de um ou mais domínios que têm uma raiz do AD DS, um esquema comum e um catálogo global comum.

**Defina o tipo lógico do AD DS "OU".**
É um objeto de contêiner para usuários, grupos e computadores que fornece uma estrutura para delegar direitos administrativos e administração ao vincular GPOs

**Defina o tipo lógico do AD DS "Contêiner".**
Um contêiner é um objeto que fornece uma estrutura organizacional para uso no AD DS. Não é possível vincular GPOs a contêineres.

---

**O que são componentes físicos do AD DS?**
São os objetos tangíveis ou que descrevem componentes tangíveis no mundo real.

**Quais são os tipos de componentes físicos do AD DS?**
Controlador de domínio, Armazenamento de dados, Servidor de catálogo global, RODC, Site e Sub-rede

**Defina o tipo físico do AD DS "Controlador de domínio".**
Contém uma cópia do banco de dados do AD DS.

**Defina o tipo físico do AD DS "Armazenamento de dados".**
Há uma cópia do armazenamento de dados em cada controlador de domínio.

**Defina o tipo físico do AD DS "Servidor de catálogo global".**
É um controlador de domínio que hospeda o catálogo global, que é uma cópia parcial e somente leitura de todos os objetos em uma floresta de vários domínios.

**Defina o tipo físico do AD DS "RODC ".**
Um RODC é uma instalação especial, somente leitura, do AD DS.

**Defina o tipo físico do AD DS "Site".**
Um site é um contêiner para objetos do AD DS.

**Defina o tipo físico do AD DS "Sub-rede".**
Uma sub-rede é uma parte dos endereços IP de rede de uma organização atribuídos a computadores em um site.

---
**Quais objetos existem no domínio raiz da floresta :**
- A função de mestre de esquema.
- A função de mestre de nomeação de domínio.
- O grupo Administradores de Empresa.
- O grupo Administradores de Esquema.

**Como uma floresta d0o AD DS é descrita geralmente ?**
Como um limite de segurança e um limite de replicação.

**Quais objetos existem em cada domínio :**
- Função mestra de RID (ID relativa).
- A função mestra de infraestrutura.
- A função mestra do emulador do PDC (controlador de domínio primário).
- O grupo Administradores de Domínio.

**Como um domínio do AD DS geralmente é descrito ?**
Uma unidade administrativa e como um limite de replicação.

**O que oferece um domínio do AD DS ?**
Autenticação e autorização.

**O que fazem as relações de confiança ?**
Habilitam o acesso a recursos em um ambiente complexo.


--- 
**O que são os mestres de operações do AD DS?**
As funções de mestre de operações do AD DS são responsáveis por executar operações não adequadas para um modelo de vários mestres.

**Quais são as cinco funções de mestre de operações ?**
- Mestre de esquema
- Mestre de nomeação de domínio
- Mestre de infraestrutura
- Mestre de RID do
- Mestre emulador PDC

**Quais são os mestres de operações de floresta ?**
- Mestre de nomeação de domínio.
- Mestre de esquema.

**Quais são os mestres de operações de domínio ?**
- Mestre RID.
- Mestre de infraestrutura.
- Mestre de emulador PDC.
















































































































































































