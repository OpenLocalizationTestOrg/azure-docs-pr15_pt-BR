<properties
    pageTitle="Obter iniciado Azure MFA na nuvem | Microsoft Azure"
    description="Esta é a página de autenticação multifator do Microsoft Azure que descreve como começar com o Azure MFA na nuvem."
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
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Introdução ao Azure multi-Factor Authentication na nuvem
Este artigo explica como começar a usar o Azure multi-Factor Authentication na nuvem.

> [AZURE.NOTE]  A documentação a seguir fornece informações sobre como habilitar usuários usando o **Portal de clássico do Azure**. Se você estiver procurando informações sobre como configurar a autenticação multifator do Azure para usuários do Office 365, consulte [Configurar a autenticação multifator para o Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![MFA na nuvem](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisites"></a>Pré-requisitos
Os seguintes pré-requisitos são necessários antes de habilitar a autenticação multifator Azure para seus usuários.


1. [Inscrição para uma assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/) - se você ainda não tiver uma assinatura do Azure, é necessário inscrição para um. Se você estiver apenas começando e usando o Azure MFA você pode usar uma assinatura de avaliação
2. [Criar um provedor de autenticação multifator](multi-factor-authentication-get-started-auth-provider.md) e atribuí-la ao seu diretório ou [Atribuir licenças a usuários](multi-factor-authentication-get-started-assign-licenses.md)

> [AZURE.NOTE]  Licenças estão disponíveis para usuários que têm MFA do Azure, Azure AD Premium ou do pacote de mobilidade da empresa (EMS).  MFA está incluído no Azure AD Premium e EMS. Se você tiver licenças suficientes, você não precisa criar um provedor de autenticação.


## <a name="turn-on-two-step-verification-for-users"></a>Ativar a verificação para usuários
Para iniciar a necessidade de verificação de dois-início em para um usuário, altere o estado do usuário de desativado para ativado.  Para obter mais informações sobre estados de usuário, consulte [Estados de usuário no Azure multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

Use o procedimento a seguir para habilitar MFA para seus usuários.

### <a name="to-turn-on-multi-factor-authentication"></a>Para ativar a autenticação multifator

1.  Entre no [portal clássico Azure](https://manage.windowsazure.com) como administrador.
2.  À esquerda, clique em **Active Directory**.
3.  Em diretório, selecione o diretório para o usuário que você deseja ativar.
![Clique em diretório](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Na parte superior, clique em **usuários**.
5.  Na parte inferior da página, clique em **Gerenciar multifator Auth**. Abre uma nova guia do navegador.
![Clique em diretório](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Localize o usuário que você deseja ativar para verificação em duas etapas. Talvez você precise alterar o modo de exibição na parte superior. Certifique-se de que o status é **desabilitado.** 
 ![Permitir que o usuário](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Coloque um **Verificar** na caixa ao lado do nome dele.
7.  À direita, clique em **Ativar**.
![Habilitar usuário](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Clique em **Habilitar a autenticação multifator**.
![Habilitar usuário](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Observe que o estado do usuário foi alterado de **desativado** para **ativado**.
![Permitir que os usuários](./media/multi-factor-authentication-get-started-cloud/user.png)

Depois de você habilitou seus usuários, você deve notificá-los por email. Na próxima vez em que eles tentarem entrar, ele serão solicitados a registrar sua conta para verificação em duas etapas. Depois que eles começarem a usar a verificação, também será preciso configurar senhas de aplicativos para evitar sendo bloqueadas fora aplicativos não-localizador.


## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>Usar o PowerShell para automatizar a como ativar a verificação

Para alterar o [estado](multi-factor-authentication-whats-next.md) de uso do [PowerShell do Azure AD](../powershell-install-configure.md), você pode usar o seguinte.  Você pode alterar `$st.State` igual um dos seguintes estados:

- Habilitado
- Impostas
- Desativado  

> [AZURE.IMPORTANT]  Podemos desencorajar contra movendo usuários diretamente do estado desativar para o estado de imposto. Aplicativos não baseados em navegador deixarão de funcionar porque o usuário não tem passaram registro MFA e obtido uma [senha de aplicativo](multi-factor-authentication-whats-next.md#app-passwords). Se você tiver aplicativos não baseado em navegador e exige senhas de aplicativos, recomendamos que você acesse de um estado desabilitado ativado. Isso permite aos usuários registrar e obter suas senhas de aplicativo. Depois disso, você pode movê-los para imposto.

Usando o PowerShell seria uma opção para em massa, permitindo que os usuários. Atualmente não há nenhum recurso de ativar em massa no portal do Azure e você precisa selecionar cada usuário individualmente. Se você tiver muitos usuários, isso pode ser bastante uma tarefa. Criando um PowerShell script usando este procedimento, você pode percorrer uma lista de usuários e habilitá-las.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Aqui está um exemplo:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Para obter mais informações, consulte [Estados de usuário no Azure multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>Próximas etapas
Agora que você configurou o Azure multi-Factor Authentication na nuvem, você pode configurar e configurar sua implantação. Consulte [Configurando a autenticação multifator Azure](multi-factor-authentication-whats-next.md) para obter mais detalhes.
