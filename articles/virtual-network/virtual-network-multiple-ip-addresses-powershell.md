<properties
   pageTitle="Vários endereços IP para máquinas virtuais - PowerShell | Microsoft Azure"
   description="Saiba como atribuir vários endereços IP para uma máquina virtual usando o PowerShell do Azure."
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
   ms.date="10/05/2016"
   ms.author="jdial;annahar" />


# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>Atribuir vários endereços IP para máquinas virtuais

Uma máquina Virtual do Azure (máquina virtual) pode ter uma ou mais interfaces de rede (NIC) anexadas a ele. Qualquer NIC pode ter um ou mais públicos ou privados endereços IP atribuídos a ela. Se não estiver familiarizado com endereços IP no Azure, leia o artigo de [endereços IP no Azure](virtual-network-ip-addresses-overview-arm.md) para saber mais sobre eles. Este artigo explica como usar o PowerShell do Azure para atribuir vários endereços IP para uma máquina virtual no modelo de implantação do Gerenciador de recursos do Azure.

Atribuir vários endereços IP para uma máquina virtual permite que os seguintes recursos:

- Hospedando vários sites ou serviços com endereços IP diferentes e certificados SSL em um único servidor.
- Servir como um dispositivo virtual de rede, como um firewall ou balanceador de carga.
- A capacidade de adicionar qualquer um dos endereços IP particulares para qualquer uma das NICs a um pool de back-end de Balanceador de carga do Azure. No passado, somente o endereço IP primário para a NIC primária pode ser adicionado a um pool de back-end.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Para registrar para a visualização, enviar um email para [Vários IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com sua ID de assinatura e a intenção de uso.

## <a name="scenario"></a>Cenário

Neste artigo, você associará três configurações de IP para uma interface de rede.
As configurações de exemplo a seguir serão criadas e atribuídas a uma NIC que terá três endereços IP particulares e um endereço IP público atribuído:

- IPConfig-1: Um endereço IP particular dinâmico (padrão) e um público endereço IP do recurso de endereço IP público chamado PIP1.
- IPConfig-2: Um endereço IP privado estático e nenhum endereço IP público.
- IPConfig-3: Um endereço IP particular dinâmico e nenhum endereço IP público.

    ![Texto ALT imagem](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Neste cenário supõe que você tenha um grupo de recursos chamado *RG1* dentro do qual há um VNet chamado *VNet1* e uma sub-rede chamado *Subnet1*. Além disso, ele pressupõe que você tem uma máquina virtual chamada *VM1*, uma interface de rede chamada *VM1 NIC1* associada a ele e um endereço IP público chamado *PIP1*.

[Este artigo](./virtual-machines/virtual-machines-windows-ps-create.md ) explica como criar os recursos mencionados acima, caso você não tiver criado antes.

## <a name = "create"></a>Criar uma máquina virtual com vários endereços IP

1. Abra um prompt de comando do PowerShell e conclua as etapas restantes nesta seção dentro de uma única sessão do PowerShell. Se você ainda não tiver instalado e configurado o PowerShell, conclua as etapas no artigo [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md) .

2. Alterar os "valores" dos $Variables a seguir para o Azure [local da](https://azure.microsoft.com/regions) que sua rede virtual está, o nome do seu [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups), o VNet dentro do grupo de recursos, a sub-rede que você deseja se conectar a NIC e o nome do NIC. Conclua as etapas para adicionar vários endereços IP para qualquer NIC anexada a uma máquina virtual, como você exige.

        $Location = "westcentralus"
        $RgName   = "RG1"
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
        $NicName     = "VM1-NIC1"
        $PIP = "PIP"

    Se você não souber o nome de um local de Azure existente ou grupo de recursos, digite os seguintes comandos:

        Get-AzureRmLocation      | Format-Table Location
        Get-AzureRmResourceGroup | Format-Table ResourceGroupName

    <a name="subnet"></a>NIC deve estar conectado a uma sub-rede dentro de uma rede Virtual Azure (VNet) existente. Os três componentes: NIC, sub-rede e VNet, deve existir na mesma região e [assinatura](../azure-glossary-cloud-terminology.md#subscription).  Se você não estiver familiarizado com VNets, leia o artigo de [Visão geral de rede Virtual](virtual-networks-overview.md) para saber mais sobre eles ou leia o artigo [criar um VNet](virtual-networks-create-vnet-arm-ps.md) para aprender a criar uma.

    Se você não souber o nome de uma VNet existente, digite o seguinte comando e substitua *VNet1* na variável anterior com o nome de uma VNet:

        Get-AzureRmVirtualNetwork | Format-Table Name

    Se a lista retornada estiver vazia, você precisa criar um VNet. Para saber como fazer isso, leia o artigo [criar uma rede virtual](virtual-networks-create-vnet-arm-ps.md) .

    Digite os seguintes comandos para obter o nome das sub-redes existentes dentro do VNet e substituir *Subnet1* acima com o nome de uma sub-rede:

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

4. Digite o seguinte comando para recuperar a sub-rede e atribuí-la a uma variável.

        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

5. <a name="ipconfigs"></a>Definir as configurações de IP que você deseja atribuir à NIC. Cada configuração pode ter um estático ou dinâmico endereço IP privado e um associados recurso de endereço IP público com um endereço estático ou dinâmico.

    Adicionar ou remover qualquer número das configurações a seguir dependendo de quantos endereços IP que você deseja associar ao NIC e as configurações que você deseja configurar.

    **IPConfig-1**

    Altere o valor *PIP1* para o nome de um recurso de endereço IP de público existente que existe no local que você está criando a NIC e que não está atualmente associada a NIC outra. Alterar *RG1* para o nome do grupo de recursos do recurso de endereço IP público existe no. Alterar *IPConfig-1* para o nome que você deseja dar à primeira configuração IP. Digite os seguintes comandos:

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $RgName

        $IpConfigName1 = "IPConfig-1"
        $IPConfig1     = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName1 -Subnet $Subnet -PublicIpAddress $PIP1 -Primary

    Observação a *-primário* alternar. Quando você atribui várias configurações de IP a uma NIC, uma configuração deve ser atribuída como o *principal*. Se você não souber o nome de um recurso de endereço IP público existente, digite o seguinte comando: Get-AzureRMPublicIPAddress | Nome de tabela de formato, local, endereço IP, IpConfiguration

    Se a coluna de **IPConfiguration** não tiver nenhum valor na saída retornada, o recurso de endereço IP público não está associado uma NIC existente e pode ser usado. Se a lista estiver em branco ou não há nenhum disponíveis recursos de endereço IP públicos, você pode criar um usando o comando **New-AzureRmPublicIPAddress** .

    >[AZURE.NOTE] Endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços de endereço IP, leia a página de [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

    **IPConfig-2**

    Altere *IPConfig-2* para o nome que você deseja dar à segunda configuração IP e alterar *10.0.0.5* para um endereço IP válido não utilizado para a sub-rede que você esteja atribuindo a NIC. Digite os seguintes comandos:

        $IPConfigName2 = "IPConfig-2"
        $IPAddress = 10.0.0.5

        $IPConfig2 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName2 -Subnet $Subnet -PrivateIpAddress $IPAddress

    Digite o seguinte comando, se você não souber o IP intervalo atribuído à sub-rede de endereços:

        $VNet.Subnets | Format-Table Name, AddressPrefix

    **IPConfig-3**

    Alterar *IPConfig-3* para o nome que você deseja conceder a terceira configuração de IP e digite os seguintes comandos:

        $IPConfigName3 = "IPConfig-3"
        $IPConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet

    >[AZURE.NOTE] Você pode atribuir o endereço IP privado até 250 a uma NIC. Não há um limite quanto ao número de endereços IP públicos que podem ser usadas em uma assinatura. Para saber mais, leia o artigo [limita do Azure](../azure-subscription-service-limits.md#networking-limits---azure-resource-manager) .

6. Crie a NIC usando as configurações de IP definidas na etapa anterior.

        $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName -Location $Location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3

7. Anexe a NIC ao criar uma máquina virtual seguindo as etapas no artigo [criar uma máquina virtual](../virtual-machines/virtual-machines-windows-ps-create.md) . Embora o artigo cria uma máquina virtual executando o Windows Server, as etapas são as mesmas para uma VM Linux, diferente selecionando um sistema operacional diferente. Execute as etapas 1 a 3 do artigo. Ignorar as etapas 4 e 5 e, em seguida, concluir a etapa 6 a criar um artigo da máquina virtual.

    >[AZURE.WARNING] Etapa 6 a criar um artigo de máquina virtual falhará se você alterou a variável chamada $nic para algo diferente na etapa 6 deste artigo ou ainda não tiver concluído as etapas anteriores deste artigo.

8. Exibir o IP particular aborda esse DHCP Azure atribuído a NIC e o recurso de endereço IP público atribuído a NIC digitando o seguinte comando:

        $nic.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary

9. <a name="os"></a>Adicione manualmente todos os endereços IP particulares (incluindo o principal) para a configuração de TCP/IP no sistema operacional. 

**Windows**

1. A partir de um prompt de comando, digite *ipconfig/todos os*.  Você só pode ver o endereço IP privado *principal* (por meio de DHCP).
2. Em seguida, digite *ncpa. cpl* na janela do prompt de comando. Isso abrirá uma nova janela.
3. Abra as propriedades de **Conexão de área Local**.
4. Clique duas vezes em protocolo IP versão 4 (IPv4)
5. Selecione **usar o seguinte endereço IP** e insira os seguintes valores:
    - **Endereço IP**: insira o endereço IP privado *principal*
    - **Máscara de sub-rede**: conjunto com base em sua sub-rede. Por exemplo, se a sub-rede é uma /24 sub-rede, em seguida, a máscara de sub-rede é 255.255.255.0.
    - **Gateway padrão**: O primeiro endereço IP na sub-rede. Se sua sub-rede for 10.0.0.0/24, o endereço IP do gateway é 10.0.0.1.
    - Clique em **usar os seguintes endereços de servidor DNS** e insira os seguintes valores:
        - **Servidor DNS preferencial:** Se você não estiver usando seu próprio servidor DNS, insira 168.63.129.16.  Se você estiver, insira o endereço IP para seu servidor DNS.
    - Clique no botão **Avançado** e adicione os endereços IP adicionais. Adicione cada um dos endereços IP privados secundários listados na etapa 8 para a NIC com a mesma sub-rede especificada para o endereço IP primário.
    - Clique **Okey** para fechar as configurações de TCP/IP e **Okey** novamente para fechar as configurações do adaptador. Em seguida, isso será restabelecer sua conexão de RDP.

6. A partir de um prompt de comando, digite *ipconfig/todos os*. Todos os endereços IP que você adicionou são mostrados e DHCP está desativado.


**Linux (Ubuntu)**

1. Abra uma janela de terminal.
2. Verifique se que você é o usuário raiz. Se você não for, você pode fazer isso usando o seguinte comando:

            sudo -i
3. Atualize o arquivo de configuração da interface de rede (presumindo que 'eth0').
    - Manter o item de linha existente para dhcp. Isto configurará o endereço IP primário como costumava ser anterior.
    - Adicione uma configuração de um endereço IP estático adicional com os seguintes comandos:

                cd /etc/network/interfaces.d/
                ls

        Você deve ver um arquivo. cfg.
4. Abra o arquivo: vi *filename*.

    Você deve ver as seguintes linhas no final do arquivo:

            auto eth0
            iface eth0 inet dhcp
5. Adicione as seguintes linhas após as linhas que existem neste arquivo:

            iface eth0 inet static
            address <your private IP address here>
6. Salve o arquivo usando o seguinte comando:

            :wq
7.  Redefina a interface de rede com o seguinte comando:

            sudo ifdown eth0 && sudo ifup eth0

    >[AZURE.IMPORTANT] Execute ifdown e ifup na mesma linha se usando uma conexão remota.
8. Verifique se que o endereço IP é adicionado à interface de rede com o seguinte comando:

            ip addr list eth0

    Você deve ver o endereço IP que você adicionou como parte da lista.

**Linux (Redhat, CentOS e outros)**

1. Abra uma janela de terminal.
2. Verifique se que você é o usuário raiz. Se você não for, você pode fazer isso usando o seguinte comando:

            sudo -i
3. Insira sua senha e siga as instruções conforme solicitado. Quando você estiver o usuário raiz, navegue até a pasta de scripts de rede com o seguinte comando:

            cd /etc/sysconfig/network-scripts
4. Liste os arquivos relacionados ifcfg usando o seguinte comando:

            ls ifcfg-*

    Você deve ver *eth0 ifcfg* como um dos arquivos.
5. Copie o arquivo *ifcfg-eth0* e nomeie- *ifcfg-eth0:0* com o seguinte comando:

            cp ifcfg-eth0 ifcfg-eth0:0
6. Editar o *ifcfg-eth0:0* arquivo com o seguinte comando:

            vi ifcfg-eth1
7. Alterar o dispositivo para o nome apropriado no arquivo; *eth0:0* neste caso, com o seguinte comando:

            DEVICE=eth0:0
8. Alterar o *IPADDR = YourPrivateIPAddress* linha para refletir o endereço IP.
9. Salve o arquivo com o seguinte comando:

            :wq
10. Reiniciar os serviços de rede e verifique se que as alterações forem bem-sucedidas executando os seguintes comandos:

            /etc/init.d/network restart
            Ipconfig

    Você deve ver o endereço IP que você adicionou, *eth0:0*, na lista retornada.

## <a name="add"></a>Adicionar endereços IP para uma máquina virtual existente

Conclua as seguintes etapas para adicionar endereços IP adicionais a uma NIC existente:

1. Abra um prompt de comando do PowerShell e conclua as etapas restantes nesta seção dentro de uma única sessão do PowerShell. Se você ainda não tiver instalado e configurado o PowerShell, conclua as etapas no artigo [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md) .

2. Altere os "valores" de $Variables a seguir para o nome da NIC que você deseja adicionar endereços IP e o grupo de recursos e o local que a NIC existe no:

        $NicName     = "RG1-VM1-NIC1"
        $RgName   = "RG1"
        $NicLocation = "westcentralus"

    Se você não souber o nome da NIC que você deseja alterar, digite os seguintes comandos, em seguida, altere os valores das variáveis anterior:

        Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location

3. Criar uma variável e defini-lo para a NIC existente digitando o seguinte comando:

        $nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName

4. Recupere a ID de sub-rede a NIC é conectada ao realizando- [etapa 3](#subnet) de criar uma máquina virtual com vários seção de endereços IP deste artigo.

5. Crie as configurações de IP que você deseja adicionar à rede, seguindo as instruções na [etapa 5](#ipconfigs) do criar uma máquina virtual com vários seção de endereços IP deste artigo.

6. Alterar *$IPConfigName4* para o nome da configuração de IP que você criou na etapa anterior. Para adicionar a configuração, digite o seguinte comando:

        Add-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName4 -NetworkInterface $nic -Subnet $Subnet1

7. Para definir a NIC com a configuração de IP, digite o seguinte comando:

        Set-AzureRmNetworkInterface -NetworkInterface $nic

8. Adicione os endereços IP que você adicionou à NIC ao sistema operacional máquina virtual seguindo as instruções na [etapa 9](#os) do criar uma máquina virtual com vários seção de endereços IP deste artigo.
