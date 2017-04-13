<properties
    pageTitle="Configurando o Oracle GoldenGate VMs | Microsoft Azure"
    description="Percorrer um tutorial para configurar e implementando Oracle GoldenGate em VMs de servidor do Windows Azure para alta disponibilidade e recuperação de desastres."
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


#<a name="configuring-oracle-goldengate-for-azure"></a>Configurando Oracle GoldenGate do Azure


Este tutorial demonstra como configurar o Oracle GoldenGate ambiente de máquinas virtuais do Azure para alta disponibilidade e recuperação de dados. O tutorial destaca a [replicação bidirecional](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm) para bancos de dados não - RAC Oracle e requer que ambos os sites estão ativos.

Oracle GoldenGate oferece suporte a distribuição de dados e integração de dados. Ele permite que você configurar uma distribuição de dados e a solução de sincronização de dados por meio da configuração de replicação Oracle-Oracle e fornece uma solução flexível alta disponibilidade. Oracle GoldenGate suplementa protetor de dados Oracle com os recursos de replicação para habilitar as migrações e atualizações de distribuição e inatividade de informações da empresa. Para obter informações detalhadas, consulte [Usando Oracle GoldenGate com protetor de dados do Oracle](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm).

Oracle GoldenGate contém os seguintes componentes principais: extrair, dados bomba, Replicat, trilhas ou extrair arquivos, pontos de verificação, gerente e coletor. Para fazer replicação bidirecional entre dois sites, você precisa criar e iniciar a todos os componentes em ambos os sites. Para obter informações detalhadas sobre a arquitetura do Oracle GoldenGate, consulte o [Guia de GoldenGate do Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/index.html).

Este tutorial supõe que você já tem conhecimento teórico e prático em conceitos de banco de dados Oracle alta disponibilidade e recuperação de dados, bem como [GoldenGate Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/index.html). Para obter mais informações, consulte o [site da web Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html).

Além disso, o tutorial supõe que você já tiver implementado os seguintes pré-requisitos:

- Você já tiver revisado a seção de alta disponibilidade e considerações de recuperação de desastres no tópico com as [imagens de máquina Virtual de Oracle - considerações diversos](virtual-machines-windows-classic-oracle-considerations.md) . Observe que Azure suporta instâncias de banco de dados Oracle autônomo, mas não Oracle Real Application Clusters (Oracle RAC) no momento.

- Baixar o software Oracle GoldenGate do site [Downloads do Oracle](http://www.oracle.com/us/downloads/index.html) . Você selecionou o Middleware de fusão do produto Pack Oracle – integração de dados. Em seguida, selecionou Oracle GoldenGate em Oracle v11.2.1 Media Pack para o Microsoft Windows x64 (64 bits) para um banco de dados do Oracle 11g. Em seguida, baixe Oracle GoldenGate V11.2.1.0.3 para Oracle 11g 64 bits no Windows 2008 (64 bits).

- Você criou duas máquinas virtuais (VMs) no Azure usando Oracle Enterprise Edition no Windows Server. Certifique-se de que as máquinas virtuais são no [mesmo serviço de nuvem](virtual-machines-linux-load-balance.md) e na mesma [Rede Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para garantir que eles possam acessar uns aos outros sobre o endereço IP privado persistente.

- Você configurou os nomes de máquina Virtual como "MachineGG1" para um Site e "MachineGG2" para o Site B no Azure portal clássico.

- Criou bancos de dados de teste "TestGG1" em um Site e "TestGG2" no local B.

- Faça logon no seu servidor Windows como um membro do grupo Administradores ou membro do grupo **ORA_DBA** .

Neste tutorial, você irá:

1. Configurar o banco de dados no Site A e B de Site  

    1. Executar o carregamento de dados inicial

2. Preparar o Site A e B de Site para replicação de banco de dados

3. Criar todos os objetos necessários para oferecer suporte a replicação DDL

4. Configurar o Gerenciador de GoldenGate no Site A e B

5. Criar grupo extrair e Data Pump processos no Site A e B de Site

    1. Criar processos de extrair e Data Pump em um Site

    2. Criar uma tabela de ponto de verificação de GoldenGate no Site B

    3. Adicionar REPLICAT no Site B

    4. Criar processos de extrair e Data Pump no Site B

    5. Criar uma tabela de ponto de verificação de GoldenGate em um Site

    6. Adicionar REPLICAT em um Site

    7. Adicionar trandata no Site A e B de Site

    8. Iniciar processos extrair e Data Pump em um Site

    9. Iniciar processos extrair e Data Pump no Site B

    10. Iniciar processo de REPLICAT no Site A

    11. Iniciar processo de REPLICAT no Site B

6. Verifique se o processo de replicação bidirecional

>[AZURE.IMPORTANT] Este tutorial foi configuração e testado em relação a configuração de software a seguir:
>
>|                        | **Um banco de dados do site**              | **Banco de dados do site B**              |
>|------------------------|----------------------------------|----------------------------------|
>| **Versão do Oracle**     | Oracle11g lançamento 2 – (11.2.0.1) | Oracle11g lançamento 2 – (11.2.0.1) |
>| **Nome da máquina**       | MachineGG1                       | MachineGG2                       |
>| **Sistema Operacional**   | Windows 2008 R2                  | Windows 2008 R2                  |
>| **Oracle SID**         | TESTGG1                          | TESTGG2                          |
>| **Esquema de replicação** | SCOTT                            | SCOTT                            |

Para obter versões subsequentes do banco de dados Oracle e Oracle GoldenGate, talvez haja algumas alterações adicionais que você precisa implementar. Para as informações específicas de versão mais atualizadas, consulte a documentação de [Banco de dados do Oracle](http://www.oracle.com/us/corporate/features/database-12c/index.html) e [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) no site do Oracle. Por exemplo, para um banco de dados de origem de lançamento 11.2.0.4 e posterior, a captura de DDL é executada pelo servidor logmining assíncrona e requer nenhum disparador especiais, tabelas ou outros objetos de banco de dados esteja instalado. Atualizações de GoldenGate Oracle podem ser executadas sem interromper aplicativos do usuário. O uso de um disparador DDL e objetos de suporte é necessário quando extrair está no modo integrado com 11g fonte bancos de dados Oracle anterior à versão 11.2.0.4. Para obter orientações detalhadas, consulte [Instalando e configurando o Oracle GoldenGate de banco de dados do Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf).

##<a name="1-setup-database-on-site-a-and-site-b"></a>1. configurar o banco de dados no Site A e B de Site
Esta seção explica como executar os pré-requisitos de banco de dados no Site A e B. Você deve executar todas as etapas desta seção em ambos os sites: Site A e B.

Primeira, remota área de trabalho para Site A e B de sites através do portal de clássico Azure. Abra um prompt de comando do Windows e criar um diretório base para arquivos de instalação do Oracle GoldenGate:

    mkdir C:\OracleGG

Em seguida, descompacte e instale o software Oracle GoldenGate nesta pasta. Após essa etapa, você pode iniciar o intérprete de comando do Software de GoldenGate (GGSCI) executando o seguinte comando:

    C:\OracleGG\.\ggsci

Você pode usar [GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm) para executar vários comandos que configurar, controle e monitor GoldenGate Oracle.

Em seguida, execute o seguinte comando para criar todas as subpastas do pacote de instalação:

    GGSCI (Hostname) 1> CREATE SUBDIRS

Execute o seguinte comando para sair do prompt de comando do GGSCI:

    GGSCI (Hostname) 1> EXIT

Em seguida, você precisa criar um usuário de banco de dados, que será usado pelos processos Oracle GoldenGate Manager, extrair e replicação. Observe que você pode criar usuários individuais para cada processo ou configurar somente um usuário comum. Neste tutorial, podemos criar um usuário, que é chamado como ggate. Em seguida, podemos conceder esse usuário os privilégios necessários. Observe que você deve executar as seguintes operações Site A e B.

Abrir SQL\*Plus janela de comando do Site A e B de Site com privilégios de administrador do banco de dados usando **SYSDBA**, como:

    Enter username: / as sysdba

E execute:

    SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata\<DBNAME>\<DBNAME>ggs_data01.dbf' size 200m;
    SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
          grant connect, resource to ggate;
          grant select any dictionary, select any table to ggate;
          grant create table to ggate;
          grant flashback any table to ggate;
          grant execute on dbms_flashback to ggate;
          grant execute on utl_file to ggate;
          grant create any table to ggate;
          grant insert any table to ggate;
          grant update any table to ggate;
          grant delete any table to ggate;
          grant drop any table to ggate;

Em seguida, localize a inicialização\<DatabaseSID\>. ORA arquivo no % ORACLE\_inicial %\\banco de dados de pasta de Site A e B de Site e acrescentar os seguintes parâmetros de banco de dados a INITTEST.ora:

    UNDO\_MANAGEMENT=AUTO
    UNDO\_RETENTION=86400

Para obter uma lista completa de todos os comandos de Oracle GoldenGate GGSCI, consulte [referência para Oracle GoldenGate para Windows](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm).

### <a name="perform-initial-data-load"></a>Executar o carregamento de dados inicial

Você pode executar o carregamento de dados inicial do banco de dados seguindo vários métodos. Por exemplo, você pode usar a [Carga direto do Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm) ou regulares utilitários de importação e exportação para exportar dados da tabela do Site A para o local B.

Para demonstrar o processo de replicação do Oracle GoldenGate, este tutorial demonstra Criando uma tabela no Site A e B do site, usando os seguintes comandos.

Primeiro, abra o SQL\*Plus janela de comando e execute o seguinte comando para criar uma tabela de inventário em bancos de dados do Site A e B de Site:

    create table scott.inventory
    (prod_id number,
    prod_category varchar2(20),
    qty_in_stock number,
    last_dml timestamp default systimestamp);

Em seguida, adicione uma restrição a tabela recém-criado no Site A e bancos de dados do Site B:

    alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

Em seguida, conceder todos os privilégios na tabela de estoque nova para o ggate de usuário no Site A e b Site:

    grant all on scott.inventory to ggate;

Em seguida, criar e ativar um gatilho de banco de dados, INVENTORY_CDR_TRG, na tabela recém-criado para certificar-se de que todas as transações para a nova tabela serão registradas se o usuário não é ggate. Executar essa operação no Site A e B.

    CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
    BEFORE UPDATE
    ON SCOTT.INVENTORY
    REFERENCING NEW AS New OLD AS Old
    FOR EACH ROW
    BEGIN
    IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
    THEN
    :NEW.LAST_DML := SYSTIMESTAMP;
    END IF;
    END;
    /


##<a name="2-prepare-site-a-and-site-b-for-database-replication"></a>2. preparar Site A e B de Site para replicação de banco de dados
Esta seção explica como preparar o Site A e B de Site para replicação de banco de dados. Você deve executar todas as etapas desta seção em ambos os sites: Site A e B.

Primeira, remota área de trabalho para Site A e B de sites através do portal de clássico Azure. Alternar o banco de dados para o modo de archivelog usando o SQL * Plus janela de comando:

    sql>shutdown immediate
    sql>startup mount
    sql>alter database archivelog;
    sql>alter database open;


Em seguida, habilite o mínimo [complementar log](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) da seguinte maneira:

    SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

Em seguida, prepare o banco de dados para dar suporte a replicação DDL (linguagem de definição de dados):

    SQL> alter system set recyclebin=off scope=spfile;

Em seguida, desligar e reiniciar o banco de dados:

    sql>shutdown immediate
    sql>startup


##<a name="3-create-all-necessary-objects-to-support-ddl-replication"></a>3. criar todos os objetos necessários para oferecer suporte a replicação DDL
Esta seção lista os scripts que você precisa usar para criar todos os objetos necessários para oferecer suporte a replicação DDL. Você precisa executar os scripts especificados nesta seção no Site A e B.

Abra um prompt de comando do Windows e navegue até a pasta GoldenGate Oracle, como c:\\OracleGG. Inicie o SQL\*Plus prompt de comando com privilégios de administrador do banco de dados, como usando **SYSDBA** no Site A e B.

Em seguida, execute os seguintes scripts:

    SQL> @marker_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @ddl_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @role_setup.sql
    Enter GoldenGate schema name: ggate
    SQL> grant ggs_ggsuser_role to ggate;
     Grant succeeded.
     SQL> @ddl_enable
     Trigger altered.
     SQL> @ddl_pin ggate


Ferramenta de GoldenGate Oracle requer um logon de nível de tabela para o suporte DDL (linguagem de definição de dados). É por isso que, habilitar complementar de registro em log no nível da tabela usando o comando Adicionar TRANDATA. Abrir janela de intérprete Oracle GoldenGate comando, fazer login no banco de dados e execute o comando Adicionar TRANDATA:

    GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

    GGSCI(Hostname) 6> add trandata scott.inventory

##<a name="4-configure-goldengate-manager-on-site-a-and-site-b"></a>4. configurar o Gerenciador de GoldenGate no Site A e B
O Gerenciador de GoldenGate Oracle executa um número de funções como iniciando os outros GoldenGate processos, gerenciamento de arquivos de log de trilha e relatórios.

Você precisará configurar o processo do Gerenciador de GoldenGate Oracle no Site A e B. Para fazer isso, execute as seguintes etapas no Site A e B.

Janelas abertas janela de comando e iniciam o intérprete de comandos do Oracle GoldenGate:

    cd C:\OracleGG\
    c:\OracleGG>ggsci
    Oracle GoldenGate Command Interpreter for Oracle
    Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
    Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
    Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


Registra a sessão GGSCI em um banco de dados para que você possa executar comandos que afetam o banco de dados:

    GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

Exibir o status e o tempo de retardo (onde relevante) para todos os processos de gerente, extrair e Replicat em um sistema:

    GGSCI (HostName) 2> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Abra o arquivo de parâmetro usando o comando Editar parâmetros e em seguida, anexe as seguintes informações:

    GGSCI (HostName) 3> edit params mgr
    PORT 7809
    USERID ggate, PASSWORD ggate
    PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

Exibir o status e o tempo de retardo (onde relevante) para todos os processos de gerente, extrair e Replicat em um sistema:

    GGSCI (HostName) 46> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Registra a sessão GGSCI em um banco de dados para que você possa executar comandos que afetam o banco de dados:

    GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

    Successfully logged into database.

Inicie o processo de gerente:

    GGSCI (HostName) 48> start manager
    Manager started.

##<a name="5-create-extract-group-and-data-pump-processes-on-site-a-and-site-b"></a>5. Crie extrair processos de grupo e Data Pump no Site A e B de Site

###<a name="create-extract-and-data-pump-processes-on-site-a"></a>Criar processos de extrair e Data Pump em um Site

Você precisa criar os processos de extrair e Data Pump no Site A e o Site B. área de trabalho remota Site A e B de sites através do portal de clássico Azure. Abrir a janela de intérprete de comando do GGSCI. Execute os seguintes comandos no Site r

    GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
    EXTRACT added.
    GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
    EXTTRAIL added.
    GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
    EXTRACT added.
    GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
    RMTTRAIL added.

Abra o arquivo de parâmetro usando o comando Editar parâmetros e em seguida, anexe as seguintes informações: GGSCI (MachineGG1) 18 > Editar parâmetros ext1 EXTRAIR ext1 ID de usuário ggate, senha ggate EXTTRAIL C:\OracleGG\dirdat\aa TRANLOGOPTIONS EXCLUDEUSER ggate tabela scott.inventory, GETBEFORECOLS (Diante atualização KEYINCLUDING (prod_category, qty_in_stock, last_dml), Diante excluir KEYINCLUDING (prod_category, qty_in_stock, last_dml));

Abra o arquivo de parâmetro usando o comando Editar parâmetros e em seguida, anexe as seguintes informações:

    GGSCI (MachineGG1) 15> edit params dpump1
    EXTRACT dpump1
     USERID ggate, PASSWORD ggate
     RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
     RMTTRAIL C:\OracleGG\dirdat\ab
     PASSTHRU
     TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-b"></a>Criar uma tabela de ponto de verificação de GoldenGate no Site B

Em seguida, você precisa adicionar uma tabela de ponto de verificação no local B. Para fazer isso, você precisa abrir uma janela de intérprete do comando GoldenGate e executar:

    C:\OracleGG\ggsci
    GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

E, em seguida, adicione a tabela de ponto de verificação no banco de dados, onde ggate é o proprietário:

    GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
    Successfully created checkpoint table ggate.checkpointtable.

Adicione o nome da tabela de ponto de verificação para o arquivo globais no servidor de destino, o que é o Site B nesta etapa. Editar o arquivo globais no local b:

    GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

Em seguida, acrescente o parâmetro CHECKPOINTTABLE ao arquivo globais existente:

    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Como uma etapa final, salve e feche o arquivo de parâmetros globais.


###<a name="add-replicat-on-site-b"></a>Adicionar REPLICAT no Site B
Esta seção descreve como adicionar um processo REPLICAT "REP2" no local B.

Use Adicionar REPLICAT comando para criar um grupo de Replicat em b Site:

    GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

Abra o arquivo de parâmetro usando o comando Editar parâmetros e em seguida, anexe as seguintes informações:

    GGSCI (MachineGG2) 10> edit params rep2
    REPLICAT rep2
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

###<a name="create-extract-and-data-pump-processes-on-site-b"></a>Criar processos de extrair e Data Pump no Site B

Esta seção descreve como criar um novo processo de extrair "EXT2" e um novo processo de bomba de dados "DPUMP2" no local b:

    GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
     EXTRACT added.
    GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
     EXTTRAIL added.
    GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
     EXTRACT added.
    GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
     RMTTRAIL added.

Abra o arquivo de parâmetro usando o comando Editar parâmetros e em seguida, anexe as seguintes informações:

    GGSCI (MachineGG2) 31> edit params ext2
    EXTRACT ext2
    USERID ggate, PASSWORD ggate
    EXTTRAIL C:\OracleGG\dirdat\ac
    TRANLOGOPTIONS EXCLUDEUSER ggate
    TABLE scott.inventory,
    GETBEFORECOLS (
    ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
    ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

Abra o arquivo de parâmetro usando o comando Editar parâmetros e em seguida, anexe as seguintes informações:

    GGSCI (MachineGG2) 32> edit params dpump2
    EXTRACT dpump2
    USERID ggate, PASSWORD ggate
    RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
    RMTTRAIL C:\OracleGG\dirdat\ad
    PASSTHRU
    TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-a"></a>Criar uma tabela de ponto de verificação de GoldenGate em um Site

Abrir janela de intérprete do Oracle GoldenGate comando e criar uma tabela de ponto de verificação:

    GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

    GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

    Successfully created checkpoint table ggate.checkpointtable.

Você também precisa adicionar o nome da tabela de ponto de verificação para o arquivo globais no Site A.

Abrir janela de intérprete de comando do Oracle GoldenGate e edite o arquivo globais no Site r

    GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
    Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Salve e feche o arquivo de parâmetros globais.

###<a name="add-replicat-on-site-a"></a>Adicionar REPLICAT em um Site

Esta seção descreve como adicionar um processo REPLICAT "REP1" no Site A.

O comando a seguir cria um rep1 de grupo Replicat com o nome de uma trilha e o checkpointtable associado.

    GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
     REPLICAT added.

Abra o arquivo de parâmetro usando o comando Editar parâmetros e em seguida, anexe as seguintes informações:

    GGSCI (MachineGG1) 10> edit params rep1
    REPLICAT rep1
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

### <a name="add-trandata-on-site-a-and-site-b"></a>Adicionar trandata no Site A e B de Site

Habilite o log de complementar no nível da tabela usando o comando Adicionar TRANDATA. Abrir janela de intérprete Oracle GoldenGate comando, fazer login no banco de dados e execute o comando Adicionar TRANDATA.

Área de trabalho remota MachineGG1, abra o intérprete de comandos do Oracle GoldenGate e executar:

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Área de trabalho remota MachineGG2, abra o intérprete de comandos do Oracle GoldenGate e executar:

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

Exibir informações sobre o estado de nível de tabela complementar log:

    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="add-trandata-on-site-a-and-site-b"></a>Adicionar trandata no Site A e B de Site

Habilite o log de complementar no nível da tabela usando o comando Adicionar TRANDATA. Abrir janela de intérprete Oracle GoldenGate comando, fazer login no banco de dados e execute o comando Adicionar TRANDATA.

Área de trabalho remota MachineGG1, abra o intérprete de comandos do Oracle GoldenGate e executar:

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Área de trabalho remota MachineGG2, abra o intérprete de comandos do Oracle GoldenGate e executar:

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
    Display information about the state of table-level supplemental logging:
    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="start-extract-and-data-pump-processes-on-site-a"></a>Iniciar processos extrair e Data Pump em um Site

Iniciar o ext1 de processo extrair em r de Site

    GGSCI (MachineGG1) 31> start extract ext1
    Sending START request to MANAGER …
    EXTRACT EXT1 starting

Iniciar o dpump1 de processo de bomba de dados no Site r

    GGSCI (MachineGG1) 23> start extract dpump1
    Sending START request to MANAGER …
    EXTRACT DPUMP1 starting
Exibir informações sobre o ext1 de grupo extrair: GGSCI (MachineGG1) 32 > info extrair ext1 EXTRAIR EXT1 última iniciado 25-11-2013 08:03 Status executando o ponto de verificação retardo 00:00:00 (atualizado 00:00:02 atrás) Log leitura ponto de verificação Oracle Refazer 25-11-Logs 2013 08:03:18 Seqno 6, RBA 3230720 SCN 0.1074371 (1074371)

Exibir o status e o tempo de retardo (onde relevante) para todos os processos de gerente, extrair e Replicat em um sistema:

    GGSCI (MachineGG1) 16> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
    EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

###<a name="start-extract-and-data-pump-processes-on-site-b"></a>Iniciar processos extrair e Data Pump no Site B

Iniciar o ext2 de processo extrair em b Site:

    GGSCI (MachineGG2) 22> start extract ext2
    Sending START request to MANAGER …
    EXTRACT EXT2 starting

Iniciar o dpump2 de processo de bomba de dados no local b:

    GGSCI (MachineGG2) 23> start extract dpump2
    Sending START request to MANAGER …
    EXTRACT DPUMP2 starting

Exibir o status e o tempo de retardo (onde relevante) para todos os processos de gerente, extrair e Replicat em um sistema:

    GGSCI (ActiveGG2orcldb) 6> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
    EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### <a name="start-replicat-process-on-site-a"></a>Iniciar processo de REPLICAT no Site A

Esta seção descreve como iniciar o processo REPLICAT "REP1" no Site A.

Iniciar o processo de Replicat no Site r

    GGSCI (MachineGG1) 38> start replicat rep1
    Sending START request to MANAGER …
    REPLICAT REP1 starting

Exiba o status de um grupo de Replicat:

    GGSCI (MachineGG1) 39> status replicat rep1
     REPLICAT REP1: RUNNING

###<a name="start-replicat-process-on-site-b"></a>Iniciar processo de REPLICAT no Site B

Esta seção descreve como iniciar o processo REPLICAT "REP2" no local B.

Iniciar o processo de Replicat no local b:

    GGSCI (MachineGG2) 26> start replicat rep2
    Sending START request to MANAGER …
    REPLICAT REP2 starting

Exiba o status de um grupo de Replicat:

    GGSCI (MachineGG2) 27> status replicat rep2
    REPLICAT REP2: RUNNING

##<a name="6-verify-the-bi-directional-replication-process"></a>6. Verifique se o processo de replicação bidirecional

Para verificar a configuração do Oracle GoldenGate, inserir uma linha para o banco de dados no Site a área de trabalho remota para abrir Site r. SQL * Plus janela de comando e executar: SQL > Selecionar nome do banco de dados de $ v;

    NAME
    ———
    TESTGG

    SQL> insert into inventory values  (100,’TV’,100,sysdate);

    1 row created.

    SQL> commit;

    Commit complete.

Em seguida, verifique se essa linha é replicada no local B. Para fazer isso, a área de trabalho remota para Site B. abrir cima janela SQL Plus e execute:

    SQL> select name from v$database;

    NAME
    ———
    TESTGG

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13

Inserir um novo registro no local b:

    SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
    1 row created.

    SQL> commit;

    Commit complete.

Área de trabalho remota Site A e verifique se a replicação ocorreu:

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13
    101 DVD 10 22-MAR-13

##<a name="additional-resources"></a>Recursos adicionais
[Imagens de máquinas virtuais do Oracle do Azure](virtual-machines-linux-classic-oracle-images.md)
