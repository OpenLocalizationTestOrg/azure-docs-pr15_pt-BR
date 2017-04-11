<properties 
    pageTitle="Mover dados para o SQL Server em uma máquina virtual Azure | Azure" 
    description="Mova dados de arquivos simples ou de um local SQL Server para SQL Server na máquina virtual do Azure." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016" 
    ms.author="bradsev" /> 

# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Mover dados para o SQL Server em uma máquina virtual Azure

Este tópico descreve as opções para mover dados de arquivos simples (formatos CSV ou TSV) ou de um SQL Server no local para o SQL Server em uma máquina virtual Azure. Essas tarefas para mover dados para a nuvem são parte do processo de ciência de dados de equipe.

Para um tópico que descreve as opções para mover dados para um banco de dados do SQL Azure para aprendizado de máquina, consulte [mover dados para um banco de dados do SQL Azure para aprendizado de máquina do Azure](machine-learning-data-science-move-sql-azure.md).

No **menu** abaixo links para tópicos que descrevem como a inclusão de dados em outros ambientes de destino onde os dados podem ser armazenados e processados durante o processo de ciência do equipe dados (TDSP).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


A tabela a seguir resume as opções para mover dados para o SQL Server em uma máquina virtual Azure.

<b>FONTE</b> |<b>DESTINO: SQL Server na máquina virtual Azure</b> |
------------------ |-------------------- |
<b>Arquivo simples</b> |1. <a href="#insert-tables-bcp">utilitário de cópia em massa de linha de comando (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">em massa inserir SQL consulta</a><br> 3. <a href="#sql-builtin-utilities">gráficos utilitários internos no SQL Server</a>
<b>No SQL Server local</b> | 1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">implantar um banco de dados SQL Server para um Assistente de máquina virtual do Microsoft Azure</a><br> 2. <a href="#export-flat-file">Exportar para um arquivo simples</a><br> 3. <a href="#sql-migration">Assistente de migração de banco de dados SQL</a> <br> 4. <a href="#sql-backup">o banco de dados back backup e restauração</a><br>

Observe que este documento pressupõe que comandos SQL são executados do SQL Server Management Studio ou Visual Studio Database Explorer.

> [AZURE.TIP] Como alternativa, você pode usar [Fábrica de dados do Azure](https://azure.microsoft.com/services/data-factory/) para criar e agendar um pipeline que mova os dados para um servidor de SQL máquina virtual no Azure. Para obter mais informações, consulte [copiar dados com o Azure Data Factory (atividade de cópia)](../data-factory/data-factory-data-movement-activities.md).


## <a name="prereqs"></a>Pré-requisitos
Este tutorial supõe que você possui:

* Uma **assinatura do Azure**. Se você não tiver uma assinatura, você pode inscrever-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento do Azure**. Você usará uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) . Após ter criado a conta de armazenamento, você precisará obter a chave de conta usada para acessar o armazenamento. Consulte [Exibir, copiar e as teclas de acesso do armazenamento gerar](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* **SQL Server em uma máquina virtual Azure**configurado. Para obter instruções, consulte [Configurar uma máquina virtual de Azure SQL Server como um servidor de bloco de anotações de IPython para análise avançada](machine-learning-data-science-setup-sql-server-virtual-machine.md).
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


## <a name="filesource_to_sqlonazurevm"></a>Movimentação de dados de uma fonte de arquivo simples para SQL Server em uma máquina virtual do Azure

Se seus dados estiverem em um arquivo simples (organizado em um formato de linha/coluna), ele pode ser movido para máquina virtual do SQL Server no Azure via os seguintes métodos:

1. [Utilitário de cópia em massa de linha de comando (BCP)](#insert-tables-bcp) 
2. [Consulta SQL de inserção em massa](#insert-tables-bulkquery)
3. [Gráficos utilitários internos no SQL Server (importação/exportação, SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>Utilitário de cópia em massa de linha de comando (BCP)

BCP é um utilitário de linha de comando instalado com o SQL Server e é uma das formas mais rápidas para mover dados. Ele funciona em todas as três variantes do SQL Server (no SQL Server local, SQL Azure e SQL Server VM no Azure). 

> [AZURE.NOTE]**Onde devem ser meus dados para BCP?**  
> Embora não seja necessário, ter arquivos contendo os dados de origem localizados na mesma máquina que o SQL server de destino permite transferências mais rápidas (rede velocidade vs disco local velocidade AE). Você pode mover os arquivos simples que contém os dados na máquina onde o SQL Server está instalado usando várias ferramentas como [AZCopy](../storage/storage-use-azcopy.md)de cópia dos arquivos, [Gerenciador de armazenamento do Azure](http://storageexplorer.com/) ou windows copiar/colar via protocolo RDP (Remote Desktop).

1. Certifique-se de que o banco de dados e as tabelas são criadas do banco de dados do SQL Server de destino. Aqui está um exemplo de como fazer que usando o `Create Database` e `Create Table` comandos:

        CREATE DATABASE <database_name>
    
        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        ) 
    
2. Gere o arquivo de formato que descreva o esquema para a tabela com o seguinte comando na linha de comando da máquina onde bcp está instalado.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n` 

3. Inserir os dados no banco de dados usando o comando bcp da seguinte maneira. Isso deve funcionar na linha de comando, considerando que o SQL Server está instalado na mesma máquina:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Otimizar BCP insere** Consulte o artigo a seguir ['Diretrizes para importação de massa otimizando'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) otimizar tais inserções.


### <a name="insert-tables-bulkquery-parallel"></a>Paralelizar inserções mais rápida para movimentação de dados

Se os dados que está movendo forem grandes, você pode acelerar coisas executando simultaneamente vários comandos BCP em paralelo em um Script do PowerShell.

> [AZURE.NOTE]**Dados de grande inclusão** Para otimizar o carregamento de conjuntos de dados grandes e muito grandes de dados, partição suas tabelas de banco de dados lógica e física usando várias tabelas de grupos de arquivos e partição. Para obter mais informações sobre como criar e carregar dados em tabelas de partição, consulte [Tabelas de partição de SQL de carga paralelos](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).


O script do PowerShell de exemplo abaixo demonstram paralelas inserções usando bcp:
    
    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
           bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

            #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
            #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
      }
    

    # Background processing of all partitions
    for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
    {
      Write-Debug "Submit loading partition # $i"
      Start-Job $ScriptBlock -Arg $i      
    }
    

    # Wait for it all to complete
    While (Get-Job -State "Running")
    {
      Start-Sleep 10
      Get-Job
    }
    
    # Getting the information back from the jobs
    Get-Job | Receive-Job
    Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>Consulta SQL de inserção em massa

[Consulta de SQL inserir em massa](https://msdn.microsoft.com/library/ms188365) pode ser usado para importar dados para o banco de dados de arquivos de linha/coluna com base (os tipos suportados são descritos[Preparar dados de massa exportar ou importar (SQL Server)](https://msdn.microsoft.com/library/ms188609)) tópico. 

Aqui estão alguns exemplos de comandos para inserir em massa são como a seguir:  

1. Analisar seus dados e defina quaisquer opções personalizadas antes de importar para certificar-se de que o banco de dados do SQL Server assume o mesmo formato para todos os campos como datas especiais. Aqui está um exemplo de como definir o formato de data como ano-mês-dia (se seus dados contém a data no formato de ano-mês-dia):

        SET DATEFORMAT ymd; 
    
2. Importe dados usando instruções de importação em massa:

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH 
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )
      

### <a name="sql-builtin-utilities"></a>Utilitários internos no SQL Server

Você pode usar integrações serviços SSIS (SQL Server) para importar dados para o SQL Server VM no Azure de um arquivo simples. SSIS está disponível nos dois ambientes studio. Para obter detalhes, consulte [ambientes de Studio e do Integration Services (SSIS)](https://technet.microsoft.com/library/ms140028.aspx):

- Para obter detalhes sobre as ferramentas de dados do SQL Server, consulte [Ferramentas de dados do Microsoft SQL Server](https://msdn.microsoft.com/data/tools.aspx)  
- Para obter detalhes sobre o Assistente de importação/exportação, consulte o [Assistente de exportação e importação do SQL Server](https://msdn.microsoft.com/library/ms141209.aspx)


## <a name="sqlonprem_to_sqlonazurevm"></a>Movimentação de dados do local SQL Server para SQL Server em uma máquina virtual Azure

Você também pode usar as estratégias de migração a seguir:

1. [Implantar um banco de dados do SQL Server em um Assistente de máquina virtual do Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportar para arquivo simples](#export-flat-file) 
3. [Assistente de migração de banco de dados SQL](#sql-migration)
4. [Banco de dados back backup e restauração](#sql-backup)

Podemos descrevem cada um desses abaixo:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Implantar um banco de dados do SQL Server em um Assistente de máquina virtual do Microsoft Azure

**Implantar um banco de dados SQL Server para um Assistente de máquina virtual do Microsoft Azure** é uma maneira simple e recomendada para mover dados de uma instância do SQL Server local para SQL Server em uma máquina virtual do Azure. Para obter etapas detalhadas, bem como uma discussão das outras alternativas, consulte [migrar um banco de dados SQL Server em uma máquina virtual do Azure](../virtual-machines/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exportar para arquivo simples

Vários métodos podem ser usados em massa exportar dados de um local On SQL Server como documentadas no tópico da [importação em massa e exportação de dados (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) . Este documento abordará o programa de cópia em massa (BCP) como um exemplo. Depois que os dados são exportados em um arquivo simples, ele pode ser importado para outro SQL server usando importação em massa. 

1. Exportar os dados do local SQL Server para um arquivo usando o utilitário bcp da seguinte maneira

    `bcp dbname..tablename out datafile.tsv -S  servername\sqlinstancename -T -t \t -t \n -c`

2. Criar o banco de dados e a tabela na máquina virtual do SQL Server no Azure usando o `create database` e `create table` para o esquema de tabela exportados na etapa 1.

3. Crie um arquivo de formato para descrever o esquema de tabela de dados está sendo exportado/importado. Detalhes do arquivo de formato são descritas em [criar um arquivo de formato (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formatar geração do arquivo quando executando BCP da máquina do SQL Server 

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

    Formatar geração do arquivo quando executando BCP remotamente em relação a um SQL Server 

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
        
    
4. Use qualquer um dos métodos descritos na seção [Movendo dados de origem de arquivo](#filesource_to_sqlonazurevm) para mover os dados em arquivos simples a um SQL Server.

### <a name="sql-migration"></a>Assistente de migração de banco de dados SQL

[Assistente de migração de banco de dados do SQL Server](http://sqlazuremw.codeplex.com/) fornece uma maneira amigável para mover dados entre duas instâncias do SQL server. Ele permite ao usuário mapear o esquema de dados entre fontes e tabelas de destino, escolha os tipos de coluna e várias outras funcionalidades. Ele usa a cópia em massa (BCP) nos bastidores. Uma captura de tela da tela de boas-vindas para o Assistente de migração de banco de dados do SQL é mostrada abaixo.  

![Assistente de migração do SQL Server][2]

### <a name="sql-backup"></a>Banco de dados back backup e restauração

Compatível com SQL Server: 

1. [Banco de dados back backup e restaurar a funcionalidade](https://msdn.microsoft.com/library/ms187048.aspx) (ambos para um arquivo ou local bacpac exportam para blob) e [Aplicativos de camadas de dados](https://msdn.microsoft.com/library/ee210546.aspx) (usando bacpac). 
2. Capacidade de criar VMs do SQL Server diretamente no Azure com um banco de dados copiado ou copiar um banco de dados existente do SQL Azure. Para obter mais detalhes, consulte [usar o Assistente de banco de dados de cópia](https://msdn.microsoft.com/library/ms188664.aspx). 

Uma captura de tela da parte traseira do banco de dados para cima/restauração opções do SQL Server Management Studio é mostrado abaixo.

![Ferramenta de importação do SQL Server][1]

## <a name="resources"></a>Recursos

[Migrar um banco de dados para o SQL Server em uma máquina virtual Azure](../virtual-machines/virtual-machines-windows-migrate-sql.md)

[SQL Server em Visão geral de máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png
