## Promiscuous  Mode
No Hyper-V, esse modo é alcançado **ativando o modo de "Port Mirroring" ou permitindo "MAC Spoofing"**, dependendo do caso. O modo promíscuo verdadeiro, como nas interfaces físicas em Linux, não é diretamente configurável, mas essas opções têm efeito semelhante.

###### Permitir MAC Spoofing
```powershell
Set-VMNetworkAdapter -VMName "[VM NAME]" -MacAddressSpoofing On
```
###### (Opcional) Habilitar Port Mirroring
```powershell
# Caso queira enviar o tráfego dessa VM para outra
Set-VMNetworkAdapter -VMName "[VM NAME]" -PortMirroring Source

# Caso queira receber o tráfeg de outras VMs
Set-VMNetworkAdapter -VMName "[VM NAME]" -PortMirroring Destination
```
***
$$\#54 : Nycolas Ramos$$