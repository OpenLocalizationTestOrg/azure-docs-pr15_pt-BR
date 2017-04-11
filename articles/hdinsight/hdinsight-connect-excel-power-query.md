<properties
    pageTitle="Conectar o Excel ao Hadoop com o Power Query | Microsoft Azure"
    description="Saiba como aproveitar os componentes do business intelligence e usar o Power Query para Excel para acessar os dados armazenados em Hadoop em HDInsight."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>


#<a name="connect-excel-to-hadoop-by-using-power-query"></a>Conectar o Excel para Hadoop usando o Power Query

Um recurso chave da solução Microsoft grandes de dados é a integração dos componentes do Microsoft business intelligence (BI) com clusters Hadoop no Azure HDInsight. Exemplo primário dessa integração é a capacidade de conexão do Excel com a conta de armazenamento do Azure que contém os dados associados a seu cluster Hadoop usando o Microsoft Power Query para suplementos do Excel. Este artigo explica como configurar e usar o Power Query para consultar dados associados a um cluster de Hadoop gerenciado com HDInsight.

> [AZURE.NOTE] Enquanto as etapas neste artigo podem ser usadas com cluster um Linux ou HDInsight baseados no Windows, o Windows é exigido para a estação de trabalho do cliente.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **HDInsight um cluster**. Para configurar um, consulte [Introdução ao Azure HDInsight][hdinsight-get-started].
- **A estação de trabalho** que está executando o Windows 7, Windows Server 2008 R2 ou um sistema operacional posterior.
- **Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 autônomo ou Office 2010 Professional Plus**.


## <a name="install-power-query"></a>Instalar o Power Query

Power Query pode ser usado para importar dados de uma variedade de fontes no Microsoft Excel, onde ele pode power ferramentas de BI como o PowerPivot e Power View. Em particular, o Power Query pode importar dados que tenha sido saída ou que foi gerado por um trabalho Hadoop em execução em um cluster de HDInsight.

Baixar o Microsoft Power Query para Excel a partir do [Centro de Download Microsoft] [ powerquery-download] e instalá-lo.

## <a name="import-hdinsight-data-into-excel"></a>Importar dados de HDInsight no Excel

O suplemento do Power Query para Excel torna mais fácil importar dados de seu cluster HDInsight para o Excel, onde as ferramentas de BI como PowerPivot e Power Map podem ser usados para inspecionar, analisar e apresentam os dados.

**Importar dados de um cluster de HDInsight**

1. Abra o Excel.

2. Crie uma nova pasta de trabalho em branco.

3. Clique no menu de **Power Query** , clique em **Do Azure**e clique em **Do Microsoft Azure HDInsight**.

    ![HDI. PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

    **Observação:** Se você não vir o menu de **Power Query** , vá para **arquivo** > **Opções** > **Suplementos**e selecione **suplementos COM** da caixa suspensa **Gerenciar** na parte inferior da página. Selecione o botão **Ir …** e verifique se que a caixa para o Power Query para suplementos do Excel tiver sido verificada.

    **Observação:** Power Query também permite importar dados de HDFS, clicando em **De outras fontes**.

3. Para **Nome da conta**, insira o nome da conta de armazenamento de Blob do Azure associada ao seu cluster e clique em **Okey**. Isso pode ser a [conta de armazenamento padrão](hdinsight-administer-use-management-portal.md#find-the-default-storage-account) ou uma conta de armazenamento vinculado.  O formato é *https://<StorageAccountName>.blob.core.windows.net/*.

4. **Chave da conta**, insira a chave para a conta de armazenamento de Blob e, em seguida, clique em **Salvar**. (Você precisa fazer isso apenas na primeira vez que você acessa esse armazenamento.)

5. No painel **navegador** , à esquerda do Editor de consulta, clique duas vezes no nome de contêiner de armazenamento de Blob. Por padrão, o nome de contêiner é o mesmo nome que o nome do cluster.

6. Localize **HiveSampleData.txt** na coluna de **nome** (o caminho da pasta é **... / seção/depósito/hivesampletable/**) e clique em **binário** à esquerda da HiveSampleData.txt. HiveSampleData.txt vem com todo o cluster. Opcionalmente, você pode usar seu próprio arquivo.

    ![HDI. PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Se desejar, você pode renomear os nomes de coluna. Quando você estiver pronto, clique em **Fechar e carregar**.  Os dados foram carregados à pasta de trabalho:

    ![HDI. PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o Power Query para recuperar dados de HDInsight para o Excel. Da mesma forma, você pode recuperar dados de HDInsight em Azure SQL Database. Também é possível carregar dados em HDInsight. Para saber mais, consulte os seguintes artigos:

* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft seção][hdinsight-ODBC]
* [Carregar dados ao HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
