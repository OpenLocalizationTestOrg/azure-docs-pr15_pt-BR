<properties 
    pageTitle="Como depurar baseada em SAML logon único para aplicativos do Azure Active Directory | Microsoft Azure" 
    description="Saiba como depurar baseada em SAML logon único para aplicativos do Azure Active Directory " 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#<a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Como depurar baseada em SAML logon único para aplicativos do Azure Active Directory

Ao depurar uma integração de aplicativo baseada em SAML, geralmente é útil usar uma ferramenta como [Fiddler](http://www.telerik.com/fiddler) para ver a solicitação SAML, a resposta SAML e o token SAML real que seja emitido pelo aplicativo. Examinando o token SAML, você pode garantir que todos os atributos necessários, o nome de usuário no assunto SAML e o emissor URI estejam chegando conforme esperado.

![][1]

A resposta do Azure AD que contém o token SAML normalmente é aquele que ocorre depois que um HTTP 302 redirecionar de https://login.windows.net e são enviadas para a **URL de resposta** configurado do aplicativo. 
 
Você pode exibir o token SAML selecionando essa linha e, em seguida, selecionando o **inspetores > formulários da Web** guia no painel direito. A partir daí, o valor de **SAMLResponse** de atalho e selecione **Enviar para TextWizard**. Selecione **De Base64** no menu **transformar** decodificar o token e ver seu conteúdo.
 
**Observação**: para ver o conteúdo desta solicitação de HTTP, Fiddler pode solicitar a configurar descriptografia de tráfego HTTPS, o que você precisará fazer.

## <a name="related-articles"></a>Artigos relacionados

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Configurando o logon único para aplicativos que não estão na Galeria de aplicativo do Active Directory do Azure](active-directory-saas-custom-apps.md)
- [Como personalizar declarações emitidas no Token SAML para aplicativos previamente integrados](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ./media/active-directory-saml-debugging/fiddler.png
