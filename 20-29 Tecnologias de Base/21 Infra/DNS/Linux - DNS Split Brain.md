o `DNS Split Brain` permite que façamos resoluções dinâmicas de acordo com alguma política que definirmos.

#### Configurações inciais
É preciso fazer algumas configurações iniciais no bind9, devemos definir essas configurações em ==/etc/named.conf==.
##### /etc/named.conf
```c
options {
	listen-on port 53 { any; }; /* Permite que o servidor escute em qualquer interface*/
	allow-query { any; };       /* Permite requições de qualquer host */
};

include "/etc/named/views.conf"; /* Arquivo onde ficará as views */
```

#### Zonas
Criamos então dois arquivos de zona, eles resolvem os mesmos endereços, mas um resolve para endereços da rede interna e a outro para a externa. Eu costumo armazena-las em ==/etc/named/zones/==
##### /etc/named/zones/wsc.com.internal.db
```c
$TTL 3H
@   IN  SOA @ root.wsc.com. (
        0           ;Serial
        1D          ;Refresh
        1H          ;Retry
        1W          ;Expire
        3H )        ;Minimum TTL
                       
        IN  NS  @
        IN  A   10.0.10.10

www     IN  A   10.0.10.10
server  IN  A   10.0.10.20
```
##### /etc/named/zones/wsc.com.external.db
```js
$TTL 3H
@   IN  SOA @ root.wsc.com. (
        0           ;Serial
        1D          ;Refresh
        1H          ;Retry
        1W          ;Expire
        3H )        ;Minimum TTL
                       
        IN  NS  @
        IN  A   172.16.30.1

www     IN  A   172.16.30.1
server   IN  A   172.16.30.1
```
#### Views
As `Views` são o cerne da configuração do `DNS Split Brain` no `bind9`, com elas definimos com base nas ACLs quem pode ou não acessar uma zona. Eu costumo armazenar as views junto com as acls em ==/etc/named/views.conf==
##### /etc/named/views.conf
```c
#### INTERNAL ####
acl internal {
	10.0.10.0/24;
	localhost;
};

view "internal" {
	match-clients { internal; };
	recursion yes;
	allow-query { internal; };
	
	zone "wsc.com" {
		type master;
		file "/etc/named/zones/wsc.com.internal.internal.db";
	};
};
#### INTERNAL ####


#### EXTERNAL ####
acl external {
	any;
};

view "external" {
	match-clients { external; };
	recursion yes;
	allow-query { external; };
	
	zone "wsc.com" {
		type master;
		file "/etc/named/zones/wsc.com.external.internal.db";
	};
};
#### EXTERNAL ####
```

