```ios
# Modo duplex
SW(config-if)# duplex full

# Configura a velocidade da interface
SW(config-if)# speed 100

# Configura o modo mdix
SW(config-if)# mdix auto
```
***
## Verificação de switch
```ios
SW> enable

# Status e a configuração da interface
SW# show interfaces [interface-id]

# Configuração atual de inicialização
SW# show startup-config

# Configuração atual em execução
SW# show running-config

# Informações sobre o sistema de arquivos da memória flash
SW# show flash

# Status de hardware e software do sistema
SW# show version

# Histórico de comandos
SW# show history
```
***
## Erros de interface
- **Frames Runt** - Quadros Ethernet que são mais curtos do que o comprimento mínimo permitido de 64 bytes são chamados de runts. NICs com funcionamento inadequado normalmente são a causa de quadros runt em excesso, mas sua causa também se dá por colisões.
- **Giants** - Os quadros Ethernet maiores que o tamanho máximo permitido são chamados gigantes.
- **CRC errors** - Nas interfaces Ethernet e serial, os erros CRC geralmente indicam um erro de mídia ou cabo. As causas mais comuns incluem interferência elétrica, conexões soltas ou danificadas ou cabeamento incorreto. Se houver muitos erros de CRC, há muito ruído no link, e você deve verificar o cabo. Você também deve procurar as causas do ruído e eliminá-las.

“Output errors” refere-se à soma de todos os erros que impediram a transmissão final de datagramas para fora da interface que está sendo examinada. Os erros de saída relatados do comando **show interfaces** incluem o seguinte:

- **Collisions** - Colisões em operações half-duplex são normais. No entanto, você jamais verá colisões em uma interface configurada para a comunicação de duplex completo.
- **Late collisions** - Uma colisão tardia refere-se a uma colisão que ocorre após a transmissão de 512 bits do quadro. Comprimentos excessivos de cabos são a causa mais comum de colisões tardias. Outra causa comum é a configuração incorreta de duplex. Por exemplo, você pode ter uma extremidade de uma conexão configurada para full-duplex e a outra para half-duplex. Você veria colisões tardias na interface configurada para half-duplex. Nesse caso, você deve configurar a mesma configuração duplex em ambas as extremidades. Uma rede corretamente projetada e configurada nunca deve ter colisões tardias.