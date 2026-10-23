## Dump

#### Dump de processos
###### pslist
A partir do plugin `windows.pslist` podemos fazer o dump de qualquer processo especificando o `pid` e a flag `--dump`.
```sh
$ vol -f dump.mem windows.pslist --pid 6988 --dump
```
***
## Time
#### Tempo de aplicativos
###### windows.registry.userassist
O plugin lê e interpreta o conteúdo armazenado na chave do registro do Windows (normalmente em `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`), que rastreia quantas vezes um programa foi executado, quanto tempo esteve em foco e a última vez que foi aberto. Assim podemos descobrir por quanto tempo um programa ficou aberto.
```sh
 $ vol -r pretty -f dump.mem windows.registry.userassis
```
`-r pretty` : Indica que queremos a saída em um formato que facilita a leitura.