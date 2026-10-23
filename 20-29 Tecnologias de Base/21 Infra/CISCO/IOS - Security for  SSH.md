We can configure a cisco device to support SSH using the following six steps:

**Configure a unique device hostname**, **Configure the IP domain name**, **Generate a key to encrypt SSH traffic**, **Verify or create a local database entry**, **Authenticate against the local database** e **Enable vty inbound SSH sessions**.

```ios
Router# configure terminal
Router(config)# hostname R1
R1(config)# ip domain-name span.com
R1(config)# crypto key generate rsa general-keys modulus 1024
The name for the keys will be: Rl.span.com % The key modulus size is 1024 bits
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
Dec 13 16:19:12.079: %SSH-5-ENABLED: SSH 1.99 has been enabled
R1(config)#
R1(config)# username Bob secret cisco
R1(config)# line vty 0 4
R1(config-line)# login local
R1(config-line)# transport input ssh
R1(config-line)# exit
R1(config)#
```

To verify SSH and display the generated keys
```ios
R1# show crypto key mypubkey rsa
```