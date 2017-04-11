<properties 
    pageTitle="Usar seção com Hadoop para análise de log de site | Microsoft Azure" 
    description="Aprenda a usar seção com HDInsight para analisar os logs de site. Você vai usar um arquivo de log como entrada para uma tabela do HDInsight e usar HiveQL para os dados da consulta." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/17/2016" 
    ms.author="nitinme"/>

# <a name="use-hive-with-hdinsight-to-analyze-logs-from-websites"></a>Use a seção com HDInsight para analisar logs de sites

Saiba como usar HiveQL com HDInsight para analisar logs de um site. Análise de log de site pode ser usado para sua audiência com base nas atividades semelhantes de segmento, categorizar os visitantes do site por demografia e descobrir o conteúdo que eles exibição, eles são provenientes de sites e assim por diante.

Neste exemplo, você vai usar um cluster de HDInsight para analisar arquivos de log do site para obter percepção a frequência de visitas ao site de sites externos em um dia. Você também será gerar um resumo dos erros de site que os usuários experiência. Você aprenderá como:

- Conecte a um armazenamento de Blob do Azure, que contém os arquivos de log do site.
- Crie tabelas de seção para consultar esses logs.
- Crie consultas de seção para analisar os dados.
- Usar o Microsoft Excel para se conectar ao HDInsight (usando conectividade aberta de banco de dados (ODBC) para recuperar os dados analisados.

![HDI. Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##<a name="prerequisites"></a>Pré-requisitos

- Você deve ter provisionado um cluster de Hadoop em Azurehdinsight. Para obter instruções, consulte [Provisionar HDInsight Clusters][hdinsight-provision]. 
- Você deve ter o Microsoft Excel 2013 ou Excel 2010 instalado.
- Você deve ter o [Driver ODBC seção da Microsoft](http://www.microsoft.com/download/details.aspx?id=40886) para importar dados de seção no Excel.


##<a name="to-run-the-sample"></a>Para executar a amostra

1. No [Portal do Azure](https://portal.azure.com/), do Startboard (se você fixos cluster lá), clique no bloco de cluster no qual você deseja executar o exemplo.

2. Da lâmina do cluster, em **Links rápidos**, clique em **Painel de Cluster**e da lâmina do **Painel de controle de Cluster** , clique em **Painel de controle de Cluster HDInsight**. Como alternativa, você pode abrir o painel diretamente, usando a seguinte URL:

        https://<clustername>.azurehdinsight.net
    
    Quando solicitado, autentica usando o nome de usuário de administrador e a senha que você usou ao provisionar o cluster.
  
2. Da página da web que é aberta, clique na guia **Obtendo iniciado Galeria** e sob a categoria de **soluções com dados de exemplo** , clique na amostra de **Análise de Log de site** .

3. Siga as instruções fornecidas na página da web para concluir a amostra.

##<a name="next-steps"></a>Próximas etapas
Tente o seguinte exemplo: [analisar dados de sensor usando seção com HDInsight](hdinsight-hive-analyze-sensor-data.md).


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
 
