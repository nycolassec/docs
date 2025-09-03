## Memory 
###### memdump
Faz o despejo de memória de um determinado processo ou endereço
```sh
$ vol2 -f memmory.dump --profile=[PROFILE] memdump -p [PID] -D [OUT DIR]
```
###### dumpfiles
Faz o dump dos arquivos relacionados a um determinado processo, o `-n` garante seu nome original seja concatenado ao final e a flag `-u` garante que ele faça o despejo do arquivo mesmo que ele esteja corrompido.
```sh
$ vol2 -f memmory.dump --profile=[PROFILE] dumpfiles -p [PID] -D [OUT DIR] -n
```

```sh
$ vol2 -f memmory.dump --profile=[PROFILE] dumpfiles -p [PID] -D [OUT DIR] -n -u
```
***
## Find a malware injection
###### malfind
Ele identifica regiões de memória marcadas com permissões de execução (como PAGE_EXECUTE_READWRITE) e que não possuem associação com arquivos legítimos no disco, características típicas de injeção de código malicioso.
```sh
$ vol2 -f memmory.dump --profile=[PROFILE] malfind -o [OFFSET]
```
Além de também ser possível fazer o dump, podemos combinar esse plugin com o VAD info.
###### vadinfo
O **VAD** é uma estrutura mantida pelo sistema operacional (no Windows, por exemplo) que descreve e gerencia essas regiões de memória virtual: qual é o intervalo de endereços e que permissões (leitura, escrita, execução) cada região possui.
```sh
$ vol2 -f memmory.dump --profile=[PROFILE] vadinfo -a [Inject Mem] -o [OFFSET]
```
***
### Forward link
Após u processo ser retirado da **ActiveProcessLinks list** é criado um processo de **Forward link**, normalmente é o processo que vem logo em seguida do processo retirado. Podemos achalo com um simples `pslist` ou `psxview`.

Mas também podemos fazer pelo `volshell`. Primeiro iniciamos o `volshell` e definimos o o `Current Context` para o Offset do malware encontrado.
```sh
$ vol2 -f memmory.dump --profile=[PROFILE] volshell

In [1]: cc(offset=0x000000007d336950, physical=True)
In [2]: flink = proc().ActiveProcessLinks.Flink
In [3]: fproc = flink - 0x188
In [4]: cc(fproc)
Current context: SearchIndexer. @ 0xfffffa800397db00, pid=2616, ppid=500 DTB=0x3aa8c000
```
***
## Hives
###### hivelist
Os hives do sistema guardam várias informações do sistema, antes de fazer o dump de um determinado hive temos que ver onde ele está no sistema.
```sh
$ vol2 -f memmory.dump --profile=[PROFILE] hivelist

0x8781a278 0x26886278 \REGISTRY\MACHINE\SYSTEM
0x8b79d008 0x24798008 \SystemRoot\System32\Config\SOFTWARE
```
###### printkey
Ao usarmos o `printkey` especificando o endereço da hive ele mostrara as subkeys  e os valores de registro, devemos especificar o caminho das keys com o `-K` .

Podemos ver quais programas vão iniciar junto com o sistema pelo hive `SOFTWARE` achado anteriormente.
```sh
$ vol2 -f memmory.dump --profile=[PROFILE] printkey -o 0x8b79d008 -K "Microsoft\Windows\CurrentVersion\Run"

Registry: \SystemRoot\System32\Config\SOFTWARE
Key name: Run (S)

Values:
REG_SZ        VMware User Process : (S) "C:\Program Files\VMware\VMware Tools\vmtoolsd.exe" -n vmusr
REG_EXPAND_SZ MrRobot         : (S) c:\users\anyconnect\AnyConnect\AnyConnectInstaller.exe
```

Através do hive `SOFTWARE` podemos ver as tasks do windows.
```sh
$ vol2 -f memmory.dump --profile=[PROFILE] printkey -o 0x8b79d008 -K "Microsoft\Windows NT\CurrentVersion\Schedule\Taskcache\Tree\At1"
```

Para pegarmos o hostname precisamos primeiro pegar o endereço da hive `SYSTEM`, após isso especificamos a key `ControlSet001\Control\ComputerName\ComputerName`.
```sh
$ vol2 -f memmory.dump --profile=[PROFILE] printkey -o 0x8781a278 -K "ControlSet001\Control\ComputerName\ComputerName"

Registry: \REGISTRY\MACHINE\SYSTEM
Key name: ComputerName (S)

Values:
REG_SZ                        : (S) mnmsrvc
REG_SZ        ComputerName    : (S) FRONT-DESK-PC
```
***
## Handles
###### handles
```sh
$ vol.py -f memmory.dump --profile=[PROFILE] handles -p 2996 -t mutant
```
***
## Hashes
O `hashdump` faz o dump das hashes (LM/NTLM)
```sh
$ vol.py -f memmory.dump --profile=[PROFILE] hashdump
```
***
## Metadados
###### mftparser
Com esse plugin conseguimos fazer o dump da MFT table do sistema para um arquivo de texto.
```sh
$ vol.py -f memmory.dump --profile=[PROFILE] mftparser
```
###### shimcahe
Analisa a estrutura de dados do ShimCache na memória ou no registro do Windows, listando informações sobre executáveis (ex: caminho completo, tamanho do arquivo, timestamps de modificação e execução do programa)
```sh
$ vol.py -f memmory.dump --profile=[PROFILE] shimcache
```
***
## Commands
###### cmdscan
O `cmdscan` extrai e exibe entradas de linha de comando do registro do Windows. Também mostra os processos que estão gerenciando a execução de comandos.
```sh
vol.py -f memmory.dump --profile=[PROFILE] cmdscan
```
###### cmdline
O `cmdline` recupera argumentos de linha de comando de processos em execução.
```sh
vol.py -f memmory.dump --profile=[PROFILE] cmdline
```
###### consoles
O `consoles` captura e analisa o histórico do console.
```sh
vol.py -f memmory.dump --profile=[PROFILE] consoles
```






