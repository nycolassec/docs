These are also called “well-known ports”.

|**Port**|**Description**|
|---|---|
|**20/21**|File Transfer Protocol (FTP)|
|**22**|Secure Shell (SSH)|
|**23**|Telnet remote login service|
|**25**|Simple Mail Transfer Protocol (SMTP)|
|**53**|Domain Name System (DNS)|
|**67/68**|Dynamic Host Configuration Protocol (DHCP)|
|**69**|Trivial File Transfer Protocol (TFTP)|
|**80**|Hypertext Transfer Protocol (HTTP)|
|**110**|Post Office Protocol version 3 (POP3)|
|**123**|Network Time Protocol (NTP)|
|**143**|Internet Message Access Protocol (IMAP)|
|**161/162**|Simple Network Management Protocol (SNMP)|
|**443**|HTTP Secure (HTTPS)|

In Linux, log files can be categorized as:
- Application logs
- Event logs
- Service logs
- System logs


The table lists a few popular Linux log files and their functions

| **Linux Log File**                                | **Description**                                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **/var/log/messages**                             | - This directory contains generic computer activity logs.<br>- It is mainly used to store informational and non-critical system messages.<br>- In Debian-based computers, /var/log/syslog directory serves the same purpose.                                                                                                                                 |
| **/var/log/auth.log**                             | - This file stores all authentication-related events in Debian and Ubuntu computers.<br>- Anything involving the user authorization mechanism can be found in this file.                                                                                                                                                                                     |
| **/var/log/secure**                               | - This directory is used by RedHat and CentOS computers instead of /var/log/auth.log.<br>- It also tracks sudo logins, SSH logins, and other errors logged by SSSD.                                                                                                                                                                                          |
| **/var/log/boot.log**                             | - This file stores boot-related information and messages logged during the computer startup process.                                                                                                                                                                                                                                                         |
| **/var/log/dmesg**                                | - This directory contains kernel ring buffer messages.<br>- Information related to hardware devices and their drivers is recorded here.<br>- It is very important because, due to their low-level nature, logging systems such as syslog are not running when these events take place and therefore are often unavailable to the administrator in real-time. |
| **/var/log/kern.log**                             | - This file contains information logged by the kernel.                                                                                                                                                                                                                                                                                                       |
| **/var/log/cron**                                 | - Cron is a service used to schedule automated tasks in Linux and this directory stores its events.<br>- Whenever a scheduled task (also called a cron job) runs, all its relevant information including execution status and error messages are stored here.                                                                                                |
| **/var/log/mysqld.log** or **/var/log/mysql.log** | - This is the MySQL log file.<br>- All debug, failure and success messages related to the mysqld process and mysqld_safe daemon are logged here.<br>- RedHat, CentOS and Fedora Linux distributions store MySQL logs under /var/log/mysqld.log, while Debian and Ubuntu maintain the log in /var/log/mysql.log file.                                         |

|**Task**|**Arch**|**Debian / Ubuntu**|
|---|---|---|
|Install a package by name|**pacman -S**|**apt install**|
|Remove a package by name|**pacman -Rs**|**apt remove**|
|Update a local package|**pacman -Syy**|**apt-get update**|
|Upgrade all currently installed packages|**pacman -Syu**|**apt-get upgrade**|




















