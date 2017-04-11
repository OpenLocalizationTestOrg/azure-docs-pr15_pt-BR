<properties
   pageTitle="Guia de solução de problemas de rota expressa: Obtendo ARP tabelas | Microsoft Azure"
   description="Esta página fornece instruções para obter o ARP tabelas para um circuito de rota expressa."
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

# <a name="expressroute-troubleshooting-guide-getting-arp-tables-in-the-classic-deployment-model"></a>Guia de solução de problemas de rota expressa: Obtendo ARP tabelas no modelo clássico de implantação

> [AZURE.SELECTOR]
[PowerShell - Gerenciador de recursos](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell - clássico](expressroute-troubleshooting-arp-classic.md)

Este artigo explica as etapas para obter as tabelas de protocolo de resolução de endereço (ARP) para seu circuito rota expressa do Azure.

>[AZURE.IMPORTANT] Este documento destina-se para ajudá-lo a diagnosticar e corrigir problemas simples. Ele não tem a intenção de ser uma substituição para o suporte da Microsoft. Se você não consegue resolver o problema usando as seguintes diretrizes, abra uma solicitação de suporte com a [Ajuda do Microsoft Azure + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Resolução Protocol () ARP e tabelas de endereço
ARP é um protocolo de camada 2 que está definido no [RFC 826](https://tools.ietf.org/html/rfc826). ARP é usado para mapear um endereço de Ethernet (endereço MAC) para um endereço IP.

Uma tabela de ARP fornece um mapeamento do endereço IPv4 e endereço MAC de um determinado correspondência. A tabela ARP para uma correspondência de circuito de rota expressa fornece as seguintes informações para cada interface (primário e secundário):

1. Mapeamento de um endereço IP de interface de roteador local para um endereço MAC
2. Mapeamento de um endereço IP de interface de roteador rota expressa para um endereço MAC
3. A idade do mapeamento

Tabelas ARP podem ajudar com Validando configuração de camada 2 e solucionar problemas de conectividade de camada 2 básicos.

A seguir é um exemplo de uma tabela de ARP:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


A seção a seguir fornece informações sobre como exibir as tabelas ARP que são vistas pelos roteadores de borda de rota expressa.

## <a name="prerequisites-for-using-arp-tables"></a>Pré-requisitos para usar tabelas ARP

Certifique-se de que você tenha o seguinte antes de continuar:

 - Um circuito rota expressa válido que está configurado com pelo menos uma correspondência. O circuito deve ser totalmente configurado pelo provedor de conectividade. Você (ou seu provedor de conectividade) configure pelo menos um da peerings (Azure público particular, Azure ou Microsoft) neste circuito.

 - Intervalos de endereços IP que são usados para configurar o peerings (Azure público particular, Azure e Microsoft). Examine os exemplos de atribuição de endereço IP na [página de requisitos de roteamento rota expressa](expressroute-routing.md) para obter uma compreensão de como os endereços IP são mapeados para interfaces no seu aise e rota expressa do lado. Você pode obter informações sobre a configuração de correspondência consultando a [página de configuração de correspondência de rota expressa](expressroute-howto-routing-classic.md).

 - Informações do seu provedor de equipe ou de conectividade de rede sobre os endereços MAC das interfaces que são usadas com estes endereços IP.

 - O módulo mais recente do Windows PowerShell para Azure (versão 1,50 ou posterior).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tabelas de ARP para seu circuito rota expressa
Esta seção fornece instruções sobre como exibir as tabelas ARP para cada tipo de correspondência usando o PowerShell. Antes de continuar, você ou seu provedor de conectividade precisa configurar a correspondência. Cada circuito tem dois caminhos (primárias e secundários). Você pode verificar a tabela ARP para cada caminho de maneira independente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelas ARP para correspondência particular Azure
O cmdlet a seguir fornece o ARP tabelas para correspondência particular Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

A seguir está um exemplo de saída de um dos caminhos de:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabelas de ARP para correspondência público Azure:
O cmdlet a seguir fornece o ARP tabelas para correspondência público Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

A seguir está um exemplo de saída de um dos caminhos de:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


A seguir está um exemplo de saída de um dos caminhos de:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabelas ARP para correspondência da Microsoft
O cmdlet a seguir fornece o ARP tabelas para correspondência da Microsoft:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Exemplo de saída é mostrado abaixo para um dos caminhos de:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Como usar essas informações
A tabela de ARP de uma correspondência pode ser usada para validar a conectividade e a configuração de camada 2. Esta seção fornece uma visão geral da aparência das tabelas ARP em cenários diferentes.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Tabela de ARP quando um circuito estiver em estado operacional (esperado)

 - A tabela de ARP tem uma entrada para o lado local com um endereço IP e MAC válido e uma entrada semelhante para o lado de Microsoft.
 - O último octeto do endereço IP local é sempre um número ímpar.
 - O último octeto do endereço IP da Microsoft é sempre um número par.
 - O mesmo endereço MAC aparece no lado Microsoft para peerings todos os três (primário e secundário).


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Tabela de ARP quando ela é local ou ao lado do provedor de conectividade tem problemas

 Somente uma entrada é exibida na tabela ARP. Mostra o mapeamento entre o endereço MAC e o endereço IP que é usado no lado de Microsoft.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Se você tiver um problema assim, abra uma solicitação de suporte com seu provedor de conectividade para solucioná-lo.


### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Tabela de ARP ao lado de Microsoft tem problemas

 - Você não verá uma tabela de ARP mostrada para uma correspondência se há problemas no lado de Microsoft.
 -  Abra uma solicitação de suporte com a [Ajuda do Microsoft Azure + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especifica que você tenha um problema com a conectividade de camada 2.

## <a name="next-steps"></a>Próximas etapas

 - Valide configurações de camada 3 para seu circuito rota expressa:
     - Obtenha uma rota de resumo para determinar o estado de sessões BGP.
     - Obtenha uma tabela de rota para determinar quais prefixos são anunciados em rota expressa.
 - Valide dados transferência revisando bytes e reduzir.
 - Abra uma solicitação de suporte com [a Ajuda do Microsoft Azure + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se você ainda estiver enfrentando problemas.
