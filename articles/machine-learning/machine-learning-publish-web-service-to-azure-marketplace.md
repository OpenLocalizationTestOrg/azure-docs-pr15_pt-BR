<properties 
    pageTitle="Publicar aprendizado de máquina serviço ao Azure Marketplace web | Microsoft Azure" 
    description="Como publicar seu serviço da Web de aprendizado de máquina de Azure do Azure Marketplace" 
    services="machine-learning" 
    documentationCenter="" 
    authors="BharathS" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="bharaths"/>

# <a name="publish-azure-machine-learning-web-service-to-the-azure-marketplace"></a>Publicar o serviço de Web de aprendizado de máquina Azure para o Azure Marketplace 

Do Azure Marketplace oferece a capacidade de publicar serviços da web de aprendizado de máquina do Azure como paga ou libere serviços para consumo por clientes externos. Este artigo fornece uma visão geral do processo com links para as diretrizes para iniciá-lo. Usando esse processo, você pode disponibilizar seus serviços da web para outros desenvolvedores consumir em seus aplicativos.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="overview-of-the-publishing-process"></a>Visão geral do processo de publicação 

A seguir estão as etapas para a publicação de um serviço da web de aprendizado de máquina do Azure ao Azure Marketplace:

1. Criar e publicar um serviço de aprendizado de máquina solicitação-resposta (RR)
2. Implantar o serviço de produção e obter as informações de ponto de extremidade chave API e OData.
3. Usar a URL do serviço da web publicado para publicar [Azure Marketplace (dados de mercado)](https://publish.windowsazure.com/workspace/) 
4. Depois de enviada, sua oferta é revisada e precisa ser aprovados antes de seus clientes pode iniciar comprando-lo. O processo de publicação pode levar alguns dias úteis. 

## <a name="walk-through"></a>Percorrer
###<a name="step-1-create-and-publish-a-machine-learning-request-response-service-rrs"></a>Etapa 1: Criar e publicar um serviço de aprendizado de máquina solicitação-resposta (RR)###
 Se você ainda não fez isso, por favor, dê uma olhada neste [percorrer](machine-learning-walkthrough-5-publish-web-service.md).

###<a name="step-2-deploy-the-service-to-production-and-obtain-the-api-key-and-odata-endpoint-information"></a>Etapa 2: Implantar o serviço de produção e obter a chave API e informações de ponto de extremidade de OData###
1. No [Portal de clássico do Azure](http://manage.windowsazure.com), selecione a opção de **APRENDIZADO de máquina** na barra de navegação à esquerda e selecione o seu espaço de trabalho. 

2. Clique na guia **Serviços WEB** e selecione o serviço da web que você gostaria de publicar para o marketplace.

    ![Azure Marketplace][workspace]

3. Selecione o ponto de extremidade que você gostaria de ter o marketplace consumir. Se você não tiver criado quaisquer pontos de extremidade adicionais, você pode selecionar o ponto de extremidade **padrão** .

4. Depois de clicar no ponto de extremidade, você poderá ver a **Chave API**. Você precisará essa parte das informações posteriormente na etapa 3, portanto, verifique uma cópia dele.

    ![Azure Marketplace][apikey]

5. Clique no método de **Solicitação/resposta** , neste ponto, que não há suporte para serviços de execução de lote publicação para o marketplace. Isso levará você para a página de ajuda de API para o método de solicitação/resposta.

6. Copie o **Endereço do ponto de extremidade OData**, você precisará dessas informações posteriormente na etapa 3.

    ![Azure Marketplace][odata]




Implante o serviço em produção.



###<a name="step-3-use-the-url-of-the-published-web-service-to-publish-to-azure-marketplace-datamarket"></a>Etapa 3: Usar a URL do serviço da web publicado para publicar Azure Marketplace (DataMarket)###

1.  Navegue até [Azure Marketplace (dados de mercado)](http://datamarket.azure.com/home) 
2.  Clique no link **Publicar** na parte superior da página. Isso levará você para o [Portal de publicação do Microsoft Azure](https://publish.windowsazure.com)
3.  Clique na seção **fornecedores** para registrar como um fornecedor.
4.  Ao criar uma nova oferta, selecione **Serviços de dados**, clique em **criar um novo serviço de dados**. 
 
    ![Azure Marketplace][image1]

    <br />


5.  Em **planos** fornecem informações sobre sua oferta, incluindo um plano de preços. Decida se você oferecerá um serviço gratuito ou pago. Para obter paga, forneça informações de pagamento como suas informações bancárias e o imposto.

6.  Em **Marketing** fornecem informações sobre sua oferta, como o título e a descrição de sua oferta.

7.  Em **preço** que você pode definir o preço para seus planos para países específicos ou deixe o sistema "autoprice" sua oferta.

8. Na guia **Serviço de dados** , clique em **Serviço Web** como a **Fonte de dados**.

    ![Azure Marketplace][image2]

9.  Obtenha a chave de URL e API de serviço web do Portal de clássico do Azure, conforme explicado na etapa 2 acima.

10. Na caixa de diálogo Configuração do serviço de dados de mercado, cole o endereço do ponto de extremidade OData na caixa de texto **URL do serviço** .

11. Para **autenticação**, escolha **cabeçalho** como o **Esquema de autenticação**.

    - Digite "Autorização" para o **nome de cabeçalho**.
    - Para o **Valor do cabeçalho**, digite "Portador" (sem as aspas), clique na barra de **espaço** e, em seguida, colar a chave de API.
    - Marque a caixa de seleção **Este serviço é OData** .
    - Clique em **Testar Conexão** para testar a conexão.

12. Em **categorias**, certifique-se de **Que aprendizado de máquina** está selecionada.

13. Quando você terminar inserindo todos os metadados sobre sua oferta, clique em **Publicar**e, em seguida, **envio por Push para preparação**. Neste ponto, você será notificado de quaisquer problemas restantes que você precisa corrigir.

14. Depois de certificar conclusão de todos os problemas pendentes, clique em **Solicitar aprovação por push para produção**. O processo de publicação pode levar alguns dias úteis. 


[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]:./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]:./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]:./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 
