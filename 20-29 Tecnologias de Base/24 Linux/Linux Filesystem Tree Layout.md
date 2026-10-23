Para dominar os tópicos de **Linux Filesystem Tree Layout (FHS)** e **User Environment (Bash)** na certificação LFCS, é necessário ir muito além da memorização de comandos básicos. O exame é 100% focado em desempenho e persistência (fazer com que as configurações sobrevivam a reboots).

Abaixo está uma dissecação conceitual profunda de ambas as áreas, seguida de desafios reais no estilo da prova para você praticar.

---
### Linux Filesystem Tree Layout (FHS)

O **Filesystem Hierarchy Standard (FHS)** é o padrão que define os nomes convencionais e propósitos de cada diretório no Linux. Ele é estruturado em torno de dois conceitos fundamentais:

1. **Dados compartilháveis (shareable)** vs. **Não-compartilháveis (unshareable)**.
2. **Dados estáticos (static)** (como arquivos executáveis e bibliotecas) vs. **Dados variáveis (variable)** (como logs e arquivos temporários).

Toda essa hierarquia emana da raiz absoluta do sistema: o diretório `/`.

#### Dissecação dos Principais Diretórios de Topo

- **`/etc`**: Destina-se estritamente a **arquivos de configuração estáticos globais** específicos do host local. O FHS **proíbe explicitamente** o armazenamento de binários executáveis aqui. Exemplos cruciais para a prova incluem o `/etc/fstab` (montagem de discos) e `/etc/ssh/sshd_config` (configuração do SSH).
- **`/var`**: Destinado a **dados variáveis** que mudam constantemente durante o ciclo de vida do sistema (logs, caches, filas de e-mail e spool). Isolar o crescimento desses dados em `/var` evita o preenchimento acidental das partições estáticas do sistema. Para troubleshoot, o diretório `/var/log` é o seu primeiro destino.
- **`/usr`**: Reúne os recursos de sistema dos usuários (**User System Resources**). Historicamente projetado para ser montado em modo somente leitura (read-only), contém a maior parte dos programas, documentações e bibliotecas do sistema localizados em `/usr/bin`.
- **`/bin` e `/sbin`**: Tradicionalmente, o `/bin` continha comandos fundamentais acessíveis a **todos os usuários** (como `ls` ou `cat`), enquanto o `/sbin` continha utilitários essenciais de **administração do sistema** que necessitavam de privilégios de root para execução completa (como `fdisk`, `fsck` ou `ip`).
- **`/opt`**: Reservado para a instalação de pacotes de software adicionais (**add-on**) de terceiros. O software que reside aqui deve ser autossuficiente, mantendo suas próprias estruturas internas sob um subdiretório isolado (ex: `/opt/app-xyz`).
- **`/proc` e `/sys`**: São sistemas de arquivos virtuais (pseudo-FS) mantidos inteiramente na memória RAM pelo Kernel. O `/proc` expõe informações de processos em tempo real (através de IDs numéricos - PIDs) e do Kernel, enquanto o `/sys` organiza dinamicamente dados estruturados do hardware, barramentos e drivers.
- **`/dev`**: Expõe arquivos de dispositivos especiais (device nodes) que permitem a interface direta com o hardware. A população deste diretório é gerenciada em tempo real pelo subsistema `udev`.

#### O Paradigma Moderno: "Usr-Merge"

Nas distribuições Linux modernas (como Fedora, RHEL, Debian e Ubuntu), a separação histórica entre `/bin` e `/usr/bin` perdeu seu propósito prático devido à utilização precoce do `initramfs` (que carrega os drivers necessários para montar a raiz principal no boot).

Com isso, foi adotado o movimento **Usr-Merge**. Nele, os diretórios `/bin`, `/sbin`, `/lib` e `/lib64` na raiz foram convertidos em **links simbólicos (symlinks)** que apontam diretamente para seus equivalentes em `/usr` (por exemplo, `/bin` \(\rightarrow\) `/usr/bin`). No entanto, a distinção lógica e semântica entre comandos normais de usuário e ferramentas de administração (`/usr/sbin`) continua válida para a administração de segurança e manipulação de variáveis de ambiente.

#### A Gestão de Dados Voláteis: `/tmp`, `/var/tmp` e `/run`

- **`/tmp`**: Diretório de escrita universal para dados puramente operacionais rápidos. Em sistemas modernos, ele é geralmente montado diretamente na memória RAM sob o sistema de arquivos virtual `tmpfs`. Portanto, **sua volatilidade é absoluta**: tudo o que estiver em `/tmp` desaparecerá instantaneamente após um `reboot` ou queda de energia.
- **`/var/tmp`**: Projetado para dados temporários que **necessitam sobreviver a reinicializações do sistema** (como arquivos grandes em meio a processos longos de compilação ou backups). Ele é armazenado em disco físico e limpo de maneira programada por serviços baseados em tempo (como o `systemd-tmpfiles`).
- **`/run`**: Contém dados de tempo de execução (_run-time variable data_) gerados desde o início do boot, como sockets Unix, arquivos de bloqueio e arquivos PID de daemons ativos. Ele precisa ser esvaziado no boot precoce para evitar conflitos com processos antigos.

---

### User Environment (Bash)

No Linux, a forma como a shell inicializa as variáveis de ambiente e aliases depende de uma classificação rigorosa: a sessão é de **Login** ou de **Não-Login**?

- **Sessão de Login Interativa**: Ocorre quando há uma autenticação inicial formal do usuário no sistema (como um login via SSH ou o comando `su - usuario`).
- **Sessão de Não-Login Interativa**: Ocorre quando você abre uma sub-shell a partir de uma sessão já iniciada (como abrir um terminal na interface gráfica ou executar apenas `su` sem o caractere hífen `-`).

#### Ordem de Carregamento dos Arquivos de Perfil

Quando você inicia uma shell, o Bash busca variáveis e customizações seguindo um fluxo estrito:

```
[Fase Pré-Shell (PAM /etc/environment)]
                  │
                  ▼
       [A shell é de LOGIN?]
         ├── SIM ──► 1. Lê /etc/profile (carrega /etc/profile.d/*.sh)
         │           2. Lê o PRIMEIRO encontrado entre:
         │              ~/.bash_profile, ~/.bash_login ou ~/.profile
         │
         └── NÃO ──► 1. Lê /etc/bashrc (RHEL) ou /etc/bash.bashrc (Debian/Ubuntu)
                     2. Lê ~/.bashrc (usuário local)
```

1. **Pré-Shell (`/etc/environment`)**: Lido pelo módulo PAM do Linux antes de o binário da shell ser executado. **Não é um script de shell**: não aceita expansões, testes ou a declaração `export`. Contém puras atribuições simples de chave e valor (ex: `PATH="valor"`).
2. **Sessões de Login**:
    - O sistema lê primeiro o arquivo global `/etc/profile`. É uma boa prática não modificar este arquivo diretamente; em vez disso, crie arquivos de script separados na pasta drop-in `/etc/profile.d/*.sh`, os quais são lidos sequencialmente pelo `/etc/profile` e preservados em atualizações.
    - Depois, o Bash busca no diretório home do usuário e executa o **primeiro** que encontrar entre: `~/.bash_profile`, `~/.bash_login` ou `~/.profile`, parando imediatamente após a primeira correspondência. Geralmente, o `~/.bash_profile` do usuário contém um script que invoca explicitamente o `~/.bashrc`.
3. **Sessões de Não-Login**:
    - Ignora o `/etc/profile` e os perfis pessoais de login.
    - Lê o arquivo global de não-login da distribuição: `/etc/bashrc` (em sistemas baseados em Red Hat) ou `/etc/bash.bashrc` (em sistemas baseados em Debian/Ubuntu).
    - Por fim, lê o arquivo pessoal do usuário `~/.bashrc`. É o local recomendado para persistir variáveis pessoais e aliases interativos.

#### Variáveis de Ambiente e a Importância do `export`

Ao definir uma variável de forma comum (`VAR="valor"`), ela fica restrita apenas ao processo da shell atual. Para que qualquer script, binário ou processo-filho herde essa variável, ela deve ser promovida a **variável de ambiente** usando a diretiva `export`.

- Declarar e exportar na mesma linha: `export MINHA_VAR="valor"`.
- Para inspecionar apenas variáveis de ambiente exportadas, utilize os comandos `env` ou `printenv` (o comando `set` listará tudo, inclusive funções locais).

#### A Engenharia da Variável `$PATH`

A variável `$PATH` dita a lista de diretórios onde a shell busca comandos executáveis de forma sequencial. Se você reescrever o `$PATH` incorretamente (como `export PATH="/opt/scripts"`), perderá o acesso imediato a comandos básicos (como `ls` ou `vi`). Para estendê-la de forma segura:

- **Prefixação (Prioridade Máxima)**: `export PATH="/meu/dir:$PATH"`.
- **Sufixação (Fallback)**: `export PATH="$PATH:/meu/dir"`.

#### A Armadilha da Tabela Hash da Shell (`hash -r`)

Para otimizar o desempenho, o Bash não varre todo o `$PATH` todas as vezes que você digita um comando. Após a primeira execução de um executável, seu caminho exeto é salvo em uma tabela em cache (Hash Table).

Se você mover um script ou utilitário de lugar (por exemplo, mover de `/usr/local/bin` para `/opt/bin`) e tentar executá-lo novamente na mesma sessão, o Bash retornará um erro dizendo que o executável não foi encontrado. A resolução mandatória é rodar o comando:

```
hash -r
```

Este comando limpa o cache de busca de caminhos da shell ativa, forçando o recálculo imediato na próxima invocação do comando.

#### Aliases vs. Funções de Shell (Shell Functions)

Os aliases servem para criar atalhos simples de comandos (ex: `alias ll='ls -lathr'`). No entanto, **aliases não aceitam argumentos posicionais dinâmicos** (como `$1` ou `$2`) no meio de encadeamentos ou canais (pipes). Se você criar algo como `alias busca='grep $1 /var/log/syslog'`, o Bash falhará catastroficamente ao expandir, jogando o parâmetro do usuário no final da string.

Para injetar lógica dinâmica, condições e variáveis dinâmicas, a prática recomendada pela LFCS é escrever uma **Shell Function** e adicioná-la no final do arquivo de inicialização apropriado:

```
function buscar_log() {
    grep -i "$1" /var/log/syslog | less
}
```

---

### DESAFIOS PRÁTICOS (ESTILO LFCS)

Para fixar esses conceitos de forma prática, resolva estes 3 desafios no seu próprio ambiente de estudos.

#### DESAFIO 1: Implantação de Aplicação de Terceiros e PATH Global

> **Cenário**: Um desenvolvedor deixou um script administrativo pesado no diretório temporário do usuário root (`/root/sys-monitor.sh`).
> 
> **Suas Tarefas**:
> 
> 1. Mova este script para a estrutura correta do **FHS** para pacotes e aplicações de terceiros (add-on), criando um subdiretório dedicado chamado `sys-monitor` contendo uma pasta `bin` para guardar o executável.
> 2. Garanta que o script tenha permissões de execução.
> 3. Altere o ambiente global (**System-wide**) para que o novo caminho do script seja incluído de forma persistente na variável `$PATH` de todos os usuários do sistema. Use a estrutura drop-in adequada do FHS que resista a atualizações de pacotes do sistema.
> 4. Faça com que a nova configuração seja validada imediatamente na sua sessão atual, evitando erros de cache de caminhos caso o comando já tenha sido invocado anteriormente de outra pasta.

---

#### DESAFIO 2: O Analista de Logs Dinâmico (Shell Function Persistente)

> **Cenário**: A equipe de segurança cibernética exige uma ferramenta rápida e local para o usuário administrador de auditoria chamado `auditor`. Eles precisam que, ao digitar `check_audit [palavra-chave]`, o sistema retorne todas as linhas (sem diferenciar maiúsculas de minúsculas) do log de segurança que contenham a palavra especificada.
> 
> **Suas Tarefas**:
> 
> 1. Crie o usuário `auditor`.
> 2. No ambiente pessoal deste usuário (não global), implemente uma solução persistente a reinicializações que permita a execução do comando `check_audit [argumento]`.
> 3. O comando deve ler o log `/var/log/secure` (ou `/var/log/auth.log` se estiver no Ubuntu) e filtrar os matches de forma paginada usando o utilitário `less`.
> 4. _Atenção_: Como o alias tradicional possui limitações para tratar parâmetros posicionais intermediários, você deve usar obrigatoriamente uma **Shell Function** dentro do arquivo de inicialização correto do usuário.

---

#### DESAFIO 3: Persistência do Kernel no Boot e FHS Virtual

> **Cenário**: Para solucionar um problema de intercomunicação de rede entre contêineres e máquinas virtuais locais, você precisa realizar um ajuste na memória viva do Kernel.
> 
> **Suas Tarefas**:
> 
> 1. Habilite imediatamente, por meio de alteração em tempo de execução, o encaminhamento de pacotes IPv4 (`net.ipv4.ip_forward=1`).
> 2. Garanta que essa alteração seja persistente a reboots, mas sem editar ou corromper o arquivo histórico `/etc/sysctl.conf`. Em vez disso, utilize o diretório drop-in do FHS recomendado pelas boas práticas modernas.
> 3. Force o sistema a ler o novo arquivo imediatamente para validar o sucesso e aplique as configurações de runtime.

---

### GABARITO & RESOLUÇÃO DOS DESAFIOS

#### Resolução do Desafio 1:

```
# 1. Criar o diretório compatível com o FHS sob /opt e mover o script
mkdir -p /opt/sys-monitor/bin
mv /root/sys-monitor.sh /opt/sys-monitor/bin/sys-monitor.sh

# 2. Dar permissão de execução ao utilitário
chmod +x /opt/sys-monitor/bin/sys-monitor.sh

# 3. Adicionar o novo diretório de binários ao PATH de forma global usando drop-in
echo 'export PATH=$PATH:/opt/sys-monitor/bin' > /etc/profile.d/sys-monitor-path.sh

# 4. Aplicar e limpar o cache de tabela hash da shell atual
source /etc/profile.d/sys-monitor-path.sh
hash -r
```

#### Resolução do Desafio 2:

```
# 1. Criar o usuário auditor
useradd -m auditor

# 2. Editar o arquivo .bashrc na home do usuário auditor
# Nota: Você pode fazer isso editando diretamente o arquivo como root
vi /home/auditor/.bashrc

# Adicione no final do arquivo a seguinte função:
function check_audit() {
    grep -i "$1" /var/log/secure | less
}

# 3. Dar permissões de leitura ao auditor no arquivo de log (se necessário, ou usando ACL)
setfacl -m u:auditor:r /var/log/secure

# 4. Validar simulando a sessão do auditor
su - auditor
source ~/.bashrc
# Teste o comando dinâmico:
check_audit "failed"
```

#### Resolução do Desafio 3:

```
# 1. Habilitar imediatamente no runtime do Kernel (memória volátil do Procfs)
sysctl -w net.ipv4.ip_forward=1

# 2. Persistir criando uma configuração drop-in recomendada no FHS
echo "net.ipv4.ip_forward = 1" > /etc/sysctl.d/99-ip-forwarding.conf

# 3. Recarregar e validar ativamente a configuração de runtime
sysctl -p /etc/sysctl.d/99-ip-forwarding.conf
sysctl net.ipv4.ip_forward
```
