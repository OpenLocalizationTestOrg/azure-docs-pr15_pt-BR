<properties
   pageTitle="Fluxo de dados de análise de fluxo de dados Lucerne loja | Azure"
   description="Usar a análise de fluxo Azure transmitir dados no repositório de Lucerne de dados do Azure"
   services="data-lake-store,stream-analytics" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/07/2016"
   ms.author="nitinme"/>

# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Dados de fluxo do Azure armazenamento de Blob no repositório de Lucerne de dados usando a análise de fluxo Azure

Neste artigo, você aprenderá como usar o armazenamento do Azure dados Lucerne como uma saída para um trabalho de análise de fluxo de Azure. Este artigo demonstra um cenário simple que lê dados de um blob de armazenamento do Azure (entrada) e grava os dados para o armazenamento de Lucerne de dados (saída).

>[AZURE.NOTE] No momento, criação e configuração do Data Lucerne Store saídas para a análise de fluxo é suportada somente no [Portal de clássico do Azure](https://manage.windowsazure.com). Portanto, algumas partes deste tutorial usará o Portal de clássico do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).

- **Ativar sua assinatura do Azure** para dados Lucerne Store Public Preview. Consulte [as instruções](data-lake-store-get-started-portal.md#signup).

- **Conta de armazenamento do azure**. Você vai usar um contêiner de blob dessa conta para entrada de dados para um trabalho de análise de fluxo. Para este tutorial, suponha que você criar uma conta de armazenamento chamado **datalakestoreasa** e um contêiner dentro da conta chamado **datalakestoreasacontainer**. Quando você tiver criado o contêiner, carregar um arquivo de dados de exemplo-lo. Você pode obter um arquivo de dados de exemplo do [Azure dados Lucerne gito repositório](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Você pode usar vários clientes, como o [Gerenciador de armazenamento do Azure](http://storageexplorer.com/), para carregar dados em um contêiner de blob.

    >[AZURE.NOTE] Se você criar a conta a partir do Portal do Azure, certifique-se de que criá-lo com o modelo **clássico** de implantação. Isso garante que a conta de armazenamento pode ser acessada Azure clássico no Portal do, porque é o que usamos para criar uma a análise de fluxo de trabalho. Para obter instruções sobre como criar uma conta de armazenamento do Portal do Azure usando a implantação clássico, consulte [criar uma conta de armazenamento do Azure](../storage/storage-create-storage-account/#create-a-storage-account).
    >
    > Ou, você poderia criar uma conta de armazenamento do Portal de clássico do Azure.

- **Conta de armazenamento de Lucerne de dados do azure**. Siga as instruções contidas em [Introdução ao armazenamento de Lucerne de dados do Azure usando o Portal do Azure](data-lake-store-get-started-portal.md).  


## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de análise de fluxo

Você começa criando um trabalho de análise de fluxo que inclui uma fonte de entrada e um destino de saída. Para este tutorial, a fonte for um contêiner de blob do Microsoft Azure e o destino é dados Lucerne Store.

1. Entre [Portal de clássico Azure](https://manage.windowsazure.com).

2. No canto inferior esquerdo da tela, clique em **novo**, **Serviços de dados**, **A análise de fluxo**, **Criar rápida**. Forneça os valores conforme mostrado abaixo e clique em **Criar trabalho de análise do fluxo**.

    ![Criar um trabalho de análise de fluxo] (./media/data-lake-store-stream-analytics/create.job.png "Criar um análise de fluxo de trabalho")

## <a name="create-a-blob-input-for-the-job"></a>Criar uma entrada de Blob para o trabalho

1. Abra a página para o trabalho de análise de fluxo, clique na guia **entradas** e, em seguida, clique em **Adicionar uma entrada** para iniciar um assistente.

2. Na página **Adicionar uma entrada para o seu trabalho** , selecione **fluxo de dados**e, em seguida, clique na seta Avançar.

    ![Adicionar uma entrada para o seu trabalho] (./media/data-lake-store-stream-analytics/create.input.1.png "Adicionar uma entrada para o seu trabalho")

3. Na página **Adicionar um fluxo de dados para o seu trabalho** , selecione o **armazenamento de Blob**e, em seguida, clique na seta Avançar.

    ![Adicionar um fluxo de dados para o trabalho] (./media/data-lake-store-stream-analytics/create.input.2.png "Adicionar um fluxo de dados para o trabalho")

4. Na página **configurações de armazenamento de Blob** , fornecem detalhes para o armazenamento de blob que você usará como fonte de dados de entrada.

    ![Fornecer configurações de armazenamento de blob] (./media/data-lake-store-stream-analytics/create.input.3.png "Fornecer configurações de armazenamento de blob")

    * **Enter um alias de entrada**. Este é um nome exclusivo que você fornece para o trabalho de entrada.
    * **Selecione uma conta de armazenamento**. Certificar-se de que a conta de armazenamento está na mesma região como o trabalho de análise de fluxo ou provocará custo adicional de mover dados entre regiões.
    * **Fornecer um contêiner de armazenamento**. Você pode optar por criar um novo recipiente ou selecione um contêiner existente.

    Clique na seta Avançar.

5. Na página **configurações de serialização** , definir o formato de serialização como **CSV**, delimitador como **guia**, codificação como **UTF8**e clique na marca de seleção.

    ![Fornecer as configurações de serialização] (./media/data-lake-store-stream-analytics/create.input.4.png "Fornecer as configurações de serialização")

6. Quando terminar com o assistente, a entrada de blob será adicionada sob a guia de **entradas** e a coluna de **Diagnóstico** deve mostrar **Okey**. Você pode também explicitamente testar a conexão para a entrada, usando o botão **Testar Conexão** na parte inferior.

## <a name="create-a-data-lake-store-output-for-the-job"></a>Criar uma saída de armazenamento de Lucerne de dados para o trabalho

1. Abra a página para o trabalho de análise de fluxo, clique na guia **saídas** e, em seguida, clique em **Adicionar uma saída** para iniciar um assistente.

2. Na página **Adicionar uma saída ao seu trabalho** , selecione **Dados Lucerne armazenar**e, em seguida, clique na seta Avançar.

    ![Adicionar uma saída para o seu trabalho] (./media/data-lake-store-stream-analytics/create.output.1.png "Adicionar uma saída para o seu trabalho")

3. Na página **conexão de autorizar** , se você já tiver criado uma conta de armazenamento de Lucerne de dados, clique em **Autorizar agora**. Caso contrário, clique em **Inscreva-se agora** para criar uma nova conta. Para este tutorial, vamos supor que você já tem uma conta de armazenamento de Lucerne dados criada (conforme mencionado no pré-requisito). Você será automaticamente autorizado usando as credenciais com as quais você conectado ao Portal de clássico do Azure.

    ![Autorize dados Lucerne Store] (./media/data-lake-store-stream-analytics/create.output.2.png "Autorize dados Lucerne Store")

4. Na página **Configurações do repositório de Lucerne de dados** , insira as informações conforme mostrado na captura de tela abaixo.

    ![Configurações do repositório de Lucerne especificar dados] (./media/data-lake-store-stream-analytics/create.output.3.png "Configurações do repositório de Lucerne especificar dados")

    * **Enter um alias de saída**. Este é um nome exclusivo que você fornecer para a saída de trabalho.
    * **Especificar uma conta de armazenamento de Lucerne de dados**. Você deve já criou, conforme mencionado no pré-requisito.
    * **Especificar um padrão de prefixo de caminho**. Isso é necessário para identificar os arquivos de saída que foram criados para armazenamento de dados de Lucerne pelo trabalho de análise de fluxo. Como os títulos das saídas escritos pelo trabalho estão em um formato GUID, incluindo um prefixo ajudará a identificar a saída escrita. Se você quiser incluir um carimbo de data e hora como parte do prefixo certificar-se de que você incluir `{date}/{time}` no padrão de prefixo. Se você incluir isso, os campos de **Data **e **Hora formato** estão habilitados e você pode selecionar o formato de escolha.

    Clique na seta Avançar.

5. Na página **configurações de serialização** , definir o formato de serialização como **CSV**, delimitador como **guia**, codificação como **UTF8**e clique na marca de seleção.

    ![Especifique o formato de saída] (./media/data-lake-store-stream-analytics/create.output.4.png "Especifique o formato de saída")

6. Quando terminar com o assistente, a saída de dados Lucerne repositório será adicionada na guia **saídas** e a coluna de **Diagnóstico** deve mostrar **Okey**. Você pode também explicitamente testar a conexão para a saída, usando o botão **Testar Conexão** na parte inferior.

## <a name="run-the-stream-analytics-job"></a>Executar a a análise de fluxo de trabalho

Para executar uma análise de fluxo de trabalho, você deve executar uma consulta a partir da guia de consulta. Para este tutorial, você pode executar a consulta de amostra, substituindo os espaços reservados com o trabalho de entrada e saída aliases, conforme mostrado na captura de tela abaixo.

![Executar consulta] (./media/data-lake-store-stream-analytics/run.query.png "Executar consulta")

Clique em **Salvar** da parte inferior da tela e, em seguida, clique em **Iniciar**. Na caixa de diálogo, selecione **Hora personalizado**e, em seguida, selecione uma data no passado, como **1/1/2016**. Clique na marca de seleção para iniciar o trabalho. Ele pode levar alguns minutos para iniciar o trabalho.

![Definir o tempo de trabalho] (./media/data-lake-store-stream-analytics/run.query.2.png "Definir o tempo de trabalho")

Depois que o trabalho é iniciado, clique na guia **Monitor** para ver como os dados foi processados.

![Trabalho de monitor] (./media/data-lake-store-stream-analytics/run.query.3.png "Trabalho de monitor")

Finalmente, você pode usar o [Portal do Azure](https://portal.azure.com) para abrir sua conta de armazenamento de Lucerne de dados e verificar se os dados com êxito escritos à conta.

![Verificar saída] (./media/data-lake-store-stream-analytics/run.query.4.png "Verificar saída")

No painel Explorador de dados, observe que a saída é gravada em uma pasta conforme especificado no repositório Lucerne dados as configurações de saída (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Consulte também

* [Criar um cluster de HDInsight para usar dados Lucerne Store](data-lake-store-hdinsight-hadoop-use-portal.md)
