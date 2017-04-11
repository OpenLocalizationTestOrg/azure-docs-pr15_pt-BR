<properties
  pageTitle="Conectar a um serviço de nuvem a um controlador de domínio personalizado | Microsoft Azure"
  description="Saiba como conectar as funções da web/trabalhador para um domínio personalizado do AD usando o PowerShell e extensão de domínio do AD"
  services="cloud-services"
  documentationCenter=""
  authors="Thraka"
  manager="timlt"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="adegeo"/>

# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Conexão funções de serviços de nuvem do Azure para um controlador de domínio do AD hospedado no Azure personalizado

Nós primeiro irá configurar uma rede Virtual (VNet) no Azure. Em seguida, adicionaremos um controlador de domínio Active Directory (hospedada em uma máquina Virtual do Azure) para o VNet. Em seguida, podemos adicionará as funções de serviço de nuvem existentes para o VNet pré-criada e subsequentemente conectá-las ao controlador de domínio.

Antes de começar, algumas coisas a ter em mente:

1.  Este tutorial usa PowerShell, portanto, verifique se que você tiver instalado o PowerShell Azure e pronta. Para obter ajuda com a configuração do PowerShell do Azure, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

2.  Suas instâncias de controlador de domínio do AD e função Web/trabalhador precisam estar na VNet.

Siga este guia passo a passo e se você tiver problemas, deixe-no que um comentário abaixo. Alguém obter de volta para você (Sim, podemos ler comentários).

3. A rede que é referenciada por nuvem <mark>deve ser</mark> uma **rede virtual clássica**de serviço.

## <a name="create-a-virtual-network"></a>Criar uma rede Virtual

Você pode criar uma rede Virtual no Azure usando o portal do Azure clássico ou o PowerShell. Para este tutorial, usaremos PowerShell. Para criar uma rede Virtual usando o portal de clássico Azure, consulte [Criar uma rede Virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina Virtual

Quando tiver concluído Configurando a rede Virtual, você precisará criar um controlador de domínio do AD. Para este tutorial, podemos configurará um controlador de domínio do AD em uma máquina Virtual do Azure.

Para fazer isso, crie uma máquina virtual através do PowerShell usando os comandos abaixo:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Promover sua máquina Virtual a controlador de domínio
Para configurar a máquina Virtual como um controlador de domínio do AD, você precisará fazer logon na máquina virtual e configurá-lo.

Para efetuar login para a máquina virtual, você pode obter o arquivo RDP através do PowerShell, use os comandos abaixo.

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Depois que você está conectado a máquina virtual, configure sua máquina Virtual como um controlador de domínio do AD seguindo o guia passo a passo sobre [como configurar seu controlador de domínio do AD do cliente](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Adicionar seu serviço de nuvem à rede Virtual

Em seguida, é necessário adicionar sua implantação de serviço de nuvem para o VNet que você acabou de criar. Para fazer isso, modificar seu cscfg de serviço de nuvem adicionando as seções relevantes para sua cscfg usando o Visual Studio ou o editor de sua escolha.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Em seguida, criar seu projeto de serviços de nuvem e implantá-lo no Azure. Para obter ajuda com implantação seu pacote de serviços de nuvem no Azure, veja [como criar e implantar um serviço na nuvem](cloud-services-how-to-create-deploy.md#deploy)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Conectar seu funções da web/trabalhador ao domínio

Quando seu projeto de serviço de nuvem é implantado no Azure, conecte seu instâncias de função no domínio AD personalizado usando a extensão de domínio do AD. Para adicionar a extensão de domínio do AD a sua implantação de serviços de nuvem existente e ingressar no domínio personalizado, execute os seguintes comandos do PowerShell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

E é isso.

Nuvem serviços agora devem ser associados ao seu controlador de domínio personalizado. Se você quiser saber mais sobre as diferentes opções disponíveis para como configurar o domínio do AD extensão, use a Ajuda do PowerShell, conforme mostrado abaixo.

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
