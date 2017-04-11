<properties
    pageTitle="Conectar seu aplicativo à sua rede virtual usando o PowerShell"
    description="Instruções sobre como conectar-se a e trabalhar com redes virtuais usando o PowerShell"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="wpickett"
    editor="cephalin"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="ccompy"/>

# <a name="connect-your-app-to-your-virtual-network-by-using-powershell"></a>Conectar seu aplicativo à sua rede virtual usando o PowerShell #

## <a name="overview"></a>Visão geral ##

No serviço de aplicativo do Azure, você pode conectar seu aplicativo (web, celular ou API) a uma rede virtual Azure (VNet) em sua assinatura. Esse recurso é chamado de integração de VNet. O recurso de integração de VNet não deve ser confundido com o recurso de ambiente de aplicativo de serviço, que permite que você execute uma instância do serviço de aplicativo do Azure em sua rede virtual.

O recurso de integração de VNet tem uma interface de usuário (UI) no novo portal do que você pode usar a integração com redes virtuais que são implantadas usando o modelo de implantação clássico ou o modelo de implantação do Gerenciador de recursos do Azure. Se você quiser saber mais sobre o recurso, consulte [integrar seu aplicativo com uma rede virtual Azure](web-sites-integrate-with-vnet.md).

Este artigo é não sobre como usar a interface do usuário, mas preferir sobre como habilitar a integração usando o PowerShell. Como os comandos para cada modelo de implantação são diferentes, este artigo apresenta uma seção para cada modelo de implantação.  

Antes de continuar com este artigo, certifique-se de que você tenha:

- O SDK do PowerShell Azure mais recente instalado. Você pode instalá-lo com o Web Platform Installer.
- Um aplicativo no serviço de aplicativo do Azure em execução em um padrão ou Premium SKU.

## <a name="classic-virtual-networks"></a>Redes virtuais clássicas ##

Esta seção explica três tarefas para redes virtuais que usam o modelo clássico de implantação:

1. Conecte seu aplicativo a uma rede virtual preexistente que tem um gateway e está configurada para conectividade de ponto-a-site.
1. Atualize suas informações de integração de rede virtual para o aplicativo.
1. Desconecte seu aplicativo da sua rede virtual.

### <a name="connect-an-app-to-a-classic-vnet"></a>Conectar um aplicativo a um VNet clássico ###

Para conectar um aplicativo a uma rede virtual, siga estas três etapas:

1. Declare aplicativo web que ele for ingressar em uma determinada rede virtual. O aplicativo irá gerar um certificado fornecido à rede virtual para conectividade de ponto-a-site.
1. Carregar o certificado de aplicativo da web para a rede virtual e, em seguida, recuperar o pacote do ponto-a-site VPN URI.
1. Atualize conexão de rede virtual do aplicativo da web com o pacote de ponto-a-site URI.

A primeira e a terceira etapas são totalmente programáveis, mas a segunda etapa requer uma ação única, manual pelo portal ou acesso para executar ações de **colocar** ou **PATCH** sobre o ponto de extremidade do Gerenciador de recursos do Azure de rede virtual. Contate o suporte do Azure para tê-lo ativado. Antes de começar, certifique-se de que você tenha uma rede virtual clássica com conectividade de ponto-a-site já ativado e um gateway implantado. Para criar o gateway e habilitar a conectividade de ponto-a-site, você precisa usar o portal, conforme descrito na [criação de um gateway VPN][createvpngateway].

A rede virtual clássica precisa estar na mesma assinatura do seu plano de serviço de aplicativo que detém o aplicativo que você integrar.

##### <a name="set-up-azure-powershell-sdk"></a>Configurar o SDK do PowerShell do Azure #####

Abra uma janela do PowerShell e configurar sua conta do Azure e assinatura usando:

    Login-AzureRmAccount

Esse comando abrirá um aviso para obter suas credenciais do Azure. Após entrar, use um dos seguintes comandos para selecionar a assinatura que você deseja usar. Certifique-se de que você está usando a assinatura que sua rede virtual e o plano de serviço de aplicativo estão em.

    Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

ou

    Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

##### <a name="variables-used-in-this-article"></a>Variáveis usados neste artigo #####

Para simplificar a comandos, podemos definirá uma variável de PowerShell **$Configuration** com a configuração específica.

Defina uma variável da seguinte maneira no PowerShell com os seguintes parâmetros:

    $Configuration = @{}
    $Configuration.WebAppResourceGroup = "[Your web app resource group]"
    $Configuration.WebAppName = "[Your web app name]"
    $Configuration.VnetSubscriptionId = "[Your vnet subscription id]"
    $Configuration.VnetResourceGroup = "[Your vnet resource group]"
    $Configuration.VnetName = "[Your vnet name]"

O local do aplicativo deve ser o local sem espaços. Por exemplo, oeste EUA é westus.

    $Configuration.WebAppLocation = "[Your web app Location]"

O próximo item é onde o certificado deve ser escrito. Ele deve ser um caminho gravável no computador local. Certifique-se de incluir. cer no final.

    $Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]"

Para ver o que você definir, digite **$Configuration**.

    > $Configuration

    Name                           Value
    ----                           -----
    GeneratedCertificatePath       C:\vnetcert.cer
    VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
    WebAppResourceGroup            vnetdemo-rg
    VnetResourceGroup              testase1-rg
    VnetName                       TestNetwork
    WebAppName                     vnetintdemoapp
    WebAppLocation                 centralus

O restante desta seção pressupõe que você tenha uma variável criada conforme descrito apenas.

##### <a name="declare-the-virtual-network-to-the-app"></a>Declarar a rede virtual para o aplicativo #####

Use o seguinte comando para informar o aplicativo que ele usará nesta rede virtual específica. Isso fará com que o aplicativo gerar certificados necessários:

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

Se este comando for bem-sucedido, **$vnet** deve ter uma variável de **Propriedades** . A variável de **Propriedades** deve conter uma impressão digital do certificado e os dados de certificado.

##### <a name="upload-the-web-app-certificate-to-the-virtual-network"></a>Carregar o certificado de aplicativo da web para a rede virtual #####

Um manual, etapa única é necessária para cada combinação de rede virtual e assinatura. Isto é, se você está se conectando aplicativos na assinatura A uma rede Virtual, você precisará fazer isso apenas uma vez, independentemente de quantos aplicativos você configurar. Se você estiver adicionando um novo aplicativo para outra rede virtual, você precisará fazer isso novamente. O motivo para isso é que um conjunto de certificados é gerado em um nível de inscrição no serviço de aplicativo do Azure e o conjunto é gerado uma vez para cada rede virtual que os aplicativos se conectará.

Os certificados serão já foram definidos se seguir essas etapas, ou se você integrada com a mesma rede virtual usando o portal.

A primeira etapa é gerar o arquivo. cer. A segunda etapa é carregar o arquivo. cer para sua rede virtual. Para gerar o arquivo. cer da chamada API na etapa anterior, execute os seguintes comandos.

    $certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
    [System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

O certificado estarão no local que **$Configuration.GeneratedCertificatePath** especifica.

Para carregar o certificado manualmente, usar o [portal do Azure] [ azureportal] e **Procurar rede Virtual (clássico)** > **conexões VPN** > **ponto-a-site** > **Gerenciar certificados**. A partir daqui, carregue seu certificado.

##### <a name="get-the-point-to-site-package"></a>Obter o pacote de ponto-a-site #####

A próxima etapa na configuração de uma conexão de rede virtual em um aplicativo web é obter o pacote de ponto-a-site e fornecê-lo ao seu aplicativo web.

Salve o modelo a seguir para um arquivo chamado GetNetworkPackageUri.json em algum lugar no seu computador, por exemplo, C:\Azure\Templates\GetNetworkPackageUri.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "certData": {
                "type": "string"
            },
            "certThumbprint": {
                "type": "string"
            },
            "networkName": {
                "type": "string"
            }
        },
        "variables": {
            "legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]"
            },
            "resources": [
            ],
        "outputs" : {
            "PackageUri" :
            {
            "value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string"
            }
        }
    }


Definir parâmetros de entrada:

    $parameters = @{"certData" = $vnet.Properties.certBlob ;
    certThumbprint = $vnet.Properties.certThumbprint ;
    "networkName" = $Configuration.VnetName }

Chame o script:

    $output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json


A variável **$output. Outputs.packageUri** agora conterá o pacote URI a ser dada ao seu aplicativo web.

##### <a name="upload-the-point-to-site-package-to-your-app"></a>Carregar o pacote de ponto-a-site em seu aplicativo #####

A etapa final é fornecer o aplicativo com este pacote. Basta executar o seguinte comando:

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation

Se uma mensagem solicitar que você confirme que você está substituindo um recurso existente, verifique se permitir que ele.

Após este comando tiver êxito, seu aplicativo agora deve estar conectado à rede virtual. Para confirmar o sucesso, vá para seu console de aplicativo e digite o seguinte:

    SET WEBSITE_

Se houver uma variável de ambiente chamada WEBSITE_VNETNAME que tem um valor que corresponde ao nome da rede virtual de destino, todas as configurações tenham êxito.

### <a name="update-classic-vnet-integration-information"></a>Atualizar informações de integração de VNet clássicas ###

Para atualizar ou sincronizado novamente suas informações, basta repetir as etapas que você seguiu quando você criou a integração em primeiro lugar. Essas etapas são:

1. Defina suas informações de configuração.
1. Declare a rede virtual para o aplicativo.
1. Obter o pacote de ponto-a-site.
1. Carregue o pacote de ponto-a-site para o seu aplicativo.

### <a name="disconnect-your-app-from-a-classic-vnet"></a>Desconecte seu aplicativo um VNet clássico ###

Para desconectar o aplicativo, é necessário ter as informações de configuração que foi definidas durante a integração de rede virtual. Usando essas informações, em seguida, há um comando desconectar seu aplicativo da sua rede virtual.

    $vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## <a name="resource-manager-virtual-networks"></a>Redes virtuais do Gerenciador de recursos ##

Redes virtuais do Gerenciador de recursos têm APIs do Gerenciador de recursos do Azure, que simplificam alguns processos quando comparadas com redes virtuais clássicas. Nós temos um script que irá ajudá-lo a concluir as seguintes tarefas:

- Criar uma rede virtual do Gerenciador de recursos e integrar seu aplicativo com ele.
- Criar um gateway, configurar a conectividade de ponto-a-site em uma rede virtual preexistentes do Gerenciador de recursos e, em seguida, integrar seu aplicativo com ele.
- Integre seu aplicativo com uma rede virtual preexistentes Gerenciador de recursos que tenha um gateway e conectividade habilitado ponto-a-site.
- Desconecte seu aplicativo da sua rede virtual.

### <a name="resource-manager-vnet-app-service-integration-script"></a>Script de integração do Gerenciador de recursos VNet App Service ###

Copie o seguinte script e salve-o em um arquivo. Se você não quiser usar o script, fique à vontade para aprender com ele para ver como configurar as coisas com uma rede virtual do Gerenciador de recursos.


    function ReadHostWithDefault($message, $default)
    {
        $result = Read-Host "$message [$default]"
        if($result -eq "")
        {
            $result = $default
        }
            return $result
        }

    function PromptCustom($title, $optionValues, $optionDescriptions)
    {
        Write-Host $title
        Write-Host
        $a = @()
        for($i = 0; $i -lt $optionValues.Length; $i++){
            Write-Host "$($i+1))" $optionDescriptions[$i]
        }
        Write-Host

        while($true)
        {
            Write-Host "Choose an option: "
            $option = Read-Host
            $option = $option -as [int]

            if($option -ge 1 -and $option -le $optionValues.Length)
            {
                return $optionValues[$option-1]
            }
        }
    }

    function PromptYesNo($title, $message, $default = 0)
    {
        $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
        $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
        $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
        $result = $host.ui.PromptForChoice($title, $message, $options, $default)
        return $result
    }

    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
        Write-Host "Creating a new VNET"
        $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
        New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }

    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
        $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
        $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

        Write-Host "Creating a public IP address for this VNET"
        $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip

        Write-Host "Adding a root certificate to this VNET"
        $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData

        Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
        New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }

    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
        Write-Host "Adding a new Vnet"
        Write-Host
        $vnetName = Read-Host "Specify a name for this Virtual Network"

        $vnetGatewayName="$($vnetName)-gateway"
        $vnetIpName="$($vnetName)-ip"
        $vnetIpConfigName="$($vnetName)-ipconf"

        # Virtual Network settings
        $vnetAddressSpace="10.0.0.0/8"
        $vnetGatewayAddressSpace="10.5.0.0/16"
        $vnetPointToSiteAddressSpace="172.16.0.0/16"

        $changeRequested = 0
        $resourceGroupName = $webAppResourceGroup

        while($changeRequested -eq 0)
        {
            Write-Host
            Write-Host "Currently, I will create a VNET with the following settings:"
            Write-Host
            Write-Host "Virtual Network Name: $vnetName"
            Write-Host "Resource Group Name:  $resourceGroupName"
            Write-Host "Gateway Name: $vnetGatewayName"
            Write-Host "Vnet IP name: $vnetIpName"
            Write-Host "Vnet IP config name:  $vnetIpConfigName"
            Write-Host "Address Space:$vnetAddressSpace"
            Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
            Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
            Write-Host
            $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

            if($changeRequested -eq 0)
            {
                $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
                $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
                $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
                $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
            }
        }

        $ErrorActionPreference = "Stop";

        # We create the virtual network and add it here. The way this works is:
        # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
        # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
        # 3) Get the VPN package from the gateway and pass it back to the App.

        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
        $location = $webApp.Location

        Write-Host "Creating App association to VNET"
        $propertiesObject = @{
         "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
        }
        $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location

        CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        Write-Host "Finished!"
    }

    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $ErrorActionPreference = "Stop";

        # At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
        Write-Host "Getting App information"
        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $location = $webApp.Location

        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"
        }

        # Display existing vnets
        $vnets = Get-AzureRmVirtualNetwork
        $vnetNames = @()
        foreach($vnet in $vnets)
        {
            $vnetNames += $vnet.Name
        }

        Write-Host
        $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames

        # We need to check if this VNET is able to be joined to a App, based on following criteria
            # If there is no gateway, we can create one.
            # If there is a gateway:
                # It must be of type Vpn
                # It must be of VpnType RouteBased
                # If it doesn't have the right certificate, we will need to add it.
                # If it doesn't have a point-to-site range, we will need to add it.

        $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }

        if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
        {
            $ErrorActionPreference = "Continue";
            # There is no gateway. We need to create one.
            Write-Host "This Virtual Network has no gateway. I will need to create one."

            $vnetName = $vnet.Name
            $vnetGatewayName="$($vnetName)-gateway"
            $vnetIpName="$($vnetName)-ip"
            $vnetIpConfigName="$($vnetName)-ipconf"

            # Virtual Network settings
            $vnetAddressSpace="10.0.0.0/8"
            $vnetGatewayAddressSpace="10.5.0.0/16"
            $vnetPointToSiteAddressSpace="172.16.0.0/16"

            $changeRequested = 0

            Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
            foreach($subnet in $vnet.Subnets)
            {
                Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
            }

            $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"

            while($changeRequested -eq 0)
            {
                Write-Host
                Write-Host "Currently, I will create a VNET gateway with the following settings:"
                Write-Host
                Write-Host "Virtual Network Name: $vnetName"
                Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
                Write-Host "Gateway Name: $vnetGatewayName"
                Write-Host "Vnet IP name: $vnetIpName"
                Write-Host "Vnet IP config name:  $vnetIpConfigName"
                Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
                Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
                Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
                Write-Host
                $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

                if($changeRequested -eq 0)
                {
                    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
                }
            }

            $ErrorActionPreference = "Stop";

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # If there is no gateway subnet, we need to create one.
            if($gatewaySubnet -eq $null)
            {
                $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
                $vnet.Subnets.Add($gatewaySubnet);
                Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
            }

            CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
        }
        else
        {
            $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
            $gatewayResourceGroup = $uriParts[4]
            $gatewayName = $uriParts[8]

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName

            # validate gateway types, etc.
            if($gateway.GatewayType -ne "Vpn")
            {
                Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
                return
            }

            if($gateway.VpnType -ne "RouteBased")
            {
                Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
                return
            }

            if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
            {
                Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
                $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
                Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
            }

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnet.Name)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # We need to check if the certificate here exists in the gateway.
            $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates

            $certFound = $false
            foreach($certificate in $certificates)
            {
                if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
                {
                    $certFound = $true
                    break
                }
            }

            if(-not $certFound)
            {
                Write-Host "Adding certificate"
                Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
            }
        }

        # Now finish joining by getting the VPN package and giving it to the App
        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

        Write-Host "Finished!"
    }

    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"

            Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        }
          else
        {
          Write-Host "Not connected to a VNET."
        }
    }

    Write-Host "Please Login"
    Login-AzureRmAccount

    # Choose subscription. If there's only one we will choose automatically

    $subs = Get-AzureRmSubscription
    $subscriptionId = ""

    if($subs.Length -eq 0)
    {
        Write-Error "No subscriptions bound to this account."
        return
    }

    if($subs.Length -eq 1)
    {
        $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
        $subscriptionChoices = @()
        $subscriptionValues = @()

        foreach($subscription in $subs){
        $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
        $subscriptionValues += ($subscription.SubscriptionId);
        }

        $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $resourceGroup = Read-Host "Please enter the Resource Group of your App"

    $appName = Read-Host "Please enter the Name of your App"

    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options

    if($option -eq 0)
    {
        AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
        AddExistingVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 2)
    {
        RemoveVnet $subscriptionId $resourceGroup $appName
    }

Salve uma cópia do script. Neste artigo, ele é chamado V2VnetAllinOne.ps1, mas você pode usar outro nome. Não há nenhum argumento para esse script. Você simplesmente executá-lo. A primeira coisa que o script fará é pedir que você entrar. Após entrar, o script obtém detalhes sobre a sua conta e retorna uma lista de assinaturas. A execução de scripts inicial não contando a solicitação para suas credenciais, fica assim:

    PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
    Please Login

    Environment           : AzureCloud
    Account               : ccompy@microsoft.com
    TenantId              : 722278f-fef1-499f-91ab-2323d011db47
    SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
    CurrentStorageAccount :

    Choose a subscription

    1) Assinatura de demonstração (af5358e1-acac-2c90-a9eb-722190abf47a)
    2) Teste de MS (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
    3) Assinatura de demonstração roxo (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)

    Escolha uma opção: 3

    Conta: ccompy@microsoft.com ambiente: AzureCloud assinatura: locatário 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d: 722278f-fef1-499f-91ab-2323d011db47

    Insira o grupo de recursos do aplicativo: hcdemo rg insira o nome do aplicativo: v2vnetpowershell o que você deseja fazer?

    1) Adicionar uma nova rede Virtual a um aplicativo
    2) Adicionar uma rede Virtual existente a um aplicativo
    3) Remover uma rede Virtual de um aplicativo

O restante desta seção explica cada uma dessas três opções.

### <a name="create-a-resource-manager-vnet-and-integrate-with-it"></a>Criar um VNet Gerenciador de recursos e integração com eles ###

Para criar uma nova rede virtual que usa o modelo de implantação do Gerenciador de recursos e integrá-lo com seu aplicativo, selecione **1) adicionar uma nova rede Virtual a um aplicativo**. Isso solicitará o nome da rede virtual. No meu caso, como você pode ver nas configurações a seguir, eu usou o nome, v2pshell.

O script fornece os detalhes sobre a rede virtual que está sendo criada. Se eu quiser, posso alterar qualquer um dos valores. Em execução neste exemplo, criei uma rede virtual que tem as seguintes configurações:

    Virtual Network Name:         v2pshell
    Resource Group Name:          hcdemo-rg
    Gateway Name:                 v2pshell-gateway
    Vnet IP name:                 v2pshell-ip
    Vnet IP config name:          v2pshell-ipconf
    Address Space:                10.0.0.0/8
    Gateway Address Space:        10.5.0.0/16
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):

Se você quiser alterar qualquer um dos valores, digite **Y** e faça as alterações. Quando estiver satisfeito com as configurações de rede virtual, digite **N** ou simplesmente pressione Enter quando solicitado sobre como alterar as configurações. A partir daí até a conclusão, o script informará alguns dos que ' i ' s fazendo até ele começa a criar o gateway de rede virtual. Essa etapa pode levar uma hora. Não há nenhum indicador de progresso durante esta fase, mas o script avisará você quando o gateway foi criado.

Quando o script for concluído, ela indicará **concluído**. Neste ponto, você terá uma rede virtual do Gerenciador de recursos que tem o nome e as configurações que você selecionou. Essa nova rede virtual também será integrado com seu aplicativo.

### <a name="integrate-your-app-with-a-preexisting-resource-manager-vnet"></a>Integrar seu aplicativo com um preexistentes VNet Gerenciador de recursos ###

Quando você estiver integração com uma rede virtual preexistente, se você fornecer uma rede virtual do Gerenciador de recursos que não tem um gateway ou conectividade de ponto-a-site, o script configurará que. Se o VNET já tiver essas coisas configurar, o script vai diretamente para a integração de aplicativo. Para iniciar esse processo, simplesmente selecione **2) adicionar uma rede Virtual existente a um aplicativo**.

Essa opção funciona somente se você tiver uma rede virtual preexistentes Gerenciador de recursos que está na mesma assinatura do seu aplicativo. Depois de selecionar a opção, você verá uma lista de suas redes virtual do Gerenciador de recursos.   

    Select a VNET to integrate with

    1) v2demonetwork
    2) v2pshell
    3) v2vnetintdemo
    4) v2asenetwork
    5) v2pshell2

    Escolha uma opção: 5

Basta selecione a rede virtual que você deseja integrar. Se você já tiver um gateway que tenha conectividade de ponto-a-site habilitada, o script simplesmente irá integrar seu aplicativo com a sua rede virtual. Se você não tiver um gateway, você precisará especificar a sub-rede de gateway. Sua sub-rede de gateway deve ser em seu espaço de endereço de rede virtual, e ele não pode estar em qualquer outra sub-rede. Se você tiver uma rede virtual sem um gateway e executar esta etapa, coisas esta aparência:

    This Virtual Network has no gateway. I will need to create one.
    Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
    default: 172.16.0.0/24
    Please choose a GatewaySubnet address space: 172.16.1.0/26

Neste exemplo, criei um gateway de rede virtual que tem as seguintes configurações:

    Virtual Network Name:         v2pshell2
    Resource Group Name:          vnetdemo-rg
    Gateway Name:                 v2pshell2-gateway
    Vnet IP name:                 v2pshell2-ip
    Vnet IP config name:          v2pshell2-ipconf
    Address Space:                172.16.0.0/16
    Gateway Address Space:        172.16.1.0/26
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):
    Creating App association to VNET

Se você quiser alterar qualquer uma dessas configurações, você poderá fazer isso. Caso contrário, pressione Enter e o script criará seu gateway e anexar o aplicativo à sua rede virtual. A hora de criação do gateway ainda é uma hora, porém, então certifique-se de que que você tenha em mente. Quando tudo estiver concluído, o script dirá **concluído**.

### <a name="disconnect-your-app-from-a-resource-manager-vnet"></a>Desconecte seu aplicativo VNet um gerente de recursos ###

Desconectar seu aplicativo de sua rede virtual não tomadas gateway ou desabilitar conectividade de ponto-a-site. Você pode, afinal, estar usando-lo para outra coisa. Ele também não desconectá-lo de quaisquer outros aplicativos diferente da que você forneceu. Para executar esta ação, selecione **3) remover uma rede Virtual de um aplicativo**. Quando você fizer isso, você verá algo parecido com isto:

    Currently connected to VNET v2pshell

    Confirm
    Are you sure you want to delete the following resource:
    /subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
    hell/virtualNetworkConnections/v2pshell
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Embora o script diz delete, ele não exclui a rede virtual. Ele está removendo apenas a integração. Depois de confirmar que este é o que você deseja fazer, o comando é processado bem rapidamente e informa **True** quando estiver pronto.

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com
