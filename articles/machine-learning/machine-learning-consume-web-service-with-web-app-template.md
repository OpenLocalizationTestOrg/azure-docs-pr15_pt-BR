<properties
    pageTitle="Consumir um serviço da web de aprendizado de máquina com um modelo de aplicativo da web | Microsoft Azure"
    description="Use um modelo de aplicativo da web no Azure Marketplace para consumir um serviço web de previsão no aprendizado de máquina do Azure."
    keywords="serviço da Web, operationalization, API REST, aprendizado de máquina"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="garye;raymondl"/>

# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>Consumir um serviço da web de aprendizado de máquina do Azure com um modelo de aplicativo da web

>[AZURE.NOTE] Este tópico descreve técnicas aplicáveis a um serviço web clássico. 

Uma vez você desenvolveu seu modelo de previsão e implantá-lo como um serviço web Azure usando Studio de aprendizado de máquina ou usando ferramentas como R ou Python, você pode acessar o modelo de operationalized usando uma API REST.

Há várias maneiras para consumir a API REST e acessar o serviço web. Por exemplo, você pode escrever um aplicativo em c#, R ou Python usando o código de exemplo gerado para você quando você implantou o serviço da web (disponível na página de Ajuda da API no painel de serviço web no Studio de aprendizado de máquina). Ou você pode usar a pasta de trabalho do Microsoft Excel de exemplo criada para você (também está disponível no painel de serviço web no Studio).

Mas a maneira mais rápida e fácil de acessar o serviço web é por meio de modelos de aplicativo Web disponível no [Azure Marketplace de aplicativo Web](https://azure.microsoft.com/marketplace/web-applications/all/).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>A modelos de aplicativo da Web de aprendizado de máquina Azure

Os modelos de aplicativo web disponíveis no mercado Azure podem criar um aplicativo web personalizado que sabe dados de entrada e os resultados esperados de seu serviço da web. Tudo o que você precisa fazer é dar o acesso de aplicativo da web para seu serviço da web e dados e o modelo faz o resto.

Dois modelos estão disponíveis:

- [Modelo de aplicativo do Azure ML solicitação-resposta serviço Web](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
- [Modelo de aplicativo do Azure lote ML execução serviço Web](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Cada modelo cria um aplicativo de ASP.NET de exemplo, usando o URI de API e a chave para o serviço da web e implantado como um site do Azure. O modelo de resposta de solicitação de serviço (RR) cria um aplicativo web que permite que você envie uma única linha de dados para o serviço da web para obter um único resultado. O modelo de serviço de execução de lote (BES) cria um aplicativo web que permite que você enviar muitas linhas de dados para obter vários resultados.

Sem codificação é necessária para usar esses modelos. Basta fornecer o URI de API e a chave e o modelo constrói o aplicativo para você.

## <a name="how-to-use-the-request-response-service-rrs-template"></a>Como usar o modelo de resposta de solicitação de serviço (RR)

Assim que tiver implantado seu serviço da web, você pode seguir as etapas abaixo para usar o modelo de aplicativo da web de RR, conforme mostrado no diagrama a seguir.

![Processo para usar o modelo de RR da web][image1]

1. No Studio de aprendizado de máquina, abra a guia **Serviços Web** e abra o serviço da web que você deseja acessar. Copie a chave listada em **chave API** e salvá-lo.

    ![Chave API][image3]

2. Abra a página de ajuda de API de **Solicitação/resposta** . Na parte superior da página da Ajuda, sob **solicitação**, copie o valor de **URI de solicitação** e salvá-lo. Este valor ficará assim:

        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

    ![URI de solicitação][image4]

3. Acesse o [portal do Azure](https://portal.azure.com), **Login**, clique em **novo**, procure e selecione **Azure ML resposta de solicitação de serviço Web App**, clique em **criar**. 

    - Dê um nome exclusivo para o aplicativo web. A URL do aplicativo web será esse nome seguido `.azurewebsites.net.` por exemplo,`http://carprediction.azurewebsites.net.`

    - Selecione a assinatura do Azure e serviços em qual o seu serviço da web está em execução.

    - Clique em **criar**.

    ![Crie aplicativo web][image5]

4. Quando Azure terminou Implantando o web app, clique na **URL** na página de configurações de aplicativo da web no Azure ou insira a URL em um navegador da web. Por exemplo,`http://carprediction.azurewebsites.net.`

5. Quando o aplicativo web é executado ele solicitará que você a **URL de postagem de API** e **Chave API**.
Insira os valores que você salvou anteriormente:
    - O **URI de solicitação** da página de ajuda de API de **API postar na URL**
    - **Chave API** no painel de serviço web para a **Chave API**.

    Clique em **Enviar**.

    ![Insira a postagem URI e chave API][image6]

6. O aplicativo web exibe sua página de **Configuração de aplicativo da Web** com as configurações atuais de serviço web. Aqui você pode fazer alterações nas configurações usadas pelo web app.

    > [AZURE.NOTE] Alterar as configurações aqui só é alterada para este aplicativo web. Ele não altera as configurações padrão do seu serviço da web. Por exemplo, se você alterar a **Descrição** aqui ele não altera a descrição mostrada no painel de serviço da web no Studio de aprendizado de máquina.

    Quando terminar, clique em **Salvar alterações**e clique em **Ir para página inicial**.

7. Na home page, você pode inserir valores para enviar para o serviço web, clique em **Enviar**e o resultado será retornado.

Se você quiser retornar para a página de **configuração** , vá para o `setting.aspx` página do aplicativo web. Por exemplo: `http://carprediction.azurewebsites.net/setting.aspx.` você será solicitado a digitar a chave de API novamente - você precisar para acessar a página e atualizar as configurações.

Você pode interromper, reiniciar ou excluir o aplicativo da web no portal do Azure como qualquer outro aplicativo web. Enquanto estiver em execução, você pode navegue até o endereço web inicial e insira novos valores.

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>Como usar o modelo de serviço de execução de lote (BES)

Você pode usar o modelo de aplicativo da web de BES da mesma maneira que o modelo de RR, exceto que o aplicativo web criado permitirá várias linhas de dados de enviar e receber vários resultados.

Os resultados de um serviço de web de execução de lote são armazenados em um contêiner de armazenamento do Azure; os valores de entrada podem se originar de armazenamento do Azure ou um arquivo local.
Portanto, você precisará de um contêiner de armazenamento do Azure para armazenar os resultados retornados pelo web app, e você precisará preparar seus dados de entrada.

![Processo para usar o modelo de web BES][image2]

1. Siga o mesmo procedimento para criar o BES web app para o modelo de RR, exceto:
    - Obtenha o **URI de solicitação** da página de ajuda de API de **Execução de LOTE** para o serviço web.
    - Vá para o [Modelo de aplicativo do Azure ML lote execução serviço Web](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) para abrir o modelo de BES no Azure Marketplace e clique em **Criar Web App**.

2. Para especificar onde deseja que os resultados armazenados, insira as informações do contêiner de destino na home page do web app. Também especifica onde o aplicativo da web pode obter os valores de entrada, em um arquivo local ou um contêiner de armazenamento do Azure.
Clique em **Enviar**.

    ![Informações de armazenamento][image7]

O aplicativo web exibirá uma página com status de trabalho.
Quando o trabalho concluído você terá a localização dos resultados em armazenamento de blob do Microsoft Azure. Você também tem a opção de baixar os resultados para um arquivo local.

## <a name="for-more-information"></a>Para obter mais informações

Para saber mais sobre...

- Criando uma experiência de aprendizado de máquina com Studio de aprendizado de máquina, consulte [criar seu primeiro experimento no Studio de aprendizado de máquina do Azure](machine-learning-create-experiment.md)

- como implantar seu aprendizado de máquina experimentar como um serviço web, consulte [implantar um serviço web de aprendizado de máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md)

- outras maneiras de acessar o serviço web, consulte [como consumir um serviço da web de aprendizado de máquina do Azure](machine-learning-consume-web-services.md)


[image1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[image5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[image6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png
