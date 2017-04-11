<properties
    pageTitle="Visão geral de ponto de extremidade de v 2.0 | Microsoft Azure"
    description="Uma introdução à criação de aplicativos de Account da Microsoft e o Active Directory do Azure entrada."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="dastrock"/>

# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Entrar Account Microsoft & Azure AD usuários em um único aplicativo

No passado, um desenvolvedor de aplicativos que desejam dar suporte a contas da Microsoft e o Active Directory do Azure foi necessário para integrar com dois sistemas separados.  Agora, apresentamos uma nova versão da API de autenticação que permite que você entre usuários com os dois tipos de contas usando o sistema de Azure AD.  Este sistema de autenticação convergentes é conhecido como **o ponto de extremidade de v 2.0**.  Com o ponto de extremidade de v 2.0, uma integração simples permite alcançar um público que se expande por milhões de usuários com contas pessoais e de trabalho/escola.

Aplicativos que usam o ponto de extremidade de v 2.0 também podem consumir APIs REST do [Microsoft Graph](https://graph.microsoft.io) e [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) utilizando qualquer tipo de conta.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## <a name="getting-started"></a>Guia de Introdução
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

Escolha sua plataforma favorita na lista a seguir para criar um aplicativo usando nosso bibliotecas de código-fonte aberto e estruturas.  Como alternativa, você pode usar nossa documentação de protocolo OAuth 2.0 & OpenID conectar para enviar e receber mensagens de protocolo diretamente sem usar uma biblioteca de autenticação.

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>Quais são as novidades
As informações conceituais aqui será útil entender o que é e o que não é possível fazer com o ponto de extremidade de v 2.0.

- Saiba mais sobre os [tipos de aplicativos que você pode criar com o ponto de extremidade de v 2.0](active-directory-v2-flows.md).
- Entenda as [restrições e limitações, restrições,](active-directory-v2-limitations.md) com o ponto de extremidade de v 2.0.
- Nós adicionamos suporte para [Administração restrito escopos](active-directory-v2-scopes.md) e o [cliente de OAuth2 credenciais conceder](active-directory-v2-protocols-oauth-client-creds.md)recentemente.  Experimentá-las!

## <a name="reference"></a>Referência
Esses links será útil para explorar a plataforma em camadas:

- Construir 2016: [Introdução ao Microsoft identidades: Enterprise notas entrar para seus aplicativos](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
- Obter ajuda sobre estouro de pilha usando o [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal) marcas.
- [Referência de protocolo v 2.0](active-directory-v2-protocols.md)
- [Referência de Token de v 2.0](active-directory-v2-tokens.md)
- [Referência da biblioteca v 2.0](active-directory-v2-libraries.md)
- [Escopos e consentimento no ponto de extremidade v 2.0](active-directory-v2-scopes.md)
- [O Portal de registro de aplicativo da Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)
- [Referência da API REST o Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [O Microsoft Graph](https://graph.microsoft.io)