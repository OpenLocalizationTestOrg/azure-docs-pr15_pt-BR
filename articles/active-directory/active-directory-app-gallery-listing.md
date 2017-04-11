<properties
   pageTitle="Listando seu aplicativo na Galeria de aplicativo do Active Directory do Azure"
   description="Como listar um aplicativo compatível com logon único na Galeria do Active Directory do Azure | Microsoft Azure"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>


# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Listando seu aplicativo na Galeria de aplicativo do Active Directory do Azure

Para listar um aplicativo compatível com logon único com o Azure Active Directory na [Galeria do Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/), o aplicativo primeiro precisa implementar um dos seguintes modos de integração:

* **Conectar-se OpenID** - integração direta com o Azure AD usando a conexão de OpenID para autenticação e a API de consentimento do Azure AD para configuração. Se você estiver apenas começando uma integração e seu aplicativo não suporta SAML, em seguida, este é o modo de recomendar.

* **SAML** – seu aplicativo já tem a capacidade de configurar provedores de identidade de terceiros usando o protocolo SAML.

Requisitos de listagem para cada modo estão abaixo.

##<a name="openid-connect-integration"></a>OpenID conectar integração

Para integrar seu aplicativo com o Azure AD, seguindo as [instruções do desenvolvedor](active-directory-authentication-scenarios.md). Em seguida, conclua as perguntas abaixo e enviar para waadpartners@microsoft.com.

* Fornece credenciais para uma conta ou um locatário de teste com o aplicativo que pode ser usado pela equipe do Azure AD para testar a integração.  

* Fornece instruções sobre como a equipe do Azure AD pode entrar e conectar uma instância do Azure AD para seu aplicativo usando o [Azure AD consentimento framework](active-directory-integrating-applications.md#overview-of-the-consent-framework). 

* Forneça qualquer instruções adicionais necessárias para a equipe do Azure AD testar logon único com seu aplicativo. 

* Forneça as informações abaixo:

> Nome da empresa:
> 
> Site de empresa:
> 
> Nome do aplicativo:
> 
> Descrição de aplicativo (limite de 256 caracteres):
> 
> Site de aplicativo (informacional):
> 
> Site de suporte técnico de aplicativo ou informações de contato:
> 
> ID do cliente do aplicativo, conforme mostrado nos detalhes do aplicativo em https://manage.windowsazure.com:
> 
> URL de inscrição do aplicativo onde os clientes ir para se inscrever para e/ou comprar o aplicativo:
> 
> Escolha até três categorias para o seu aplicativo seja listado em (para categorias disponíveis, consulte o Marketplace do Azure Active Directory):
> 
> Anexe aplicativo pequeno ícone (arquivo PNG, 45px por 45px, cor de plano de fundo sólida):
> 
> Anexe ícone grande de aplicativo (arquivo PNG, 215px por 215px, cor de plano de fundo sólida):
> 
> Anexe logotipo de aplicativo (arquivo PNG, 150px por 122px, cor de plano de fundo transparente):

##<a name="saml-integration"></a>Integração de SAML

Qualquer aplicativo compatível com SAML 2.0 pode ser integrado diretamente com um locatário do AD Azure usando [essas instruções para adicionar um aplicativo personalizado](active-directory-saas-custom-apps.md). Depois que você tenha testado que a integração com seu aplicativo funcione com o Azure AD, envie as seguintes informações para <waadpartners@microsoft.com>.

* Fornece credenciais para uma conta ou um locatário de teste com o aplicativo que pode ser usado pela equipe do Azure AD para testar a integração.  

* Fornecem os valores de (serviço de consumidor de declaração) SAML logon URL, a URL de emissor (ID de entidade) e URL de resposta do aplicativo, como descrito [aqui](active-directory-saas-custom-apps.md). Se você normalmente fornecer esses valores como parte de um arquivo de metadados SAML, em seguida, envie que também.

* Forneça uma breve descrição de como configurar o Azure AD como um provedor de identidade em seu aplicativo usando SAML 2.0. Se seu aplicativo suporta Configurando Azure AD como um provedor de identidade por meio de um portal administrativo autoatendimento, em seguida, verifique se as credenciais fornecidas acima incluem a capacidade de configurar.

* Forneça as informações abaixo:

> Nome da empresa:
> 
> Site de empresa:
> 
> Nome do aplicativo:
> 
> Descrição de aplicativo (limite de 256 caracteres):
> 
> Site de aplicativo (informacional):
> 
> Site de suporte técnico de aplicativo ou informações de contato:
> 
> URL de inscrição do aplicativo onde os clientes ir para se inscrever para e/ou comprar o aplicativo:
> 
> Escolha até três categorias para o seu aplicativo seja listado em (para categorias disponíveis, consulte o [Azure Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/))):
> 
> Anexe aplicativo pequeno ícone (arquivo PNG, 45px por 45px, cor de plano de fundo sólida):
> 
> Anexe ícone grande de aplicativo (arquivo PNG, 215px por 215px, cor de plano de fundo sólida):
> 
> Anexe logotipo de aplicativo (arquivo PNG, 150px por 122px, cor de plano de fundo transparente):
