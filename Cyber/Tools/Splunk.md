### Detecting User/Domain Recon
**Timeframe**: `earliest=1690447949 latest=1690450687`

```
index=main source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational" EventID=1 earliest=1690447949 latest=1690450687
| search process_name IN (arp.exe,chcp.com,ipconfig.exe,net.exe,net1.exe,nltest.exe,ping.exe,systeminfo.exe,whoami.exe) OR (process_name IN (cmd.exe,powershell.exe) AND process IN (*arp*,*chcp*,*ipconfig*,*net*,*net1*,*nltest*,*ping*,*systeminfo*,*whoami*))
| stats values(process) as process, min(_time) as _time by parent_process, parent_process_id, dest, user
| where mvcount(process) > 3
```
- **`Time Ranger Filter (earliest e latest)`** - Restringe a faixa de tempo de eventos àqueles que ocorrem entre os registros de data e hora do UNIX 1690447949 e 1690450687.
- **`Statistics (stats)`** - É usado para agregar eventos  baseado em algum campo. Nesse caso `by parent_process, parent_process_id, dest, user`, então serão agrupados os eventos que compartilham o mesmo valor nessas propriedades.
	- **`values(process) as process`** - Armazena os valores exclusivo do campo process como campo multi valor chamado process.
	- **`min(_time) as time`** - Captura o tempo mais antigo que ocorreu em cada grupo
- **`Process Count mvcount(process)`** - Filtra os resultados para incluir somente onde o número de processos forem maiores que 3.
***
### Detecting Recon By Targeting BloodHound
**Timeframe**: `earliest=1690447949 latest=1690450687`

```
index=main earliest=1690195896 latest=1690285475 source="WinEventLog:SilkService-Log"
| spath input=Message 
| rename XmlEventData.* as * 
| table _time, ComputerName, ProcessName, ProcessId, DistinguishedName, SearchFilter
| sort 0 _time
| search SearchFilter="*(samAccountType=805306368)*"
| stats min(_time) as _time, max(_time) as maxTime, count, values(SearchFilter) as SearchFilter by ComputerName, ProcessName, ProcessId
| where count > 10
| convert ctime(maxTime)
```
- `Path Extraction`: The `spath` command is used to extract fields from the `Message` field, which likely contains structured data such as `XML` or `JSON`. The `spath` command automatically identifies and extracts fields based on the data structure.
- `Field Renaming`: The `rename` command is used to rename fields that start with `XmlEventData.` to the equivalent field names without the `XmlEventData.` prefix. This is done for easier reference to the fields in later stages of the search.
- `Tabulating Results`: The `table` command is used to display the results in a tabular format with the following columns: `_time`, `ComputerName`, `ProcessName`, `ProcessId`, `DistinguishedName`, and `SearchFilter`. The `table` command only includes these fields in the output.
- `Sorting`: The `sort` command is used to sort the results based on the `_time` field in ascending order (from oldest to newest). The `0` argument means that there is no limit on the number of results to sort.
- `Search Filter`: The search command is used to filter the results to only include events where the `SearchFilter` field contains the string `*(samAccountType=805306368)*`. This step is looking for events related to LDAP queries with a specific filter condition.
- `Statistics`: The `stats` command is used to aggregate events based on the fields `ComputerName`, `ProcessName`, and `ProcessId`. For each unique combination of these fields, the search calculates the following statistics:
    - `min(_time) as _time`: The earliest time (`_time`) that an event occurred within each group.
    - `max(_time) as maxTime`: The latest time (`_time`) that an event occurred within each group.
    - `count`: The number of events within each group.
    - `values(SearchFilter) as SearchFilter`: All unique values of the `SearchFilter` field within each group.
- `Filtering by Event Count`: The `where` command is used to filter the results to only include those where the `count` field is greater than `10`. This step is looking for instances where the same process on the same computer made more than ten search queries with the specified filter condition.
- `Time Conversion`: The `convert` command is used to convert the `maxTime` field from Unix timestamp format to human-readable format (`ctime`).
***
### Detecting Password Spraying
**Timeframe**: `earliest=1690280680 latest=1690289489`

```
index=main earliest=1690280680 latest=1690289489 source="WinEventLog:Security" EventCode=4625
| bin span=15m _time 
| stats values(user) as Users, dc(user) as dc_user by src, Source_Network_Address, dest, EventCode, Failure_Reason
```
- `Time Binning`: The `bin` command is used to create `time buckets of 15 minutes` duration for each event based on the `_time` field.
- `Statistics`: The `stats` command is used to aggregate events based on the fields `src`, `Source_Network_Address`, `dest`, `EventCode`, and `Failure_Reason`. For each unique combination of these fields, the search calculates the following statistics:
    - `values(user) as Users`: All unique values of the `user` field within each group.
    - `dc(user) as dc_user`: The distinct count of unique values of the `user` field within each group. This represents the number of different users associated with the failed logon attempts in each group.
***
### Detecting Responder-like Attacks
```shell-session
index=main earliest=1690290078 latest=1690291207 EventCode=22 
| table _time, Computer, user, Image, QueryName, QueryResults
```
[Sysmon Event ID 22](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90022) can also be utilized to track DNS queries associated with non-existent/mistyped file shares.

```shell-session
index=main earliest=1690290814 latest=1690291207 EventCode IN (4648) 
| table _time, EventCode, source, name, user, Target_Server_Name, Message
| sort 0 _time
```
***
#### Detecting Kerberoasting/AS-REProasting

**Benign TGS Requests**
```
index=main earliest=1690388417 latest=1690388630 EventCode=4648 OR (EventCode=4769 AND service_name=iis_svc) 
| dedup RecordNumber 
| rex field=user "(?<username>[^@]+)"
| table _time, ComputerName, EventCode, name, username, Account_Name, Account_Domain, src_ip, service_name, Ticket_Options, Ticket_Encryption_Type, Target_Server_Name, Additional_Information
```
- `| dedup RecordNumber`: Removes duplicate events based on the `RecordNumber` field.
- `| rex field=user "(?<username>[^@]+)"`: Extracts the `username` portion of the `user` field using a regular expression and stores it in a new field called `username`.

**Detecting Kerberoasting - SPN Querying**
```
index=main earliest=1690448444 latest=1690454437 source="WinEventLog:SilkService-Log" 
| spath input=Message 
| rename XmlEventData.* as * 
| table _time, ComputerName, ProcessName, DistinguishedName, SearchFilter 
| search SearchFilter="*(&(samAccountType=805306368)(servicePrincipalName=*)*"
```
 
 **Detecting Kerberoasting - TGS Requests**
```shell-session
index=main earliest=1690450374 latest=1690450483 EventCode=4648 OR (EventCode=4769 AND service_name=iis_svc)
| dedup RecordNumber
| rex field=user "(?<username>[^@]+)"
| bin span=2m _time 
| search username!=*$ 
| stats values(EventCode) as Events, values(service_name) as service_name, values(Additional_Information) as Additional_Information, values(Target_Server_Name) as Target_Server_Name by _time, username
| where !match(Events,"4648")
```
- `| dedup RecordNumber`: Removes duplicate events based on the `RecordNumber` field.
- `| rex field=user "(?<username>[^@]+)"`: Extracts the `username` portion of the `user` field using a regular expression and stores it in a new field called `username`.
- `| search username!=*$`: Filters out events where the `username` field ends with a `$`.
- `| where !match(Events,"4648")`: Filters out events that have the value `4648` in the Events field.

**Detecting Kerberoasting Using Transactions - TGS Requests**
```
index=main earliest=1690450374 latest=1690450483 EventCode=4648 OR (EventCode=4769 AND service_name=iis_svc)
| dedup RecordNumber
| rex field=user "(?<username>[^@]+)"
| search username!=*$ 
| transaction username keepevicted=true maxspan=5s endswith=(EventCode=4648) startswith=(EventCode=4769) 
| where closed_txn=0 AND EventCode = 4769
| table _time, EventCode, service_name, username
```
- **`| transaction username keepevicted=true maxspan=5s endswith=(EventCode=4648) startswith=(EventCode=4769)`**: Groups events into `transactions` based on the `username` field.
	- **`keepevicted=true`** option includes events that do not meet the transaction criteria.
	- **`maxspan=5s`** option sets the maximum time duration of a transaction to 5 seconds.
	- **`endswith=(EventCode=4648)`** and **`startswith=(EventCode=4769)`** options specify that transactions should start with an event with `EventCode 4769` and end with an event with `EventCode 4648`.
- **`| where closed_txn=0 AND EventCode = 4769`**: Filters the results to only include transactions that are not closed (`closed_txn=0`) and have an `EventCode` of `4769`.
- **`| table _time, EventCode, service_name, username`**: Displays the remaining events in tabular format with the specified fields.

##### AS-REPRoasting
**Detecting AS-REPRoasting - Querying Accounts With Pre-Auth Disabled**
**Timeframe**: `earliest=1690392745 latest=1690393283`
```
index=main earliest=1690392745 latest=1690393283 source="WinEventLog:SilkService-Log" 
| spath input=Message 
| rename XmlEventData.* as * 
| table _time, ComputerName, ProcessName, DistinguishedName, SearchFilter 
| search SearchFilter="*(samAccountType=805306368)(userAccountControl:1.2.840.113556.1.4.803:=4194304)*"
```

**Detecting AS-REPRoasting - TGT Requests For Accounts With Pre-Auth Disabled**
**Timeframe**: `earliest=1690392745 latest=1690393283`
```shell-session
index=main earliest=1690392745 latest=1690393283 source="WinEventLog:Security" EventCode=4768 Pre_Authentication_Type=0
| rex field=src_ip "(\:\:ffff\:)?(?<src_ip>[0-9\.]+)"
| table _time, src_ip, user, Pre_Authentication_Type, Ticket_Options, Ticket_Encryption_Type
```
- **`| rex field=src_ip "(\:\:ffff\:)?(?<src_ip>[0-9\.]+)"`** : Uses a regular expression to extract the `src_ip` (source IP address) field.
- **`| table _time, src_ip, user, Pre_Authentication_Type, Ticket_Options, Ticket_Encryption_Type`** :  Displays the remaining events in tabular format with the specified fields.
***
### Detecting Pass-the-Hash
**Detecting Pass-the-Hash With Splunk**
Before we move on to reviewing the searches, please consult [this](https://blog.netwrix.com/2021/11/30/how-to-detect-pass-the-hash-attacks) source to gain a better understanding of where the search part `Logon_Process=seclogo` originated from.
**Timeframe**: `earliest=1690450689 latest=1690451116`
```
index=main earliest=1690450708 latest=1690451116 source="WinEventLog:Security" EventCode=4624 Logon_Type=9 Logon_Process=seclogo
| table _time, ComputerName, EventCode, user, Network_Account_Domain, Network_Account_Name, Logon_Type, Logon_Process
```
As already mentioned, we can enhance the search above by adding LSASS memory access to the mix as follows.

```
index=main earliest=1690450689 latest=1690451116 (source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational" EventCode=10 TargetImage="C:\\Windows\\system32\\lsass.exe" SourceImage!="C:\\ProgramData\\Microsoft\\Windows Defender\\platform\\*\\MsMpEng.exe") OR (source="WinEventLog:Security" EventCode=4624 Logon_Type=9 Logon_Process=seclogo)
| sort _time, RecordNumber
| transaction host maxspan=1m endswith=(EventCode=4624) startswith=(EventCode=10)
| stats count by _time, Computer, SourceImage, SourceProcessId, Network_Account_Domain, Network_Account_Name, Logon_Type, Logon_Process
| fields - count
```
- **`| transaction host maxspan=1m endswith=(EventCode=4624) startswith=(EventCode=10)`** : Groups related events based on the `host` field, with a maximum time span of `1` minute between the start and end events. This command is used to associate process access events targeting `lsass.exe` with remote logon events.
- **`| fields - count`** : Removes the `count` field from the results.
***
### Detecting Pass-the-Ticket
**Timeframe**: `earliest=1690451665 latest=1690451745`
```
index=main earliest=1690392405 latest=1690451745 source="WinEventLog:Security" user!=*$ EventCode IN (4768,4769,4770) 
| rex field=user "(?<username>[^@]+)"
| rex field=src_ip "(\:\:ffff\:)?(?<src_ip_4>[0-9\.]+)"
| transaction username, src_ip_4 maxspan=10h keepevicted=true startswith=(EventCode=4768)
| where closed_txn=0
| search NOT user="*$@*"
| table _time, ComputerName, username, src_ip_4, service_name, category
```
- **`| rex field=user "(?<username>[^@]+)"`** : This command extracts the `username` from the `user` field using a regular expression. It assigns the extracted value to a new field called `username`.
- **`| rex field=src_ip "(\:\:ffff\:)?(?<src_ip_4>[0-9\.]+)"`** : This command extracts the IPv4 address from the `src_ip` field, even if it's originally recorded as an IPv6 address. It assigns the extracted value to a new field called `src_ip_4`.
- **`| transaction username, src_ip_4 maxspan=10h keepevicted=true startswith=(EventCode=4768)`** : This command groups events into transactions based on the `username` and `src_ip_4` fields. A transaction begins with an event that has an `EventCode` of `4768`. The `maxspan=10h` parameter sets a maximum duration of `10` hours for a transaction. The `keepevicted=true` parameter ensures that open transactions without an ending event are included in the results.
***
### Detecting Overpass-the-Hash
```
index=main earliest=1690443407 latest=1690443544 source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational" (EventCode=3 dest_port=88 Image!=*lsass.exe) OR EventCode=1
| eventstats values(process) as process by process_id
| where EventCode=3
| stats count by _time, Computer, dest_ip, dest_port, Image, process
| fields - count
```
***
### Detecting Golden Tickets/Silver Tickets
**Detecting Golden Tickets With Splunk (Yet Another Ticket To Be Passed Approach)**
**Timeframe**: `earliest=1690451977 latest=1690452262`
```
index=main earliest=1690451977 latest=1690452262 source="WinEventLog:Security" user!=*$ EventCode IN (4768,4769,4770) 
| rex field=user "(?<username>[^@]+)"
| rex field=src_ip "(\:\:ffff\:)?(?<src_ip_4>[0-9\.]+)"
| transaction username, src_ip_4 maxspan=10h keepevicted=true startswith=(EventCode=4768)
| where closed_txn=0
| search NOT user="*$@*"
| table _time, ComputerName, username, src_ip_4, service_name, category
```

**Detecting Silver Tickets With Splunk Through User Correlation**
Let's first create a list of users (`users.csv`) leveraging `Event ID 4720 (A user account was created)` as follows

```
index=main latest=1690448444 EventCode=4720
| stats min(_time) as _time, values(EventCode) as EventCode by user
| outputlookup users.csv
```

Let's now compare the list above with logged-in users as follows.
**Timeframe**: `latest=1690545656`
```
index=main latest=1690545656 EventCode=4624
| stats min(_time) as firstTime, values(ComputerName) as ComputerName, values(EventCode) as EventCode by user
| eval last24h = 1690451977
| where firstTime > last24h
```| eval last24h=relative_time(now(),"-24h@h")```
| convert ctime(firstTime)
| convert ctime(last24h)
| lookup users.csv user as user OUTPUT EventCode as Events
| where isnull(Events)
```
- **`| lookup users.csv user as user OUTPUT EventCode as Events`** : This command performs a `lookup` using the `users.csv` file, matches the `user` field from the search results with the `user` field in the CSV file, and outputs the `EventCode` column from the CSV file as a new field called `Events`.
- **`| where isnull(Events)`** : This command filters the results to include only those where the `Events` field is null. This indicates that the user was not found in the `users.csv` file.

**Detecting Silver Tickets With Splunk By Targeting Special Privileges Assigned To New Logon**
**Timeframe**: `latest=1690545656`
````
index=main latest=1690545656 EventCode=4672
| stats min(_time) as firstTime, values(ComputerName) as ComputerName by Account_Name
| eval last24h = 1690451977 
```| eval last24h=relative_time(now(),"-24h@h") ```
| where firstTime > last24h 
| table firstTime, ComputerName, Account_Name 
| convert ctime(firstTime)
````


























































































































































































































