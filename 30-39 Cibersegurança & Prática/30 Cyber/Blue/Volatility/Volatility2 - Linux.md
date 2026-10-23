#### linux_check_syscall
Retorna as `syscalls` feitas no sistema. Se a entrada corresponder ao símbolo padrão, o nome do `syscall` é mostrado; caso contrário, é exibido HOOKED, indicando desvio de implementação.
#### linux_lsmod
Retorna **todos os módulos de kernel carregados**, inclusive aqueles escondidos por rootkits que removem vestígios, pode ser usado com `-P` para mais detalhes
***
#### linux_dump_map
Faz o dump dos segmentos de memória de um processo em específico.