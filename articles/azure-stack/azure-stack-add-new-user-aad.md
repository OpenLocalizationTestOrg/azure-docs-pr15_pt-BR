<properties
    pageTitle="Adicionar uma nova conta de locatário do Azure pilha no Active Directory do Azure | Microsoft Azure"
    description="Depois de implantar o Microsoft Azure pilha VDC, você precisará criar conta de usuário de pelo menos um locatário para que você pode explorar o portal de locatário."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Adicionar uma nova conta de locatário do Azure pilha no Active Directory do Azure

Após a [implantação do Azure pilha VDC](azure-stack-run-powershell-script.md), será necessário uma conta de usuário do locatário para que possa explorar o portal de locatário e testar seus planos e ofertas. Você pode criar uma conta de locatário usando [o portal Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) ou usando o [PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Crie uma conta de locatário de pilha Azure usando o portal do Azure

Você deve ter uma assinatura do Azure para usar o portal do Azure.

1. Faça logon no [Azure](http://manage.windowsazure.com).

2.  Na barra de navegação à esquerda do Microsoft Azure, clique em **Active Directory**.

3.  Na lista de diretório, clique na pasta que você deseja usar para pilha Azure ou crie um novo.

4.  Na página diretório, clique em **usuários**.

5.  Clique em **Adicionar usuário**.

6.  No assistente **Adicionar usuário** , na lista **tipo de usuário** , escolha **novo usuário na sua organização**.

7.  Na caixa **nome de usuário** , digite um nome para o usuário.

8.  No **@** caixa, escolha a entrada apropriada.

9.  Clique na seta próxima.

10.  Na página de **perfil de usuário** do assistente, digite um **nome**, **Sobrenome**e **nome para exibição**.

11. Na lista de **função** , escolha o **usuário**.

12. Clique na seta próxima.

13. Na página **obter senha temporária** , clique em **criar**.

14. Copie a **nova senha**.

15. Faça logon no Microsoft Azure com a nova conta. Altere a senha quando solicitado.

16. Faça logon em `https://portal.azurestack.local` com a nova conta para ver o portal de locatário.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Criar uma conta de locatário pilha Azure usando o PowerShell

Se você não tiver uma assinatura do Azure, é possível usar o portal do Azure para adicionar uma conta de usuário do locatário. Nesse caso, você pode usar o Active Directory módulo Azure para Windows PowerShell.

> [AZURE.NOTE] Se você estiver usando o Microsoft Account (Live ID) para implantar o Azure pilha VDC, você não pode usar o PowerShell AAD para criar a conta de locatário. 

1.  Instale o [Assistente Microsoft Online Services entrar para profissionais de TI RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).

2.  Instale o [Azure Active Directory módulo para Windows PowerShell (versão de 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297) e abri-lo.

3.  Execute os seguintes cmdlets:




    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack PoC
   
            $msolcred = get-credential
    
    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".
    
            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId
    
    ```

4.  Entrar no Microsoft Azure com a nova conta. Altere a senha quando solicitado.

5.  Entrar no `https://portal.azurestack.local` com a nova conta para ver o portal de locatário.



