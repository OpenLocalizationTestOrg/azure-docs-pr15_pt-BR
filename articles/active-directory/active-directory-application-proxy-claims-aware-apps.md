<properties
    pageTitle="Trabalhando com requerimentos cientes aplicativos no Proxy de aplicativo"
    description="Discute como começar a trabalhar com o Proxy de aplicativo do Azure AD."
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



# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Trabalhando com requerimentos cientes aplicativos no Proxy de aplicativo

Aplicativos de reconhecimento de declarações realizar um redirecionamento para o serviço STS (Security Token), que por sua vez solicita credenciais do usuário por um token antes de redirecionar o usuário para o aplicativo. Para habilitar o Proxy de aplicativo trabalhar com esses redirecionamentos, as etapas a seguir precisam ser levados.

## <a name="prerequisites"></a>Pré-requisitos
Antes de executar este procedimento, certifique-se de que o STS o aplicativo de reconhecimento de declarações redireciona para está disponível fora da sua rede local.

## <a name="azure-classic-portal-configuration"></a>Configuração de portal clássica Azure

1. Publica seu aplicativo de acordo com as instruções descritas em [publicar aplicativos com o Proxy de aplicativo](active-directory-application-proxy-publish.md).
2. Na lista de aplicativos, selecione o aplicativo de reconhecimento de declarações e clique em **Configurar**.
3. Se você escolheu **passagem** como seu **Método de pré-autenticação**, verifique se selecionar **HTTPS** como seu esquema de **URL externo** .
4. Se você escolheu **Azure Active Directory** como seu **Método de pré-autenticação**, selecione **Nenhum** como seu **Método de autenticação interno**.


## <a name="adfs-configuration"></a>Configuração do ADFS

1. Abra o gerenciamento do ADFS.
2. Vá para **Contar festa confia**, clique com o botão direito no aplicativo do qual você está publicando com Proxy de aplicativo e escolha **Propriedades**.  
  ![Relações de confiança de terceiros terceira clique direito do mouse no nome do aplicativo - screentshot](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. Na guia **pontos de extremidade** , em **tipo de ponto de extremidade**, selecione **Web Services Federation**.
4. Em **Confiáveis de URL** , digite a URL que você inseriu no Proxy de aplicativo em **URL externa** e clique em **Okey**.  
  ![Adicionar um ponto de extremidade - definir valor de URL confiáveis - captura de tela](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="see-also"></a>Consulte também

- [Publicar aplicativos com o Proxy de aplicativo](active-directory-application-proxy-publish.md)
- [Ativar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Solucionar problemas com o Proxy de aplicativo](active-directory-application-proxy-troubleshoot.md)
- [Ativar os aplicativos cliente nativo interagir com aplicativos de proxy](active-directory-application-proxy-native-client.md)

Para as últimas notícias e atualizações, confira o [blog do Proxy de aplicativo](http://blogs.technet.com/b/applicationproxyblog/)
