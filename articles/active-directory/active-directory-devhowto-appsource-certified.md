<properties
   pageTitle="Como obter AppSource certificado para o Active Directory do Azure | Microsoft Azure"
   description="Detalhes sobre como obter o aplicativo AppSource certificado para o Active Directory do Azure."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/28/2016"
   ms.author="skwan;bryanla"/>

#<a name="how-to-get-appsource-certified-for-azure-active-directory-ad"></a>Como obter certificados de AppSource do Azure Active Directory (AD) 

Para receber certificação AppSource para Azure AD, seu aplicativo deve implementar o sinal de vários locatário no padrão com o Azure AD usando os protocolos OpenID conectar, OAuth 2.0 ou SAML 2.0. 

Se não estiver familiarizado com o Azure AD entrar ou desenvolvimento de aplicativos de vários locatários:

1. Comece lendo sobre o [navegador da Web App cenários em cenários de autenticação do Azure AD][AAD-Auth-Scenarios-Browser-To-WebApp].  
2. Em seguida, confira o Azure AD [guias de início rápido de aplicativo de web][AAD-QuickStart-Web-Apps], que demonstram como implementar entrar e incluem exemplos de código companion. 

    > [AZURE.TIP] Experimente a visualização do nosso novo [portal do desenvolvedor](https://identity.microsoft.com/Docs/Web) que ajudarão você a começar a trabalhar com o Active Directory do Azure em poucos minutos!  O portal do desenvolvedor o orientará durante o processo de registrar um aplicativo e integrar Azure AD seu código.  Quando tiver terminado, você terá um aplicativo simples que pode autenticar os usuários em seu locatário e um back-end que podem aceitar tokens e executar a validação.

3. Para saber como implementar o locatário vários entrar padrão com Azure AD, confira [como entrar em qualquer usuário do Azure Active Directory (AD) usando o padrão de locatário vários aplicativos][AAD-Howto-Multitenant-Overview]

## <a name="related-content"></a>Conteúdo relacionado
Para obter mais informações sobre como criar aplicativos que suportam Azure AD entrar, ou para obter ajuda e suporte, consultem o [Guia do desenvolvedor do Azure AD][AAD-Dev-Guide].

Use a seção de comentários Disqus seguindo este artigo para fornecer comentários e ajudar a refinar e nosso conteúdo de forma.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#web-application-quick-start-guides


<!--Image references-->










