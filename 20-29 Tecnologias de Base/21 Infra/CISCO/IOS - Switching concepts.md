## Switching forwarding methods
Layer 2 switches use one of two methods to switch frames:
**Store-and-forward switching** - This method makes a forwarding decision on frame after it has received the entire frame and checked the frame for errors using a mathematical error-checking mechanism known as a cyclic redundancy check (CRC).
**Cut-through switching** - This method begins the forwarding process after the destination MAC address of an incoming frame and the egress port have been determined.

Store-and-forward switching, as distinguished from cut-through switching, has the following two primary characteristics:
- **Error checking** - After receiving the entire frame on the ingress port, the switch compares the frame check sequence (FCS) value in the last field of the datagram against its own FCS calculations. The FCS is an error checking process that helps to ensure that the frame is free of physical and data-link errors. If the frame is error-free, the switch forwards the frame. Otherwise, the frame is dropped.
- **Automatic buffering** - The ingress port buffering process used by store-and-forward switches provides the flexibility to support any mix of Ethernet speeds. For example, handling an incoming frame traveling into a 100 Mbps Ethernet port that must be sent out a 1 Gbps interface would require using the store-and-forward method. With any mismatch in speeds between the ingress and egress ports, the switch stores the entire frame in a buffer, computes the FCS check, forwards it to the egress port buffer and then sends it.
- The store-and-forward switching method drops frames that do not pass the FCS check. Therefore, it does not forward invalid frames.

By contrast, the **cut-through switching method** may forward invalid frames because no FCS check is performed. However, cut-through switching has the ability to perform rapid frame switching. This means the switch can make a forwarding decision as soon as it has looked up the destination MAC address of the frame in its MAC address table.
The switch does not have to wait for the rest of the frame to enter the ingress port before making its forwarding decision.

Fragment free switching is a modified form of cut-through switching in which the switch only starts forwarding the frame. Fragment free switching provides better error checking than cut-through, with practically no increase in latency.
The lower latency speed of cut-through switching makes it more appropriate for extremely demanding, high-performance computing (HPC) applications that require process-to-process latencies of 10 microseconds or less.

## 1. O Funcionamento Básico e o Full-Duplex

Os switches conectam diferentes segmentos de uma rede local e usam uma **tabela de endereços MAC** (endereços físicos dos dispositivos) para saber exatamente para onde enviar cada pacote de dados (frame).

- **Eliminação de Colisões:** Por padrão, as portas dos switches modernos tentam operar em modo **Full-Duplex**. Isso significa que o dispositivo conectado pode enviar e receber dados ao mesmo tempo. Como a transmissão e a recepção usam caminhos separados, as colisões de pacotes de dados são eliminadas.
    
- **Largura de Banda Dedicada:** Cada porta fornece a velocidade total de banda para o dispositivo conectado, em vez de compartilhar essa velocidade com a rede inteira (como faziam os antigos _hubs_). É uma exigência para conexões de 1 Gbps ou superiores.
    

## Características que Aliviam a Congestão

A imagem destaca quatro características fundamentais que os switches possuem para evitar gargalos:

### ⚡ Altas Velocidades de Porta (Fast port speeds)

As portas dos switches variam de velocidade dependendo da sua função na rede:

- **Camada de Acesso (onde os PCs/dispositivos se conectam):** Geralmente operam a 100 Mbps ou 1 Gbps.
    
- **Camada de Distribuição e Núcleo (Core/Data Centers):** Ligam partes maiores da rede e precisam de muito mais vazão, operando a 10 Gbps, 40 Gbps ou até 100 Gbps para dar conta do acúmulo de dados de vários usuários.
    

### 🧠 Comutação Interna Rápida (Fast internal switching)

Não adianta ter portas rápidas se o "cérebro" do switch for lento. Eles utilizam barramentos internos de alta velocidade ou memória compartilhada para processar e encaminhar os dados quase instantaneamente de uma porta para a outra.

### 💾 Grandes Buffers de Memória (Large frame buffers)

O _buffer_ é uma memória temporária. Se uma porta de 1 Gbps está enviando dados para um dispositivo conectado a uma porta mais lenta de 100 Mbps, o switch armazena temporariamente os pacotes excedentes nessa memória em vez de descartá-los (o que causaria perda de dados e lentidão devido à necessidade de retransmissão).

### 🎛️ Alta Densidade de Portas (High port density)

Refere-se ao número de portas físicas disponíveis em um único switch. Ter switches com muitas portas (como 48 portas em vez de várias unidades de 24) traz duas vantagens:

1. **Redução de custos:** Comprar menos equipamentos físicos sai mais barato.
    
2. **Desempenho:** Mantém o tráfego local dentro do mesmo switch, evitando que os dados precisem viajar por outros cabos e switches da rede para chegar ao destino, o que diminui a chance de lentidão.
    

Ficou claro como esses componentes evitam que a rede fique "travada"? Se quiser, podemos aprofundar em como o switch monta essa tabela de endereços MAC ou em algum desses pontos específicos!
