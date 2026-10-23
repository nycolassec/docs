## Searching
#### Basic searching
```
index="main" "UNKNOWN"
```

Boolean operators AND, OR, and NOT are used for more specific queries
```
index="main" "*UNKNOWN*" OR "KNOW"
```
#### Fields and comparison operators
These fields can be used with comparison operators (`=`, `!=`, `<`, `>`, `<=`, `>=`) for more precise searches. Example:
```
index="main" EventCode!=1
```
#### The fields command
The `fields` command specifies which fields should be included or excluded in the search results. Example:
```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | fields - User
```
#### The table command
The `table` command presents search results in a tabular format. Example:
```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | table _time, host, Image
```
#### The rename command
The `rename` command renames a field in the search results. Example:
```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | rename Image as Process
```
#### The dedup command
The `dedup` command removes duplicate events. Example:
```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | dedup Image
```
The `dedup` command removes duplicate entries based on the `Image` field from the process creation events. This means if the same process (`Image`) is created multiple times, it will appear only once in the results, effectively removing repetition.
#### The sort command
The `sort` command sorts the search results. Example:
```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | sort - _time
```
#### The stats command
The `stats` command performs statistical operations. Example:
```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=3 | stats count by _time, Image
```
This query will return a table where each row represents a unique combination of a timestamp (`_time`) and a process (`Image`). The count column indicates the number of network connection events that occurred for that specific process at that specific time.
#### The chart command
The `chart` command creates a data visualization based on statistical operations. Example:
```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=3 | chart count by _time, Image
```
This query will return a table where each row represents a unique timestamp (`_time`) and each column represents a unique process (`Image`). The cell values indicate the number of network connection events that occurred for each process at each specific time.
#### The eval command
The eval command creates or redefines fields. Example:
```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | eval Process_Path=lower(Image)
```
This command creates a new field `Process_Path` which contains the lowercase version of the `Image` field. It doesn't change the actual `Image` field, but creates a new field that can be used in subsequent operations or for display purposes.
#### The rex command
The `rex` command extracts new fields from existing ones using regular expressions. Example:
```
index="main" EventCode=4662 | rex max_match=0 "[^%](?<guid>{.*})" | table guid
```
- `index="main" EventCode=4662` filters the events to those in the `main` index with the `EventCode` equal to `4662`. This narrows down the search to specific events with the specified EventCode.
- `rex max_match=0 "[^%](?<guid>{.*})"` uses the rex command to extract values matching the pattern from the events' fields. The regex pattern `{.*}` looks for substrings that begin with `{` and end with `}`. The `[^%]` part ensures that the match does not begin with a `%` character. The captured value within the curly braces is assigned to the named capture group `guid`.
#### Time range
Every event in Splunk has a timestamp. Using the time range picker or the `earliest` and `latest` commands, you can limit searches to specific time periods. Example:
```
index="main" earliest=-7d EventCode!=1
```
By combining the `index="main"` condition with `earliest=-7d` and `EventCode!=1`, the query will retrieve events from the `main` index that occurred in the last seven days and do not have an `EventCode` value of `1`.
#### The transaction command
```
index="main" sourcetype="WinEventLog:Sysmon" (EventCode=1 OR EventCode=3)
| transaction ProcessGuid startswith=eval(EventCode=1) endswith=eval(EventCode=3) maxspan=1m maxspan=1m
| table Image
| dedup Image
```
Agrupa eventos que tenham o mesmo Image dentro de uma janela de 1 minuto onde começa quando o processo é criado (EventCode=1) e termina quando ele faz uma conexão de rede (EventCode=3).
***
## Discovery
#### Identify available indexes
```
eventcount summarize=false index=* | table index
```
#### Identify available sourcetypes
```
| metadata type=sourcetypes index=* | table sourcetype
```
#### Identify available fields
```
index=* sourcetype="stream:http" | table *
```

***
```
index=corelight sourcetype=* "suricata"
| dedup sourcetype
| table sourcetype
```

```
"pycomm3"
| table src dest src_host dest_host body
| eval src_real = split(src,",")
| stats count by src_real
```





