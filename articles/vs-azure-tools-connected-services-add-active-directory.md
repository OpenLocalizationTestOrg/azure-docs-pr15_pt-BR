<properties 
   pageTitle="Adicionando um Azure Active Directory usando serviços conectados no Visual Studio | Microsoft Azure"
   description="Adicionar um Active Directory do Azure usando a caixa de diálogo do Visual Studio adicionar conectado serviços"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="active-directory"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Adicionando um Azure Active Directory usando serviços conectados no Visual Studio 

##<a name="overview"></a>Visão geral
Usando o Azure Active Directory (AD Azure), você pode oferecer suporte a Single Sign-On (SSO) para aplicativos de web do ASP.NET MVC ou AD autenticação nos serviços de Web API. Com a autenticação do Azure AD, seus usuários podem usar suas contas do Azure AD para se conectar aos aplicativos web. As vantagens de autenticação AD Azure com API Web incluem segurança aprimorada de dados ao expor uma API de um aplicativo web. Com o Azure AD, você não precisa gerenciar um sistema de autenticação separada com seu próprio gerenciamento de conta e usuário.

## <a name="supported-project-types"></a>Tipos de projeto com suporte

Você pode usar a caixa de diálogo Serviços conectados para se conectar ao Azure AD nos seguintes tipos de projeto.

- Projetos ASP.NET MVC

- Projetos da Web do ASP.NET API


### <a name="connect-to-azure-ad-using-the-connected-services-dialog"></a>Conectar ao Azure AD usando a caixa de diálogo Serviços conectados

1. Verifique se que você tiver uma conta do Azure. Se você não tiver uma conta do Azure, você pode inscrever-se para uma [avaliação gratuita](http://go.microsoft.com/fwlink/?LinkId=518146).

1. No Visual Studio, abra o menu de atalho do nó **referências** em seu projeto e escolha **Adicionar serviços conectados**.
1. Selecione **Azure AD autenticação** e, em seguida, escolha **Configure**.

    ![Escolha adicionar autenticação do Azure AD](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. Na primeira página da **Configurar Azure AD autenticação**, marque **Configurar Single Sign-on usando o Azure AD**.

    Se seu projeto está configurado com outra configuração de autenticação, o assistente avisa que continuar desativará a configuração anterior.

    ![Configurar o Azure AD no Assistente](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1.  Na segunda página, selecione um domínio na lista suspensa de **domínio** . Lista de domínios contém todos os domínios acessíveis por contas listadas na caixa de diálogo Configurações de conta. Como alternativa, você pode inserir um nome de domínio se você não encontrar o que está procurando, como mydomain.onmicrosoft.com. Você pode escolher a opção para criar um novo aplicativo do Azure AD ou use as configurações de um aplicativo do Azure AD existente. 

    ![Configurar o Azure AD no Assistente](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)


1. Na terceira página do assistente, certifique-se de que **Leia os dados de diretório** está marcada. O assistente irá preencher o **segredo cliente**. 

    ![Configurar o Azure AD no Assistente](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Escolha o botão **Concluir** . A caixa de diálogo adiciona o código de configuração necessárias e referências para habilitar o seu projeto para autenticação do Azure AD. Você pode ver o domínio do AD no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Examine a página Introdução que aparece no seu navegador para obter ideias sobre próximas etapas e a página o que aconteceu para ver como seu projeto foi modificado. Se você quiser verificar se tudo funcionou, abra um dos arquivos de configuração modificados e verifique as configurações mencionadas no que aconteceu estão lá. Por exemplo, o Web. config principal em um projeto do ASP.NET MVC terá estas configurações adicionadas:

        <appSettings> 
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:AADInstance" value="https://login.windows.net/" />
            <add key="ida:Domain" value="Your selected domain" />
            <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
            <add key="ida:PostLogoutRedirectUri" value="The default redirect URI from the project" />
        </appSettings>

## <a name="how-your-project-is-modified"></a>Como o seu projeto é modificado

Quando você executa o assistente, o Visual Studio adiciona Azure AD e associados referências ao seu projeto. Arquivos de configuração e arquivos de código do seu projeto também são modificados para adicionar suporte para Azure AD. As modificações específico Visual Studio torna dependem do tipo de projeto. Para obter informações detalhadas sobre como os projetos do ASP.NET MVC são modificados, consulte [Quais projetos de MVC aconteceu –](http://go.microsoft.com/fwlink/p/?LinkID=513809). Para projetos de API da Web, consulte [o que aconteceu – Web API projetos](http://go.microsoft.com/fwlink/p/?LinkId=513810).

##<a name="next-steps"></a>Próximas etapas

Faça perguntas e obter ajuda.

 - [Fórum do MSDN: Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)

 - [Documentação do Azure AD](https://azure.microsoft.com/documentation/services/active-directory/)

 - [Postagem de blog: Introdução ao Azure AD](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

