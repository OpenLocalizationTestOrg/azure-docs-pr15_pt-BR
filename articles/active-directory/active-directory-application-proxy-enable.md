<properties
    pageTitle="Habilitar o Proxy de aplicativo do Azure AD | Microsoft Azure"
    description="Ativar o Proxy de aplicativo no portal de clássico do Azure e instalar os conectores para o proxy inverso."
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

# <a name="enable-application-proxy-in-the-azure-portal"></a>Ativar o Proxy de aplicativo no portal do Azure

Este artigo conduz você pelas etapas para habilitar o Proxy de aplicativo do Microsoft Azure AD para seu diretório de nuvem no Azure AD.

Se você estiver familiarizado com o Proxy de aplicativo pode ajudá-lo a fazer, saiba mais sobre [como fornecer acesso remoto seguro para aplicativos locais](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Pré-requisitos de Proxy de aplicativo
Antes de poder habilitar e usar os serviços de Proxy do aplicativo, você precisa ter:

- Uma [assinatura do Microsoft Azure AD básica ou premium](active-directory-editions.md) e um diretório do Azure AD para os quais você é um administrador global.
- Um servidor executando o Windows Server 2012 R2 ou Windows 8.1 ou superior, no qual você pode instalar o conector de Proxy do aplicativo. O servidor envia solicitações para os serviços de Proxy do aplicativo na nuvem e ele precisa de uma conexão HTTP ou HTTPS para os aplicativos que você está publicando.

    - Para logon único para seus aplicativos publicados, esta máquina deve ser domínio no mesmo domínio AD como os aplicativos que você está publicando.

- Se houver um firewall no caminho, certifique-se de que ela está aberta para que o conector possa fazer solicitações de HTTPS (TCP) para o Proxy de aplicativo. O conector usa essas portas junto com subdomínios que fazem parte dos domínios de alto nível msappproxy.net e servicebus.windows.net. Certifique-se abrir as seguintes portas ao tráfego de **saída** :

  	| Número da porta | Descrição |
  	| --- | --- |
  	| 80 | Habilite o tráfego de saída HTTP para validação de segurança. |
  	| 443 | Habilitar a autenticação de usuário contra Azure AD (necessário somente para o processo de registro de conector) |
  	| 10100 – 10120 | Habilitar respostas HTTP LOB enviadas para o proxy |
  	| 9352, 5671 | Permitir a comunicação entre o conector para o serviço do Azure para solicitações de entrada. |
  	| 9350 | Opcional, para habilitar o melhor desempenho para solicitações de entrada |
  	| 8080 | Habilite a sequência de inicialização do conector e a atualização automática do conector |
  	| 9090 | Ativar o registro de conector (necessário somente para o processo de registro de conector) |
  	| 9091 | Habilitar a renovação automática de certificados de confiança de conector |

    Se seu firewall impõe o tráfego de acordo com usuários de origem, abra essas portas para o tráfego proveniente de serviços do Windows em execução como um serviço de rede. Além disso, verifique se habilitar porta 8080 para sistema/Autoridade NT.

- Se sua organização usa servidores proxy para se conectar à internet, por favor, examine a postagem no blog [Trabalhando com os servidores de proxy local existente](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/) para obter detalhes sobre como configurá-los.

## <a name="step-1-enable-application-proxy-in-azure-ad"></a>Etapa 1: Habilitar o Proxy de aplicativo no Azure AD
1. Entrar como um administrador no [portal clássico Azure](https://manage.windowsazure.com/).
2. Vá para o Active Directory e selecione a pasta na qual você deseja habilitar o Proxy de aplicativo.

    ![Active Directory - ícone](./media/active-directory-application-proxy-enable/ad_icon.png)

3. Selecione **Configurar** página diretório de e role para baixo até o **Proxy de aplicativo**.
4. Botão de alternância **habilitar serviços de Proxy de aplicativo para este diretório** **habilitadas**.

    ![Habilitar o Proxy de aplicativo](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. Selecione **Baixar agora**. Isso o leva para o **Azure AD Application Proxy conector Download**. Leia e aceite os termos de licença e clique em **Baixar** para salvar o arquivo do Windows Installer (.exe) para o conector.

## <a name="step-2-install-and-register-the-connector"></a>Etapa 2: Instalar e registrar o conector
1. Execute **AADApplicationProxyConnectorInstaller.exe** no servidor que você preparado de acordo com os pré-requisitos.
2. Siga as instruções no Assistente para instalar.
3. Durante a instalação, você irá será solicitado a registrar o conector com o Proxy de aplicativo do seu locatário do Azure AD.

  - Fornece suas credenciais de administrador global do Azure AD. Seu locatário de administrador global pode ser diferente das suas credenciais do Microsoft Azure.
  - Verifique se o administrador que registra o conector está na mesma pasta em que você ativou o serviço de Proxy do aplicativo. Por exemplo, se o domínio de locatário for contoso.com, o administrador deve ter admin@contoso.com ou qualquer outro alias desse domínio.
  - Se **A configuração de segurança aprimorada IE** é definido como **** no servidor onde você está instalando o conector, a tela de registro pode ser bloqueada. Siga as instruções na mensagem de erro para permitir o acesso. Certifique-se de que a segurança aprimorada do Internet Explorer está desativado.
  - Se o registro de conector não funcionar, consulte [Solucionar problemas de Proxy de aplicativo](active-directory-application-proxy-troubleshoot.md).  

4. Quando a instalação for concluída, dois novos serviços são adicionados ao seu servidor:

    - **Conector de Proxy de aplicativo do Microsoft AAD** permite a conectividade
    - **Atualizador de conector de Proxy de aplicativo do Microsoft AAD** é um serviço de atualização automatizado, que periodicamente verifica novas versões do conector e atualiza o conector conforme necessário.

    ![Serviços de conector de Proxy de aplicativo - captura de tela](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. Clique em **Concluir** na janela de instalação.

Para fins de alta disponibilidade, você deve implantar pelo menos dois conectores. Para implantar mais conectores, repita as etapas 2 e 3 acima. Cada conector deve ser registrado separadamente.

Se você quiser desinstalar o conector, desinstale o serviço do conector e o serviço do atualizador. Reinicie o computador para remover completamente o serviço.


## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para [publicar aplicativos com o Proxy de aplicativo](active-directory-application-proxy-publish.md).

Se você tiver aplicativos que estão em redes separadas ou em locais diferentes, você pode usar grupos de conector para organizar os conectores diferentes em unidades lógicas. Saiba mais sobre como [trabalhar com conectores de Proxy do aplicativo](active-directory-application-proxy-connectors.md).
