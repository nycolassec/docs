## Memory 
###### memdump
Faz o despejo de memória de um determinado processo ou endereço
```sh
$ vol2 -f memmory.dump --profile=[PROFILE] memdump -p [PID] -D [OUT DIR]
```
###### dumpfiles
Faz o dump dos arquivos relacionados a um determinado processo, o `-n` garante seu nome original seja concatenado ao final.
```sh
$ vol2 -f memmory.dump --profile=[PROFILE] dumpfiles -p [PID] -D [OUT DIR] -n
```

```sh
$ vol2 -f memmory.dump --profile=[PROFILE] dumpfiles -p [PID] -D [OUT DIR] -n
```
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