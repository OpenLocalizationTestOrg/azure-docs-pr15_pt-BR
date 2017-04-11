<properties
    pageTitle="Azure B2C diretório ativo: Registro de aplicativos | Microsoft Azure"
    description="Como registrar seu aplicativo com o Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-register-your-application"></a>Azure B2C diretório ativo: Registrar seu aplicativo

## <a name="prerequisite"></a>Pré-requisito

Para criar um aplicativo que aceita consumidor inscrição e entrar, primeiro é necessário registrar o aplicativo com um locatário do Azure Active Directory B2C. Obter seu próprio locatário usando as etapas descritas em [criar um locatário do Azure AD B2C](active-directory-b2c-get-started.md). Depois de seguir todas as etapas no artigo, você terá a lâmina de recursos B2C fixada sua Startboard.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Navegue até a lâmina de recursos B2C

Se você tiver a lâmina de recursos B2C fixada sua Startboard, você verá a lâmina assim que você entrar no [portal do Azure](https://portal.azure.com/) como o Administrador Global do locatário B2C.

Você também pode acessar o blade clicando em **Procurar** e, em seguida, **Azure AD B2C** no painel de navegação à esquerda no [portal do Azure](https://portal.azure.com/).

> [AZURE.IMPORTANT] Você precisa ser um Administrador Global do locatário B2C sejam capazes de acessar a lâmina de recursos B2C. Um Administrador Global de qualquer outro locatário ou um usuário de qualquer locatário não é possível acessá-lo.  Você pode alternar para seu locatário B2C usando o seletor de exibição de locatários no canto superior direito do Portal do Azure.

## <a name="register-an-application"></a>Registrar um aplicativo

1. Na lâmina B2C recursos no portal do Azure, clique em **aplicativos**.
2. Clique em **+ Adicionar** na parte superior da lâmina.
3. Insira um **nome** para o aplicativo que descreverá seu aplicativo para consumidores. Por exemplo, você poderia inserir "Contoso B2C aplicativo".
4. Se você estiver escrevendo um aplicativo baseado na web, alterne a chave **incluir web app / web API** como **Sim**. As **URLs de resposta** são pontos de extremidade onde Azure AD B2C retornarão qualquer tokens que seu aplicativo solicita. Por exemplo, insira `https://localhost:44321/`. Se seu aplicativo web também chamando alguns web API protegido por Azure AD B2C, convém criar um **Aplicativo segredo** também clicando no botão **Gerar chave** .

    > [AZURE.NOTE] Um **Segredo do aplicativo** é uma credencial de segurança importantes e devem ser protegidos adequadamente.

5. Se você estiver escrevendo um aplicativo móvel, alterne a chave de **clientes nativos incluir** como **Sim**. Copiar até o padrão **Redirecionar URI** que será criado automaticamente para você.
6. Clique em **criar** para registrar seu aplicativo.
7. Clique no aplicativo que você acabou de criar e copie a **ID de aplicativo de cliente** exclusivo que você usará posteriormente no seu código.

> [AZURE.IMPORTANT] Aplicativos criados na lâmina recursos B2C precisam gerenciado no mesmo local. Se você editar B2C aplicativos usando o PowerShell ou outro portal, eles ficam sem suporte e provavelmente não funcionará com o Azure AD B2C.

## <a name="build-a-quick-start-application"></a>Criar um aplicativo de início rápido

Agora que você tem um aplicativo registrado com Azure AD B2C, você pode concluir um dos nossos tutoriais de início rápido para começar a trabalhar com. Aqui estão algumas recomendações:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]
