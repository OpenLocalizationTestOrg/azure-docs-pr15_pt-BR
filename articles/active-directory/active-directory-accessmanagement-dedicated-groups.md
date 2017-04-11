<properties
    pageTitle="Dedicada grupos do Active Directory do Azure | Microsoft Azure"
    description="Visão geral dos grupos como dedicados funcionam no Active Directory do Azure e como eles são criados."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>

# <a name="dedicated-groups-in-azure-active-directory"></a>Dedicado grupos do Active Directory do Azure

No Azure Active Directory (AD Azure), o recurso grupos dedicado automaticamente cria e preenche a associação de grupos do Azure AD predefinido. Membros de grupos dedicados não podem ser adicionados ou removidos usando o portal clássico Azure, os cmdlets do Windows PowerShell, ou programaticamente.

>[AZURE.NOTE] Grupos dedicados exigem que uma licença do Azure AD Premium é atribuída a
>- o administrador que gerencia a regra em um grupo
>- todos os usuários que são selecionados pela regra para ser um membro do grupo

**Para habilitar grupos dedicados**

1. No [portal de clássico Azure](https://manage.windowsazure.com), selecione **Active Directory**e abra o diretório da sua organização.

2. Selecione a guia **grupos** e, em seguida, abra o grupo que você deseja editar.

3. Selecione a guia **Configurar** e então defina **Habilitar grupos dedicada** como **Sim**.

Quando a opção Habilitar dedicado grupos estiver definida como **Sim**, você pode habilitar ainda mais o diretório criar automaticamente o grupo dedicado de todos os usuários definindo a **Habilitar "Todos os usuários" grupo** alternar para **Sim**. Você, em seguida, também pode editar o nome desse grupo dedicado digitando-a **nome de exibição para "Todos os usuários" grupo** campo.

O grupo de todos os usuários pode ser usado para atribuir as mesmas permissões para todos os usuários no diretório. Por exemplo, você pode conceder a todos os usuários na sua acesso de diretório para um aplicativo de SaaS atribuindo acesso para o grupo de todos os usuários dedicado a esse aplicativo.

O grupo de todos os usuários dedicado inclui todos os usuários no diretório, inclusive convidados e usuários externos. Se precisar de um grupo que exclua a usuários externos, em seguida, você pode fazer isso criando um grupo com uma regra de dinâmica baseado no atributo como estas:

                (user.userPrincipalName -notContains "#EXT#@")

Para um grupo que exclua todos os convidados, use uma regra como estas:

                (user.userType -ne "Guest")

Para saber mais sobre como criar regras *avançadas* (regras que podem conter várias comparações) para associações de grupo dinâmico, consulte [usando atributos para criar regras avançadas](active-directory-accessmanagement-groups-with-advanced-rules.md).


Estes artigos fornecem informações adicionais sobre Active Directory do Azure.

* [Gerenciando o acesso aos recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)
* [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [O que é Azure Active Directory?](active-directory-whatis.md)
* [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
