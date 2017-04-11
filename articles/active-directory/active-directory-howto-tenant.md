<properties
    pageTitle="Como obter um locatário do Azure AD | Microsoft Azure"
    description="Como obter um locatário do Active Directory do Azure para registrar e criação de aplicativos."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="terrylan"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/28/2015"
    ms.author="dastrock"/>

# <a name="how-to-get-an-azure-active-directory-tenant"></a>Como obter um locatário do Active Directory do Azure

No Azure Active Directory (AD Azure), um [locatário](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) é representante de uma organização.  É uma instância dedicada do serviço Azure AD que uma organização recebe e possui quando ele se inscreve para um serviço de nuvem da Microsoft como Azure, Microsoft Intune ou Office 365.  Cada locatário Azure AD é diferentes e separadas das outros locatários do Azure AD.  

Um locatário hospeda os usuários em uma empresa e as informações sobre eles - suas senhas, dados de perfil de usuário, permissões e assim por diante.  Ele também contém grupos, aplicativos e outras informações relativas à sua segurança e de uma organização.

Para permitir que usuários do Azure AD entrar em seu aplicativo, você deve registrar seu aplicativo em um locatário de sua preferência.  Publicar um aplicativo em um locatário do Azure AD é **totalmente gratuita**.  Na verdade, a maioria dos desenvolvedores irá criar vários locatários e aplicativos para experimento, desenvolvimento, teste e testes.  As organizações que Inscreva-se e consumam seu aplicativo podem optar por comprar licenças se quiserem tirar proveito de recursos avançados de diretório.

Portanto, como você faz sobre a obtenção de um locatário do Azure AD?  O processo pode ser um pouco se diferentes que você:

- [Tiver uma assinatura existente do Office 365](#use-an-existing-office-365-subscription)
- [Tiver uma assinatura existente do Azure associada a uma Account da Microsoft](#use-an-msa-azure-subscription)
- [Tiver uma assinatura existente do Azure associada a uma conta organizacional](#use-an-organizational-azure-subscription)
- [Ter nenhuma das anteriores & deseja começar do zero](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>Usar uma assinatura existente do Office 365
Se você tiver uma assinatura existente do Office 365, você já tem um locatário do Azure AD! Você pode entrar no [portal do Azure](https://portal.azure.com) com sua conta do Office 365 e começar a usar o Azure AD.

## <a name="use-an-msa-azure-subscription"></a>Usar uma assinatura do Azure MSA
Se você já se inscreveu para uma assinatura do Azure com sua Account Microsoft individuais, você já tem um locatário!  Quando fizer logon no [Portal do Azure](https://portal.azure.com), você será conectado automaticamente seu locatário padrão. Você está livre para usar este locatário ajustá-la -, mas talvez você queira criar uma conta de administrador organizacional.

Para fazer isso, siga estas etapas.  Como alternativa, você poderá criar um novo locatário e um administrador em locatário seguindo um processo semelhante.

1.  Entrar no [Portal do Azure](https://portal.azure.com) com sua conta individual
2.  Navegue até a seção "Azure Active Directory" do portal (encontrado na barra de navegação esquerda, em **Mais serviços**)
3.  Você deve automaticamente ser conectado ao "Diretório padrão", se não você pode alternar diretórios clicando no nome da sua conta no canto superior direito.
4.  Na seção **Tarefas rápidas** , escolha **Adicionar um usuário**.
5.  No formulário do usuário adicionar, forneça os seguintes detalhes:

    - Nome: (escolha um valor apropriado)
    - Nome de usuário: (escolha um nome de usuário para este administrador)
    - Perfil: (preencher os valores apropriados para o primeiro nome, último nome, cargo e departamento)
    - Função: Administrador Global

6.  Quando você concluiu o formulário do usuário adicionar e recebe a senha temporária para o novo usuário administrativo, certifique-se de gravar essa senha, pois você precisará fazer logon com este novo usuário para alterar a senha. Você também pode enviar a senha diretamente para o usuário, usando um email alternativo.
7.  Clique em **criar** para criar um novo usuário.
8.  Para alterar a senha temporária, faça logon em [https://login.microsoftonline.com](https://login.microsoftonline.com) com esta nova conta de usuário e alterar a senha quando solicitado.


## <a name="use-an-organizational-azure-subscription"></a>Usar uma assinatura do Azure organizacional
Se você já se inscreveu para uma assinatura do Azure com sua conta organizacional, você já tem um locatário!  No [Portal do Azure](https://portal.azure.com), você deve encontrar um locatário quando você navegar para "Mais serviços" e "Azure Active Directory."  Você está livre para usar este locatário ajustá-la. 


## <a name="start-from-scratch"></a>Começar do zero
Se todas as opções acima for sem sentido para você, não se preocupe.  Visite simplesmente [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) inscrevam para Azure uma nova organização.  Após concluir o processo, você terá seu próprio locatário Azure AD com o nome de domínio que você escolheu durante a inscrição para cima.  No [Portal do Azure](https://portal.azure.com), você pode encontrar seu locatário, navegando até "Azure Active Directory" na navegação à esquerda

Como parte do processo de inscrição no Azure, você precisará fornecer detalhes de cartão de crédito.  Você pode prosseguir com confiança - você não será cobrado para publicar aplicativos no Azure AD ou criar novos locatários.
