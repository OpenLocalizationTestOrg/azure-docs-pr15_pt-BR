<properties
   pageTitle="Visualizar dados de depósito de dados do SQL com o Power BI Microsoft Azure"
   description="Visualizar dados de depósito de dados do SQL com o Power BI"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="" />

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="lodipalm;barbkess;sonyama" />

# <a name="visualize-data-with-power-bi"></a>Visualizar dados com o Power BI

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Aprendizado de máquina Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [O Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Este tutorial mostra como usar o Power BI para se conectar ao SQL Data Warehouse e criar algumas visualizações básicas.

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## <a name="prerequisites"></a>Pré-requisitos

Para percorrer neste tutorial, você precisa:

- Um depósito de dados do SQL pré-carregado com o banco de dados AdventureWorksDW. Para provisionar isso, consulte [criar um depósito de dados do SQL][] e escolha carregar os dados de exemplo. Se você já tiver um depósito de dados mas não tem dados de exemplo, você pode [carregar dados de amostra manualmente][].


## <a name="1-connect-to-your-database"></a>1. se conectar ao seu banco de dados

Para abrir o Power BI e conectar ao seu banco de dados AdventureWorksDW:

1. Entrar no [portal do Azure][].
2. Clique em **bancos de dados SQL** e escolha o banco de dados AdventureWorks SQL Data Warehouse.

    ![Localizar seu banco de dados][1]

3. Clique no botão 'Abrir no Power BI'.

    ![Botão do Power BI][2]

4. Agora você deve ver a página de conexão de SQL Data Warehouse exibindo seu endereço da web de banco de dados. Clique em Avançar.

    ![Conexão do Power BI][3]

6. Insira seu nome de usuário do servidor SQL Azure e senha e você será totalmente conectado ao seu banco de dados do SQL Data Warehouse.

    ![Power BI entrar][4]

7. Depois que você tenha entrado no Power BI, clique em dataset AdventureWorksDW na lâmina à esquerda. Isso abrirá o banco de dados.

    ![AdventureWorksDW aberta do Power BI][5]



## <a name="2-create-a-report"></a>2. criar um relatório

Agora você está pronto para usar o Power BI para analisar seus dados de amostra AdventureWorksDW. Para executar a análise, AdventureWorksDW tem uma exibição chamada AggregateSales. Este modo de exibição contém algumas das principais métricas para analisar as vendas da empresa.

1. Para criar um mapa do valor das vendas de acordo com o CEP, no painel direito de campos, clique na exibição AggregateSales para expandi-la. Clique nas colunas CEP e SalesAmount para selecioná-los.

    ![Power BI selecione AggregateSales][6]

    Power BI reconhece automaticamente esta é dados geográficos e colocá-lo em um mapa para você.

    ![Mapa do Power BI][7]

2. Esta etapa cria um gráfico de barras que mostra a quantidade de vendas por receita de cliente. Para criar este ir para o modo de exibição de AggregateSales expandido. Clique no campo SalesAmount. Arraste o campo receita do cliente para a esquerda e solte-o para o eixo.

    ![Eixo select do Power BI][8]

    Podemos movido o gráfico de barras sobre à esquerda.

    ![Power BI barra][9]

3. Esta etapa cria um gráfico de linhas que mostra a quantidade de vendas por data do pedido. Para criar este ir para o modo de exibição de AggregateSales expandido. Clique em SalesAmount e DataDoPedido. Em visualizações coluna clique no ícone de gráfico de linha; Este é o primeiro ícone na segunda linha em visualizações.

    ![Gráfico de selecionar linha do Power BI][10]

    Agora você tem um relatório que mostra os três diferentes visualizações de dados.

    ![Linha do Power BI][11]

Você pode salvar seu progresso a qualquer momento clicando em **arquivo** e selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas
Agora que damos algum tempo para aquecimento com os dados de exemplo, consulte como [desenvolver][], [carregar][]ou [migrar][]. Ou, dê uma olhada o [site Power BI][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[migrar]: sql-data-warehouse-overview-migrate.md
[desenvolver]: sql-data-warehouse-overview-develop.md
[carregar]: sql-data-warehouse-overview-load.md
[carregar dados de amostra manualmente]: sql-data-warehouse-load-sample-databases.md
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Criar um depósito de dados do SQL]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Portal do Azure]: https://portal.azure.com/
[Site do Power BI]: http://www.powerbi.com/
