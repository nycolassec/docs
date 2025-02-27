A camada 2 do modelo OSI prepara os dados da rede para serem transmitidos para o rede física.

- Permite que camadas superiores acessem a mídia. O protocolo de camada superior desconhece o meio no qual os dados serão enviados.
- Controla como os dados serão enviados e recebidos na mídia.
- Recebe dados encapsulados e os redireciona para o protocolo de camada superior adequado.
- Executa detecção de erros e rejeita qualquer frame corrompido.

---
# Data Link Sublayers
Os padrões IEEE 802 LAN/MAN especificam as seguintes duas subcamadas para L2:
- **Logical Link Control (LLC)** - Essa subcamada IEEE 802.2 se comunica com software de rede das camadas superiores e com o dispositivo de hardware das camadas inferiores. Coloca no frame informações que identificam qual protocolo da camada de rede está sendo usado. Essa informação permite múltiplos protocolos de camada de rede usarem a mesma interface de rede e mídia.
- **Media Access Control (MAC)** - Implementa essa sub camada no hardware (IEEE 802.3, 802.11, or 802.15). É responsável pelo encapsulamento dos dados e controle de acesso a rede. Provê endereçamento e é integrado com várias tecnologias de camada física.

![Data Link Sublayer](../../Images/Redes-OSI-L2.png)

## Subcamada MAC
A subcamada MAC fornece encapsulamento de dados e acesso a mídia:
- **Delimitação de quadros** - O processo de enquadramento fornece delimitadores importantes para identificar campos dentro de um quadro. Esses bits delimitadores fornecem sincronização entre os nós de transmissão e recebimento.
- **Endereço** - fornece endereçamento de origem e destino para o transporte da estrutura da camada 2 entre os dispositivos no mesmo meio compartilhado.
- **Detecção de erros** - inclui um trailer usado para detectar erros de transmissão.

A subcamada MAC também fornece controle de acesso à mídia, permitindo que vários dispositivos se comuniquem em um meio compartilhado (semi-duplex). As comunicações full-duplex não requerem controle de acesso.
### Encapsulamento de dados
O encapsulamento de dados IEE 802.3 inclui o seguinte:
- Frame Ethernet - Essa é a estrutura interna do frame ethernet
- Ethernet Addressing - O Frame Ethernet inclui tanto o MAC de origem quanto o de destino para o envio do Frame da NIC Ethernet para a NIC Ethernet na mesma rede.
- Ethernet Error Detection - O Frame Ethernet inclui o frame check sequence usado para detecção de erro.
### Acesso a mídia
Conforme mostrado na figura, a subcamada MAC IEEE 802.3 inclui as especificações para diferentes padrões de comunicação Ethernet em vários tipos de mídia, incluindo cobre e fibra.

Cada ambiente de rede que os pacotes encontrem entre a rede local e a rede destino terão características diferentes. A subcamada MAC resolve esse problema. Com links seriais o método de acesso consiste em uma conexão direta entre dois dispositivos , normalmente dois roteadores. Portanto eles não requerem as técnicas em pregadas na subcamada MAC IEEE 802.

Em cada salto ao longo do caminho, o roteador irá encapsular o pacote em um frame apropriado. Um controle de acesso a mídia adequado é usado para acessar cada link.

---
# Métodos de controle de acesso
Redes multi acesso precisam de regras que definem como os dispositivos irão compartilhar  a mídia física. Há dois métodos de controle de acesso para compartilhar a mídia:
- Contention-based access
- Controlled access

## Contention-based access
Aqui todos os nodes irão operar em half-duplex, competindo pelo uso da mídia. No entanto somente um dispositivo pode enviar  por vez. Então há um processo se mais de um dispositivo transmitir ao mesmo tempo. Alguns exemplos incluem:
- Acesso múltiplo com detecção de colisão (CSMA/CD) usado em LANs Ethernet de topologia de barramento herdada
- Acesso múltiplo por operadora com prevenção de colisão (CSMA / CA) usado em LANs sem fio

Exemplos de redes contention-based access  são:
- Wireless LAN (uses CSMA/CA)
- Legacy bus-topology Ethernet LAN (uses CSMA/CD)
- Legacy Ethernet LAN using a hub (uses CSMA/CD)
## Controlled-based Access
Aqui cada node tem um tempo para usar a o meio. Esse tipo de rede legada é ineficiente pois cada node tem que esperar para sua vez para usar o meio. Alguns exemplos de desse tipo de rede são:
- Legacy Toke Ring
- Legacy ARCNET

---
































































