<properties 
   pageTitle="Solucionar problemas de grupos de segurança de rede - PowerShell | Microsoft Azure"
   description="Saiba como solucionar problemas de grupos de segurança de rede no modelo de implantação do Gerenciador de recursos do Azure usando o PowerShell do Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
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
   ms.author="anithaa" />

# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Solucionar problemas de grupos de segurança de rede usando o PowerShell do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](virtual-network-nsg-troubleshoot-portal.md)
- [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)

Se você configurou grupos de segurança de rede (NSGs) em sua máquina virtual (VM) e está enfrentando problemas de conectividade de máquina virtual, este artigo fornece uma visão geral dos recursos de diagnóstico para NSGs ajudar a solucionar ainda mais.

NSGs permitem que você controle os tipos de tráfego fluxo e sair de suas máquinas virtuais (VMs). NSGs podem ser aplicadas a sub-redes em uma rede Virtual do Azure (VNet), interfaces de rede (NIC) ou ambos. As regras eficazes aplicadas a uma NIC são uma agregação das regras que consta os NSGs aplicados a uma NIC e a sub-rede que ele está conectado ao. Regras entre esses NSGs às vezes podem entrar em conflito com os outros e afetar a conectividade de rede de uma máquina virtual.  

Você pode exibir todas as regras de segurança eficaz de sua NSGs, conforme aplicado em NICs do sua máquina virtual. Este artigo mostra como solucionar problemas de conectividade de máquina virtual usando essas regras no modelo de implantação do Gerenciador de recursos do Azure. Se você não estiver familiarizado com os conceitos de VNet e NSG, leia os artigos de visão geral de [rede Virtual](virtual-networks-overview.md) e [grupos de segurança de rede](virtual-networks-nsg.md) .

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Usando regras de segurança eficaz para solucionar problemas de fluxo de tráfego de máquina virtual

O cenário que segue é um exemplo de um problema de conexão comuns:

Uma máquina virtual chamada *VM1* faz parte de uma sub-rede denominada *Subnet1* dentro de um VNet chamado *WestUS-VNet1*. A tentativa de conexão para a máquina virtual usando o RDP pela porta TCP 3389 falhará. NSGs são aplicadas a NIC *VM1 NIC1* e a sub-rede *Subnet1*. O tráfego para a porta TCP 3389 é permitido no NSG associado com a interface de rede *VM1 NIC1*, porém ping TCP para falha de porta 3389 do VM1.

Embora este exemplo usa a porta TCP 3389, as etapas a seguir podem ser usadas para determinar falhas de conexão de entrada e saída por qualquer porta.

## <a name="detailed-troubleshooting-steps"></a>Detalhadas etapas de solução de problemas
Conclua estas etapas para solucionar problemas de NSGs para uma máquina virtual:

1. Inicie uma sessão do PowerShell do Azure e faça logon no Azure. Se você não estiver familiarizado com o uso do PowerShell do Azure, leia o artigo [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md) .

2. Digite o seguinte comando para retornar todas as regras NSG aplicadas a uma NIC denominada *NIC1 VM1* no grupo de recursos *RG1*:

        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] Se você não souber o nome de uma NIC, insira o seguinte comando para recuperar os nomes de todas as NICs de um grupo de recursos: 
    
    >`Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`

    O texto a seguir é um exemplo da saída regras eficaz retornado para *VM1 NIC1* NIC:

        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
        
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]

    Observe as seguintes informações na saída:

    - Há duas seções de **NetworkSecurityGroup** : uma é associada a uma sub-rede (*Subnet1*) e um está associado uma NIC (*VM1 NIC1*). Neste exemplo, um NSG foi aplicado a cada.
    - **Associação** mostra o recurso (sub-rede ou NIC) um determinado NSG está associado. Se o recurso NSG for movida/desassociado imediatamente antes de executar este comando, talvez você precise esperar alguns segundos para que a alteração para refletir na saída do comando. 
    - Os nomes de regra que são prefaciados com *defaultSecurityRules*: NSG um quando for criado, várias regras de segurança padrão são criadas dentro dela. Regras padrão não podem ser removidas, mas eles podem ser substituídos com regras de prioridade mais altas.
     Leia o artigo de [Visão geral NSG](virtual-networks-nsg.md#default-rules) para saber mais sobre as regras de segurança do NSG padrão.
    - **ExpandedAddressPrefix** expande os prefixos de endereço para NSG marcas de formatação padrão. Marcas representam vários prefixos de endereço. Expansão das marcas pode ser úteis quando Solucionando problemas de conectividade de máquina virtual de/para prefixos de endereço específico. Por exemplo, com VNET correspondência, marca VIRTUAL_NETWORK expande para mostrar peered VNet prefixos na saída anterior.

        >[AZURE.NOTE] As comando apenas mostra eficaz regras se um NSG estiver associado uma sub-rede, uma NIC ou ambos. Uma máquina virtual pode ter várias placas de rede com NSGs diferentes aplicados. Quando a solução de problemas, execute o comando para cada NIC.
        
3. Para facilitar a filtragem ao longo de um número maior de regras NSG, digite os seguintes comandos para solucionar ainda mais: 

        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView

    Um filtro para o tráfego RDP (porta TCP 3389), é aplicado ao modo de exibição de grade, conforme mostrado na figura a seguir:

    ![Lista de regras](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
    
4. Como você pode ver no modo de exibição de grade, há dois permitirem e negar regras para RDP. A saída da etapa 2 mostra que a regra de *DenyRDP* está na NSG aplicado à sub-rede. Para regras de entrada, NSGs aplicados à sub-rede são processados primeiro. Se uma correspondência for encontrada, o NSG aplicado à interface de rede não é processada. Nesse caso, a regra de *DenyRDP* da sub-rede bloqueia RDP para a máquina virtual (**VM1**).

    >[AZURE.NOTE] Uma máquina virtual pode ter várias placas de rede anexadas. Cada pode ser conectado a uma sub-rede diferente. Como os comandos nas etapas anteriores são executados em relação a uma NIC, é importante garantir que você especificar a NIC que você está tendo a falha de conectividade para. Se você não tiver certeza, você sempre pode executar os comandos em relação a cada NIC anexado a máquina virtual.

5. Para RDP em VM1, altere a regra de *Negar RDP (3389)* para *Permitir RDP(3389)* em **Subnet1-NSG** NSG. Confirme que a porta TCP 3389 esteja aberta abrindo uma conexão de RDP para a máquina virtual ou usando a ferramenta de PsPing. Você pode saber mais sobre PsPing lendo a [página de download do PsPing](https://technet.microsoft.com/sysinternals/psping.aspx)

    Você pode ou remove as regras de um NSG usando as informações na saída do seguinte comando:

        Get-Help *-AzureRmNetworkSecurityRuleConfig
        

## <a name="considerations"></a>Considerações

Considere os seguintes pontos ao solucionar problemas de conectividade:

- Regras NSG padrão bloqueará o acesso de entrada da internet e permitir apenas VNet o tráfego de entrada. Regras devem ser explicitamente adicionadas para permitir o acesso de entrada da Internet, conforme necessário.
- Se não houver nenhuma regra de segurança NSG causando conectividade de rede de uma máquina virtual falha, o problema pode ser devido a:
    - Software de firewall executado no sistema operacional da VM
    - Rotas configuradas para dispositivos virtuais ou tráfego de local. Tráfego de Internet pode ser redirecionado para o local por meio de túnel forçada. Uma conexão de RDP/SSH da Internet para sua máquina virtual pode não funcionar com essa configuração, dependendo de como o hardware de rede local lida com esse tráfego. Leia o artigo de [Solução de problemas de rotas](virtual-network-routes-troubleshoot-powershell.md) para aprender a diagnosticar problemas de rota que podem ser impedir o fluxo de tráfego e sair a máquina virtual. 
- Se você tiver peered VNets, por padrão, a marca VIRTUAL_NETWORK se expandirá automaticamente para incluir prefixos para VNets peered. Você pode exibir esses prefixos na lista **ExpandedAddressPrefix** , para solucionar qualquer problema relacionado à conectividade aos VNet. 
- Regras de segurança efetivas são mostradas somente se houver um NSG associado a máquina virtual NIC e ou sub-rede. 
- Não se houver nenhum NSGs associados a NIC ou sub-rede e você tem um endereço IP público atribuído a sua máquina virtual, todas as portas serão abertas para acesso de entrada e saído. Se a máquina virtual tem um endereço IP público, aplicar NSGs ao sub-rede ou NIC é recomendável.  
