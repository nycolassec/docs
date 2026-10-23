#### Options
```
password=infected,trace=1,free=yes,procdump=yes,tlsmaster=yes
```

- **`trace=1`**: Força o monitor a registrar uma trilha extensa e minuciosa de todas as chamadas de API executadas pelos processos, garantindo que nenhuma transição de contexto ou syscall de baixo nível fique de fora dos logs comportamentais.
    
- **`free=yes`**: Ativa o modo de evasão a técnicas anti-sandbox e anti-hooking. Malwares sofisticados costumam verificar se há ganchos (_hooks_) de depuração ou monitoramento nas APIs do Windows. O `free` implementa contramedidas para injetar o monitor de forma mais limpa e persistente.
    
- **`procdump=yes`**: Indispensável para malwares compactados (_packed_), protegidos por _crypters_ ou injetores de memória (_process hollowing_). O CAPE aguarda o momento em que o malware se descompacta na memória RAM e extrai o executável puro e limpo, salvando-o nos _Dropped Files_ para engenharia reversa posterior.
    
- **`tlsmaster=yes`**: Captura as chaves de sessão SSL/TLS geradas na memória durante a execução. Isso permite que o sistema (ou ferramentas externas como o Wireshark) descriptografe o tráfego HTTPS gerado pelo malware ao se comunicar com o servidor de Comando e Círculo (C2).

```
password=infected,procmemdump=yes,procdump=yes,tlsmaster=yes,full_memory_dump=yes,human=1,route=internet
```

```
password=infected,procmemdump=yes,procdump=yes,tlsmaster=yes,full_memory_dump=yes,memory=yes,human=1,route=internet,trace=1,trace_all=yes
```


/home/cape/.cache/pypoetry/virtualenvs/capev2-t2x27zRb-py3.10/bin/python utils/cleaners.py --clean

password=infected,route=internet