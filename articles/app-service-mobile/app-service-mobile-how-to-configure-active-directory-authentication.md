<properties
    pageTitle="Como configurar a autenticação do Active Directory do Azure para seu aplicativo de serviços de aplicativo"
    description="Saiba como configurar a autenticação do Active Directory do Azure para seu aplicativo de serviços de aplicativo."
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Como configurar seu aplicativo de serviço de aplicativo para usar o logon do Active Directory do Azure

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar serviços de aplicativo do Azure para usar o Active Directory do Azure como um provedor de autenticação.

## <a name="express"> </a>Configurar Azure Active Directory usando configurações express

13. No [portal do Azure], navegue até seu aplicativo. Clique em **configurações**e em seguida **Autenticação/autorização**.

14. Se a autenticação / autorização recurso não está habilitado, ative a opção para **em**.

15. Clique **Azure Active Directory**e, em seguida, clique em **Express** em **Modo de gerenciamento**.

16. Clique em **Okey** para registrar o aplicativo do Azure Active Directory. Isso criará um novo registro. Se quiser escolher um registro existente, em vez disso, clique em **Selecionar um aplicativo existente** e, em seguida, procure o nome de um registro criado anteriormente em seu locatário.
Clique no registro para selecioná-la e clique em **Okey**. Clique em **Okey** na lâmina de configurações do Active Directory do Azure.

    ![][0]

    Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado para o conteúdo do site e as APIs. Você deve autorizar usuários em seu código de aplicativo.

17. (Opcional) Para restringir o acesso ao seu site para somente os usuários autenticados pelo Active Directory do Azure, defina **fazer quando solicitação não é autenticada** como **fazer logon com o Active Directory do Azure**. Isso requer que todas as solicitações de ser autenticada, e todas as solicitações não autenticadas são redirecionadas para o Active Directory do Azure para autenticação.

17. Clique em **Salvar**.

Agora você está pronto para usar o Active Directory do Azure para autenticação em seu aplicativo.

## <a name="advanced"> </a>(Método alternativo) configurar manualmente o Azure Active Directory com configurações avançadas
Você também pode optar por fornecer configurações manualmente. Essa é a solução preferencial se o locatário AAD que deseja usar for diferente do locatário com o qual você entrar no Azure. Para concluir a configuração, você deve primeiro criar um registro no Active Directory do Azure e, em seguida, você deve fornecer alguns dos detalhes registro ao aplicativo de serviço.

### <a name="register"> </a>Registrar seu aplicativo com o Active Directory do Azure

1. Faça logon no [portal do Azure]e navegue até seu aplicativo. Copie a **URL**. Você usará isso para configurar seu aplicativo do Azure Active Directory.

3. Entre [portal de clássico Azure] e navegue para o **Active Directory**.

    ![][2]

4. Selecione seu diretório e selecione a guia de **aplicativos** na parte superior. Clique em **Adicionar** na parte inferior para criar um novo registro de aplicativo.

5. Clique em **Adicionar um aplicativo minha organização está desenvolvendo**.

6. No Assistente para adicionar aplicativos, insira um **nome** para o seu aplicativo e clique no tipo de **API do Web Application e/ou da Web** . Clique para continuar.

7. Na caixa **URL de SIGN-ON** , cole a URL do aplicativo que você copiou anteriormente. Insira essa mesma URL na caixa de **URI de ID do aplicativo** . Clique para continuar.

8. Depois que o aplicativo tenha sido adicionado, clique na guia **Configurar** . Edite a **URL de resposta** em **logon único** para ser a URL de seu aplicativo acrescentado com o caminho, _/.auth/login/aad/callback_. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Certifique-se de que você está usando o esquema de HTTPS.

    ![][3]

9. Clique em **Salvar**. Copie a **ID do cliente** para o aplicativo. Você irá configurar seu aplicativo usá-la mais tarde.

10. Na barra de comando inferior, clique em **Pontos de extremidade do modo de exibição**e copie a URL do **Documento de metadados de Federação** e baixar esse documento ou navegar até ela em um navegador.

11. Dentro do elemento de **EntityDescriptor** raiz, deve haver um atributo **entityID** do formulário `https://sts.windows.net/` seguido de um GUID específico para seu locatário (chamado de "ID de locatário"). Copiar esse valor - ele servirá como sua **URL do emissor**. Você irá configurar seu aplicativo usá-la mais tarde.

### <a name="secrets"> </a>Adicionar Azure informações do Active Directory ao seu aplicativo

13. Volta do [portal do Azure], navegue até seu aplicativo. Clique em **configurações**e em seguida **Autenticação/autorização**.

14. Se o recurso de autenticação/autorização não estiver habilitado, habilite a alternar para **em**.

15. Clique em **Active Directory do Azure**e, em seguida, clique em **Avançado** em **Modo de gerenciamento**. Cole o valor de identificação de cliente e a URL do emissor que você obteve anteriormente. Clique em **Okey**.

    ![][1]

    Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado para o conteúdo do site e as APIs. Você deve autorizar usuários em seu código de aplicativo.

17. (Opcional) Para restringir o acesso ao seu site para somente os usuários autenticados pelo Active Directory do Azure, defina **fazer quando solicitação não é autenticada** como **fazer logon com o Active Directory do Azure**. Isso requer que todas as solicitações de ser autenticada, e todas as solicitações não autenticadas são redirecionadas para o Active Directory do Azure para autenticação.

17. Clique em **Salvar**.

Agora você está pronto para usar o Active Directory do Azure para autenticação em seu aplicativo.

## <a name="optional-configure-a-native-client-application"></a>(Opcional) Configurar um aplicativo cliente nativo

Active Directory do Azure também permite que você registrar clientes nativos, que fornece um controle maior sobre permissões de mapeamento. Isso é necessário se você desejar executar logon usando uma biblioteca como a **Biblioteca de autenticação do Active Directory**.

1. Navegue até o **Active Directory** no [Azure portal clássico].

2. Selecione seu diretório e selecione a guia de **aplicativos** na parte superior. Clique em **Adicionar** na parte inferior para criar um novo registro de aplicativo.

3. Clique em **Adicionar um aplicativo minha organização está desenvolvendo**.

4. No Assistente para adicionar aplicativos, insira um **nome** para o seu aplicativo e clique no tipo de **Aplicativo cliente nativo** . Clique para continuar.

5. Na caixa **Redirecionar URI** , insira o ponto de extremidade de _/.auth/login/done_ do seu site, usando o esquema HTTPS. Este valor deve ser semelhante ao _https://contoso.azurewebsites.net/.auth/login/done_. Se criar um aplicativo do Windows, em vez disso, use o [pacote SID](app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como o URI.

6. Depois que o aplicativo nativo tenha sido adicionado, clique na guia **Configurar** . Localizar a **ID do cliente** e anote esse valor.

7. Rolar a página para baixo até a seção **permissões para outros aplicativos** e clique em **Adicionar aplicativo**.

8. Procure o aplicativo da web que você registrou anteriormente e clique no ícone de sinal de adição. Clique na seleção para fechar a caixa de diálogo. Se o aplicativo da web não pode ser encontrado, navegue até seu registro e adicionar um novo URL de resposta (por exemplo, a versão HTTP da URL do atual), clique em Salvar e, em seguida, repita essas etapas - o aplicativo deverá ser mostrada na lista.

9. Na nova entrada que você acabou de adicionar, abra a lista suspensa de **Permissões delegada** e selecione **Access (appName)**. Clique em **Salvar**.

Você já configurou um aplicativo de cliente nativo que pode acessar o seu aplicativo de serviço de aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Portal do Azure]: https://portal.azure.com/
[Azure portal clássico]: https://manage.windowsazure.com/
[alternative method]:#advanced
