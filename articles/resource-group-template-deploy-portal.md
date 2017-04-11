<properties 
    pageTitle="Usar o portal do Azure para implantar recursos Azure | Microsoft Azure" 
    description="Use o portal do Azure e gerenciar de recurso do Azure para implantar seus recursos." 
    services="azure-resource-manager,azure-portal" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Implantar recursos com modelos do Gerenciador de recursos e portal do Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Este tópico mostra como usar o [portal do Azure](https://portal.azure.com) com [Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md) para implantar seus recursos Azure. Para saber sobre como gerenciar seus recursos, consulte [recursos de gerenciar Azure por meio do portal](./azure-portal/resource-group-portal.md).

Atualmente, não cada serviço suporta o portal ou o Gerenciador de recursos. Para esses serviços, você precisa usar o [portal clássico](https://manage.windowsazure.com). O status de cada serviço, consulte o [gráfico de disponibilidade de portal Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## <a name="create-resource-group"></a>Criar grupo de recursos

1. Para criar um grupo de recursos vazia, selecione **novo** > **gerenciamento** > **Grupo de recursos**.

    ![Criar grupo de recursos vazia](./media/resource-group-template-deploy-portal/create-empty-group.png)

2. Dar a ele um nome e local e, se necessário, selecione uma assinatura. Você precisa fornecer um local para o grupo de recursos, porque o grupo de recursos armazena metadados sobre os recursos. Por razões de conformidade, talvez você queira especificar onde os metadados está armazenado. Em geral, recomendamos que você especifique um local onde a maioria dos seus recursos residirão. Usar o mesmo local pode simplificar o seu modelo.

    ![definir valores de grupo](./media/resource-group-template-deploy-portal/set-group-properties.png)

## <a name="deploy-resources-from-marketplace"></a>Implantar recursos do Marketplace

Depois de criar um grupo de recursos, você pode implantar recursos-lo do Marketplace. O Marketplace fornece soluções predefinidas para cenários comuns.

1. Para iniciar uma implantação, selecione **novo** e o tipo de recurso que você gostaria de implantar. Em seguida, procure por versão específica do recurso que você gostaria de implantar.

    ![implantar o recurso](./media/resource-group-template-deploy-portal/deploy-resource.png)

2. Se você não vir a solução específica que você gostaria de implantar, você pode pesquisar o Marketplace para ele.

    ![marketplace de pesquisa](./media/resource-group-template-deploy-portal/search-resource.png)

3. Dependendo do tipo de recurso selecionado, você tem uma coleção de propriedades relevantes para definir antes da implantação. Essas opções não são mostradas aqui, conforme elas variam com base no tipo de recurso. Para todos os tipos, você deve selecionar um grupo de recursos de destino. A imagem a seguir mostra como criar um aplicativo web e implantá-lo para o grupo de recursos que você criou.

    ![Criar grupo de recursos](./media/resource-group-template-deploy-portal/select-existing-group.png)

    Como alternativa, você pode optar por criar um grupo de recursos quando Implantando seus recursos. Selecione **Criar novo** e dê um nome para o grupo de recursos.

    ![Criar novo grupo de recursos](./media/resource-group-template-deploy-portal/select-new-group.png)

4. Começa a sua implantação. A implantação pode levar alguns minutos. Quando a implantação tiver terminado, você verá uma notificação.

    ![notificação do modo de exibição](./media/resource-group-template-deploy-portal/view-notification.png)

5. Após implantar seus recursos, você pode adicionar mais recursos ao grupo de recursos usando o comando **Adicionar** na lâmina de grupo de recursos.

    ![adicionar recurso](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implantar recursos de modelo personalizado

Se você quiser executar uma implantação, mas não usar qualquer um dos modelos no mercado, você pode criar um modelo personalizado que define a infraestrutura para sua solução. Para saber mais sobre a criação de modelos, consulte [modelos de coautoria Gerenciador de recursos do Azure](resource-group-authoring-templates.md).

1. Para implantar um modelo personalizado por meio do portal, selecione **novo**e iniciar a pesquisa para **Implantação do modelo** até que você pode selecioná-lo entre as opções.

    ![implantação de modelo de pesquisa](./media/resource-group-template-deploy-portal/search-template.png)

2. Selecione o **Modelo de implantação** dos recursos disponíveis.

    ![Selecione implantação de modelo](./media/resource-group-template-deploy-portal/select-template.png)

3. Depois de iniciar a implantação do modelo, abra o modelo em branco que está disponível para a personalização.

    ![Criar modelo](./media/resource-group-template-deploy-portal/show-custom-template.png)

    No editor, adicione a sintaxe JSON que define os recursos que você deseja implantar. Selecione **Salvar** quando terminar. Para obter orientações sobre como escrever a sintaxe JSON, consulte [explicação passo a passo de modelo do Gerenciador de recursos](resource-manager-template-walkthrough.md).

    ![Editar modelo](./media/resource-group-template-deploy-portal/edit-template.png)

4. Ou, você pode selecionar um modelo preexistente de [modelos de início rápido Azure](https://azure.microsoft.com/documentation/templates/). Esses modelos são contribuiu pela comunidade. Eles abordam muitos cenários comuns e alguém adicionou um modelo semelhante ao que você está tentando implantar. Você pode pesquisar os modelos para encontrar algo que corresponde ao seu cenário.

    ![Selecione o modelo de início rápido](./media/resource-group-template-deploy-portal/select-quickstart-template.png)

    Você pode exibir o modelo selecionado no editor.

5. Depois de fornecer todos os outros valores, selecione **criar** para implantar o modelo. 

    ![implantar o modelo](./media/resource-group-template-deploy-portal/create-custom-deploy.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Implantar recursos a partir de um modelo salvo à sua conta

O portal permite que você salvar um modelo de sua conta do Azure e reimplantá-la mais tarde. Para obter mais informações sobre como trabalhar com esses salvo modelos, [começar a usar modelos particulares no portal do Azure](./marketplace-consumer/mytemplates-getstarted.md).

1. Para localizar seus modelos salvos, selecione **Procurar** > **modelos**.

    ![procurar modelos](./media/resource-group-template-deploy-portal/browse-templates.png)

2. Na lista de modelos salvos à sua conta, selecione aquele que você deseja trabalhar em.

    ![modelos de salvos](./media/resource-group-template-deploy-portal/saved-templates.png)

3. Selecione **implantar** reimplantar este modelo salvo.

    ![implantar o modelo salvo](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Próximas etapas

- Para exibir logs de auditoria, consulte [operações com o Gerenciador de recursos de auditoria](resource-group-audit.md).
- Para solucionar erros de implantação, consulte [implantações de grupo de recursos de solução de problemas com portal Azure](resource-manager-troubleshoot-deployments-portal.md).
- Para recuperar um modelo de uma implantação ou grupo de recursos, consulte [Gerenciador de recursos do Azure exportar modelo de recursos existentes](resource-manager-export-template.md).





