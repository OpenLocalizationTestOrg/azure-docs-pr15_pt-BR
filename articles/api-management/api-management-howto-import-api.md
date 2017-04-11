<properties 
    pageTitle="Conceitos-chave Gerenciamento de API" 
    description="Saiba mais sobre APIs, produtos, funções, grupos e outros conceitos-chave Gerenciamento de API." 
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

# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>Como importar a definição de uma API com operações de gerenciamento de API do Azure

Em gerenciamento de API, novas APIs podem ser criados e as operações adicionadas manualmente ou API pode ser importados juntamente com as operações em uma etapa.

APIs e suas operações podem ser importadas usando os seguintes formatos.

-   WADL
-   Swagger

Este guia mostra como criar uma nova API e importar suas operações em uma etapa. Para obter informações sobre como criar manualmente uma API e adicionando operações, consulte [como criar APIs][] e [como adicionar operações para uma API][].

## <a name="import-api"> </a>Importar uma API

APIs são criadas e configurados no portal do publisher. Para acessar o portal do publisher, clique em **Gerenciar** no Portal de clássico do Azure para o serviço de gerenciamento de API. Se você ainda não tiver criado uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][] do tutorial de [Introdução ao gerenciamento de API do Azure][] .

![Portal do Publisher][api-management-management-console]

Clique em **APIs** no menu de **Gerenciamento de API** à esquerda e, em seguida, clique em **Importar API**.

![API de importação][api-management-import-apis]

A janela de **Importação API** tem três guias que correspondem às três maneiras de fornecer a especificação de API.

-   **Da área de transferência** permite que você cole a especificação de API na caixa de texto designado.
-   **Do arquivo** permite que você procure e selecione o arquivo que contém a especificação de API.
-   **URL do** permite que você forneça a URL para a especificação da API.

![Formato de importação API][api-management-import-api-clipboard]

Depois de fornecer a especificação de API, use os botões de opção à direita para indicar o formato de especificação. Há suporte para os seguintes formatos.

-   WADL
-   Swagger

Em seguida, digite um **sufixo de URL da Web API**. Isso é acrescentado à URL base para seu serviço de gerenciamento de API. A base URL é comum para todas as APIs hospedadas em cada instância de um serviço de gerenciamento de API. Gerenciamento de API distingue APIs pelo seu sufixo e, portanto, o sufixo deve ser exclusivo para cada API em uma instância de serviço de gerenciamento de API específica.

Depois que todos os valores são inseridos, clique em **Salvar** para criar a API e as operações associadas. 

>[AZURE.NOTE] Para um tutorial de importar uma calculadora básica API no formato de Swagger, consulte [Gerenciar sua primeira API no gerenciamento de API do Azure](api-management-get-started.md).

## <a name="export-api"></a> Exportar uma API

Além de importar novas APIs, você pode exportar as definições de suas APIs do portal do publisher. Para fazer isso, clique em **Exportar API** a partir da **guia Resumo** da sua **API**.

![API de exportação][api-management-export-api]

APIs podem ser exportados usando WADL ou Swagger. Selecione o formato desejado, clique em **Salvar**e escolha o local em que deseja salvar o arquivo.

![Formato de API de exportação][api-management-export-api-format]

## <a name="next-steps"> </a>Próximos passos

Depois que uma API é criada e as operações de importação, você pode revisar e configurar configurações adicionais, adicione a API a um produto e publicá-lo para que fique disponível para os desenvolvedores. Para obter mais informações, consulte os seguintes guias.

-   [Como definir configurações de API][]
-   [Como criar e publicar um produto][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Introdução ao gerenciamento de API do Azure]: api-management-get-started.md
[Criar uma instância de serviço de gerenciamento de API]: api-management-get-started.md#create-service-instance

[Como adicionar operações para uma API]: api-management-howto-add-operations.md
[Como criar e publicar um produto]: api-management-howto-add-products.md
[Como criar APIs]: api-management-howto-create-apis.md
[Como definir configurações de API]: api-management-howto-create-apis.md#configure-api-settings
