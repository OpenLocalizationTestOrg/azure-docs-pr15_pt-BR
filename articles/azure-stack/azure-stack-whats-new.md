<properties
    pageTitle="O que há de novo no Azure pilha | Microsoft Azure"
    description="O que há de novo na pilha do Azure"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="whats-new-in-azure-stack-technical-preview-2"></a>O que há de novo no Azure pilha Technical Preview 2
Esta versão fornece novos recursos para locatários e administradores.

## <a name="network"></a>Rede   
   - [iDNS](azure-stack-understanding-dns-in-tp2.md) fornece registro de nome de rede interna e resolução de sistema de nome de domínio (DNS) sem a infraestrutura adicional do DNS.
   - [Os gateways de rede virtual](azure-stack-create-vpn-connection-one-node-tp2.md) fornecem opções de conectividade VPN para os recursos do Azure ou local.
   - Rotas de definidos pelo usuário pode rotear o tráfego de rede por meio de firewalls, segurança, outros dispositivos e outros serviços.
   - Você pode criar recursos de rede do Marketplace.   

## <a name="storage"></a>Armazenamento
 - [Filas do Azure](https://msdn.microsoft.com/library/dd179353.aspx) habilitar mensagens de serviço confiável e persistente.
 - Dados de desempenho de armazenamento de captura de [análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343270.aspx) . Você pode usar esses dados para solicitações de rastreamento, analisar tendências de uso e diagnosticar problemas com sua conta de armazenamento.
 - Armazenamento de blob dá suporte à [operação de bloco de acréscimo](https://msdn.microsoft.com/library/azure/mt427365.aspx).
 - Suporte de conta de armazenamento API Premium.
 - Suporte de serviço de armazenamento para ferramentas e SDKs, como o Azure CLI, PowerShell, .NET, Python e Java SDK comuns do locatário. 
 - [Assinatura de acesso compartilhado do armazenamento conta](https://msdn.microsoft.com/library/azure/mt584140.aspx) habilitar delegação granular de acesso aos seus serviços de armazenamento sem precisar compartilhar sua chave da conta completo.  
 - Serviços de armazenamento agora usam [Contas de serviço gerenciadas do grupo](https://technet.microsoft.com/library/hh831477.aspx) de segurança forte com sobrecarga de gerenciamento de baixo.
 - Você pode recuperar recursos de locatário não utilizadas sob demanda.
 - Comprimento de retenção de conta de armazenamento excluída é configurável.
 - Você pode recuperar contas de armazenamento de locatário excluídos.

## <a name="compute"></a>Calcular
- Você pode desalocar máquinas virtuais.
- Você pode reimplantar extensões de máquina virtual para fins de gerenciamento de configuração e solução de problemas.
- Você pode redimensionar discos de máquina virtual.
- Máquinas virtuais pode ter várias interfaces de rede.

### <a name="portal-experience"></a>Experiência do Portal
 - Azure regiões de pilha são uma unidade lógica de escala e gerenciamento no Azure pilha. Nesta visualização, você pode exibir informações sobre os serviços, como computação, rede e armazenamento por região.
 - Agora você pode visualizar a interface de [azure-pilha-updates.md] (atualizações).

## <a name="key-vault"></a>Chave cofre
- [Compartimento de chave na pilha do Azure](azure-stack-kv-intro.md) fornece gerenciamento seguro de suas chaves e senhas para os aplicativos de nuvem.
- Você pode fazer auditoria e monitorar o uso da chave por aplicativos e VMs.

## <a name="billing-and-usage"></a>Faturamento e uso
 - [Consumo de APIs e cobrança](azure-stack-billing-and-chargeback.md) expor dados sobre como seus serviços são consumidos.  
 - Provedores de delegado habilitar revendedores oferecer os serviços do Azure pilha aos seus clientes.

## <a name="monitoring-and-health"></a>Monitoramento e integridade
 - Novos recursos de monitoramento disponíveis no portal e APIs permitem que você proativamente exibir e gerenciar alertas em seu ambiente.  

## <a name="next-steps"></a>Próximas etapas
- [Entender a arquitetura de VDC de pilha Azure](azure-stack-architecture.md)      
- [Compreender os pré-requisitos de implantação](azure-stack-deploy.md)
- [Implantar pilha Azure](azure-stack-run-powershell-script.md)

  
