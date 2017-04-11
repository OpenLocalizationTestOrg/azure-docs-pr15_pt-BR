<properties
    pageTitle="Adicionar seu nome de domínio personalizado para visualização do Active Directory do Azure | Microsoft Azure"
    description="Como adicionar nomes de domínio da sua empresa ao Azure Active Directory e como verificar o nome de domínio."
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
    ms.date="10/17/2016"
    ms.author="curtand"/>

# <a name="add-a-custom-domain-name-to-azure-active-directory-preview"></a>Adicione um nome de domínio personalizado para visualização do Active Directory do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-domains-add-azure-portal.md)
- [Azure portal clássico](active-directory-add-domain.md)

Você tem um ou mais nomes de domínio que sua organização usa para fazer negócios e seus usuários entram sua rede corporativa usando seu nome de domínio corporativo. Usando a visualização do Azure Active Directory (AD Azure), você pode adicionar seu nome de domínio corporativo ao Azure AD também. [O que é no preview?](active-directory-preview-explainer.md) Isso permite que você atribua nomes de usuário no diretório que são familiares aos seus usuários, tais como ‘alice@contoso.com.’ o processo é simples:

1. Adicione o nome de domínio personalizado para seu diretório
2. Adicionar uma entrada de DNS para o nome de domínio no registrador de nomes de domínio
3. Verifique o nome de domínio personalizado no Azure AD

## <a name="how-do-i-add-a-domain-name"></a>Como posso adicionar um nome de domínio?

1.  Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2.  Selecione **mais serviços**, insira o **Azure Active Directory** na caixa de texto e, em seguida, pressione **Enter**.

    ![Gerenciamento de usuários de abertura](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Na lâmina ***nome do diretório*** , selecione **os nomes de domínio**.

4. Na lâmina ** *diretório-nome* - nomes de domínio** , selecione o comando **Adicionar** .

  ![Selecionando o comando Adicionar](./media/active-directory-domains-add-azure-portal/add-command.png)

5. Na lâmina **nome de domínio** , digite o nome do seu domínio personalizado na caixa, como 'contoso.com' e selecione **Adicionar domínio**. Certifique-se de incluir o com, .net ou outra extensão de nível superior.

6. Na lâmina ***nome_do_domínio*** (ou seja, a lâmina que é aberta com o novo nome de domínio no título), obtenha as informações de entrada DNS que Azure AD usará para verificar que sua organização possui o nome de domínio personalizado.

  ![obter informações de entrada DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

Agora que você adicionou o nome de domínio, Azure AD deve verificar que sua organização possui o nome de domínio. Antes de Azure AD pode realizar essa verificação, você deve adicionar uma entrada de DNS no arquivo de zona de DNS para o nome de domínio. Esta tarefa é executada no site do registrador de nomes de domínio para o nome de domínio.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Adicionar a entrada DNS no registrador de nomes de domínio para o domínio

A próxima etapa para usar seu nome de domínio personalizado com o Azure AD é atualizar o arquivo de zona DNS para o domínio. Isso permite Azure AD verificar que sua organização possui o nome de domínio personalizado.

1.  Entrar no registrador de nomes de domínio para o domínio. Se você não tiver acesso ao atualizar a entrada DNS, peça a pessoa ou equipe que tenha esse acesso para concluir a etapa 2 e avise quando ela estiver concluída.

2.  Atualize o arquivo de zona DNS para o domínio, adicionando a entrada DNS fornecida pela Azure AD. Essa entrada DNS permite Azure AD verificar sua posse do domínio. A entrada DNS não altera quaisquer comportamentos como roteamento de email ou hospedagem na web.

Para obter ajuda com esta adição da entrada DNS, leia [as instruções para adicionar uma entrada DNS em registradores DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Verifique o nome de domínio com o Azure AD

Depois de adicionar a entrada DNS, você estará pronto para verificar o nome de domínio com o Azure AD.

Um nome de domínio pode ser verificado somente depois que os registros DNS tiveram propagado. Essa propagação normalmente leva apenas alguns segundos, mas, às vezes, pode levar uma hora ou mais. Se a verificação não funcionar da primeira vez, tente novamente mais tarde.

1.  Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2.  Selecione **Procurar**, insira o gerenciamento de usuários na caixa de texto e, em seguida, pressione **Enter**.

    ![Gerenciamento de usuários de abertura](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Na lâmina **gerenciamento de usuários - nomes de domínio** , selecione o nome de domínio não verificados que você deseja verificar.

4. Na lâmina ***nome_do_domínio*** (ou seja, a lâmina que é aberta com o novo nome de domínio no título), selecione **Verificar** para concluir a verificação.

Agora você pode [atribuir nomes de usuário que incluem seu nome de domínio personalizado](active-directory-users-create-azure-portal.md).

## <a name="troubleshooting"></a>Solução de problemas

Se você não consegue verificar um nome de domínio personalizado, tente o seguinte. Vamos começar com o mais comum e trabalhar para baixo até o menos comum.

1.  **Aguarde uma hora**. Registros DNS necessário propagar antes Azure AD pode verificar o domínio. Isso pode levar uma hora ou mais.

2.  **Verifique se o registro de DNS foi inserido, e ele está correto**. Conclua essa etapa no site para o registrador de nome de domínio para o domínio. Azure AD não pode verificar o nome de domínio se a entrada DNS não estiver presente no arquivo de zona DNS, ou se não for uma correspondência exata com a entrada DNS que Azure AD fornecido a você. Se você não tem acesso ao atualizar os registros DNS para o domínio no registrador de nomes de domínio, compartilhar a entrada DNS com a pessoa ou equipe em sua organização que tem esse acesso e peça-lhe para adicionar a entrada DNS.

3.  **Excluir o nome de domínio de outro diretório no Azure AD**. Um nome de domínio pode ser verificado em apenas um único diretório. Se um nome de domínio foi verificado anteriormente no outro diretório, ele deve ser excluído lá antes que ele pode ser verificado em sua nova pasta. Para saber sobre a exclusão de nomes de domínio, leia [Gerenciar nomes de domínio personalizado](active-directory-domains-manage-azure-portal.md).    

## <a name="add-more-custom-domain-names"></a>Adicionar mais nomes de domínio personalizado

Se sua organização usa vários nomes de domínio personalizado, como 'contoso.com' e 'contosobank.com', você pode adicioná-los até no máximo 900 nomes de domínio. Use as mesmas etapas neste artigo para adicionar cada um dos seus nomes de domínio.

## <a name="next-steps"></a>Próximas etapas

[Gerenciar nomes de domínio personalizado](active-directory-domains-manage-azure-portal.md)
