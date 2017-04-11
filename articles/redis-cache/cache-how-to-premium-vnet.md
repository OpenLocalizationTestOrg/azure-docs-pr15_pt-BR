<properties 
    pageTitle="Como configurar o suporte de rede Virtual para um Premium Azure relacionada Cache | Microsoft Azure" 
    description="Aprenda a criar e gerenciar o suporte de rede Virtual para suas instâncias do Azure relacionada Cache de nível Premium" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Como configurar o suporte de rede Virtual para um Premium Azure relacionada Cache
Cache Azure relacionada tem ofertas de cache diferentes que fornecem flexibilidade na escolha do tamanho do cache e recursos, incluindo o novo nível de Premium.

Os recursos de nível do Azure relacionada Cache premium incluem clusters, persistência e suporte de rede virtual (VNet). Um VNet é uma rede privada na nuvem. Quando uma instância do Azure relacionada Cache é configurada com uma VNet, não é publicamente endereçamento e só podem ser acessado de máquinas virtuais e aplicativos dentro do VNet. Este artigo descreve como configurar o suporte de rede virtual para uma instância de Cache relacionada do Azure premium.

>[AZURE.NOTE] Cache de relacionada Azure suporta os dois clássico e VNets ARM.

Para obter informações sobre outros recursos premium do cache, consulte [Introdução para o nível Premium de Cache do Azure relacionada](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Por que VNet?
Implantação de [Rede Virtual do Azure (VNet)](https://azure.microsoft.com/services/virtual-network/) fornece isolamento para seu Cache relacionada Azure, bem como sub-redes, diretivas de controle de acesso e outros recursos restringir o acesso ao Azure relacionada Cache e segurança aprimorada.

## <a name="virtual-network-support"></a>Suporte de rede virtual
Suporte de rede (VNet) virtual é configurado na lâmina **Novo Cache relacionada** durante a criação de cache. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Depois que você tiver selecionado um nível de preço de premium, você pode configurar integração do Azure relacionada Cache VNet selecionando um VNet que está na mesma assinatura e local como o cache. Para usar um novo VNet, criá-lo primeiro seguindo as etapas em [criar uma rede virtual usando o portal de Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [criar uma rede virtual (clássico) usando o portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) e retorne lâmina **Novo Cache relacionada** para criar e configurar o cache de premium.

Para configurar o VNet para seu novo cache, clique em **Rede Virtual** na lâmina **Novo Cache relacionada** e selecione o VNet desejado na lista suspensa.

![Rede virtual][redis-cache-vnet]

Selecione a sub-rede desejada na lista suspensa **sub-rede** e especifique o **endereço IP estático**de desejado. Se você estiver usando um VNet clássico o campo de **endereço IP estático** é opcional e, se nenhum for especificado, um é escolhido da sub-rede selecionada.

>[AZURE.IMPORTANT] Ao implantar um Cache do Azure relacionada um VNet ARM, o cache deve ser em uma sub-rede dedicada que não contém outros recursos, com exceção de instâncias do Azure relacionada Cache. Se uma tentativa para implantar um Cache do Azure relacionada a uma VNet ARM para uma sub-rede que contém outros recursos, a implantação falhará.

![Rede virtual][redis-cache-vnet-ip]

>[AZURE.IMPORTANT] Os quatro primeiros endereços em uma sub-rede são reservados e não podem ser usados. Para obter mais informações, consulte [existem restrições sobre o uso de endereços IP dentro essas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Após o cache é criado, você pode exibir a configuração para o VNet clicando em **Uma rede Virtual** da lâmina **configurações** .

![Rede virtual][redis-cache-vnet-info]


Para conectar-se à sua instância de cache Azure relacionada ao usar um VNet, especifique o nome de host do cache na cadeia de conexão, conforme mostrado no exemplo a seguir.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>VNet de Cache relacionada Azure perguntas Frequentes

A lista a seguir contém respostas para perguntas frequentes sobre o dimensionamento de Cache relacionada do Azure.

-   [Quais são alguns problemas comuns de configuração incorreta com Cache de relacionada do Azure e VNets?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
-   [Pode usar VNets com um cache padrão ou básico?](#can-i-use-vnets-with-a-standard-or-basic-cache)
-   [Por que criar um cache relacionada falha em algumas sub-redes, mas não outros?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
-   [Todos os recursos de cache funcionam ao hospedar um cache em um VNET?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)


## <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Quais são alguns problemas comuns de configuração incorreta com Cache de relacionada do Azure e VNets?

Quando o Azure relacionada Cache estiver hospedado em um VNet, as portas na tabela a seguir são usadas. Se essas portas estiverem bloqueadas, o cache pode não funcionar corretamente. Ter um ou mais dessas portas bloqueados é o problema mais comum de configuração incorreta ao usar o Azure relacionada Cache em um VNet.

| Portas     | Direção        | Protocolo de transporte | Finalidade                                                                           | IP remoto                           |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443     | Saída         | TCP                | Relacionada dependências no Azure armazenamento/PKI (Internet)                                | *                                   |
| 53          | Saída         | TCP/UDP            | Relacionada dependências em DNS (Internet/VNet)                                         | *                                   |
| 6379, 6380  | Entrada          | TCP                | Comunicação de cliente para relacionada, balanceamento de carga do Azure                               | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 8443        | Entrada/saída | TCP                | Detalhes de implementação para relacionada                                                   | VIRTUAL_NETWORK                     |
| 8500        | Entrada          | TCP/UDP            | Balanceamento de carga de Azure                                                              | AZURE_LOADBALANCER                  |
| 10221-10231 | Entrada/saída | TCP                | Detalhes de implementação para relacionada (podem restringir o ponto de extremidade remoto para VIRTUAL_NETWORK) | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 13000-13999 | Entrada          | TCP                | Comunicação de cliente para Clusters relacionada, balanceamento de carga do Azure                      | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 15000-15999 | Entrada          | TCP                | Comunicação de cliente para Clusters relacionada, balanceamento de carga do Azure                      | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 16001       | Entrada          | TCP/UDP            | Balanceamento de carga de Azure                                                              | AZURE_LOADBALANCER                  |
| 20226       | Entrada + saída | TCP                | Detalhes de implementação de Clusters relacionada                                          | VIRTUAL_NETWORK                     |


Há requisitos de conectividade de rede de Cache Azure relacionada que não pode ser atendida inicialmente em uma rede virtual. Cache de relacionada Azure requer todos os itens a seguintes para funcionar corretamente quando usada em uma rede virtual.

-  Conectividade de rede de saída para pontos de extremidade de armazenamento do Azure em todo o mundo. Isso inclui pontos de extremidade localizados na mesma região como a instância do Azure relacionada Cache, bem como pontos de extremidade de armazenamento localizado em **outras** regiões Azure. Pontos de extremidade de armazenamento Azure resolver em domínios DNS a seguir: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*e *file.core.windows.net*. 
-  Conectividade de rede de saída para *ocsp.msocsp.com*, *mscrl.microsoft.com* e *crl.microsoft.com*. Essa conectividade é necessário para oferecer suporte à funcionalidade SSL.
-  A configuração de DNS para a rede virtual deve ser capaz de resolver todos os pontos de extremidade e domínios mencionados os pontos anteriores. Esses requisitos de DNS podem ser atendidos, garantindo uma infraestrutura DNS válida é configurada e mantida para a rede virtual.



### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Pode usar VNets com um cache padrão ou básico?

VNets só pode ser usado com caches premium.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Por que criar um cache relacionada falha em algumas sub-redes, mas não outros?

Se você estiver implantando um Cache do Azure relacionada a um VNet ARM, o cache deve ser em uma sub-rede dedicada que não contém nenhum outro tipo de recurso. Se uma tentativa para implantar um Cache do Azure relacionada a uma sub-rede BRAÇO VNet que contém outros recursos, a implantação falhará. Você deve excluir os recursos existentes dentro da sub-rede antes de criar um novo cache relacionada.

Você pode implantar vários tipos de recursos para um VNet clássico enquanto você tiver bastante endereços IP disponíveis.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Todos os recursos de cache funcionam ao hospedar um cache em um VNET?

Quando o cache for parte de um VNET, somente os clientes na VNET podem acessar o cache. Como resultado, os seguintes recursos de gerenciamento de cache não funcionam no momento.

-   Relacionada Console - como relacionada Console usa o cliente de cli.exe relacionada hospedado em VMs que não fazem parte de sua VNET, ele não consegue se conectar ao seu cache.


## <a name="use-expressroute-with-azure-redis-cache"></a>Use rota expressa com Cache relacionada Azure

Clientes possam se conectar um circuito de [Rota expressa do Azure](https://azure.microsoft.com/services/expressroute/) em sua infraestrutura de rede virtual, portanto, ampliando sua rede local no Azure. 

Por padrão, um circuito de rota expressa recém-criado anuncia uma rota padrão que permite a conectividade de Internet de saída. Com essa configuração, aplicativos cliente são capazes de se conectar a outros pontos de extremidade Azure, incluindo Azure relacionada Cache.

No entanto, uma configuração de cliente comum é definir seu próprios rota padrão (0.0.0.0/0) que força o tráfego de Internet de saída para fluxo em vez disso, o local. Este fluxo de tráfego invariavelmente quebras conectividade com o Azure relacionada Cache porque o tráfego de saída é locais bloqueados ou NAT faria para um conjunto não reconhecido de endereços que já não funcionam com vários pontos de extremidade Azure.

A solução é definir uma (ou mais) definidos pelo usuário rotas (UDRs) na sub-rede que contém o Cache do Azure relacionada. Um UDR define rotas de sub-rede específicos que serão atendidas em vez da rota padrão.

Se possível, é recomendável usar a seguinte configuração:

- A configuração de rota expressa anuncia 0.0.0.0/0 e por padrão forçar túneis todo o tráfego de saída local.
- O UDR aplicado à sub-rede que contém o Cache do Azure relacionada define 0.0.0.0/0 com um tipo de salto próxima de Internet (um exemplo disto é futuramente para baixo neste artigo).

O efeito combinado destas etapas é que o nível de sub-rede UDR tem precedência sobre a rota expressa forçada túnel garantindo acesso de Internet de saída do Azure relacionada Cache.

Embora a conexão com uma instância do Azure relacionada Cache de um local aplicativo usando rota expressa não é um cenário de uso típico devido a questões de desempenho (para melhor desempenho Azure relacionada Cache clientes devem estar na mesma região como o Cache do Azure relacionada), neste cenário, que o caminho de rede de saída não pode percorrer proxies corporativos internos, nem pode ser forçar encapsulado para o local. Isso altera o endereço NAT eficaz de tráfego de rede de saída do Azure relacionada Cache. Alterar o endereço NAT de um Cache do Azure relacionada o tráfego de rede saída da instância causa falhas de conectividade para muitos dos pontos de extremidade listados acima. Isso resulta na criação do Azure relacionada Cache tentativas.

**Importante:**  As rotas definidas em UDR **deve** ser específicos o suficiente para precedência sobre quaisquer rotas anunciadas pela configuração rota expressa. O exemplo a seguir usa o intervalo de endereços ampla 0.0.0.0/0 e como tal pode potencialmente ser acidentalmente substituído por anúncios de rota usando intervalos de endereços mais específicos.

**Muito importante:**  Cache Azure relacionada não é compatível com configurações de rota expressa **incorretamente entre-anunciar rotas do caminho aos público para o caminho de correspondência particular**. Configurações de rota expressa que tem correspondência público configurado, receberá anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se esses intervalos de endereços incorretamente entre-anunciado no caminho aos particular, o resultado final é que todos os pacotes de rede de saída da sub-rede a instância Azure relacionada Cache são incorretamente forçar-encapsulado em infraestrutura de rede local do cliente. Este fluxo de rede quebras Azure relacionada Cache. A solução para este problema é parar rotas de cruz anúncio do caminho aos público para o caminho de correspondência particular.

Informações básicas sobre rotas definidas pelo usuário estão disponíveis neste [Visão geral](../virtual-network/virtual-networks-udr-overview.md). 

Para obter mais informações sobre a rota expressa, consulte [Visão geral técnica de rota expressa](../expressroute/expressroute-introduction.md)

## <a name="next-steps"></a>Próximas etapas
Saiba como usar mais recursos premium do cache.

-   [Introdução para o nível Premium de Cache do Azure relacionada](cache-premium-tier-intro.md)





  
<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

