<properties
    pageTitle="Adicionar o cache para melhorar o desempenho em gerenciamento de API do Azure | Microsoft Azure"
    description="Saiba como melhorar a latência, o consumo de largura de banda e a carga de serviço web para chamadas de serviço de gerenciamento de API."
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

# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Adicionar o cache para melhorar o desempenho em gerenciamento de API do Azure

Operações de gerenciamento de API podem ser configuradas para armazenamento em cache de resposta. Cache de resposta significativamente pode reduzir a latência de API, consumo de largura de banda e carga de serviço de dados que não mudam frequentemente da web.

Este guia mostra como adicionar o cache de resposta para sua API e configurar políticas para as operações de eco API de amostra. Você pode chamar a operação a partir do portal do desenvolvedor para verificar o cache em ação.

>[AZURE.NOTE] Para obter informações sobre itens de cache pela chave usando expressões de política, consulte [personalizado cache no gerenciamento de API do Azure](api-management-sample-cache-by-key.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir as etapas neste guia, você deve ter uma instância de serviço de gerenciamento de API com uma API e um produto configurado. Se você ainda não tiver criado uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][] do tutorial de [Introdução ao gerenciamento de API do Azure][] .

## <a name="configure-caching"> </a>Configurar uma operação para armazenamento em cache

Nesta etapa, você irá examinar as configurações de cache da operação de **Recurso obter (em cache)** da amostra API de eco.

>[AZURE.NOTE] Cada instância do serviço de gerenciamento de API vem pré-configurado com uma API de eco que pode ser usado para testar e saiba mais sobre gerenciamento de API. Para obter mais informações, consulte [Introdução ao gerenciamento de API do Azure][].

Para começar, clique em **Gerenciar** no Portal de clássico do Azure para o serviço de gerenciamento de API. Isso o leva para o portal de fornecedor de gerenciamento de API.

![Portal do Publisher][api-management-management-console]

Clique em **APIs** no menu de **Gerenciamento de API** à esquerda e clique em **API de eco**.

![API de eco][api-management-echo-api]

Clique na guia **operações** e, em seguida, clique na operação de **Recurso obter (em cache)** na lista de **operações** .

![Operações de API de eco][api-management-echo-api-operations]

Clique na guia **cache** para exibir as configurações de cache para esta operação.

![Guia cache][api-management-caching-tab]

Para habilitar o armazenamento em cache para uma operação, marque a caixa de seleção **Habilitar** . Neste exemplo, o cache é habilitado.

Cada resposta de operação é marcada, com base nos valores nos campos **variar por parâmetros de cadeia de caracteres de consulta** e **variar por cabeçalhos** . Se você quiser armazenar em cache várias respostas com base em parâmetros de cadeia de caracteres de consulta ou cabeçalhos, você pode configurá-los nesses dois campos.

**Duração** Especifica o intervalo de expiração das respostas em cache. Neste exemplo, o intervalo é **3600** segundos, que é equivalente a uma hora.

Usando a configuração de cache neste exemplo, a primeira solicitação para a operação de **Recurso obter (em cache)** retorna uma resposta do serviço de back-end. Esta resposta serão armazenados cache, encaixado pelo especificado cabeçalhos e parâmetros de cadeia de caracteres de consulta. Chamadas subsequentes para a operação, com parâmetros correspondentes, terá a resposta em cache retornada, até que o intervalo de duração do cache expirou.

## <a name="caching-policies"> </a>Revisar as políticas de cache

Nesta etapa, você deve examinar as configurações de cache para a operação de **Recurso obter (em cache)** da amostra API de eco.

Quando as configurações de cache estão definidas para uma operação na guia **cache** , diretivas de cache são adicionadas para a operação. Essas políticas podem ser visualizadas e editadas no editor de política.

Clique em **políticas** no menu **Gerenciamento de API** à esquerda e selecione **eco API / obter recursos (em cache)** na lista suspensa de **operação** .

![Operação de escopo de política][api-management-operation-dropdown]

Isso exibe as políticas para essa operação no editor de política.

![Editor de política de gerenciamento de API][api-management-policy-editor]

A definição de política para essa operação inclui as políticas que definem a configuração de cache que foram revisadas usando a guia **cache** na etapa anterior.

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

>[AZURE.NOTE] As alterações feitas as políticas de cache no editor de política serão refletidas na guia **cache** de uma operação e vice-versa.

## <a name="test-operation"> </a>Uma operação de chamadas e testar o armazenamento em cache

Para ver o cache em ação, podemos chamar a operação a partir do portal do desenvolvedor. Clique em **portal do desenvolvedor** no menu superior direito.

![Portal do desenvolvedor][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e selecione **API de eco**.

![API de eco][api-management-apis-echo-api]

>Se você tiver apenas uma API configurada ou visível para sua conta, clicando em APIs leva você diretamente para as operações para essa API.

Selecione a operação de **Recurso obter (em cache)** e clique em **Console aberto**.

![Console aberto][api-management-open-console]

O console permite invocar operações diretamente do portal do desenvolvedor.

![Console][api-management-console]

Mantenha os valores padrão para **param1** e **param2**.

Selecione a chave desejada na lista suspensa de **chave de assinatura** . Se a sua conta tiver apenas uma assinatura, ela já será selecionada.

Digite **sampleheader:value1** na caixa de texto **solicitações de cabeçalhos** .

Clique em **Obter HTTP** e tome nota dos cabeçalhos de resposta.

Insira **sampleheader:value2** na caixa de texto **solicitações de cabeçalhos** e clique em **HTTP Get**.

Observe que o valor de **sampleheader** ainda é **valor1** na resposta. Experimente alguns valores diferentes e observe que a resposta em cache da primeira chamada é retornada.

Digite **25** no campo **param2** e, em seguida, clique em **HTTP Get**.

Observe que o valor de **sampleheader** na resposta agora é **valor2**. Como os resultados de operação são codificados por cadeia de caracteres de consulta, a resposta em cache anterior não foi retornada.

## <a name="next-steps"> </a>Próximos passos

-   Para obter mais informações sobre políticas de cache, consulte [políticas de cache][] na [referência de política de gerenciamento de API][].
-   Para obter informações sobre itens de cache pela chave usando expressões de política, consulte [personalizado cache no gerenciamento de API do Azure](api-management-sample-cache-by-key.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introdução ao gerenciamento de API do Azure]: api-management-get-started.md

[Referência de política de gerenciamento de API]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Políticas de cache]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Criar uma instância de serviço de gerenciamento de API]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
