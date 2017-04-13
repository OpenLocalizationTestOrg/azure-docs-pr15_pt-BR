<properties
   pageTitle="Criar uma máquina virtual com várias placas de rede"
   description="Aprenda a criar e configurar vms com várias placas de rede"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management,azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="create-a-vm-with-multiple-nics"></a>Criar uma máquina virtual com várias placas de rede

Você pode criar máquinas virtuais (VMs) no Azure e anexar várias interfaces de rede (NICs) para cada uma das suas VMs. Multi NIC é uma exigência para muitos dispositivos virtuais de rede, como soluções de otimização de WAN e entrega de aplicativos. Multi NIC também fornece mais funcionalidade de gerenciamento de tráfego de rede, incluindo isolamento de tráfego entre um frente encerrar NIC e volta final NIC (s) ou separação de tráfego de plano de dados de tráfego de plano de gerenciamento.

![NIC multi para máquina virtual](./media/virtual-networks-multiple-nics/IC757773.png)

A figura acima mostra uma máquina virtual com três NICs, que cada conectado a uma sub-rede diferente.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

- Voltado para a Internet VIP (implantações clássicas) é suportado apenas na NIC "padrão". Não há apenas uma VIP ao IP da NIC padrão.
- No momento, não há suporte para endereços de instância nível público IP (LPIP) (implantações clássicos) para multi NIC VMs.
- A ordem das NICs de dentro da VM será aleatória e também poderia alterar em atualizações de infraestrutura Azure. No entanto, os endereços IP e ethernet correspondente MAC endereços será permanecem os mesmos. Por exemplo, suponha **que eth1** tem o endereço IP 10.1.0.100 e o endereço de MAC 00-0D-3A-B0-39-0D; Após uma atualização de infraestrutura Azure e reinicialização, ele pode ser alterado para **Eth2**, mas o IP e MAC emparelhamento permanecerá igual. Quando uma reinicialização for iniciado pelo cliente, a ordem NIC permanecerá igual.
- O endereço para cada NIC em cada máquina virtual deve estar localizado em uma sub-rede, várias placas de rede em uma única VM podem cada atribuir endereços que estão na mesma sub-rede.
- O tamanho de máquina virtual determina o número de NICS que você pode criar para uma máquina virtual. Referência do [Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) e artigos de tamanhos de máquina virtual [Linux](../virtual-machines/virtual-machines-linux-sizes.md) para determinar quantas NICS suporta o tamanho de cada máquina virtual. 

## <a name="network-security-groups-nsgs"></a>Grupos de segurança de rede (NSGs)
Em uma implantação do Gerenciador de recursos, qualquer NIC em uma máquina virtual pode estar associado com uma rede segurança grupo (NSG), inclusive NICs em uma máquina virtual que tem várias placas de rede habilitadas. Se uma NIC é atribuída um endereço em uma sub-rede onde a sub-rede está associada a um NSG, as regras NSG de sub-rede também aplicam à que NIC. Além de associar sub-redes NSGs, você também pode associar uma NIC um NSG.

Se uma sub-rede está associada com um NSG e uma NIC dentro daquela sub-rede está individualmente associado um NSG, as regras NSG associadas são aplicadas na **ordem de fluxo** de acordo com a direção do tráfego estão sendo passado em ou reduzir a NIC:

- O **tráfego de entrada** cujo destino é a NIC em questão flui primeiro através da sub-rede, disparo regras NSG da sub-rede, antes de passar para a NIC, em seguida, aciona regras de NSG da NIC.
- O **tráfego de saída** cuja origem é a NIC em questão flua primeiro check-out de NIC, disparo regras NSG da NIC, antes de passar por sub-rede, em seguida, aciona regras de NSG da sub-rede.

Saiba mais sobre [Grupos de segurança de rede](virtual-networks-nsg.md) e como eles são aplicados com base nas associações sub-redes, VMs e NICs..

## <a name="how-to-configure-a-multi-nic-vm-in-a-classic-deployment"></a>Como configurar um multi NIC VM em uma implantação clássica

As instruções a seguir ajudará você a criar um multi NIC VM contendo 3 NICs: uma NIC padrão e duas placas de rede adicionais. As etapas de configuração criará uma máquina virtual que será configurada acordo com o fragmento de arquivo de configuração de serviço abaixo:

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


É necessário os seguintes pré-requisitos antes de tentar executar os comandos do PowerShell no exemplo.

- Uma assinatura do Azure.
- Uma rede virtual configurada. Consulte [Visão geral de rede Virtual](virtual-networks-overview.md) para obter mais informações sobre VNets.
- A versão mais recente do PowerShell do Azure baixou e instalou. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

Para criar uma máquina virtual com várias placas de rede, siga as etapas abaixo:

1. Selecione uma imagem de máquina virtual na Galeria de imagens de máquina virtual do Azure. Observe que imagens mudarem com frequência e estão disponíveis por região. A imagem especificada no exemplo abaixo pode alterar ou não podem ser na sua região, então certifique-se de especificar a imagem que você precisa.

        $image = Get-AzureVMImage `
            -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. Crie uma configuração de máquina virtual.

        $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
            -Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. Crie o login de administrador padrão.

        Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
            -Password "<YourAdminPassword>"

1. Adicione NICs adicionais para a configuração de máquina virtual.

        Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
            -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm
        Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
            -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. Especifique a sub-rede e o endereço IP da NIC de padrão.

        Set-AzureSubnet -SubnetNames "Frontend" -VM $vm
        Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm

1. Crie a máquina virtual na sua rede virtual.

        New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE] O VNet que você especificar aqui já deve existir (conforme mencionado os pré-requisitos). O exemplo a seguir especifica uma rede virtual chamada **MultiNIC-VNet**.

## <a name="limitations"></a>Limitações

As seguintes limitações são aplicáveis ao usar o recurso NIC multi:

- Vários NIC VMs devem ser criadas no Azure redes virtuais (VNets). Não VNet VMs não podem ser configuradas com vários NICs.
- Todas as VMs em um conjunto de disponibilidade precisam usar vários NIC ou NIC única. Não pode haver uma mistura de vários NIC VMs e VMs NIC único dentro de um conjunto de disponibilidade. Aplicam as mesmas regras para VMs em um serviço de nuvem.
- Uma máquina virtual com NIC única não pode ser configurada com vários NICs (e vice-versa) quando ele é implantado, sem exclusão e criá-lo novamente.


## <a name="secondary-nics-access-to-other-subnets"></a>Acesso de NICs secundário a outras sub-redes

Por padrão NICs secundárias não serão configuradas com um gateway padrão, devido às quais o fluxo de tráfego nas NICs secundários será limitado para ser dentro da mesma sub-rede. Se os usuários desejam habilitar NICs secundárias falar fora de sua própria sub-rede, eles precisam adicionar uma entrada na tabela de roteamento para configurar o gateway conforme descrito abaixo.

>[AZURE.NOTE] VMs criadas antes de julho de 2015 pode ter um gateway padrão configurado para todas as NICs. O gateway padrão para NICs secundários não será removido até que essas VMs são reinicializados. Em sistemas operacionais que usam o modelo de roteamento de host fracos, como Linux, conectividade com a Internet pode quebrar se o tráfego de entrada e saída usar NICs diferentes.

### <a name="configure-windows-vms"></a>Configurar VMs do Windows

Suponha que você tenha uma máquina de virtual do Windows com dois NICs da seguinte maneira:

- Endereço IP da NIC principal: 192.168.1.4
- Endereço IP da NIC secundário: 192.168.2.5

A tabela de rota IPv4 para esta máquina virtual teria esta aparência:

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

Observe que a rota padrão (0.0.0.0) só está disponível para NIC principal. Você não poderá acessar recursos fora da sub-rede para a NIC secundária, conforme mostrado abaixo:

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

Para adicionar uma rota padrão no NIC secundário, siga as etapas abaixo:

1. A partir de um prompt de comando, execute o comando a seguir para identificar o número de índice para a NIC secundária:

        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================

2. Observe que a segunda entrada na tabela, com um índice de 27 (no exemplo).
3. No prompt de comando, execute o comando de **rota adicionar** conforme mostrado abaixo. Neste exemplo, estiver especificando 192.168.2.1 como o gateway padrão para a NIC secundária:

        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27

4. Para testar a conectividade, volte para o prompt de comando e tente fazer ping uma sub-rede diferente do NIC secundário como mostrado int eh exemplo abaixo:

        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128

5. Você também pode verificar sua tabela de rotas para verificar a rota recém-adicionados, conforme mostrado abaixo:

        C:\Users\Administrator>route print

        ...

        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### <a name="configure-linux-vms"></a>Configurar Linux VMs

Para Linux VMs, como o comportamento padrão usa host fraco roteamento, recomendamos que as NICs secundárias estão restritas fluxos de tráfego apenas dentro da mesma sub-rede. No entanto se determinados cenários exigem conectividade fora da sub-rede, os usuários devem ativar o roteamento de política com base para garantir que o tráfego de entrada e saída usa a mesma NIC.

## <a name="next-steps"></a>Próximas etapas

- Implante [VMs MultiNIC em um cenário de aplicativo de nível 2 em uma implantação do Gerenciador de recursos](virtual-network-deploy-multinic-arm-template.md).
- Implante [VMs MultiNIC em um cenário de aplicativo de nível 2 em uma implantação clássico](virtual-network-deploy-multinic-classic-ps.md).
