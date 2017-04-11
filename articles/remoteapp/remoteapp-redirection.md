<properties
    pageTitle="Usar o redirecionamento do Azure RemoteApp | Microsoft Azure"
    description="Saiba como configurar e usar o redirecionamento em RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-redirection-in-azure-remoteapp"></a>Usar o redirecionamento no RemoteApp do Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Redirecionamento de dispositivo permite que os usuários interagem com aplicativos remotos usando os dispositivos conectados ao seu computador local, telefone ou tablet. Por exemplo, se você tiver fornecido Skype por meio do Azure RemoteApp, o usuário precisa a câmera instalada em seu PC para trabalhar com o Skype. Isso também é verdadeiro para impressoras, alto-falantes, monitores e periféricos um intervalo de USB conectados.

RemoteApp utiliza o protocolo RDP (Remote Desktop) e RemoteFX para fornecer redirecionamento.

## <a name="what-redirection-is-enabled-by-default"></a>Quais redirecionamento está habilitado por padrão?
Quando você usa RemoteApp, os redirecionamentos a seguintes estão habilitados por padrão. As informações entre parênteses mostram a configuração de RDP.

- Reproduzir sons no computador local (**Reproduzir neste computador**). (audiomode:i:0)
- Capturar áudio do computador local e enviar para o computador remoto (**registro deste computador**). (audiocapturemode:i:1)
- Imprimir em impressoras locais (redirectprinters:i:1)
- Portas (redirectcomports:i:1)
- Dispositivo de cartão inteligente (redirectsmartcards:i:1)
- Área de transferência (capacidade de copiar e colar) (redirectclipboard:i:1)
- Desmarque suavização de fonte tipo (Permitir suavização de fonte: i:1)
- Redirecione dispositivos Plug and Play suportadas. (devicestoredirect:s: *)

## <a name="what-other-redirection-is-available"></a>Quais outro redirecionamento está disponível?
Duas opções de redirecionamento são desabilitadas por padrão:

- Redirecionamento de unidade (mapeamento de unidade): unidades do computador local ficam unidades mapeadas na sessão remota. Isso permite que você salvar ou abrir arquivos de suas unidades locais enquanto você trabalha na sessão remota.
- Redirecionamento de USB: você pode usar dispositivos USB conectados ao seu computador local dentro da sessão remota.

## <a name="change-your-redirection-settings-in-remoteapp"></a>Alterar suas configurações de redirecionamento no RemoteApp
Você pode alterar as configurações de redirecionamento de dispositivo para um conjunto usando o Microsoft Azure PowerShell com o SDK. Depois de instalar o novo PowerShell e SDK, primeiro configure-o para gerenciar sua assinatura, conforme descrito em [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

Use um comando semelhante à seguinte para definir as propriedades RDP personalizadas:

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

(Observe que *' n* é usado como um delimitador entre propriedades individuais).

Para obter uma lista de quais propriedades RDP personalizadas são configuradas, execute o cmdlet a seguir. Observe que as propriedades personalizadas somente são mostradas como resultados de saída e não as propriedades padrão:  

    Get-AzureRemoteAppCollection -CollectionName <collection name>

Quando você define propriedades personalizadas que você especifique todas as propriedades personalizadas sempre; Caso contrário, a configuração será revertida para desativado.   

### <a name="common-examples"></a>Exemplos comuns
Use o cmdlet a seguir para habilitar o redirecionamento de unidade:  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

Use esse cmdlet para habilitar o USB e unidade redirecionamento:

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

Use esse cmdlet para desabilitar o compartilhamento de área de transferência:  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

> [AZURE.IMPORTANT] Certifique-se de logoff completamente todos os usuários na coleção (e não apenas desconectá-las) antes de testar a alteração. Para garantir que os usuários são desconectados completamente, vá para a guia **sessões** na coleção no portal do Azure e logoff todos os usuários que estiver desconectados ou conectados. Às vezes, pode levar alguns segundos para as unidades locais a mostrar no Explorer dentro da sessão.

## <a name="change-usb-redirection-settings-on-your-windows-client"></a>Alterar as configurações de redirecionamento de USB em seu cliente do Windows

Se você quiser usar o redirecionamento de USB em um computador que se conecta ao RemoteApp, existem 2 ações que precisam acontecer. 1 - seu administrador precisa habilitar o redirecionamento de USB no nível do conjunto usando o PowerShell do Azure. 2 - de cada dispositivo onde você deseja usar o redirecionamento de USB, você precisa ativar uma diretiva de grupo que permite que ele. Esta etapa precisa ser feito para cada usuário que deseja usar o redirecionamento de USB.

> [AZURE.NOTE] Redirecionamento de USB com o Azure RemoteApp tem suporte apenas para computadores Windows.

### <a name="enable-usb-redirection-for-the-remoteapp-collection"></a>Habilitar o redirecionamento de USB para o conjunto de RemoteApp
Use o cmdlet a seguir para habilitar o redirecionamento de USB no nível do conjunto:

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### <a name="enable-usb-redirection-for-the-client-computer"></a>Habilitar o redirecionamento de USB para o computador cliente

Para definir configurações de redirecionamento de USB em seu computador:

1. Abra o Editor de política de Grupo Local (GPEDIT. MSC). (Executar gpedit. msc um prompt de comando).
2. Abra o **computador configuração Administrativos\Componentes Windows \ Serviços área de Trabalho Remota\Host Conexão da área de trabalho Client\RemoteFX redirecionamento de dispositivos USB**.
3. Clique duas vezes **RDP Permitir redirecionamento de outros dispositivos USB RemoteFX suportados deste computador**.
4. Selecione **ativado**e selecione **Administradores e usuários os direitos de acesso do RemoteFX USB redirecionamento**.
5. Abra um prompt de comando com permissões administrativas e execute o seguinte comando:

        gpupdate /force
6. Reinicie o computador.

Você também pode usar a ferramenta de gerenciamento de política de grupo para criar e aplicar a diretiva de redirecionamento de USB para todos os computadores em seu domínio:

1. Faça logon em controlador de domínio como administrador do domínio.
2. Abra o Console de gerenciamento de política de grupo. (Clique **Iniciar > Ferramentas administrativas > Gerenciamento de política de grupo**.)
3. Navegue até o domínio ou unidade organizacional para o qual você deseja criar a política.
4. **Diretiva de domínio padrão**de atalho e, em seguida, clique em **Editar**.
5. Abra o **computador configuração Administrativos\Componentes Windows \ Serviços área de Trabalho Remota\Host Conexão da área de trabalho Client\RemoteFX redirecionamento de dispositivos USB**.
6. Clique duas vezes **RDP Permitir redirecionamento de outros dispositivos USB RemoteFX suportados deste computador**.
7. Selecione **ativado**e selecione **Administradores e usuários os direitos de acesso do RemoteFX USB redirecionamento**.
8. Clique em **Okey**.  
