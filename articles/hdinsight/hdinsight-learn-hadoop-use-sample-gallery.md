<properties
   pageTitle="Saiba Hadoop em HDInsight com a Galeria de amostra | Microsoft Azure"
   description="Aprenda rapidamente Hadoop executando aplicativos de amostra da Galeria HDInsight obtendo iniciado. Usar dados de exemplo ou fornecer o seu próprio."
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
   ms.date="10/21/2016"
   ms.author="jgao"/>

# <a name="learn-hadoop-by-using-the-azure-hdinsight-getting-started-gallery"></a>Saiba Hadoop usando a Galeria de Introdução do Azure HDInsight obtendo

Galeria de Introdução obtendo só está disponível para clusters HDInsight baseados no Windows. A Galeria fornece uma fácil e rápida maneira Saiba Hadoop executando aplicativos de exemplo no HDInsight. Alguns dos exemplos vêm com dados de exemplo. Você pode fornecer seus próprios dados para os exemplos restantes. Atualmente, há os seguintes exemplos de seis (com Aguarde mais):

- Soluções com seus dados do Azure
    - Análise de log de site do Microsoft Azure
    - Análise de armazenamento do Microsoft Azure
- Soluções com dados de exemplo
    - Análise de dados de sensor
    - Análise de tendência do Twitter
    - Análise de log de site
    - Recomendação de filme mahout

![Soluções de HDInsight Hadoop, tempestade e HBase obtendo iniciado Galeria incluindo dados de exemplo.][hdinsight.sample.gallery]

O vídeo a seguir mostra como executar a amostra de análise de tendência de Twitter:

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.YouTube.com/embed/7ePbHot1SN4></a></center>

Painel pode ser acessado, navegando até http://<YourHDInsightClusterName>.azurehdinsight.net/ ou a partir do portal do Azure.

**Para executar uma amostra da Galeria de Introdução obtendo**

1. Entre [portal do Azure][azure.portal].
2. Clique em **Procurar** , no menu à esquerda, clique em **Clusters de HDInsight**e, em seguida, clique em nome do seu cluster.
3. Clique em **Painel de controle** do menu superior.
4. Insira o nome de usuário e senha do usuário HTTP (também chamado de usuário cluster).
6. Clique em **Galeria de Introdução obtendo** na parte superior da página.
7. Clique em um dos exemplos. Cada amostra fornece etapas detalhadas para executá-la. A imagem a seguir mostra o exemplo de análise de tendência de Twitter:

    ![Exemplo de análise de tendência de HDInsight Twitter][hdinsight.twitter.sample]

## <a name="next-steps"></a>Próximas etapas
Outras maneiras para saber mais sobre HDInsight incluem:

- [Mapa de aprendizagem do HDInsight][hdinsight.learn.map]
- [HDInsight infographic][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]:https://portal.azure.com
