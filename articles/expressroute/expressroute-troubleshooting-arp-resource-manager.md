<properties 
   pageTitle="Guia de solução de problemas de rota expressa - obtendo ARP tabelas | Microsoft Azure"
   description="Esta página fornece instruções sobre como obter o ARP tabelas para um circuito de rota expressa"
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carolz"
   editor="tysonn"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/10/2016"
   ms.author="ganesr"/>

#<a name="expressroute-troubleshooting-guide---getting-arp-tables-in-the-resource-manager-deployment-model"></a>Guia de solução de problemas de rota expressa - tabelas de ARP obtendo no modelo de implantação do Gerenciador de recursos

> [AZURE.SELECTOR]
[PowerShell - Gerenciador de recursos](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell - clássico](expressroute-troubleshooting-arp-classic.md)

Este artigo conduz você pelas etapas para saber as tabelas ARP para seu circuito rota expressa. 

>[AZURE.IMPORTANT] Este documento destina-se para ajudá-lo a diagnosticar e corrigir problemas simples. Ele não tem a intenção de ser uma substituição para o suporte da Microsoft. Você deve abrir um tíquete com [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se você não consegue resolver o problema usando a orientação descrita a seguir.

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Resolução Protocol () ARP e tabelas de endereço
Protocolo de resolução de endereço (ARP) é um protocolo de camada 2 definido na [RFC 826](https://tools.ietf.org/html/rfc826). ARP é usado para mapear o endereço de Ethernet (endereço MAC) com um endereço ip.

A tabela ARP fornece um mapeamento do endereço ipv4 e endereço MAC de um determinado correspondência. A tabela ARP para uma correspondência de circuito de rota expressa fornece as seguintes informações para cada interface (primário e secundário)

1. Mapeamento de endereço de ip de interface de roteador de local para o endereço MAC
2. Mapeamento de endereço de ip de interface de roteador de rota expressa para o endereço MAC
3. Idade do mapeamento

Tabelas ARP podem ajudar a validar a configuração de camada 2 e básicas de solução de problemas de camada 2 problemas de conectividade. 

Tabela de ARP de exemplo: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


A seção a seguir fornece informações sobre como você pode ver as tabelas ARP vistas pelos roteadores de borda de rota expressa. 

## <a name="prerequisites-for-learning-arp-tables"></a>Pré-requisitos para obter informações sobre tabelas ARP

Verifique se você tem o seguinte antes de você progresso ainda mais

 - Um circuito de rota expressa válido configurado com pelo menos uma correspondência. O circuito deve ser totalmente configurado pelo provedor de conectividade. Você (ou seu provedor de conectividade) deve ter configurado pelo menos um da peerings (Azure público particular, Azure e Microsoft) neste circuito.
 - Intervalos de endereços IP usados para configurar o peerings (Azure público particular, Azure e Microsoft). Examine os exemplos de atribuição de endereço ip na [página de requisitos de roteamento rota expressa](expressroute-routing.md) para obter uma compreensão de como os endereços ip são mapeados para interfaces no seu lado e rota expressa do lado. Você pode obter informações sobre a configuração de correspondência consultando a [página de configuração de correspondência de rota expressa](expressroute-howto-routing-arm.md).
 - Informações de sua equipe de rede / provedor de conectividade nos endereços MAC de interfaces usado com esses endereços IP.
 - Você deve ter o módulo do PowerShell mais recente para o Azure (versão 1.50 ou mais recente).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Obtendo o ARP tabelas para seu circuito rota expressa
Esta seção fornece instruções sobre como você pode ver as tabelas ARP por correspondência usando o PowerShell. Você ou o seu provedor de conectividade deve ter configurado a correspondência antes progredindo ainda mais. Cada circuito tem dois caminhos (primárias e secundários). Você pode verificar a tabela ARP para cada caminho de maneira independente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelas ARP para correspondência particular Azure
O cmdlet a seguir fornece o ARP tabelas para correspondência particular Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary
        
        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Exemplo de saída é mostrado abaixo para um dos caminhos

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabelas ARP para correspondência público Azure
O cmdlet a seguir fornece o ARP tabelas para correspondência público Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary
        
        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Exemplo de saída é mostrado abaixo para um dos caminhos

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabelas ARP para correspondência da Microsoft
O cmdlet a seguir fornece o ARP tabelas para correspondência da Microsoft

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary
        
        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Exemplo de saída é mostrado abaixo para um dos caminhos

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Como usar essas informações
A tabela de ARP de uma correspondência pode ser usada para determinar validar a configuração de camada 2 e a conectividade. Esta seção fornece uma visão geral de como serão a aparência de tabelas ARP em cenários diferentes.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabela de ARP quando um circuito está no estado operacional (estado esperado)

 - A tabela ARP terá uma entrada para o lado local com um endereço IP válido e o endereço de MAC e uma entrada semelhante para o lado de Microsoft. 
 - O último octeto do endereço ip local sempre será um número ímpar.
 - O último octeto do endereço ip Microsoft sempre será um número par.
 - O mesmo endereço MAC aparecerá no lado Microsoft para todos os 3 peerings (principais / secundários). 


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabela de ARP quando local / lado de provedor de serviços de conectividade tem problemas

 - Apenas uma entrada aparecerá na tabela ARP. Isso mostrará o mapeamento entre o endereço MAC e o endereço IP usado no lado de Microsoft. 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Abra uma solicitação de suporte com seu provedor de conectividade para depurar esses problemas. 


### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabela de ARP ao lado de Microsoft tem problemas

 - Você não verá uma tabela de ARP mostrada para uma correspondência se há problemas no lado de Microsoft. 
 -  Abra um tíquete com [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especifica que você tenha um problema com a conectividade de camada 2. 

## <a name="next-steps"></a>Próximas etapas

 - Validar configurações de camada 3 para seu circuito rota expressa
     - Obter a rota de resumo para determinar o estado de sessões BGP 
     - Obter a tabela de rota para determinar quais prefixos são anunciados em rota expressa
 - Validar dados transferência revisando bytes em / check-out
 - Abra um tíquete com [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se você ainda estiver enfrentando problemas.
