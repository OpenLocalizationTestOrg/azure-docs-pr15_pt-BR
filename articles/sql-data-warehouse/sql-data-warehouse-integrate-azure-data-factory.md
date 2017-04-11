<properties
   pageTitle="Usar dados Azure fábrica com o SQL Data Warehouse | Microsoft Azure"
   description="Dicas para usar o Azure dados fábrica (AAD) com depósito de dados do SQL Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Usar dados Azure fábrica com depósito de dados do SQL

Azure Data Factory oferece um método totalmente gerenciado para organizar a transferência de dados e a execução de procedimentos armazenados no SQL Data Warehouse.  Isso permitirá que você mais facilmente configurados e agenda extrair transformação e carregamento (ETL) procedimentos complexos com SQL Data Warehouse. Para obter uma visão mais completa de fábrica de dados do Azure, consulte a [documentação de fábrica de dados do Azure][].

## <a name="data-movement"></a>Movimentação de dados

Azure fábrica de dados permite a movimentação de dados entre fontes locais e diferentes serviços do Azure.  Integração do atual, em geral com fábrica de dados do Azure oferece suporte para movimentação de dados para e a partir dos seguintes locais:

+ Armazenamento de blob do Microsoft Azure
+ Banco de dados do SQL Azure
+ No SQL Server local
+ SQL Server em IaaS

Para obter informações sobre como configurar um dados a atividade de cópia consulte [copiar dados com fábrica de dados do Azure][]

## <a name="stored-procedures"></a>Procedimentos armazenados
 Da mesma forma que pode ser usado para agendar a transferência de dados, fábrica de dados do Azure também podem ser usada para coordenar a execução de procedimentos armazenados.  Isso permite mais complexos canais seja criado e estende a capacidade da fábrica do Azure dados de computação proveito das SQL Data Warehouse.

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral de integração, consulte [Visão geral de integração do SQL Data Warehouse][].
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento de depósito de dados do SQL][].

<!--Image references-->

<!--Article references-->

[Copiar dados com fábrica de dados do Azure]: ../data-factory/data-factory-data-movement-activities.md
[Visão geral de desenvolvimento de depósito de dados do SQL]: ./sql-data-warehouse-overview-develop.md
[Visão geral de integração de depósito de dados do SQL]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Documentação do Azure fábrica de dados]:https://azure.microsoft.com/documentation/services/data-factory/

