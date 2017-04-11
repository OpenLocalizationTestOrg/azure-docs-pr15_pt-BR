<properties
    pageTitle="Análise de sentimento usando a análise de fluxo do Azure e aprendizado de máquina do Azure | Microsoft Azure"
    description="Como usar uma função definida pelo usuário e aprendizado de máquina em um trabalho de análise de fluxo"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>


<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/04/2016" 
    ms.author="jeffstok"
/>

# <a name="sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Análise de sentimento usando a análise de fluxo do Azure e aprendizado de máquina do Azure #

Este artigo destina-se para ajudá-lo a configurar rapidamente um trabalho de análise de fluxo de Azure simple, com a integração de aprendizado de máquina do Azure. Nós será usar um modelo de aprendizado de máquina de análise de sentimento da Galeria de inteligência de Cortana para analisar dados de texto streaming e determinar a pontuação de sentimento em tempo real. As informações neste artigo podem ajudá-lo a compreender cenários como analytics sentimento em tempo real no fluxo de dados do Twitter, analisar registros do cliente chats com a equipe de suporte e avaliar comentários em fóruns, blogs e vídeos, além de muitos outros em tempo real, previsão pontuação cenários.

Este artigo oferece um arquivo CSV de exemplo com texto como entrada no armazenamento de Blob do Azure, mostrado na imagem a seguir. O trabalho se aplica o modelo de análise sentimento como uma função definida pelo usuário (UDF) nos dados de texto de exemplo do armazenamento de blob. O resultado final é colocado no mesmo armazenamento de blob em outro arquivo CSV. 

![Análise de fluxo aprendizado de máquina](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

A imagem a seguir demonstra essa configuração. Para um cenário mais realista, você pode substituir o armazenamento de Blob com streaming Twitter dados de uma entrada de Hubs de evento do Azure. Além disso, você pode criar uma visualização em tempo real do [Microsoft Power BI](https://powerbi.microsoft.com/) do sentimento agregação.    

![Análise de fluxo aprendizado de máquina](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para concluir as tarefas que estão demonstrou neste artigo são da seguinte maneira:

-   Uma assinatura ativa do Azure.
-   Um arquivo CSV com alguns dados nela. Você pode baixar o arquivo mostrado na Figura 1 do [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/sampleinput.csv), ou você pode criar seu próprio arquivo. Neste artigo, vamos supor que você use um fornecido para download no GitHub.

Em um alto nível, para concluir as tarefas demonstradas neste artigo, você deverá fazer o seguinte:

1.  Carregue o arquivo CSV de entrada ao armazenamento de Blob do Azure.
2.  Adicione um modelo de análise sentimento da Galeria de inteligência Cortana para seu espaço de trabalho de aprendizado de máquina do Azure.
3.  Implante esse modelo como um serviço web no espaço de trabalho de aprendizado de máquina.
4.  Crie um trabalho de análise de fluxo que chama esse serviço web como uma função, determinar sentimento na entrada de texto.
5.  Iniciar a a análise de fluxo de trabalho e observe a saída.

## <a name="upload-the-csv-input-file-to-blob-storage"></a>Carregue o arquivo CSV de entrada para armazenamento de Blob

Nesta etapa, você pode usar qualquer arquivo CSV, como já especificada como disponível para download no GitHub. Você pode usar o [Gerenciador de armazenamento do Azure](http://storageexplorer.com/) ou Visual Studio para carregar o arquivo, ou você pode usar código personalizado. Usamos exemplos com base no Visual Studio.

1.  No Visual Studio, clique em **Azure** > **armazenamento** > **Anexar armazenamento externo**. Digite um **nome de conta** e **chave da conta**.  

    ![Análise de fluxo de máquina aprendizagem, Server Explorer](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)  

2.  Expanda o armazenamento anexado na etapa 1, clique em **Criar contêiner de Blob**e insira um nome lógico. Depois de criar o contêiner, abri-lo para exibir seu conteúdo. (Ele estará vazio neste ponto).  

    ![Fluxo de aprendizado de máquina de análise, criar blob](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)  

3.  Para carregar o arquivo CSV, clique em **Carregar Blob**e, em seguida, clique em **arquivo do disco local**.  

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adicionar o modelo de análise sentimento da Galeria de inteligência Cortana

1.  Baixe o [modelo de análise de previsão sentimento](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) da Galeria de inteligência de Cortana.  
2.  No Studio de aprendizado de máquina, clique em **Abrir no Studio**.  

    ![Fluxo de aprendizado de máquina de análise, abrir Studio de aprendizado de máquina](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3.  Entre para ir para o espaço de trabalho. Selecione o local mais adequado para seu próprio local.
4.  Clique em **Executar** na parte inferior da página.  
5.  Após o processo é executado com êxito, clique em **Implantar o serviço da Web**.
6.  O modelo de análise sentimento está pronto para uso. Para validar, clique no botão **Testar** e forneça a entrada de texto, como, "Eu adora Microsoft". O teste deve retornar um resultado similar ao seguinte:

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![Aprendizado de máquina de análise de fluxo, dados de análise](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

Na coluna de **aplicativos** , clique no link para o **Excel 2010 ou pasta de trabalho anterior** obter sua chave API e a URL que você precisará posteriormente configurar o análises de fluxo de trabalho. (Esta etapa é necessária somente para usar um modelo de aprendizado de máquina de outro espaço de trabalho de conta do Microsoft Azure. Este artigo pressupõe for o caso, para solucionar esse cenário.)  

Observe a chave web de URL e acesso de serviço do arquivo Excel baixado, conforme mostrado abaixo:  

![Aprendizado de máquina de análise de fluxo, rapidamente](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Criar um trabalho de análise de fluxo que usa o modelo de aprendizado de máquina

1.  Acesse o [portal do Azure](https://manage.windowsazure.com).  
2.  Clique em **novo** > **Serviços de dados** > **fluxo Analytics** > **criação rápida**. Insira um nome para o seu trabalho em **Nome do trabalho**, insira a região apropriada para o trabalho em **região**e, em seguida, selecione a conta na **Conta de armazenamento de monitoramento regionais**.    
3.  Depois que o trabalho é criado, na guia **entradas** , clique em **Adicionar uma entrada**.  

    ![Fluxo de aprendizado de máquina de análise, adicione a entrada de aprendizado de máquina](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)  

4.  Na primeira página do assistente **Adicionar entrada** , clique em **fluxo de dados**e clique em **Avançar**. Na próxima página, clique em **Armazenamento de Blob** como a entrada e clique em **Avançar**.  
5.  Na página **Configurações de armazenamento de Blob** do assistente, forneça o nome de contêiner de blob do armazenamento conta definido anteriormente quando você carregou os dados. Clique em **Avançar**. Para **Formato de serialização do evento**, clique em **CSV**. Aceite os valores padrão para o restante da página **configurações de serialização** . Clique em **Okey**.  
6.  Na guia **saídas** , clique em **Adicionar uma saída**.  

    ![Fluxo de aprendizado de máquina de análise, adicionar saída](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)  

7.  Clique em **Armazenamento de Blob**e insira os mesmos parâmetros, exceto para o contêiner. O valor de **entrada** foi configurado para ler a partir do contêiner de denominada "test" onde o arquivo **CSV** foi carregado. Para **saída**, insira "testoutput". Nomes de contêiner devem ser diferentes. Verificar se existe este contêiner.     
8.  Clique em **próximo** para definir **as definições de serialização a saída**. Como com a **entrada**, clique em **CSV**e clique no botão **Okey** .
9.  Na guia **funções** , clique em **Adicionar uma função de aprendizado de máquina**.  

    ![Fluxo de aprendizado de máquina de análise, adicione a função de aprendizado de máquina](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)  

10. Na página **Configurações do serviço de Web de aprendizado de máquina** , localize o espaço de trabalho aprendizado de máquina, o serviço da web e o ponto de extremidade padrão. Neste artigo, aplica as configurações manualmente para ganhar familiaridade com a configuração de um serviço web para qualquer espaço de trabalho, desde que você saiba a URL e tem a chave de API. Insira a **URL** do ponto de extremidade e **chave API**. Clique em **Okey**.    

    ![Aprendizado de máquina de análise de fluxo, serviço da web de aprendizado de máquina](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)    

11. Na guia **consulta** , modifique a consulta da seguinte maneira:    

 ```
    WITH subquery AS (  
        SELECT text, sentiment(text) as result from input  
    )  
 
    Select text, result.[Scored Labels]  
    Into output  
    From subquery  
 ```    
12. Clique em **Salvar** para salvar a consulta.

## <a name="start-the-stream-analytics-job-and-observe-the-output"></a>Iniciar a a análise de fluxo de trabalho e observe a saída

1.  Clique em **Iniciar** na parte inferior da página de trabalho.
2.  No **Começar a caixa de diálogo de consulta**, clique em **Vez de personalizado**e selecione um tempo antes de quando você carregou o CSV ao armazenamento de Blob. Clique em **Okey**.  

    ![Aprendizado de máquina de análise de fluxo, tempo personalizado](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)  

3.  Vá para o armazenamento de Blob usando a ferramenta usado para carregar o arquivo CSV, por exemplo, Visual Studio.
4.  Alguns minutos após o trabalho é iniciado, o contêiner de saída é criado e um arquivo CSV é carregado para ele.  
5.  Abra o arquivo no editor CSV padrão. Deve ser exibido algo semelhante ao seguinte:  

    ![Aprendizado de máquina de análise de fluxo, modo de exibição CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## <a name="conclusion"></a>Conclusão

Este artigo mostra como criar um trabalho de análise de fluxo que lê streaming dados de texto e aplica a análise de sentimento aos dados em tempo real. Você pode fazer tudo isso sem precisar se preocupar com a complexidade da criação de um modelo de análise sentimento. Essa é uma das vantagens do conjunto de inteligência de Cortana.

Você também pode exibir métricas relacionadas à função de aprendizado de máquina do Azure. Para fazer isso, clique na guia **Monitor** . Três métricas relacionadas à função são exibidas.  

- **Função solicitações** indica o número de solicitações enviadas a um serviço da web de aprendizado de máquina.  
- **Função eventos** indica o número de eventos na solicitação. Por padrão, cada solicitação para um serviço da web de aprendizado de máquina contém até 1.000 eventos.  
- **Falha na função solicitações** indica o número de solicitações com Falha ao serviço da web de aprendizado de máquina.  

    ![Aprendizado de máquina de análise de fluxo, modo de exibição de monitor de aprendizado de máquina](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)  
