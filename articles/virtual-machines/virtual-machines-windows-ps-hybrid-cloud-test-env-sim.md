<properties 
    pageTitle="Ambiente de teste de nuvem híbrida simulada | Microsoft Azure" 
    description="Criar um ambiente de nuvem híbrida simulada para IT pro ou testes de desenvolvimento, usando duas redes virtuais Azure e uma conexão de VNet para VNet." 
    services="virtual-machines-windows" 
    documentationCenter="" 
    authors="JoeDavies-MSFT" 
    manager="timlt" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="josephd"/>

# <a name="set-up-a-simulated-hybrid-cloud-environment-for-testing"></a>Configurar um ambiente de nuvem híbrida simulado para teste

Este artigo orienta a criação de um ambiente de nuvem híbrida simulado com o Microsoft Azure usando duas redes virtuais Azure. Aqui está a configuração resultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

Isso simula um ambiente de produção de nuvem híbrida e consiste em:

- Uma rede simulada e simplificada local hospedada em uma rede virtual Azure (a rede virtual TestLab).
- Uma rede virtual do local entre simulada hospedada no Azure (TestVNET).
- Uma conexão de VNet para VNet entre as duas redes virtuais.
- Controlador de domínio secundário na rede virtual TestVNET.

Isso fornece que uma base e partida comum de pontos de que você pode:

- Desenvolver e testar aplicativos em um ambiente de nuvem híbrida simulada.
- Crie configurações de teste de computadores, alguns dentro da rede virtual TestLab e alguns dentro da rede virtual TestVNET, para simular cargas de trabalho do híbrido baseado em nuvem IT.

Há quatro fases principais para configurar esse ambiente de teste de nuvem híbrida:

1.  Configure a rede virtual TestLab.
2.  Crie rede virtual entre locais.
3.  Crie a conexão VPN de VNet para VNet.
4.  Configure DC2. 

Essa configuração requer uma assinatura do Azure. Se você tiver uma assinatura do MSDN ou Visual Studio, consulte [crédito Azure mensal para os assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Máquinas virtuais e gateways de rede virtual no Azure provocam um custo monetário em andamento quando eles estiverem em execução. Esse custo é cobrado contra seu MSDN ou assinatura paga. Um gateway VPN Azure é implementado como um conjunto de duas máquinas virtuais Azure. Para minimizar os custos, crie o ambiente de teste e execute o teste necessários e demonstração assim que possível.

## <a name="phase-1-configure-the-testlab-virtual-network"></a>Fase 1: Configurar a rede virtual TestLab

Use as instruções no tópico [configuração básica do ambiente de teste](https://technet.microsoft.com/library/mt771177.aspx) para configurar os computadores DC1, APP1 e CLIENT1 na rede virtual Azure denominada TestLab. 

Em seguida, inicie um prompt de PowerShell do Azure.

> [AZURE.NOTE] O comando a seguir define uso Azure PowerShell 1.0 e posterior.

Entre em sua conta.

    Login-AzureRMAccount

Obtenha o nome da sua assinatura usando o seguinte comando.

    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Defina sua assinatura do Azure. Use a mesma assinatura que você usou para construir sua configuração básica na fase 1. Substituir tudo entre aspas, incluindo o < e > caracteres, com o nome correto.

    $subscr="<subscription name>"
    Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Em seguida, adicione uma sub-rede de gateway à rede virtual TestLab da sua configuração básica, que será usada para hospedar o gateway Azure.

    $rgName="<name of your resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
    Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

Em seguida, solicite um endereço IP público para atribuir para o gateway para a rede virtual TestLab.

    $gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

Em seguida, crie seu gateway.

    $vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
    New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Tenha em mente que novos gateways podem levar 20 minutos ou mais para criar.

Azure no portal em seu computador local, conecte ao DC1 com as credenciais de CORP\User1. Para configurar o domínio CORP para que computadores e usuários usem o seu controlador de domínio local para autenticação, execute esses comandos em um prompt de comando do Windows PowerShell de nível de administrador no DC1.

    New-ADReplicationSite -Name "TestLab" 
    New-ADReplicationSite -Name "TestVNET"
    New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
    New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

Esta é sua configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph1.png)
 
## <a name="phase-2-create-the-testvnet-virtual-network"></a>Fase 2: Criar a rede virtual TestVNET

Primeiro, crie a rede virtual TestVNET e protegê-la com um grupo de segurança de rede.

    $rgName="<name of the resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
    $testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
    $gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
    New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
    $rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
    New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
    $vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
    $nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
    Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

Em seguida, solicite um endereço IP público a ser alocado para o gateway para a rede virtual TestVNET e crie seu gateway.

    $gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
    New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Esta é sua configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph2.png)
 
##<a name="phase-3-create-the-vnet-to-vnet-connection"></a>Fase 3: Criar a conexão de VNet para VNet

Primeiro, obtenha uma chave pré compartilhada aleatória, criptografia forte 32 caracteres de seu administrador de rede ou segurança. Como alternativa, use as informações ao [criar uma cadeia de caracteres aleatória para uma chave pré compartilhada IPsec](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) para obter uma chave pré compartilhada.

Em seguida, use estes comandos para criar a conexão VPN de VNet para VNet, que pode demorar algum tempo para ser concluída.

    $sharedKey="<pre-shared key value>"
    $gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
    $gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
    New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
    New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

Após alguns minutos, a conexão deve ser estabelecida.

Esta é sua configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph3.png)
 
## <a name="phase-4-configure-dc2"></a>Etapa 4: Configurar DC2

Primeiro, crie uma máquina virtual para DC2. Execute esses comandos no prompt de comando do PowerShell do Azure em seu computador local.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<the storage account name for the base configuration>"
    $vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
    $vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Em seguida, conectar-se para a nova máquina virtual DC2 do portal do Azure.

Em seguida, configure uma regra de Firewall do Windows para permitir o tráfego de teste de conectividade básica. A partir de um prompt de comando do Windows PowerShell nível de administrador no DC2, execute esses comandos.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc1.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedida do endereço IP 10.0.0.4. Este é um teste de tráfego a conexão de VNet para VNet.

Em seguida, adicione o disco de dados extras no DC2 como um novo volume com a letra f.

1.  No painel esquerdo do Gerenciador de servidor, clique em **arquivo e serviços de armazenamento**e, em seguida, clique em **discos**.
2.  No painel de conteúdo, no grupo de **discos** , clique em **disco 2** (com a **partição** definida como **desconhecido**).
3.  Clique em **tarefas**e, em seguida, clique em **Novo Volume**.
4.  No antes que você comece a página do Assistente de novo Volume, clique em **Avançar**.
5.  Na página Selecionar o servidor e disco, clique em **2 de disco**e clique em **Avançar**. Quando solicitado, clique em **Okey**.
6.  Em especificar o tamanho da página de volume, clique em **Avançar**.
7.  Em atribuir a uma página de carta ou uma pasta de unidade, clique em **Avançar**.
8.  Na página de configurações do sistema selecione arquivo, clique em **Avançar**.
9.  Na página Confirmar seleções, clique em **criar**.
10. Ao concluir, clique em **Fechar**.

Em seguida, configure DC2 como um controlador de domínio de réplica do domínio corp.contoso.com. Execute esses comandos no prompt de comando do Windows PowerShell no DC2.

    Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
    Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Observe que você será solicitado a fornecer a senha de CORP\User1 e uma senha de modo de restauração de serviços de diretório (DSRM) e reiniciar o DC2.

Agora que a rede virtual TestVNET tem seu próprio servidor DNS (DC2), você deve configurar a rede virtual TestVNET para usar este servidor DNS.

1.  No painel esquerdo do portal do Azure, clique no ícone de redes virtuais e clique em **TestVNET**.
2.  Na guia **configurações** , clique em **servidores DNS**.
3.  Em **servidor DNS principal**, digite **192.168.0.4** para substituir 10.0.0.4.
4.  Clique em **Salvar**.

Esta é sua configuração atual. 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)
 
Ambiente de nuvem híbrida simulada agora está pronto para testar.

## <a name="next-step"></a>Próxima etapa

- Configure uma [linha baseado na web, do aplicativo de negócios](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md) neste ambiente.
