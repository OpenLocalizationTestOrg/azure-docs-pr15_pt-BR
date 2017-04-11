<properties
    pageTitle="Adicionar novos usuários ao Azure Active Directory | Microsoft Azure"
    description="Explica como adicionar novos usuários ou alterar informações do usuário no Active Directory do Azure."
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
    ms.date="09/22/2016"
    ms.author="curtand"/>

# <a name="add-new-users--or-users-with-microsoft-accounts-to-azure-active-directory"></a>Adicionar novos usuários ou usuários com contas do Microsoft Azure Active Directory

Adicione usuários para preencher seu diretório. Este artigo explica como adicionar novos usuários em sua organização e como adicionar usuários que têm contas da Microsoft. Para obter mais informações sobre como adicionar usuários de outros diretórios do Azure Active Directory ou adicionando usuários de empresas de parceiro, consulte [Adicionar usuários de outros diretórios ou empresas de parceiros no Active Directory do Azure](active-directory-create-users-external.md). Usuários adicionados não tem permissões de administrador por padrão, mas você pode atribuir funções-los a qualquer momento.

## <a name="add-a-user"></a>Adicionar um usuário

1. Entrar no [portal clássico Azure](https://manage.windowsazure.com) com uma conta que seja um administrador global do diretório.
2. Selecione o **Active Directory**e, em seguida, selecione o nome do seu diretório da organização.
3. Selecione a guia de **usuários** e, em seguida, na barra de comando, selecione **Adicionar usuário**.
4. Na página **Conte-nos sobre este usuário** , em **tipo de usuário**, selecione:

    - **Novo usuário na sua organização** – adiciona uma nova conta de usuário no diretório.
    - **Usuário com uma conta existente do Microsoft** – adiciona uma conta existente do consumidor Microsoft ao seu diretório (por exemplo, uma conta do Outlook)

5. Dependendo do **tipo de usuário**, insira um nome de usuário (para o novo usuário) ou um endereço de email (para um usuário com uma conta da Microsoft).
6. Na página de **perfil** do usuário, forneça um nome e sobrenome, um nome amigável e uma função de usuário na lista de **funções** . Para obter mais informações sobre funções de usuário e administrador, consulte [Atribuindo funções de administrador no Azure AD](active-directory-assign-admin-roles.md). Especifique se deseja **Ativar autenticação multifator** do usuário.
7. Na página **obter senha temporária** , selecione **criar**.

> [AZURE.IMPORTANT] Se sua organização usar mais de um domínio, você deve saber sobre os seguintes problemas ao adicionar uma conta de usuário:
>
> - Para adicionar contas de usuário mesmo nome de usuário principal (UPN) em domínios, **primeiro** adicione, por exemplo, geoffgrisso@contoso.onmicrosoft.com, **seguido por** geoffgrisso@contoso.com.
> - **Não** adicionar geoffgrisso@contoso.com antes de adicionar geoffgrisso@contoso.onmicrosoft.com. Nesta ordem é importante e pode ser complicada para desfazer.

## <a name="change-user-information"></a>Alterar as informações de usuário

Você pode alterar qualquer atributo de usuário, exceto a ID do objeto.

1. Abra seu diretório.
2. Selecione a guia de **usuários** e, em seguida, selecione o nome de exibição do usuário que você deseja alterar.
3. Conclua as alterações e clique em **Salvar**.

Se o usuário que você está alterando é sincronizado com o serviço do Active Directory local, você não pode alterar as informações do usuário usando este procedimento. Para alterar o usuário, use suas ferramentas de gerenciamento do Active Directory local.

## <a name="guest-user-management-and-limitations"></a>Limitações e gerenciamento de usuário convidado

Contas de convidado são usuários de outros diretórios que foram convidados para seu diretório para acessar documentos do SharePoint, aplicativos ou outros recursos Azure. Uma conta de convidado no seu diretório tem seu atributo UserType subjacente definida como "Convidado". Usuários regulares (especificamente, membros do seu diretório) têm o atributo de UserType "Membro".

Convidados têm um conjunto limitado de direitos no diretório. Esses direitos restrinja a capacidade de convidados descobrir informações sobre outros usuários no diretório. No entanto, os usuários convidados ainda podem interagir com os usuários e grupos associados com os recursos que estão trabalhando. Usuários convidados podem:

- Veja outros usuários e grupos associados a uma assinatura do Azure ao qual ela estão atribuídas
- Ver os membros de grupos aos quais eles pertencem
- Procurar outros usuários no diretório, se elas souberem o endereço de email completo do usuário
- Ver somente um conjunto limitado de atributos dos usuários que eles procurarem – limitados para exibir o nome, endereço de email, nome de usuário principal (UPN) e fotografia em miniatura
- Obter uma lista de domínios verificados no diretório
- Consentimento para aplicativos, conceder-lhes o mesmo acesso que os membros têm no seu diretório

## <a name="set-guest-user-access-policies"></a>Definir políticas de acesso de usuário convidado

Na guia **Configurar** de um diretório inclui opções para controlar o acesso para usuários convidados. Essas opções podem ser alteradas somente no portal de clássico Azure por um administrador global do diretório. Atualmente, não haverá nenhum método PowerShell ou API.

Para abrir a guia **Configurar** no portal de clássico do Azure, selecione **Active Directory**e selecione o nome da pasta.

![Configure o guia no Active Directory do Azure][1]

Você pode editar as opções para controlar o acesso para usuários convidados.

![Opções de controle de acesso para usuários convidados][2]


## <a name="whats-next"></a>Qual é a próxima

- [Adicionar usuários de outros diretórios ou empresas de parceiros no Active Directory do Azure](active-directory-create-users-external.md)
- [Administrando Azure AD](active-directory-administer.md)
- [Gerenciar senhas no Azure AD](active-directory-manage-passwords.md)
- [Gerenciar grupos no Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png
