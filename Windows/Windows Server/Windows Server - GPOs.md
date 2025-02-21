Uma **GPO (Group Policy Object)** é um conjunto de configurações administrativas no **Active Directory (AD)** que permite controlar e configurar os ambientes de usuários e computadores em uma rede baseada em Windows.

---
## Estrutura de uma GPO
**Configurações de Computador:** Aplicam-se quando um computador é iniciado. Podem incluir políticas de segurança, scripts de inicialização, configurações de rede, entre outros.

**Configurações de Usuário:** Aplicam-se quando um usuário faz login. Podem incluir políticas de desktop, configurações de aplicativos, restrições de acesso, etc.

---
## Escopo de  um GPO
Você pode usar vários métodos para gerenciar o escopo dos GPOs baseados em domínio. O primeiro é a vinculação do GPO. No AD DS, você pode vincular GPOs a:

- **Sites**
- **Domínios**
- **Unidades Organizacionais**

O site, o domínio ou a UO se torna o escopo máximo do GPO

---
## Ordem de processamento de um GPO
A Política de Grupo obedece à seguinte ordem de processamento hierárquico:

1. GPOs locais.
2. GPOs vinculados ao site.
3. GPOs vinculados ao domínio.
4. GPOs vinculados à unidade organizacional.
5. GPOs vinculados à unidade organizacional acessória.

Por exemplo, uma política que restringe o acesso ao Painel de Controle aplicada no nível do domínio pode ser revertida por uma política aplicada no nível da unidade organizacional aos objetos contidos nessa unidade organizacional específica.

---
## Herança de GPO
O comportamento padrão da Política de Grupo é que os GPOs vinculados a um contêiner de nível superior são herdados por contêineres de nível inferior. 

Para bloquear a herança, clique com o botão direito do mouse ou acesse o menu de contexto do domínio ou da unidade organizacional na árvore do console GPMC e selecione **Bloquear Herança**.
![[Pasted image 20241205172344.png]]
A opção Bloquear Herança é uma propriedade de um contêiner e, portanto, bloqueia todas as configurações de Política de Grupo de GPOs que se vinculam aos pais na hierarquia de Política de Grupo.

---
### Impor um link de GPO
Quando você define uma vinculação de GPO como Imposto, o GPO usa o nível mais alto de precedência. As configurações de política nesse GPO prevalecem sobre as configurações de política conflitantes em outros GPOs.
![[Pasted image 20241205172649.png]]

> [!important] Uma vinculação imposta se aplica a contêineres filho mesmo quando esses contêineres estão definidos para Bloquear Herança. A opção Imposto faz com que a política se aplique a todos os objetos dentro de seu escopo.

---
## Avaliação de precedência
Para facilitar a avaliação da precedência do GPO, basta selecionar uma UO ou um domínio e selecionar a guia **Herança de Política de Grupo**.

Essa guia exibe a precedência resultante dos GPOs levando em conta a vinculação de GPO, a ordem de vinculação, o bloqueamento de heranças e a imposição de vinculações.
![[Pasted image 20241205172954.png]]

>[!important] Essa guia não leva em conta políticas vinculadas a um site, segurança de GPO ou filtragem WMI.

---
## GPOs voltadas a segurança















































