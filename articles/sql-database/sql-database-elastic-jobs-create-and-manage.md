<properties
    pageTitle="Criar e gerenciar escaladas check-out de bancos de dados do SQL Azure usando trabalhos Elástico | Microsoft Azure"
    description="Percorra a criação e o gerenciamento de um trabalho de elástica banco de dados."
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/27/2016"
    ms.author="ddove"/>

# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Criar e gerenciar escaladas check-out de bancos de dados do SQL Azure usando trabalhos Elástico (prévia)

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)


**Trabalhos de banco de dados elástica** simplificar o gerenciamento de grupos de bancos de dados executando operações administrativas como alterações de esquema, gerenciamento de credenciais, atualizações de dados de referência, coleta de dados de desempenho ou conjunto de telemetria do locatário (cliente). Elástico trabalhos de banco de dados está disponível atualmente por meio do portal do Azure e cmdlets do PowerShell. No entanto, o Azure emerge das portal reduzido funcionalidade limitada a execução em todos os bancos de dados em um [banco de dados elástica pool (visualização)](sql-database-elastic-pool.md). Para acessar recursos adicionais e a execução de scripts em um grupo de bancos de dados incluindo um conjunto personalizadas ou um conjunto de fragmentar (criado com a [biblioteca de cliente do banco de dados elástica](sql-database-elastic-scale-introduction.md)), consulte [Criando e gerenciando trabalhos usando o PowerShell](sql-database-elastic-jobs-powershell.md). Para obter mais informações sobre trabalhos, consulte [Visão geral de trabalhos de elástica banco de dados](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Para uma avaliação gratuita, consulte [avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Um pool de elástica banco de dados. Consulte [sobre o elástica pools de banco de dados](sql-database-elastic-pool.md)
* Instalação de componentes de serviço de trabalho de banco de dados elástica. Consulte [Instalando o serviço de trabalho de banco de dados elástica](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Criando trabalhos

1. Usando o [portal do Azure](https://portal.azure.com), de um pool de trabalho elástica banco de dados existente, clique em **Criar trabalho**.
2. Digite o nome de usuário e a senha do administrador do banco de dados (criado na instalação de trabalhos) para o banco de dados de controle de trabalhos (armazenamento de metadados para trabalhos).

    ![Nomear o trabalho, digite ou cole código e clique em executar][1]
2. Na lâmina **Criar trabalho** , digite um nome para o trabalho.
3. Digite o nome de usuário e senha para se conectar a bancos de dados de destino com permissões suficientes para execução de scripts tenha êxito.
4. Cole ou digite no script T-SQL.
5. Clique em **Salvar** e, em seguida, clique em **Executar**.

    ![Criar trabalhos e executar][5]

## <a name="run-idempotent-jobs"></a>Executar trabalhos idempotente

Quando você executa um script em relação a um conjunto de bancos de dados, você deve ter certeza de que o script é idempotente. Isto é, o script deve ser capaz de executar várias vezes, mesmo se ele falha antes em um estado incompleto. Por exemplo, quando um script falha, o trabalho será automaticamente repetido até conseguir (dentro dos limites, como a repetir lógica eventualmente deixará o repetindo). A maneira de fazer isso é usar a uma cláusula "Se existe" e excluir qualquer instância encontrada antes de criar um novo objeto. Um exemplo é mostrado aqui:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Como alternativa, use uma cláusula "Se não existe" antes de criar uma nova instância:

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

Esse script atualiza a tabela criada anteriormente.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Verificar o status de trabalho

Depois que um trabalho começou, você pode verificar seu andamento.

1. Na página do pool elástica banco de dados, clique em **Gerenciar trabalhos**.

    ![Clique em "Gerenciar trabalhos"][2]

2. Clique no nome do (a) de um trabalho. O **STATUS** pode ser "Concluído" ou "Falha". Detalhes do trabalho aparecem (b) com sua data e hora da criação e execução. Na lista (c) abaixo que mostra o andamento do script em cada banco de dados no pool, dando a seus detalhes de data e hora.

    ![Verificando um trabalho concluído][3]


## <a name="checking-failed-jobs"></a>Verificação falha trabalhos

Se uma tarefa falhar, um log de sua execução pode ser encontradas. Clique no nome do trabalho falha para ver seus detalhes.

![Verificar um trabalho falhou][4]


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png

 
