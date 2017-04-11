<properties
    pageTitle="Publicar aplicativos com o Proxy de aplicativo do Azure AD | Microsoft Azure"
    description="Publica aplicativos de locais na nuvem com o Proxy de aplicativo do Azure AD."
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
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publicar aplicativos usando o Proxy de aplicativo do Azure AD

Proxy de aplicativo do Azure AD ajuda você a oferecer suporte a trabalhadores remotos publicando aplicativos de locais a serem acessados pela internet. Nesse ponto, você já deve ter [ativado o Proxy de aplicativo no portal de clássico do Azure](active-directory-application-proxy-enable.md). Este artigo conduz você pelas etapas para publicar aplicativos que são executados em sua rede local e fornecem acesso remoto seguro de fora da sua rede. Depois de concluir este artigo, você estará pronto para configurar o aplicativo com informações personalizadas ou requisitos de segurança.

> [AZURE.NOTE] Proxy de aplicativo é um recurso que está disponível somente se você atualizou para o Premium ou edição básica do Active Directory do Azure. Para obter mais informações, consulte [edições do Active Directory do Azure](active-directory-editions.md).

## <a name="publish-an-app-using-the-wizard"></a>Publicar um aplicativo usando o Assistente

1. Entrar como um administrador no [portal clássico Azure](https://manage.windowsazure.com/).
2. Vá para o Active Directory e selecione o diretório em que você ativou o Proxy de aplicativo.

    ![Active Directory - ícone](./media/active-directory-application-proxy-publish/ad_icon.png)

3. Clique na guia **aplicativos** e, em seguida, clique no botão **Adicionar** na parte inferior da tela

    ![Adicionar aplicativo](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

4. Selecione **publicar um aplicativo que poderá ser acessado de fora da sua rede**.

    ![Publicar um aplicativo que estará acessível de fora da sua rede](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

5. Forneça as seguintes informações sobre seu aplicativo:

    - **Nome**: O nome amigável para o seu aplicativo. Ele deve ser exclusivo no diretório.
    - **URL interna**: O endereço que o conector de Proxy de aplicativo usa para acessar o aplicativo de dentro de sua rede privada. Você pode fornecer um caminho específico no servidor back-end para publicar, enquanto o resto do servidor é não publicado. Dessa forma, você pode publicar sites diferentes no mesmo servidor e dê a cada um deles suas próprias regras de acesso e nome.

        > [AZURE.TIP] Se você publicar um caminho, verifique se ele inclui todos os necessárias imagens, scripts e folhas de estilo para seu aplicativo. Por exemplo, se seu aplicativo estiver em https://yourapp/app e usa imagens localizadas em https://yourapp/media, você deve publicar https://yourapp/ como o caminho.

    - **Método de pré-autenticação**: como Proxy de aplicativo verifica os usuários antes de dar acesso ao seu aplicativo. Escolha uma das opções no menu suspenso.

        - Active Directory do Azure: Proxy de aplicativo redireciona os usuários para entrar com Azure AD, que autentica suas permissões para o diretório e o aplicativo.
        - Passagem: Os usuários não precisam autenticar para acessar o aplicativo.

    ![Propriedades de aplicativo](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  

6. Para concluir o assistente, clique na marca de seleção na parte inferior da tela. O aplicativo agora é definido no Azure AD.


## <a name="assign-users-and-groups-to-the-application"></a>Atribuir usuários e grupos para o aplicativo

Em ordem para seus usuários acessar seu aplicativo publicado, você precisa atribuí-las individualmente ou em grupos. (Lembre-se de atribuir acesso, muito.) Isso requer que cada usuário tem uma licença para o Azure básica ou superior. Você pode atribuir licenças individualmente ou em grupos. Consulte [Atribuindo usuários a um aplicativo](active-directory-applications-guiding-developers-assigning-users.md) para obter mais detalhes. 

Para aplicativos que exijam autenticação prévia, isso concede permissões para usar o aplicativo. Para os aplicativos que não exijam autenticação prévia, podem ainda ser atribuída a usuários do aplicativo para que ele apareça na sua lista de aplicativos, como Meus aplicativos.

1. Após concluir o Assistente adicionar aplicativos, você pode ver a página de início rápido para seu aplicativo. Para gerenciar quem tem acesso ao aplicativo, selecione **usuários e grupos**.

    ![Início rápido do aplicativo Proxy atribuir aos usuários - captura de tela](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. Procurar grupos específicos no seu diretório ou mostrar todos os seus usuários. Para exibir os resultados da pesquisa, clique na marca de seleção.

    ![Procurar grupos ou usuários - captura de tela](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. Selecione cada usuário ou grupo que você deseja atribuir a este aplicativo e clique em **atribuir**. Você será solicitado a confirmar esta ação.

> [AZURE.NOTE] Para aplicativos de autenticação integrada do Windows, você pode atribuir somente os usuários e grupos que são sincronizados em seu local Active Directory. Os usuários que entrar com uma conta da Microsoft e convidados não podem ser atribuídos para aplicativos publicados com Proxy de aplicativo do Azure Active Directory. Verifique se os seus usuários entrar com credenciais que fazem parte do mesmo domínio que o aplicativo que você está publicando.

## <a name="test-your-published-application"></a>Testar seu aplicativo publicado

Depois de publicar seu aplicativo, você pode testar-out, navegando até a URL que você publicou. Certifique-se de que você pode acessá-lo, ele processa corretamente e que tudo funciona como esperado. Se você tiver problemas ou recebe uma mensagem de erro, tente o [guia de solução de problemas](active-directory-application-proxy-troubleshoot.md).

## <a name="configure-your-application"></a>Configurar seu aplicativo

Você pode modificar aplicativos publicados ou configurar opções avançadas na página Definir. Nesta página, você pode personalizar o seu aplicativo alterando o nome ou o upload de um logotipo. Você também pode gerenciar regras de acesso, como o método de autenticação prévia ou a autenticação multifator.

![Configuração avançada](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


Depois que você publicar aplicativos usando Azure Active Directory aplicativo Proxy, eles aparecem na lista de aplicativos no Azure AD e você poderá gerenciá-los lá.

Se você desabilitar os serviços de Proxy de aplicativo após publicar aplicativos, elas não são mais acessíveis a partir de fora da sua rede privada. Isso não exclui os aplicativos.

Para exibir um aplicativo e certifique-se de que ele é acessível, clique duas vezes no nome do aplicativo. Se o serviço de Proxy de aplicativo está desabilitado e o aplicativo não está disponível, uma mensagem de aviso aparece na parte superior da tela.

Para excluir um aplicativo, selecione um aplicativo na lista e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- [Publicar aplicativos usando seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
- [Ativar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabalhando com aplicativos de reconhecimento de declarações](active-directory-application-proxy-claims-aware-apps.md)

Para as últimas notícias e atualizações, confira o [blog do Proxy de aplicativo](http://blogs.technet.com/b/applicationproxyblog/)
