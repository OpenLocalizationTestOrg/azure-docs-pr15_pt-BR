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
    ms.topic="hero-article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

#<a name="what-is-api-management"></a>O que é gerenciamento de API?

Gerenciamento de API ajuda as organizações publicar APIs para externo, parceiros e desenvolvedores internos para desbloquear o potencial de seus dados e serviços. Empresas em todos os lugares estão pretendendo para estender suas operações como uma plataforma digital, criando novos canais, localizando novos clientes e conduzir a mais profundo envolvimento com existentes. Gerenciamento de API fornece as competências para garantir um programa de API bem-sucedida pelo contrato de desenvolvedor, obtenção de informações de negócios, análise, segurança e proteção.

Assista a este vídeo para obter uma visão geral do gerenciamento de API do Azure e aprenda a usar o gerenciamento de API para adicionar vários recursos à sua API, incluindo o controle de acesso, a limitação de taxa, monitoramento, log de eventos e cache de resposta, com o mínimo de trabalho de sua parte.

> [AZURE.VIDEO azure-api-management-overview]

Para usar o gerenciamento de API, os administradores podem criar APIs. Cada API consiste em uma ou mais operações, e cada API pode ser adicionado a um ou mais produtos. Para usar uma API, desenvolvedores assine um produto que contém essa API e, em seguida, ele podem chamar operação da API, sujeitos a quaisquer diretivas de uso que pode estar em vigor.

Este tópico fornece uma visão geral dos conceitos-chave Gerenciamento de API.

>[AZURE.NOTE] Para obter mais informações, consulte o [gerenciamento de API baseado em nuvem: controlar o Power de APIs](http://j.mp/ms-apim-whitepaper) white paper PDF. Este white paper introdutório sobre gerenciamento de API pelo CITO Research abrange: 
>
> - Os desafios e requisitos de API comuns
>     - Separação APIs e apresentar fachadas
>     - Colocar os desenvolvedores em funcionamento rapidamente
>     - Protegendo o acesso
>     - Análise e métricas
> - Obtendo controle e ideias com uma plataforma de gerenciamento de API
> - Usando as soluções de nuvem vs no local
> - Gerenciamento de API do Azure

## <a name="apis"> </a>APIs e operações

APIs são a base de uma instância de serviço de gerenciamento de API. Cada API representa um conjunto de operações disponíveis para os desenvolvedores. Cada API contém uma referência para o serviço de back-end que implementa a API e seu mapa de operações às operações implementada pelo serviço back-end. Operações de gerenciamento de API são altamente configuráveis, com controle sobre URL mapeamento, consulta e parâmetros de caminho, solicitação e resposta conteúdo e cache de resposta de operação. Limite de taxa, cotas e diretivas de restrição de IP também podem ser implementadas no nível de operação individual ou API.

Para obter mais informações, consulte [como criar APIs][] e [como adicionar operações para uma API][].


## <a name="products"></a> Produtos

Produtos são como APIs forem reproduzidos para desenvolvedores. Produtos de gerenciamento de API tem um ou mais APIs e são configurados com um título, descrição e termos de uso. Produtos podem ser **aberto** ou **protegido**. Produtos protegidos devem estar inscrito no antes que eles podem ser usados, ao abrir produtos podem ser usados sem uma assinatura. Quando um produto está pronto para uso pelos desenvolvedores podem ser publicado. Depois de publicado, pode ser visualizado (e no caso de produtos protegidos assinado) por desenvolvedores. Aprovação de assinatura está configurada no nível do produto e pode exigir aprovação do administrador ou ser aprovada automaticamente.

Grupos são usados para gerenciar a visibilidade de produtos para os desenvolvedores. Produtos conceder visibilidade a grupos, e os desenvolvedores podem exibir e inscrever-se para os produtos que estão visíveis para os grupos nos quais eles pertencem. 

Para obter mais informações, consulte [como criar e publicar um produto][] e vídeo a seguir.

> [AZURE.VIDEO using-products]

## <a name="groups"></a> Grupos

Grupos são usados para gerenciar a visibilidade de produtos para os desenvolvedores. Gerenciamento de API tem os seguintes grupos de sistema imutável.

-   **Os administradores** - os administradores de assinatura Azure são membros deste grupo. Os administradores gerenciar instâncias de serviço de gerenciamento de API, criando as APIs, operações e produtos que são usados por desenvolvedores.
-   **Desenvolvedores** - portal do desenvolvedor autenticado usuários enquadram este grupo. Os desenvolvedores são os clientes que criar aplicativos usando seu APIs. Os desenvolvedores têm acesso ao portal do desenvolvedor e criarem aplicativos que as operações de uma API de chamadas.
-   **Convidados** - usuários de portal do desenvolvedor não autenticados, como clientes potenciais visitar o portal do desenvolvedor de uma instância de gerenciamento de API se encaixam este grupo. Elas podem ser concedidas determinado acesso somente leitura, como a capacidade de exibir APIs, mas não os chamará.

Além desses grupos de sistema, administradores podem criar grupos personalizados ou [aproveitar grupos externos no associado locatários do Active Directory do Azure](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group). Personalizar e grupos externos podem ser usados junto com os grupos do sistema no dando a visibilidade de desenvolvedores e acesso a produtos de API. Por exemplo, você poderia criar um grupo personalizado para os desenvolvedores afiliados com uma organização de parceiro específico e permitir acesso às APIs de um produto que contém apenas APIs relevantes. Um usuário pode ser um membro de mais de um grupo.

Para obter mais informações, consulte [como criar e usar grupos][].

## <a name="developers"></a> Desenvolvedores

Desenvolvedores representam as contas de usuário em uma instância de serviço de gerenciamento de API. Os desenvolvedores podem ser criados ou convidados a participar por administradores ou ela podem se inscrever no [portal do desenvolvedor][]do. Cada desenvolvedor é um membro de um ou mais grupos e pode ser assinar os produtos que conceder visibilidade a esses grupos.

Quando os desenvolvedores assina um produto que eles recebem a chave primária e secundária para o produto. Esta chave é usada ao fazer chamadas para APIs do produto.

Para obter mais informações, consulte [como criar ou convidar os desenvolvedores][] e [como associar grupos com desenvolvedores][].

## <a name="policies"></a> Políticas

As políticas são um poderoso recurso de gerenciamento de API que permitem que o publisher alterar o comportamento da API por meio de configuração. Políticas são um conjunto de instruções que são executadas sequencialmente na solicitação ou resposta de uma API. Instruções populares incluem conversão de formato de XML para JSON e a limitação de taxa de chamada para restringir a quantidade de chamadas de entrada de um desenvolvedor e muitos outras diretivas estão disponíveis.

Expressões de política podem ser usadas como valores de atributo ou valores de texto em qualquer uma das políticas de gerenciamento de API, a menos que a política especifica o contrário. Algumas políticas como as políticas de [fluxo de controle](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) e [Defina variável](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable) são baseadas em expressões de política. Para obter mais informações, consulte [Advanced políticas](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies), [expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx)e assista a este vídeo.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

Para obter uma lista completa de políticas de gerenciamento de API, consulte [referência de política][]. Para obter mais informações sobre como usar e configurar políticas, consulte [políticas de gerenciamento de API][]. Para um tutorial sobre como criar um produto com taxa políticas de cota e de limite, consulte [como criar e configurar configurações avançadas de produto][]. Para ver uma demonstração, consulte o vídeo a seguir.

> [AZURE.VIDEO rate-limits-and-quotas]

## <a name="developer-portal"></a> Portal do desenvolvedor

O portal do desenvolvedor é onde os desenvolvedores podem saber mais sobre as operações de APIs, o modo de exibição e a chamada e inscrever-se em produtos. Clientes potenciais podem visitar o portal do desenvolvedor, APIs e operações de exibir e inscrever-se. A URL do seu portal do desenvolvedor está localizada no painel no Portal de clássico do Azure para a sua instância de serviço de gerenciamento de API.

Você pode personalizar a aparência do seu portal do desenvolvedor adicionando conteúdo personalizado, personalização do estilo, e adicione a sua marca.

## <a name="api-management-and-the-api-economy"></a>Gerenciamento de API e a economia de API

Para saber mais sobre o gerenciamento de API, assista a apresentação a seguir da conferência Microsoft Ignite 2015.

> [AZURE.VIDEO microsoft-ignite-2015-azure-api-management-and-the-api-economy]

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Portal do desenvolvedor]: #developer-portal

[Como criar APIs]: api-management-howto-create-apis.md
[Como adicionar operações para uma API]: api-management-howto-add-operations.md
[Como criar e publicar um produto]: api-management-howto-add-products.md
[Como criar e usar grupos]: api-management-howto-create-groups.md
[Como associar grupos desenvolvedores]: api-management-howto-create-groups.md#associate-group-developer
[Como criar e configurar configurações avançadas de produto]: api-management-howto-product-with-rules.md
[Como criar ou convidar desenvolvedores]: api-management-howto-create-or-invite-developers.md
[Referência de política]: api-management-policy-reference.md
[Políticas de gerenciamento de API]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance



 
