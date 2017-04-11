<properties
    pageTitle="registro de aplicativo v 2.0 | Microsoft Azure"
    description="Como registrar um aplicativo com a Microsoft para habilitar entrar e acessar serviços da Microsoft usando o ponto de extremidade de v 2.0"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Como registrar um aplicativo com o ponto de extremidade de v 2.0

Para criar um aplicativo que aceita MSA & Azure AD entrar, você precisará primeiro registrar um aplicativo com a Microsoft.  No momento, você não poderá usar qualquer aplicativos existentes que você tenha com Azure AD ou MSA - você precisará criar um novo.

> [AZURE.NOTE]
    Nem todos os cenários do Active Directory do Azure e recursos são compatíveis com o ponto de extremidade de v 2.0.  Para determinar se você deve usar o ponto de extremidade de v 2.0, leia sobre as [limitações da versão 2.0](active-directory-v2-limitations.md).

## <a name="visit-the-microsoft-app-registration-portal"></a>Visite o portal de registro do aplicativo Microsoft
Primeiras coisas primeiro - navegue até [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Este é um novo portal de registro de aplicativo onde você pode gerenciar seus aplicativos da Microsoft.

Entrar com seja uma pessoal ou trabalho ou escola conta da Microsoft.  Se você não tiver um, inscreva-se em uma nova conta pessoal. Ir adiante, não demora longa - vamos aguardar aqui.

Feito? Você deve agora estar vendo sua lista de aplicativos da Microsoft, que é provavelmente vazia.  Vamos alterar isso.

Clique em **Adicionar um aplicativo**e dê um nome.  O portal atribuirá seu aplicativo uma Id de aplicativo exclusivo que você usará posteriormente no seu código.  Se seu aplicativo inclui um componente de servidor que precisa tokens de acesso para chamando APIs (acha: Office, Azure ou sua própria web API), convém criar um **Aplicativo segredo** aqui também.
<!-- TODO: Link for app secrets -->

Em seguida, adicione as plataformas que seu aplicativo usará.

- Para aplicativos web baseado, forneça um **URI redirecionar** onde entrar mensagens podem ser enviadas.
- Para aplicativos móveis, copie para baixo o uri de redirecionamento padrão criado automaticamente para você.

Opcionalmente, você pode personalizar a aparência da sua página de entrada na seção do perfil.  Certifique-se de clicar em **Salvar** antes de passar.

> [AZURE.NOTE] Quando você cria um aplicativo usando [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), o aplicativo será registrado no locatário inicial da conta que você usa para entrar no portal do.  Isso significa que você não pode registrar um aplicativo em seu locatário do AD Azure usando uma conta da Microsoft.  Se desejar explicitamente registrar um aplicativo em um determinado locatário, entre com uma conta originalmente criada em locatário.

## <a name="build-a-quick-start-app"></a>Criar um aplicativo de início rápido
Agora que você tem um aplicativo do Microsoft, você pode concluir um dos nossos tutoriais de início rápido do v 2.0.  Aqui estão algumas recomendações:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]
