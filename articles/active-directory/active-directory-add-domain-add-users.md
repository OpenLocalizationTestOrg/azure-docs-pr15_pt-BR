<properties
    pageTitle="Atribuir usuários a um domínio personalizado no Active Directory do Azure | Microsoft Azure"
    description="Como preencher um domínio personalizado no Active Directory do Azure com contas de usuário."
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="assign-users-to-a-custom-domain"></a>Atribuir usuários a um domínio personalizado

Depois de adicionar seu domínio personalizado ao Azure Active Directory, você deve adicionar as contas de usuário para este domínio para que você possa começar a autenticação-los.

## <a name="users-synced-in-from-a-directory-on-your-corporate-network"></a>Usuários sincronizados em de um diretório na sua rede corporativa

Se você já configurou uma conexão entre o seu local do Active Directory e o Azure Active Directory, a sincronização pode preencher as contas. Para obter mais informações sobre como sincronizar o Active Directory do Azure com seu Active Directory local, consulte [integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).

## <a name="users-added-and-managed-in-the-cloud"></a>Os usuários adicionados e gerenciados na nuvem

Para alterar o domínio para uma conta de usuário existente:

1.  Abra o portal do clássico Azure usando uma conta que seja um administrador global ou um administrador do usuário.

2.  Abra seu diretório.

3.  Selecione a guia de **usuários** .

4.  Selecione o usuário na lista.

5.  Mudar o domínio para o usuário e selecione **Salvar**.

Isso também pode ser feito usando o [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) ou a [API do gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>Selecione um domínio personalizado ao criar um novo usuário

1.  Abra o portal do clássico Azure usando uma conta que seja um administrador global ou um administrador do usuário.

2.  Abra seu diretório.

3.  Selecione a guia de **usuários** .

4.  Na barra de comando, selecione **Adicionar**.

5.  Quando você adiciona o nome de usuário, escolha o domínio personalizado na lista de domínio.

6.  Selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

-   [Usando nomes de domínio personalizado para simplificar o processo de conexão para seus usuários](active-directory-add-domain.md)

-   [Gerenciar nomes de domínio personalizado](active-directory-add-manage-domain-names.md)

-   [Saiba mais sobre os conceitos de gerenciamento de domínio do Azure AD](active-directory-add-domain-concepts.md)
