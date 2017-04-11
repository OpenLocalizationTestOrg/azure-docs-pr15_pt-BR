<properties
   pageTitle="Aplicativo Web com alta disponibilidade | Arquitetura de referência Azure | Microsoft Azure"
   description="Arquitetura recomendada para aplicativo web com alta disponibilidade, executando no Microsoft Azure."
   services="app-service,app-service\web,sql-database" 
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
   ms.date="06/27/2016"
   ms.author="mwasson"/>

# <a name="azure-reference-architecture-web-application-with-high-availability"></a>Arquitetura de referência Azure: aplicativo Web com alta disponibilidade

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artigo mostra uma arquitetura recomendada para um aplicativo web com alta disponibilidade, em execução no Microsoft Azure. A arquitetura baseia-se nos [arquitetura de referência Azure: melhorando escalabilidade em um aplicativo web][guidance-web-apps-scalability].

## <a name="architecture-diagram"></a>Diagrama de arquitetura

![Arquitetura de referência: aplicativo Web com alta disponibilidade](media/blueprints/paas-web-app-multi-region.png)

Essa arquitetura baseia mostrada na [escalabilidade melhorando em um aplicativo web][guidance-web-apps-scalability]. As principais diferenças são:

- **Regiões primárias e secundários**. Essa arquitetura usa duas regiões para obter maior disponibilidade. O aplicativo é implantado em cada regiões. Durante operações normais, o tráfego de rede é roteado para a região principal. Mas se que ficar indisponível, o tráfego é roteado para a região secundária. Para obter detalhes sobre failover, consulte [Gerenciando failover](#managing-failover-and-failback).

- **Gerenciador de tráfego azure**. [Gerenciador de tráfego] [ traffic-manager] roteia solicitações de entrada para a região principal. Se o aplicativo em execução região ficar indisponível, o Gerenciador de tráfego não sobre a região secundário. 

- **Localização geográfica replicação** de banco de dados SQL e DocumentDB.

## <a name="recommendations"></a>Recomendações

### <a name="regional-pairing"></a>Emparelhamento regionais

Cada região Azure é combinado com outra região dentro a mesma Geografia. Em geral, escolha regiões do mesmo par regional (por exemplo, Leste dos EUA 2 e centro dos EUA). Vantagens de fazê-lo:

- Se houver uma queda de ampla, é priorizamos a recuperação de pelo menos uma região fora cada par.
- Atualizações de sistema Azure planejadas são implementadas em pares regiões sequencialmente, para minimizar o tempo de inatividade possível.
- Na maioria dos casos, pares residem na mesma geografia, para atender aos requisitos de residência de dados.

No entanto, certifique-se de que as duas regiões oferece suporte para todos os serviços Azure necessários para seu aplicativo. Consulte [serviços por região][services-by-region]. Para obter mais informações sobre pares regionais, consulte [desastre e continuidade de negócios recuperação (BCDR): Azure par regiões][regional-pairs].

### <a name="resource-groups"></a>Grupos de recursos

Considere a possibilidade de colocar a região principal, secundário região e Gerenciador de tráfego em separados [grupos de recursos][resource groups]. Isso permite que você gerencie os recursos implantados em cada região como uma única coleção &mdash; você pode implantá-los separadamente, exclua a implantação e assim por diante. 

### <a name="traffic-manager"></a>Gerenciador de tráfego

**Roteamento.** Gerenciador de tráfego oferece suporte a vários [algoritmos de roteamento][tm-routing]. Para o cenário descrito neste artigo, use o roteamento de _prioridade_ (anteriormente chamado de roteamento de _failover_ ). Com essa configuração, o Gerenciador de tráfego envia todas as solicitações para região principal, a menos que o ponto de extremidade para aquela região fica inacessível. Neste ponto, ele passam automaticamente para a região secundária. Método de [Roteamento]de configurar Failover[tm-configure-failover].

**Teste de integridade.** Gerenciador de tráfego usa um teste HTTP (ou HTTPS) para monitorar a disponibilidade de cada ponto de extremidade. O teste dá Gerenciador de tráfego de um teste de aprovado/reprovado para falha sobre a região secundário. Ele funciona enviando uma solicitação para um caminho de URL especificado. Se ele recebe uma resposta não-200 dentro de um período de tempo limite, o teste falhará. Após quatro solicitações falhou, Gerenciador de tráfego marca o ponto de extremidade como degradado e não conseguir sobre outro ponto de extremidade. Para obter detalhes, consulte [Gerenciador de tráfego de ponto de extremidade monitoração e failover][tm-monitoring].

Como prática recomendada, crie um ponto de extremidade de teste de integridade que relata a integridade geral do aplicativo e usar este ponto de extremidade para o teste de integridade. O ponto de extremidade deve verificar dependências críticas como os aplicativos de serviço de aplicativo, fila do armazenamento e banco de dados SQL. Caso contrário, o teste pode relatar um ponto de extremidade "Íntegro" quando partes essenciais do aplicativo realmente estão falhando. 

Por outro lado, não use o teste de integridade para verificar os serviços de prioridade mais baixos. Por exemplo, se um serviço de email falhar, o aplicativo pode mudar para um provedor de segundo ou apenas enviar emails mais tarde. O aplicativo provavelmente não deve falhar sobre nessa situação. Para obter mais informações, consulte [Padrão de monitoramento de ponto de extremidade de integridade][health-endpoint-monitoring-pattern].
  
### <a name="sql-database"></a>Banco de dados SQL

Usar a [Replicação de localização geográfica Active] [ sql-replication] para criar um secundário legível em uma região diferente. Você pode ter até quatro secundários legíveis. Se seu banco de dados primário falhar, ou simplesmente precisa ser colocado offline, você pode failover para qualquer um dos seus bancos de dados secundários. Replicação de localização geográfica ativa pode ser configurada para qualquer banco de dados em qualquer pool elástica banco de dados.

### <a name="documentdb"></a>DocumentDB

DocumentDB oferece suporte a localização geográfica replicação em regiões. Uma região está designada como gravável e os outros são réplicas somente leitura. 

Se houver uma interrupção regional, você pode falhar com selecionando outra região para ser a região de gravação. O cliente de DocumentDB SDK envia automaticamente solicitações de gravação para a região atual de gravação, então você não precisa atualizar a configuração de cliente após um failover. Para obter mais informações, consulte [dados de distribuir globalmente com DocumentDB][docdb-geo]. 

> [AZURE.NOTE] Todas as réplicas pertencem ao mesmo grupo de recursos.

### <a name="storage"></a>Armazenamento

Para o armazenamento do Azure, usar o [armazenamento de localização geográfica redundantes de acesso de leitura] [ ra-grs] (ar-GRS). Com o armazenamento de ar GRS, os dados são replicados para uma região secundária. Você tem acesso somente leitura aos dados na região secundário, por meio de um ponto de extremidade separado. Se houver uma interrupção regional ou um desastre, a equipe de armazenamento do Azure pode determinar realizar um failover de localização geográfica à região secundário. Não há nenhuma ação de cliente necessária para esse failover.

Para armazenamento de fila, crie uma fila backup na região secundário. Durante o failover, o aplicativo pode usar a fila de backup, até que a região principal fique disponível novamente. Dessa maneira, o aplicativo ainda pode processar novas solicitações. 

## <a name="availability-considerations"></a>Considerações de disponibilidade

Uma arquitetura de várias regiões pode fornecer maior disponibilidade que implantando em uma única região. Se uma interrupção regional afeta a região principal, você poderá falhar sobre a região secundário. Essa arquitetura também pode ajudar se um subsistema individual do aplicativo falhar.  
     
Há várias abordagens gerais para alcançar alta disponibilidade em data centers:      
- Ativo/passivo com o modo de espera ativo. Tráfego vai para uma região, enquanto o outro aguarda em espera. O aplicativo é implantado e em execução na região secundário. Você pode começar com uma contagem de instância menor no Centro de dados secundária e, em seguida, dimensionar conforme necessário. 

- Ativo/passivo com fria espera. O mesmo, mas aplicativo não é implantado até que seja necessário para failover. Essa abordagem custa menos para executar, mas geralmente terá mais tempo durante uma falha de inatividade. 

- Ativo/ativo. Ambas as regiões estão ativas e as solicitações são carga equilibrada entre elas. Se um data center ficar indisponível, ela é considerada fora rotação. 

Este artigo aborda ativo/passivo com o modo de espera ativo, usando o [Gerenciador de tráfego do Azure] [ traffic-manager] para rotear o tráfego para a região. 

### <a name="traffic-manager"></a>Gerenciador de tráfego

Gerenciador de tráfego passam automaticamente se a região principal ficar indisponível. Quando o Gerenciador de tráfego Falha ao longo, há um período de tempo, quando os clientes não podem acessar o aplicativo, que pode ser alguns minutos. Dois fatores afetam a duração total:

- O teste de integridade deve detectar que o data center principal tornou inacessível.

- Servidores DNS devem atualizar os registros DNS em cache para o endereço IP, que depende do DNS time to live (TTL). O TTL padrão é 300 segundos (5 minutos), mas você pode configurar esse valor quando você cria o perfil do Gerenciador de tráfego.

Para obter detalhes, consulte [Sobre o Gerenciador de tráfego monitoramento][tm-monitoring]. 

Gerenciador de tráfego é um ponto de possível falha do sistema. Se o serviço falhar, os clientes não podem acessar seu aplicativo durante o tempo de inatividade. Examine o [Gerenciador de tráfego SLA][tm-sla]e determine se usando o Gerenciador de tráfego sozinho atende às suas necessidades de negócios para alta disponibilidade. Caso contrário, considere adicionar outra solução de gerenciamento de tráfego como um failback. Se o serviço Gerenciador de tráfego do Azure falhar, altere os registros CNAME no DNS para apontar para o outro serviço de gerenciamento de tráfego. (Esta etapa deve ser executada manualmente e seu aplicativo ficarão indisponível até que as alterações DNS são propagadas.) 

### <a name="sql-database"></a>Banco de dados SQL

O objetivo de ponto de recuperação (RPO) e o tempo de recuperação estimado (ERTER) para o banco de dados SQL são descritos [aqui][sql-rpo]. 

### <a name="storage"></a>Armazenamento

Armazenamento de ar GRS fornece armazenamento durável, mas é importante entender o que pode ocorrer durante uma interrupção: 

- Se ocorrer uma falha de armazenamento, haverá um período de tempo quando você não tem acesso de gravação para os dados. Você ainda pode ler do ponto de extremidade secundário durante a interrupção.

- Se um desastre ou interrupção regional afeta o local principal e os dados não poderão ser recuperados, a equipe de armazenamento do Azure pode determinar realizar um failover de localização geográfica à região secundário. 

- Replicação de dados para a região secundária é executada assíncrona. Portanto, se um failover geográfica for realizado, perda de dados é possível, se os dados não poderão ser recuperados da região primária.

- Falhas temporárias, como uma interrupção de rede, não irá disparar um failover de armazenamento. Crie seu aplicativo seja flexível a falhas temporárias. Reduções possíveis:

    - Ler o secundário.

    - Alterne temporariamente a outra conta de armazenamento para novas operações de gravação (por exemplo, para mensagens de fila). 

    - Copie dados de secundário para outra conta de armazenamento.

    - Fornece funcionalidade reduzida até que o sistema falhar novamente.

Para obter mais informações, consulte [o que fazer se ocorrer uma falha de armazenamento do Azure][storage-outage].

## <a name="managing-failover-and-failback"></a>Gerenciando failover e failback

### <a name="traffic-manager"></a>Gerenciador de tráfego

Gerenciador de tráfego passam automaticamente se a região principal ficar indisponível. Por padrão, ele também automaticamente falhará, depois que a região principal fica disponível novamente.

No entanto, é recomendável realizar failback manual, em vez de automaticamente falhando novamente. Antes de falhar novamente, verifique se todos os subsistemas de aplicativo íntegros. Caso contrário, você pode criar uma situação onde o aplicativo inverte trocadas entre data centers. 

Para impedir que o failback automático, diminua manualmente a prioridade da região primária após um evento de failover. Por exemplo, suponha que a região primária é prioridade 1 e o secundário é prioridade 2. Após um failover, defina a região primária como prioridade 3, para impedir que o failback automático. Quando você estiver pronto para voltar, redefina a prioridade para 1.

Os seguintes comandos atualizam a prioridade.

**PowerShell** 

```bat
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name <endpoint> -ProfileName <profile> -ResourceGroupName <resource-group> -Type AzureEndpoints
$endpoint.Priority = 3
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

Para obter mais informações, consulte [Cmdlets do Gerenciador de tráfego Azure][tm-ps].

**CLI Azure**

```bat
azure network traffic-manager endpoint set --name <endpoint> --profile-name <profile> --resource-group <resource-group> --type AzureEndpoints --priority 3
```    

### <a name="sql-database"></a>Banco de dados SQL

Se o banco de dados primário falhar, execute um failover manual ao banco de dados secundário. Consulte [restaurar um banco de dados do SQL Azure ou failover para um secundário][sql-failover]. Até que você não sobre, o banco de dados secundário permanece somente leitura. 


<!-- links -->

[azure-sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[docdb-geo]: ../documentdb/documentdb-distribute-data-globally.md
[guidance-web-apps-scalability]: guidance-web-apps-scalability.md
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[ra-grs]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../resource-group-overview.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[sql-failover]: ../sql-database/sql-database-disaster-recovery.md
[sql-replication]: ../sql-database/sql-database-geo-replication-overview.md
[sql-rpo]: ../sql-database/sql-database-business-continuity.md#sql-database-business-continuity-features
[storage-outage]: ../storage/storage-disaster-recovery-guidance.md
[tm-configure-failover]: ../traffic-manager/traffic-manager-configure-failover-routing-method.md
[tm-monitoring]: ../traffic-manager/traffic-manager-monitoring.md
[tm-ps]: https://msdn.microsoft.com/en-us/library/mt125941.aspx
[tm-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[tm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/en-us/services/traffic-manager/
