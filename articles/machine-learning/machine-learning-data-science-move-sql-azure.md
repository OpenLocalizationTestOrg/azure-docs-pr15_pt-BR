<properties 
    pageTitle="Mover dados para um banco de dados do SQL Azure para aprendizado de máquina do Azure | Azure" 
    description="Criar tabela SQL e carregar dados a tabela SQL" 
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

# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Mover dados para um banco de dados do SQL Azure para aprendizado de máquina do Azure

Este tópico descreve as opções para mover dados de arquivos simples (formatos CSV ou TSV) ou de dados armazenados em um local SQL Server para um banco de dados do SQL Azure. Essas tarefas para mover dados para a nuvem são parte do processo de ciência de dados de equipe.

Para um tópico que descreve as opções para mover dados para um SQL Server no local para aprendizado de máquina, consulte [mover dados para o SQL Server em uma máquina virtual Azure](machine-learning-data-science-move-sql-server-virtual-machine.md).

Os links a seguir **menu** para os tópicos que descrevem como a inclusão de dados em ambientes de destino onde os dados podem ser armazenados e processados durante o processo de ciência do equipe dados (TDSP).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

A tabela a seguir resume as opções para mover dados para um banco de dados do SQL Azure.

<b>FONTE</b> |<b>DESTINO: Banco de dados do SQL Azure</b> |
-------------- |--------------------------------|
<b>Arquivo simples (CSV ou TSV formatado)</b> |<a href="#bulk-insert-sql-query">Consulta SQL de inserção em massa |
<b>SQL Server no local</b> | 1. <a href="#export-flat-file">exportar para arquivo simples<br> 2. <a href="#insert-tables-bcp">Assistente de migração de banco de dados SQL<br> 3. <a href="#db-migration">o banco de dados back backup e restauração<br> 4. <a href="#adf">fábrica de dados do azure |


## <a name="prereqs"></a>Pré-requisitos
Os procedimentos descritos aqui exigem que você tenha:

* Uma **assinatura do Azure**. Se você não tiver uma assinatura, você pode inscrever-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento do Azure**. Você usa uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account) . Após ter criado a conta de armazenamento, você precisa obter a chave de conta usada para acessar o armazenamento. Consulte [Exibir, copiar e as teclas de acesso do armazenamento gerar](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Acesso a um **banco de dados do SQL Azure**. Se você deve configurar um banco de dados do SQL Azure, [Introdução ao Microsoft Azure SQL Database](../sql-database/sql-database-get-started.md) fornece informações sobre como configurar uma nova instância de um banco de dados do SQL Azure.
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

**Dados**: os processos de migração são demonstrou usando o [conjunto de dados NYC táxi](http://chriswhong.com/open-data/foil_nyc_taxi/). O dataset NYC táxi contém informações sobre os dados de viagem e feiras e esteja disponível, conforme observado essa postagem, armazenamento de blob do Microsoft Azure: [NYC táxi dados](http://www.andresmh.com/nyctaxitrips/). Um exemplo e uma descrição desses arquivos são fornecidas em [NYC táxi viagens Dataset descrição](machine-learning-data-science-process-sql-walkthrough.md#dataset).
 
Você pode adaptar os procedimentos descritos aqui para um conjunto de seus próprios dados ou siga as etapas conforme descrito usando o dataset NYC táxi. Para carregar o dataset NYC táxi em seu banco de dados do SQL Server no local, siga o procedimento descrito em [Massa importar dados para o banco de dados do SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Estas instruções são para um SQL Server em uma máquina Virtual do Azure, mas o procedimento para carregar o SQL Server no local é a mesma.


## <a name="file-to-azure-sql-database"></a>Movimentação de dados de uma fonte de arquivo simples para um banco de dados do SQL Azure

Dados de arquivos simples (CSV ou TSV formatado) podem ser movidos para um banco de dados do SQL Azure usando uma consulta de SQL inserir em massa.

### <a name="bulk-insert-sql-query"></a>Consulta SQL de inserção em massa

As etapas do procedimento usando a consulta de SQL inserir em massa são semelhantes aos perfis abordados nas seções para mover dados de uma fonte de arquivo simples para o SQL Server em uma máquina virtual do Azure. Para obter detalhes, consulte [Consulta de SQL inserir em massa](machine-learning-data-science-move-sql-server-virtual-machine.md#insert-tables-bulkquery).


##<a name="sql-on-prem-to-sazure-sql-database"></a>Movimentação de dados do SQL Server do local para um banco de dados do SQL Azure

Se os dados de origem são armazenados em um SQL Server no local, há diversas possibilidades para mover os dados para um banco de dados do SQL Azure:

1. [Exportar para arquivo simples](#export-flat-file) 
2. [Assistente de migração de banco de dados SQL](#insert-tables-bcp)
3. [Banco de dados back backup e restauração](#db-migration)
4. [Fábrica de dados do Azure](#adf)

As etapas para as três primeiras são muito semelhantes às seções em [mover dados para o SQL Server em uma máquina virtual Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) que abordam esses mesmos procedimentos. Links para as seções apropriadas nesse tópico são fornecidas nas instruções a seguir.

###<a name="export-flat-file"></a>Exportar para arquivo simples

As etapas para este exportar para um arquivo simples são semelhantes aos perfis coberta de [Exportar para o arquivo simples](machine-learning-data-science-move-sql-server-virtual-machine.md#export-flat-file).

###<a name="insert-tables-bcp"></a>Assistente de migração de banco de dados SQL

As etapas para usar o Assistente de migração de banco de dados SQL são semelhantes aos perfis cobertos no [Assistente de migração de banco de dados do SQL](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-migration).

###<a name="db-migration"></a>Banco de dados back backup e restauração

As etapas para usar o banco de dados de backup e restauração são semelhantes aos perfis coberta de [banco de dados de backup e restauração](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-backup).

###<a name="adf"></a>Fábrica de dados do Azure

O procedimento para mover dados para um banco de dados do SQL Azure com o Azure dados fábrica (AAD) é fornecido no tópico [mover dados do servidor SQL no local para o SQL Azure com fábrica de dados do Azure](machine-learning-data-science-move-sql-azure-adf.md). Este tópico mostra como mover dados de um banco de dados do SQL Server no local para um banco de dados do SQL Azure por meio de armazenamento de Blob do Azure usando AAD. 

Considere usar AAD quando dados precisam ser migrada continuamente em um cenário de híbrido que acessa recursos no local e nuvem e quando os dados é transação ou precisam ser modificada ou tem lógica de negócios adicionada a ele, quando está sendo migrado. AAD permite o agendamento e monitoramento de trabalhos usando scripts JSON simples que gerenciam a movimentação dos dados periodicamente. AAD também tem outros recursos como o suporte para operações complexas.




