<properties
    pageTitle="Clusterize MySQL com conjuntos de balanceamento de carga | Microsoft Azure"
    description="Configurar uma balanceamento de carga alta disponibilidade, Linux MySQL cluster criado com o modelo clássico de implantação no Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="bureado"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/14/2015"
    ms.author="jparrel"/>

# <a name="using-load-balanced-sets-to-clusterize-mysql-on-linux"></a>Usando conjuntos de balanceamento de carga para clusterize MySQL no Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


O objetivo deste artigo é explorar e ilustrar as diferentes abordagens disponíveis para implantar os serviços baseados em Linux altamente disponíveis na Microsoft Azure, explorando alta disponibilidade do servidor MySQL como última palavra. Um vídeo ilustrando essa abordagem está disponível no [canal 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Nós da estrutura de tópicos uma nada compartilhado dois nós mestre único MySQL alta disponibilidade solução baseada em DRBD, Corosync e Pacemaker. Somente um nó está executando MySQL por vez. Leitura e escrita do recurso DRBD também está limitado a apenas um nó por vez.

Não é necessário para uma solução de VIP como LVS desde que usamos conjuntos de balanceamento de carga do Microsoft Azure para fornecer a funcionalidade de alternada e a detecção de ponto de extremidade, remoção e recuperação normal do VIP. VIP é um endereço IPv4 globalmente roteável atribuído pelo Microsoft Azure quando criamos primeiro o serviço de nuvem.

Existem outras arquiteturas de possíveis para MySQL incluindo Cluster útil, Percona e Galera bem como várias soluções de middleware, incluindo pelo menos uma disponível como uma máquina virtual em [Depósito de máquina virtual](http://vmdepot.msopentech.com). Desde que essas soluções podem replicar em ponto a ponto versus difusão seletiva ou difusão e não dependem de armazenamento compartilhado ou várias interfaces de rede, os cenários devem ser fáceis implantar o Microsoft Azure.

Claro essas arquiteturas clusters podem ser estendidas para outros produtos, como PostgreSQL e OpenLDAP na forma similar. Por exemplo, esta balanceamento de carga procedimento com nada compartilhado foi testada com êxito com vários mestres OpenLDAP e você poderá visualizá-la em nosso blog de 9 de canal.

## <a name="getting-ready"></a>Se preparando

Você precisará de uma conta do Microsoft Azure com uma assinatura válida capaz de criar pelo menos dois (2) VMs (x foi usada neste exemplo), definem uma rede e uma sub-rede, um grupo de afinidade e uma disponibilidade, bem como a capacidade para criar novos VHDs na mesma região como o serviço de nuvem e para anexá-los às VMs Linux.

### <a name="tested-environment"></a>Ambiente testado

- Ubuntu 13.10
  - DRBD
  - Servidor MySQL
  - Corosync e Pacemaker

### <a name="affinity-group"></a>Grupo de afinidade

Um grupo de afinidade para a solução é criado pelo registro em log para o Azure clássico portal rolar para baixo até configurações e criando um novo grupo de afinidade. Recursos alocados criados posteriormente serão atribuídos a esse grupo de afinidade.

### <a name="networks"></a>Redes

Uma nova rede é criada, e uma sub-rede foi criada dentro da rede. Escolhemos uma rede 10.10.10.0/24 com apenas um /24 sub-rede dentro.

### <a name="virtual-machines"></a>Máquinas virtuais

A máquina virtual 13.10 do primeiro Ubuntu é criada usando uma imagem da Galeria de Ubuntu Endorsed e chamada `hadb01`. Um novo serviço de nuvem é criado no processo, chamado hadb. Chamamos dessa forma para ilustrar a natureza compartilhada, balanceamento de carga que o serviço terá quando adicionamos mais recursos. A criação de `hadb01` será normal e preenchido usando o portal. Um ponto de extremidade para o SSH é criado automaticamente e nossa rede criado está selecionada. Nós também optar por criar uma nova disponibilidade definida para VMs.

Uma vez criada a máquina virtual primeira (tecnicamente, quando o serviço de nuvem é criado) Vamos prosseguir para criar a segunda máquina virtual, `hadb02`. Para a segunda máquina de virtual também usaremos máquina virtual do Ubuntu 13.10 da galeria usando o portal, mas podemos será optar por usar um serviço de nuvem existente, `hadb.cloudapp.net`, em vez de criar um novo. O conjunto de rede e disponibilidade deve estar selecionado automaticamente para nós. Um ponto de extremidade SSH será criado, também.

Depois que as duas VMs foram criadas, podemos levará anotação da porta SSH de `hadb01` (TCP 22) e `hadb02` (atribuído automaticamente pelo Azure)

### <a name="attached-storage"></a>Armazenamento conectado

Vamos anexar um novo disco a ambas as VMs e criar novos discos de 5 GB no processo. Os discos serão hospedados no contêiner de VHD em uso para nossos discos do sistema operacional principal. Depois de discos são criados e anexados não é necessário para que possamos reinicie Linux conforme o núcleo verão o novo dispositivo (geralmente `/dev/sdc`, você pode verificar `dmesg` para a saída)

Em cada máquina virtual podemos continuar para criar uma nova partição usando `cfdisk` (partição de Linux principal,) e gravar a nova tabela de partição. **Não crie um sistema de arquivos nessa partição** .

## <a name="setting-up-the-cluster"></a>Configurando o cluster

Em ambas as VMs Ubuntu, precisamos usar APT para instalar Corosync, Pacemaker e DRBD. Usando `apt-get`:

    sudo apt-get install corosync pacemaker drbd8-utils.

**Não instale MySQL neste momento** . Debian e scripts de instalação do Ubuntu irá inicializar um diretório de dados MySQL em `/var/lib/mysql`, mas desde que o diretório será ser substituído por um sistema de arquivos DRBD, precisamos fazer isso mais tarde.

Neste ponto podemos também deve verificar (usando `/sbin/ifconfig`) se ambas as VMs estão usando endereços na sub-rede 10.10.10.0/24 e que eles podem ping uns aos outros por nome. Se desejar também é possível usar `ssh-keygen` e `ssh-copy-id` para garantir que os dois VMs possam se comunicar via SSH sem exigir uma senha.

### <a name="setting-up-drbd"></a>Configurando DRBD

Vamos criar um recurso DRBD que usa subjacente `/dev/sdc1` partição para produzir um `/dev/drbd1` recurso poderá ser formatado usando ext3 e usados em nós primárias e secundários. Para fazer isso, abra `/etc/drbd.d/r0.res` e copie a seguinte definição de recurso. Fazer isso em ambas as VMs:

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

Depois de fazer isso, inicializar o recurso usando `drbdadm` em ambas as VMs:

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

E finalmente, na imagem principal (`hadb01`) forçar a propriedade (principal) do recurso DRBD:

    sudo drbdadm primary --force r0

Se você examinar o conteúdo de/proc/drbd (`sudo cat /proc/drbd`) em ambas as VMs, você deve ver `Primary/Secondary` na `hadb01` e `Secondary/Primary` na `hadb02`consistente com a solução neste momento. O disco de 5 GB será sincronizado através da rede 10.10.10.0/24 sem nenhum custo aos clientes.

Depois que o disco está sincronizado você pode criar o sistema de arquivos em `hadb01`. Para fins de teste nós usamos ext2, mas a instrução a seguir cria um sistema de arquivos ext3:

    mkfs.ext3 /dev/drbd1

### <a name="mounting-the-drbd-resource"></a>O recurso DRBD de montagem

Em `hadb01` agora estamos prontos para montar os recursos DRBD. Usar Debian e derivados `/var/lib/mysql` como diretório de dados do MySQL. Como podemos ainda não tiver instalado o MySQL, vamos criar o diretório e montar o recurso DRBD. On `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="setting-up-mysql"></a>Configurando MySQL

Agora você está pronto para instalar MySQL em `hadb01`:

    sudo apt-get install mysql-server

Para `hadb02`, você tem duas opções. É possível instalá servidor mysql agora, qual será criar /var/lib/mysql e preencha-a com um novo diretório de dados e, em seguida, prossiga para remover o conteúdo. On `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

A segunda opção é failover para `hadb02` e, em seguida, instale o servidor mysql lá (scripts de instalação notará a instalação existente e não toque nele)

On `hadb01`:

    sudo drbdadm secondary –force r0

On `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Se você não planeja failover DRBD agora, a primeira opção é mais fácil embora possivelmente menos elegante. Depois de configurar isso, você pode começar a trabalhar em seu banco de dados MySQL. Em `hadb02` (ou qualquer um dos servidores estiver ativo, de acordo com a DRBD):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

**Aviso**: esta última instrução efetivamente desabilita a autenticação do usuário raiz nesta tabela. Isso deve ser substituído pela sua nota de produção CONCEDER instruções e está incluído somente para fins de ilustração.

Você também precisa habilitar a rede para MySQL se você quiser fazer consultas de fora as VMs, que é o propósito deste guia. Em ambas as VMs, abra `/etc/mysql/my.cnf` e procure `bind-address`, alterá-la de 127.0.0.1 para 0.0.0.0. Após salvar o arquivo, execute uma `sudo service mysql restart` seu primária atual.

### <a name="creating-the-mysql-load-balanced-set"></a>Criando a carga de MySQL equilibrada conjunto

Nós será voltar para o portal e navegue até o `hadb01` máquina virtual e, em seguida, pontos de extremidade. Podemos irá criar uma nova empresa, escolha MySQL (TCP 3306) da lista suspensa e escala na caixa de *conjunto de balanceamento de carga de nova de criar* . Podemos chamará nosso ponto de extremidade de balanceamento de carga `lb-mysql`. Podemos deixará maioria das opções sozinha, com exceção de tempo que irá reduzir para 5 (segundos, mínimos)

Após o ponto de extremidade é criado, vá para `hadb02`, pontos de extremidade e criar uma nova empresa, mas será escolhemos `lb-mysql`, selecione MySQL no menu suspenso. Você também pode usar a CLI do Azure para esta etapa.

Neste momento temos tudo o que precisamos para uma operação manual do cluster.

### <a name="testing-the-load-balanced-set"></a>Teste a carga equilibrada conjunto

Testes podem ser executados de uma máquina fora, usando qualquer cliente MySQL, bem como aplicativos (por exemplo, phpMyAdmin executando como um site do Azure) nesse caso, usamos ferramenta de linha de comando do MySQL em outra caixa de Linux:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Manualmente falhando sobre

Você pode simular failovers agora desligando MySQL, alternando principal do DRBD e iniciar o MySQL novamente.

Em hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Em seguida, na hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Depois que você failover manualmente você pode repetir consulta remota e ele devem estar trabalhando perfeitamente.

## <a name="setting-up-corosync"></a>Configurando Corosync

Corosync é a infraestrutura de cluster subjacente necessária para Pacemaker trabalhar. Para usuários de v1 e v2 pulsação (e outras metodologias como Ultramonkey) Corosync é uma divisão das funcionalidades de CRM, enquanto Pacemaker permanece mais semelhante ao pulsações em funcionalidade.

A principal restrição para Corosync no Azure é que Corosync prefere difusão seletiva transmissão em comunicações de difusão ponto a ponto, mas apenas dá suporte para o sistema de rede do Microsoft Azure difusão ponto a ponto.

Felizmente, Corosync tem um modo de difusão ponto a ponto de trabalho e a única restrição real, como todos os nós não estão se comunicando entre si *automagically*, você precisa definir os nós nos seus arquivos de configuração, incluindo seus endereços IP. Podemos usar os arquivos de exemplo Corosync para difusão ponto a ponto e alteração apenas vincular endereço, listas de nó e diretório de log (Ubuntu usa `/var/log/corosync` enquanto o exemplo arquivos usam `/var/log/cluster`) e habilitando ferramentas de quorum.

**Observação o `transport: udpu` diretiva abaixo e os endereços IP definidos manualmente para os nós**.

Em `/etc/corosync/corosync.conf` para ambos os nós:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Vamos copiar esse arquivo de configuração em ambas as VMs e iniciar Corosync em ambos os nós:

    sudo service start corosync

Logo depois de iniciar o serviço de cluster deve ser estabelecido no anel atual e quorum deve ser constituíram. Podemos verificar essa funcionalidade consultando logs ou:

    sudo corosync-quorumtool –l

Uma saída semelhante a imagem abaixo deverá seguir:

![corosync-quorumtool - l exemplo de saída](media/virtual-machines-linux-classic-mysql-cluster/image001.png)

## <a name="setting-up-pacemaker"></a>Configurando Pacemaker

Pacemaker usa o cluster para monitorar para recursos, definir quando descem primárias e alterne esses recursos para secundários. Recursos podem ser definidos em um conjunto de scripts disponíveis ou LSB (como inicialização) scripts, entre outras opções.

Queremos Pacemaker para "proprietário", o recurso DRBD, o ponto de montagem e o serviço MySQL. Se Pacemaker pode ativar e desativar DRBD, monte- / umount-la e iniciar/parar MySQL na ordem correta quando algo ruim acontece com o principal, nosso programa de instalação estiver concluída.

Quando você instala Pacemaker, sua configuração deve ser simple o suficiente, algo como:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

Verifique-executando `sudo crm configure show`. Agora, crie um arquivo (digamos, `/tmp/cluster.conf`) com os seguintes recursos:

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"

    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"

    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"

    primitive mysqld lsb:mysql

    group mysql fs_mysql mysqld

    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master

    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start

    property stonith-enabled=false

    property no-quorum-policy=ignore

E agora carregá-lo na configuração (só é necessário fazer isso em um nó):

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

Além disso, certifique-se de que Pacemaker começa na inicialização em ambos os nós:

    sudo update-rc.d pacemaker defaults

Após alguns segundos e usando `sudo crm_mon –L`, verifique se um dos seus nós tornou o mestre para o cluster e está executando todos os recursos. Você pode usar montagem e ps para verificar se os recursos estão em execução.

A captura de tela a seguir mostra `crm_mon` com um nó parado (sair usando Control-C)

![nó crm_mon interrompido](media/virtual-machines-linux-classic-mysql-cluster/image002.png)

E esta captura de tela mostra ambos os nós, com um mestre e um auxiliar:

![crm_mon operacionais mestre/escravo](media/virtual-machines-linux-classic-mysql-cluster/image003.png)

## <a name="testing"></a>Teste

Estamos prontos para uma simulação de failover automático. Há duas maneiras de fazer isso: suaves e disco rígido. A forma suave é usando a função de desligamento do cluster: ``crm_standby -U `uname -n` -v on``. Usando isso no mestre, o auxiliar assumirá. Lembre-se de definir essa opção novamente para desativá-lo (crm_mon informará um nó estiver em espera caso contrário)

O disco rígido maneira é desligar a máquina virtual de principal (hadb01) através do portal ou alterando a runlevel na VM (ou seja, interromper, desligamento) e em seguida, estamos ajudando Corosync e Pacemaker por sinalização indo do mestre para baixo. Podemos testar isso (útil para janelas de manutenção), mas podemos também pode forçar o cenário congelando apenas a máquina virtual.

## <a name="stonith"></a>STONITH

Ele deve ser possível emitir um desligamento de máquina virtual por meio da CLI do Azure em vez de um script STONITH que controla um dispositivo físico. Você pode usar `/usr/lib/stonith/plugins/external/ssh` como uma base e habilitar STONITH na configuração do cluster. CLI Azure deve ser instalado globalmente e o perfil/configurações de publicação devem ser carregado para o usuário do cluster.

Código de exemplo para o recurso disponível no [GitHub](https://github.com/bureado/aztonith). Você precisa alterar a configuração do cluster adicionando procedimentos para `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**Observação:** o script não executará superiores/inferiores verificações. O recurso SSH original tinha 15 verificações de ping mas tempo de recuperação para uma máquina virtual do Azure pode ser mais variável.

## <a name="limitations"></a>Limitações

Aplicam as seguintes limitações:

- O script de recurso DRBD linbit que gerencia DRBD como um recurso em usos de Pacemaker `drbdadm down` quando desligando um nó, mesmo se o nó apenas vai em espera. Isso não é ideal, pois o auxiliar será não ser sincronizando o recurso DRBD enquanto o mestre obtém gravações. Se o mestre não falhar generosamente, o auxiliar pode assumir um estado de sistema de arquivos mais antigo. Há duas maneiras possíveis de solucionar isso:
  - Impondo um `drbdadm up r0` em todos os nós de cluster por meio de um local monitoração (não clusterized), ou,
  - Editando o linbit DRBD script lembrando-se de que `down` não é chamado, em `/usr/lib/ocf/resource.d/linbit/drbd`.
- Balanceador de carga precisa de pelo menos 5 segundos para responder, para que aplicativos devem ser reconhecimento de cluster e ser mais tolerância a falhas de tempo limite; outras arquiteturas também pode ajudar, por exemplo filas do aplicativo, middlewares de consulta, etc.
- Ajuste de MySQL é necessária para garantir a gravação é feita em um ritmo são e caches são transferidos para o disco frequentemente possível para minimizar a perda de memória
- Escrever desempenho serão dependente na máquina virtual interconexão no switch virtual como esse é o mecanismo usado por DRBD para replicar o dispositivo
