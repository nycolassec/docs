#### Detecting User/Domain Recon
**Timeframe**: `earliest=1690447949 latest=1690450687`

```
index=main source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational" EventID=1 earliest=1690447949 latest=1690450687
| search process_name IN (arp.exe,chcp.com,ipconfig.exe,net.exe,net1.exe,nltest.exe,ping.exe,systeminfo.exe,whoami.exe) OR (process_name IN (cmd.exe,powershell.exe) AND process IN (*arp*,*chcp*,*ipconfig*,*net*,*net1*,*nltest*,*ping*,*systeminfo*,*whoami*))
| stats values(process) as process, min(_time) as _time by parent_process, parent_process_id, dest, user
| where mvcount(process) > 3
```
- **`index e source`** - Seleciona eventos onde o **index** é **main** e onde o **source** é **XmlWinEventLog:Microsoft-Windows-Sysmon/Operational**, que é o evento do Sysmon formatado em XML.
- **`EventId`** - Filtra para selecionar apenas o **EventId** de **1**, que no Sysmon indica a criação de um processo.
- **`Time Ranger Filter (earliest e latest)`** - Restringe a faixa de tempo de eventos àqueles que ocorrem entre os registros de data e hora do UNIX 1690447949 e 1690450687.
- **`Process Name (process_name)`** - Filtra os eventos incluindo somente àqueles onde o campo **process_name** corresponde á algum da lista passada.
- **`Statistics (stats)`** - É usado para agregar eventos  baseado em algum campo. Nesse caso `by parent_process, parent_process_id, dest, user`, então serão agrupados os eventos que compartilham o mesmo valor nessas propriedades.
	- **`values(process) as process`** - Armazena os valores exclusivo do campo process como campo multi valor chamado process.
	- **`min(_time) as time`** - Captura o tempo mais antigo que ocorreu em cada grupo
- **`Process Count mvcount(process)`** - Filtra os resultados para incluir somente onde o número de processos forem maiores que 3.
***
#### Detecting Recon By Targeting BloodHound
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
- **`index e source`** - Seleciona eventos onde o **index** é **main** e o **source** é **WinEventLog:SilkService-Log**. Esta fonte indica os dados de Logs obtidos pelo **SilkETW**.

- `Filtering by Index and Source`: The search starts by selecting events from the main index where the source is `WinEventLog:SilkService-Log`. This source represents Windows Event Log data gathered by `SilkETW`.
- `Time Range Filter`: The search restricts the time range of events to those occurring between the Unix timestamps 1690195896 and 1690285475. These timestamps represent the earliest and latest times in which the events occurred.
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

































