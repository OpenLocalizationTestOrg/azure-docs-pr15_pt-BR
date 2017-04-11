<properties
   pageTitle="Executando Linux VMs em várias regiões para alta disponibilidade | Arquitetura de referência | Microsoft Azure"
   description="Como implantar VMs em vários regiões no Azure para alta disponibilidade e resiliência."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/21/2016"
   ms.author="mwasson"/>

# <a name="running-linux-vms-in-multiple-regions-for-high-availability"></a>Executando Linux VMs em várias regiões para alta disponibilidade

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Executando Linux VMs em várias regiões para alta disponibilidade](guidance-compute-multiple-datacenters-linux.md)
- [Executando o Windows VMs em vários regiões para alta disponibilidade](guidance-compute-multiple-datacenters.md)

Neste artigo, recomendamos um conjunto de práticas para ser executado Linux VMs (máquinas virtuais) em várias regiões Azure, para alcançar disponibilidade e uma infraestrutura de recuperação de desastres robusta.

> [AZURE.NOTE] Azure tem dois diferentes modelos de implantação: [Gerenciador de recursos] [ resource groups] e clássico. Este artigo usa o Gerenciador de recursos, a Microsoft recomenda para novas implantações.

Uma arquitetura de várias regiões pode fornecer maior disponibilidade que implantando em uma única região. Se uma queda regional afeta a região principal, você pode usar o [Gerenciador de tráfego] [ traffic-manager] falha sobre a região secundário. Essa arquitetura também pode ajudar se um subsistema individual do aplicativo falhar.

Há várias abordagens gerais para alcançar alta disponibilidade em data centers:   
  
- Ativo/passivo com o modo de espera ativo. Tráfego vai para uma região, enquanto o outro aguarda em espera. VMs na região secundário estão alocados e em execução em todos os momentos.

- Ativo/passivo com fria espera. O mesmo, mas VMs na região secundário não estão alocados até que seja necessário para failover. Essa abordagem custa menos para executar, mas geralmente terá mais tempo durante uma falha de inatividade.

- Ativo/ativo. Ambas as regiões estão ativas e as solicitações são carga equilibrada entre elas. Se um data center ficar indisponível, ela é considerada fora rotação.

Essa arquitetura se concentra nos ativo/passivo com o modo de espera ativo, usando o Gerenciador de tráfego para failover. Observe que você pode implantar um pequeno número de VMs para o modo de espera ativo e, em seguida, dimensionar conforme necessário.

## <a name="architecture-diagram"></a>Diagrama de arquitetura

O diagrama a seguir se baseia a arquitetura mostrada na [confiabilidade adicionando para uma arquitetura de N camadas no Azure](guidance-compute-n-tier-vm-linux.md). 

> Um documento do Visio que inclui este diagrama de arquitetura está disponível para download no [Centro de download da Microsoft][visio-download]. Este diagrama é em "computação - página de região (Linux) multi.

![[0]][0]

- **Regiões primárias e secundários**. Essa arquitetura usa duas regiões para obter maior disponibilidade. Uma é a região principal. Durante operações normais, o tráfego de rede é roteado para a região principal. Mas se que ficar indisponível, o tráfego é roteado para a região secundária.

- ** [Gerenciador de tráfego do azure] [ traffic-manager] ** roteia solicitações de entrada para a região principal. Se região ficar indisponível, o Gerenciador de tráfego não sobre a região secundário. Para obter mais informações, consulte a seção [Configurando Gerenciador de tráfego](#configuring-traffic-manager).

- **Grupos de recursos**. Criar [grupos de recursos] de separados[ resource groups] para a região principal, a região secundária e para o Gerenciador de tráfego. Isso lhe dá flexibilidade para gerenciar cada região como um único conjunto de recursos. Por exemplo, você poderia reimplantar uma região, sem interromper outro. [Vincular os grupos de recursos][resource-group-links], de modo que você pode executar uma consulta para listar todos os recursos para o aplicativo.

- **VNets**. Crie um VNet separada para cada região. Verifique se que os espaços de endereço não se sobreponham.

- **Apache Cassandra** implantado em dados centros em regiões Azure. Centros de dados de Cassandra são implantados em diferentes regiões Azure para alta disponibilidade. Dentro de cada região, nós são configurados no modo de reconhecimento de rack com falhas e atualizar domínios, para resiliência dentro da região.

## <a name="recommendations"></a>Recomendações

Azure oferece muitos recursos diferentes e tipos de recurso, portanto, essa arquitetura de referência pode ser provisionado muitas maneiras diferentes. Fornecemos um modelo do Gerenciador de recursos do Azure para instalar a arquitetura de referência que segue essas recomendações. Se você optar por criar sua própria arquitetura de referência seguir essas recomendações, a menos que tenha um requisito específico que não correspondam a uma recomendação.

### <a name="regional-pairing"></a>Emparelhamento regionais

Cada região Azure é combinado com outra região dentro a mesma Geografia. Em geral, escolha regiões do mesmo par regional (por exemplo, Leste dos EUA 2 e Central dos EUA). Vantagens de fazê-lo:

- Se houver uma queda de ampla, é priorizamos a recuperação de pelo menos uma região fora cada par.

- Atualizações de sistema Azure planejadas são implementadas em pares regiões sequencialmente, para minimizar o tempo de inatividade possível.

- Pares residem na mesma geografia, para atender aos requisitos de residência de dados.

No entanto, certifique-se de que as duas regiões oferece suporte para todos os serviços Azure necessários para o seu aplicativo (consulte [serviços por região][services-by-region]). Para obter mais informações sobre pares regionais, consulte [desastre e continuidade de negócios recuperação (BCDR): Azure par regiões][regional-pairs].

### <a name="traffic-manager-configuration"></a>Configuração do Gerenciador de tráfego

Considere os seguintes pontos ao configurar o tráfego manager para seu cenário:

- **Roteamento.** Gerenciador de tráfego oferece suporte a vários [algoritmos de roteamento][tm-routing]. Para o cenário descrito neste artigo, use o roteamento de _prioridade_ (anteriormente chamado de roteamento de _failover_ ). Com essa configuração, Gerenciador de tráfego envia todas as solicitações para a região principal, a menos que a região principal fica inacessível. Neste ponto, ele passam automaticamente para a região secundária. Método de [Roteamento]de configurar Failover[tm-configure-failover].

- **Teste de integridade.** Gerenciador de tráfego usa um HTTP (ou HTTPS) [teste] [ tm-monitoring] para monitorar a disponibilidade de cada região. O teste verifica se há uma resposta HTTP 200 para um caminho de URL especificado. Como prática recomendada, crie um ponto de extremidade que relata a integridade geral do aplicativo e usar este ponto de extremidade para o teste de integridade. Caso contrário, o teste pode relatar um ponto de extremidade "Íntegro" quando partes essenciais do aplicativo realmente estão falhando. Para obter mais informações, consulte [Padrão de monitoramento de ponto de extremidade de integridade][health-endpoint-monitoring-pattern].

Quando o Gerenciador de tráfego Falha ao longo, há um período de tempo, quando os clientes não podem acessar o aplicativo, que pode ser alguns minutos. Dois fatores afetam a duração total:

- O teste de integridade deve detectar que o data center principal tornou inacessível.

- Servidores DNS devem atualizar os registros DNS em cache para o endereço IP, que depende do DNS time to live (TTL). O TTL padrão é 300 segundos (5 minutos), mas você pode configurar esse valor quando você cria o perfil do Gerenciador de tráfego.

Para obter detalhes, consulte [Sobre o Gerenciador de tráfego monitoramento][tm-monitoring].

Se Gerenciador de tráfego falhar ao longo, é recomendável executar um failback manual, em vez de automaticamente falhando novamente. Verifique se todos os subsistemas de aplicativo eficaz, primeiro. Caso contrário, você pode criar uma situação onde o aplicativo inverte trocadas entre data centers.

Por padrão, o Gerenciador de tráfego automaticamente falha novamente. Para evitar isso, diminua manualmente a prioridade da região primária após um evento de failover. Por exemplo, suponha que a região primária é prioridade 1 e o secundário é prioridade 2. Após um failover, defina a região primária como prioridade 3, para impedir que o failback automático. Quando você estiver pronto para voltar, atualize a prioridade para 1.

O seguinte comando CLI Azure atualiza a prioridade:

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --priority 3
```    

Outra maneira de evitar Flip-flop é desativar temporariamente o ponto de extremidade:

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --status Disabled
```    

Dependendo da causa de uma falha, talvez seja necessário redploy os recursos dentro de uma região. Antes de falhar novamente, execute um teste de prontidão operacional. O teste deve verificar coisas como:

- VMs estão configuradas corretamente. (Todo o software necessário está instalado, IIS está executando, etc.)

- Subsistemas de aplicativo estão íntegros.

- Testes de funcionamento. (Por exemplo, a camada de banco de dados é acessível da camada de web.)

### <a name="cassandra-deployment-across-multiple-regions"></a>Implantação de Cassandra em várias regiões

Cassandra data centers são divisões das cargas de trabalho: um grupo de nós relacionados configurados juntos em um cluster de diferenciação de replicação e carga de trabalho.

É recomendável [DataStax Enterprise] [ datastax] para produção usar. Para obter mais informações sobre como executar DataStax no Azure, consulte o [Guia de implantação de Enterprise DataStax Azure][cassandra-in-azure]. As seguintes recomendações gerais se aplicam a qualquer edição Cassandra.

- Atribua um endereço IP público a cada nó. Isso permite que os clusters para se comunicar em regiões usando a infraestrutura de backbone Azure, fornecendo produtividade alta a baixo custo.

- Proteger nós usando o firewall apropriado e configurações de NSG, permitindo tráfego somente em hosts conhecido, incluindo clientes e outros nós de cluster. Observe que Cassandra usa portas diferentes para comunicação, OpsCenter, Spark e assim por diante. Para uso da porta no Cassandra, consulte [o acesso de porta de firewall Configurando][cassandra-ports].

- Usar a criptografia SSL para todos os [clientes para nó] [ ssl-client-node] e [para nós] [ ssl-node-node] comunicações.

- Dentro de uma região, siga as diretrizes nas [recomendações Cassandra](guidance-compute-n-tier-vm-linux.md#cassandra-recommendations).

## <a name="availability-considerations"></a>Considerações de disponibilidade

Com um aplicativo de N camadas complexo, você não talvez precise replicar todo o aplicativo na região secundário. Em vez disso, você pode replicar apenas um subsistema crítico que é necessário para oferecer suporte à continuidade de negócios.

Gerenciador de tráfego é um ponto de possível falha do sistema. Se o serviço falhar, os clientes não podem acessar seu aplicativo durante o tempo de inatividade. Examine o [Gerenciador de tráfego SLA][tm-sla]e determine se usando o Gerenciador de tráfego sozinho atende às suas necessidades de negócios para alta disponibilidade. Caso contrário, considere adicionar outra solução de gerenciamento de tráfego como um failback. Se o serviço Gerenciador de tráfego do Azure falhar, altere os registros CNAME no DNS para apontar para o outro serviço de gerenciamento de tráfego. (Esta etapa deve ser executada manualmente e seu aplicativo ficarão indisponível até que as alterações DNS são propagadas.)

Para o cluster Cassandra, os cenários de failover considerar dependem os níveis de consistência usados pelo aplicativo, bem como o número de réplicas usadas. Para níveis de consistência e uso em Cassandra, consulte [consistência de dados Configurando] [ cassandra-consistency] e [Cassandra: quantos nós fala para com Quorum?][cassandra-consistency-usage] Disponibilidade de dados no Cassandra é determinada pelo nível de consistência usado pelo aplicativo e o mecanismo de replicação. Para replicação em Cassandra, consulte [Replicação de dados no explicadas de bancos de dados NoSQL][cassandra-replication].

## <a name="manageability-considerations"></a>Considerações de capacidade de gerenciamento

Quando você atualizar sua implantação, atualize uma região de cada vez, para reduzir a chance de uma falha de global de uma configuração incorreta ou um erro no aplicativo.

Teste a resiliência do sistema para falhas. Aqui estão alguns cenários comuns de falha para testar:

- Encerre instâncias de máquina virtual.

- Recursos de pressão como CPU e memória.

- Rede de desconectar/atraso.

- Falhe processos.

- Expire certificados.

- Simule falhas de hardware.

- Desligar o serviço DNS nos controladores de domínio.

Meça os tempos de recuperação e verifique se que ele atender às suas necessidades de negócios. Teste combinações de modos de falha, também.

## <a name="next-steps"></a>Próximas etapas

Esta série se concentrou em implantações de nuvem puro. Cenários corporativos geralmente exigem uma rede híbridos, conectando a uma rede local com uma rede virtual Azure. Para saber como criar tal rede híbrida, consulte [Implementando uma arquitetura de rede híbrida com o Azure e local VPN][hybrid-vpn].

<!-- Links -->

[azure-sla]: https://azure.microsoft.com/support/legal/sla/
[cassandra-in-azure]: https://academy.datastax.com/resources/deployment-guide-azure
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[cassandra-consistency-usage]: https://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2
[cassandra-ports]: http://docs.datastax.com/en/latest-dse/datastax_enterprise/sec/secConfFirePort.html
[datastax]: https://www.datastax.com/products/datastax-enterprise
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[hybrid-vpn]: guidance-hybrid-network-vpn.md
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../azure-resource-manager/resource-group-overview.md
[resource-group-links]: ../resource-group-link-resources.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssl-client-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLClientToNode_t.html
[ssl-node-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLNodeToNode_t.html
[tablediff]: https://msdn.microsoft.com/en-us/library/ms162843.aspx
[tm-configure-failover]: ../traffic-manager/traffic-manager-configure-failover-routing-method.md
[tm-monitoring]: ../traffic-manager/traffic-manager-monitoring.md
[tm-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[tm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/en-us/services/traffic-manager/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[vnet-to-vnet]: ../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[wsfc]: https://msdn.microsoft.com/en-us/library/hh270278.aspx
[0]: ./media/blueprints/compute-multi-dc-linux.png "Arquitetura de rede altamente disponível para aplicativos de fileiras Azure"
