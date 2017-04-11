<properties
    pageTitle="Serviços de domínio do Azure AD: Habilitar a sincronização de senha | Microsoft Azure"
    description="Introdução ao Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="maheshu"/>

# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Habilitar a sincronização de senha com os serviços de domínio do Azure AD
Em tarefas anteriores, você ativou a serviços de domínio do Azure AD para seu locatário do Azure AD. A próxima tarefa é habilitar hash credenciais necessárias para autenticação NTLM e Kerberos sincronizar os serviços de domínio do Azure AD. Depois que a sincronização de credencial é configurada, os usuários possam entrar no domínio gerenciado usando suas credenciais corporativas.

As etapas envolvidas são diferentes com base em se sua organização tem um Azure AD somente na nuvem locatário ou está configurado para sincronizar com seu diretório local usando o Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Somente na nuvem Azure AD de locatários](active-directory-ds-getting-started-password-sync.md)
- [Sincronizado locatário Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Tarefa 5: Habilitar a sincronização de senha para serviços de domínio AAD para um Azure somente na nuvem locatário do AD
Serviços de domínio do AD Azure precisam credencial hash em um formato adequado para autenticação NTLM e Kerberos, para autenticar os usuários no domínio gerenciado. A menos que você ative AAD serviços de domínio do seu locatário, Azure AD não gerar ou armazenar hash de credencial no formato necessário para autenticação NTLM ou Kerberos. Por razões de segurança óbvias, Azure AD também não armazenar as credenciais no formulário de texto não criptografado. Portanto, o Azure AD não tem uma maneira de gerar esses hash de credencial NTLM ou Kerberos com base em credenciais existentes dos usuários.

> [AZURE.NOTE] Se sua organização tiver um somente na nuvem locatário do Azure AD, os usuários que precisam usar os serviços de domínio do Azure AD deve alterar suas senhas.

Esse processo de alteração de senha faz com que a credencial hash obrigatório pelos serviços de domínio do Azure AD para autenticação Kerberos e NTLM será gerado no Azure AD. Ou você pode expirar senhas para todos os usuários no locatário que precisam usar os serviços de domínio do Azure AD ou instruir esses usuários para alterar suas senhas.


### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Ativar a geração de hash de credencial NTLM e Kerberos para um Azure somente na nuvem locatário do AD
Aqui estão instruções que necessárias para fornecer aos usuários finais, para que eles possam alterar suas senhas:

1. Navegue até a página de painel de acesso do Azure AD para sua organização no [http://myapps.microsoft.com](http://myapps.microsoft.com).

2. Selecione a guia **perfil** nesta página.

3. Clique no bloco de **Alterar senha** nesta página.

    ![Crie uma rede virtual para os serviços de domínio do Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password.png)

    > [AZURE.NOTE] Se você não vir a opção **Alterar senha** na página painel de acesso, certifique-se de que sua organização tiver configurado o [gerenciamento de senha no Azure AD](../active-directory/active-directory-passwords-getting-started.md).

4. Na página **Alterar senha** , digite sua senha (antiga) existente e, em seguida, digite uma nova senha e confirmá-la. Clique em **Enviar**.

    ![Crie uma rede virtual para os serviços de domínio do Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Depois que você alterou sua senha, a nova senha poderá ser usada nos serviços de domínio do Azure AD em breve. Após alguns minutos (normalmente, cerca de 20 minutos), você pode entrar computadores que ingressaram em domínio gerenciado usando a senha alterada recentemente.

<br>

## <a name="related-content"></a>Conteúdo relacionado

- [Como atualizar sua própria senha](../active-directory/active-directory-passwords-update-your-own-password.md)

- [Introdução ao gerenciamento de senha no Azure AD](../active-directory/active-directory-passwords-getting-started.md).

- [Habilitar a sincronização de senha para serviços de domínio AAD para um Azure sincronizado locatário do AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Administrar um domínio gerenciado de serviços de domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Ingressar em uma máquina virtual Windows a um domínio gerenciado de serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Ingressar em uma máquina virtual de Red Hat Enterprise Linux em um domínio gerenciado de serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
