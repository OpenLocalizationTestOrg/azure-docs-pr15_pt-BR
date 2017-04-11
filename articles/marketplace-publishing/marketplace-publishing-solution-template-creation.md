<properties
   pageTitle="Guia para criar um modelo de solução do Marketplace | Microsoft Azure"
   description="Instruções detalhadas de como criar, certificar e implantar um modelo de solução de imagem de Multi-máquina virtual para compra no Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="07/27/2016"
      ms.author="hascipio; v-divte" />

# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Guia para criar um modelo de solução do Azure Marketplace
Depois de concluir a etapa 1, [registro e criação de conta][link-acct-creation], podemos guiado você sobre a criação de um modelo de solução compatível com o Azure na [técnicos pré-requisitos para criar um modelo de solução](marketplace-publishing-solution-template-creation-prerequisites.md). Agora podemos apresentará as etapas para criar um modelo de solução para várias VMs no [Portal de publicação] [ link-pubportal] do Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Criar sua oferta de modelo de solução no Portal de publicação
Vá para [https://publish.windowsazure.com](http://publish.windowsazure.com). Quando entrar pela primeira vez no [Portal de publicação](https://publish.windowsazure.com/), use a mesma conta com a qual foi registrada perfil de vendedor da sua empresa. Posteriormente, você pode adicionar qualquer funcionário da sua empresa como co-administrador no Portal de publicação.

### <a name="1-select-solution-templates"></a>1. Selecione "Modelos de solução"

  ![desenho][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Crie um novo modelo de solução

  ![desenho][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. iniciar com topologias
Um modelo de solução é um "parent" a todas as suas topologias. Você pode definir várias topologias de um modelo de oferta/solução. Quando uma oferta será enviada para preparação, ele é enviado todas as suas topologias. Siga as etapas abaixo para definir sua oferta:     

- Criar uma topologia: "Identificador de topologia" é geralmente o nome da topologia para o modelo de solução. O identificador de topologia é usado na URL conforme mostrado abaixo:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/ {PublisherNamespace} / {OfferIdentifier} {TopologyIdentifier}

  Azure Portal: https://portal.azure.com/#gallery/ {PublisherNamespace}. {OfferIdentifier} {TopologyIdentifier}

- Adicione uma nova versão.

### <a name="4-get-your-topology-versions-certified"></a>4. obter suas versões de topologia certificadas
Carregar um arquivo zip que contém todos os arquivos necessários para provisionar essa versão específica da topologia. Este arquivo zip deve conter o seguinte:

- arquivo de *mainTemplate.json* e *createUiDefinition.json* na sua pasta raiz.
- Quaisquer modelos vinculados e scripts necessários.

  > [AZURE.TIP] Enquanto os desenvolvedores trabalham sobre como criar a solução topologias de modelo e obtendo-os certificados, os negócios, departamentos de marketing e/ou legais da sua empresa podem trabalhar no conteúdo legal e de marketing.

## <a name="next-steps"></a>Próximas etapas
Agora que você criou seu modelo de solução e carregado o arquivo zip, siga as instruções do [guia de conteúdo de marketing Marketplace](marketplace-publishing-push-to-staging.md) antes de enviar a oferta para teste. Para ver o conjunto completo de marketplace publicando artigos, visite [Introdução: como publicar uma oferta do Azure Marketplace](marketplace-publishing-getting-started.md).

Você também pode estar interessado nestes artigos relacionados:

- Imagens de máquina virtual: [Sobre imagens de máquinas virtuais no Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
- Extensões de máquina virtual: [agente de máquina virtual e visão geral de extensões de máquina virtual](https://msdn.microsoft.com/library/azure/dn832621.aspx) e [extensões de máquina virtual do Azure e recursos](https://msdn.microsoft.com/library/azure/dn606311.aspx)
- Gerenciador de recursos do Azure: [habilitados BRAÇO Azure](../resource-group-authoring-templates.md) e [exemplos de modelo BRAÇO simples](https://github.com/rjmax/ArmExamples)
- Conta de armazenamento acelera: [como Monitor para otimização de conta de armazenamento](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) e [armazenamento de Premium](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
