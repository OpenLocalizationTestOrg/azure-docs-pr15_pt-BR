<properties
    pageTitle="Segurança OAUTH em SaaS conectores e aplicativos de API | Azure"
    description="Leia mais sobre a segurança OAUTH na conectores e aplicativos da API do serviço de aplicativo do Azure; arquitetura de microservices; SaaS"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="dwrede"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="mandia"/>


# <a name="learn-about-oauth-security-in-saas-connectors"></a>Saiba mais sobre segurança OAUTH do SaaS conectores

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2014-12-01-visualização de aplicativos de lógica.

Muitas do Software como um conectores de serviço (SaaS) como exigem que os usuários autenticados usando o protocolo OAUTH Facebook, Twitter, DropBox e assim por diante.  Quando você usa esses conectores SaaS de lógica de aplicativos, fornecemos uma experiência de usuário simplificada onde você clica em "Autorizar" no designer de aplicativos de lógica. Quando você **Autorizar**, são perguntado para entrar em (se não estiver) e fornecer consentimento para se conectar ao serviço SaaS em seu nome. Depois que você fornecer consentimento e autorizar, seus aplicativos de lógica poderá acessar esses serviços SaaS.

## <a name="create-your-own-saas-app"></a>Criar seu próprio aplicativo SaaS
Esta experiência simplificada é possível porque criamos anteriormente e registrado nosso aplicativo nesses serviços SaaS.  Em alguns casos, talvez você queira registrar e usar seu próprio aplicativo.  Isso é necessário, por exemplo, quando você quiser usar esses conectores SaaS em seus aplicativos personalizados. Este exemplo usa o conector DropBox, mas o processo é o mesmo para todos os conectores que dependem OAUTH.

Mesmo no contexto de aplicativos de lógica, você pode usar seu próprio aplicativo em vez de usar o aplicativo padrão que fornecemos. Se o botão "Autorizar" falhar ao conectar, tente criar seu próprio aplicativo. A seguinte lista estas etapas para o conector Twitter:

1. Abra o conector do Twitter no portal do Azure preview. Vá para **Procurar** > **API aplicativos**. Selecione o conector Twitter:  
    ![][1]

2. Selecione **configurações** > **autenticação**:  
    ![][2]

3. Copie o valor de **URI de redirecionamento** :  
    ![][3]

4. Vá para [Twitter](http://apps.twitter.com) e **criar um novo aplicativo**. Na propriedade **URL de retorno de chamada** , cole o valor de **URI redirecionar** copiado do seu conector Twitter: ![][4]  
5. Quando seu aplicativo do Twitter for criado, selecione **chave e Tokens de acesso**. Copie estes valores.
6. Em suas configurações de autenticação de conector do Twitter, cole esses valores nas propriedades **ID do cliente** e **Segredo cliente** :   
    ![][5]  
7. Salve suas configurações de conector.  

Agora, você deve ser capaz de usar o conector de aplicativos de lógica. Quando você usa esse conector de aplicativos de lógica, ele usa seu aplicativo em vez do aplicativo padrão.  

> [AZURE.NOTE] Se você tiver autorizado um aplicativo anteriormente, você pode precisar autorize de novo aplicativo.


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png
