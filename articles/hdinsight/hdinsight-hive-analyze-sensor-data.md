<properties
    pageTitle="Analisar dados de sensor usando seção e Hadoop | Microsoft Azure"
    description="Aprenda a analisar dados usando o Console de consulta seção com HDInsight (Hadoop) e, em seguida, visualize os dados no Microsoft Excel com PowerView."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016" 
    ms.author="larryfr"/>

#<a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Analisar dados de sensor usando o Console de consulta seção em Hadoop em HDInsight

Aprenda a analisar dados usando o Console de consulta seção com HDInsight (Hadoop) e, em seguida, visualize os dados no Microsoft Excel usando o Power View.

> [AZURE.NOTE] As etapas neste documento só funcionam com clusters HDInsight baseados no Windows.

Neste exemplo, você usará seção para processar dados históricos produzidos por aquecimento, ventilação e sistemas de ar condicionado (HVAC) para identificar os sistemas que não são capazes de confiavelmente manter uma temperatura de conjunto. Você aprenderá como:

- Crie tabelas de seção para consultar dados armazenados em arquivos de valor (CSV) separados por vírgulas.
- Crie consultas de seção para analisar os dados.
- Usar o Microsoft Excel para se conectar ao HDInsight (usando o banco de dados ODBC (conectividade) para recuperar os dados analisados.
- Use o Power View para visualizar os dados.

![Um diagrama da arquitetura de solução](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

##<a name="prerequisites"></a>Pré-requisitos

* Um cluster de HDInsight (Hadoop): consulte [clusters provisionar Hadoop no HDInsight](hdinsight-provision-clusters.md) para obter informações sobre a criação de um cluster.

* Microsoft Excel 2013

    > [AZURE.NOTE] Microsoft Excel é usado para visualização de dados com o [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Driver ODBC Microsoft seção](http://www.microsoft.com/download/details.aspx?id=40886)

##<a name="to-run-the-sample"></a>Para executar a amostra

1. Em seu navegador da web, navegue até a URL a seguir. Substituir `<clustername>` com o nome do seu cluster HDInsight.

        https://<clustername>.azurehdinsight.net

    Quando solicitado, autentica usando o nome de usuário de administrador e a senha que você usou quando este cluster de provisionamento.

2. Da página da web que é aberta, clique na guia **Obtendo iniciado Galeria** e sob a categoria de **soluções com dados de exemplo** , clique na amostra de **Análise de dados de Sensor** .

    ![Obtendo introdução de galeria](./media/hdinsight-hive-analyze-sensor-data/getting-started-gallery.png)

3. Siga as instruções fornecidas na página da web para concluir a amostra.
