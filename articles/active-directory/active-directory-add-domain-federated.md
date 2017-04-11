<properties
    pageTitle="Adicionar seu nome de domínio personalizado e configurar o logon federado ao Azure Active Directory | Microsoft Azure"
    description="Como adicionar nomes de domínio da sua empresa ao Azure Active Directory e como configurar o logon federado entre Azure Active Directory e de sua solução de federação local."
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
    ms.topic="get-started-article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="add-your-custom-domain-name-to-azure-active-directory"></a>Adicionar seu nome de domínio personalizado ao Azure Active Directory

Você pode configurar um nome de domínio personalizado, como 'contoso.com', para que os usuários em contoso.com podem ter uma federados única experiência de logon de sua rede corporativa. Se você já tiver os serviços de Federação do Active Directory (AD FS) ou um servidor de Federação diferentes em sua rede corporativa, você pode configurar Azure AD para utilizar seu nome de domínio personalizado usando a ferramenta de Azure AD Connect. Você também pode usar Azure AD Connect para implantar um novo ambiente do AD FS e configurar que para federados single sign-on ao Azure AD.

Se você não tiver e não planeja implantar o AD FS ou outro servidor de federação, siga estas instruções: [Adicionar um nome de domínio personalizado ao Azure Active Directory](active-directory-add-domain.md).

## <a name="add-a-custom-domain-name-to-your-directory"></a>Adicionar um nome de domínio personalizado ao seu diretório

1. Entrar no [portal clássico Azure](https://manage.windowsazure.com/) com uma conta de usuário que é um administrador global do seu diretório do Azure AD.

2. No **Active Directory**, abra seu diretório e selecione a guia **domínios** .

3. Na barra de comandos, selecione **Adicionar**e, em seguida, insira o nome do seu domínio personalizado, como 'contoso.com'. Certifique-se de incluir o com, .net ou outra extensão de nível superior.

4. Selecione a caixa de seleção **para planejar configurar esse domínio para logon único com meu Active Directory local** .

5. Selecione **Adicionar**.

Execute a ferramenta de Azure AD Connect para obter a entrada DNS que Azure AD usará para verificar o domínio. Você verá a entrada DNS no **Domínio do Azure AD** etapa do assistente. Você pode ver o que essa etapa do assistente se parece com [nessas instruções](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Se você não tiver a ferramenta de Azure AD Connect, você poderá [baixá-lo aqui](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Adicionar a entrada DNS no registrador de nomes de domínio para o domínio

A próxima etapa para usar seu nome de domínio personalizado com o Azure AD é atualizar o arquivo de zona DNS para o domínio. Isso permite Azure AD verificar que sua organização possui o nome de domínio personalizado.

1. Entrar no site do registrador de nomes de domínio para seu nome de domínio. Se você não tem acesso para fazer isso, peça a pessoa ou equipe em sua organização que tem esse acesso para concluir a etapa 2 e avise quando ela estiver concluída.

2. Atualize o arquivo de zona DNS para o domínio, adicionando a entrada DNS fornecida pela Azure AD. Essa entrada DNS permite Azure AD verificar sua posse do domínio. A entrada DNS não altera quaisquer comportamentos como roteamento de email ou hospedagem na web.

Para obter ajuda com esta etapa, leia [as instruções para adicionar uma entrada DNS em registradores DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Verifique o nome de domínio com o Azure AD

Depois de adicionar a entrada DNS, você estará pronto para verificar o nome de domínio com o Azure AD.

Para verificar o domínio, selecione **Avançar** na etapa do assistente Azure AD Connect **Azure AD domínio** . Azure AD procurará a entrada DNS no arquivo de zona de DNS para o domínio. Azure AD somente Verifique se o nome de domínio depois que os registros DNS tiveram propagado. Propagação normalmente leva apenas alguns segundos, mas, às vezes, pode levar uma hora ou mais. Se a verificação não funcionar da primeira vez, tente novamente mais tarde.

Em seguida, prossiga com as etapas restantes do Assistente do Azure AD Connect. Isso sincronizará os usuários do seu servidor AD do Windows Azure AD. Usuários sincronizados no domínio que você configurou para federação poderão acessar uma federados única experiência de logon de sua rede corporativa Azure AD.

## <a name="troubleshooting"></a>Solução de problemas

Se você não consegue verificar um nome de domínio personalizado, tente o seguinte. Vamos começar com o mais comum e trabalhar para baixo até o menos comum.

1.  **Aguarde uma hora**. Registros DNS necessário propagar antes Azure AD pode verificar o domínio. Isso pode levar uma hora ou mais.

2.  **Verifique se o registro de DNS foi inserido, e ele está correto**. Conclua essa etapa no site para o registrador de nome de domínio para o domínio. Azure AD não pode verificar o nome de domínio se a entrada DNS não estiver presente no arquivo de zona DNS, ou se não for uma correspondência exata com a entrada DNS que Azure AD fornecido a você. Se você não tem acesso ao atualizar os registros DNS para o domínio no registrador de nomes de domínio, compartilhar a entrada DNS com a pessoa ou equipe em sua organização que tem esse acesso e peça-lhe para adicionar a entrada DNS.

3.  **Excluir o nome de domínio de outro diretório no Azure AD**. Um nome de domínio pode ser verificado em apenas um único diretório. Se um nome de domínio foi verificado anteriormente no outro diretório, ele deve ser excluído lá antes que ele pode ser verificado em sua nova pasta. Para saber sobre a exclusão de nomes de domínio, leia [Gerenciar nomes de domínio personalizado](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>Adicionar mais nomes de domínio personalizado

Se sua organização usa vários nomes de domínio personalizado, como 'contoso.com' e 'contosobank.com', você pode adicioná-los até no máximo 900 nomes de domínio. Use as mesmas etapas neste artigo para adicionar cada um dos seus nomes de domínio.

## <a name="next-steps"></a>Próximas etapas

-   [Gerenciar nomes de domínio personalizado](active-directory-add-manage-domain-names.md)
-   [Saiba mais sobre os conceitos de gerenciamento de domínio do Azure AD](active-directory-add-domain-concepts.md)
-   [Mostrar a que sua empresa da identidade visual quando os usuários entrar](active-directory-add-company-branding.md)
-   [Usar o PowerShell para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
