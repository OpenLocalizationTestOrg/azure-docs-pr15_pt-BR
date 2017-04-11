<properties 
    pageTitle="Recursos relacionados e vinculados na Galeria de bloco" 
    description="Saiba mais sobre recursos relacionados e vinculados que são exibidos na Galeria de bloco do portal do Azure preview." 
    services="azure-portal" 
    documentationCenter="" 
    authors="adamabdelhamed" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="azure-portal" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/16/2015" 
    ms.author="adamab"/>

# <a name="related-and-linked-resources-in-the-tile-gallery"></a>Recursos relacionados e vinculados na Galeria de bloco

A Galeria de bloco permite localizar blocos para um determinado recurso e arraste-os para seu blade atual. Usando a Galeria de peças, você pode criar modos de exibição de gerenciamento que se estendem por recursos. Para qualquer recurso especificado, os recursos relacionados incluem todos os recursos que compartilham o mesmo grupo de recursos como o recurso e todos os recursos vincular ou do recurso.

## <a name="linked-resources-in-azure-resource-manager"></a>Recursos vinculados no Gerenciador de recursos do Azure

Vinculação é um recurso do Gerenciador de recursos do Azure.  Ele permite que você declare relações entre recursos, mesmo se eles não residem no mesmo grupo de recursos. Vinculação não tem nenhum impacto sobre o tempo de execução de seus recursos, sem impacto sobre cobrança e sem afetar o acesso baseado em função.  É simplesmente um mecanismo que você pode usar para representar as relações para que a experiência de ferramentas, como a Galeria de bloco pode fornecer um gerenciamento avançado.  As ferramentas podem inspecionar os links usando os links de API e fornecer gerenciamento de relacionamento personalizado experiências também. 

## <a name="how-do-i-link-my-resources"></a>Como vincular meus recursos?

Quando você cria recursos por meio do portal ou por implantar um modelo através do PowerShell do Azure ou CLI do Azure, os links são criados automaticamente para alguns recursos dependentes. Você pode também programaticamente vincular recursos usando [Vinculadas API REST de recursos](https://msdn.microsoft.com/library/azure/mt238499.aspx) ou declarando as relações no modelo. Para uma discussão completa de trabalhar com recursos vinculados, consulte [recursos de vinculação no Gerenciador de recursos do Azure](../resource-group-link-resources.md).

## <a name="next-steps"></a>Próximas etapas

- Se precisar de uma introdução à criação de modelos do Gerenciador de recursos do Azure, consulte [modelos de criação de páginas](../resource-group-authoring-templates.md).
- Para mergulhar em mais detalhes sobre a criação de links entre recursos, consulte [recursos de vinculação no Gerenciador de recursos do Azure](../resource-group-link-resources.md).
- Para compreender mais sobre como trabalhar com grupos de recursos por meio do portal de visualização, consulte [usando o Portal de visualização do Azure para gerenciar seus recursos Azure](resource-group-portal.md).
