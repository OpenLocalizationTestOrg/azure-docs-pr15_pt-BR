<properties
   pageTitle="Analisar dados com aprendizado de máquina do Azure | Microsoft Azure"
   description="Use o aprendizado de máquina do Azure para criar uma modelo com base em dados armazenados no armazém de dados do SQL Azure de aprendizado de máquina previsão."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/14/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="analyze-data-with-azure-machine-learning"></a>Analisar dados com aprendizado de máquina do Azure

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Aprendizado de máquina Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [O Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Neste tutorial usa o aprendizado de máquina do Azure para criar uma modelo com base em dados armazenados no armazém de dados do SQL Azure de aprendizado de máquina previsão. Especificamente, isso cria uma campanha de marketing direcionada para Adventure Works, a loja de bicicletas, prever se um cliente é provável comprar uma bicicleta ou não.

> [AZURE.VIDEO integrating-azure-machine-learning-with-azure-sql-data-warehouse]


## <a name="prerequisites"></a>Pré-requisitos
Para percorrer neste tutorial, você precisa:

- Um depósito de dados do SQL pré-carregado com dados de exemplo AdventureWorksDW. Para provisionar isso, consulte [criar um depósito de dados do SQL][] e escolha carregar os dados de exemplo. Se você já tiver um depósito de dados mas não tem dados de exemplo, você pode [carregar dados de amostra manualmente][].

## <a name="1-get-data"></a>1. obter dados
Os dados estão no modo de exibição dbo.vTargetMail no banco de dados AdventureWorksDW. Para ler esses dados:

1. Entrar no [studio de aprendizado de máquina do Azure][] e clique em minhas experiências.
2. Clique em **+ novo** e selecione **Experimento em branco**.
3. Insira um nome para seu experimento: direcionadas Marketing.
4. Arraste o módulo do **leitor** do painel módulos na tela de desenho.
5. Especifique os detalhes do seu banco de dados do SQL Data Warehouse no painel Propriedades.
6. Especifique a **consulta** do banco de dados para ler os dados de interesse.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Execute o experimento clicando em **Executar** em tela experimento.
![Executar o experimento][1]


Após o experimento concluir executando com êxito, clique na porta de saída na parte inferior do módulo leitor e selecione **Visualizar** para ver os dados importados.
![Exibir dados importados][3]


## <a name="2-clean-the-data"></a>2. limpar os dados
Para limpar os dados, solte algumas colunas que não são relevantes para o modelo. Para fazer isso:

1. Arraste o módulo do **Projeto colunas** na tela de desenho.
2. Clique no **seletor de coluna de lançamento** no painel Propriedades para especificar quais colunas você deseja cancelar.
![Colunas de projeto][4]

3. Excluir dois colunas: CustomerAlternateKey e GeographyKey.
![Remover colunas desnecessárias][5]


## <a name="3-build-the-model"></a>3. criar o modelo
Podemos dividirá os dados 80-20: 80% de treinar uma modelo e 20% para testar o modelo de aprendizado de máquina. Faremos usar dos algoritmos "Dois-classe" para esse problema de classificação binária.

1. Arraste o módulo de **divisão** na tela de desenho.
2. Insira 0,8 fração das linhas no primeiro conjunto de dados de saída no painel Propriedades.
![Dividir dados em conjunto de teste e treinamento][6]
3. Arraste o módulo de **Classe dois aumentou árvore de decisão** na tela de desenho.
4. Arraste o módulo do **Modelo de trem** na tela de desenho e especifique as entradas. Clique em **Iniciar o seletor de coluna** no painel Propriedades.
      - Primeira entrada: algoritmo de ML.
      - O segundo de entrada: dados de treinar o algoritmo em.
![Conectar o módulo do modelo de trem][7]
5. Selecione a coluna **BikeBuyer** como a coluna prever.
![Selecionar coluna prever][8]


## <a name="4-score-the-model"></a>4. o modelo de pontuação
Agora, podemos testar como o modelo executa nos dados de teste. Podemos irá comparar o algoritmo de nossa escolha com um algoritmo de diferentes para ver qual executa melhor.

1. Arraste o módulo do **Modelo de pontuação** na tela de desenho.
    Primeira entrada: treinamento entrada segunda modelo: testar os dados ![pontuação o modelo][9]
2. Arraste a **Classe de dois Bayes ponto máquina** para a tela experimento. Podemos irá comparar como esse algoritmo executa em comparação com a árvore de decisão de classe dois aumentou.
3. Copie e cole os módulos modelos de trem e pontuação na tela.
4. Arraste o módulo do **Modelo avaliar** para a tela para comparar os dois algoritmos.
5. **Executar** o experimento.
![Executar o experimento][10]
6. Clique na porta de saída na parte inferior do módulo avaliar modelo e clique em Visualizar.
![Visualizar resultados de avaliação][11]

As avaliações fornecidas são a curva ROC, curva de diagrama e levante cancelamento de precisão. Observando essas métricas, podemos ver que o primeiro modelo executada melhor do que o outro. Para ver o que o primeiro modelo previsto, clique na porta de saída do modelo de pontuação e clique em Visualizar.
![Visualizar resultados de pontuação][12]

Você verá duas ou mais colunas adicionadas ao seu conjunto de dados de teste.

- Probabilidades marcadas: a probabilidade de que um cliente é um comprador de bicicleta.
- Rótulos marcados: classificação feita pelo modelo – comprador de bicicleta (1) ou não (0). Esse limite de probabilidade para rotular está definido como 50% e pode ser ajustada.

Comparando a coluna BikeBuyer (real) com os rótulos marcou (previsão), você pode ver como o modelo tiver realizado. Próximas etapas, você pode usar esse modelo para fazer previsões para novos clientes e publicar esse modelo como um serviço web ou gravar resultados de volta depósito de dados do SQL.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a criação de modelos de aprendizado de máquina de previsão, consulte [Introdução ao aprendizado de máquina no Azure][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure studio de aprendizado de máquina]:https://studio.azureml.net/
[Introdução à máquina aprendizagem no Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[carregar dados de amostra manualmente]: sql-data-warehouse-load-sample-databases.md
[Criar um depósito de dados do SQL]: sql-data-warehouse-get-started-provision.md
