<properties
pageTitle="Adicionar marca à sua página de entrada na visualização do Azure Active Directory da empresa específicas de idioma | Microsoft Azure"
description="Saiba como adicionar uma empresa específica do idioma identidade visual imagens e texto em uma página de entrada Azure"
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
ms.date="09/12/2016"
ms.author="curtand"/>

# <a name="add-language-specific-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Adicionar marca à sua página de entrada na visualização do Azure Active Directory da empresa específicas de idioma

Para evitar confusão, muitas empresas desejam aplicar uma aparência consistente em todos os sites e serviços que gerenciam. Visualização do Azure Active Directory fornece esse recurso, permitindo que você personalize a aparência da página entrar com o logotipo da empresa e esquemas de cores personalizado. [O que é no preview?](active-directory-preview-explainer.md) A página de entrada é a página que aparece quando você entra no Office 365 ou outros aplicativos baseados na web que estão usando o Azure AD como seu provedor de identidade. Você interage com esta página para inserir suas credenciais.

## <a name="customizing-the-sign-in-page-for-another-language"></a>Personalizar a página de entrada para outro idioma

Você pode adicionar elementos específicos de idioma à sua página de entrada personalizada somente se você já tiver criado uma página de entrada personalizada como descrito em [Adicionar marca à sua página de entrada da empresa](active-directory-branding-custom-signon-azure-portal.md). Você pode configurar uma página de entrada por diretório com um conjunto padrão de elementos personalizáveis. Depois que você configurou o conjunto padrão de elementos da página, você pode configurar versões mais para localidades diferentes. Você também pode misturar e corresponder vários elementos. Por exemplo, você pode:

- Crie um padrão **entrar imagem da página** que funciona para todas as culturas, em seguida, criar versões específicas para o inglês e francês. Quando você definir seus navegadores para um destes dois idiomas, é exibida a imagem de idiomas específicos, enquanto a ilustração padrão é exibido para todos os outros idiomas.

- Configure logotipos diferentes para sua organização (por exemplo, versões japonês ou hebraico).

Recomendamos que você mantenha o número de variações de idioma baixa, por razões de desempenho e manutenção.

**Para adicionar a marca ao seu diretório da empresa:**

1.  Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2.  Selecione **mais serviços**, insira os **usuários e grupos** na caixa de texto e, em seguida, pressione **Enter**.

    ![Gerenciamento de usuários de abertura](./media/active-directory-branding-localize-azure-portal/user-management.png)

3. Na lâmina **usuários e grupos** , selecione a **marca da empresa**.

4. Sobre o **usuários e grupos - identidade visual da empresa** lâmina, selecione o comando **Adicionar idioma** .

    ![Adicionar elementos de identidade visual específicos do idioma](./media/active-directory-branding-localize-azure-portal/add-language.png)

5. Modificar os elementos que você deseja personalizar. Todos os elementos são opcionais.

6. Clique em **Salvar**.

Pode demorar até uma hora para quaisquer alterações feitas para a página de entrada de identidade visual para que apareçam.

## <a name="next-steps"></a>Próximas etapas

[Adicionar marca à sua página de entrada da empresa](active-directory-branding-custom-signon-azure-portal.md)
