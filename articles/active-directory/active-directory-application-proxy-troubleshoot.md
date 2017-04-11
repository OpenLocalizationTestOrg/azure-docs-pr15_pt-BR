<properties
    pageTitle="Solucionar problemas de Proxy de aplicativo | Microsoft Azure"
    description="Discute como solucionar erros no Proxy de aplicativo do Azure AD."
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
    ms.date="08/19/2016"
    ms.author="kgremban"/>



# <a name="troubleshoot-application-proxy"></a>Solucionar problemas de Proxy de aplicativo

Se ocorrerem erros no acessando um aplicativo publicado ou em aplicativos de publicação, verifique as seguintes opções para ver se o Proxy de aplicativo do Microsoft Azure AD está funcionando corretamente:

- Abra o console Serviços do Windows e verificar se o serviço do **Conector de Proxy de aplicativo do Microsoft AAD** está ativado e em execução. Você talvez também queira examinar a página de propriedades de serviço de Proxy do aplicativo, conforme mostrado na imagem a seguir:  
  ![Captura de tela de janela de propriedades de conector de Proxy de aplicativo do Microsoft AAD](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)

- Abra o Visualizador de eventos e procure eventos de conector de Proxy de aplicativo no **Logs aplicativos e serviços** > **Microsoft** > **AadApplicationProxy** > **conector** > **administrador**.
- Se necessário, logs mais detalhadas estão disponíveis ativando analytics depuração logs e como ativar o log de sessão de conector de Proxy do aplicativo.

## <a name="the-page-is-not-rendered-correctly"></a>A página não é renderizada corretamente

Se você não estiver recebendo uma mensagem de erro específica, você ainda pode ter problemas com o seu aplicativo de processamento ou funcionando incorretamente. Isso pode ocorrer se você publicou o caminho de artigo, mas o aplicativo requer o conteúdo que existe fora de caminho.

Por exemplo, se você publicar o caminho https://yourapp/app, mas o aplicativo chama imagens no https://yourapp/media, elas não sejam processadas. Certifique-se de que publicar o aplicativo usando o caminho de nível mais alto que você precisar incluir todo o conteúdo relevante. Neste exemplo, seria http://yourapp/.

Se você mudar seu caminho para incluir conteúdo referenciado, mas ainda precisa de usuários para parar em um link mais profundo no caminho, consulte o postagem de blog [Definindo o link certo para aplicativos de Proxy de aplicativo no painel de acesso do Azure AD e inicializador de aplicativos do Office 365](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="general-errors"></a>Erros gerais

Erro | Descrição | Resolução
--- | --- | ---
Este aplicativo corporativo não pode ser acessado. Você não está autorizado a acessar este aplicativo. Falha de autorização. Certifique-se para atribuir ao usuário acesso a este aplicativo. | Você não pode ter atribuída ao usuário para este aplicativo. | Vá para a guia de **aplicativo** e, em **usuários e grupos**, atribua este usuário ou grupo para este aplicativo.
Este aplicativo corporativo não pode ser acessado. Você não está autorizado a acessar este aplicativo. Falha de autorização. Certifique-se de que o usuário tenha uma licença do Azure Active Directory Premium ou básico. | Os usuários podem receber este erro ao tentar acessar o aplicativo que você publicou se eles não foram atribuídos explicitamente com uma licença de Premium/Basic pelo administrador do assinante. | Acesse a guia do Active Directory **licenças** do assinante e certifique-se de que esse usuário ou grupo é atribuído um Premium ou licença básica.


## <a name="connector-troubleshooting"></a>Solução de problemas do conector
Se o registro falha durante a instalação do Assistente de conector, há duas maneiras de exibir o motivo da falha. Ou procure no log de eventos em **aplicativos e serviços Logs\Microsoft\AadApplicationProxy\Connector\Admin**ou execute o seguinte comando do Windows PowerShell.

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

| Erro | Descrição | Resolução |
| --- | --- | --- |
| Registro de conector falhou: Verifique se você ativou o Proxy de aplicativo no Portal de gerenciamento do Azure e que você digitou seu nome de usuário do Active Directory e a senha corretamente. Erro: 'um ou mais erros.' | Você fechou a janela de registro sem efetuar login no Azure AD. | Execute o Assistente de conector novamente e registrar o conector. |
| Registro de conector falhou: Verifique se você ativou o Proxy de aplicativo no Portal de gerenciamento do Azure e que você digitou seu nome de usuário do Active Directory e a senha corretamente. Erro: ' AADSTS50001: recurso `https://proxy.cloudwebappproxy.net /registerapp` está desabilitada.' | Proxy de aplicativo está desativado.  | Certifique-se de que habilitar o Proxy de aplicativo no portal do clássico Azure antes de tentar registrar o conector. Para obter mais informações sobre como habilitar o Proxy de aplicativo, consulte [Serviços de habilitar o Proxy de aplicativo](active-directory-application-proxy-enable.md). |
| Registro de conector falhou: Verifique se você ativou o Proxy de aplicativo no Portal de gerenciamento do Azure e que você digitou seu nome de usuário do Active Directory e a senha corretamente. Erro: 'um ou mais erros.' | Se a janela de registro abre e fecha imediatamente sem permitir que você faça logon, você provavelmente receberá esse erro. Este erro ocorre quando há um erro de rede no seu sistema. | Certifique-se de que é possível se conectar usando um navegador para um site público e que as portas estão abertas conforme especificado em [pré-requisitos de Proxy do aplicativo](active-directory-application-proxy-enable.md). |
| Registro de conector falhou: Verifique se o computador está conectado à Internet. Erro: ' não ocorreu em nenhum ponto de extremidade listening em `https://connector.msappproxy.net :9090/register/RegisterConnector` que pôde aceitar a mensagem. Isso geralmente é causado por um endereço incorreto ou ação SOAP. Consulte InnerException, se presente, para obter mais detalhes.' | Se você entrar usando seu nome de usuário do Azure AD e a senha, mas então recebe este erro, pode ser que todas as portas acima 8081 são bloqueadas. | Certifique-se de que as portas necessárias estão abertas. Para obter mais informações, consulte [pré-requisitos de Proxy do aplicativo](active-directory-application-proxy-enable.md). |
| Limpar erro é apresentado na janela de registro. Não é possível continuar – apenas para fechar a janela. | Você inseriu o nome de usuário errado ou a senha. | Tentar novamente. |
| Registro de conector falhou: Verifique se você ativou o Proxy de aplicativo no Portal de gerenciamento do Azure e que você digitou seu nome de usuário do Active Directory e a senha corretamente. Erro: ' AADSTS50059: nenhuma informação de identificação locatário encontrado na solicitação de ou implícita por qualquer credenciais fornecidas e pesquisar por princípio de serviço URI falhou. | Você está tentando efetuar logon usando uma Account da Microsoft e não de um domínio que faz parte da ID da organização do diretório que você está tentando acessar. | Certifique-se de que o administrador faz parte do mesmo nome de domínio como o domínio de locatário, por exemplo, se o domínio do Azure AD for contoso.com, o administrador deve ter admin@contoso.com. |
| Falha ao recuperar a política de execução atual para execução de scripts do PowerShell. | Se a instalação do conector falhar, verifique se a política de execução do PowerShell não está desabilitada. | Abra o Editor de política de grupo. Vá para **Configuração do computador** > **Modelos administrativos** > **Componentes do Windows** > **Do Windows PowerShell** e clique duas vezes em **Ativar a execução de scripts**. Isso pode ser definido como **Não configurado** ou **ativado**. Se definido como **habilitado**, certifique-se de que, em Opções, a política de execução estiver definida para qualquer um dos **scripts do locais de permitir e remoto assinado** ou para **Permitir que todos os scripts**. |
| Conector Falha ao baixar a configuração. | Certificado de cliente do conector, que é usado para autenticação, expirou. Isso também pode ocorrer se você tiver o conector instalado atrás de um proxy. Nesse caso, o conector não é possível acessar a Internet e não será capaz de fornecer aplicativos aos usuários remotos. | Renovar confiabilidade manualmente usando o `Register-AppProxyConnector` cmdlet do Windows PowerShell. Se o conector estiver atrás de um proxy, é necessário conceder acesso à Internet para as contas de conector "serviços de rede" e "sistema local". Isso pode ser feito por conceder-lhes acesso ao Proxy ou definindo-los para ignorar o proxy. |
| Registro de conector falhou: Verifique se você é um Administrador Global do seu Active Directory para registrar o conector. Erro: 'a solicitação de registro negada.' | O alias que você está tentando fazer login não é um administrador nesse domínio. O conector é sempre instalado para o diretório que possui o domínio do usuário. | Certifique-se de que o administrador que você está tentando logon como tem permissões globais locatários Azure AD.|


## <a name="kerberos-errors"></a>Erros Kerberos

| Erro | Descrição | Resolução |
| --- | --- | --- |
| Falha ao recuperar a política de execução atual para execução de scripts do PowerShell. | Se a instalação do conector falhar, verifique se a política de execução do PowerShell não está desabilitada. | Abra o Editor de política de grupo. Vá para **Configuração do computador** > **Modelos administrativos** > **Componentes do Windows** > **Do Windows PowerShell** e clique duas vezes em **Ativar a execução de scripts**. Isso pode ser definido como **Não configurado** ou **ativado**. Se definido como **habilitado**, certifique-se de que, em Opções, a política de execução estiver definida para qualquer um dos **scripts do locais de permitir e remoto assinado** ou para **Permitir que todos os scripts**. |
| 12008 - azure AD excedeu o número máximo de tentativas de autenticação de Kerberos permitidos no servidor back-end. | Esse evento pode indicar a configuração incorreta entre Azure AD e o servidor de aplicativo de back-end ou um problema na configuração de data e hora em ambos os computadores. | O servidor back-end recusou o tíquete Kerberos criado por Azure AD. Verifique se que Azure AD e o servidor de aplicativo de back-end estão configuradas corretamente. Verifique se a configuração de data e hora no Azure AD e o servidor de aplicativo de back-end são sincronizados. |
| 13016 - azure AD não pode recuperar uma permissão Kerberos em nome de usuário porque não há nenhum UPN no token de borda ou no cookie acesso. | Há um problema com a configuração de STS. | Corrigi a configuração de declaração UPN no STS. |
| 13019 - azure AD não pode recuperar uma permissão Kerberos em nome de usuário devido ao seguinte erro de API geral. | Esse evento pode indicar a configuração incorreta entre Azure AD e o servidor do controlador de domínio ou um problema na configuração de data e hora em ambos os computadores. | Controlador de domínio recusou o tíquete Kerberos criado por Azure AD. Verifique se que Azure AD e o servidor de aplicativo de back-end estão configurados corretamente, especialmente a configuração de SPN. Verifique se que o Azure AD é domínio associado ao mesmo domínio que o controlador de domínio para garantir que o controlador de domínio estabelece confiança com Azure AD. Verifique se a configuração de data e hora no Azure AD e o controlador de domínio estão sincronizados. |
| 13020 - azure AD não pode recuperar uma permissão Kerberos em nome de usuário porque o servidor back-end SPN não está definido. | Esse evento pode indicar a configuração incorreta entre Azure AD e o servidor do controlador de domínio ou um problema na configuração de data e hora em ambos os computadores. | Controlador de domínio recusou o tíquete Kerberos criado por Azure AD. Verifique se que Azure AD e o servidor de aplicativo de back-end estão configurados corretamente, especialmente a configuração de SPN. Verifique se que o Azure AD é domínio associado ao mesmo domínio que o controlador de domínio para garantir que o controlador de domínio estabelece confiança com Azure AD. Verifique se a configuração de data e hora no Azure AD e o controlador de domínio estão sincronizados. |
| 13022 - azure AD não pode autenticar o usuário porque o servidor back-end responde a tentativas de autenticação Kerberos com um erro de HTTP 401. | Esse evento pode indicar a configuração incorreta entre Azure AD e o servidor de aplicativo de back-end ou um problema na configuração de data e hora em ambos os computadores. | O servidor back-end recusou o tíquete Kerberos criado por Azure AD. Verifique se que Azure AD e o servidor de aplicativo de back-end estão configuradas corretamente. Verifique se a configuração de data e hora no Azure AD e o servidor de aplicativo de back-end são sincronizados. |
| O site não pode exibir a página. | O usuário pode receber este erro ao tentar acessar o aplicativo que você publicou se o aplicativo é um aplicativo de IWA. O SPN definido para este aplicativo pode estar incorreto. | Para aplicativos IWA: Certifique-se de que o SPN configurado para este aplicativo está correto. |
| O site não pode exibir a página. | O usuário pode receber este erro ao tentar acessar o aplicativo que você publicou se o aplicativo é um aplicativo do OWA. Isso pode ser causado por um destes procedimentos: <br> -O SPN definido para este aplicativo está incorreto. <br> -O usuário que tentou acessar o aplicativo está usando uma conta da Microsoft em vez da conta corporativa apropriada para entrar, ou o usuário é um usuário convidado. <br> -O usuário que tentou acessar o aplicativo não está definido corretamente para este aplicativo no lado local. | As etapas para atenuar adequadamente: <br> -Verifique se o SPN configurado para este aplicativo está correto. <br> -Verifique se o usuário entra no usando sua conta corporativa que corresponda ao domínio do aplicativo publicado. Usuários de Account da Microsoft e convidado não consegue acessar aplicativos IWA. <br> -Verifique se esse usuário tem as permissões apropriadas conforme definido para este aplicativo de back-end na máquina local. |
| Este aplicativo corporativo não pode ser acessado. Você não está autorizado a acessar este aplicativo. Falha de autorização. Certifique-se para atribuir ao usuário acesso a este aplicativo. | Seus usuários podem receber este erro ao tentar acessar o aplicativo que você publicou se usarem contas da Microsoft em vez de sua conta corporativa para entrar. Usuários convidados também podem chegar a esse erro. | Convidados e usuários do Microsoft Account não consegue acessar os aplicativos IWA. Certificar-se de que o usuário entra no usando sua conta corporativa que corresponda ao domínio do aplicativo publicado. |
| Este aplicativo corporativo não pode ser acessado no momento. Tente novamente mais tarde... O conector expirou. | Seus usuários podem receber este erro ao tentar acessar o aplicativo que você publicou se elas não estiverem definidas corretamente para este aplicativo no lado local. | Certifique-se de que os usuários têm as permissões apropriadas conforme definido para este aplicativo de back-end na máquina local. |


## <a name="see-also"></a>Consulte também

- [Habilitar o Proxy de aplicativo do Active Directory do Azure](active-directory-application-proxy-enable.md)
- [Publicar aplicativos com o Proxy de aplicativo](active-directory-application-proxy-publish.md)
- [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar acesso condicional](active-directory-application-proxy-conditional-access.md)

Para as últimas notícias e atualizações, confira o [blog do Proxy de aplicativo](http://blogs.technet.com/b/applicationproxyblog/)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
