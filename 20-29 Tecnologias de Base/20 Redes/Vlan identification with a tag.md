AS shown in the figure the VLAN tag control information filed consists of a type filed, a priority field, a canonical format Identifier field, and VLAN ID field:
- Type - A 2 byte value called the tag protocol ID (TPID) value. For Ethernet, it is set to hexadecimal 0x8100.
- User priority - A 3 bit value that supports level or service implementation.
- Canonical format identifier (CFI) - A 1 bit identifier that enables token ring frames to be carried across Ethernet links.
- VLAN ID (VID) - A 12 bit VLAN identification number that supports up to 4096 VLAN IDs.
After the switch inserts the tag control information fields, ir recalculates the FCS values and inserts the new FCS into the frame.


## Native VLANs and 802.1Q Tagging
### 1. O Conceito Base (Introdução)

- **Padrão 802.1Q e a VLAN Nativa:** Este padrão determina que toda porta de tronco (uma porta que conecta switches e carrega tráfego de várias redes virtuais) deve ter uma "VLAN nativa". O padrão de fábrica para essa VLAN é a **VLAN 1**.
    
- **Função:** A principal função da VLAN nativa é receber qualquer quadro de dados (frame) que chegue à porta **sem uma etiqueta (untagged)**.
    
- **Tráfego de Gerenciamento:** O texto cita que a comunicação de gerenciamento entre os switches geralmente é feita sem etiquetas, trafegando, portanto, através da VLAN nativa.
    

### 2. Quadros Marcados na VLAN Nativa (Tagged Frames on the Native VLAN)

Esta seção explica o comportamento do switch caso ele receba pacotes que tenham uma etiqueta de VLAN (_VLAN tag_) correspondente à VLAN nativa:

- **Descarte de Pacotes (Drop):** O tráfego de controle na VLAN nativa **não deve** ter etiqueta. Se a porta do switch receber um quadro marcado com o _mesmo número de identificação_ (VLAN ID) da sua VLAN nativa, o switch entenderá isso como um erro e **descartará o quadro**.
    
- **Boas Práticas na Cisco:** Por causa dessa regra, ao configurar switches Cisco, é necessário garantir que os dispositivos conectados não enviem pacotes marcados para a VLAN nativa.
    
- **Compatibilidade com Terceiros:** O texto ressalta que equipamentos de outras marcas (como telefones IP, servidores, roteadores e switches não-Cisco) podem ter comportamentos diferentes e suportar quadros marcados na VLAN nativa.
    

### 3. Quadros Não Marcados na VLAN Nativa (Untagged Frames on the Native VLAN)

A última seção detalha como o switch processa os pacotes que chegam corretamente sem etiqueta:

- **Encaminhamento Padrão:** Quando o switch Cisco recebe um quadro sem etiqueta, ele o direciona para a VLAN nativa (embora o texto note que tráfego sem etiqueta em links de tronco é incomum em redes muito bem projetadas).
    
- **Possibilidade de Descarte:** Se não houver nenhum dispositivo conectado ou nenhuma outra porta de tronco associada àquela VLAN nativa, o quadro será descartado por não ter um destino válido.
    
- **O papel do PVID (Port VLAN ID):** O switch usa um valor chamado PVID para saber qual é a VLAN nativa daquela porta. O PVID sempre terá o valor do ID da VLAN nativa.
    
- **Exemplo Prático:** Se o administrador de rede configurar a VLAN nativa como **VLAN 99**, o PVID da porta será **99**. A partir desse momento, todo pacote sem etiqueta que entrar ou sair será encaminhado para a VLAN 99. Se nenhuma alteração for feita, o PVID continuará sendo o padrão: **VLAN 1**.