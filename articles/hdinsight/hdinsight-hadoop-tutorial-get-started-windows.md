<properties
   pageTitle="Tutorial do Hadoop: Introdução ao Hadoop no Windows | Microsoft Azure"
   description="Introdução ao Hadoop no HDInsight. Aprenda a criar Hadoop clusters no Windows, executar uma consulta de seção em dados e analisar saída no Excel."
   keywords="tutorial do Hadoop, hadoop no windows, cluster hadoop, saiba hadoop, consulta de seção"
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/07/2016"
   ms.author="nitinme"/>


# <a name="hadoop-tutorial-get-started-using-hadoop-in-hdinsight-on-windows"></a>Tutorial do Hadoop: começar a usar o Hadoop no HDInsight no Windows

> [AZURE.SELECTOR]
- [Baseado em Linux](../hdinsight-hadoop-linux-tutorial-get-started.md)
- [Baseado no Windows](../hdinsight-hadoop-tutorial-get-started-windows.md)

Para ajudá-lo a aprender Hadoop no Windows e comece a usar o HDInsight, este tutorial mostra como executar uma consulta de seção em dados não estruturados em um cluster de Hadoop e, em seguida, analise os resultados no Microsoft Excel.

>[AZURE.NOTE] As informações neste documento são específicas para clusters HDInsight baseados no Windows. Para obter informações sobre clusters baseados em Linux, consulte [Hadoop tutorial: Introdução ao uso baseados em Linux Hadoop em HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

Suponha que você tem um grande conjunto de dados não estruturado e você deseja executar uma consulta de seção nele para extrair informações significativas. É exatamente o que você vai fazer neste tutorial. Veja aqui como fazer isso:

   !["Hadoop tutorial: criar uma conta; criar um cluster de Hadoop; enviar uma consulta de seção; Analise dados no Excel.][image-hdi-getstarted-flow]

Assista a um vídeo de demonstração deste tutorial para aprender Hadoop em HDInsight:

![Vídeo de um tutorial Hadoop primeiro: enviar uma consulta de seção em um cluster de Hadoop e analisar os resultados no Excel.][img-hdi-getstarted-video]

**[Assista o tutorial de Hadoop para HDInsight no YouTube](https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS)**

Em conjunto com a disponibilidade geral do Azure HDInsight, a Microsoft também fornece emulador de HDInsight do Azure, anteriormente conhecido como *Microsoft HDInsight Developer Preview*. O emulador destinos cenários de desenvolvedor e aceita somente implantações de nó único. Para obter informações sobre como usar o emulador de HDInsight, consulte [Começar a usar o emulador HDInsight][hdinsight-emulator].

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial para Hadoop no Windows, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Estação de trabalho de um computador** com o Office 2013 Professional Plus, o Office 365 Pro Plus, o Excel 2013 autônomo ou o Office 2010 Professional Plus.

### <a name="access-control-requirements"></a>Requisitos de controle de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="create-hadoop-clusters"></a>Criar clusters Hadoop

Quando você cria um cluster, você criar recursos de computação Azure que contêm Hadoop e aplicativos relacionados. Nesta seção, você criar um cluster de versão 3,2 HDInsight. Você também pode criar clusters Hadoop para outras versões. Para obter instruções, consulte [criar HDInsight clusters usando opções personalizadas][hdinsight-provision]. Para obter informações sobre versões de HDInsight e seus SLAs, consulte [o controle de versão do HDInsight componente](hdinsight-component-versioning.md).


**Para criar um cluster de Hadoop**

1. Entre [Portal do Azure](https://portal.azure.com/).
2. Clique em **novo**, clique em **Análise de dados**e clique em **HDInsight**. O portal abre um **Novo HDInsight Cluster** blade.

    ![Criar um novo cluster no Portal do Azure] (./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.CreateCluster.1.png "Criar um novo cluster no Portal do Azure")

3. Insira ou selecione o seguinte:

    ![Tipo e digite o nome de cluster] (./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.CreateCluster.2.png "Tipo e digite o nome de cluster")
    
  	|Nome do campo| Valor|
  	|----------|------|
  	|Nome do cluster| Um nome exclusivo para identificar o cluster|
  	|Tipo de cluster| Selecione **Hadoop** para este tutorial. |
  	|Sistema operacional de cluster| Selecione **O Centro de dados do Windows Server 2012 R2** para este tutorial.|
  	|Versão de HDInsight| Selecione a versão mais recente para este tutorial.|
  	|Assinatura| Selecione a assinatura Azure que será usada para o cluster.|
  	|Grupo de recursos | Selecione um grupo de recursos Azure existente ou crie um novo grupo de recursos. Um cluster de HDInsight básico contém um cluster e sua conta de armazenamento do padrão.  Você pode agrupar os dois em um grupo de recursos para facilitar o gerenciamento.|
  	|Credenciais| Insira o nome de usuário do cluster login e a senha. Cluster baseado no Windows pode ter 2 contas de usuário.  O usuário de cluster (ou usuário HTTP) é usado para gerenciar o cluster e submeter trabalhos.  Opcionalmente, você pode criar uma área de trabalho remota (RDP) conta de usuário remoto conectar ao cluster. Se você optar por habilitar a área de trabalho remota, você irá criar a conta de usuário RDP.|
  	|Fonte de dados| Clique em criar novo para criar uma nova conta de armazenamento do Azure padrão. Use o nome de cluster como o nome do contêiner de padrão. Cada cluster HDinsight tem um contêiner de Blob padrão em um atribuições dos armazenamento do Azure.  O local da conta padrão do armazenamento do Azure determina o local do cluster HDInsight.|
  	|Nó níveis de preços| Use 1 ou 2 nós de trabalho com a anotação padrão de nó e cabeça trabalhador preços camada para este tutorial.|
  	|Configuração opcional| Ignore esta parte.|

9. No **Novo HDInsight Cluster** blade, certifique-se de que **Pin para Startboard** está selecionada e clique em **criar**. Isso criará o cluster e adicionar um bloco para disponibilizá-lo para o Startboard do seu Portal do Azure. O ícone indicará que o cluster está criando e mudará para exibir o ícone de HDInsight após a conclusão da criação.

  	| Durante a criação | Criação concluída |
  	| ------------------ | --------------------- |
  	| ![Criando indicador em startboard](./media/hdinsight-hadoop-tutorial-get-started-windows/provisioning.png) | ![Criou o bloco de cluster](./media/hdinsight-hadoop-tutorial-get-started-windows/provisioned.png) |

    > [AZURE.NOTE] Levará algum tempo para o cluster seja criado, geralmente cerca de 15 minutos. Use o bloco na Startboard ou a entrada de **notificações** no lado esquerdo da página para verificar o processo de criação.

10. Uma vez concluída a criação, clique no bloco para o cluster do Startboard para iniciar a lâmina de cluster.


## <a name="run-a-hive-query-from-the-portal"></a>Executar uma consulta de ramificação a partir do portal
Agora que você criou um cluster de HDInsight, a próxima etapa é executar um trabalho de seção para consultar um exemplo de tabela de seção. Usaremos *hivesampletable*, que vem com clusters de HDInsight. A tabela contém dados sobre fabricantes de dispositivos móveis, plataformas e modelos. Uma consulta de seção nesta tabela recupera dados para dispositivos móveis por um fabricante específico.

> [AZURE.NOTE] Ferramentas de HDInsight para Visual Studio vem com o SDK do Azure para .NET versão 2.5 ou posterior. Usando as ferramentas no Visual Studio, você pode se conectar ao HDInsight cluster, criar tabelas de seção e executar consultas de seção. Para obter mais informações, consulte [Introdução ao uso de ferramentas de Hadoop HDInsight para Visual Studio][1].

**Para executar um trabalho de seção no painel de cluster**

1. Entre [Portal do Azure](https://portal.azure.com/).
2. Clique em **Procurar tudo** e clique em **Clusters de HDInsight** para ver uma lista de clusters, incluindo o cluster que você acabou de criar na seção anterior.
3. Clique no nome do cluster que você deseja usar para executar o trabalho de seção e clique em **Painel de controle** na parte superior da lâmina.
4. Abre uma página da Web em uma guia do navegador diferente. Insira a conta de usuário do Hadoop e a senha. O nome de usuário padrão é **administrador**; a senha é o que você inseriu durante a criação do cluster.
5. No painel, clique na guia **Editor de seção** . A seguinte página da web é aberta.

    ![Guia de Editor de seção no painel de cluster HDInsight.][img-hdi-dashboard]

    Há várias guias na parte superior da página. Na guia padrão é **Editor de seção**e outras guias são **Histórico de trabalho** e o **Navegador de arquivo**. Usando o dashboard, você pode enviar consultas de seção, verifique os logs de trabalho do Hadoop e procurar arquivos no armazenamento.

    > [AZURE.NOTE] Observe que a URL da página da Web é * &lt;ClusterName&gt;. azurehdinsight.net*. Portanto, em vez de abrir o painel a partir do portal, você pode abrir o painel de um navegador da web usando a URL.

6. Na guia **Editor de seção** , para **Nome da consulta**, digite **HTC20**.  O nome da consulta é o nome do cargo. No painel consulta, insira a consulta de seção, conforme mostrado na imagem:

    ![Seção consulta inserida no painel consulta do Editor de seção.][img-hdi-dashboard-query-select]

4. Clique em **Enviar**. Demora alguns momentos para obter os resultados de volta. A tela é atualizada a cada 30 segundos. Você também pode clicar em **Atualizar** para atualizar a tela.

    ![Resultados de uma consulta de seção no listados na parte inferior do painel de cluster.][img-hdi-dashboard-query-select-result]

5. Depois que o status mostra que o trabalho está concluído, clique no nome da consulta na tela para ver a saída. Tome nota da **Hora de início de trabalho (UTC)**. Você precisará dele mais tarde.

    ![Trabalho hora de início listadas na guia Histórico de trabalho do painel de cluster HDInsight.][img-hdi-dashboard-query-select-result-output]

    A página também mostra a **Saída de trabalho** e o **Log de trabalho**. Você também tem a opção para baixar o arquivo de saída (\_stdout) e o arquivo de log \(_stderr).


**Para navegar até o arquivo de saída**

1. No painel de cluster, clique em **Navegador de arquivos**.
2. Clique em nome de sua conta de armazenamento, clique no seu nome de contêiner (que é o mesmo nome do seu cluster) e clique em **usuário**.
3. Clique em **administrador** e clique em GUID que tem a hora da última modificação (um pouco após o trabalho começar tempo você anotou anteriormente). Copie esse GUID. Você precisará na próxima seção.


    ![A consulta de seção arquivo que GUID listado na guia do navegador de arquivo de saída.][img-hdi-dashboard-query-browse-output]


##<a name="connect-to-microsoft-business-intelligence-tools-for-excel"></a>Conectar a ferramentas do Microsoft business intelligence para Excel

Você pode usar o suplemento do Power Query para o Microsoft Excel para importar a saída de trabalho do HDInsight para o Excel, onde as ferramentas do Microsoft business intelligence podem ser usadas para analisar melhor os resultados.

Você deve ter o Excel 2013 ou 2010 instalado para concluir essa parte do tutorial.

**Para baixar o Microsoft Power Query para Excel**

- Baixar o Microsoft Power Query para o Microsoft Excel a partir do [Centro de Download da Microsoft](http://www.microsoft.com/download/details.aspx?id=39379) e instalá-lo.

**Importar dados de HDInsight**

1. Abra o Excel e crie uma nova pasta de trabalho.
3. Clique no menu de **Power Query** , clique em **De outras fontes**e clique em **Do Azure HDInsight**.

    ![Menu de importação de PowerQuery do Excel aberta para Azurehdinsight.][image-hdi-gettingstarted-powerquery-importdata]

3. Insira o **Nome da conta** da conta de armazenamento de Blob do Azure que está associada ao seu cluster e clique em **Okey**. (Essa é a conta de armazenamento que você criou anteriormente no tutorial.)
4. Insira a **Chave da conta** para a conta de armazenamento de Blob do Azure e clique em **Salvar**.
5. No painel direito, clique duas vezes no nome do blob. Por padrão, o nome de blob é a mesma que o nome do cluster.

6. Localize **stdout** na coluna **nome** . Verifique se o GUID na coluna correspondente do **Caminho da pasta** corresponde a GUID que você copiou anteriormente. Uma correspondência sugere que os dados de saída correspondem ao trabalho enviado. Clique em **binário** no lado esquerdo de coluna do **stdout**.

    ![Localizando a saída de dados GUID na lista de conteúdo.][image-hdi-gettingstarted-powerquery-importdata2]

9. Clique em **Fechar e carregar** no canto superior esquerdo para importar o trabalho da seção de saída para o Excel.

##<a name="run-samples"></a>Executar amostras

HDInsight cluster fornece um console de consulta que inclui uma galeria de Introdução para executar amostras diretamente do portal. Você pode usar as amostras para aprender a trabalhar com HDInsight, examinando alguns cenários básicos. Esses exemplos vêm com todos os componentes necessários, como os dados para analisar e as consultas a serem executadas nos dados. Para saber mais sobre os exemplos na Galeria de Introdução, consulte [Saiba Hadoop em HDInsight usando a Galeria de Introdução obtendo HDInsight](hdinsight-learn-hadoop-use-sample-gallery.md).

**Para executar a amostra**

1. De startboard Portal Azure, clique no bloco para o cluster que você acabou de criar.
 
2. Na nova lâmina cluster, clique em **Dashboard**. Quando solicitado, digite o nome de usuário de administrador e a senha para o cluster.

    ![Iniciar o painel de controle de cluster] (./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.Cluster.Dashboard.png "Iniciar o painel de controle de cluster")
 
3. Da página da Web que é aberta, clique na guia **Obtendo iniciado Galeria** e na categoria **soluções com dados de exemplo** , clique em amostra que você deseja executar. Siga as instruções na página da Web para concluir a amostra. A tabela a seguir lista alguns exemplos e fornece mais informações sobre o que cada amostra faz.

Exemplo | O que ela faz?
------ | ---------------
[Análise de dados de sensor][hdinsight-sensor-data-sample] | Saiba como usar HDInsight para processar dados históricos que é produzidos por aquecimento, ventilação e sistemas de ar condicionado (HVAC) para identificar os sistemas que não são capazes de confiavelmente manter uma temperatura de conjunto.
[Análise de log de site][hdinsight-weblogs-sample] | Saiba como usar HDInsight para analisar arquivos de log do site para obter percepção a frequência de visitas ao site em um dia de sites externos e um resumo dos erros de site que os usuários enfrentar.
[Análise de tendência do Twitter](hdinsight-analyze-twitter-data.md) | Saiba como usar HDInsight para analisar tendências em Twitter.

##<a name="delete-the-cluster"></a>Excluir o cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="next-steps"></a>Próximas etapas
Neste tutorial Hadoop, você aprendeu como criar um cluster de Hadoop no Windows no HDInsight, executa uma consulta de seção no dados e importar os resultados para o Excel, onde podem ser mais processada e exibida graficamente com ferramentas de business intelligence. Para saber mais, consulte os seguintes tutoriais:

- [Introdução ao uso de ferramentas de Hadoop HDInsight para Visual Studio][1]
- [Começar a usar o emulador HDInsight][hdinsight-emulator]
- [Usar o armazenamento de Blob do Azure com HDInsight][hdinsight-storage]
- [Administrar HDInsight usando o PowerShell][hdinsight-admin-powershell]
- [Carregar dados ao HDInsight][hdinsight-upload-data]
- [Usar MapReduce com HDInsight][hdinsight-use-mapreduce]
- [Use a seção com HDInsight][hdinsight-use-hive]
- [Usar porco com HDInsight][hdinsight-use-pig]
- [Usar Oozie com HDInsight][hdinsight-use-oozie]
- [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-versions]: hdinsight-component-versioning.md


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-emulator]: hdinsight-hadoop-emulator-get-started.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: hdinsight-hbase-tutorial-get-started.md


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#step-1-install


[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-hadoop-tutorial-get-started-windows/hdi-get-started-video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png
 
