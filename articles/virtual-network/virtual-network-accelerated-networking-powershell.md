<properties 
   pageTitle="Acelerada, de rede para uma máquina virtual - PowerShell | Microsoft Azure"
   description="Saiba como configurar a rede acelerada para uma máquina virtual Azure usando o PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="jdial" />

# <a name="accelerated-networking-for-a-virtual-machine"></a>Rede acelerada para uma máquina virtual

> [AZURE.SELECTOR]
- [Portal do Azure](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

Rede acelerada permite a única raiz e/s virtualização (SR-IOV) a uma máquina virtual (VM), bastante melhorar seu desempenho de rede. Este caminho de alto desempenho ignora o host do datapath reduzindo latência, variação e utilização de CPU para uso com as cargas de trabalho de rede maiores em tipos de máquina virtual com suporte. Este artigo explica como usar o PowerShell do Azure para configurar a rede acelerada no modelo de implantação do Gerenciador de recursos do Azure. Você também pode criar uma máquina virtual com acelerada rede usando o Portal do Azure. Para saber como fazer isso, clique na caixa de Portal Azure na parte superior deste artigo.

A figura a seguir mostra a comunicação entre duas máquinas virtuais (VM) com e sem acelerada de rede:

![Comparação](./media/virtual-network-accelerated-networking-powershell/image1.png)

Sem acelerada, de rede, todo o tráfego de rede e sair a máquina virtual deve percorrer o host e switch virtual. Switch virtual fornece todas as imposição de política, como grupos de segurança de rede, listas de controle de acesso, isolamento e outros serviços de rede virtualizado tráfego de rede. Para saber mais, leia o artigo [virtualização de rede do Hyper-V e Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) .

Com acelerada, de rede, o tráfego de rede chega o cartão de rede (NIC) e é então encaminhado para a máquina virtual. Todas as diretivas de rede que switch virtual aplica sem acelerada rede são descarregadas e aplicadas em hardware. Aplicação da política em hardware habilita a NIC para encaminhar o tráfego de rede diretamente para a máquina virtual, ignorando o host e a opção virtual, mantendo todo a política que ela aplicada no host.

Os benefícios da rede acelerada aplicam-se apenas para a máquina virtual que ele está habilitado. Para obter melhores resultados, ele é ideal para ativar esse recurso em pelo menos duas VMs conectada para o mesmo VNet.  Ao se comunicar em VNets ou conexão local, este recurso tem um impacto mínimo latência geral.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>Benefícios

- **Menor latência / superiores pacotes por segundo (pps):** Removendo switch virtual do datapath remove o tempo gastam pacotes do host para processamento de política e aumenta o número de pacotes que pode ser processado dentro a máquina virtual.
- **Reduzida variação:** Processamento de switch virtual depende da quantidade de política que precisa ser aplicada e a carga de trabalho da CPU que está fazendo o processamento. Transferindo a imposição de política para o hardware remove essa variabilidade oferecendo pacotes diretamente para a máquina virtual, removendo o host para comunicação de máquina virtual e todas as interrupções de software e opções de contexto.
- **Reduzida da CPU:** Ignorar switch virtual no host leva a menor utilização da CPU para o processamento de tráfego de rede.

## <a name="limitations"></a>Limitações

As seguintes limitações existem ao usar esse recurso:
 
- **Criação da interface de rede:** Rede acelerada só pode ser habilitada para uma nova interface de rede.  Ele não pode ser habilitado em uma interface de rede existente.
- **Criação de máquina virtual:** Uma interface de rede com a rede acelerada habilitada somente pode ser anexada a uma máquina virtual quando a máquina virtual é criada. A interface de rede não pode ser anexada a uma máquina virtual existente.
- **Regiões:** Oferecida em apenas as regiões Central Oeste dos EUA e Oeste Europa Azure. O conjunto de regiões expandirá no futuro.
- **Sistema operacional de suporte:** Microsoft Windows Server 2012 R2 e Windows Server 2016 Technical Preview 5. Suporte Linux e Windows Server 2012 será adicionado em breve.
- **Tamanho de máquina virtual:** Standard_D15_v2 e Standard_DS15_v2 são que os únicos tamanhos suportados de máquina virtual instância. Para obter mais informações, consulte o artigo de [tamanhos de máquina virtual do Windows](../virtual-machines/virtual-machines-windows-sizes.md) . O conjunto de tamanhos de instância de máquina virtual suportados expandirá no futuro.

Alterações para essas limitações serão anunciadas através da página de [atualizações de rede Virtual do Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview) .

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Criar uma máquina virtual do Windows com a rede acelerada

1. Abra um prompt de comando do PowerShell e conclua as etapas restantes nesta seção dentro de uma única sessão do PowerShell. Se você ainda não tiver instalado e configurado o PowerShell, conclua as etapas no artigo [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md) .
2. Para registrar para a visualização, enviar um email para [Acelerada assinaturas de rede](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com sua ID de assinatura e a intenção de uso. Não conclua as etapas restantes até após receber um email notificando que você já foi aceita em visualização.
3. Registre o recurso com a sua assinatura por meio de comandos a seguir:

        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network

4. Substitua *westcentralus* com o nome do outro local compatível com esse recurso listado na seção [limitações](#limitations) deste artigo (se desejar). Insira o seguinte comando para definir uma variável para o local:

        $locName = "westcentralus"

5. Substitua *RG1* com um nome para o grupo de recursos que conterá a nova interface de rede e digite os seguintes comandos para criá-lo:

        $rgName = "RG1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

6. Alterar *VM1 NIC1* que você deseja nomear a interface de rede e, em seguida, digite o seguinte comando:

        $NICName = "VM1-NIC1"

7. A interface de rede deve estar conectada a uma sub-rede dentro de uma rede Virtual Azure (VNet) existente no mesmo local e [assinatura](../azure-glossary-cloud-terminology.md#subscription) como a interface de rede. Saiba mais sobre VNets lendo o artigo de [Visão geral de rede Virtual](virtual-networks-overview.md) se não estiver familiarizado com elas. Para criar um VNet, conclua as etapas no artigo [criar uma VNet](virtual-networks-create-vnet-arm-ps.md) . Altere os "valores" de $Variables a seguir para o nome da VNet e sub-rede que você deseja se conectar a interface de rede para.

        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"

    Se você não souber o nome de uma VNet existente no local que você escolheu na etapa 3, digite os seguintes comandos:
        
        $VirtualNetworks = Get-AzureRmVirtualNetwork
        $VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
        
    Se a lista retornada estiver vazia, você precisa criar um VNet no local. Para criar um VNet, conclua as etapas no artigo [criar uma rede virtual](virtual-networks-create-vnet-arm-ps.md) .

    Para obter o nome das sub-redes dentro do VNet, digite os seguintes comandos e substituir *Subnet1* acima com o nome de uma sub-rede:
        
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

8. Digite os seguintes comandos para recuperar o VNet e a sub-rede e atribuí-las a variáveis.

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

9. Identifica um recurso de endereço IP de público existente que pode ser associado à interface de rede para que você possa se conectar a ela pela Internet. Se você não quiser acessar a máquina virtual com a interface de rede pela Internet, você pode ignorar esta etapa. Sem um endereço IP público, você deve se conectar para a máquina virtual outra máquina virtual conectada para o mesmo VNet. 

    Alterar *PIP1* para o nome de um recurso de endereço IP de público existente que existe no local que você está criando a interface de rede no e que não está atualmente associada a outra interface de rede. Se necessário, altere $rgName para o nome do grupo de recursos, o recurso de endereço IP público existe em e digite o seguinte comando:

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName

    Se você não souber o nome de um recurso de endereço IP público existente, digite os seguintes comandos:

        $PublicIPAddresses = Get-AzureRmPublicIPAddress
        $PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration

    Se a coluna de **IPConfiguration** não tiver nenhum valor na saída retornada, o recurso de endereço IP público não está associado uma interface de rede existente e pode ser usado. Se a lista estiver em branco ou não há nenhum disponíveis recursos de endereço IP públicos, você pode criar um usando o comando New-AzureRmPublicIPAddress.

    >[AZURE.NOTE] Endereços IP públicos têm uma taxa nominal. Saiba mais sobre preços lendo a página de [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) .
10. Se você optou por não adicionar um recurso de endereço IP público para a interface, remova *- PublicIPAddress $PIP1* no final do comando que se segue. Crie a interface de rede com a rede acelerada digitando o seguinte comando:

        $nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 

11. Atribua a interface de rede a uma máquina virtual ao criar a máquina virtual seguindo as instruções nas etapas 3 e 6 do artigo [criar uma máquina virtual](../virtual-machines/virtual-machines-windows-ps-create.md) . Na etapa 2 de 6, substitua *Standard_A1* um dos tamanhos de máquina virtual listados na seção [limitações](#limitations) deste artigo.

    >[AZURE.NOTE] Se você alterou o *nome* da $locName, $rgName ou $nic variáveis neste artigo, a etapa 6 a criar um artigo de máquina virtual falhará. Entretanto, você pode alterar os *valores* das variáveis.

12. Assim que a máquina virtual for criada, o download do [driver de rede acelerada](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), conectar-se para a máquina virtual e execute o instalador do driver dentro a máquina virtual.

13. Clique no botão do Windows e clique em **Gerenciador de dispositivos**. Verifique se o **Adaptador de Ethernet de função Virtual Mellanox ConnectX-3** aparece sob a opção de **rede** quando expandida, conforme mostrado na figura a seguir:

    ![Gerenciador de dispositivos](./media/virtual-network-accelerated-networking-powershell/image2.png)