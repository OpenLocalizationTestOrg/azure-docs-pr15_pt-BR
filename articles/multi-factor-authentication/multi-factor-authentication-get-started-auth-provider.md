<properties
    pageTitle="Obter o provedor de autenticação multifator Azure de Introdução | Microsoft Azure"
    description="Saiba como criar um provedor de autenticação multifator Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>



# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Guia de Introdução com um provedor de autenticação multifator do Azure
A verificação está disponível por padrão para os administradores globais que têm o Active Directory do Azure e usuários do Office 365. No entanto, se quiser tirar proveito de [recursos avançados](multi-factor-authentication-whats-next.md) , em seguida, você deve adquirir a versão completa do Azure multifator autenticação (MFA).

> [AZURE.NOTE]  Um provedor de autenticação multifator Azure é usado para tirar proveito dos recursos oferecidos pela versão completa do Azure MFA. Ele está disponível para usuários que **não tenham licenças por meio do Azure MFA, Azure AD Premium ou EMS**.  Azure MFA Azure AD Premium e EMS incluem a versão completa do Azure MFA por padrão.  Se você tiver licenças, então você não é necessário um provedor de autenticação multifator Azure.

Um provedor do Azure multifator Auth é necessário para baixar o SDK.

> [AZURE.IMPORTANT]  Para baixar o SDK, crie um provedor de autenticação multifator Azure mesmo se tiver o Azure MFA, AAD Premium ou EMS licenças.  Se você cria um provedor de autenticação multifator Azure para essa finalidade e já tem licenças, certifique-se de criar o provedor com o modelo **Por usuário habilitado** . Em seguida, vincule o provedor para a pasta que contém as licenças Azure MFA, Azure AD Premium ou EMS.  Isso garante que você é cobrado somente se você tiver mais usuários únicos usando o SDK do que o número de licenças que você possui.


## <a name="to-create-a-multi-factor-auth-provider"></a>Para criar um provedor de autenticação multifator

Use as seguintes etapas para criar um provedor de autenticação multifator Azure.

1. Entre no [portal clássico Azure](https://manage.windowsazure.com) como administrador.
2. À esquerda, selecione **Active Directory**.
3. Na página do Active Directory, na parte superior, selecione **Os provedores de autenticação multifator**.
![Criando um provedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. Na parte inferior, clique em **novo**.
![Criando um provedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. Em serviços de aplicativo, selecione o **Provedor de autenticação multifator**
![criando um provedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Selecione **criar rápido**.
![Criando um provedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. Preencha os seguintes campos e selecione **criar**.
    1. **Nome** – o nome do provedor de autenticação multifator.
    2. **Modelo de uso** – se deseja habilitar usuários individuais ou pagar por verificação. Escolha uma das duas opções:
        - Por autenticação – modelo de compra que cobra por autenticação. Normalmente é usado para cenários que usam a autenticação multifator Azure em um aplicativo de voltados para o consumidor.
        - Por usuário habilitado – o modelo de compra que cobra por habilitado usuário. Normalmente é usado para acesso de funcionário para aplicativos como o Office 365. Escolha esta opção se você tiver alguns usuários que já estão licenciados para MFA do Azure.
    2. **Diretório** locatário Azure Active Directory que o provedor de autenticação multifator está associado. Lembre-se dos seguintes procedimentos:
        - Não é necessário um diretório Azure AD para criar um provedor de autenticação multifator. Deixe a caixa em branco se você estiver planejando usar o servidor de autenticação multifator Azure ou o SDK somente.
        - O provedor de autenticação multifator devem ser associado um diretório do Azure AD para tirar proveito dos recursos avançados.
        - Azure AD Connect, AAD Sync ou DirSync são apenas um requisito se você estiver sincronizando seu ambiente do Active Directory local com um diretório Azure AD.  Se você usar apenas um diretório Azure AD que não está sincronizado, em seguida, isso não é necessário.
![Criando um provedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
5. Depois de clicar em criar, o provedor de autenticação multifator é criado e você verá uma mensagem informando: **criado com êxito o provedor de autenticação multifator**. Clique em **Okey**.
![Criando um provedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)
