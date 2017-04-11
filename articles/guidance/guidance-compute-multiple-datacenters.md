<properties
   pageTitle="Executando o Windows VMs em vários regiões para alta disponibilidade | Arquitetura de referência | Microsoft Azure"
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

# <a name="running-windows-vms-in-multiple-regions-for-high-availability"></a>Executando o Windows VMs em vários regiões para alta disponibilidade

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Executando Linux VMs em várias regiões para alta disponibilidade](guidance-compute-multiple-datacenters-linux.md)
- [Executando o Windows VMs em vários regiões para alta disponibilidade](guidance-compute-multiple-datacenters.md)

Neste artigo, recomendamos um conjunto de práticas para executar máquinas virtuais do Windows (VMs) em várias regiões Azure, para alcançar disponibilidade e uma infraestrutura de recuperação de desastres robusta.

> [AZURE.NOTE] Azure tem dois diferentes modelos de implantação: [Gerenciador de recursos] [ resource groups] e clássico. Este artigo usa o Gerenciador de recursos, a Microsoft recomenda para novas implantações.

Uma arquitetura de várias regiões pode fornecer maior disponibilidade que implantando em uma única região. Se uma queda regional afeta a região principal, você pode usar o [Gerenciador de tráfego] [ traffic-manager] falha sobre a região secundário. Essa arquitetura também pode ajudar se um subsistema individual do aplicativo falhar. 

Há várias abordagens gerais para alcançar alta disponibilidade em data centers:

- Ativo/passivo com o modo de espera ativo. Tráfego vai para uma região, enquanto o outro aguarda em espera. VMs na região secundário estão alocados e em execução em todos os momentos.

- Ativo/passivo com fria espera. O mesmo, mas VMs na região secundário não estão alocados até que seja necessário para failover. Essa abordagem custa menos para executar, mas geralmente terá mais tempo durante uma falha de inatividade.

- Ativo/ativo. Ambas as regiões estão ativas e as solicitações são carga equilibrada entre elas. Se um data center ficar indisponível, ela é considerada fora rotação. 

Essa arquitetura se concentra nos ativo/passivo com o modo de espera ativo, usando o Gerenciador de tráfego para failover. Observe que você pode implantar um pequeno número de VMs para o modo de espera ativo e, em seguida, dimensionar conforme necessário.

## <a name="architecture-diagram"></a>Diagrama de arquitetura

O diagrama a seguir se baseia a arquitetura mostrada na [confiabilidade adicionando para uma arquitetura de N camadas no Azure](guidance-compute-n-tier-vm.md).

> Um documento do Visio que inclui este diagrama de arquitetura está disponível para download no [Centro de download da Microsoft][visio-download]. Este diagrama é em "computação - página de região (Windows) multi.

[! [0]][0]

- **Regiões primárias e secundários**. Essa arquitetura usa duas regiões para obter maior disponibilidade. Uma é a região principal. Durante operações normais, o tráfego de rede é roteado para a região principal. Mas se que ficar indisponível, o tráfego é roteado para a região secundária.

- ** [Gerenciador de tráfego do azure] [ traffic-manager] ** roteia solicitações de entrada para a região principal. Se região ficar indisponível, o Gerenciador de tráfego não sobre a região secundário. Para obter mais informações, consulte a seção [Configurando Gerenciador de tráfego](#configuring-traffic-manager).

- **Grupos de recursos**. Criar [grupos de recursos] de separados[ resource groups] para a região principal, a região secundária e para o Gerenciador de tráfego. Isso lhe dá flexibilidade para gerenciar cada região como um único conjunto de recursos. Por exemplo, você poderia reimplantar uma região, sem interromper outro. [Vincular os grupos de recursos][resource-group-links], de modo que você pode executar uma consulta para listar todos os recursos para o aplicativo.

- **VNets**. Crie um VNet separada para cada região. Verifique se que os espaços de endereço não se sobreponham. 

- **SQL Server sempre no grupo de disponibilidade**. Se você estiver usando o SQL Server, recomendamos que [Sempre em Availabilty grupos SQL] [ sql-always-on] para alta disponibilidade. Crie um grupo de disponibilidade único que inclui as instâncias do SQL Server em ambas as regiões. Para obter mais informações, consulte a seção [Configurando o grupo de disponibilidade do SQL Server sempre ligado](#configuring-the-sql-server-alwayson-availability-group ).

> [AZURE.NOTE] Considere também [Azure SQL Database][azure-sql-db], que fornece um banco de dados relacional como um serviço de nuvem. Com o banco de dados SQL, não é necessário configurar um grupo de disponibilidade ou gerenciar o failover.  

- **Gateways VPN**: criar um [gateway VPN] [ vpn-gateway] em cada VNet e configurar uma [conexão de VNet para VNet][vnet-to-vnet], para habilitar o tráfego de rede entre os dois VNets. Isso é necessário para o grupo de disponibilidade SQL sempre ligado.

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

### <a name="sql-server-always-on-configuration"></a>SQL Server sempre na configuração

SQL Server sempre em grupos de disponibilidade requerem um controlador de domínio. Todos os nós no grupo de disponibilidade devem estar no mesmo domínio AD. Os seguintes pontos oferecem orientação sobre como configurar um grupo de disponibilidade do SQL Server sempre em:

- No mínimo, coloque dois controladores de domínio em cada região. 

- Dê um endereço IP estático de cada controlador de domínio.

- Crie uma conexão de VNet para VNet para permitir a comunicação entre o VNets.

- Para cada VNet, adicione os endereços IP dos controladores de domínio (de ambas as regiões) à lista de servidor DNS. Você pode usar o seguinte comando CLI. Obter mais informações, consulte [Gerenciar DNS servers usados por uma rede virtual (VNet)][vnet-dns].

```bat
azure network vnet set --resource-group dc01-rg --name dc01-vnet --dns-servers "10.0.0.4,10.0.0.6,172.16.0.4,172.16.0.6"
```

- Criar um [Cluster de Failover do Windows Server] [ wsfc] cluster (WSFC) que inclui as instâncias do SQL Server em ambas as regiões. 

- Crie um SQL Server sempre na disponibilidade grupo que inclui as instâncias do SQL Server nas regiões primárias e secundárias. Consulte [Estendendo sempre no grupo de disponibilidade ao data center remoto Azure (PowerShell)](https://blogs.msdn.microsoft.com/sqlcat/2014/09/22/extending-alwayson-availability-group-to-remote-azure-datacenter-powershell/) para ver as etapas. 

- Coloque a réplica principal na região primária.

- Coloque uma ou mais réplicas secundárias na região primária. Configurá-los para usar confirmação síncrona com failover automático.

- Coloque uma ou mais réplicas secundárias na região secundário. Configurá-los para usar confirmação *assíncrona* , por razões de desempenho. (Caso contrário, todas as transações de SQL necessário aguardar em uma viagem pela rede à região secundário). 

> [AZURE.NOTE] Réplicas de confirmação assíncrona não suportam failover automático. 

Para obter mais informações, consulte [Executando o Windows VMs para uma arquitetura de N camadas no Azure](guidance-compute-n-tier-vm.md#SQL-AlwaysOn-Availability-Group).

## <a name="availability-considerations"></a>Considerações de disponibilidade

Com um aplicativo de N camadas complexo, você não talvez precise replicar todo o aplicativo na região secundário. Em vez disso, você pode replicar apenas um subsistema crítico que é necessário para oferecer suporte à continuidade de negócios.

Gerenciador de tráfego é um ponto de possível falha do sistema. Se o serviço falhar, os clientes não podem acessar seu aplicativo durante o tempo de inatividade. Examine o [Gerenciador de tráfego SLA][tm-sla]e determine se usando o Gerenciador de tráfego sozinho atende às suas necessidades de negócios para alta disponibilidade. Caso contrário, considere adicionar outra solução de gerenciamento de tráfego como um failback. Se o serviço Gerenciador de tráfego do Azure falhar, altere os registros CNAME no DNS para apontar para o outro serviço de gerenciamento de tráfego. (Esta etapa deve ser executada manualmente e seu aplicativo ficarão indisponível até que as alterações DNS são propagadas.) 

Para o cluster do SQL Server, há dois cenários de failover a considerar:

1. Todas as réplicas SQL na região primária falharem. Por exemplo, isso pode acontecer durante uma interrupção regional. Nesse caso, você manualmente deve falhar sobre o grupo de disponibilidade do SQL, embora o passam do Gerenciador de tráfego automaticamente no front-end. Siga as etapas em [realizar um Failover de Manual forçado de um grupo de disponibilidade do SQL Server](https://msdn.microsoft.com/library/ff877957.aspx), que descreve como realizar um failover forçado usando o SQL Server Management Studio, Transact-SQL ou PowerShell no SQL Server 2016. 

    > [AZURE.WARNING] Com failover forçada, há um risco de perda de dados. Depois que a região principal estiver online novamente, tirar um instantâneo do banco de dados e usar [tablediff] para encontrar as diferenças.

2. Gerenciador de tráfego falha sobre a região secundário, mas a réplica SQL principal ainda está disponível. Por exemplo, a camada de front-end pode falhar, sem afetar as VMs de SQL. Nesse caso, o tráfego de Internet é roteado para a região secundária e região ainda pode se conectar à réplica SQL principal. No entanto, haverá maior latência, porque as conexões de SQL estão progredindo em regiões. Nessa situação, você deve executar um failover manual da seguinte maneira: 

    - Alterne temporariamente uma réplica SQL na região secundário para confirmação *síncrono* . Isso garante que não haverá perda de dados durante o failover.

    - Alternar para réplica SQL. 
    
    - Quando você não volta à região primária, restaure a configuração de confirmação assíncrona. 

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
[azure-sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[hybrid-vpn]: guidance-hybrid-network-vpn.md
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../azure-resource-manager/resource-group-overview.md
[resource-group-links]: ../resource-group-link-resources.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[sql-always-on]: https://msdn.microsoft.com/en-us/library/hh510230.aspx
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
[0]: ./media/blueprints/compute-multi-dc.png "Arquitetura de rede altamente disponível para aplicativos de fileiras Azure"