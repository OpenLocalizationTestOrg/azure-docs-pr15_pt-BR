<properties
   pageTitle="Criação de soluções integradas com SQL Data Warehouse | Microsoft Azure"
   description="Ferramentas e parceiros com soluções que se integram ao depósito de dados do SQL. "
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
   ms.date="05/31/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#<a name="leverage-other-services-with-sql-data-warehouse"></a>Aproveitar outros serviços com o SQL Data Warehouse
Além de funcionalidade central, SQL Data Warehouse permite aos usuários aproveitar muitos dos outros serviços no Azure junto com ele.  Especificamente, podemos atualmente entraram em etapas profundamente integrar com o seguinte:

+ Power BI
+ Fábrica de dados do Azure
+ Aprendizado de máquina Azure
+ Análise de fluxo Azure

Estamos trabalhando para se conectar com mais serviços em ecossistema do Azure.

##<a name="power-bi"></a>Power BI
Integração do Power BI permite que você aproveite os recursos de computação do SQL Data Warehouse com o relatório dinâmico e visualização do Power BI. Integração do Power BI atualmente inclui:

+ **Conexão direta**: uma mais avançadas de conexão com empilhamento lógico contra SQL Data Warehouse.  Isso fornece análise mais rápido em uma escala maior.
+ **Abrir no Power BI**: O botão 'Abrir no Power BI' passa informações de instância Power BI, permitindo uma conexão perfeita.

Consulte a [documentação do Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx) para obter mais informações ou [integrar com o Power BI](./sql-data-warehouse-integrate-power-bi.md) .

##<a name="azure-data-factory"></a>Fábrica de dados do Azure
Azure Data Factory fornece aos usuários uma plataforma gerenciada para criar canais de carga de extrair complexas.  Integração do SQL Data Warehouse com o Azure Data Factory inclui o seguinte:

+ **Procedimentos armazenados**: coordenar a execução de procedimentos armazenados no SQL Data Warehouse.
+ **Copiar**: uso AAD para mover dados para o SQL Data Warehouse.  Essa operação pode usar o mecanismo de movimentação de dados padrão do AAD ou PolyBase nos bastidores. 

Consulte [integrar com fábrica de dados do Azure](./sql-data-warehouse-integrate-azure-data-factory.md) ou a [documentação de fábrica de dados do Azure](https://azure.microsoft.com/documentation/services/data-factory/) para obter mais informações.

##<a name="azure-machine-learning"></a>Aprendizado de máquina Azure
Azure aprendizado de máquina é um serviço de análise totalmente gerenciado que permite aos usuários criar modelos complexos aproveitando um grande conjunto de ferramentas de previsão.  SQL Data Warehouse é suportado como uma fonte e de destino para esses modelos com a seguinte funcionalidade:

+ **Ler dados:** Unidade modelos em escala usando T-SQL contra SQL Data Warehouse.
+ **Gravar dados:** Confirme as alterações de qualquer modelo de volta para o SQL Data Warehouse.

Consulte a [documentação de aprendizado de máquina do Azure](https://azure.microsoft.com/services/machine-learning/) para obter mais informações ou [integrar com aprendizado de máquina do Azure](./sql-data-warehouse-integrate-azure-machine-learning.md) .

##<a name="azure-stream-analytics"></a>Análise de fluxo Azure
Azure Analytics fluxo é uma infraestrutura complexa e totalmente gerenciada para processar e consumindo dados de eventos gerados do Hub de evento do Azure.  Integração com o SQL Data Warehouse permite para fluxo de dados para efetivamente sejam processados e armazenados junto com dados relacionais, permitindo análise mais profunda, mais avançado.  

+ **Trabalho saída:** Envie a saída de trabalhos de análise de fluxo diretamente para o SQL Data Warehouse.

Consulte [integrar com a análise de fluxo Azure](./sql-data-warehouse-integrate-azure-stream-analytics.md) ou a [documentação de análise de fluxo de Azure](https://azure.microsoft.com/documentation/services/stream-analytics/) para obter mais informações.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-partner-business-intelligence.md

<!--MSDN references-->

<!--Other Web references-->
