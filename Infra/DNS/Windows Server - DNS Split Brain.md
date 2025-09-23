o `DNS Split Brain` permite que façamos resoluções dinâmicas de acordo com alguma política que definirmos.
#### Primary Zone
Primeiro criamos a `Primary Zone` que irá armazenar os registros. Pode ser feito via GUI.
```powershell
Add-DnsServerPrimaryZone -Name "wsc.com" -ZoneFile "wsc.com.dns" -ReplicationScope "Domain"  # se for AD-integrated
```
#### Zone Scope
Criamos então escopos que irão diferenciar os registros. Normalmente só iremos diferenciar a rede interna das demais, então podemos criar apenas uma zona para a rede interna e deixar que a zona padrão resolva para os hosts externos.
```powershell
Add-DnsServerZoneScope -ZoneName "wsc.com" -Name "InternalScope"
```
#### Registros
Ao criar um registro devemos especificar a qual escopo ele pertence, assim podemos criar registros com nomes idênticos que irão resolver para endereços diferentes. Caso não seja especificado um escopo, o registro vai para o escopo padrão, que é o único que pode ser gerenciando via GUI.
```powershell
# Registro no scopo Internal
Add-DnsServerResourceRecord -ZoneName "wsc.com" -A -Name "www" -IPv4Address "10.0.10.10" -ZoneScope "InternalScope"

# Registro no escopo default
Add-DnsServerResourceRecord -ZoneName "empresa.com" -A -Name "www" -IPv4Address "172.16.30.10"
```
#### Políticas baseadas na rede do Client
Para definir quem terá acesso a qual escopo, podemos criar uma política baseada na rede dos clients. Primeiro precisamos criar os clients subnets.
```powershell
Add-DnsServerClientSubnet -Name "InternalSubnet" -IPv4Subnet "10.0.10.0/24"
```

Agora criamos a política, passando a condição `-ClientSubnet "EQ,InternalSubnet"`, a qual escopo e zona ela será aplicada.
```powershell
Add-DnsServerQueryResolutionPolicy -Name "InternalPolicie" -Action ALLOW `
    -ClientSubnet "EQ,InternalSubnet" `
    -ZoneScope "InternalScope,1" `
    -ZoneName "wsc.com"
```
#### Políticas baseadas na interface do Server
Aqui criamos as politicas baseadas na interface que irá receber as requisições `-ServerInterface "EQ,10.0.10.10"`.
```powershell
Add-DnsServerQueryResolutionPolicy -Name "InternalPolicie" -Action ALLOW `
    -ServerInterface "EQ,10.0.10.10" `
    -ZoneScope "InternalScope,1" `
    -ZoneName "wsc.com"
```