<properties 
    pageTitle="Habilitar a área de trabalho remota para serviços de nuvem (node)" 
    description="Saiba como habilitar o acesso de área de trabalho remota para máquinas virtuais hospedando o seu aplicativo do Azure Node." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="enabling-remote-desktop-in-azure"></a>Habilitando a área de trabalho remota no Azure

Área de trabalho remota permite que você acesse a área de trabalho de uma instância de função em execução no Azure. Você pode usar uma conexão de desktop remoto para configurar a máquina virtual ou solucionar problemas com seu aplicativo.

> [AZURE.NOTE] Este artigo se aplica aos aplicativos de Node hospedados como um serviço de nuvem do Azure.


## <a name="prerequisites"></a>Pré-requisitos

- Instalar e configurar [O Powershell do Azure](../powershell-install-configure.md).
- Implante um aplicativo Node em um serviço de nuvem Azure. Para obter mais informações, consulte [criar e implantar um aplicativo Node para um serviço de nuvem do Azure](cloud-services-nodejs-develop-deploy-app.md).


## <a name="step-1-use-azure-powershell-to-configure-the-service-for-remote-desktop-access"></a>Etapa 1: Usar o PowerShell Azure para configurar o serviço para acesso de área de trabalho remota

Para usar a área de trabalho remota, você precisa atualizar a definição de serviço Azure e a configuração com um nome de usuário, senha e certificado. 

Execute as etapas a seguir em um computador que contém os arquivos de origem para o aplicativo.

1. Execute **o Windows PowerShell** como administrador. (No menu **Iniciar** ou **Tela Iniciar**, procure **O Windows PowerShell**.)

2.  Navegue até a pasta que contém a definição de serviço (.csdef) e arquivos de configuração (.cscfg) do serviço.

3. Insira o seguinte cmdlet do PowerShell:

        Enable-AzureServiceProjectRemoteDesktop

4. No prompt, digite um nome de usuário e senha.

    ![Habilitar azureserviceprojectremotedesktop][enable-rdp]

3.  Insira o seguinte cmdlet do PowerShell para publicar as alterações:

        Publish-AzureServiceProject

    ![azureserviceproject publicar][publish-project]

## <a name="step-2-connect-to-the-role-instance"></a>Etapa 2: Conectar-se à instância de função

Depois que você publica a definição do serviço de atualização, você pode se conectar à instância de função.

1.  No [portal de clássico Azure], selecione **Os serviços de nuvem** e selecione seu serviço.

    ![Azure portal clássico][cloud-services]

2.  Clique em **instâncias**e clique em **produção** ou **preparação** para ver as instâncias de seu serviço. Selecione uma ocorrência e, em seguida, clique em **Conectar** na parte inferior da página.

    ![A página de ocorrências][3]

2.  Quando você clica em **Conectar**, o navegador da web solicitará que você salvar um arquivo. rdp. Abra este arquivo. (Por exemplo, se você estiver usando o Internet Explorer, clique em **Abrir**.)

    ![Aviso para abrir ou salvar o arquivo. rdp][4]

3.  Quando o arquivo é aberto, o seguinte aviso de segurança é exibida:

    ![Aviso de segurança do Windows][5]

4.  Clique em **Conectar**, e um aviso de segurança será exibida para inserir as credenciais para acessar a instância. Insira a senha que você criou em [etapa 1] [etapa 1: configurar o serviço de acesso à área de trabalho remota usando o PowerShell do Azure] e clique em **Okey**.

    ![prompt de nome de usuário/senha][6]

Quando a conexão é feita, Conexão de área de trabalho remota exibe a área de trabalho da instância no Azure. 

![Sessão de área de trabalho remota][7]

## <a name="step-3-configure-the-service-to-disable-remote-desktop-access"></a>Etapa 3: Configurar o serviço para desabilitar o acesso de área de trabalho remota 

Quando você não precisa mais conexões da área de trabalho remota para as instâncias de função na nuvem, desative o acesso remoto de área de trabalho usando o [PowerShell do Azure].

1.  Insira o seguinte cmdlet do PowerShell:

        Disable-AzureServiceProjectRemoteDesktop

2.  Insira o seguinte cmdlet do PowerShell para publicar as alterações:

        Publish-AzureServiceProject

## <a name="additional-resources"></a>Recursos adicionais

- [Acessar remotamente instâncias de função no Azure] 
- [Usando a área de trabalho remota com funções Azure]
- [Central de desenvolvedores do Node](/develop/nodejs/)

  [PowerShell Azure]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Azure portal clássico]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
[3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
[4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
[5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
[6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
[7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
[Acessar remotamente instâncias de função no Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
[Usando a área de trabalho remota com funções Azure]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx
 