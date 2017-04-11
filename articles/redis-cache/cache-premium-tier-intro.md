<properties 
    pageTitle="Introdução para o nível Premium de Cache do Azure relacionada | Microsoft Azure" 
    description="Saiba como criar e gerenciar persistência relacionada, relacionada agrupamento e suporte VNET para suas instâncias do Azure relacionada Cache de nível Premium" 
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

# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Introdução para o nível Premium de Cache do Azure relacionada
Cache Azure relacionada é um cache distribuído, gerenciado que ajuda você a criar aplicativos altamente escaláveis e responde fornecendo acesso super rápido aos seus dados. 

O novo nível de Premium é uma camada pronta Enterprise, que inclui todos os recursos padrão camadas e muito mais, como o melhor desempenho, cargas de trabalho maiores, recuperação, importação/exportação e segurança aprimorada. Continue lendo para saber mais sobre os recursos adicionais da camada de cache Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Melhor desempenho em comparação com o nível padrão ou básico
**Melhor desempenho sobre padrão ou básica camada.** Caches na camada Premium são implantados em hardware que tem processadores mais rápidos e fornece melhor desempenho em comparação ao Basic ou camada padrão. Caches de nível Premium têm latências menores e maior produtividade. 

**Taxa de transferência para o mesmo tamanho Cache é maior no Premium em comparação com o nível padrão.** Por exemplo, a taxa de transferência de um 53 P4 GB (Premium) cache é 250 mil solicitações por segundo em comparação com 150 K para C6 (padrão).

Para obter mais informações sobre o tamanho, a produtividade e largura de banda com caches premium, consulte [FAQ de Cache do Azure relacionada](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Relacionada persistência de dados
A camada de Premium permite que você manter os dados de cache em uma conta de armazenamento do Azure. Em um cache Basic/padrão todos os dados são armazenados apenas na memória. No caso de infraestrutura subjacente questões lá podem ser possibilidade de perda de dados. É recomendável usar o recurso de persistência de dados relacionada na camada Premium para aumentar a resiliência contra perda de dados. Cache de relacionada Azure oferece RDB e AOF (em breve) opções no [relacionada persistência](http://redis.io/topics/persistence). 

Para obter instruções sobre como configurar persistência, consulte [como configurar persistência para um Premium Azure relacionada Cache](cache-how-to-premium-persistence.md).

##<a name="redis-cluster"></a>Relacionada cluster
Se você quiser criar caches maior que 53 GB ou deseja dados fragmentar em vários nós relacionada, você pode usar relacionada cluster que está disponível no nível Premium. Cada nó consiste em um par de cache primário/réplica gerenciado pelo Azure para alta disponibilidade. 

**Agrupamento de relacionada lhe produtividade e escala máxima.** Produtividade aumenta linear à medida que você aumenta o número de fragmentos (nós) no cluster. Por exemplo Se você cria um cluster de P4 de 10 fragmentos, então a taxa de transferência disponível é 250K * 10 = solicitações de 2,5 milhões por segundo. Consulte o [Azure relacionada Cache perguntas Frequentes](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para obter mais detalhes sobre o tamanho, a produtividade e largura de banda com caches premium.

Para começar a usar clusters, consulte [como configurar clusters para um Premium Azure relacionada Cache](cache-how-to-premium-clustering.md).

##<a name="enhanced-security-and-isolation"></a>Isolamento e segurança aprimorada

Caches criados na camada Basic ou padrão são acessíveis na internet pública. Acesso ao Cache é restrito com base na tecla de acesso. Com a camada de Premium ainda mais você pode garantir que somente os clientes dentro de uma rede especificada podem acessar o Cache. Você pode implantar Cache relacionada em uma [Rede Virtual do Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). Você pode usar todos os recursos de VNet como sub-redes, políticas de controle de acesso, e outros recursos para obter mais restringem o acesso relacionada.

Para obter mais informações, consulte [como configurar o suporte de rede Virtual para um Premium Azure relacionada Cache](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importação/exportação

Importação/exportação é uma operação de gerenciamento de dados do Azure relacionada Cache que permite importar dados para o Cache do Azure relacionada ou exportar dados do Azure relacionada Cache por importar e exportar um instantâneo de banco de dados de Cache relacionada (RDB) de um cache premium em um blob de página em uma conta de armazenamento do Azure. Isso permite que você migrar entre diferentes instâncias do Azure relacionada Cache ou preencher o cache com dados antes de usar.

Importar pode ser usado para trazer relacionada compatível RDB arquivos de qualquer servidor relacionada em execução em qualquer nuvem ou ambiente, incluindo relacionada em execução no Linux, Windows ou qualquer provedor de nuvem como Amazon Web Services e outras pessoas. Importar dados é uma maneira fácil de criar um cache com dados preenchidos. Durante o processo de importação, Azure relacionada Cache carrega os arquivos RDB de armazenamento do Azure na memória e então insere as chaves para o cache.

Exportação permite que você exporte os dados armazenados no Azure relacionada Cache relacionada compatível RDB (s). Você pode usar esse recurso para mover dados de uma instância de Cache relacionada do Azure para outro ou para outro servidor relacionada. Durante o processo de exportação, um arquivo temporário é criado na máquina virtual que hospeda a instância do servidor de Cache relacionada do Azure e o arquivo é carregado para a conta de armazenamento designado. Quando a operação de exportação for concluído com qualquer um status de sucesso ou falha, o arquivo temporário é excluído.

Para obter mais informações, consulte [como importar dados e exportar dados do Azure relacionada Cache](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Reinicialização

A camada de premium permite reinicializar um ou mais nós do seu cache sob demanda. Isso permite que você teste seu aplicativo para resiliência em caso de uma falha. Você pode reinicializar os nós a seguir.

-   Nó mestre do cache
-   Nó auxiliar do cache
-   Nós mestre e auxiliar do cache
-   Ao usar um cache premium com clusters, você pode reinicializar o mestre, auxiliar ou ambos os nós para fragmentos individuais no cache

Para obter mais informações, consulte [Perguntas frequentes sobre reinicializar](cache-administration.md#reboot-faq)e [reinicializar](cache-administration.md#reboot) .

## <a name="schedule-updates"></a>Agendar atualizações

O recurso de atualizações agendadas permite designar uma janela de manutenção para seu cache. Quando a janela de manutenção é especificada, todas as atualizações relacionada server são feitas durante esta janela. Para designar uma janela de manutenção, selecione os dias desejados e especifique a manutenção janela Iniciar horas para cada dia. Observe que o horário de janela de manutenção está em UTC. 

Para obter mais informações, consulte [Agendar atualizações](cache-administration.md#schedule-updates) e [Agendar atualizações perguntas Frequentes](cache-administration.md#schedule-updates-faq).

>[AZURE.NOTE] Somente relacionada atualizações são feitas durante a janela de manutenção programada de servidor. A janela de manutenção não se aplica ao Azure atualizações ou atualizações para o sistema operacional de máquina virtual.

## <a name="to-scale-to-the-premium-tier"></a>Dimensionar para o nível premium

Para dimensionar para o nível premium, basta escolha uma das camadas premium na lâmina **alterar preços camada** . Você também pode dimensionar o cache para o nível premium usando o PowerShell e CLI. Para obter instruções passo a passo, consulte [como escala Azure relacionada Cache](cache-how-to-scale.md) e [como automatizar uma operação de escala](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Próximas etapas

Criar um cache e explore os novos recursos de nível premium.

-   [Como configurar persistência para um Premium Azure relacionada Cache](cache-how-to-premium-persistence.md)
-   [Como configurar o suporte de rede Virtual para um Premium Azure relacionada Cache](cache-how-to-premium-vnet.md)
-   [Como configurar clusters para um Premium Azure relacionada Cache](cache-how-to-premium-clustering.md)
-   [Como importar dados para e exportar dados do Azure relacionada Cache](cache-how-to-import-export-data.md)
-   [Como administrar Cache relacionada do Azure](cache-administration.md)
  

