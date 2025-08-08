*Version: Volatility 3 Framework 2.5.2*

Volatility e uma ferramenta forense para analise de memoria volÃ¡til que se denomina memoria RAM( Random Access Memory). Podemos analisar a memoria, extrair arquivos, verificar processos e realizar investigaÃ§Ã£o completa do dispositivo atravÃ©s da memoria.

## InstalaÃ§Ã£o do volatility3 

Baixe o [repositÃ³rio](https://github.com/volatilityfoundation/volatility3) e depois instale as dependÃªncias python necessÃ¡rias.

```
git clone https://github.com/volatilityfoundation/volatility3
cd volatility3
pip3 install -r requirements.txt
python3 vol.py -h 
```

Depois de instalar o volatility adicione o caminho da instalaÃ§Ã£o na variavel de ambiente via terminal para que toda vez que algum terminal seja spwanado a variavel "PATH" receba o caminho do volatility. Assim podemos executar "vol.py" de qualquer parte do sistema sem precisar especificar o caminho completo do arquivo, como exemplo "python3 /home/t2m5/Downloads/Volatility3/vol.py", com isso vc sÃ³ chamara "vol.py" sem a necessidade do full path. Para configurar a variÃ¡vel de ambiente adicione o caminho no arquivo "./home/\<username>/.zshrc", esse arquivo e um modelo de template utilizado para spawnar todo terminal do usuÃ¡rio, entÃ£o toda vez que um terminal for aberto a variÃ¡vel vai ser setada.

```bash
vi /home/<username>/.zshrc
	export PATH=<caminho_volatility>:$PATH
```
## PadrÃ£o de uso do volatility3

```bash
vol.py -f "memorydump.vmem" windows.<plugin_name>
````

Para uma analise mais fluida e que nÃ£o precise rodar varias vezes um modulo, aconselho a criar um diretÃ³rio para uso do caso e salvar a saida do plugin em um arquivo txt para que se houver a necessidade de consultar a informaÃ§Ã£o, nÃ£o precisaremos rodar novamente o modulo no volatility. Como no exemplo abaixo: 

```bash 
mkdir vol
vol.py -f "memory.vmss" windows.netscan > vol/netscan.txt
```

Toda vez que eu necessitar de alguma informaÃ§Ã£o do netscan eu irei ler o arquivo ao inves de rodar o modulo que consequentemente demoraria muito mais.

## Ferramentas de Captura de memoria RAM

HÃ¡ diversas ferramentas que realizam a captura de memoria ou um conjunto de informaÃ§Ãµes do sistema para uma pÃ³s analise ou atÃ© mesmo uma resposta a incidente imediata. Deixarei algumas ferramentas abaixo para essa aquisiÃ§Ã£o, sendo para windows ou linux

- https://github.com/504ensicsLabs/LiME
- https://github.com/microsoft/avml/releases
- https://cloudnine.com/ediscoverydaily/electronic-discovery/capturing-memory-and-obtaining-protected-files-with-ftk-imager-ediscovery-best-practices/
## Lista de plugins 

- **Windows**
	- **windows.crashinfo.Crashinfo**
	- **windows.devicetree.DeviceTree**
	- **windows.dlllist.DllList**
	- **windows.driverirp.DriverIrp**
	- **windows.drivermodule.DriverModule**
	- **windows.driverscan.DriverScan**
	- **windows.dumpfiles.DumpFiles**
	- **windows.envars.Envars**
	- **windows.filescan.FileScan**
	- **windows.getservicesids.GetServiceSIDs**
	- **windows.getsids.GetSIDs**
	- **windows.handles.Handles**
	- **windows.hashdump.Hashdump**
	- **windows.iat.IAT**     
	- **windows.joblinks.JobLinks**
	- **windows.ldrmodules.LdrModules**
	- **windows.lsadump.Lsadump**
	- **windows.malfind.Malfind**
	- **windows.mbrscan.MBRScan**
	- **windows.memmap.Memmap**
	- **windows.mftscan.ADS**
	- **windows.mftscan.MFTScan**
	- **windows.modscan.ModScan**
	- **windows.modules.Modules**
	- **windows.mutantscan.MutantScan**
	- **windows.netscan.NetScan**
	- **windows.netstat.NetStat**
	- **windows.notepad.Notepad**
	- **windows.poolscanner.PoolScanner**
	- **windows.privileges.Privs**
	- **windows.pslist.PsList**
	- **windows.psscan.PsScan**
	- **windows.pstree.PsTree**
	- **windows.registry.certificates.Certificates**
	- **windows.registry.hivelist.HiveList**
	- **windows.registry.hivescan.HiveScan**
	- **windows.registry.printkey.PrintKey**
	- **windows.registry.userassist.UserAssist**
	- **windows.sessions.Sessions**
	- **windows.skeleton_key_check.Skeleton_Key_Check**
	- **windows.ssdt.SSDT**   
	- **windows.statistics.Statistics**
	- **windows.strings.Strings**
	- **windows.svcscan.SvcScan**
	- **windows.symlinkscan.SymlinkScan**
	- **windows.truecrypt.Passphrase**
	- **windows.vadinfo.VadInfo**
	- **windows.vadwalk.VadWalk**
	- **windows.vadyarascan.VadYaraScan**
	- **windows.verinfo.VerInfo**
	- **windows.virtmap.VirtMap**

- **Linux**
	- **linux.bash.Bash**     
	- **linux.capabilities.Capabilities**
	- **linux.check_afinfo.Check_afinfo**
	- **linux.check_creds.Check_creds**
	- **linux.check_idt.Check_idt**
	- **linux.check_modules.Check_modules**
	- **linux.check_syscall.Check_syscall**
	- **linux.elfs.Elfs**     
	- **linux.envars.Envars**
	- **linux.iomem.IOMem**   
	- **linux.keyboard_notifiers.Keyboard_notifiers**
	- **linux.kmsg.Kmsg**    
	- **linux.lsmod.Lsmod**   
	- **linux.lsof.Lsof**    
	- **linux.malfind.Malfind**
	- **linux.mountinfo.MountInfo**
	- **linux.proc.Maps**     
	- **linux.psaux.PsAux**   
	- **linux.pslist.PsList**
	- **linux.psscan.PsScan**
	- **linux.pstree.PsTree**
	- **linux.sockstat.Sockstat**
	- **linux.tty_check.tty_check**
	- **linux.vmayarascan.VmaYaraScan**


Todos os plugins ficam disponÃ­veis na aba de ajuda

## Listagem de Processos 

**pslist**

```bash
vol.py -f "memorydump.vmem" windows.pslist > pslist
```

**pstree**

```bash
vol.py -f "memorydump.vmem" windows.tree > pstree
```

**psscan**

```bash
vol.py -f "memorydump.vmem" windows.psscan > pslist
```

## Arquivos

**filescan**

```bash
vol.py -f "memorydump.vmem" windows.filescan
```

**dumpfiles**

Sem a especificaÃ§Ã£o de um endereÃ§o de memoria fÃ­sica ou logica acontecera o download de todos arquivos encontrados na memoria.
	
```bash
vol.py -f "memorydump.vmem" windows.dumpfiles
```
	
Com a especificaÃ§Ã£o do endereÃ§o de memoria do arquivo, o volatility irÃ¡ realizar o download do mesmo.
	
```bash
vol.py -f "memorydump.vmem" windows.dumpfiles --physaddr 0x38A0e09c
	
vol.py -f "memorydump.vmem" windows.dumpfiles --virtaddr 0x38A0e09c
```

## Handle

**handles**

```bash
vol.py -f "memorydump.vmem" windows.handles --pid 43
```

## Comandos

**cmdline**

```bash
vol.py -f "memorydump.vmem" windows.cmdline > cmdline
```

**notepad**

```bash
vol.py -f "memorydump.vmem" windows.notepad > nodepad
```

### Rede/ConexÃµes

**netscan**

```bash
vol.py -f "memorydump.vmem" windows.netscan > netscan
```

**netstat**

```bash
vol.py -f "memorydump.vmem" windows.netstat > netstat 
```



### Registro do Windows

**hivelist**

```bash
vol.py -f "memorydump.vmem" windows.registry.hivescan
```

```bash
vol.py -f "memorydump.vmem" windows.registry.hivelist
```

**printkey**

```bash
vol.py -f "memorydump.vmem" windows.registry.printkey
```

```bash
vol.py -f "memorydump.vmem" windows.registry.printkey --key "Software\Microsoft\Windows\CurrentVersion"
```



### DetecÃ§Ã£o de Malware

**malfind**

```bash
vol.py -f "memorydump.vmem" windows.malfind > malfind
```

**yarascan**

```bash
vol.py -f "memorydump.vmem" windows.vadyarascan --yara-rules \<strings>
```

## Linux Memory Analisys

O volatility 3 nÃ£o usa o sistema de profiles como o antigo volatility 2. Ele utiliza o modelo de ISF (Intermediate Symbol Format) que e armazenado em tabelas para endereÃ§ar aos arquivos de memorias. HÃ¡ duas formas de adquirir um ISF, por meio da consultas em sites que armazenam samples de ISF ou pela geraÃ§Ã£o de um ISF, vamos demostrar as duas formas.


Verificando versÃ£o do kernel por meio dos banners da captura de imagem.

```bash
vol.py -f kali.lime banners.Banners
```

Realize a consulta no site [Volatility3 Linux ISF Server](https://isf-server.techanarchy.net/) para verificar se jÃ¡ existe um ISF criado pela comunidade. 

![[Pasted image 20240809111026.png]]

A consulta pode ser realizada pelo Banner da imagem ou pela versÃ£o do kernel do sistema operacional, se jÃ¡ houver um ISF pronto o processo irÃ¡ ser extremamente mais rÃ¡pido. Se existir um ISF, realize o download do mesmo e salve na pasta symbols links do volatility.

```bash
find / 2>/dev/null | grep /volatility3/symbols/
cd <path_volatility>/volatility3/symbols/
wget <link_do_ISF>
```

Se nÃ£o houver nenhum ISF disponÃ­vel, devemos criar um ISF para conseguirmos analisas nossa captura de memoria. Para isso precisamos baixar a ferramenta [dwarf2json](https://github.com/volatilityfoundation/dwarf2json).

```bash
git clone https://github.com/volatilityfoundation/dwarf2json
cd dwarf2json
go build
./dwarf2json --help
```

Agora com o dwarf2json podemos criar nosso prÃ³prio arquivo ISF, mas para isso precisamos de dois componentes essenciais 

- Linux Kernel DWARF file (vmlinux)
- System.Map

### Referencias

- https://github.com/volatilityfoundation/volatility3
- https://cpuu.hashnode.dev/how-to-perform-memory-forensic-analysis-in-linux-using-volatility-3
- https://cpuu.hashnode.dev/how-to-perform-memory-forensic-analysis-in-windows-11-using-volatility-3
- https://blog.onfvp.com/post/volatility-cheatsheet/

*Escrito por Thaylon Roberto Muniz da Silva, WorldSkills Lyon 2024, 15/02/2024*.