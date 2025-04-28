A ideia do **Etherchannel** seria fazer um **bundle** dos links/somar os links físicos em um único link lógico.

Isso não apenas nos permite lidar com grandes volumes de tráfego, mas também proporciona redundância e balanceamento de carga, essenciais para manter nossa rede operacional mesmo em situações de falha.

Quando fazemos um Etherchannel, o STP considera como se fosse uma somente uma interface.

![[redes-infra-images-03.png]]

---
---
## Configuração
---
As interfaces usadas para fazer o bundle, dever ter as mesmas configurações. Sendo assim podemos configurar até 8  portas em um **Etherchannel**.

Também é recomendado que seja configurado em um número par de portas, apesar de poder ser configurado em um número ímpar de portas, o balanceamento não fica tão bom.

![[redes-infra-images-04.png]]
Podemos notar que uma das porta do ``SW2``, foi bloqueada pelo ``STP``.

É ideal que se faça a configuração com as interfaces desligadas.
```SW
(config)# interface range gigabitEthernet 0/1-2

(config-if-range)# shutdow
```
![[redes-infra-images-05.png]]


Os dois Switches tem de estar com as portas como **trunk**, e com o encapsulamento certo.
```SW
(config-if-range)# switchport trunk encapsulation dot1q
(config-if-range)# switchport mode trunk
```

Então definimos um grupo para rodar o **Etherchannel**, é aceito do número 1 ao 6.
```SW
(config-if-range)# channel-group 1 mode active
```

O modo define qual protocolo ir emos usar.

### Modos
---

| Modo      | Protocolo |
| --------- | --------- |
| active    | LACP      |
| passive   | LACP      |
| auto      | PAgP      |
| desirable | PAgP      |
| on        |           |
No LACP o modo **passive** apenas escuta as mensagens de negociação enquanto o modo **active** envia as mensagens de negociação. Para iniciarmos o **Etherchannel** pelo menos um grupo precisa ser **active**. É definido pelo padrão IEEE 802.3ad

No PAgP o modo **desirable** envia as mensagens de negociação, enquanto o modo **auto** escuta as mensagens de negociação. 

normalmente se usa o padrão aberto **LACP**.


Agora ligamos as interfaces de novo.
```SW
(config-if-range)# no shutdown
```

### Vendo informações
---
```SW
# show spanning-tree
```
![[redes-infra-images-06.png]]

```SW
# show etherchannel summary
```
![[redes-infra-images-07.png]]

Como as duas portas estão com **P**, elas estão `in port-channel`.

Caso alguma interface seja desligada, aparecerá como **D** de `down`


```SW
# show ip interface brief
```
![[redes-infra-images-08.png]]
![[redes-infra-images-09.png]]

Esse comando mostra todas as interfaces , mas será destacado a parte do **etherchannel**

### Load balance
---
```SW
# show etherchannel load-balance
```
![[redes-infra-images-10.png]]
Aqui o load balance está associado ao **source mac address**.

O mais ideal é que seja baseado no endereço IP de destino, assim quando ele alternará qual link é usado com base no endereço IP ao qual a informação se destina.

```SW
(config)# port-channel load-balance ?
```
![[redes-infra-images-11.png]]

```SW
(config)# port-channel load-balance src-dst-ip
```
Aqui mudamos o modo do nosso load balance

```SW
# show etherchannel load-balance
```
Mostrará qual o modo do nosso **load balance**

## Adicionando mais switches
---

![[redes-infra-images-12.png]]

As interfaces do `SW1` que se conectam ao `SW3`, para obedecermos ao "padrão", tem de estar em modo `trunk`, mesmo não sendo obrigatório.

```SW1
(config)# interface range fastEthernet 0/1-2

(config-if-range)# switchport mode trunk

(config-if-range)# shutdown

(config-if-range)# channel-group 2 mode on
```

Agora fazemos as mesmas configurações para o `SW3`
```SW3
(config)# interface range fastEthernet 0/1-2

(config-if-range)# switchport mode trunk

(config-if-range)# shutdown

(config-if-range)# channel-group 2 mode on
```

Aqui não colocamos nenhum protocolo, pois não precisa de protocolo para subir o ``etherchannel``.










