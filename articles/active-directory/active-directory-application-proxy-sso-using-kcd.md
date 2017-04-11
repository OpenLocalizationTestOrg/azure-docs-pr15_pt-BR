<properties
    pageTitle="Logon único com o Proxy de aplicativo | Microsoft Azure"
    description="Discute como fornecer o logon único usando o Proxy de aplicativo do Azure AD."
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
    ms.date="08/10/2016"
    ms.author="kgremban"/>


# <a name="single-sign-on-with-application-proxy"></a>Logon único com o Proxy de aplicativo

Logon único é um elemento fundamental do Proxy de aplicativo do Azure AD. Ele fornece a melhor experiência do usuário com as seguintes etapas:

1. Um usuário entra na nuvem  
2. Todas as validações de segurança acontecem na nuvem (pré-autenticação)  
3. Quando a solicitação é enviada para o aplicativo local, o conector de Proxy de aplicativo representa o usuário. O aplicativo de back-end pensa que este é um usuário normal provenientes de um dispositivo de domínio.

![Diagrama de acesso do usuário final, por meio de Proxy do aplicativo, à rede corporativa](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Proxy de aplicativo do Azure AD ajuda você a fornecem uma experiência de logon único (SSO) para seus usuários. Use as instruções a seguir para publicar seus aplicativos usando o SSO:


## <a name="sso-for-on-prem-iwa-apps-using-kcd-with-application-proxy"></a>SSO para aplicativos IWA local usando KCD com Proxy de aplicativo
Você pode habilitar logon único para os aplicativos usando autenticação do Windows integrada (IWA) dando permissão de conectores de Proxy de aplicativo do Active Directory para representar usuários e enviar e receber tokens em seu nome.


### <a name="network-diagram"></a>Diagrama de rede

Este diagrama explica o fluxo quando um usuário tenta acessar um aplicativo de local que usa IWA.

![Diagrama de fluxo de autenticação do Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. O usuário insere a URL para acessar o aplicativo de local por meio de Proxy de aplicativo.
2. Proxy de aplicativo redireciona a solicitação para serviços de autenticação do Azure AD para preauthenticate. Neste ponto, Azure AD se aplica a qualquer aplicável políticas de autenticação e autorização, como a autenticação multifator. Se o usuário for validado, o Azure AD cria um símbolo e o envia para o usuário.
3. O usuário passa o token para o Proxy de aplicativo.
4. Proxy de aplicativo valida o token recupera o nome Principal de usuário (UPN) dele e, em seguida, envia a solicitação, o nome UPN e o nome Principal de serviço (SPN) ao conector por meio de um canal seguro duplamente autenticado.
5. O conector executa negociação de Kerberos restringido delegação (KCD) com o local AD, representando o usuário para obter um token de Kerberos para o aplicativo.
6. Active Directory envia o token de Kerberos para o aplicativo ao conector.
7. O conector envia a solicitação original para o servidor de aplicativo, usando o token de Kerberos que ele recebeu do AD.
8. O aplicativo envia a resposta ao conector que é retornado para o serviço de Proxy do aplicativo e finalmente ao usuário.

### <a name="prerequisites"></a>Pré-requisitos

Antes de você começar a usar o SSO para Proxy de aplicativo, verifique se que seu ambiente está pronto com as seguintes configurações e configurações:

- Seus aplicativos, como os aplicativos Web do SharePoint, estão definidos para usar autenticação Windows integrada. Para obter mais informações consulte [Ativar o suporte para autenticação Kerberos](https://technet.microsoft.com/library/dd759186.aspx)ou do SharePoint, consulte [Planejar a autenticação Kerberos no SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).

- Todos os seus aplicativos têm nomes principais de serviço.

- O servidor que executa o conector e o servidor executando o aplicativo são domínio associado e parte do mesmo domínio ou domínios confiáveis. Para obter mais informações sobre a associação de domínio, consulte [ingressar em um computador em um domínio](https://technet.microsoft.com/library/dd807102.aspx).

- O servidor que executa o conector tem acesso de leitura a TokenGroupsGlobalAndUniversal para os usuários. Esta é uma configuração padrão que pode ter sido afetada pelo ambiente de proteção de segurança. Obter mais ajuda com isso no [KB2009157](https://support.microsoft.com/en-us/kb/2009157).

### <a name="active-directory-configuration"></a>Configuração do Active Directory

Configuração do Active Directory varia, dependendo se o conector de Proxy de aplicativo e o servidor publicado estiverem no mesmo domínio ou não.

#### <a name="connector-and-published-server-in-the-same-domain"></a>Conector e servidor publicado no mesmo domínio

1. No Active Directory, vá para **Ferramentas** > **usuários e computadores**.
2. Selecione o servidor que executa o conector.
3. Clique com botão direito e selecione **Propriedades** > **delegação**.
4. Selecione **Confiar no computador para delegação apenas aos serviços especificados** e em **serviços aos quais esta conta pode apresentar credenciais delegadas**, adicione o valor para a identidade SPN do servidor do aplicativo.
5. Isso permite que o conector de Proxy de aplicativo para representar usuários no AD contra os aplicativos definidos na lista.

![Captura de tela de janela de propriedades do servidor – o conector](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-published-server-in-different-domains"></a>Conector e servidor publicado em domínios diferentes

1. Para obter uma lista de pré-requisitos para trabalhar com KCD em domínios, consulte [Delegação restrita Kerberos em domínios](https://technet.microsoft.com/library/hh831477.aspx).
2. No Windows 2012 R2, use o `principalsallowedtodelegateto` propriedade no servidor conector para habilitar o Proxy de aplicativo para o representante para o servidor de conector, onde está o servidor publicado `sharepointserviceaccount` e o servidor delegando é `connectormachineaccount`.

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE] `sharepointserviceaccount`pode ser a conta da máquina SPS ou uma conta de serviço em que o pool de aplicativo SPS está em execução.


### <a name="azure-classic-portal-configuration"></a>Configuração de portal clássica Azure

1. Publica seu aplicativo de acordo com as instruções descritas em [publicar aplicativos com o Proxy de aplicativo](active-directory-application-proxy-publish.md). Certifique-se de selecionar o **Active Directory do Azure** como o **Método de pré-autenticação**.
2. Quando seu aplicativo for exibido na lista de aplicativos, selecione-o e clique em **Configurar**.
3. Em **Propriedades**, defina **Interno método de autenticação** para **Autenticação integrada do Windows**.  
  ![Configuração de aplicativo avançado](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. Insira o **SPN de aplicativo interno** do servidor do aplicativo. Neste exemplo, o SPN para nosso aplicativo publicado é http/lob.contoso.com.  

>[AZURE.IMPORTANT] Se seu UPN local e o nome UPN do Azure Active Directory não são idênticos, você precisará configurar o [delegada identidade de login](#delegated-login-identity) para pré-autenticação trabalhar.

|  |  |
| --- | --- |
| Método de autenticação interna | Se você usar o Azure AD para pré-autenticação, você pode definir um método de autenticação interna para permitir que seus usuários a vantagem de logon único (SSO) para este aplicativo. <br><br> Selecione **Autenticação integrada do Windows** (IWA) se o aplicativo usa IWA e você pode configurar Kerberos restringido delegação (KCD) para habilitar o SSO para este aplicativo. Aplicativos que usam IWA devem ser configurados com KCD, caso contrário Proxy do aplicativo não será possível publicar esses aplicativos. <br><br> Selecione **Nenhum** se seu aplicativo não usar IWA. |
| Aplicativo interno SPN | Este é o nome de capital de serviço (SPN) do aplicativo interno conforme configurado no local Azure AD. O SPN é usado pelo aplicativo Proxy conector para buscar tokens de Kerberos para o aplicativo usando KCD. |


## <a name="sso-for-non-windows-apps"></a>SSO para aplicativos não-Windows
O fluxo de delegação de Kerberos no Proxy de aplicativo do Azure AD inicia quando Azure AD autentica o usuário na nuvem. Depois que a solicitação chega no local, o Azure AD Application Proxy Connector emite um tíquete Kerberos em nome de usuário interagindo com o Active Directory local. Esse processo é conhecido como Kerberos restringido delegação (KCD). Na próxima fase, uma solicitação é enviada para o aplicativo de back-end com essa permissão Kerberos. Há um número de protocolos que definem como enviar tais solicitações. A maioria dos servidores não-Windows esperar negociação/SPNego que agora é suportado em Proxy de aplicativo do Azure AD.

![Diagrama de SSO não-Windows](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### <a name="delegated-login-identity"></a>Identidade de login delegados
Identidade de login delegada ajuda você a lidar com dois cenários de login diferentes:

- Aplicativos não-Windows que normalmente obter identidade de usuário no formulário de um nome de usuário ou o nome de conta SAM, não um endereço de email (username@domain).
- Configurações de logon alternativo onde o UPN no Azure AD e o UPN no seu local Active Directory são diferentes.

Com o Proxy de aplicativo, você pode selecionar quais identidade usar para obter a permissão Kerberos. Esta configuração é por aplicativo. Algumas dessas opções são adequadas para sistemas que não aceitam o formato do endereço de email, as outras pessoas foram projetadas para logon alternativo.

![Captura de tela de parâmetro de identidade login delegada](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Se a identidade de login delegada é usada, o valor talvez não seja exclusivo para todos os domínios ou florestas em sua organização. Você pode evitar esse problema publicando esses aplicativos duas vezes com dois grupos diferentes de conector. Como cada aplicativo tem uma audiência de usuário diferentes, você pode ingressar seus conectores para um domínio diferente.


## <a name="working-with-sso-when-on-premises-and-cloud-identities-are-not-identical"></a>Trabalhando com SSO quando locais e nuvem identidades não são idênticas
A menos que configurado caso contrário, o Proxy de aplicativo pressupõe que os usuários tenham a mesma identidade na nuvem e local. Você pode configurar, para cada aplicativo, qual identidade deve ser usada ao executar logon único.  

Esse recurso permite muitas empresas que têm diferentes identidades local e nuvem ter SSO da nuvem ao local aplicativos sem exigir que os usuários insiram diferentes nomes de usuário e senhas. Isso inclui organizações que:

- Tiver vários domínios internamente (joe@us.contoso.com, joe@eu.contoso.com) e um domínio único na nuvem (joe@contoso.com).

- Tem o nome de domínio não roteáveis internamente (joe@contoso.usa) e um legal na nuvem.

- Não use nomes de domínio internamente (Jorge)

- Use aliases diferentes local e na nuvem. Por exemplo joe-johns@contoso.comvs.joej@contoso.com  

Ele também ajudará com aplicativos que não aceitar endereços na forma de endereço de email, que é um cenário muito comum para servidores de back-end não-Windows.


### <a name="setting-sso-for-different-cloud-and-on-prem-identities"></a>Configurando SSO para diferentes identidades de nuvem e local

1. Definir configurações de Azure AD Connect para que a identidade principal será o endereço de email (email). Isso é feito como parte do processo de personalizar, alterando o campo **Nome do usuário principal** nas configurações de sincronização. Observe que essas configurações também determinam como os usuários logon no Office 365, Windows10 dispositivos e outros aplicativos que usam o Azure AD como seu armazenamento de identidade.  
  ![Identificando a captura de tela de usuários - menu suspenso UPN](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Em configurações de configuração do aplicativo para o aplicativo que você deseja modificar, selecione a **Identidade de Login delegada** a ser usado:
  - Nome de princípio de usuário:joe@contoso.com  
  - Nome de princípio de usuário alternativo:joed@contoso.local  
  - Parte do nome de usuário nome de usuário princípio: José  
  - Parte do nome de usuário alternativo princípio do nome de usuário: joed  
  - Nome de conta SAM local: configuração dependendo do controlador de domínio local

  ![Captura de tela de menu suspenso de identidade login delegada](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)  

### <a name="troubleshooting-sso-for-different-identities"></a>Solução de problemas SSO para diferentes identidades
Se houver um erro no processo de SSO ele aparecerá no log de eventos de máquina conector conforme explicado em [solução de problemas](active-directory-application-proxy-troubleshoot.md).
Mas, em alguns casos, a solicitação será com êxito enviada para o aplicativo de back-end enquanto este aplicativo responderá em várias outras respostas HTTP. Nesses casos de solução de problemas deve começar examinando o número de evento 24029 na máquina conector no log de eventos de sessão de Proxy do aplicativo. A identidade do usuário que foi usada para delegação será exibido no campo "usuário" nos detalhes do evento. Para ativar o log de sessão, selecione **Mostrar analítico e logs de depuração** no menu Exibir do Visualizador de eventos.


## <a name="see-also"></a>Consulte também

- [Publicar aplicativos com o Proxy de aplicativo](active-directory-application-proxy-publish.md)
- [Solucionar problemas com o Proxy de aplicativo](active-directory-application-proxy-troubleshoot.md)
- [Trabalhando com aplicativos de reconhecimento de declarações](active-directory-application-proxy-claims-aware-apps.md)
- [Habilitar acesso condicional](active-directory-application-proxy-conditional-access.md)

Para as últimas notícias e atualizações, confira o [blog do Proxy de aplicativo](http://blogs.technet.com/b/applicationproxyblog/)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg
