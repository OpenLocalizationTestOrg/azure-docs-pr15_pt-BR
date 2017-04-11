<properties 
    pageTitle="Estágio de uma implantação de serviço de nuvem (node) | Microsoft Azure" 
    description="Saiba como implantar o seu aplicativo do Azure para um ambiente de teste e, em seguida, implantar em um ambiente de produção usando troca de IP Virtual (VIP)." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>



# <a name="staging-an-application-in-azure"></a>Preparar um aplicativo no Azure

Um aplicativo de pacote pode ser implantado no ambiente de teste do Azure para ser testado antes que você os mova para o ambiente de produção na qual o aplicativo é acessível na Internet. O ambiente de teste é exatamente como o ambiente de produção, exceto que você só pode acessar o aplicativo em estágios com uma URL ofuscada que é gerado pelo Azure. Após ter verificado que seu aplicativo está funcionando corretamente, ele pode ser implantado ao ambiente de produção, executando uma troca de IP Virtual (VIP).

> [AZURE.NOTE] As etapas neste artigo aplicam-se apenas aos aplicativos de nó hospedados como um serviço de nuvem do Azure.

## <a name="step-1-stage-an-application"></a>Etapa 1: Um aplicativo do estágio

Essa tarefa aborda como um aplicativo do estágio usando o **Microsoft Azure PowerShell**.

1.  Quando um serviço de publicação, basta passar o **-Slot** parâmetro para o cmdlet **Publicar-AzureServiceProject** .

    ```powershell
    Publish-AzureServiceProject -Slot staging
    ```

2.  Faça logon no [portal de clássico Azure] e selecione **Serviços de nuvem**. Depois que o serviço de nuvem é criado e o status de coluna de **preparação** foi atualizado **em execução**, clique no nome do serviço.

    ![Portal exibindo um serviço em execução][cloud-service]

3.  Selecione o **Painel de controle**e selecione **teste**.

    ![Painel de serviço de nuvem][cloud-service-dashboard]

4. Observe o valor na entrada de **URL do Site** para a direita. O nome DNS é uma ID interna ofuscada Azure gerado.

    ![url do site][cloud-service-staging-url]

Agora você pode verificar se o aplicativo está funcionando corretamente no ambiente de teste usando a URL de site de preparação.

## <a name="step-2-upgrade-an-application-in-production-by-swapping-vips"></a>Etapa 2: Atualizar um aplicativo em produção por VIPs trocando

Após ter verificado a versão atualizada de um aplicativo no ambiente de teste, você pode rapidamente disponibilizá-lo em produção trocando o IPs virtual (VIPs) os ambientes de estágios e produção.

> [AZURE.NOTE] Esta etapa supõe que você já implantou um aplicativo de produção e transferidos a versão atualizada do aplicativo.

1.  Entrar no [portal do Azure clássico], clique em **Serviços de nuvem** e selecione o nome do serviço.

2.  No **Painel de controle**, selecione **teste**e, em seguida, clique em **trocar** na parte inferior da página. Isso abre a caixa de diálogo de troca de VIP.

    ![caixa de diálogo de troca de VIP][vip-swap-dialog]

3.  Examine as informações e clique em **Okey**. As duas implantações começam a atualização como a implantação de preparação alterna para produção e a implantação de produção alterna para o teste.

E com sucesso transferidos uma implantação atualizado uma implantação de produção trocando VIPs com a implantação no teste.

## <a name="additional-resources"></a>Recursos adicionais

- [Como implantar uma atualização do serviço de produção trocando VIPs no Azure]

[Azure portal clássico]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[Como implantar uma atualização do serviço de produção trocando VIPs no Azure]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
