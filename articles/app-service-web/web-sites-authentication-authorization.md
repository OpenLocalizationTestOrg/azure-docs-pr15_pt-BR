<properties 
    pageTitle="Autenticar com o Active Directory local em seu aplicativo Azure | Microsoft Azure" 
    description="Saiba mais sobre as diferentes opções para os aplicativos de linha de negócios em um serviço de aplicativo do Azure para autenticar com o Active Directory local" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="web" 
    ms.date="08/31/2016" 
    ms.author="cephalin"/>

# <a name="authenticate-with-on-premises-active-directory-in-your-azure-app"></a>Autenticar com o Active Directory local no seu aplicativo do Azure #

Este artigo mostra como autenticar com o Active Directory (AD) no [Serviço de aplicativo do Azure](../app-service/app-service-value-prop-what-is.md)local. Um aplicativo do Azure está hospedado na nuvem, mas há maneiras para autenticar local usuários do AD com segurança. 

## <a name="authenticate-through-azure-active-directory"></a>Autenticação por meio do Active Directory do Azure
Um locatário do Azure Active Directory pode ser sincronizada de diretório com um local AD. Essa abordagem permite que usuários do AD acessar seu aplicativo da internet e autenticar usando suas credenciais de local. Além disso, o serviço de aplicativo do Azure fornece uma [solução de ativar-chave para este método](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). Com apenas alguns cliques de um botão, você pode habilitar a autenticação com um locatário sincronizado de diretório para seu aplicativo do Azure. Essa abordagem tem as seguintes vantagens:

-   Não exige qualquer código de autenticação em seu aplicativo. Permitem serviço de aplicativo faça a autenticação para você e use seu tempo em fornecer funcionalidade em seu aplicativo.
-   [API do Azure AD Graph](http://msdn.microsoft.com/library/azure/hh974476.aspx) permite o acesso aos dados do diretório de seu aplicativo do Azure.
-   Fornece o SSO para [todos os aplicativos com suporte pelo Azure Active Directory](/marketplace/active-directory/), incluindo o Office 365, Dynamics CRM Online, Microsoft Intune e milhares de aplicativos em nuvem não sejam da Microsoft. 
-   Active Directory do Azure suporta controle de acesso baseado em função. Você pode usar o padrão de [Authorize(Roles="X")] com mínimas alterações para seu código.

Para ver como escrever um aplicativo de linha de negócios Azure que autentica com o Azure Active Directory, consulte [criar um aplicativo do Azure de linha de negócios com a autenticação do Active Directory do Azure](web-sites-dotnet-lob-application-azure-ad.md).

## <a name="authenticate-through-an-on-premises-sts"></a>Autenticação por meio de um STS local
Se você tiver um local serviço de símbolo seguro (STS) como os serviços de Federação do Active Directory (AD FS), você pode usar que interajam autenticação de seu aplicativo do Azure. Essa abordagem é melhor quando a política da empresa impede dados AD sejam armazenados no Azure. No entanto, observe o seguinte:

-   Topologia de STS deve ser implantado no local, com sobrecarga de custo e gerenciamento.
-   Apenas administradores do AD FS podem configurar [regras de declaração e terceira relações de confiança de terceiros](http://technet.microsoft.com/library/dd807108.aspx), que pode limitar as opções do desenvolvedor. Por outro lado, é possível gerenciar e personalizar [declarações](http://technet.microsoft.com/library/ee913571.aspx) em uma base por aplicativo.
-   Acesso ao local dados AD requerem uma solução separada por meio do firewall corporativo.

Para ver como escrever um aplicativo de linha de negócios Azure que autentica com um STS local, consulte [criar um aplicativo do Azure de linha de negócios com a autenticação do AD FS](web-sites-dotnet-lob-application-adfs.md).
 
