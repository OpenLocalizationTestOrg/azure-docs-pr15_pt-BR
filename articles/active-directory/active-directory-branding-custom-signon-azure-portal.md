<properties
pageTitle="Personalizar sua página de entrada na visualização do Active Directory do Azure | Microsoft Azure"
description="Saiba como adicionar uma marca para a página de entrada Azure da empresa"
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
ms.date="09/30/2016"
ms.author="curtand"/>

# <a name="add-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Adicionar marca à sua página de entrada na visualização do Azure Active Directory da empresa

Para evitar confusão, muitas empresas desejam aplicar uma aparência consistente em todos os sites e serviços que gerenciam. Visualização do Azure Active Directory fornece esse recurso, permitindo que você personalize a aparência da página entrar com o logotipo da empresa e esquemas de cores personalizado. [O que é no preview?](active-directory-preview-explainer.md) A página de entrada é a página que aparece quando você entra no Office 365 ou outros aplicativos baseados na web que estão usando o Azure AD como seu provedor de identidade. Você interage com esta página para inserir suas credenciais.

Se você quiser mostrar a marca de sua empresa, cores e outros elementos personalizáveis nesta página, consulte as imagens a seguir compreender a diferença entre as duas experiências.

O seguinte exemplo para a página de entrada do Office 365 em um computador desktop **antes de** personalização e mostra a captura de tela:

![O Office 365 página de entrada antes de personalização](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

A captura de tela mostra e exemplo para o Office 365-na página de entrada em um computador desktop **depois de** personalização seguintes:

![O Office 365 página de entrada depois de personalização](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)


## <a name="customizing-the-sign-in-page"></a>Personalizar a página de entrada

Normalmente, se precisar de acesso baseado em navegador a seus aplicativos de nuvem e os serviços que assinado por sua empresa, use a página de entrada.

Se você aplicou alterações à sua página de entrada, pode demorar até uma hora para que as alterações apareçam.

Uma página de entrada com marca aparece apenas quando você as visitar um serviço com uma URL de locatário específicos como https://outlook.com/**contoso**com ou https://mail. **Contoso**. com.

Quando você visitar um serviço com URLs específicas não locatário (por exemplo: https://mail.office365.com), sem marca entrar aparece uma página. Nesse caso, a sua marca aparece depois que você inseriu sua ID de usuário ou que você selecionou um bloco de usuário.

> [AZURE.NOTE]
>
- Seu nome de domínio deve aparecer como "Ativo" na parte de **domínios** do portal do Azure em que você configurou marca. Para obter mais informações, consulte [Adicionar nomes de domínio personalizado](active-directory-domains-add-azure-portal.md).
- Página de entrada de identidade visual não são transferidas para o sinal do consumidor na página da Microsoft. Se você entrar com uma conta da Microsoft, você pode ver uma lista com sua identidade visual dos blocos de usuário processado pelo Azure AD, mas não se aplicam a identidade visual da sua organização para a página de entrada do conta Microsoft.

Na sua página de entrada, a caixa de seleção **Mantenha-me conectado** permite que um usuário permanecer conectado quando eles feche e abra novamente o seu navegador. 

   ![Mantenha-me conectado](./media/active-directory-branding-custom-signon-azure-portal/01.png)

Ele não afeta duração da sessão. Você pode ocultar a caixa de seleção na página de entrada do Active Directory do Azure.
Se a caixa de seleção é exibida depende da configuração **Mantenha-me conectado desativado**.

   ![Mantenha-me conectado](./media/active-directory-branding-custom-signon-azure-portal/02.png)


Para ocultar a caixa de seleção, configure esta configuração para **Sim**. 

> [AZURE.NOTE] Alguns recursos do SharePoint Online e o Office 2010 dependem de usuários sejam capazes de marcar esta caixa. Se você definir essa configuração para oculta, seus usuários poderá ver avisos adicionais e inesperados para entrar.




**Para adicionar a marca ao seu diretório da empresa:**

1.  Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2.  Selecione **mais serviços**, insira os **usuários e grupos** na caixa de texto e, em seguida, pressione **Enter**.

    ![Gerenciamento de usuários de abertura](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)

3. Na lâmina **usuários e grupos** , selecione a **marca da empresa**.

4. Sobre o **usuários e grupos - identidade visual da empresa** lâmina, selecione o comando **Editar** .

    ![Editar a identidade visual personalizada](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)

5. Modificar os elementos que você deseja personalizar. Todos os elementos são opcionais.

6. Clique em **Salvar**.

Pode demorar até uma hora para quaisquer alterações feitas para a página de entrada de identidade visual para que apareçam.

## <a name="next-steps"></a>Próximas etapas

[Adicionar a marca da empresa específicas de idioma](active-directory-branding-localize-azure-portal.md)
