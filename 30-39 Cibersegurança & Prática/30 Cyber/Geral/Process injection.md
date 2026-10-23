Colocar código malicioso dentro da memória de outro processo legítimo, e fazer esse processo executar esse código injetado.
#### Motivos
- Esconder o malware.
- Burlar antivírus / EDR que monitoram só processos novos
- Ganhar os mesmos privilégios do processo legítimo
- Acessar a memória, rede, arquivos que o processo alvo tem acesso
***
## Thread Local Storage - T1055.005
No formato de executável do Windows (PE - Portable Executable), existe uma área chamada TLS Directory (Thread Local Storage).

Nessas áreas o programa pode registrar funções que o windows executa automaticamente em momentos específicos da vida de cada thread, principalmente:
- Quando um novo thread e criado no processo.
- Antes mesmo da função main()/ WinMain()/ DllMain() ser chamada.