<properties 
    pageTitle="Solução de problemas de RemoteApp Azure - falhas de inicialização e conexão do aplicativo | Microsoft Azure" 
    description="Saiba como solucionar problemas com Iniciar e conectar aos aplicativos no Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



#<a name="troubleshoot-azure-remoteapp---application-launch-and-connection-failures"></a>Solucionar problemas de RemoteApp Azure - falhas de inicialização e conexão do aplicativo 

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Aplicativos hospedados no Azure RemoteApp podem falhar ao início para vários motivos. Este artigo descreve vários motivos e mensagens de erro, os usuários podem receber quando tentar iniciar os aplicativos. Ele também fala sobre falhas de conexão. (Mas este artigo não descreve problemas ao entrar no cliente do Azure RemoteApp.)  

Leia mais para obter informações sobre mensagens de erro comuns devido a falhas de inicialização e conexão do aplicativo.

##<a name="were-getting-you-set-up-try-again-in-10-minutes"></a>Estamos obtendo configurar... Tente novamente em 10 minutos.

Esse erro significa que Azure RemoteApp está chegando para satisfazer a necessidade de capacidade de seus usuários. No plano de fundo mais ocorrências de máquina virtual do Azure RemoteApp estão sendo criadas para lidar com as necessidades de capacidade de seus usuários. Normalmente, isso leva cerca de cinco minutos mas pode levar até 10 minutos. Às vezes, isso não acontece com rapidez suficiente e recursos são necessários imediatamente. Por exemplo um 9H cenário onde muitos usuários precisarão usar seu aplicativo no Azure RemoteAppn ao mesmo tempo. Se isso acontecer a você que nós pode habilitar o **modo de capacidade** no back-end. Para fazer isso abrir um tíquete do Azure e ou e-mail para [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com). Certifique-se de incluir seu ID da assinatura na solicitação.  

![Estamos obtendo você configurar](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## <a name="could-not-auto-reconnect-to-your-applications-please-re-launch-your-application"></a>Não foi possível autoreconectar aos seus aplicativos, verifique novamente iniciar o aplicativo  

Essa mensagem de erro é geralmente Vista se você estava usando o Azure RemoteApp coloca o PC em suspensão mais de 4 horas e, em seguida, ativou seu PC para cima e o cliente do Azure RemoteApp tentar automático reconectar e tempo limite foi excedido.  Instruir os usuários a navegar de volta para o aplicativo e tente abri-la a partir do cliente do Azure RemoteApp.

![Não foi possível autoreconectar aos seus aplicativos](./media/remoteapp-apptrouble/ra-apptrouble2.png) 

## <a name="problems-with-the-temp-profile"></a>Problemas com o perfil temp 

Esse erro ocorre quando seu perfil de usuário (disco de perfil de usuário) Falha ao montar e o usuário recebeu um perfil temporário.  Administradores devem navegar para o conjunto no portal do Azure e, em seguida, vá para a guia **sessões** e tentar **Fazer logoff** do usuário. Isso irá forçar um log cheio da sessão do usuário - depois que o usuário tentar iniciar um aplicativo novamente. Se isso falhar contatar o suporte do Azure e ou e-mail para [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

## <a name="azure-remoteapp-has-stopped-working"></a>Azure RemoteApp parou de funcionar

Essa mensagem de erro significa que o cliente do Azure RemoteApp está tendo um problema e precisa ser reiniciado. Instruir os usuários para fechar: selecione **Fechar o programa** e inicie o cliente do Azure RemoteApp novamente.  Se o problema persistir abrir e tíquete do Azure e ou e-mail para [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

![Azure RemoteApp parou de funcionar](./media/remoteapp-apptrouble/ra-apptrouble3.png)  

## <a name="an-error-occurred-while-remote-desktop-connection-was-accessing-this-resource-retry-the-connection-or-contact-your-system-administrator"></a>Ocorreu um erro na Conexão de Desktop remoto estava acessando este recurso. Repetir a conexão ou contate o administrador do sistema

Esta é uma mensagem de erro genérica - contatar o suporte do Azure e ou e-mail para [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com) para que poderemos investigar. 

![Mensagem genérica do Azure RemoteApp](./media/remoteapp-apptrouble/ra-apptrouble4.png) 