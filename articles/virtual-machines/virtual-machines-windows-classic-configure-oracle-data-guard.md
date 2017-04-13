<properties
    pageTitle="Configurando o protetor de dados do Oracle VMs | Microsoft Azure"
    description="Percorrer um tutorial para configurar e implementando protetor de dados do Oracle no Azure máquinas virtuais para alta disponibilidade e recuperação de desastres."
    services="virtual-machines-windows"
    authors="rickstercdn"
    manager="timlt"
    documentationCenter=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/06/2016"
    ms.author="rclaus" />

#<a name="configuring-oracle-data-guard-for-azure"></a>Configurando a proteção de dados Oracle para o Azure


Este tutorial demonstra como configurar e implementar protetor de dados do Oracle no ambiente de máquinas virtuais do Azure para alta disponibilidade e recuperação de dados. O tutorial se concentra nos replicação unidirecional para bancos de dados não - RAC Oracle.

Proteção de dados do Oracle oferece suporte a proteção de dados e recuperação para banco de dados Oracle. É um simples, alto desempenho, solução para soltar para recuperação de dados, proteção de dados e alta disponibilidade para todo o banco de dados do Oracle.

Este tutorial supõe que você já tem conhecimento teórico e prático em conceitos de banco de dados Oracle alta disponibilidade e recuperação de dados. Para obter informações, consulte o [site da web Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html) e também o [guia de administração e conceitos de proteção de dados do Oracle](https://docs.oracle.com/cd/E11882_01/server.112/e41134/toc.htm).

Além disso, o tutorial supõe que você já tiver implementado os seguintes pré-requisitos:

- Você já tiver revisado a seção de alta disponibilidade e considerações de recuperação de desastres no tópico com as [imagens de máquina Virtual de Oracle - considerações diversos](virtual-machines-windows-classic-oracle-considerations.md) . Azure suporta instâncias de banco de dados Oracle autônomo, mas não Oracle Real Application Clusters (Oracle RAC) no momento.


- Você criou duas máquinas virtuais (VMs) no Azure usando a mesma plataforma fornecida imagem Oracle Enterprise Edition. Verifique se as máquinas virtuais no [mesmo serviço de nuvem](virtual-machines-windows-load-balance.md) e na mesma rede Virtual para garantir que eles possam acessar uns aos outros sobre o endereço IP privado persistente. Além disso, é recomendável colocar as VMs no mesmo [disponibilidade definida](virtual-machines-windows-manage-availability.md) para permitir que o Azure para colocá-los em domínios de falha separada e atualizar domínios. Proteção de dados Oracle só está disponível com Oracle Database Enterprise Edition. Cada máquina deve ter pelo menos 2 GB de memória e 5 GB de espaço em disco. Para obter as informações mais atualizadas na plataforma fornecida tamanhos de máquina virtual, consulte [Tamanhos de máquina Virtual do Azure](virtual-machines-windows-sizes.md). Se precisar de volume de disco adicional para as VMs, você pode anexar discos adicionais. Para obter informações, consulte [como anexar um disco de dados para uma máquina Virtual](virtual-machines-windows-classic-attach-disk.md).



- Você configurou os nomes de máquina Virtual como "Local1" para a máquina virtual e "Machine2" para a máquina virtual espera principal no Azure portal clássico.

- Você configurou a variável de ambiente **ORACLE_HOME** para apontar para o mesmo caminho de instalação do oracle raiz no primário e espera máquinas virtuais, tais como `C:\OracleDatabase\product\11.2.0\dbhome_1\database`.

- Faça logon no seu servidor Windows como um membro do grupo **administradores** ou membro do grupo **ORA_DBA** .

Neste tutorial, você irá:

Implementar o ambiente de banco de dados físico em espera

1. Criar um banco de dados principal

2. Preparar o banco de dados principal para a criação de banco de dados em espera

    1. Habilitar o log de forçada

    2. Criar um arquivo de senha

    3. Configurar um log de refazer em espera

    4. Habilitar o arquivamento

    5. Definir parâmetros de inicialização do banco de dados principal

Criar um banco de dados em espera físico

1. Preparar um arquivo de parâmetro de inicialização do banco de dados em espera

2. Configurar o ouvinte e tnsnames para dar suporte ao banco de dados em máquinas primárias e em espera

    1. Configurar listener.ora em ambos os servidores para manter as entradas para ambos os bancos de dados

    2. Para manter as entradas para bancos de dados principais e em espera, configure Tnsnames nas máquinas virtuais primário e em espera. 

    3. Inicie o ouvinte e verifique tnsping em ambas as máquinas virtuais para ambos os serviços.

3. Iniciar a instância espera nomount estado

4. Use RMAN para clonar o banco de dados e criar um banco de dados em espera

5. Inicie o banco de dados em espera físico no modo de recuperação gerenciados

6. Verifique se o banco de dados em espera físico

> [AZURE.IMPORTANT] Este tutorial foi configurado e testado em relação a seguinte configuração de hardware e software:
>
>|                      | **Banco de dados principal**                      | **Banco de dados em espera**                      |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Versão do Oracle**   | Versão do Enterprise Oracle11g (11.2.0.4.0) | Versão do Enterprise Oracle11g (11.2.0.4.0) |
>| **Nome da máquina**     | Local1                                  | Machine2                                  |
>| **Sistema Operacional** | Windows 2008 R2                           | Windows 2008 R2                           |
>| **Oracle SID**       | TESTE                                      | TESTE\_STBY                                |
>| **Memória**           | Mínimo 2 GB                                  | Mínimo 2 GB                                  |
>| **Espaço em disco**       | Min 5 GB                                  | Min 5 GB                                  |

Para obter versões subsequentes do banco de dados Oracle e proteção de dados do Oracle, talvez haja algumas alterações adicionais que você precisa implementar. Para obter informações específicas da versão mais atualizadas, consulte a documentação de [Proteção de dados](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e o [Banco de dados do Oracle](http://www.oracle.com/us/corporate/features/database-12c/index.html) no site do Oracle.

##<a name="implement-the-physical-standby-database-environment"></a>Implementar o ambiente de banco de dados físico em espera
### <a name="1-create-a-primary-database"></a>1. criar um banco de dados principal

- Crie um banco de dados principal "Testar" na máquina Virtual primária. Para obter informações, consulte Criar e configurar um banco de dados do Oracle.
- Para ver o nome do seu banco de dados, conecte-se ao banco de dados como o usuário do sistema com a função SYSDBA no SQL * Plus prompt de comando e executar a instrução a seguir:

        SQL> select name from v$database;

        The result will display like the following:

        NAME
        ---------
        TEST
- Em seguida, consulta os nomes dos arquivos de banco de dados do modo de exibição de sistema dba_data_files:

        SQL> select file_name from dba_data_files;
        FILE_NAME
        -------------------------------------------------------------------------------
        C:\ <YourLocalFolder>\TEST\USERS01.DBF
        C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
        C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
        C:\<YourLocalFolder>\TEST\SYSTEM01.DBF
        C:\<YourLocalFolder>\TEST\EXAMPLE01.DBF

### <a name="2-prepare-the-primary-database-for-standby-database-creation"></a>2. preparar o banco de dados principal para a criação de banco de dados em espera

Antes de criar um banco de dados em espera, é recomendável que você garantir que o banco de dados principal está configurado corretamente. A seguir está uma lista das etapas que você precisa realizar:

1. Habilitar o log de forçada
2. Criar um arquivo de senha
3. Configurar um log de refazer em espera
4. Habilitar o arquivamento
5. Definir parâmetros de inicialização do banco de dados principal

#### <a name="enable-forced-logging"></a>Habilitar o log de forçada

Para implementar um banco de dados em espera, precisamos habilitar 'Forçado log' no banco de dados principal. Essa opção garante que, mesmo se uma operação de 'nologging' for concluída, forçar log tem precedência e todas as operações estiver conectadas aos logs de refazer. Portanto, podemos garantir que tudo no banco de dados principal está conectado e replicação para em espera inclui todas as operações do banco de dados principal. Para habilitar o log de forçar, execute a instrução de banco de dados alter:

    SQL> ALTER DATABASE FORCE LOGGING;

    Database altered.

#### <a name="create-a-password-file"></a>Criar um arquivo de senha

Para poder enviar e aplicar os logs arquivados do servidor primário para o servidor de espera, a senha do sistema deve ser idêntica nos servidores primário e em espera. É por isso que você criar um arquivo de senha do banco de dados principal e copiá-lo para o servidor de espera.

>[AZURE.IMPORTANT] Ao usar o banco de dados do Oracle 12c, há um novo usuário, **SYSDG**, que você pode usar para administrar protetor de dados do Oracle. Para obter mais informações, consulte [alterações no banco de dados do Oracle 12 c lançamento](http://docs.oracle.com/database/121/UNXAR/release_changes.htm#UNXAR404).

Além disso, verifique se o ORACLE\_ambiente HOME já está definido no local1. Caso contrário, defina-o como uma variável de ambiente usando a caixa de diálogo variáveis de ambiente. Para acessar essa caixa de diálogo, inicie o utilitário de **sistema** clicando duas vezes no ícone sistema no **Painel de controle**; Clique na guia **Avançado** e escolha **Variáveis de ambiente**. Para definir as variáveis de ambiente, clique no botão **novo** em **Variáveis do sistema**. Após configurar as variáveis de ambiente, feche o prompt de comando do Windows existente e abra um novo.

Execute a seguinte instrução para alternar para o Oracle\_diretório inicial, como c:\\OracleDatabase\\produto\\11.2.0\\dbhome\_1\\banco de dados.

    cd %ORACLE_HOME%\database

Em seguida, crie um arquivo de senha usando o utilitário de criação de arquivo de senha, [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm). No Windows prompt de comando no Local1, execute o seguinte comando definindo o valor de senha como a senha do **sistema**:

    ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

Esse comando cria um arquivo de senha, nomeado como PWDTEST.ora, em ORACLE\_HOME\\diretório de banco de dados. Você deve copiá-lo para % ORACLE\_inicial %\\directory no Machine2 de banco de dados manualmente.

#### <a name="configure-a-standby-redo-log"></a>Configurar um log de refazer em espera

Em seguida, você precisa configurar um Log de refazer espera para que o principal corretamente possa receber Refazer quando ele se transformar em um modo de espera. Pré-criá-las aqui também permite que os logs de refazer espera seja criada automaticamente em espera. É importante configurar os Logs de refazer espera (SRL) com o mesmo tamanho como logs de refazer o online. O tamanho dos arquivos de log de refazer espera atual deve corresponder exatamente o tamanho dos arquivos de log de refazer online do banco de dados principal atual.

Execute a seguinte instrução no SQL\*PLUS prompt de comando no local1. O arquivo de log de $ v é um modo de exibição de sistema que contém informações sobre os arquivos de log de refazer.

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE    MEMBER                                                       IS_
    ---------- ------- ------- ------------------------------------------------------------ ---
    3         ONLINE  C:\<YourLocalFolder>\TEST\REDO03.LOG               NO
    2         ONLINE  C:\<YourLocalFolder>\TEST\REDO02.LOG               NO
    1         ONLINE  C:\<YourLocalFolder>\TEST\REDO01.LOG               NO
    Next, query the v$log system view, displays log file information from the control file.
    SQL> select bytes from v$log;
    BYTES
    ----------
    52428800
    52428800
    52428800


Observe que 52428800 é 50 megabytes.

Em seguida, no SQL\*Plus janela, execute as seguintes instruções para adicionar um novo grupo de arquivo de log de refazer espera um banco de dados em espera e especificar um número que identifica o grupo usando a cláusula de grupo. Usando os números de grupo pode fazer a administração de grupos de arquivos de log de refazer espera mais fácil:

    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:\<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:\<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:\<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
    Database altered.

Em seguida, execute o seguinte modo de exibição do sistema para listar informações sobre Refazer arquivos de log. Essa operação também verifica se os grupos de arquivos de log de refazer espera foram criados:

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE MEMBER IS_
    ---------- ------- ------- --------------------------------------------- ---
    3         ONLINE C:\<YourLocalFolder>\TEST\REDO03.LOG NO
    2         ONLINE C:\<YourLocalFolder>\TEST\REDO02.LOG NO
    1         ONLINE C:\<YourLocalFolder>\TEST\REDO01.LOG NO
    4         STANDBY C:\<YourLocalFolder>\TEST\REDO04.LOG
    5         STANDBY C:\<YourLocalFolder>\TEST\REDO05.LOG NO
    6         STANDBY C:\<YourLocalFolder>\TEST\REDO06.LOG NO
    6 rows selected.

#### <a name="enable-archiving"></a>Habilitar o arquivamento

Em seguida, habilite o arquivamento executando as instruções a seguir para colocar o banco de dados principal no modo de ARCHIVELOG e ativar o arquivamento automático. Você pode habilitar o modo de log de arquivo morto montagem do banco de dados e, em seguida, executando o comando archivelog.

Primeiro, faça logon como sysdba. No prompt de comando do Windows, execute:

    sqlplus /nolog

    connect / as sysdba

Em seguida, desligamento o banco de dados no SQL\*Plus prompt de comando:

    SQL> shutdown immediate;
    Database closed.
    Database dismounted.
    ORACLE instance shut down.

Em seguida, execute o comando de montagem de inicialização montar o banco de dados. Isso garante que Oracle associa a instância do banco de dados especificado.

    SQL> startup mount;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.

Em seguida, execute:

    SQL> alter database archivelog;
    Database altered.

Em seguida, execute o Alter instrução de banco de dados com a cláusula aberta para disponibilizar o banco de dados para uso normal:

    SQL> alter database open;

    Database altered.

#### <a name="set-primary-database-initialization-parameters"></a>Definir parâmetros de inicialização do banco de dados principal

Para configurar a proteção de dados, você precisa criar e configurar os parâmetros de espera em um pfile regular (arquivo de parâmetro de inicialização do texto) primeiro. Quando o pfile estiver pronta, é necessário convertê-lo em um arquivo de parâmetro de servidor (SPFILE).

Você pode controlar o ambiente de proteção de dados usando os parâmetros na inicialização. Arquivo de ORA. Quando seguindo este tutorial, você precisa atualizar o banco de dados principal inicial. ORA para que ele pode conter ambas as funções: principal ou em espera.

    SQL> create pfile from spfile;
    File created.

Em seguida, você precisa editar o pfile para adicionar os parâmetros em espera. Para fazer isso, abra o INITTEST. ORA arquivo no local de % ORACLE\_inicial %\\banco de dados. Em seguida, acrescente as instruções a seguir para o arquivo de INITTEST.ora. A convenção de nomenclatura para sua inicialização. Arquivo de ORA é inicialização\<YourDatabaseName\>. ORA.

    db_name='TEST'
    db_unique_name='TEST'
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
    LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
    LOG_ARCHIVE_DEST_STATE_1=ENABLE
    LOG_ARCHIVE_DEST_STATE_2=ENABLE
    REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE
    LOG_ARCHIVE_FORMAT=%t_%s_%r.arc
    LOG_ARCHIVE_MAX_PROCESSES=30
    # Standby role parameters --------------------------------------------------------------------
    fal_server=TEST_STBY
    fal_client=TEST
    standby_file_management=auto
    db_file_name_convert='TEST_STBY','TEST'
    log_file_name_convert='TEST_STBY','TEST'
    # ---------------------------------------------------------------------------------------------


O bloco de declaração anterior inclui três itens de configuração importantes:
-   **LOG_ARCHIVE_CONFIG …:** Definir as identificações exclusivas de banco de dados usando esta política.
-   **LOG_ARCHIVE_DEST_1 …:** Definir o local da pasta de arquivamento local usando esta política. Recomendamos que você crie um novo diretório para necessidades de arquivamento de seu banco de dados e especifique o local de arquivamento local usando esta política explicitamente em vez de usar padrão pasta % da Oracle ORACLE_HOME%\database\archive.
-   **LOG_ARCHIVE_DEST_2... LGWR ASYNC …:** você define um processo de gravador de log assíncrono (LGWR) para coletar dados de transação de refazer e transmiti-lo para destinos em espera. Aqui, o DB_UNIQUE_NAME Especifica um nome exclusivo para o banco de dados no servidor de espera de destino.

Depois que o novo arquivo de parâmetro estiver pronto, você precisa criar o spfile dele.

Desligamento primeiro, o banco de dados:

    SQL> shutdown immediate;

    Database closed.

    Database dismounted.

    ORACLE instance shut down.

Em seguida, execute o comando de nomount de inicialização da seguinte maneira:

    SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes

Agora, crie um spfile:

    SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

    File created.

Em seguida, desligamento o banco de dados:

    SQL> shutdown immediate;

    ORA-01507: database not mounted

Em seguida, use o comando de inicialização para iniciar uma instância:

    SQL> startup;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.
    Database opened.

##<a name="create-a-physical-standby-database"></a>Criar um banco de dados em espera físico
Esta seção aborda as etapas que você deve executar no Machine2 para preparar o banco de dados em espera físico.

Primeiro, você precisa área de trabalho remota Machine2 através do portal de clássico Azure.

Em seguida, no servidor em espera (Machine2), criar todas as pastas necessárias para o banco de dados em espera, como c:\\\<YourLocalFolder\>\\teste. Enquanto segue neste tutorial, certifique-se de que a estrutura da pasta corresponde a estrutura da pasta no local1 manter todos os arquivos necessários, como arquivos de controlfile, arquivos de dados, redologfiles, udump, bdump e cdump. Além disso, definir a ORACLE\_HOME e ORACLE\_variáveis de ambiente BASE em Machine2. Caso contrário, defini-los como uma variável de ambiente usando a caixa de diálogo variáveis de ambiente. Para acessar essa caixa de diálogo, inicie o utilitário de **sistema** clicando duas vezes no ícone sistema no **Painel de controle**; Clique na guia **Avançado** e escolha **Variáveis de ambiente**. Para definir as variáveis de ambiente, clique no botão **novo** sob as **Variáveis do sistema**. Após configurar as variáveis de ambiente, você precisará fechar o prompt de comando do Windows existente e abra um novo para ver as alterações.

Em seguida, siga estas etapas:

1. Preparar um arquivo de parâmetro de inicialização do banco de dados em espera

2. Configurar o ouvinte e tnsnames para dar suporte ao banco de dados em máquinas primárias e em espera

    1. Configurar listener.ora em ambos os servidores para manter as entradas para ambos os bancos de dados

    2. Configurar Tnsnames nas máquinas virtuais primário e em espera para manter as entradas para bancos de dados principais e em espera

    3. Inicie o ouvinte e verifique tnsping em ambas as máquinas virtuais para ambos os serviços.

3. Iniciar a instância espera nomount estado

4. Use RMAN para clonar o banco de dados e criar um banco de dados em espera

5. Inicie o banco de dados em espera físico no modo de recuperação gerenciados

6. Verifique se o banco de dados em espera físico

### <a name="1-prepare-an-initialization-parameter-file-for-standby-database"></a>1. prepare um arquivo de parâmetro de inicialização do banco de dados em espera

Esta seção demonstra como preparar um arquivo de parâmetro de inicialização do banco de dados em espera. Para fazer isso, primeiro copie o INITTEST. ORA arquivo de máquina 1 para Machine2 manualmente. Você deve ser capaz de ver o INITTEST. ORA arquivo no % ORACLE\_inicial %\\pasta de banco de dados em ambas as máquinas. Em seguida, modifique o arquivo INITTEST.ora Machine2 para configurá-la para a função espera conforme especificado abaixo:

    db_name='TEST'
    db_unique_name='TEST_STBY'
    db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
    db_file_name_convert=’TEST’,’TEST_STBY’
    log_file_name_convert='TEST','TEST_STBY'


    job_queue_processes=10
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
    LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE)
    LOG_ARCHIVE_DEST_STATE_1='ENABLE'
    LOG_ARCHIVE_DEST_STATE_2='ENABLE'
    LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
    LOG_ARCHIVE_MAX_PROCESSES=30


O bloco de declaração anterior inclui dois itens de configuração importantes:

-   **\*. LOG_ARCHIVE_DEST_1:** você precisa criar a pasta de c:\OracleDatabase\TEST_STBY\archives no Machine2 manualmente.
-   **\*. LOG_ARCHIVE_DEST_2:** essa é uma etapa opcional. Você define como ele pode ser necessária quando o computador principal estiver em manutenção e máquina espera se torna um banco de dados principal.

Em seguida, é necessário iniciar a instância de espera. No servidor de banco de dados em espera, digite o seguinte comando em um prompt de comando do Windows para criar uma instância do Oracle, criando um serviço do Windows:

    oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

O comando **Oradim** cria uma instância do Oracle, mas não é iniciado. Você pode encontrá-lo em c:\\OracleDatabase\\produto\\11.2.0\\dbhome\_1\\diretório BIN.

##<a name="configure-the-listener-and-tnsnames-to-support-the-database-on-primary-and-standby-machines"></a>Configurar o ouvinte e tnsnames para dar suporte ao banco de dados em máquinas primárias e em espera
Antes de criar um banco de dados em espera, você precisa certificar-se de que os bancos de dados principais e espera em sua configuração podem conversar com os outros. Para fazer isso, você precisa configurar o ouvinte e TNSNames manualmente ou usando o utilitário de configuração de rede NETCA. Esta é uma tarefa obrigatória quando você usa o utilitário de recuperação Manager (RMAN).

### <a name="configure-listenerora-on-both-servers-to-hold-entries-for-both-databases"></a>Configurar listener.ora em ambos os servidores para manter as entradas para ambos os bancos de dados

Área de trabalho remota para local1 e editar o arquivo listener.ora conforme especificado abaixo. Quando você edita o arquivo listener.ora, certifique-se de que a abertura e o parêntese de fechamento alinham na mesma coluna. Você pode encontrar o arquivo listener.ora na seguinte pasta: c:\\OracleDatabase\\produto\\11.2.0\\dbhome\_1\\rede\\administrador\\.

    # listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )

Área de trabalho remota, Avançar para Machine2 e edite o listener.ora de arquivo da seguinte maneira: # listener.ora arquivo de configuração de rede: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test_stby)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )


### <a name="configure-tnsnamesora-on-the-primary-and-standby-virtual-machines-to-hold-entries-for-both-primary-and-standby-databases"></a>Configurar Tnsnames nas máquinas virtuais primário e em espera para manter as entradas para bancos de dados principais e em espera

Área de trabalho remota para local1 e editar o arquivo Tnsnames conforme especificado abaixo. Você pode encontrar o arquivo Tnsnames na seguinte pasta: c:\\OracleDatabase\\produto\\11.2.0\\dbhome\_1\\rede\\administrador\\.

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )

Área de trabalho remota Machine2 e edite o Tnsnames arquivo da seguinte maneira:

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )


### <a name="start-the-listener-and-check-tnsping-on-both-virtual-machines-to-both-services"></a>Inicie o ouvinte e verifique tnsping em ambas as máquinas virtuais para ambos os serviços.

Abra um novo prompt de comando do Windows em máquinas virtuais primário e em espera e execute as instruções a seguir:

    C:\Users\DBAdmin>tnsping test

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test)))
    OK (0 msec)


    C:\Users\DBAdmin>tnsping test_stby

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test_stby)))
    OK (260 msec)


##<a name="start-up-the-standby-instance-in-nomount-state"></a>Iniciar a instância espera nomount estado
Para configurar o ambiente para dar suporte a banco de dados em espera na máquina Virtual espera (MACHINE2).

Primeiro, copie o arquivo de senha da máquina primária (local1) na máquina espera (Machine2) manualmente. Isso é necessário como a senha do **sistema** deve ser idêntica em ambos os computadores.

Em seguida, abra o prompt de comando do Windows no Machine2 e configurar as variáveis de ambiente para apontar para o banco de dados de espera da seguinte maneira:

    SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
    SET ORACLE_SID=TEST_STBY

Em seguida, iniciar o banco de dados de espera em estado de nomount e gere um spfile.

Inicie o banco de dados:

    SQL>shutdown immediate;

    SQL>startup nomount
    ORACLE instance started.

    Total System Global Area  747417600 bytes
    Fixed Size                  2179496 bytes
    Variable Size             473960024 bytes
    Database Buffers          264241152 bytes
    Redo Buffers                7036928 bytes


##<a name="use-rman-to-clone-the-database-and-to-create-a-standby-database"></a>Use RMAN para clonar o banco de dados e criar um banco de dados em espera
Você pode usar o utilitário de recuperação Manager (RMAN) para fazer qualquer cópia de backup do banco de dados principal para criar o banco de dados em espera físico.

Área de trabalho remota para a espera Máquina Virtual (MACHINE2) e executar o utilitário RMAN especificando uma conexão completa de cadeia de caracteres para os dois o destino (banco de dados principal, Local1) e AUXILIAR (espera banco de dados, Machine2) instâncias.

>[AZURE.IMPORTANT] Não use a autenticação do sistema operacional porque não há nenhum banco de dados no computador servidor em espera ainda.

    C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY

    RMAN>DUPLICATE TARGET DATABASE
      FOR STANDBY
      FROM ACTIVE DATABASE
      DORECOVER
        NOFILENAMECHECK;

##<a name="start-the-physical-standby-database-in-managed-recovery-mode"></a>Inicie o banco de dados em espera físico no modo de recuperação gerenciados
Este tutorial demonstra como criar um banco de dados em espera físico. Para obter informações sobre como criar um banco de dados em espera lógico, consulte a documentação do Oracle.

Abrir SQL\*além do prompt de comando e habilitar o protetor de dados no servidor (MACHINE2) ou espera Máquina Virtual da seguinte maneira:

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

Quando você abre o banco de dados em espera no modo de **montar** , o log de arquivo morto remessa continua e o processo de recuperação gerenciados continua log aplicando no banco de dados em espera. Isso garante que o banco de dados em espera permanece atualizado com o banco de dados principal. Observe que o banco de dados em espera não pode ser acessível para fins de relatório durante esse período.

Quando você abre o banco de dados em espera no modo **Somente leitura** , o arquivo morto envio de log continua. Mas interrompe o processo de recuperação gerenciado. Isso faz com que o banco de dados em espera tornar-se cada vez mais desatualizado até que o processo de recuperação gerenciado é retomado. Você pode acessar o banco de dados em espera para fins de relatórios durante esse tempo, mas dados podem não refletir as alterações mais recentes.

Em geral, recomendamos que você mantenha o banco de dados em espera no modo de **montar** para manter os dados no banco de dados em espera atualizado se houver uma falha do banco de dados principal. No entanto, você pode manter o banco de dados em espera no modo **De leitura somente** para fins de relatórios dependendo de requisitos do seu aplicativo. As etapas a seguir demonstram como habilitar o protetor de dados no modo somente leitura usando SQL\*Plus:

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE OPEN READ ONLY;


##<a name="verify-the-physical-standby-database"></a>Verifique se o banco de dados em espera físico
Esta seção demonstra como verificar a configuração de alta disponibilidade como administrador.

Abrir SQL\*Plus janela prompt de comando e seleção arquivados Refazer log na máquina Virtual espera (Machine2):

    SQL> show parameters db_unique_name;

    NAME                                TYPE       VALUE
    ------------------------------------ ----------- ------------------------------
    db_unique_name                      string     TEST_STBY

    SQL> SELECT NAME FROM V$DATABASE

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    45                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   NO
    47                    23-FEB-14   23-FEB-14   NO

Abrir SQL * Plus prompt de comando janela e alternar arquivos de log na máquina principal (local1):

    SQL> alter system switch logfile;
    System altered.

    SQL> archive log list
    Database log mode              Archive Mode
    Automatic archival             Enabled
    Archive destination            C:\OracleDatabase\archive
    Oldest online log sequence     69
    Next log sequence to archive   71
    Current log sequence           71

Verifique o log de refazer arquivados na máquina Virtual espera (Machine2):

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   YES
    48                    23-FEB-14   23-FEB-14   YES

    49                    23-FEB-14   23-FEB-14   YES
    50                    23-FEB-14   23-FEB-14   IN-MEMORY

Verificar se há qualquer espaço na máquina Virtual espera (Machine2):

    SQL> SELECT * FROM V$ARCHIVE_GAP;
    no rows selected.

Outro método de verificação poderia ser failover para o banco de dados em espera e, em seguida, teste se é possível failback ao banco de dados principal. Para ativar o banco de dados em espera, como um banco de dados principal, use as instruções a seguir:

    SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
    SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

Se você não tiver ativado flashback no banco de dados principal original, é recomendável que você descartar o banco de dados principal original e recrie como um banco de dados em espera.

Recomendamos que você habilite o banco de dados do flashback em primário e os bancos de dados em espera. Quando ocorre um failover, o banco de dados principal pode ser atualizado de volta para o tempo antes do failover e rapidamente convertido em um banco de dados em espera.

##<a name="additional-resources"></a>Recursos adicionais
[Imagens de máquinas virtuais do Oracle do Azure](virtual-machines-windows-classic-oracle-images.md)
