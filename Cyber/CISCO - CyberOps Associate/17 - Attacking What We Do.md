**DNS Open Resolver Attacks**
Many organizations use the services of publicly open DNS servers such as GoogleDNS (8.8.8.8) to provide responses to queries. This type of DNS server is called an open resolver. A DNS open resolver answers queries from clients outside of its administrative domain. DNS open resolvers are vulnerable to multiple malicious activities described in the table.

|**DNS Resolver Vulnerabilities**|**Description**|
|---|---|
|**DNS cache poisoning attacks**|Threat actors send spoofed, falsified record resource (RR) information to a DNS resolver to redirect users from legitimate sites to malicious sites. DNS cache poisoning attacks can all be used to inform the DNS resolver to use a malicious name server that is providing RR information for malicious activities.|
|**DNS amplification and reflection attacks**|Threat actors use DoS or DDoS attacks on DNS open resolvers to increase the volume of attacks and to hide the true source of an attack. Threat actors send DNS messages to the open resolvers using the IP address of a target host. These attacks are possible because the open resolver will respond to queries from anyone asking a question.|
|**DNS resource utilization attacks**|A DoS attack that consumes the resources of the DNS open resolvers. This DoS attack consumes all the available resources to negatively affect the operations of the DNS open resolver. The impact of this DoS attack may require the DNS open resolver to be rebooted or services to be stopped and restarted.|

**DNS Stealth Attacks**
To hide their identity, threat actors also use the DNS stealth techniques described in the table to carry out their attacks.

| **DNS Stealth Techniques**       | **Description**                                                                                                                                                                                                                                                                                                   |
| -------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Fast Flux**                    | Threat actors use this technique to hide their phishing and malware delivery sites behind a quickly-changing network of compromised DNS hosts. The DNS IP addresses are continuously changed within minutes. Botnets often employ Fast Flux techniques to effectively hide malicious servers from being detected. |
| **Double IP Flux**               | Threat actors use this technique to rapidly change the hostname to IP address mappings and to also change the authoritative name server. This increases the difficulty of identifying the source of the attack.                                                                                                   |
| **Domain Generation Algorithms** | Threat actors use this technique in malware to randomly generate domain names that can then be used as rendezvous points to their command and control (C&C) servers.                                                                                                                                              |

**DNS Domain Shadowing Attacks**
Domain shadowing involves the threat actor gathering domain account credentials in order to silently create multiple sub-domains to be used during the attacks. These subdomains typically point to malicious servers without alerting the actual owner of the parent domain.



**DHCP Spoofing Attack**
A DHCP spoofing attack occurs when a rogue DHCP server is connected to the network and provides false IP configuration parameters to legitimate clients. A rogue server can provide a variety of misleading information:
- **Wrong default gateway** - Threat actor provides an invalid gateway, or the IP address of its host to create a MiTM attack. This may go entirely undetected as the intruder intercepts the data flow through the network.
- **Wrong DNS server** - Threat actor provides an incorrect DNS server address pointing the user to a malicious website.
- **Wrong IP address** - Threat actor provides an invalid IP address, invalid default gateway IP address, or both. The threat actor then creates a DoS attack on the DHCP client.


Server connection logs can often reveal information about the type of scan or attack. The different types of connection status codes are listed here:
- **Informational 1xx** - This is a provisional response, consisting only of the Status-Line and optional headers. It is terminated by an empty line. There are no required headers for this class of status code. Servers MUST NOT send a 1xx response to an HTTP/1.0 client except under experimental conditions.
- **Successful 2xx** - The client’s request was successfully received, understood, and accepted.
- **Redirection 3xx** - Further action must be taken by the user agent to fulfill the request. A client SHOULD detect infinite redirection loops, because these loops generate network traffic for each redirection.
- **Client Error 4xx** - For cases in which the client seems to have erred. Except when responding to a HEAD request, the server SHOULD include an entity containing an explanation of the situation, and if it is temporary. User agents SHOULD display any included entity to the user.
- **Server Error 5xx** - For cases where the server is aware that it has erred, or it cannot perform the request. Except when responding to a HEAD request, the server SHOULD include an entity containing an explanation of the error situation, and if it is temporary. User agents SHOULD display any included entity to the user.


















































































































































































































































