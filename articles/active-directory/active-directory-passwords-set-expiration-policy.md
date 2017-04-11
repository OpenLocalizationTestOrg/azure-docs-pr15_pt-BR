<properties
    pageTitle="Definir políticas de expiração de senha no Active Directory do Azure | Microsoft Azure"
    description="Saiba como verificar diretivas de expiração e alterar expiração de senha de usuário individualmente ou em massa para senhas do Azure Active directory"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand"/>


# <a name="set-password-expiration-policies-in-azure-active-directory"></a>Definir políticas de expiração de senha no Active Directory do Azure

> [AZURE.IMPORTANT] **Você está aqui porque você está tendo problemas para entrar?** Em caso afirmativo, [Veja como você pode alterar e redefinir sua própria senha](active-directory-passwords-update-your-own-password.md).

Como um administrador global para um serviço de nuvem da Microsoft, você pode usar o Microsoft Azure Active Directory módulo para Windows PowerShell para definir senhas de usuários não para expirar. Você também pode usar cmdlets do Windows PowerShell para remover o nunca-expira configuração ou para saber qual usuário senhas estão configuradas não para expirar. Este artigo fornece ajuda para serviços de nuvem, como Microsoft Intune e o Office 365, que dependem do Microsoft Azure Active Directory para serviços de identidade e diretório.

  > [AZURE.NOTE] Somente senhas para contas de usuário que não estão sincronizadas por meio da sincronização de diretório podem ser configuradas para não expirar. Para obter mais informações sobre a sincronização de diretório, consulte a lista de tópicos no [mapa para sincronização de diretório](https://msdn.microsoft.com/library/azure/hh967642.aspx).

Para usar cmdlets do Windows PowerShell, você primeiro deve instalá-las.

## <a name="what-do-you-want-to-do"></a>O que você deseja fazer?

- [Como verificar a política de expiração de senha](#how-to-check-expiration-policy-for-a-password)

- [Definir uma senha para expirar](#set-a-password-to-expire)

- [Definir uma senha para que ela não expirará](#set-a-password-to-never-expire)

## <a name="how-to-check-expiration-policy-for-a-password"></a>Como verificar a política de expiração de senha

1.  Conecte ao Windows PowerShell usando suas credenciais de administrador da empresa.

2.  Siga um destes procedimentos:

    - Para ver se a senha de um único usuário está definida para nunca expirar, execute o seguinte cmdlet usando o nome de usuário principal (UPN) (por exemplo, aprilr@contoso.onmicrosoft.com) ou a ID de usuário do usuário que você deseja verificar:`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`

    - Para ver a configuração "Senha nunca expira" para todos os usuários, execute o seguinte cmdlet:`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## <a name="set-a-password-to-expire"></a>Definir uma senha para expirar

1.  Conecte ao Windows PowerShell usando suas credenciais de administrador da empresa.

2.  Siga um destes procedimentos:

    - Para definir a senha de um usuário para que a senha irá expirar, execute o cmdlet a seguir usando o nome de usuário principal (UPN) ou a ID de usuário do usuário:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`

    - Para definir as senhas de todos os usuários na organização para que eles expirará, use o seguinte cmdlet:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

## <a name="set-a-password-to-never-expire"></a>Definir uma senha para nunca expirar

1. Conecte ao Windows PowerShell usando suas credenciais de administrador da empresa.

2.  Siga um destes procedimentos:

    - Para definir a senha de um usuário para nunca expirar, execute o cmdlet a seguir usando o nome de usuário principal (UPN) ou a ID de usuário do usuário:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`

    - Para definir as senhas de todos os usuários em uma organização para nunca expirar, execute o seguinte cmdlet:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>Próximas etapas

* **Você está aqui porque você está tendo problemas para entrar?** Em caso afirmativo, [Veja como você pode alterar e redefinir sua própria senha](active-directory-passwords-update-your-own-password.md).
