<properties
    pageTitle="Gerenciar nomes de domínio personalizado em sua visualização do Active Directory do Azure | Microsoft Azure"
    description="Conceitos de gerenciamento e instruções de gerenciamento de um nome de domínio do Active Directory do Azure"
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
    ms.date="09/12/2016"
    ms.author="curtand;jeffsta"/>

# <a name="managing-custom-domain-names-in-your-azure-active-directory-preview"></a>Gerenciar nomes de domínio personalizado em sua visualização do Active Directory do Azure

Um nome de domínio é uma parte importante do identificador para muitos recursos de diretório: ela faz parte de um usuário nome ou endereço de email para um usuário, parte do endereço para um grupo e pode ser parte do aplicativo URI de identificação para um aplicativo. Um recurso na visualização do Azure Active Directory (AD Azure) pode incluir um nome de domínio que já está verificado serem pertencentes a pasta que contém o recurso. [O que é no preview?](active-directory-preview-explainer.md) Somente um administrador global pode executar tarefas de gerenciamento de domínio no Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Definir o nome de domínio primário para seu diretório Azure AD

Quando seu diretório é criado, o nome de domínio inicial, como 'contoso.onmicrosoft.com', também é o nome de domínio primário. O domínio primário é o nome de domínio padrão para um novo usuário quando você cria um novo usuário. Isso simplifica o processo de administrador para criar novos usuários no portal. Para alterar o nome de domínio primário:

1.  Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2.  Selecione **mais serviços**, insira o **Azure Active Directory** na caixa de texto e, em seguida, pressione **Enter**.

    ![Gerenciamento de usuários de abertura](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Na lâmina ***nome do diretório*** , selecione **os nomes de domínio**.

4. Na lâmina ** *diretório-nome* - nomes de domínio** , selecione o nome de domínio que você gostaria de tornar o nome de domínio primário.

5.  Na lâmina ***nome_do_domínio*** (ou seja, a lâmina que é aberta com o novo nome de domínio no título), selecione o comando **tornar principal** . Confirme sua escolha quando solicitado.

    ![Tornar um nome de domínio primário](./media/active-directory-domains-manage-azure-portal/make-primary.png)

Você pode alterar o nome de domínio primário para seu diretório seja qualquer domínio personalizado verificado não federado. Alterar o domínio primário para seu diretório não alterará os nomes de usuário para usuários existentes.

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Adicionar nomes de domínio personalizado para seu Azure AD

Você pode adicionar até 900 nomes de domínio personalizado para cada diretório do Azure AD. O processo de [Adicionar um nome de domínio personalizado adicional](active-directory-domains-add-azure-portal.md) é o mesmo para o primeiro nome de domínio personalizado.

## <a name="add-subdomains-of-a-custom-domain"></a>Adicionar subdomínios de um domínio personalizado

Se você quiser adicionar um nome de domínio de terceiro nível como 'europe.contoso.com' ao seu diretório, você deve primeiro adicionar e verificar o domínio de segundo nível, como contoso.com. O subdomínio será automaticamente verificado pela Azure AD. Para ver que o subdomínio que você acabou de adicionar tiver sido verificado, atualize a página no navegador que lista os domínios.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>O que fazer se você alterar o registrador DNS para o seu nome de domínio personalizado

Se você alterar o registrador DNS para o seu nome de domínio personalizado, você pode continuar a usar seu nome de domínio personalizado com o Azure AD próprio sem interrupção e outras tarefas de configuração. Se você usar seu nome de domínio personalizado com o Office 365, Intune ou outros serviços que dependem de nomes de domínio personalizado no Azure AD, consulte a documentação para esses serviços.

## <a name="delete-a-custom-domain-name"></a>Excluir um nome de domínio personalizado

Você pode excluir um nome de domínio personalizado do seu Azure AD se sua organização já não usa esse nome de domínio ou se você precisar usar esse nome de domínio com outro Azure AD.

Para excluir um nome de domínio personalizado, primeiro você deve garantir que não há recursos no seu diretório contam com o nome de domínio. Você não pode excluir um nome de domínio do seu diretório se:

-   Qualquer usuário tem um nome de usuário, endereço de email ou endereço de proxy que inclui o nome de domínio.

-   Qualquer grupo tem um endereço de email ou proxy que inclui o nome de domínio.

-   Qualquer aplicativo em seu Azure AD tem um aplicativo URI de identificação que inclui o nome de domínio.

Você deve alterar ou excluir qualquer tal recurso no seu diretório Azure AD antes de excluir o nome de domínio personalizado.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Usar o PowerShell ou API de gráfico para gerenciar nomes de domínio

A maioria das tarefas de gerenciamento de nomes de domínio no Active Directory do Azure também podem ser concluídas usando Microsoft PowerShell ou programaticamente usando a API do Azure AD gráfico (no modo de visualização público).

-   [Usando o PowerShell para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Usando a API do gráfico para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Próximas etapas

-   [Adicionar nomes de domínio personalizado](active-directory-domains-add-azure-portal.md)
