<properties
    pageTitle="Gerenciando grupos do Active Directory do Azure | Microsoft Azure"
    description="Como criar e gerenciar grupos para gerenciar usuários Azure usando o Active Directory do Azure."
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
    ms.topic="get-started-article"
    ms.date="09/29/2016"
    ms.author="curtand"/>


# <a name="managing-groups-in-azure-active-directory"></a>Gerenciando grupos do Active Directory do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-groups-create-azure-portal.md)
- [Azure portal clássico](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)


Um dos recursos do gerenciamento de usuários do Azure Active Directory (AD Azure) é a capacidade de criar grupos de usuários. Usar um grupo para realizar tarefas de gerenciamento como a atribuição de licenças ou permissões para um número de usuários ao mesmo tempo. Você também pode usar grupos para atribuir a permissão de acesso para

- Recursos como objetos no diretório
- Recursos externos para o diretório como aplicativos SaaS, os serviços do Azure, sites do SharePoint ou recursos de local

Além disso, um proprietário de recurso também pode atribuir acesso a um recurso a um grupo do Azure AD pertencente a outra pessoa. Esta atribuição concede os membros do acesso ao recurso a esse grupo. Em seguida, o proprietário do grupo gerencia membro do grupo. Na verdade, o proprietário do recurso delegue para o proprietário do grupo a permissão para atribuir usuários aos seus recursos.

## <a name="how-do-i-create-a-group"></a>Como para criar um grupo?

Dependendo dos serviços ao qual sua organização assinou, você pode criar um grupo usando um destes procedimentos:
- portal clássico do Azure
- portal de conta do Office 365
- o portal de conta do Windows Intune

Nós será descrevem tarefas como realizado no portal de clássico do Azure. Para obter mais informações sobre como usar portais não Azure para gerenciar seu diretório Azure AD, consulte [Administrando seu diretório Azure AD](active-directory-administer.md).

1. No [portal de clássico Azure](https://manage.windowsazure.com), selecione **Active Directory**e selecione o nome da pasta para a sua organização.

2. Selecione a guia de **grupos** .

3. Selecione **Adicionar grupo**.

4. Na janela **Adicionar grupo** , especifique o nome e a descrição de um grupo.


## <a name="how-do-i-add-or-remove-individual-users-in-a-security-group"></a>Como adicionar ou remover usuários individuais em um grupo de segurança?

**Para adicionar um usuário individual a um grupo**

1. No [portal de clássico Azure](https://manage.windowsazure.com), selecione **Active Directory**e selecione o nome da pasta para a sua organização.

2. Selecione a guia de **grupos** .

3. Abra o grupo ao qual você deseja adicionar membros. Abra a guia **membros** do grupo selecionado se ele não estiver sendo exibido.

4. Selecione **Adicionar membros**.

5. Na página **Adicionar membros** , selecione o nome de usuário ou grupo que você deseja adicionar como um membro desse grupo. Certifique-se de que esse nome é adicionado ao painel **selecionado** .


**Para remover um usuário individual de um grupo**

1. No [portal de clássico Azure](https://manage.windowsazure.com), selecione **Active Directory**e selecione o nome da pasta para a sua organização.

2. Selecione a guia de **grupos** .

3. Abra o grupo do qual você deseja remover membros.

4. Selecione a guia **membros** , selecione o nome do membro que você deseja remover do grupo e clique em **Remover**.

6. No prompt, confirme que você deseja remover este membro do grupo.


## <a name="how-can-i-manage-the-membership-of-a-group-dynamically"></a>Como posso gerenciar a associação de um grupo dinamicamente?

No Azure AD facilmente você pode configurar uma regra simples para determinar quais usuários devem ser membros do grupo. Uma regra simples é aquele que faz apenas uma única comparação. Por exemplo, se um grupo é atribuído a um aplicativo de SaaS, você pode configurar uma regra para adicionar usuários que têm um cargo de "Representante de vendas." Essa regra concede o acesso a esse aplicativo SaaS para todos os usuários com esse cargo no diretório.

Quando os atributos de uma alteração de usuário, o sistema avalia todas as regras de grupo dinâmico em um diretório para ver se a alteração de atributo do usuário seria disparar qualquer grupo adiciona ou remove. Se um usuário satisfizer uma regra em um grupo, eles são adicionados como um membro a esse grupo. Se elas já não atendem a regra de um grupo que eles são um membro de, eles são removidos como um membros do grupo.

> [AZURE.NOTE] Você pode configurar uma regra para dinâmica participação em grupos de segurança ou Office 365. Associações de grupo aninhado atualmente não são suportadas para atribuição baseada em grupo para aplicativos.
>
> Associações dinâmicas para grupos exijam uma licença do Azure AD Premium a ser atribuído a
>
> - O administrador que gerencia a regra em um grupo
> - Todos os membros do grupo

**Para ativar a associação dinâmica para um grupo**

1. No [portal de clássico Azure](https://manage.windowsazure.com), selecione **Active Directory**e selecione o nome da pasta para a sua organização.

2. Selecione a guia de **grupos** e abra o grupo que você deseja editar.

3. Selecione a guia **Configurar** e então defina **Habilitar associações dinâmico** como **Sim**.

4. Configure uma regra única simples para o grupo controlar os membros como dinâmico para este funções de grupo. Verifique se o **Adicionar usuários onde** opção está selecionada e, em seguida, selecione uma propriedade de usuário na lista (por exemplo, departamento, jobTitle, etc.)

5. Em seguida, selecione uma condição (não é igual a, igual a, não começa com, começa com, não contém, contém, não correspondência, CORRESP).

6. Especifique um valor de comparação para a propriedade de usuário selecionado.

Para saber mais sobre como criar regras *avançadas* (regras que podem conter várias comparações) para associações de grupo dinâmico, consulte [usando atributos para criar regras avançadas](active-directory-accessmanagement-groups-with-advanced-rules.md).

## <a name="additional-information"></a>Informações adicionais

Estes artigos fornecem informações adicionais sobre Active Directory do Azure.

* [Gerenciando o acesso aos recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

* [Azure cmdlets do Active Directory para definir as configurações de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)

* [O que é Azure Active Directory?](active-directory-whatis.md)

* [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
