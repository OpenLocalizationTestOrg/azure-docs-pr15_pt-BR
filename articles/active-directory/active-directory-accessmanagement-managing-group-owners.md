
<properties
    pageTitle="Próximos passos para o gerenciamento de acesso usando grupos | Microsoft Azure"
    description="Avançadas como-para gerenciar grupos de segurança e como usar esses grupos para gerenciar o acesso a um recurso."
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
    ms.date="09/22/2016"
    ms.author="curtand"/>

# <a name="managing-owners-for-a-group"></a>Gerenciar proprietários de um grupo
Depois de atribuir um proprietário de recurso tem acesso a um recurso para um grupo do Azure AD, os membros do grupo é gerenciado pelo proprietário do grupo. O proprietário do recurso efetivamente delegue a permissão para atribuir aos usuários para o recurso para o proprietário do grupo.

## <a name="assigning-group-ownership"></a>Atribuir propriedade de grupo

**Para adicionar um proprietário a um grupo**

1. No [portal de clássico Azure](https://manage.windowsazure.com), selecione **Active Directory**e abra o diretório da sua organização.

2. Selecione a guia **grupos** e, em seguida, abra o grupo que você deseja adicionar os proprietários.

3. Selecione **Adicionar proprietários**.

4. Na página **Adicionar proprietários** , selecione o usuário que você deseja adicionar como o proprietário desse grupo e verifique se que esse nome é adicionada ao painel **selecionado** .


**Para remover um proprietário de um grupo**

1. No [portal de clássico Azure](https://manage.windowsazure.com), selecione **Active Directory**e abra o diretório da sua organização.

2. Selecione a guia **grupos** e, em seguida, abra o grupo que você deseja remover um proprietário de.

4. Selecione a guia **proprietários** .

5. Selecione o proprietário que você deseja remover do grupo e, em seguida, selecione **Remover**.

## <a name="additional-information"></a>Informações adicionais

Estes artigos fornecem informações adicionais sobre Active Directory do Azure.

* [Gerenciando o acesso aos recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)
* [Azure cmdlets do Active Directory para definir as configurações de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [O que é Azure Active Directory?](active-directory-whatis.md)
* [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
