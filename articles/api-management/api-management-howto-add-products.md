<properties 
    pageTitle="Como criar e publicar um produto de gerenciamento de API do Azure" 
    description="Aprenda a criar e publicar produtos de gerenciamento de API do Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>Como criar e publicar um produto de gerenciamento de API do Azure

Em gerenciamento de API do Azure, um produto contém um ou mais APIs, bem como uma cota de uso e os termos de uso. Depois que um produto é publicado, os desenvolvedores podem assinar o produto e começar a usar APIs do produto. O tópico fornece um guia para a criação de um produto, adicionando uma API e publicá-lo para desenvolvedores.

## <a name="create-product"> </a>Criar um produto

Operações são adicionadas e configuradas para uma API no portal do publisher. Para acessar o portal do publisher, clique em **Gerenciar** no Portal de clássico do Azure para o serviço de gerenciamento de API.

![Portal do Publisher][api-management-management-console]

>Se você ainda não tiver criado uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][] do tutorial de [Introdução ao gerenciamento de API do Azure][] .

Clique em **produtos** no menu à esquerda para exibir a página de **produtos** e clique em **Adicionar produto**.

![Produtos][api-management-products]

![Novo produto][api-management-add-new-product]

Insira um nome descritivo para o produto no campo **nome** e uma descrição do produto no campo **Descrição** .

Produtos de gerenciamento de API podem ser **aberto** ou **protegido**. Produtos protegidos devem estar inscrito no antes que eles podem ser usados, ao abrir produtos podem ser usados sem uma assinatura. Marque **Exigir assinatura** para criar um produto protegido que exige uma assinatura. Esta é a configuração padrão.

Marque **exigir aprovação de assinatura** se quiser que um administrador para revisar e aceitar ou rejeitar tentativas de assinatura para este produto. Se a caixa estiver desmarcada, tentativas de assinatura será aprovação automática. Para obter mais informações sobre assinaturas, consulte [os assinantes do modo de exibição de um produto][].

Para permitir que contas de desenvolvedor para assinar várias vezes o produto, marque a caixa de seleção **Permitir várias assinaturas** . Se essa caixa não estiver marcada, cada conta de desenvolvedor pode inscrever-se apenas uma única vez ao produto.

![Ilimitado várias assinaturas][api-management-unlimited-multiple-subscriptions]

Para limitar a contagem de várias assinaturas simultâneas, marque a caixa de seleção **limitar o número de assinaturas do simultâneos** e insira o limite de assinatura. No exemplo a seguir, assinaturas simultâneas limitam-se a quatro por conta de desenvolvedor.

![Quatro várias assinaturas][api-management-four-multiple-subscriptions]

Quando todas as novas opções de produto estão configuradas, clique em **Salvar** para criar o novo produto.

![Produtos][api-management-products-page]

>Por padrão novos produtos são não publicados e são visíveis apenas para o grupo de **administradores** .

Para configurar um produto, clique no nome do produto na guia **produtos** .

## <a name="add-apis"> </a>Adicionar APIs para um produto

A página de **produtos** contém quatro links para configuração: **Resumo**, **configurações**, **visibilidade**e **assinantes**. Na guia **Resumo** é onde você pode adicionar APIs e publicar ou cancelar a publicação de um produto.

![Resumo][api-management-new-product-summary]

Antes de publicar seu produto, você precisa adicionar um ou mais APIs. Para fazer isso, clique em **Adicionar API ao produto**.

![Adicionar APIs][api-management-add-apis-to-product]

Selecione as APIs desejadas e clique em **Salvar**.

## <a name="add-description"> </a>Adicionar informações descritivas a um produto

Na guia **configurações** permite fornecer informações detalhadas sobre o produto como sua finalidade, as APIs que fornece acesso a e outras informações úteis. O conteúdo é direcionado os desenvolvedores que serão chamado a API e podem ser escritos em texto sem formatação ou marcação HTML.

![Configurações de produto][api-management-product-settings]

Marque **Exigir assinatura** para criar um produto protegido que exige uma assinatura a ser usado, ou desmarque a caixa de seleção para criar um produto aberto que pode ser chamado sem uma assinatura.

Selecione **exigir aprovação de assinatura** se você quiser aprovar manualmente todas as solicitações de assinatura do produto. Por padrão todas as assinaturas de produto recebem automaticamente.

Para permitir que contas de desenvolvedor para assinar várias vezes o produto, marque a caixa de seleção **Permitir várias assinaturas** e, opcionalmente, especificar um limite. Se essa caixa não estiver marcada, cada conta de desenvolvedor pode inscrever-se apenas uma única vez ao produto.

Opcionalmente, preencha o campo de **termos de uso** descrevendo os termos de uso do produto quais assinantes devem aceitar para poder usar o produto.

## <a name="publish-product"> </a>Publicar um produto

Antes que possa ser chamadas as APIs em um produto, o produto deve ser publicado. Na guia **Resumo** do produto, clique em **Publicar**e clique em **Sim, publicá-lo** para confirmar. Para tornar um produto publicado anteriormente particular, clique em **Cancelar publicação**.

![Publicar o produto][api-management-publish-product]

## <a name="make-visible"> </a>Torne um produto visível para os desenvolvedores

A guia de **visibilidade** permite que você escolha quais funções são capazes de ver o produto no portal do desenvolvedor e inscrever-se ao produto.

![Visibilidade de produto][api-management-product-visiblity]

Para habilitar ou desabilitar a visibilidade de um produto para os desenvolvedores em um grupo, marque ou desmarque a caixa de seleção ao lado do grupo e clique em **Salvar**.

>Para obter mais informações, consulte [como criar e usar grupos para gerenciar contas de desenvolvedor no gerenciamento de API do Azure][].

## <a name="view-subscribers"> </a>Exibir assinantes a um produto

A guia de **assinantes** lista os desenvolvedores que se inscreveu no produto. Os detalhes e configurações para cada desenvolvedor podem ser visualizadas clicando no nome do desenvolvedor. Neste exemplo sem desenvolvedores ainda se inscreveu o produto.

![Desenvolvedores][api-management-developer-list]

## <a name="next-steps"> </a>Próximos passos

Depois que as APIs desejadas são adicionadas e o produto publicado, os desenvolvedores podem assinar o produto e começar a chamar as APIs. Para um tutorial que demonstra esses itens, além de configuração de produtos advanced consulte [como criar e configurar definições de produto avançadas no gerenciamento de API do Azure][].

Para obter mais informações sobre como trabalhar com produtos, consulte o vídeo a seguir.

> [AZURE.VIDEO using-products]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[Assinantes do modo de exibição de um produto]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Introdução ao gerenciamento de API do Azure]: api-management-get-started.md
[Criar uma instância de serviço de gerenciamento de API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[Como criar e usar grupos para gerenciar contas de desenvolvedor no gerenciamento de API do Azure]: api-management-howto-create-groups.md
[Como criar e configurar configurações avançadas de produto no gerenciamento de API do Azure]: api-management-howto-product-with-rules.md 