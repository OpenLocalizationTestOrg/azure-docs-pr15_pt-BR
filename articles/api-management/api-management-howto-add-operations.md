<properties 
    pageTitle="Como adicionar operações para uma API no gerenciamento de API do Azure | Microsoft Azure" 
    description="Saiba como adicionar operações para uma API no gerenciamento de API do Azure." 
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

# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>Como adicionar operações para uma API no gerenciamento de API do Azure

Antes de uma API no gerenciamento de API pode ser usada, operações devem ser adicionadas. Este guia mostra como adicionar e configurar diferentes tipos de operações para uma API de gerenciamento de API.

## <a name="add-operation"> </a>Adicionar uma operação

Operações são adicionadas e configuradas para uma API no portal do publisher. Para acessar o portal do publisher, clique em **Gerenciar** no Portal de clássico do Azure para o serviço de gerenciamento de API.

![Portal do Publisher][api-management-management-console]

>Se você ainda não tiver criado uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][] do tutorial de [Introdução ao gerenciamento de API do Azure][] .

Selecione a API desejada no portal do fornecedor e selecione a guia de **operações** . 

![Operações][api-management-operations]

Clique em **Adicionar operação** para adicionar uma nova operação. A **nova operação** será exibido e na guia **assinatura** será selecionada por padrão.

![Adicionar operação][api-management-add-operation]

Especifique o **verbo HTTP** escolhendo na lista suspensa.

![Método HTTP][api-management-http-method]

<a name="url-template"></a>

Defina o modelo de URL, digitando em um fragmento de URL que consiste em um ou mais segmentos de caminho de URL e zero ou mais parâmetros de cadeia de caracteres de consulta. O modelo de URL, acrescentado à URL base da API, identifica uma única operação de HTTP. Ele pode conter um ou mais nomeado variáveis partes que são identificadas por chaves. Estas partes variáveis são chamados parâmetros de modelo e são atribuídas dinamicamente valores extraídas de URL de solicitação quando a solicitação está sendo processada pela plataforma de gerenciamento de API.

![Modelo de URL][api-management-url-template]

<a name="rewrite-url-template"></a>

Se desejar, especifique o **modelo de reconfiguração de URL**. Isso permite que você usar o modelo de URL padrão para processar solicitações de entrada em front-end, ao chamar back-end através de uma URL convertido de acordo com o modelo de reconfiguração. Parâmetros de modelo a partir do modelo de URL devem ser usados no modelo reconfiguração. O exemplo a seguir mostra como conteúdo tipo codificado como segmento de caminho no serviço da web do exemplo anterior pode ser fornecido como um parâmetro de consulta na API publicado via a plataforma de gerenciamento de API usando os modelos de URL.

![Reconfiguração de modelo de URL][api-management-url-template-rewrite]

Os chamadores para a operação usará o formato `/customers?customerid=ALFKI` e isso será mapeado para `/Customers('ALFKI')` quando o serviço de back-end é chamado.


Nome para **exibição** e uma **Descrição** fornecem uma descrição da operação e são usados para fornecer documentação para os desenvolvedores usando essa API no portal do desenvolvedor.

![Descrição][api-management-description]

A descrição da operação pode ser especificada como texto sem formatação ou HTML na caixa de texto **Descrição** .

## <a name="operation-caching"> </a>Operação cache

Cache de resposta reduz a latência considerada pelos consumidores de API, reduz o consumo de largura de banda e diminui a carga na web HTTP Implementando a API do serviço. 

Para rapidamente e facilmente habilitar armazenamento em cache para a operação, selecione a guia **cache** e marque a caixa de seleção **Habilitar** .

![Armazenamento em cache][api-management-caching-tab]

**Duração** Especifica o período de tempo durante o qual a resposta de operação permanece no cache. O valor padrão é 3600 segundos ou 1 hora.

Chaves de cache são usadas para diferenciar respostas para que a resposta correspondente a cada chave de cache diferentes terá seu próprio valor em cache separada. Opcionalmente, insira os parâmetros de sequência de consulta específica e/ou cabeçalhos HTTP a ser usado na computação valores de chave de cache nas caixas de texto **variar por parâmetros de cadeia de caracteres de consulta** e **variar por cabeçalhos** respectivamente. Quando nenhum são URL solicitação especificado, completo e os seguintes valores de cabeçalho HTTP são usados na geração de chave de cache: **Aceitar** e **Aceitar conjunto de caracteres**.

>Para obter mais informações sobre cache e políticas de cache, veja [como os resultados da operação de cache no gerenciamento de API do Azure][].


## <a name="request-parameters"> </a>Parâmetros de solicitação

Parâmetros de operação são gerenciados na guia Parâmetros. Parâmetros especificados no **Modelo de URL** na guia **assinatura** são adicionados automaticamente e podem ser alterados apenas por edição do modelo de URL. Parâmetros adicionais podem ser inseridos manualmente.

Para adicionar um novo parâmetro de consulta, clique em **Adicionar parâmetro de consulta** e insira as seguintes informações:

-   **Nome** - nome do parâmetro.
-   **Descrição** - uma breve descrição do parâmetro (opcional).
-   **Tipo** - tipo de parâmetro, selecionado no menu suspenso para baixo.
-   **Valores** - valores que pode ser atribuído a este parâmetro. Um dos valores pode ser marcado como padrão (opcional).
-   **Obrigatório** - tornar o parâmetro obrigatória marcando a caixa de seleção. 

![Parâmetros de solicitação][api-management-request-parameters]

## <a name="request-body"> </a>Solicitar corpo

Se a operação permite (por exemplo, colocar, POSTAGEM) e requer um corpo que você pode fornecer um exemplo de-lo em todos os formatos de representação compatível (por exemplo, json, XML). 

>O corpo da solicitação é usado para fins de documentação somente e não é validado.

Para inserir o corpo de uma solicitação, alternar para a guia de **corpo** .

Clique em **Adicionar representação**, comece a digitar o nome de tipo de conteúdo desejado (por exemplo, aplicativo/json), selecione-o na lista suspensa e cole o exemplo de corpo de solicitação desejado no formato selecionado na caixa de texto. 

![Corpo da solicitação][api-management-request-body]

Em adicionais para representações, você também pode especificar uma descrição opcional de texto na caixa de texto **Descrição** .

## <a name="responses"> </a>Respostas

É uma boa prática fornecem exemplos de respostas para todos os códigos de status que pode produzir a operação. Cada código de status pode ter mais de um exemplo de corpo de resposta, um para cada um dos tipos de conteúdo com suporte. 

Para adicionar uma resposta, clique em **Adicionar** e comece a digitar o código de status desejado. Neste exemplo é o código de status **200 Okey**. Depois que o código é exibido na lista suspensa, selecione-o e o código de resposta é criado e adicionado à sua operação.

![Código de resposta][api-management-response-code]

Clique em **Adicionar representação**, comece a digitar o nome do tipo de conteúdo desejado (por exemplo, aplicativo/json) e, em seguida, selecione-o no menu suspenso para baixo.

![Tipo de conteúdo do corpo][api-management-response-body-content-type]

Cole o exemplo de corpo de resposta no formato selecionado na caixa de texto. 

![Corpo de resposta][api-management-response-body]

Se desejar, adicione uma descrição opcional na caixa de texto **Descrição** .

Quando a operação é configurada, clique em **Salvar**.


## <a name="next-steps"> </a>Próximos passos

Depois que as operações são adicionadas a uma API, a próxima etapa é associar a um produto de API e publicá-lo para que os desenvolvedores podem chamar suas operações.

-   [Como criar e publicar um produto][]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Introdução ao gerenciamento de API do Azure]: api-management-get-started.md
[Criar uma instância de serviço de gerenciamento de API]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[Como criar e publicar um produto]: api-management-howto-add-products.md
[Como os resultados da operação de cache no gerenciamento de API do Azure]: api-management-howto-cache.md