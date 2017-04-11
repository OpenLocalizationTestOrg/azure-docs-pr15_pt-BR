<properties
   pageTitle="Monitoramento de integridade no serviço tecidos | Microsoft Azure"
   description="Uma introdução ao modelo, que fornece monitoramento de cluster e seus aplicativos e serviços de monitoramento de integridade de estrutura de serviço do Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="introduction-to-service-fabric-health-monitoring"></a>Introdução ao monitoramento de integridade do serviço tecidos
Azure tecidos de serviço apresenta um modelo de integridade que fornece a avaliação de integridade de rich, flexível e extensível e relatórios. O modelo permite monitoramento quase em tempo real do estado do cluster e os serviços em execução. Você pode facilmente obter informações sobre a saúde e corrigir possíveis problemas antes que eles em cascata e causam interrupções de massa. No modelo típico, serviços enviar relatórios baseados em seus modos de exibição locais e que informações são agregadas para fornecer geral cluster nível de modo de exibição.

Componentes de serviço tecidos usam esse modelo de integridade avançada para relatar seu estado atual. Você pode usar o mesmo mecanismo para integridade de relatório dos seus aplicativos. Se você investir em relatórios de integridade de alta qualidade que captura suas condições personalizadas, você pode detectar e corrigir problemas do aplicativo em execução muito mais facilmente.

> [AZURE.NOTE] Podemos iniciado o subsistema de saúde para atender a uma necessidade para monitoradas atualizações. Serviço tecidos fornece monitoradas atualizações de aplicativo e cluster que garantir disponibilidade total, sem tempo de inatividade e mínima sem intervenção do usuário. Para atingir esses objetivos, a atualização verifica integridade com base em configurado políticas de atualização e permite uma atualização continuar somente quando integridade respeita limites desejados. Caso contrário, a atualização é automaticamente revertida ou pausada para dar uma chance de corrigir os problemas de administradores. Para saber mais sobre atualizações de aplicativo, consulte [Este artigo](service-fabric-application-upgrade.md).

## <a name="health-store"></a>Repositório de integridade
O armazenamento de integridade mantém informações relacionadas a saúde sobre entidades no cluster para fácil recuperação e avaliação. Ele é implementado como uma estrutura de serviço persistentes serviço estado para garantir alta disponibilidade e escalabilidade. O armazenamento de integridade é parte do **tecidos: / sistema** aplicativo e ele está disponível quando o cluster estiver ativo e em execução.

## <a name="health-entities-and-hierarchy"></a>Hierarquia e entidades de integridade
As entidades de integridade são organizadas em uma hierarquia lógica que captura interações e as dependências entre diferentes entidades. As entidades e hierarquia são criados automaticamente pela loja integridade com base nos relatórios recebidos de componentes de serviço tecidos.

As entidades de integridade espelham as entidades tecidos de serviço. (Por exemplo, **entidade de aplicativo de integridade** corresponde a uma instância de aplicativo implantada em cluster, enquanto **entidade de nó de integridade** corresponde a um nó de cluster tecidos de serviço) A hierarquia de integridade captura as interações das entidades sistema e é a base para avaliação de integridade avançadas. Você pode aprender sobre conceitos chave do serviço tecidos na [Visão geral técnica do serviço tecidos](service-fabric-technical-overview.md). Para obter mais informações sobre o aplicativo, consulte [modelo de aplicativo de serviço tecidos](service-fabric-application-model.md).

As entidades de saúde e hierarquia permitir que o cluster e os aplicativos para ser informado, depurado e monitorado efetivamente. O modelo de integridade fornece uma representação precisa, *granular* da integridade de muitas peças movendo no cluster.

![Entidades de integridade.][1]
As entidades de integridade, organizados em uma hierarquia baseada em relações pai-filho.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

As entidades de integridade são:

- **Cluster**. Representa a integridade de um cluster de estrutura de serviço. Relatórios de integridade do cluster descrevem as condições que afetam todo o cluster e não podem ser restringidas para uma ou mais filhos não íntegros. Alguns exemplos incluem o cérebro do cluster dividindo devido a problemas de comunicação ou partição de rede.

- **Nó**. Representa a integridade de um nó de serviço tecidos. Relatórios de integridade de nó descrevem as condições que afetam a funcionalidade de nó. Eles geralmente afetam todas as entidades implantadas em execução. Alguns exemplos incluem quando um nó estiver fora do espaço de disco (ou outra propriedade de máquina, como memória, conexões) e quando um nó está inoperante. A entidade de nó é identificada pelo nome nó (cadeia).

- **Aplicativo**. Representa a integridade de uma instância do aplicativo em execução no cluster. Relatórios de integridade de aplicativo descrevem as condições que afetam a integridade geral do aplicativo. Eles não podem ser restringidos para filhos individuais (serviços ou aplicativos implantados). Exemplos incluem a interação de ponta a ponta entre diferentes serviços no aplicativo. A entidade de aplicativo é identificada pelo nome do aplicativo (URI).

- **Serviço**. Representa a integridade de um serviço em execução no cluster. Relatórios de integridade do serviço descrevem as condições que afetam a integridade geral do serviço, e eles não podem ser restringidos para uma partição ou uma réplica. Alguns exemplos incluem uma configuração de serviço (como porta ou compartilhamento de arquivo externo) que está causando problemas para todas as partições. A entidade de serviço é identificada por nome do serviço (URI).

- **Partição**. Representa a integridade de uma partição de serviço. Relatórios de integridade de partição descrevem as condições que afetam o conjunto de réplica inteira. Exemplos incluem quando o número de réplicas está abaixo contagem de destino e quando uma partição for em perda de quorum. A entidade de partição é identificada pela partição ID (GUID).

- **Réplica**. Representa a integridade de uma réplica de serviço com estado ou uma instância de serviço sem estado. Os componentes de sistema e a menor unidade que watchdogs podem relatar para um aplicativo. Para serviços de estado, alguns exemplos incluem uma réplica primária relatório quando ele não pode replicar operações secundários e quando a replicação não é prosseguir em esperados ritmo. Além disso, uma instância sem estado pode relatar quando ele está ficando sem recursos ou tem problemas de conectividade. A entidade de réplica é identificada pela partição ID (GUID) e a ID de réplica ou instância (por extenso).

- **DeployedApplication**. Representa a integridade de um *aplicativo em execução em um nó*. Relatórios de integridade de aplicativo implantado descrevem as condições específicas do aplicativo no nó que não pode ser restringido pacotes de serviço implantado no mesmo nó. Alguns exemplos incluem quando o pacote de aplicativo não pode ser baixado naquele nó e quando há uma configuração de problema para cima objetos de segurança do aplicativo no nó. Aplicativo implantado é identificado por nome de aplicativo (URI) e um nome de nó (cadeia).

- **DeployedServicePackage**. Representa a integridade de um pacote de serviço em execução em um nó no cluster. Ele descreve condições específicas a um pacote de serviço que não afetam os outros pacotes de serviço no mesmo nó para o mesmo aplicativo. Exemplos incluem um pacote de código no pacote de serviço que não pode ser iniciado e um pacote de configuração que não pode ser lido. O pacote de serviço implantado é identificado por nome do aplicativo (URI), o nome de nó (cadeia) e nome de manifesto de serviço (cadeia).

O detalhamento do modelo de integridade torna mais fácil detectar e corrigir problemas. Por exemplo, se um serviço não está respondendo, é possível que a instância do aplicativo está com problemas de relatório. Relatório em que nível não é ideal, no entanto, porque o problema pode não estar afetando todos os serviços dentro desse aplicativo. O relatório deve ser aplicado para o serviço não íntegro ou uma partição filho específicos, se mais informações aponta para partição. Os dados automaticamente emerge as por meio da hierarquia e uma partição não íntegra é tornado visível nos níveis de serviço e de aplicativos. Esta agregação ajuda a identificar e resolver a causa do problema mais rapidamente.

A hierarquia de integridade é composta de relações pai-filho. Um cluster é composto de nós e aplicativos. Aplicativos tem serviços e aplicativos implantados. Aplicativos implantados tem implantado pacotes de serviço. Serviços têm partições, e cada partição tem uma ou mais réplicas. Não há um relacionamento especial entre nós e entidades implantadas. Se um nó for não íntegro conforme relatado pelo seu componente de sistema de autoridade (serviço Failover Manager), ela afeta os aplicativos implantados, pacotes de serviço e réplicas implantadas nele.

A hierarquia de integridade representa o estado mais recente do sistema com base em relatórios de integridade mais recentes, que é informações quase em tempo real.
Watchdogs internas e externas podem relatar as mesmas entidades com base em lógica específica do aplicativo ou condições monitoradas personalizadas. Relatórios de usuário coexistam com os relatórios de sistema.

Planeje a investir em como relatar e responder a integridade durante o design de um serviço de nuvem grandes, para facilitar o serviço a depurar, monitorar e operar.

## <a name="health-states"></a>Estados de integridade
Estrutura de serviço usa três estados de integridade para descrever se uma entidade é eficaz, ou não: Okey, aviso e erro. Qualquer relatório enviado para o armazenamento de integridade deve especificar um desses estados. O resultado de avaliação de integridade é um desses estados.

Os possíveis [Estados de integridade](https://msdn.microsoft.com/library/azure/system.fabric.health.healthstate) são:

- **OK**. A entidade é íntegra. Não há nenhum problemas conhecidos relatados nela ou seus filhos (quando aplicável).

- **Aviso**. A entidade experiências alguns problemas, mas ainda não estiver com problemas (por exemplo, há atrasos, mas eles não causam problemas funcionais ainda). Em alguns casos, a condição de aviso pode corrigir próprio sem interação especial e é útil fornecer visibilidade sobre o que está acontecendo. Em outros casos, a condição de aviso pode prejudicar um problema grave sem intervenção do usuário.

- **Erro**. A entidade está com problemas. Ação deve ser seguida para corrigir o estado da entidade, porque ele não pode funcionar corretamente.

- **Desconhecido**. A entidade não existe na loja de integridade. Esse resultado pode ser obtido as consultas distribuídas que os resultados de vários componentes de mesclagem. Por exemplo, obter consulta de lista de nó vai para o **FailoverManager** e **HealthManager**, consulta de lista de aplicativos de get vai para **ClusterManager** e **HealthManager**. Essas consultas mesclagem os resultados de vários componentes de sistema. Se outro componente do sistema retornará uma entidade que não chegou ou foi limpo do repositório integridade, o resultado mesclado tem desconhecido estado de integridade.

## <a name="health-policies"></a>Diretivas de integridade
O armazenamento de integridade aplica diretivas de integridade para determinar se uma entidade é eficaz, com base em seus relatórios e seus filhos.

> [AZURE.NOTE] Diretivas de integridade podem ser especificadas no manifesto cluster (para avaliação de integridade de cluster e nó) ou no manifesto do aplicativo (para avaliação de aplicativo e nenhum dos seus filhos). Solicitações de avaliação de integridade também podem passar diretivas de avaliação de integridade personalizados, que são usadas apenas para essa avaliação.

Por padrão, o serviço tecidos aplica regras rígidas (tudo o que deve ser eficaz,) para a relação hierárquica pai-filho. Se mesmo um dos filhos tem um evento não íntegro, o pai é considerado não íntegro.

### <a name="cluster-health-policy"></a>Diretiva de integridade de cluster
A [política de integridade de cluster](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.aspx) é usada para avaliar o estado de integridade de cluster e estados de integridade de nó. A política pode ser definida no manifesto do cluster. Se não estiver presente, a política padrão (zero falhas tolerated) será usada.
A política de integridade de cluster contém:

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.considerwarningaserror.aspx). Especifica se tratar de integridade de aviso relatar como erros durante a avaliação de integridade. Padrão: false.

- [MaxPercentUnhealthyApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications.aspx). Especifica a porcentagem de tolerated máxima de aplicativos que podem ser não íntegra antes do cluster é considerado em erro.

- [MaxPercentUnhealthyNodes](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes.aspx). Especifica a porcentagem máxima tolerated de nós que podem ser não íntegra antes do cluster é considerado em erro. Em grandes clusters, alguns nós são sempre para baixo ou check-out para reparos, para que essa porcentagem deve ser configurada para suportar que.

- [ApplicationTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap.aspx). O mapa de política de integridade de tipo de aplicativo pode ser usado durante a avaliação de integridade de cluster para descrever tipos de aplicativo especial. Por padrão, todos os aplicativos são colocados em um pool e avaliados com MaxPercentUnhealthyApplications. Se alguns tipos de aplicativo devem ser tratados de forma diferente, eles podem ser realizados sair do pool global. Em vez disso, eles são avaliados contra as porcentagens associadas ao seu nome de tipo de aplicativo no mapa. Por exemplo, em um cluster, há milhares de aplicativos de diferentes tipos e algumas instâncias de aplicativo de controle de um tipo de aplicativo especial. Os aplicativos de controle nunca devem estar no erro. Você pode especificar MaxPercentUnhealthyApplications global para 20% para suportar algumas falhas, mas para o tipo de aplicativo "ControlApplicationType" definir o MaxPercentUnhealthyApplications como 0. Dessa forma, se alguns dos muitos aplicativos não íntegras, mas abaixo a porcentagem não íntegra global, o cluster seria avaliado aviso. Um estado de integridade de aviso não afeta a atualização de cluster ou outros monitoramento disparadas por estado de integridade de erro. Mas aplicativo de controle até mesmo um erro tornaria cluster sem integridade, que aciona reverter ou pausa a atualização de cluster, dependendo da configuração de atualização.
Para os tipos de aplicativo definidos no mapa, todas as instâncias do aplicativo são tiradas sair do pool global de aplicativos. Eles são avaliados com base no número total de aplicativos do tipo de aplicativo, usando o MaxPercentUnhealthyApplications específico do mapa. Todo o resto dos aplicativos permanecem no pool global e são avaliadas com MaxPercentUnhealthyApplications.

O exemplo a seguir é um trecho de um manifesto de cluster. Para definir as entradas no mapa de tipo de aplicativo, prefixo o nome do parâmetro com "ApplicationTypeMaxPercentUnhealthyApplications-", seguido do nome do tipo de aplicativo.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Diretiva de integridade de aplicativo
A [política de integridade de aplicativo](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.aspx) descreve como a avaliação de eventos e agregação de estados filho é feita para aplicativos e seus filhos. Ele pode ser definido no manifesto do aplicativo, **ApplicationManifest.xml**, no pacote do aplicativo. Se não há políticas forem especificadas, o serviço tecidos pressupõe que a entidade é não íntegra se ele tiver um relatório de integridade ou um filho no estado de integridade de aviso ou erro.
As diretivas configuráveis são:

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.considerwarningaserror.aspx). Especifica se tratar de integridade de aviso relatar como erros durante a avaliação de integridade. Padrão: false.

- [MaxPercentUnhealthyDeployedApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications.aspx). Especifica a porcentagem máxima de tolerated de implantação de aplicativos que podem ser não íntegra antes que o aplicativo é considerado em erro. Essa porcentagem é calculada dividindo o número de aplicativos implantados não íntegros sobre o número de nós que os aplicativos estão atualmente implantados no cluster. A computação Arredonda para suportar uma falha no pequeno número de nós. Porcentagem de padrão: zero.

- [DefaultServiceTypeHealthPolicy](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy.aspx). Especifica a serviço tipo integridade política padrão, que substitui a política de integridade de padrão para todos os tipos de serviço no aplicativo.

- [ServiceTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap.aspx). Fornece um mapa de diretivas de integridade do serviço por tipo de serviço. Essas políticas substituem as diretivas de integridade do tipo de serviço padrão para cada tipo de serviço especificado. Por exemplo, se um aplicativo com um tipo de serviço de gateway sem estado e um tipo de serviço do mecanismo de estado, você pode configurar as políticas de integridade para sua avaliação diferente. Quando você especificar política por tipo de serviço, você pode obter um controle mais granular da integridade do serviço.

### <a name="service-type-health-policy"></a>Diretiva de integridade de tipo de serviço
A [política de integridade de tipo de serviço](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.aspx) Especifica como avaliar e agregar os serviços e os filhos dos serviços. A política contém:

- [MaxPercentUnhealthyPartitionsPerService](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice.aspx). Especifica a porcentagem máxima tolerated das partições não íntegras antes de um serviço é considerado não íntegro. Porcentagem de padrão: zero.

- [MaxPercentUnhealthyReplicasPerPartition](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition.aspx). Especifica a porcentagem máxima tolerated de réplicas não íntegras antes de uma partição é considerada não íntegra. Porcentagem de padrão: zero.

- [MaxPercentUnhealthyServices](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices.aspx). Especifica a porcentagem máxima tolerated dos serviços não íntegras antes que o aplicativo é considerado não íntegro. Porcentagem de padrão: zero.

O exemplo a seguir é um trecho de um manifesto de aplicativo:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Avaliação de integridade
Usuários e serviços automatizados podem avaliar integridade para qualquer entidade a qualquer momento. Para avaliar a integridade da entidade, as agregações de repositório de integridade integridade todos os relatórios na entidade e avalia todos os seus filhos (quando aplicável). O algoritmo de agregação de integridade usa diretivas de integridade que especificam como avaliar relatórios de integridade e como agregar estados de integridade filho (quando aplicável).

### <a name="health-report-aggregation"></a>Agregação de relatório de integridade
Uma entidade pode ter vários relatórios de integridade enviados por diferentes recursos (componentes do sistema ou watchdogs) em propriedades diferentes. A agregação usa as diretivas de integridade associado, em particular o membro ConsiderWarningAsError da política de integridade de aplicativo ou cluster. ConsiderWarningAsError Especifica como avaliar avisos.

O estado de integridade agregado é disparado pelos relatórios de integridade *pior* na entidade. Se houver relatório de integridade de pelo menos um erro, o estado de integridade agregado é um erro.

![Agregação de relatório de integridade com o relatório de erros.][2]

Uma entidade de saúde que tem um ou mais relatórios de integridade de erro é avaliada como erro. O mesmo é verdadeiro para um relatório de integridade expirado, independentemente de seu estado de integridade.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Se houver nenhum relatórios de erros e avisos de um ou mais, o estado de integridade agregado é aviso ou erro, dependendo do sinalizador de política de ConsiderWarningAsError.

![Agregação de relatório de integridade com o relatório de aviso e ConsiderWarningAsError falso.][3]

Agregação de relatório de integridade com o relatório de aviso e ConsiderWarningAsError definido como false (padrão).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agregação de integridade de filho
O estado de integridade agregados de uma entidade reflete os estados de integridade filho (quando aplicável). O algoritmo para agregar estados de integridade de filho usa as diretivas de integridade aplicáveis com base no tipo de entidade.

![Agregação de integridade de entidades filho.][4]

Agregação filho com base em políticas de integridade.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Depois que o repositório de integridade avaliadas todos os filhos, ele agrega seus estados de integridade com base na porcentagem máxima configurada de filhos não íntegras. Esse percentual é obtida a política com base no tipo de entidade e filho.

- Se todos os filhos têm estados Okey, o estado de integridade de filhos agregados é Okey.

- Se seus filhos tem Okey e estados de aviso, o estado de integridade de filhos agregados é aviso.

- Se houver filhos com estados de erro que não respeitam o máximo permitido porcentagem de filhos não íntegras, o estado de integridade agregado é um erro.

- Se os filhos com estados de erro respeitam a porcentagem máxima permitida de filhos não íntegras, o estado de integridade agregado é aviso.

## <a name="health-reporting"></a>Relatórios de integridade
Componentes do sistema, aplicativos de estrutura de sistema e watchdogs interna/externa podem relatar contra entidades tecidos de serviço. Os recursos fazer decisões *local* da integridade de entidades monitoradas, com base nas condições monitoramento. Eles não precisam examinar qualquer estado global ou agregar dados. O comportamento desejado é ter recursos simples e complexos não organismos que precisam examinar muitas coisas inferir quais informações enviar.

Para enviar dados de integridade para o repositório de integridade, um reporter precisa identificar a entidade afetada e criar um relatório de integridade. O relatório, em seguida, pode ser enviado por meio da API usando [FabricClient.HealthClient.ReportHealth](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient_members.aspx), através do PowerShell ou restante.

### <a name="health-reports"></a>Relatórios de integridade
Os [relatórios de integridade](https://msdn.microsoft.com/library/azure/system.fabric.health.healthreport.aspx) de cada uma das entidades no cluster contêm as seguintes informações:

- **SourceId**. Uma cadeia de caracteres que identifica exclusivamente o gerador de relatórios do evento integridade.

- **Identificador de entidade**. Identifica a entidade onde o relatório é aplicado. Ele varia de acordo com a [entidade digite](service-fabric-health-introduction.md#health-entities-and-hierarchy):

  - Cluster. Nenhuma.

  - Nó. Nome de nó (cadeia).

  - Aplicativo. Nome do aplicativo (URI). Representa o nome da instância do aplicativo implantado em cluster.

  - Serviço. Nome do serviço (URI). Representa o nome da instância do serviço implantado em cluster.

  - Partição. ID de partição (GUID). Representa o identificador exclusivo partição.

  - Réplica. A ID de réplica do serviço com estado ou a ID de instância de serviço sem estado (INT64).

  - DeployedApplication. Nome do aplicativo (URI) e o nome de nó (cadeia).

  - DeployedServicePackage. Nome do aplicativo (URI), o nome de nó (cadeia) e serviço manifestam nome (cadeia).

- **Propriedade**. Uma *cadeia de caracteres* (não uma enumeração fixa) que permite que o gerador de relatórios categorizar o evento de integridade de uma propriedade específica da entidade. Por exemplo, reporter r pode relatar que a integridade da propriedade Node01 "armazenamento" e reporter B pode relatar a integridade da propriedade Node01 "conectividade". Na loja de integridade, esses relatórios são tratados como eventos de integridade separada para a entidade de Node01.

- **Descrição**. Uma cadeia de caracteres que permite uma reporter fornecer informações detalhadas sobre o evento de integridade. **SourceId**, **propriedade**e **HealthState** totalmente devem descrever o relatório. A descrição adiciona legível informações sobre o relatório. O texto torna mais fácil para administradores e usuários entendam o relatório de integridade.

- **HealthState**. Uma [enumeração](service-fabric-health-introduction.md#health-states) que descreve o estado de integridade do relatório. Os valores aceitos são Okey, aviso e erro.

- **TimeToLive**. Um período de tempo que indica quanto tempo o relatório de integridade é válido. Juntamente com **RemoveWhenExpired**, ele informa o repositório de integridade como avaliar eventos expirados. Por padrão, o valor é infinito e o relatório é válido indefinidamente.

- **RemoveWhenExpired**. Um booliano. Se definido como true, o relatório de integridade expirado é automaticamente removido do armazenamento de saúde e o relatório não afeta a avaliação de integridade de entidade. Usado quando o relatório é válido para um período de tempo especificado, e o gerador de relatórios não precise explicitamente, limpe-o. Ele também é usado para excluir relatórios do armazenamento integridade (por exemplo, uma monitoração é alterada e interrompe o envio de relatórios com fonte anterior e propriedade). Ele pode enviar um relatório com uma breve TimeToLive juntamente com RemoveWhenExpired para limpar qualquer estado anterior do armazenamento de integridade. Se o valor for definido como false, o relatório expirado será tratado como um erro da avaliação de integridade. O valor false sinaliza para o repositório de integridade que a fonte deve relatar periodicamente nessa propriedade. Se não estiver, então deve haver algo errado com a monitoração. Integridade do monitoração é capturada considerando o evento como um erro.

- **SequenceNumber**. Um inteiro positivo que precisa ser crescentes, ele representa a ordem dos relatórios. Ele é usado pela loja integridade detectar obsoletos relatórios que são recebidos com atraso devido a atrasos na rede ou outros problemas. Um relatório será rejeitado se o número de sequência é que menor ou igual ao mais recentemente aplicada número da mesma entidade, a fonte e a propriedade. Se não for especificada, o número de sequência é gerado automaticamente. É necessário colocar o número de sequência somente quando relatórios sobre transições de estado. Nessa situação, a fonte deve lembrar quais relatórios-enviado e manter as informações de recuperação em failover.

Este quatro partes de informações – SourceId, identificador de entidade, propriedade e HealthState – são necessários para cada relatório de integridade. O SourceId cadeia não é permitida a iniciar com o prefixo "**sistema.**", que é reservado para o sistema de relatórios. Para a mesma entidade, há apenas um relatório para a mesma fonte e a propriedade. Vários relatórios para a mesma fonte e propriedade substituam uns aos outros, no lado do cliente integridade (se eles forem colocados em lote) ou sobre a integridade do lado da loja. A substituição baseada em números de sequência; relatórios mais recentes (com números maiores de sequência) substituem os relatórios mais antigos.

### <a name="health-events"></a>Eventos de integridade
Internamente, o armazenamento de integridade mantém [eventos de integridade](https://msdn.microsoft.com/library/azure/system.fabric.health.healthevent.aspx), que contêm todas as informações de relatórios e metadados adicionais. Os metadados incluem a hora em que o relatório foi fornecido para o cliente de integridade e a hora em que ele foi modificado no lado do servidor. Os eventos de integridade são retornados por [consultas de integridade](service-fabric-view-entities-aggregated-health.md#health-queries).

Os metadados adicionados contém:

- **SourceUtcTimestamp**. A hora no relatório foi fornecido para o cliente de integridade (Universal Coordenado).

- **LastModifiedUtcTimestamp**. A hora em que o relatório da última modificação no lado do servidor (Universal Coordenado).

- **IsExpired**. Um sinalizador para indicar se o relatório expirou quando a consulta foi executada pela loja integridade. Um evento pode ser expirado somente se RemoveWhenExpired for falsa. Caso contrário, o evento não é retornado pela consulta e é removido do repositório.

- **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. A última vez para transições de erro/de aviso de Okey. Esses campos dê o histórico da integridade transições de estado para o evento.

Os campos de transição de estado podem ser usados para alertas de forma mais inteligentes ou informações de eventos de integridade "históricos". Eles habilitarem cenários, tais como:

- Alerta quando uma propriedade foi em aviso/erro para mais de X minutos. A condição para um período de tempo de verificação evita alertas em condições temporárias. Por exemplo, um alerta se o estado de integridade tiver sido aviso para mais de cinco minutos pode ser traduzido em (HealthState = = aviso e agora - LastWarningTransitionTime > 5 minutos).

- Alerta apenas em condições que ocorreram nos últimos X minutos. Se um relatório já estava no erro antes do tempo especificado, ela pode ser ignorada porque ele já foi sinalizado anteriormente.

- Se uma propriedade está alternando entre o aviso e erro, determinar por quanto tempo ele foi não íntegro (não Okey ou seja,). Por exemplo, um alerta se a propriedade não tiver sido eficaz, por mais de cinco minutos pode ser traduzido em (HealthState! = Okey e agora - LastOkTransitionTime > 5 minutos).

## <a name="example-report-and-evaluate-application-health"></a>Exemplo: Relatório e avaliar a integridade do aplicativo
O exemplo a seguir envia um relatório de integridade através do PowerShell no aplicativo **tecidos: / WordCount** da origem **MyWatchdog**. O relatório de integridade contém informações sobre a propriedade de integridade "disponibilidade" em um estado de integridade de erro, com TimeToLive infinito. Em seguida, ele consulta a integridade do aplicativo, que retorna agregado erros de estado de integridade e os eventos de integridade relatados na lista de eventos de integridade.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Uso de modelo de integridade
O modelo de saúde permite serviços de nuvem e a plataforma de serviço tecidos subjacente para dimensionar, pois decisões de monitoramento e integridade serão distribuídos entre os diferentes monitores dentro do cluster.
Outros sistemas possui um serviço único, centralizado no nível do cluster analisa todas as *potencialmente* informações úteis emitidas por serviços. Essa abordagem impede escalabilidade deles. Ele também não permite que eles coletar informações muito específicas para ajudar a identificar problemas e possíveis problemas como próximo a causa possível.

O modelo de integridade é usado muito para monitoramento e diagnóstico, para avaliar a integridade de cluster e de aplicativo e para monitoradas atualizações. Outros serviços usam dados de integridade para executar reparos automáticos, construir histórico de saúde de cluster e emitir alertas em determinadas condições.

## <a name="next-steps"></a>Próximas etapas
[Exibir relatórios de integridade do serviço tecidos](service-fabric-view-entities-aggregated-health.md)

[Usar relatórios de integridade do sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Como relatar e verificar a integridade do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Adicionar relatórios de integridade de serviço tecidos personalizados](service-fabric-report-health.md)

[Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização do aplicativo de serviço tecidos](service-fabric-application-upgrade.md)
