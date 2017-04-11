<properties
    pageTitle="Como habilitar a publicação de aplicativos cliente nativo com aplicativos de proxy | Microsoft Azure"
    description="Discute como habilitar aplicativos cliente nativo para se comunicar com o Azure AD Application Proxy Connector para fornecer acesso remoto seguro a seus aplicativos de local."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Como habilitar aplicativos cliente nativo interagir com aplicativos de proxy

Azure Proxy de aplicativo Active Directory é amplamente usado para publicar aplicativos de navegador como o SharePoint, o Outlook Web Access e a linha personalizada de aplicativos de negócios. Ele também pode ser usado para publicar aplicativos cliente nativo, que diferem dos aplicativos web porque eles são instalados em um dispositivo. Isso é feito por Azure AD tokens que são enviadas em cabeçalhos HTTP autorizar padrão emitido de suporte.

![Relação entre os usuários finais, Active Directory do Azure e aplicativos publicados](./media/active-directory-application-proxy-native-client/richclientflow.png)

O método recomendado para publicar esses aplicativos é usar a biblioteca de autenticação Azure AD, que cuida de todas as complicações de autenticação e dá suporte a muitos ambientes de cliente diferente. Proxy de aplicativo se encaixa no [Aplicativo nativo para cenário de API da Web](active-directory-authentication-scenarios.md#native-application-to-web-api). O processo para realizar isso é da seguinte maneira:

## <a name="step-1-publish-your-application"></a>Etapa 1: Publicar seu aplicativo

Publicar seu aplicativo de proxy como faria com qualquer outro aplicativo, atribuir aos usuários e dar a elas premium ou licenças básicas. Para obter mais informações, consulte [publicar aplicativos com o Proxy de aplicativo](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Etapa 2: Configurar seu aplicativo

Configure seu aplicativo nativo da seguinte maneira:

1. Entre portal do Azure clássico.
2. Selecione o ícone do Active Directory no menu à esquerda e selecione seu diretório.
3. No menu superior, clique em **aplicativos**. Se nenhuma aplicativos foram adicionados ao seu diretório, esta página mostrará somente o link **Adicionar um aplicativo** . Clique no link ou como alternativa, você pode clicar no botão **Adicionar** na barra de comando.
4. Na página **que você deseja fazer** , clique no link para **Adicionar um aplicativo minha organização está desenvolvendo**.
5. Na página **Conte-nos sobre seu aplicativo** , especifique um nome para seu aplicativo e escolha o **aplicativo cliente nativo**. Clique no ícone de seta para continuar.
6. Na página **informações sobre o aplicativo** , forneça o **URI redirecionar** para o aplicativo cliente nativo, clique na marca de seleção para concluir.

Seu aplicativo foi adicionado e você será levado para a página de início rápido para o seu aplicativo.

## <a name="step-3-grant-access-to-other-applications"></a>Etapa 3: Conceder acesso para outros aplicativos

Habilite o aplicativo nativo para ser expostos aos outros aplicativos no seu diretório:

1. No menu superior, clique em **aplicativos**, selecione o novo aplicativo nativo e clique em **Configurar**.
2. Role para baixo até a seção **permissões para outros aplicativos** . Clique no botão **Adicionar aplicativos** e selecione o aplicativo de proxy que você deseja conceder acesso de aplicativo nativo a e clique na marca de seleção no canto inferior direito. No menu suspenso **Delegada permissões** , selecione a nova permissão.

![Permissões para outra captura de tela de aplicativos - adicionar o aplicativo](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Etapa 4: Editar a biblioteca de autenticação do Active Directory

Edite o código de aplicativo nativo no contexto de autenticação do Active Directory autenticação biblioteca (ADAL) para incluir o seguinte:

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

As variáveis devem ser substituídas da seguinte maneira:

- **TenantId** podem ser encontradas no GUID na URL da página de **configuração** do aplicativo, logo após "/ diretório /".
- **URL de frontend** é a URL de front-end você inseriu no aplicativo Proxy e pode ser encontradas na página **configuração** do aplicativo proxy.
- **Id do cliente** do aplicativo nativo podem ser encontradas na página **Configurar** do aplicativo nativo.
- **Redirecionar URI do aplicativo nativo** podem ser encontradas na página **Configurar** do aplicativo nativo.

![Configurar o novo aplicativo nativo captura de tela da página](./media/active-directory-application-proxy-native-client/new_native_app.png)

Para obter mais informações sobre o fluxo de aplicativo nativo, consulte [aplicativo nativo Web API](active-directory-authentication-scenarios.md#native-application-to-web-api).


## <a name="see-also"></a>Consulte também

- [Publicar aplicativos usando seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
- [Habilitar acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabalhando com aplicativos de reconhecimento de declarações](active-directory-application-proxy-claims-aware-apps.md)
- [Ativar o logon único](active-directory-application-proxy-sso-using-kcd.md)

Para as últimas notícias e atualizações, confira o [blog do Proxy de aplicativo](http://blogs.technet.com/b/applicationproxyblog/)
