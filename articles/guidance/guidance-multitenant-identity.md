<properties
   pageTitle="Gerenciamento de identidades para vários locatários aplicativos | Microsoft Azure"
   description="Práticas recomendadas para gerenciamento de autenticação, autorização e identidade nos aplicativos vários locatários."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="identity-management-for-multitenant-applications-in-microsoft-azure"></a>Gerenciamento de identidades para vários locatários aplicativos no Microsoft Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Esta série descreve as práticas recomendadas para multilocação, ao usar o Azure AD para gerenciamento de autenticação e identidade.

Quando você estiver criando um aplicativo de vários locatários, um dos desafios primeiro está gerenciando identidades de usuário, porque agora cada usuário pertence a um locatário. Por exemplo:

- Usuários entrar com suas credenciais organizacionais.
- Os usuários devem ter acesso a dados, mas não os dados que pertence a outros locatários da sua organização.
- Uma organização pode inscrever-se para o aplicativo e atribuir funções de aplicativo seus membros.

Azure Active Directory (AD Azure) tem alguns excelentes recursos que oferecem suporte a todos esses cenários.

Para acompanhar esta série de artigos, também criamos uma completa, [implementação de ponta a ponta] [ tailspin] de um aplicativo de vários locatários. Os artigos refletem o que aprendemos no processo de criação do aplicativo. Para começar a usar o aplicativo, consulte [executando o aplicativo de pesquisas](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md).

Aqui está a lista de artigos desta série:

- [Introdução ao gerenciamento de identidades para vários locatários aplicativos](guidance-multitenant-identity-intro.md)
- [Sobre o aplicativo de Tailspin pesquisas](guidance-multitenant-identity-tailspin.md)
- [Autenticação usando o Azure AD e conecte OpenID](guidance-multitenant-identity-authenticate.md)
- [Trabalhando com identidades baseada em declarações](guidance-multitenant-identity-claims.md)
- [Inscrição e integração de locatários](guidance-multitenant-identity-signup.md)
- [Funções de aplicativo](guidance-multitenant-identity-app-roles.md)
- [Autorização baseada em função e baseado em recursos](guidance-multitenant-identity-authorize.md)
- [Protegendo um API da web de back-end](guidance-multitenant-identity-web-api.md)
- [Tokens de acesso do cache OAuth2](guidance-multitenant-identity-token-cache.md)
- [Federando com AD FS de um cliente para aplicativos vários locatários no Azure](guidance-multitenant-identity-adfs.md)
- [Usando declaração do cliente para obter tokens de acesso do Azure AD](guidance-multitenant-identity-client-assertion.md)
- [Usar chave cofre para proteger segredos de aplicativo](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
