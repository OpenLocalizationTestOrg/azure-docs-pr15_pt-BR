<properties 
   pageTitle="Configurar o proxy da web para um dispositivo de StorSimple | Microsoft Azure"
   description="Saiba como usar o Windows PowerShell para StorSimple para definir configurações de proxy da web para o seu dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-web-proxy-for-your-storsimple-device"></a>Configurar o proxy da web para o seu dispositivo de StorSimple

## <a name="overview"></a>Visão geral

Este tutorial descreve como usar o Windows PowerShell para StorSimple para configurar e exibir configurações de proxy da web para o seu dispositivo StorSimple. As configurações de proxy da web são usadas pelo dispositivo StorSimple quando se comunicar com a nuvem. Um servidor de proxy web é usado para adicionar outra camada de segurança, filtro de conteúdo, cache para requisitos de largura de banda de facilidade ou até mesmo ajuda com a análise.

Proxy da Web é uma configuração opcional para o seu dispositivo StorSimple. Você pode configurar o proxy de web apenas através do Windows PowerShell para StorSimple. A configuração é um processo de duas etapas da seguinte maneira:

1. Primeiro, você definir configurações de proxy do web através do Assistente de configuração ou o Windows PowerShell para StorSimple cmdlets.

2. Em seguida, habilitar as configurações de proxy web configurado por meio do Windows PowerShell para StorSimple cmdlets.

Após concluir a configuração de proxy da web, você pode exibir as configurações de proxy web configurado do serviço de Gerenciador de StorSimple do Microsoft Azure e o Windows PowerShell para StorSimple. 

Após ler este tutorial, você será capaz de:

- Configurar o proxy da web usando cmdlets e o Assistente de configuração
- Habilitar proxy web usando cmdlets
- Exibir configurações de proxy da web no portal de clássico do Azure
- Solucionar problemas de erros durante a configuração de proxy da web


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Configurar o proxy de web através do Windows PowerShell para StorSimple

Você pode usar um dos seguintes para definir configurações de proxy da web:

- Assistente de configuração para orientá-lo durante as etapas de configuração.

- Cmdlets do Windows PowerShell para StorSimple.

Cada um desses métodos é discutida nas seções a seguir.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Configurar proxy de web via o Assistente de configuração

Você pode usar o Assistente de configuração para orientá-lo pelas etapas de configuração de proxy da web. Execute as seguintes etapas para configurar o proxy da web no seu dispositivo.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Para configurar o proxy de web via o Assistente de configuração

1. No menu console serial, escolha a opção 1, **fazer logon com acesso total** e fornecer a **senha de administrador do dispositivo**. Digite o seguinte comando para iniciar uma sessão do Assistente de configuração:

    `Invoke-HcsSetupWizard`

2. Se esta for a primeira vez que você usou o Assistente de configuração para o registro de dispositivo, você precisará definir todas as configurações de rede necessária até chegar a configuração de proxy da web. Se seu dispositivo já estiver registrado, você pode aceitar todas as configurações de rede configurada até chegar a configuração de proxy da web. No Assistente de configuração, quando solicitado a definir configurações de proxy da web, digite **Sim**.

3. Para a **URL de Proxy da Web**, especifique o endereço IP ou o nome de domínio totalmente qualificado (FQDN) do seu servidor de proxy da web e o número da porta TCP que você gostaria de seu dispositivo para usar ao se comunicar com a nuvem. Use o seguinte formato:

    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`

    Por padrão, o número da porta TCP 8080 é especificado.

4. Escolha o tipo de autenticação como **NTLM**, **básico**ou **Nenhum**. Basic é a autenticação menos segura para a configuração do servidor proxy. NT LAN Manager (NTLM) é um protocolo de autenticação altamente seguro e complexa que usa um sistema de mensagens de três formas (às vezes, quatro se integridade adicional é necessária) para autenticar um usuário. A autenticação padrão é NTLM. Para obter mais informações, consulte [básicos](http://hc.apache.org/httpclient-3.x/authentication.html) e [autenticação NTLM](http://hc.apache.org/httpclient-3.x/authentication.html). 

    > [AZURE.IMPORTANT] **No serviço StorSimple Manager, os gráficos de monitoramento do dispositivo não funcionam quando básica ou a autenticação NTLM está habilitada na configuração do servidor proxy para o dispositivo. Para os gráficos de monitoramento trabalhar, você precisará garantir que a autenticação está definida como nenhum.**

5. Se você estiver usando autenticação, forneça um **Nome de usuário do Web Proxy** e uma **Senha de Proxy da Web**. Você também precisará confirme a senha.

    ![Configurar o Proxy da Web no StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

Se você estiver registrando seu dispositivo pela primeira vez, continue com o registro. Se seu dispositivo já foi registrado, o assistente será encerrado. As configurações definidas serão salvos.

Proxy Web agora também será ativada. Você pode ignorar a etapa [Habilitar proxy web](#enable-web-proxy) e vá diretamente para [configurações de proxy do modo de exibição da web no portal de clássico do Azure](#view-web-proxy-settings-in-the-azure-classic-portal).


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Configurar proxy de web através do Windows PowerShell para cmdlets StorSimple

Uma maneira alternativa para definir configurações de proxy web é por meio do Windows PowerShell para StorSimple cmdlets. Execute as seguintes etapas para configurar o proxy da web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Para configurar o proxy de web via cmdlets

1. No menu console serial, escolha a opção 1, **fazer logon com acesso total**. Quando solicitado, forneça a **senha de administrador do dispositivo**. A senha padrão é `Password1`.

2. No prompt de comando, digite:

    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`

    Forneça e confirme a senha quando solicitado, conforme mostrado abaixo.

    ![Configurar o Proxy da Web em StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

O proxy da web agora está configurado e precisa ser habilitada.

## <a name="enable-web-proxy"></a>Habilitar proxy web

Web proxy está desativado por padrão. Depois de configurar as configurações de proxy da web em seu dispositivo de StorSimple, você precisa usar o Windows PowerShell para StorSimple para habilitar as configurações de proxy da web.

> [AZURE.NOTE] **Esta etapa não será necessária se você usou o Assistente de configuração para configurar o proxy da web. Proxy da Web é automaticamente habilitado por padrão após uma sessão do Assistente de configuração.**

Execute as etapas a seguir no Windows PowerShell para StorSimple habilitar o proxy da web em seu dispositivo:

#### <a name="to-enable-web-proxy"></a>Para habilitar o proxy da web

1. No menu console serial, escolha a opção 1, **fazer logon com acesso total**. Quando solicitado, forneça a **senha de administrador do dispositivo**. A senha padrão é `Password1`.

2. No prompt de comando, digite:

    `Enable-HcsWebProxy`

    Agora você habilitou a configuração de proxy da web em seu dispositivo de StorSimple.

    ![Configurar o Proxy da Web em StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-classic-portal"></a>Exibir configurações de proxy da web no portal de clássico do Azure

As configurações de proxy da web são configuradas por meio da interface do Windows PowerShell e não podem ser alteradas dentro do portal clássico. No entanto, você pode exibir essas configurações de configurado no portal do clássico. Execute as seguintes etapas para exibir o proxy da web.

#### <a name="to-view-web-proxy-settings"></a>Para exibir as configurações de proxy da web
1. Navegue até **serviço Gerenciador de StorSimple > dispositivos**. Selecione e clique em um dispositivo e clique em **Configurar**.
1. Role para baixo na página **Definir** a seção de **configurações de proxy da Web** . Você pode exibir as configurações de proxy web configurado em seu dispositivo de StorSimple conforme mostrado abaixo.

    ![Proxy do modo de exibição da Web no Portal de gerenciamento](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)
 
## <a name="errors-during-web-proxy-configuration"></a>Erros durante a configuração de proxy da web

Se as configurações de proxy da web tiveram sido configuradas incorretamente, mensagens de erro serão exibidas ao usuário no Windows PowerShell para StorSimple. A tabela a seguir explica algumas das seguintes mensagens de erro, causas prováveis e ações recomendadas.

|Não serial.|Código de erro HRESULT|Causas possíveis|Ação recomendada|
|:---|:---|:---|:---|
|1.|0x80070001|Comando é executado do controlador de passivo e não é capaz de se comunicar com o controlador de ativo.|Execute o comando no controlador de ativos. Para executar o comando do controlador de passivo, você precisará corrigir a conectividade de passivo controlador de ativos. Você precisará envolva Microsoft Support se essa conectividade foi desfeita.|
|2.|0x800710dd - o identificador de operação não é válido|Configurações de proxy não são suportadas no dispositivo virtual StorSimple.|Configurações de proxy não são suportadas no dispositivo virtual StorSimple. Elas podem ser configuradas apenas em um dispositivo físico StorSimple.|
|3.|0x80070057 - parâmetro inválido|Um dos parâmetros fornecidos para as configurações de proxy não é válido.|O URI não é fornecido no formato correto. Use o seguinte formato:`http://<IP address or FQDN of the web proxy server>:<TCP port number>`|
|4.|0x800706BA - servidor RPC não disponível|A causa é uma das seguintes opções:</br></br>Cluster não estiver disponível.</br></br>Serviço de DataPath não está funcionando.</br></br>O comando é executado do controlador passivo e não é capaz de se comunicar com o controlador de ativo.|Por favor envolva Support da Microsoft para garantir que o cluster estiver ativo e datapath serviço está em execução.</br></br>Execute o comando do controlador de ativos. Se você quiser executar o comando do controlador de passivo, você precisará garantir que o controlador de passivo possa se comunicar com o controlador de ativo. Você precisará envolva Microsoft Support se essa conectividade foi desfeita.|
|5.|0x800706BE - chamada RPC falhou|Cluster está inoperante.|Por favor envolva Microsoft Support para garantir que o cluster é para cima.|
|6.|0x8007138f - recurso de cluster não encontrado|O recurso de cluster do serviço de plataforma não foi encontrado. Isso pode acontecer quando a instalação não foi adequada.|Talvez seja necessário realizar uma fábrica redefinir em seu dispositivo. Talvez você precise criar um recurso de plataforma. Entre em contato com o Microsoft Support para as próximas etapas.|
|7.|0x8007138c - recurso de cluster não online|Recursos de cluster de plataforma ou datapath não estão online.|Entre em contato com o Microsoft Support para ajudar a garantir que o recurso de serviço de plataforma e datapath estão online.|

> [AZURE.NOTE] 
> 
> -  A lista de mensagens de erro acima não é completa. 
> - Erros relacionados às configurações de proxy da web não serão exibidos no portal de clássico do Azure em seu serviço de Gerenciador de StorSimple. Se houver um problema com o proxy web após a configuração é concluída, o status do dispositivo será alterado para **Offline** no portal do clássico. |

## <a name="next-steps"></a>Próximas etapas

- Se você tiver problemas durante a implantação do seu dispositivo ou a configuração de configurações de proxy da web, consulte [Solucionar problemas de sua implantação de dispositivo StorSimple](storsimple-troubleshoot-deployment.md).

- Para saber como usar o serviço do Gerenciador de StorSimple, vá para [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
