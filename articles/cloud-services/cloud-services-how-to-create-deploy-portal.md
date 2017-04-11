<properties
    pageTitle="Como criar e implantar um serviço de nuvem | Microsoft Azure"
    description="Aprenda a criar e implantar um serviço de nuvem usando o portal do Azure."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="adegeo"/>




# <a name="how-to-create-and-deploy-a-cloud-service"></a>Como criar e implantar um serviço de nuvem

> [AZURE.SELECTOR]
- [Portal do Azure](cloud-services-how-to-create-deploy-portal.md)
- [Azure portal clássico](cloud-services-how-to-create-deploy.md)

O portal do Azure oferece duas maneiras de criar e implantar um serviço de nuvem: *Criar rápida* e *Personalizada*.

Este artigo explica como usar o método de criação rápida para criar um novo serviço de nuvem e depois use **carregar** para carregar e implantar um pacote de serviço de nuvem no Azure. Quando você usa esse método, o portal do Azure torna links conveniente disponíveis para concluir todos os requisitos enquanto você trabalha. Se você estiver pronto para implantar o seu serviço de nuvem quando criá-lo, você pode fazer ambas ao mesmo tempo usando criar personalizada.

> [AZURE.NOTE] Se você planeja publicar seu serviço de nuvem do Visual Studio Team Services (VSTS), use a criação rápida e configurar a publicação de VSTS do início rápido do Azure ou painel. Para obter mais informações, consulte [Delivery contínua no Azure usando o Visual Studio Team Services][TFSTutorialForCloudService], ou consulte a Ajuda para a página de **Início rápido** .

## <a name="concepts"></a>Conceitos
Três componentes são necessários para implantar um aplicativo como um serviço de nuvem no Azure:

- **Definição de serviço**  
  O arquivo de definição de serviço de nuvem (.csdef) define o modelo de serviço, incluindo o número de funções.

- **Configuração do serviço**  
  O arquivo de configuração de serviço de nuvem (.cscfg) fornece configurações para a nuvem funções de serviço e individuais, incluindo o número de instâncias de função.

- **Pacote de serviço**  
  O pacote de serviço (.cspkg) contém o código do aplicativo e configurações e o arquivo de definição de serviço.

Você pode saber mais sobre essas e como criar um pacote [aqui](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Preparar seu aplicativo
Antes de implantar um serviço na nuvem, você deve criar o pacote de serviço de nuvem (.cspkg) do código do seu aplicativo e um arquivo de configuração de serviço de nuvem (.cscfg). O SDK do Azure oferece ferramentas para preparar esses arquivos de implantação necessários. Você pode instalar o SDK na página de [Downloads do Azure](https://azure.microsoft.com/downloads/) , no idioma no qual você prefere desenvolver o código do aplicativo.

Três recursos de serviço de nuvem exigem configurações especiais antes de exportar um pacote de serviço:

- Se desejar implantar um serviço de nuvem que usa o Secure Sockets Layer (SSL) para criptografia de dados, [configure seu aplicativo](cloud-services-configure-ssl-certificate-portal.md#modify) para SSL.

- Se você quiser configurar conexões de área de trabalho remota para instâncias de função, [configure as funções](cloud-services-role-enable-remote-desktop.md) de trabalho remota. Isso só pode ser feito no portal do clássico.

- Se você deseja configurar detalhado de monitoramento de seu serviço de nuvem, habilite o diagnóstico do Azure para o serviço de nuvem. *Monitoramento mínimo* (o nível padrão de monitoração) usa contadores de desempenho obtidos os sistemas operacionais de host para instâncias de função (máquinas virtuais). *Monitoramento detalhado* reúne métricas adicionais com base em dados de desempenho em instâncias de função para habilitar a análise detalhada dos problemas que ocorrem durante o processamento do aplicativo. Para descobrir como habilitar o diagnóstico do Azure, consulte [Habilitando diagnóstico no Azure](cloud-services-dotnet-diagnostics.md).

Para criar um serviço de nuvem com implantações de funções da web ou funções de trabalho, você deve [criar o pacote de serviço](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Antes de começar

- Se você ainda não instalou o SDK do Azure, clique em **Instalar o SDK do Azure** para abrir a [página de Downloads do Azure](https://azure.microsoft.com/downloads/)e baixe o SDK para o idioma no qual você prefere desenvolver o código. (Você terá a oportunidade de fazer isso posteriormente.)

- Se quaisquer instâncias de função solicitar um certificado, crie os certificados. Serviços de nuvem exigem um arquivo. pfx com uma chave privada. [Você pode carregar os certificados para Azure]() que você crie e implante o serviço de nuvem.

- Se você planeja implantar o serviço de nuvem em um grupo de afinidade, crie o grupo de afinidade. Você pode usar um grupo de afinidade para implantar o seu serviço de nuvem e outros serviços Azure no mesmo local em uma região. Você pode criar o grupo de afinidade na área de **redes** do portal clássico do Azure, na página **grupos de afinidade** .


## <a name="create-and-deploy"></a>Criar e implantar

1. Faça logon no [portal do Azure](https://portal.azure.com/).
2. Clique em **Novo > máquinas virtuais**e, em seguida, role para baixo e clique em **Serviço de nuvem**.

    ![Publicar seu serviço de nuvem](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. Na parte inferior da página de informações exibida, clique em **criar**. 
4. Na nova lâmina **Serviço na nuvem** , insira um valor para o **nome DNS**.
5. Criar um novo **Grupo de recursos** ou selecione uma existente.
6. Selecione um **local**.
7. Clique em **pacote**. Isso abrirá a lâmina **carregar um pacote** . Preencha os campos obrigatórios.  

     Se qualquer uma das suas funções contêm uma única instância, certifique-se de **que implantar mesmo se uma ou mais funções contêm uma única instância** está selecionada.

8. Certifique-se de que a **implantação de início** está selecionado.
9. Clique em **Okey** que fechará a lâmina **carregar um pacote** .
10. Se você não tiver quaisquer certificados para adicionar, clique em **criar**.

    ![Publicar seu serviço de nuvem](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Carregar um certificado

Se seu pacote de implantação foi [configurado para usar certificados](cloud-services-configure-ssl-certificate-portal.md#modify), você pode carregar o certificado agora.

1. Selecione **certificados**na lâmina **certificados de adicionar** , selecione o arquivo. pfx de certificado SSL e fornecer a **senha** do certificado,
2. Clique em **Anexar certificado**e clique em **Okey** na lâmina **certificados de adicionar** .
3. Clique em **criar** na lâmina **Serviço na nuvem** . Quando a implantação tiver atingido o status **pronto** , você pode continuar para as próximas etapas.

    ![Publicar seu serviço de nuvem](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)


## <a name="verify-your-deployment-completed-successfully"></a>Verifique se sua implantação foi concluída com êxito

1. Clique na instância de serviço de nuvem.

    O status deve mostrar que o serviço está **em execução**.

2. Em **Essentials**, clique na **URL do Site** para abrir seu serviço de nuvem em um navegador da web.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Próximas etapas

* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerenciar seu serviço de nuvem](cloud-services-how-to-manage-portal.md).
* Configure [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).