<properties
    pageTitle="Proteger sua API com gerenciamento de API Azure | Microsoft Azure"
    description="Saiba como proteger sua API com cotas e a otimização de políticas (limite de taxa)."
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
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Proteger sua API com limites de taxa usando o gerenciamento de API do Azure

Este guia mostra como é fácil adicionar proteção para sua API de back-end configurando políticas de cota e de limite de taxa com gerenciamento de API do Azure.

Neste tutorial, você irá criar um produto de "Avaliação gratuita" API que permite que os desenvolvedores fazer chamadas de até 10 por minuto e até no máximo 200 chamadas por semana para sua API usando a [taxa de chamada de limite por assinatura](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) e políticas de [cota de uso de conjunto por assinatura](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) . Em seguida, você publicar a API e testar a política de limite de taxa.

Para cenários limitação mais avançados usando as políticas de [Taxa-limite-por-chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [cota por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) , consulte [solicitação avançada a otimização com gerenciamento de API do Azure](api-management-sample-flexible-throttling.md).

## <a name="create-product"> </a>Para criar um produto

Nesta etapa, você criará um produto de avaliação gratuita que não exigem a aprovação de assinatura.

>[AZURE.NOTE] Se você já tiver um produto configurado e deseja usá-la para este tutorial, você pode pular para [Configurar políticas de cota e de limite de taxa de chamadas][] e siga o tutorial a partir daí usando seu produto no lugar do produto de avaliação gratuita.

Para começar, clique em **Gerenciar** sob o Azure clássico de seu serviço de gerenciamento de API. Isso o leva para o portal de gerenciamento de API do publisher.

![Portal do Publisher][api-management-management-console]

>Se você ainda não tiver criado uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][] do tutorial de [Gerenciar sua primeira API no gerenciamento de API do Azure][] .

Clique em **produtos** do menu **Management de API** à esquerda para exibir a página de **produtos** .

![Adicionar produto][api-management-add-product]

Clique em **Adicionar produto** para exibir a caixa de diálogo **Adicionar novo produto** .

![Adicionar novo produto][api-management-new-product-window]

Na caixa **título** , digite **Avaliação gratuita**.

Na caixa **Descrição** , digite o seguinte texto:  **assinantes poderão executar chamadas de 10/minutos até no máximo 200 chamadas/semana após o qual acesso negado.**

Produtos de gerenciamento de API podem ser protegidos ou abrir. Produtos protegidos devem estar inscrito no antes que possam ser usadas. Produtos abertos podem ser usados sem uma assinatura. Certifique-se de que **exige assinatura** está selecionado para criar um produto protegido que exige uma assinatura. Esta é a configuração padrão.

Se você quiser um administrador para revisar e aceitar ou rejeitar tentativas de assinatura para este produto, selecione **exigir aprovação de assinatura**. Se a caixa de seleção não estiver selecionada, tentativas de assinatura será aprovação automática. Neste exemplo, assinaturas são aprovadas automaticamente, portanto, não marque a caixa.

Para permitir que as contas de desenvolvedor para assinar várias vezes para o novo produto, selecione a caixa de seleção **Permitir várias inscrições simultâneas** . Este tutorial não utilizam várias assinaturas simultâneas, portanto deixá-la desmarcada.

Depois de todos os valores são inseridos, clique em **Salvar** para criar o produto.

![Produto adicionado][api-management-product-added]

Por padrão, os novos produtos são visíveis para os usuários do grupo de **administradores** . Vamos adicionar o grupo de **desenvolvedores** . Clique **Avaliação gratuita**e clique na guia de **visibilidade** .

>Em gerenciamento de API, os grupos são usados para gerenciar a visibilidade de produtos para os desenvolvedores. Produtos conceder visibilidade a grupos, e os desenvolvedores podem exibir e inscrever-se para os produtos que estão visíveis para os grupos nos quais eles pertencem. Para obter mais informações, consulte [como criar e usar grupos de gerenciamento de API do Azure][].

![Adicionar grupo de desenvolvedores][api-management-add-developers-group]

Marque a caixa de seleção **os desenvolvedores** e, em seguida, clique em **Salvar**.

## <a name="add-api"> </a>Para adicionar uma API do produto

Nesta etapa do tutorial, adicionaremos a API de eco para o novo produto de avaliação gratuita.

>Cada instância do serviço de gerenciamento de API vem pré-configurado com uma API de eco que pode ser usado para testar e saiba mais sobre gerenciamento de API. Para obter mais informações, consulte [Gerenciar sua primeira API no gerenciamento de API do Azure][].

Clique em **produtos** no menu **Gerenciamento de API** à esquerda e, em seguida, clique em **Avaliação gratuita** para configurar o produto.

![Configurar o produto][api-management-configure-product]

Clique em **Adicionar API ao produto**.

![Adicionar API ao produto][api-management-add-api]

Selecione **Eco API**e clique em **Salvar**.

![Adicionar API de eco][api-management-add-echo-api]

## <a name="policies"> </a>Para configurar diretivas de cota e de limite de taxa de chamada

Limites de taxa e cotas são configuradas no editor de política. Clique em **políticas** no menu **Gerenciamento de API** à esquerda. Na lista de **produtos** , clique em **Avaliação gratuita**.

![Política de produto][api-management-product-policy]

Clique em **Adicionar regra** para importar o modelo de política e começar a criar a taxa de políticas de cota e de limite.

![Adicionar política][api-management-add-policy]

Para inserir políticas, posicione o cursor na seção a **entrada** ou **saída** do modelo de política. Diretivas de cota e de limite de taxa são diretivas de entrada, portanto, posicione o cursor no elemento de entrada.

![Editor de política][api-management-policy-editor-inbound]

As duas diretivas que estamos adicionando neste tutorial são as políticas de [taxa de chamada de limite por assinatura](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) e [definir cota de uso por assinatura](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) .

![Instruções de política][api-management-limit-policies]

Depois que o cursor está posicionado no elemento de diretiva de **entrada** , clique na seta ao lado de **taxa de chamada de limite por assinatura** para inserir seu modelo de política.

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**Taxa de chamada de limite por assinatura** podem ser usados no nível do produto e também pode ser usado no API e níveis de nome de operação individual. Neste tutorial, somente as políticas de nível de produto são usadas, exclua os elementos **api** e **operação** do elemento do **limite de taxa** , para que apenas a permanece elemento externa **limite de taxa** , conforme mostrado no exemplo a seguir.

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

No produto avaliação gratuita, a taxa de chamada do máximo permitido é 10 chamadas por minuto, digite **10** como o valor do atributo de **chamadas** e **60** para o atributo de **período de renovação** .

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

Para configurar a política de **definir cota de uso por assinatura** , posicione o cursor imediatamente abaixo do elemento recém-adicionado **limite de taxa** dentro do elemento de **entrada** e clique na seta para a esquerda de **definir cota de uso por assinatura**.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

Porque essa política destina-se também seja no nível do produto, exclua os elementos de nome de **api** e **operação** , conforme mostrado no exemplo a seguir.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

Cotas podem ser baseadas no número de chamadas por intervalo, largura de banda ou ambos. Neste tutorial, podemos não são otimizando com base na largura de banda, portanto, exclua o atributo de **largura de banda** .

    <quota calls="number" renewal-period="seconds">
    </quota>

O produto de avaliação gratuita, a cota é 200 chamadas por semana. Especificar **200** como o valor do atributo de **chamadas** e especifique **604800** como o valor do atributo de **período de renovação** .

    <quota calls="200" renewal-period="604800">
    </quota>

>Intervalos de política são especificados em segundos. Para calcular o intervalo de uma semana, você poderá multiplicar o número de dias (7) pelo número de horas em um dia (24) pelo número de minutos em uma hora (60) pelo número de segundos em um minuto (60): 7 *24* 60 * 60 = 604800.

Quando você terminar de configurar a política, ele deve corresponder o exemplo a seguir.

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />

    </inbound>
    <outbound>

        <base />

        </outbound>
    </policies>

Depois que as regras desejadas estiverem configuradas, clique em **Salvar**.

![Salvar diretiva][api-management-policy-save]

## <a name="publish-product"></a> Para publicar o produto

Agora que o as APIs são adicionadas e as diretivas são configuradas, o produto deve ser publicado para que ele pode ser usado por desenvolvedores. Clique em **produtos** no menu **Gerenciamento de API** à esquerda e, em seguida, clique em **Avaliação gratuita** para configurar o produto.

![Configurar o produto][api-management-configure-product]

Clique em **Publicar**e, em seguida, clique em **Sim, publicá-lo** para confirmar.

![Publicar o produto][api-management-publish-product]

## <a name="subscribe-account"> </a>Para inscrever-se uma conta de desenvolvedor do produto

Agora que o produto for publicado, ele está disponível para ser assinado e usado por desenvolvedores.

>Os administradores de uma instância de gerenciamento de API automaticamente estão inscrito para cada produto. Nesta etapa tutorial, podemos será inscrever-se uma das contas não-administrador desenvolvedor para o produto de avaliação gratuita. Se sua conta de desenvolvedor fizer parte da função de administradores, você pode acompanhar junto com essa etapa, mesmo que você já está inscrito.

Clique em **usuários** no menu **Gerenciamento de API** à esquerda e, em seguida, clique no nome da sua conta de desenvolvedor. Neste exemplo, estamos usando a conta de desenvolvedor **Terra Gragg** .

![Configurar developer][api-management-configure-developer]

Clique em **Adicionar a assinatura**.

![Adicionar uma assinatura][api-management-add-subscription-menu]

Selecione a **Avaliação gratuita**e clique em **assinar**.

![Adicionar uma assinatura][api-management-add-subscription]

>[AZURE.NOTE] Neste tutorial, várias assinaturas simultâneas não estão ativadas para o produto de avaliação gratuita. Se fossem, você poderia ser solicitado a nome da assinatura, conforme mostrado no exemplo a seguir.

![Adicionar uma assinatura][api-management-add-subscription-multiple]

Após clicar em **assinar**, o produto é exibida na lista de **assinaturas** do usuário.

![Assinatura adicionada][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Uma operação de chamadas e testar o limite de taxa

Agora que o produto de avaliação gratuita está configurado e publicado, podemos algumas operações de chamadas e testar a política de limite de taxa.
Alternar para o portal do desenvolvedor clicando em **portal do desenvolvedor** , no menu superior direito.

![Portal do desenvolvedor][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e clique em **API de eco**.

![Portal do desenvolvedor][api-management-developer-portal-api-menu]

Clique em **Obter o recurso**e clique em **experimentá-lo**.

![Console aberto][api-management-open-console]

Manter o padrão de valores de parâmetro e selecione sua chave de assinatura para o produto de avaliação gratuita.

![Chave de assinatura][api-management-select-key]

>[AZURE.NOTE] Se você tiver várias assinaturas, certifique-se de selecionar a chave para **Avaliação gratuita**ou então as políticas que foram configuradas nas etapas anteriores não estará em vigor.

Clique em **Enviar**e, em seguida, exiba a resposta. Observe o **status de resposta** de **200 Okey**.

![Resultados da operação][api-management-http-get-results]

Clique em **Enviar** uma taxa maior do que a política de limite de taxa de 10 chamadas por minuto. Depois que a política de limite de taxa for excedida, será retornado um status de resposta de **429 muito muitas solicitações** .

![Resultados da operação][api-management-http-get-429]

O **conteúdo de resposta** indica o intervalo restante antes de tentativas será bem sucedidas.

Quando a política de limite de taxa de 10 chamadas por minuto está em vigor, chamadas subsequentes falhará até 60 segundos decorridos da primeira das 10 chamadas bem-sucedido ao produto antes do limite de taxa foi excedido. Neste exemplo, o intervalo restante é 54 segundos.

## <a name="next-steps"> </a>Próximos passos

-   Assista a uma demonstração de definir cotas e limites de taxa no vídeo a seguir.

> [AZURE.VIDEO rate-limits-and-quotas]


[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Gerenciar sua primeira API no gerenciamento de API do Azure]: api-management-get-started.md
[Como criar e usar grupos de gerenciamento de API do Azure]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Criar uma instância de serviço de gerenciamento de API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configurar diretivas de cota e de limite de taxa de chamada]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota
