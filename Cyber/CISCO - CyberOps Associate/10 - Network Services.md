## DHCP

DHCP can allocate IP addresses for a configurable period of time, called a lease period.

One important difference is that DHCPv6 does not provide a default gateway address. This can only be obtained dynamically from the Router Advertisement message of the router.

The offer message contains the IPv4 address and subnet mask to be assigned, the IPv4 address of the DNS server, and the IPv4 address of the default gateway.

If the offer is no longer valid, then the selected server responds with a DHCP negative acknowledgment (DHCPNAK) message.

DHCPv6 has a set of messages that is similar to those for DHCPv4. The DHCPv6 messages are SOLICIT, ADVERTISE, INFORMATION REQUEST, and REPLY.

![[Pasted image 20250218082940.png]]

## DNS
![[Pasted image 20250218090017.png]]

 If a DNS response exceeds 512 bytes, such as when Dynamic DNS (DDNS) is used, TCP port 53 is used to handle the message.

Dynamic DNS (DDNS) allows a user or organization to register an IP address with a domain name as in DNS.

WHOIS is a TCP-based protocol that is used to identify the owners of internet domains through the DNS system.


## NAT
![[Pasted image 20250218100100.png]]

## Email

The server starts the POP3 service by passively listening on TCP port 110 for client connection requests.


## HTTP 


The five status code groups are:
- **1xx** - Informational
- **2xx** - Success
- **3xx** - Redirection
- **4xx** - Client Error
- **5xx** - Server Error





















































































































































































