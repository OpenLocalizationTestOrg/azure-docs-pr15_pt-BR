<properties 
   pageTitle="Configurar conexão VPN entre duas redes virtuais | Microsoft Azure" 
   description="Saiba como configurar conexões VPN e resolução de nomes de domínio entre duas redes virtuais Azure e como configurar replicação HBase geográfica." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/28/2016"
   ms.author="jgao"/>

# <a name="configure-a-vpn-connection-between-two-azure-virtual-networks"></a>Configurar uma conexão VPN entre duas redes virtuais Azure  

> [AZURE.SELECTOR]
- [Configurar a conectividade VPN](hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurar o DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurar a replicação de HBase](hdinsight-hbase-geo-replication.md) 

Conectividade de-to-site de rede virtual Azure usa um gateway VPN para fornecer um túnel seguro usando Ipsec/IKE. O VNets podem estar em diferentes regiões e assinaturas diferentes. Você mesmo pode combinar VNet para VNet a comunicação com configurações de vários locais. Há vários motivos para VNet à conectividade de VNet:

- Cross redundância região geográfica e localização geográfica-presença 
- Aplicativos de várias camadas regionais com o limite de isolamento fortes 
- Cross assinatura, comunicação entre organizações no Azure

Para obter mais informações, consulte [Configurar um VNet para conexão de VNet](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). 

Para vê-la em vídeo:

> [AZURE.VIDEO configure-the-vpn-connectivity-between-two-azure-virtual-networks]

Este tutorial é uma parte da [série] de[ hdinsight-hbase-replication] sobre a criação de replicação HBase geográfica. 

- Configurar uma conectividade VPN entre duas redes virtuais (Este tutorial)
- [Configurar o DNS para as redes virtuais][hdinsight-hbase-geo-replication-dns]
- [Configurar a replicação de localização geográfica HBase][hdinsight-hbase-geo-replication]

O diagrama a seguir ilustra as duas redes virtuais que você criará neste tutorial:

![Diagrama de rede virtual de replicação de HDInsight HBase][img-vnet-diagram]
 

##<a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o PowerShell do Azure**.

    Antes de executar scripts do PowerShell, verifique se que você está conectado à sua assinatura do Azure usando o seguinte cmdlet:

        Add-AzureAccount

    Se você tiver várias assinaturas Azure, use o cmdlet a seguir para configurar a assinatura atual:

        Select-AzureSubscription <AzureSubscriptionName>
        
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


>[AZURE.NOTE] Nomes de serviço Azure e máquina virtual deve ser exclusivo. O nome usado neste tutorial é Contoso-[nome do serviço do Azure/máquina virtual]-[UE / EUA]. Por exemplo, Contoso-VNet-UE é a rede virtual Azure no Centro de dados do Norte da Europa; Contoso-DNS-US é o servidor DNS máquina virtual no data center Leste EUA. Você deve criar seus próprios nomes.
 

##<a name="create-two-azure-vnets"></a>Criar duas VNets do Azure



**Para criar uma rede virtual chamado Contoso-VNet-UE na Europa do Norte**

1.  Entre [Portal de clássico Azure][azure-portal].
2.  Clique em **novo**, **Serviços de rede**, **rede VIRTUAL**, **Criar personalizada**.
3.  Inserir:

    - **Nome**: Contoso-VNet-UE
    - **Local**: norte da Europa

        Este tutorial usa Norte da Europa e EUA Leste dos data centers. Você pode escolher seu próprio dos data centers.
4.  Inserir:

    - **Servidor DNS**: (deixe em branco) 
    
        Você precisará seu próprio servidor DNS para resolução de nomes em redes virtuais. Para obter mais informações sobre quando utilizar a resolução de nome fornecido Azure e quando usar seu próprio servidor DNS, consulte [Resolução de nomes (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Para obter instruções configurar a resolução de nomes entre VNets, consulte [Configurar DNS entre duas redes virtuais Azure][hdinsight-hbase-dns].
  
    - **Configurar uma VPN de ponto-a-site**: (desmarcado)

        Ponto-a-site não se aplica a esse cenário.

    - **Configurar uma VPN to-site**: (desmarcado)
    
        Você irá configurar a conexão de VPN-to-site à rede virtual Azure no data center Leste EUA.
5.  Inserir:

    -   **IP de inicial do espaço de endereço**: 10.1.0.0
    -   **CIDR de espaço de endereço**: / 16
    -   **IP de sub-rede-1 Iniciando**: 10.1.0.0
    -   **CIDR sub-rede-1**: / 24

    O espaço de endereço não pode sobrepor com a rede virtual dos EUA.  

**Para criar uma rede virtual chamado Contoso-VNet-UE na Europa Oeste**

- Repita o procedimento último com os seguintes valores:

    - **Nome**: Contoso-VNet-US
    - **Local**: Leste EUA
     
    - **Servidor DNS**: (deixe em branco)
    - **Configurar uma VPN de ponto-a-site**: (desmarcado)
    - **Configurar uma VPN to-site**: (desmarcado)
     
    - **IP de inicial do espaço de endereço**: 10.2.0.0
    - **CIDR de espaço de endereço**: / 16
    - **IP de sub-rede-1 Iniciando**: 10.2.0.0
    - **CIDR sub-rede-1**: / 24

















##<a name="configure-a-vpn-connection-between-the-two-vnets"></a>Configurar uma conexão VPN entre os dois VNets

###<a name="create-local-networks"></a>Criar redes locais

Quando você cria um VNet VNet configuração, você precisa configurar cada VNet para identificar os outros como um site de rede local. Nesta seção, você vai configurar cada VNet como uma rede local. As redes locais compartilham os mesmos espaços de endereço IP com o VNet correspondente.

![Configurar configuração do Azure VPN-to-site - azure redes locais][img-vnet-lnet-diagram]


**Para criar uma rede local chamado Contoso-LNet-UE correspondência o espaço de endereço de rede de Contoso-VNet-UE**

1. No Portal do Azure clássico, clique em **novo**, **Serviços de rede**, **Uma rede VIRTUAL**, **Adicionar rede LOCAL**.
3. Inserir:

    - **Nome**: Contoso-LNet-UE
    - **Endereço de IP do dispositivo de VPN**: 192.168.0.1 (este endereço será atualizado mais tarde)

        Normalmente, você poderia usar o endereço IP externo real para um dispositivo VPN. Para VNet VNet configurações, você usará o endereço IP de gateway VPN. Considerando que você não tiver criado os gateways VPN para os dois VNets ainda, insira um endereço IP de arbitary e voltar para corrigi-lo.
4.  Inserir:

    - **IP de inicial do espaço de endereço:** 10.1.0.0
    - **CIDR de espaço de endereço:** /16
    
    Isso deve corresponder exatamente para o intervalo que você especificou anteriormente para Contoso-VNet-UE.

**Para criar uma rede local chamado Contoso-LNet-US correspondência o espaço de endereço de rede de Contoso-VNet-US**

- Repita o procedimento último com os seguintes parâmetros:

    - **Nome**: Contoso-LNet-US
    - **Endereço de IP do dispositivo de VPN**: 192.168.0.1 (este endereço será atualizado mais tarde)
     
    - **IP de inicial do espaço de endereço**: 10.2.0.0
    - **CIDR de espaço de endereço**: / 16


###<a name="create-vpn-gateways"></a>Criar gateways VPN

Há duas partes nessa configuração. Primeiro você configura uma conexão de-to-site VNet a uma rede local e, em seguida, você cria uma VPN de roteamento dinâmica. VNet para VNet requer gateways VPN Azure com VPNs roteamento dinâmicos. Não há suporte para o Azure VPNs roteamento estáticas.

**Para configurar a conexão de-to-site Contoso-VNet-UE Contoso-LNet-US**

1.  No Portal do Azure clássico, clique em **redes** no painel esquerdo,
2.  Clique em **Contoso-VNet-UE**.
3.  Clique na guia **Configurar** .
4.  Marque **conectar à rede local**.
5.  Na **Rede LOCAL**, selecione **Contoso-LNet-US**.
6.  Clique em **Adicionar sub-rede de gateway** na seção de espaços de endereço de rede virtual.
7.  Clique em **Salvar**.
8.  Clique em **Okey** para confirmar.


**Para criar um gateway VPN para Contoso-VNet-UE**

1.  Azure clássico no Portal do, clique na guia do **painel** .
4.  Clique em **Criar GATEWAY** na parte inferior da página e clique em **Roteamento dinâmico**.
5.  Clique em **Sim** para confirmar. Observe o gráfico de gateway na página altera para amarelo e diz criando Gateway. Normalmente leva cerca de 15 minutos para o gateway criar.

    Quando o status do gateway muda para conectar-se, o endereço IP para cada Gateway ficará visível no painel de controle. Anote o endereço IP que corresponde a cada VNet, tomando cuidado para não confundi-los. Esses são os endereços IP que serão usados quando você edita os endereços IP de espaço reservado para o dispositivo de VPN em redes locais.

6.  Faça uma cópia do **Endereço IP do GATEWAY**. Você usará-lo para configurar o endereço IP de gateway VPN para Contoso-VNet-UE na próxima seção.

**Para criar um gateway VPN para Contoso-VNet-UE**

- Repita o procedimento duas últimas para configurar a conectividade de-to-site Contoso-VNet-US Contoso-LNet-UE e a criar um gateway VPN para Contoso-Vnet-US. Quando terminar, você terá o endereço IP de gateway VPN para Contoso-VNet-US.


### <a name="set-the-vpn-device-ip-addresses-for-local-networks"></a>Defina o dispositivo de VPN endereços IP para redes locais
Na seção anterior, você pode criar um gateway VPN para cada um da VNets. Você tem os endereços IP dos gateways VPN. Agora você pode voltar para configurar endereços IP de dispositivo VPN de rede local.

**Para configurar o endereço IP do dispositivo VPN para Contoso-LNet-UE** 

1.  No Portal do Azure clássico, clique em **redes** no painel esquerdo.
2.  Clique em **Redes locais** da parte superior.
3.  Clique em **Contoso-LNet-UE**e clique em **Editar** na parte inferior.
4.  Atualize o **endereço de IP do dispositivo de VPN**.  Este é o endereço que você obtém da guia Painel da Contoso-VNET-UE.
5.  Clique no botão à direita.
6.  Clique no botão de seleção.

**Para configurar o endereço IP do dispositivo VPN para Contoso-LNet-US** 

- Repita o procedimento último para configurar o endereço IP do dispositivo VPN para Contoso-LNet-US.

###<a name="set-vnet-gateway-keys"></a>Definir chaves de gateway VNet

Os gateways de Vnet usam uma chave compartilhada para autenticar conexões entre as redes virtuais. A chave não pode ser configurada a partir do Portal de clássico do Azure. Você deve usar o PowerShell ou .NET SDK.

**Para definir as teclas**

1. Em sua estação de trabalho, abra **O Windows PowerShell ISE** ou console do Windows PowerShell.
2. Atualizar os parâmetros neste script de acompanhamento e executá-lo:

        Add-AuzreAccount
        Select-AzureSubscription -[AzureSubscriptionName]
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-EU -LocalNetworkSiteName Contoso-LNet-US -SharedKey A1b2C3D4
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-US -LocalNetworkSiteName Contoso-LNet-EU -SharedKey A1b2C3D4 


##<a name="check-the-vpn-connection"></a>Verificar a conexão de VPN 

Sem qualquer VMs implantadas para o VNets, você pode usar o diagrama de rede virtual visual página VNet Dashboard no Portal de clássico do Azure para verificar o status de conexão:

![Rede virtual de replicação de HDInsight HBase status de conexão VPN][img-vpn-status]
  



##<a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como configurar uma conexão VPN entre duas redes virtuais Azure. Os outros dois artigos na série abrangem:

- [Configurar o DNS entre duas redes virtuais Azure][hdinsight-hbase-geo-replication-dns]
- [Configurar a replicação de localização geográfica HBase][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com

[powershell-install]: ../install-configure-powershell.md



[hdinsight-hbase-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-dns]: hdinsight-hbase-geo-replication-configure-dns.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-diagram.png
[img-vnet-lnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-lnet-diagram.png
[img-vpn-status]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-status.png 
