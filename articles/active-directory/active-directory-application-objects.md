<properties
pageTitle="Aplicativo do Active Directory do Azure e objetos de serviço Principal | Microsoft Azure"
description="Uma discussão da relação entre o aplicativo e objetos de serviço no Active Directory do Azure"
documentationCenter="dev-center-name"
authors="bryanla"
manager="mbaldwin"
services="active-directory"
editor=""/>

<tags
ms.service="active-directory"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="identity"
ms.date="08/10/2016"
ms.author="bryanla;mbaldwin"/>

# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Aplicativo e objetos de serviço no Active Directory do Azure
Quando você lê sobre um Azure Active Directory (AD) "aplicativo", não é sempre limpar exatamente o que é conhecido pelo autor. O objetivo deste artigo é para torná-lo mais clara, definindo os aspectos conceituais e concretos de integração de aplicativos do Azure AD, com um exemplo de registro e consentimento para um [aplicativo de vários locatário](active-directory-dev-glossary.md#multi-tenant-application).

## <a name="overview"></a>Visão geral
Um aplicativo do Azure AD é mais amplo do que apenas uma parte do software. É um termo conceitual, consultando não apenas para o software do aplicativo, mas também seu registro (também conhecidos como: configuração de identidade) com o Azure AD, permitindo que ele participe de autenticação e a autorização "conversas" no tempo de execução. Por definição, um aplicativo pode funcionar em uma função de [cliente](active-directory-dev-glossary.md#client-application) (consumindo um recurso), uma função de [servidor de recurso](active-directory-dev-glossary.md#resource-server) (expondo APIs aos clientes) ou ambos. O protocolo de conversa é definido por um [fluxo de concessão de autorização de 2.0 OAuth](active-directory-dev-glossary.md#authorization-grant), com o objetivo de permitir que o recurso de cliente/acesso/proteger os dados de um recurso respectivamente. Agora, vamos dar um nível mais profundo e ver como o modelo de aplicativo do Azure AD representa um aplicativo internamente. 

## <a name="application-registration"></a>Registro de aplicativo
Quando você registra um aplicativo no [Azure portal clássico][AZURE-Classic-Portal], dois objetos são criados no seu locatário do Azure AD: um objeto de aplicativo e um objeto de serviço.

#### <a name="application-object"></a>Objeto de aplicativo
Um aplicativo do Azure AD é *definido* pelo seu e só objeto de aplicativo, que reside no locatário Azure AD onde o aplicativo foi registrado, chamado de locatário "residência" do aplicativo. O objeto de aplicativo fornece informações relacionadas a identidade de um aplicativo e o modelo do qual seu correspondente s principal do serviço são *derivadas* para uso em tempo de execução. 

Você pode considerar o aplicativo como a representação *global* do seu aplicativo (para uso em todos os locatários) e o capital de serviço como a representação *local* (para uso em um locatário específico). A [entidade de aplicativo] do Azure AD Graph[ AAD-Graph-App-Entity] define o esquema de um objeto de aplicativo. Um objeto de aplicativo, portanto, tem uma relação de 1:1 com o aplicativo de software e 1:*n* relação com seu correspondentes *n* serviço principal objeto (s).

#### <a name="service-principal-object"></a>Objeto de serviço
O objeto de serviço define a política e permissões para um aplicativo, fornecendo a base para uma entidade de segurança representar o aplicativo quando estiver acessando recursos em tempo de execução. Gráfico do Azure AD [ServicePrincipal entidade] [ AAD-Graph-Sp-Entity] define o esquema de um objeto de serviço. 

Um objeto principal do serviço é necessário em cada locatário para a qual uma instância de utilização do aplicativo deve ser representada, permitindo o acesso seguro a recursos pertencentes a contas de usuário do locatário. Um aplicativo único locatário terá apenas uma entidade de serviço (em seu locatário inicial). Um [aplicativo Web](active-directory-dev-glossary.md#web-client) do locatário vários também terá um principal de serviço em cada locatário onde um administrador ou usuários de locatário concedeu consentimento, permitindo que ele acesse seus recursos. Seguindo consentimento, o objeto de serviço será ser consultado para solicitações de autorização futuras. 

> [AZURE.NOTE] Quaisquer alterações feitas ao seu objeto de aplicativo, também são refletidas no seu objeto de serviço no inicial locatário do aplicativo apenas (o locatário onde ele foi registrado). Para aplicativos de vários locatários, as alterações ao objeto de aplicativo não são refletidas no serviço objetos dos locatários qualquer consumidor, até o locatário consumidor remove o acesso e concede acesso novamente.

## <a name="example"></a>Exemplo
O diagrama a seguir ilustra a relação entre o objeto de aplicativo de um aplicativo e correspondente objetos, no contexto de um aplicativo de vários locatários de amostra chamado **HR aplicativo**de serviço. Há três locatários do Azure AD neste cenário: 

- **Adatum** - locatário usado pela empresa que desenvolveu o **aplicativo de RH**
- **Contoso** - locatário usado pela organização Contoso, que é um consumidor do **aplicativo de RH**
- **Fabrikam** - locatário usado pela organização Fabrikam, que também consome o **aplicativo de RH**

![Relação entre um objeto de aplicativo e um objeto de serviço](./media/active-directory-application-objects/application-objects-relationship.png)

No diagrama anterior, a etapa 1 é o processo de criação do aplicativo e objetos de serviço no locatário inicial do aplicativo.

Na etapa 2, quando os administradores de Contoso e Fabrikam concluir consentimento, um objeto principal do serviço é criado no locatário do Azure AD da sua empresa e as permissões que o administrador concedido. Observe também que o aplicativo de RH pode ser configurado/criada para permitir consentimento pelos usuários para uso individual.

Na etapa 3, os locatários de consumidor do RH aplicativo (Contoso e Fabrikam) cada tem seu próprio objeto de serviço. Cada representa o uso de uma instância do aplicativo no tempo de execução, controlado pelas permissões consentiu pelo respectivo administrador.

## <a name="next-steps"></a>Próximas etapas
Objeto de aplicativo de um aplicativo pode ser acessado através da API do Azure AD Graph, conforme representado por sua OData [entidade de aplicativo][AAD-Graph-App-Entity]

Objeto de serviço de um aplicativo pode ser acessado através da API do Azure AD Graph, conforme representado por sua OData [entidade ServicePrincipal][AAD-Graph-Sp-Entity]



<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Classic-Portal]: https://manage.windowsazure.com