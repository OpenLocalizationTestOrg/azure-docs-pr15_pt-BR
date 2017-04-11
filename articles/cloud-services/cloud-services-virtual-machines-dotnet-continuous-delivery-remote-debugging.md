<properties
    pageTitle="Ativar a depuração remota com entrega contínua | Microsoft Azure"
    description="Saiba como habilitar depuração remota quando usando fornecimento contínuo para implantar Azure"
    services="cloud-services"
    documentationCenter=".net"
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>Ativar a depuração remota ao usar o fornecimento contínuo para publicar no Azure

Você pode habilitar a depuração remota no Azure, para os serviços de nuvem ou máquinas virtuais, quando você usa o [fornecimento contínuo](cloud-services-dotnet-continuous-delivery.md) para publicar no Azure seguindo estas etapas.

## <a name="enabling-remote-debugging-for-cloud-services"></a>Habilitando a depuração remota para serviços de nuvem

1. Sobre o agente de compilação, configure o ambiente inicial para Azure conforme descrito na [Compilação de linha de comando para o Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Como o tempo de execução de depuração remota (msvsmon.exe) é necessário para o pacote, instale as [Ferramentas remoto para Visual Studio 2015](http://www.microsoft.com/en-us/download/details.aspx?id=48155) (ou as [Ferramentas remoto para Microsoft Visual Studio 2013 Update 5](https://www.microsoft.com/en-us/download/details.aspx?id=48156) se você estiver usando o Visual Studio 2013). Como alternativa, você pode copiar os binários de depuração remota de um sistema que tenha Visual Studio instalado.
3. Crie um certificado conforme descrito na [Visão geral de certificados para serviços de nuvem do Azure](cloud-services-certs-create.md). Manter a. pfx e a impressão digital de certificado do RDP e carregue o certificado para o serviço de nuvem de destino.
4. Use as seguintes opções na linha de comando MSBuild para criar e empacotar com depuração remota habilitada. (Substituto reais caminhos para seus arquivos de projeto e de sistema para os itens entre colchetes ângulo.)

        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"

    `VSX64RemoteDebuggerPath`é o caminho para a pasta contendo msvsmon.exe nas ferramentas remoto para Visual Studio.
    `RemoteDebuggerConnectorVersion`é a versão do SDK do Azure em seu serviço de nuvem. Ele também deve corresponder a versão instalada com o Visual Studio.

5. Publica o serviço de nuvem de destino, usando o arquivo de pacote e .cscfg gerado na etapa anterior.
6. Importe o certificado (arquivo. pfx) na máquina que tem o Visual Studio com o SDK do Azure para .NET estiver instalado. Verifique se importar para o `CurrentUser\My` repositório de certificados, caso contrário, anexar ao depurador no Visual Studio falhará.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>Habilitando a depuração remota para máquinas virtuais

1. Crie uma máquina virtual Azure. Consulte [criar uma máquina Virtual executando o Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [criar e gerenciar máquinas virtuais Azure no Visual Studio](../virtual-machines/virtual-machines-windows-classic-manage-visual-studio.md).
2. Na [página do portal clássica do Azure](http://go.microsoft.com/fwlink/p/?LinkID=269851), exiba dashboard da máquina virtual para ver da máquina virtual **Impressão digital de certificado de RDP**. Esse valor é usado para o `ServerThumbprint` valor na configuração extensão.
3. Criar um certificado de cliente, conforme descrito na [Visão geral de certificados para serviços de nuvem do Azure](cloud-services-certs-create.md) (mantenha o. pfx e a impressão digital de certificado RDP).
4. Instalar o Azure Powershell (versão 0.7.4 ou posterior) conforme descrito em [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).
5. Execute o seguinte script para habilitar a extensão de RemoteDebug. Substitua os caminhos e seus dados pessoais seu próprio, como seu nome de assinatura, nome do serviço e impressão digital.

    >[AZURE.NOTE] Este script está configurado para Visual Studio 2015. Se você estiver usando o Visual Studio 2013, modifique o `$referenceName` e `$extensionName` atribuições abaixo para usar `RemoteDebugVS2013` (em vez de `RemoteDebugVS2015`).

    <pre>
   AzureAccount adicionar

    Selecione-AzureSubscription "Minha assinatura do Microsoft"

    $vm = get-AzureVM - ServiceName "mytestvm1"-nome "mytestvm1"

    $endpoints = @( ,@{Name="RDConnVS2013"; PublicPort = 30400; PrivatePort = 30398} ,@{Name="RDFwdrVS2013"; PublicPort = 31400; PrivatePort = 31398})  

    foreach ($endpoint em $endpoints) {AzureEndpoint adicionar - máquina virtual $vm-nome $endpoint. Name - protocolo tcp - PublicPort $endpoint. PublicPort - Porta_local $endpoint. PrivatePort}

    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015" $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug" $extensionName = "RemoteDebugVS2015" $version = "1.*" $publicConfiguration = "<PublicConfig>< Connector.Enabled > Verdadeiro < /Connector.Enabled ><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

    $vm | Set-AzureVMExtension `
    -ReferenceName $referenceName ` 
    -Publisher $publisher `
    -ExtensionName $extensionName ` 
    -versão $version ' - PublicConfiguration $publicConfiguration

    foreach ($extension em $vm. MÁQUINA VIRTUAL. ResourceExtensionReferences) {se (($extension. Nome da referência - eq $referenceName) `
    -and ($extension.Publisher -eq $publisher) ` 
    - e ($extension. Nome - eq $extensionName) '- e ($extension. Versão - eq $version)) {$extension. ResourceExtensionParameterValues [0]. Chave = 'txt' quebra}}

    $vm | Atualização AzureVM </pre>

6. Importe o certificado (. pfx) na máquina que tem o Visual Studio com o SDK do Azure para .NET estiver instalado.
