Antes de configurar um switch, você precisa ativá-lo e permitir que ele passe pela sequência de inicialização de cinco etapas. Este tópico aborda os conceitos básicos de configuração de um switch e inclui um laboratório no final.

Depois que um switch Cisco é ligado, ele passa pela seguinte seqüência de inicialização em cinco etapas:

**Etapa 1:** Primeiro, o switch carrega um programa POST (Power-On Self-Test) armazenado na ROM. POST verifica o subsistema da CPU. Ele testa a CPU, DRAM e a parte do dispositivo flash que compõe o sistema de arquivos flash.  
  
**Etapa 2:** Depois, o switch carrega o software carregador de inicialização. O carregador de inicialização (boot loader) é um programa pequeno armazenado na ROM que é executada imediatamente após a conclusão bem-sucedida do POST.  
  
**Etapa 3:** O carregador de inicialização executa inicialização de baixo nível da CPU. Ele inicializa os registros da CPU, que controlam onde a memória física é mapeada, a quantidade de memória e sua velocidade.  
  
**Etapa 4:** O carregador de inicialização (boot loader) inicializa o sistema de arquivos da memória flash na placa do sistema.  
  
**Etapa 5:** Finalmente, o carregador de inicialização localiza e carrega uma imagem do software de sistema operacional IOS padrão na memória e passa o controle do switch para o IOS.