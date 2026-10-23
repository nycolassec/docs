**section** mostra a seção que começa com a expressão de filtragem.
```ios
R1# show running-config | section line vty
```

**include** inclui todas as linhas de saída que corresponderam á expressão de linguagem
```ios
R1# show ip interface brief | include up
```

**exclude** exclui todas as linhas de saída que corresponderam
```ios
R1# show ip interface brief | exclude unassigned
```

**begin** mostra todas as linhas de saída de um determinado ponto
```ios
R1# show ip route | begin Gateway
```

## Histórico de comandos

```ios
R1> terminal history size 200
R1> show history
```