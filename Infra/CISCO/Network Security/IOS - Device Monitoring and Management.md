## Secure Cisco IOS Image and Configuration Files

#### Enable the IOS Image Resilience Feature
To secure the IOS image and enable Cisco IOS image resilience, use the **secure boot-image** global configuration mode command.
To take a snapshot of the router running configuration and securely archive it in persistent storage, use the **secure boot-config** global configuration mode command.
The running image and running configuration archives are not visible in the **dir** command output. Use the **show secure bootset** command to verify the existence of the archive.
```
R1(config)# secure boot-image

R1(config)# secure boot-config

R1# show secure bootset
IOS resilience router id FTX1449AJBJ
 
IOS image resilience version 15.4 activated at 12:47:09 UTC Tue Sep 22 2020
Secure archive flash0:c2900-universalk9-mz.SPA.154-3.M.bin type is image (elf) []
  file size is 103727964 bytes, run size is 103907016 bytes
  Runnable image, entry point 0x81000000, run from ram
 
IOS configuration resilience version 15.4 activated at 12:47:18 UTC Tue Sep 22 2020
Secure archive flash0:.runcfg-20200922-124717.ar type is config
configuration archive size 1683 bytes
```
#### The primary Bootset Image
Restore a primary bootset from a secure archive after the router has been tampered with, as shown in the following steps and example:
```
Router# reload
<Issue Break sequence, if necessary>
rommon 1 > dir flash0:
program load complete, entry point: 0x80803000, size: 0x1b340
Directory of flash0:
 
4    103727964  -rw-     c2900-universalk9-mz.SPA.154-3.M.bin
  
 
rommon 2 > boot flash0:c2900-universalk9-mz.SPA.154-3.M.bin <Router reboots with specified image>
Router> enable
Router# conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)# secure boot-config restore flash0:rescue-cfg
ios resilience:configuration successfully restored as flash0:rescue-cfg
 
Router(config)# end
Router# copy flash0:rescue-cfg running-config
Destination filename [running-config]?  
%IOS image resilience is already active
%IOS configuration resilience is already active
 
2182 bytes copied in 0.248 secs (8798 bytes/sec)
 
R1#
```
#### Configure Secure Copy
SCP relies on:
- SSH to secure communication
- AAA to provide authentication and authorization

**Configuring**
```
R1(config)# ip domain-name span.com
R1(config)# crypto key generate rsa general-keys modulus 2048
R1(config)# username Bob privilege 15 algorithm-type scrypt secret cisco12345
R1(config)# aaa new-model                          
R1(config)# aaa authentication login default local
R1(config)# aaa authorization exec default local  
R1(config)# ip scp server enable
```

**Copying**
```
R2# copy flash0:R2backup.cfg scp:
Address or name of remote host []? 10.1.1.1
Destination username [R2]? Bob
Destination filename [R2backup.cfg]?  
Writing R2backup.cfg  
Password: <cisco12345>
!
1381 bytes copied in 8.596 secs (161 bytes/sec)
 
R2#
```
#### Recover a router password
```
R1(config)# no service password-recovery
WARNING:
Executing this command will disable password recovery    
mechanism.
Do not execute this command without another plan for  
password recovery.
Are you sure you want to continue? [yes/no]: yes
R1(config)#
```
***
