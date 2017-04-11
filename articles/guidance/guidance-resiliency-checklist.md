<properties
   pageTitle="Lista de verificação de resiliência | Microsoft Azure"
   description="Lista de verificação que fornece orientação para questões de resiliência durante o design."
   services=""
   documentationCenter="na"
   authors="petertaylor9999"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="petertay"/>

# <a name="azure-resiliency-guidance-resiliency-checklist"></a>Orientação de resiliência Azure: lista de verificação de resiliência

Projetar seu aplicativo para resiliência exige planejamento e reduzir uma variedade de modos de falha que podem ocorrer. Examine os itens desta lista de verificação em relação a seu design de aplicativo para torná-lo mais flexível.

## <a name="requirements"></a>Requisitos

- **Defina os requisitos de disponibilidade do cliente.** Seu cliente terá requisitos de disponibilidade dos componentes em seu aplicativo e isso afetará o design do seu aplicativo. Obtenha o contrato do seu cliente para as metas de disponibilidade de cada parte do seu aplicativo, caso contrário seu design pode não atender às expectativas do cliente. Para obter mais informações, consulte a seção [definindo seus requisitos de resiliência](guidance-resiliency-overview.md#defining-your-resiliency-requirements) do documento [Criando aplicativos flexíveis para Azure](guidance-resiliency-overview.md) .

## <a name="failure-mode-analysis"></a>Análise de modo de falha

- **Execute uma análise de modo de falha (FMA) para seu aplicativo.** FMA é um processo para a criação de resiliência em um aplicativo no início do estágio de design. As metas de um FMA incluem:  

    - Identifica quais tipos de falhas de um aplicativo detectado. 
    - Capture os possíveis efeitos e impacto de cada tipo de falha no aplicativo.
    - Identifica estratégias de recuperação. 

    Para obter mais informações, consulte [Criando aplicativos flexíveis para Azure: análise de modo de falha][fma].  

## <a name="application"></a>Aplicativo

- **Implante várias instâncias dos serviços.** Serviços inevitavelmente falharão e se seu aplicativo depende de uma única instância de um serviço inevitavelmente falhará também. Para provisionar várias instâncias do [Serviço de aplicativo do Azure](../app-service/app-service-value-prop-what-is.md), selecione um [Plano de serviço de aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) que oferece várias instâncias. Para serviços de nuvem do Azure, configure cada uma das suas funções usar [várias instâncias](../cloud-services/cloud-services-choose-me.md#scaling-and-management). Para [Azure VMs (máquinas virtuais)](../virtual-machines/virtual-machines-windows-about.md), certifique-se de que sua arquitetura de máquina virtual inclui máquina virtual mais de um e que cada máquina virtual está incluído em um [conjunto de disponibilidade][availability-sets].   

- **Use um balanceador de carga para distribuir solicitações.** Um balanceador de carga distribui solicitações de seu aplicativo para instâncias de serviço eficaz, Removendo instâncias não íntegras de rotação. Se seu serviço usa o serviço de aplicativo do Azure ou serviços de nuvem do Azure, ela já está carga equilibrada para você. No entanto, se seu aplicativo usa VMs do Azure, você precisará provisionar um balanceador de carga. Consulte overview [Balanceador de carga do Azure](../load-balancer/load-balancer-overview.md) para obter mais detalhes. 

- **Configure os Gateways de aplicativos do Azure para usar várias instâncias.** Dependendo de requisitos do aplicativo, um [Gateway de aplicativo do Azure](../application-gateway/application-gateway-introduction.md) pode ser melhor adequado para distribuir solicitações de serviços do aplicativo. No entanto, única instâncias do serviço Application Gateway não há garantia por um SLA portanto, é possível que o seu aplicativo pode falhar se a instância de Gateway aplicativo falhar. Provisione mais de uma instância do Gateway de aplicativo média ou maior para garantir a disponibilidade do serviço sob os termos do [SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/).

- **Conjuntos de disponibilidade de uso para cada camada de aplicativo**. Colocando suas instâncias em um [conjunto de disponibilidade] [ availability-sets] garante conectividade com pelo menos uma instância de máquina virtual dentro os termos do [SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_2/). Se suas VMs não estiverem em um conjunto de disponibilidade que não tiver garante protegendo-los e é possível que eles todos podem falhar ou ser atualizados simultaneamente. 

- **Considere a possibilidade de implantação do aplicativo em várias regiões.** Se seu aplicativo for implantado em uma única região, no evento raro toda a região fica indisponível, seu aplicativo também ficarão indisponível. Isso pode ser aceitável sob os termos de SLA do seu aplicativo. Em caso afirmativo, considere implantar o seu aplicativo e seus serviços em várias regiões. Uma implantação de várias regiões pode usar um padrão de ativa (distribuindo solicitações em várias instâncias ativas) ou um padrão de ativo-passivo (mantendo uma instância "quente" em reserva, caso a instância primária falha). Recomendamos que você implantar várias instâncias dos serviços do seu aplicativo entre pares regionais. Para obter mais informações, consulte [desastre e continuidade de negócios recuperação (BCDR): Azure par regiões](../best-practices-availability-paired-regions.md).

- **Implemente padrões de resiliência para operações remotas onde apropriado.** Se seu aplicativo depende de comunicação entre serviços remotos, o caminho de comunicação inevitavelmente falhará. Se houver várias falhas, as instâncias restantes eficaz, dos serviços do seu aplicativo podem ser sobrecarregadas com solicitações. Há vários padrões útil para lidar com falhas comuns, incluindo o padrão de tempo limite, o [padrão de repetição][retry-pattern], o [separador de circuito] [ circuit-breaker] padrão e outras pessoas. Para obter mais informações, consulte [Criando aplicativos flexíveis para Azure](guidance-resiliency-overview.md#implementing-resiliency-strategies). 

- **Use autoscaling responder a aumentos de carga.** Se seu aplicativo não estiver configurado para dimensionar automaticamente como carga aumenta, é possível que os serviços do seu aplicativo falhará se eles ficam saturados com solicitações de usuário. Para obter mais detalhes, consulte o seguinte:

    - Geral: [lista de verificação de escalabilidade](../best-practices-scalability-checklist.md) 
    - Serviço de aplicativo do Azure: [Dimensionar a contagem de instância manualmente ou automaticamente][app-service-autoscale]
    - Serviços de nuvem: [como escala automática um serviço na nuvem][cloud-service-autoscale]
    - Máquinas virtuais: [define escala automática de dimensionamento e máquina virtual][vmss-autoscale]


- **Implementar operações assíncronas sempre que possível.** Operações síncronas podem monopolizar os recursos e bloquear outras operações enquanto o chamador aguarda o conclusão do processo. Design cada parte do seu aplicativo para permitir operações assíncronas sempre que possível. Para obter mais informações sobre como implementar programação assíncrona em c#, consulte [programação assíncrona com assíncrono e esperar][asynchronous-c-sharp].

- **Use o Gerenciador de tráfego do Azure para rotear o tráfego do seu aplicativo para diferentes regiões.**  [Gerenciador de tráfego Azure] [ traffic-manager] executa balanceamento de carga no nível de DNS e pode direcionar o tráfego para diferentes regiões com base no [roteamento de tráfego] [ traffic-manager-routing] método especificado por você e a integridade dos pontos de extremidade do seu aplicativo. 

- **Configurar e testar testes de integridade de seu balanceadores de carga e gerentes de tráfego.** Verifique se sua lógica de integridade verifica as partes essenciais do sistema e responde adequadamente para testes de integridade.

    - A integridade testes do [Azure tráfego Manager] [ traffic-manager] e [Balanceador de carga do Azure] [ load-balancer] servir uma função específica. Gerenciador de tráfego, o teste de integridade determina alternar para outra região. Para um balanceador de carga, ele determina se remover uma máquina virtual de rotação.      

    - De um teste de Gerenciador de tráfego, seu ponto de extremidade de integridade deve verificar quaisquer dependências críticas que são implantados dentro da mesma região e cuja falha deve acionar um failover para outra região.  

    - Para um balanceador de carga, o ponto de extremidade de saúde deve relatar a integridade da máquina virtual. Não inclua outros níveis ou serviços externos. Caso contrário, uma falha que ocorre fora a máquina virtual fará com que o balanceador de carga remover a máquina virtual de rotação.

    - Para obter orientações sobre como implementar o monitoramento de integridade de seu aplicativo, consulte [Padrão de monitoramento de ponto de extremidade de integridade](https://msdn.microsoft.com/library/dn589789.aspx).

- **Monitore os serviços de terceiros.** Se seu aplicativo tiver dependências nos serviços de terceiros, identificar onde e como esses serviços de terceiros podem falhar e o que as falhas de efeito terá em seu aplicativo. Um serviço de terceiros pode não incluir monitoramento diagnósticos e, portanto, é importante para registrar suas chamadas deles e correlação-los com a integridade do aplicativo e usando um identificador exclusivo o log de diagnóstico. Para obter mais informações sobre as práticas recomendadas para monitoramento e diagnóstico, consulte as [orientações de monitoramento e diagnóstico] [ monitoring-and-diagnostics-guidance] documento.

- **Certifique-se de que qualquer serviço de terceiros que você consumir fornece um SLA.** Se seu aplicativo depende de um serviço de terceiros, mas o terceiros não fornece nenhuma garantia de disponibilidade na forma de um SLA, disponibilidade de seus aplicativos também não é possível garantir. Seu SLA só é tão boa quanto o componente menos disponível do seu aplicativo.


## <a name="data-management"></a>Gerenciamento de dados

- **Compreenda os métodos de replicação de fontes de dados do seu aplicativo.** Os dados do aplicativo serão armazenados nos diferentes fontes de dados e tem requisitos de disponibilidade diferentes. Avaliar os métodos de replicação para cada tipo de armazenamento de dados no Azure, incluindo [Replicação de armazenamento do Azure](../storage/storage-redundancy.md) e [Replicação geográfica SQL banco de dados ativo](../sql-database/sql-database-geo-replication-overview.md) para garantir que os requisitos de dados do aplicativo são satisfeitos.

- **Certifique-se de que nenhuma conta de usuário único tem acesso aos dados de produção e backup.** Os backups de dados são comprometidos se uma única conta de usuário tem permissão para gravar a fontes de backup e de produção. Um usuário mal-intencionado intencionalmente pode excluir todos os seus dados, enquanto acostumado acidentalmente pode excluí-la. Projetar seu aplicativo para limitar as permissões de cada conta de usuário para que somente os usuários que precisam de acesso de gravação têm acesso de gravação e é somente para produção ou backup, mas não ambos.

- **Documento sua fonte de dados falhar, exibem o processo e testá-lo.** No caso onde sua fonte de dados falha modo catastrófico, um operador humano precisará seguir um conjunto de instruções documentadas para alternar para uma nova fonte de dados. Se as etapas documentadas tem erros, um operador não poderão acompanhá-los com êxito e falha sobre o recurso. Teste regularmente as etapas de instrução para verificar se um operador acompanhá-los pode falhar e falhar novamente a fonte de dados com êxito.

- **Valide os backups de dados.** Verifique regularmente que seus dados de backup são o esperado executando um script para validar a integridade dos dados, esquema e consultas. Não há nenhum ponto tendo um backup se não é útil restaurar suas fontes de dados. Faça logon e relatar qualquer inconsistência, para que o serviço de backup pode ser reparado.

- **Considere usar um tipo de conta de armazenamento que são redundantes de localização geográfica.** Dados armazenados em uma conta de armazenamento do Azure é sempre replicados localmente. No entanto, há várias estratégias de replicação à sua escolha quando uma conta de armazenamento está provisionada. Selecione [Armazenamento redundante de localização geográfica de acesso de leitura do Azure (ar-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) para proteger seus dados de aplicativo contra o caso raro quando toda a região fica indisponível.

    > [AZURE.NOTE] Para VMs, não dependem replicação de ar GRS para os restaure máquina virtual (arquivos VHD). Em vez disso, use o [Backup do Azure][azure-backup].   

## <a name="operations"></a>Operações

- **Implemente o monitoramento e alerta práticas recomendadas em seu aplicativo.** Sem PRI monitoramento, diagnóstico e alertas, não há nenhuma maneira de detectar falhas em seu aplicativo e alerta um operador para corrigi-los. Para obter mais informações sobre as práticas recomendadas, consulte as [orientações de monitoramento e diagnóstico] [ monitoring-and-diagnostics-guidance] documento.

- **Medir estatísticas de chamada remota e disponibilizar as informações para a equipe do aplicativo.**  Se você não controlar e relatar estatísticas de chamada remota em tempo real e fornecem uma maneira fácil de examinar essas informações, a equipe de operações não terá um modo de exibição instantâneo da integridade de seu aplicativo. E se você apenas tempo de chamada remota média de medida, você não terá informações suficientes para revelar problemas nos serviços. Resuma métricas de chamada remota como latência, produtividade e erros nos percentuais 99 e 95. Execute análises estatísticas em métricas de descobrir erros que ocorrem dentro de cada percentil.

- **Controle o número de exceções temporárias e tentativas ao longo de um período de tempo apropriado.** Se você não controlar e monitorar exceções temporárias e tentativas ao longo do tempo, é possível que uma falha ou problema pode ser ocultada ao lógica de repetição do seu aplicativo. Isto é, se seu monitoramento e registro em log só mostra o sucesso ou falha de uma operação, o fato de que a operação tinha ser repetida várias vezes devido a exceções ficará oculto. Uma tendência de aumento de exceções ao longo do tempo indica que o serviço está tendo um problema e pode falhar. Para obter mais informações, consulte [Repetir orientações específicas do serviço][retry-service-guidance].

- **Implemente um sistema de aviso antecipado que um operador de alerta.** Identifica a chave de desempenho indicadores de integridade do seu aplicativo, como exceções temporárias e remota latência de chamadas e definir valores de limite apropriado para cada um deles. Envie um alerta para operações quando o valor de limite é alcançado. Defina esses limites em níveis de identificam problemas antes que eles se tornam essenciais e exigem uma resposta de recuperação.

- **Documente o processo de lançamento do aplicativo.** Sem a documentação do processo de lançamento detalhadas, um operador pode implantar uma atualização incorreta ou incorretamente definir configurações para seu aplicativo. Claramente definem o processo de lançamento do documento e verifique se ele está disponível para toda a equipe de operações. Práticas recomendadas para a implantação e apresentam resiliência de seu aplicativo são detalhadas na [implantação e apresentam resiliência] [ guidance-resilient-deployment] seção do documento a orientação de resiliência.

- **Certifique-se de que mais de uma pessoa da equipe é treinamento para monitorar o aplicativo e execute as etapas de recuperação manual.** Se você tiver apenas um único operador os membros da equipe que podem monitorar o aplicativo e disparar etapas de recuperação, essa pessoa se torna um único ponto de falha. Treinar várias pessoas em detecção e recuperação e certificar-se de que sempre há pelo menos uma ativa a qualquer momento.

- **Automatize o processo de implantação do aplicativo.** Se sua equipe de operações é necessária para implantar seu aplicativo manualmente, erros humanos podem causar a implantação falha. Para obter mais informações sobre as práticas recomendadas para automatizar a implantação do aplicativo, consulte a [implantação e apresentam resiliência] [ guidance-resilient-deployment] seção do documento a orientação de resiliência.

- **O processo de lançamento para maximizar a disponibilidade de aplicativo de design.** Se o processo de lançamento requer os serviços ficar offline durante a implantação, seu aplicativo estará disponível até que elas ficar online novamente. Use a técnica de implantação [azul/verde](http://martinfowler.com/bliki/BlueGreenDeployment.html) ou [libere delator](http://martinfowler.com/bliki/CanaryRelease.html) para implantar o seu aplicativo de produção. Ambas as técnicas envolvem implantando seu código de lançamento junto com o código de produção para que os usuários do código de lançamento podem ser redirecionados para o código de produção em caso de uma falha. Para obter mais informações, consulte a [implantação e apresentam resiliência] [ guidance-resilient-deployment] seção do documento a orientação de resiliência.

- **Faça logon e auditoria implantações do seu aplicativo.** Se você usar transferidos técnicas de implantação como azul/verde ou canary versões haverá mais de uma versão do seu aplicativo em execução em produção. Se ocorrer um problema, é essencial determinar qual versão do seu aplicativo está causando um problema. Implemente uma estratégia de log robusto para capturar o máximo possível de informações de versão específica. 

- **Certifique-se de que seu aplicativo não é executado ao [limites de assinatura do Azure](../azure-subscription-service-limits.md).** Assinaturas do Azure tem limites em determinados tipos de recursos, como o número de grupos de recursos, o número de cores e o número de contas de armazenamento.  Se seus requisitos de aplicativo excederem limites de assinatura Azure, crie outro Azure assinatura e provisionar recursos suficientes lá.

- **Certifique-se de que seu aplicativo não é executado ao [limites de cada serviço](../azure-subscription-service-limits.md).** Serviços de Azure individuais têm limites de consumo &mdash; por exemplo, de limites de armazenamento, produtividade, número de conexões, solicitações por segundo e outras métricas das. Seu aplicativo falhará se ele tenta usar recursos além esses limites. Isso resultará em inatividade de otimização e possíveis de serviço para os usuários afetados. 

    Dependendo de seus requisitos de aplicativos e o serviço específico, muitas vezes você pode evitar esses limites dimensionando para cima (por exemplo, escolhendo a outra camada de preços) ou dimensionamento (adicionando novas instâncias).  

- **Design requisitos de armazenamento do seu aplicativo para ficar dentro destinos de desempenho e escalabilidade de armazenamento do Azure.** Armazenamento do Azure foi projetado para funcionar dentro de destinos de desempenho e escalabilidade predefinidos, portanto projetar seu aplicativo para utilizar o armazenamento dentro esses destinos. Se você exceder esses destinos seu aplicativo terão a otimização de armazenamento. Para corrigir isso, provisione contas adicionais de armazenamento. Se você executar ao limite de conta de armazenamento, provisionar assinaturas adicionais do Azure e provisionar contas adicionais de armazenamento lá. Para obter mais informações, consulte [metas de desempenho e escalabilidade de armazenamento do Azure](../storage/storage-scalability-targets.md).

- **Selecione o tamanho correto de máquina virtual do aplicativo.** Meça a CPU, memória, disco e i/o das suas VMs em produção real e verifique se o tamanho de máquina virtual que tiver selecionado é suficiente. Caso contrário, seu aplicativo poderá ter problemas de capacidade como VMs abordagem seus limites. Tamanhos de máquina virtual são descritos em detalhes no documento [tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-windows-sizes.md) .

- **Determine se a carga de trabalho do seu aplicativo é estável ou flutuantes ao longo do tempo.** Se sua carga de trabalho flutuar ao longo do tempo, use escala de máquina virtual Azure define para dimensionar automaticamente o número de instâncias de máquina virtual. Caso contrário, você terá que aumentar ou diminuir o número de VMs manualmente. Para obter mais informações, consulte [Visão geral de conjuntos de escala de máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

- **Selecione o nível de serviço certo para o Azure SQL Database.** Se seu aplicativo usa o banco de dados do SQL Azure, certifique-se de que você selecionou o nível de serviço apropriado. Se você selecionar uma camada que não seja capaz de lidar com requisitos de unidade (DTU) de transação de banco de dados do seu aplicativo, o uso de dados será reduzido. Para obter mais informações sobre como selecionar o plano de serviço correto, consulte o [desempenho e opções de banco de dados SQL: entender o que está disponível em cada nível de serviço](../sql-database/sql-database-service-tiers.md) documento. 

- **Ter um plano de reversão para implantação.** É possível que sua implantação do aplicativo pode falhar e fazer seu aplicativo ficam indisponíveis. Crie um processo de reversão para voltar para uma versão de boa conhecida última e minimizar o tempo de inatividade. Consulte a [implantação e apresentam resiliência] [ guidance-resilient-deployment] seção do documento a orientação de resiliência para obter mais informações. 

- **Crie um processo de interação com suporte Azure.** Se o processo de contato [de suporte do Azure](https://azure.microsoft.com/support/plans/) não estiver definido antes de contatar o suporte necessário, tempo de inatividade será ser uma prolongada como o processo de suporte navega pela primeira vez. Inclua o processo para contatar o suporte e crescentes problemas como parte de resiliência do seu aplicativo desde o início.

- **Certifique-se de que seu aplicativo não use mais do que o número máximo de contas de armazenamento por assinatura.** Azure permite um máximo de 200 contas de armazenamento por assinatura. Se seu aplicativo exigir mais contas de armazenamento que estão disponíveis atualmente em sua assinatura, você precisará criar uma nova assinatura e criar contas de armazenamento adicional lá. Para obter mais informações, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md#storage-limits).

- **Certifique-se de que seu aplicativo não excede os destinos de escalabilidade para discos de máquina virtual.** Uma máquina virtual IaaS do Azure oferece suporte para anexar um número de discos de dados, dependendo de vários fatores, incluindo o tamanho de máquina virtual e o tipo de conta de armazenamento. Se seu aplicativo excede os destinos de escalabilidade para discos de máquina virtual, provisionar contas de armazenamento adicional e crie os discos de máquina virtual lá. Para obter mais informações, consulte [metas de desempenho e escalabilidade de armazenamento do Azure] (... / storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)

## <a name="test"></a>Teste

- **Execute failover e failback testes de seu aplicativo.** Se você ainda não testamos totalmente failover e failback, você não pode ter certeza de que os serviços dependentes em seu aplicativo aparecem novamente de forma sincronizada durante a recuperação de dados. Certifique-se de que depende do seu aplicativo de serviços failover e falhas na ordem correta.

- **Execute testes de seu aplicativo de inclusão de falhas.** Seu aplicativo pode falhar por vários motivos diferentes, como validade do certificado, esgotamento de recursos do sistema em uma máquina virtual ou falhas de armazenamento. Teste seu aplicativo em um ambiente mais próximo possível para produção, por simular ou disparo falhas reais. Por exemplo, excluir certificados, artificial consumir recursos do sistema ou excluir uma fonte de armazenamento. Verifique se a capacidade do aplicativo para recuperar todos os tipos de falhas, sozinhos e em combinação. Verifique se falhas não são propagar ou em cascata por meio de seu sistema.

- **Execute testes em produção usando ambos os dados de usuário sintéticos e real.** Teste e produção são raramente idênticos, portanto é importante usar azul/verde ou uma implantação canary e testar seu aplicativo em produção. Isso permite que você teste seu aplicativo em produção sob carga real e certifique-se de que ele funcionará como esperado quando totalmente implantado.

## <a name="security"></a>Segurança

- **Implementar proteção de nível de aplicativo contra distribuído ataques de negação de serviço (DDoS).** Os serviços do Azure estão protegidos contra ataques de DDos na camada de rede. No entanto, o Azure não pode proteger contra ataques de camada de aplicativo, porque é difícil distinguir entre as solicitações de usuário true de solicitações de usuário mal-intencionado. Para obter mais informações sobre como se proteger contra ataques de DDoS de camada de aplicativo, consulte a seção "Proteção contra DDoS" de [Segurança de rede do Microsoft Azure](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf) (download do PDF).

- **Implemente o princípio do privilégio mínimo para acessar recursos do aplicativo.** O padrão para acessar os recursos do aplicativo deverá ser mais restritivo possível. Conceder permissões de nível superiores em um base de aprovação. Concedendo acesso muito permissivo para recursos de seu aplicativo por padrão pode resultar em alguém intencionalmente ou acidentalmente excluir recursos. Azure oferece [controle de acesso baseado em função](../active-directory/role-based-access-built-in-roles.md) para gerenciar os privilégios de usuário, mas é importante verificar permissões de privilégio mínimo para outros recursos que têm seus próprios sistemas de permissões como o SQL Server. 

## <a name="telemetry"></a>Telemetria

- **Dados de telemetria do log enquanto o aplicativo está em execução no ambiente de produção.** Capture informações de telemetria robustos enquanto o aplicativo é executado no ambiente de produção ou não terá informações suficientes para diagnosticar a causa de problemas enquanto ele está ativamente servindo usuários. Mais informações estão disponíveis no log de práticas recomendadas está disponível na [orientação de monitoramento e diagnóstico] [ monitoring-and-diagnostics-guidance] documento.

- **Implemente o registro em log usando um padrão assíncrono.** Se as operações de log estão síncronas, eles podem bloquear o código do seu aplicativo. Certifique-se de que as operações de log são implementadas como operações assíncronas. 

- **Correlação dados do log em limites de serviços.** Em um aplicativo de n camadas típico, uma solicitação de usuário pode percorrer vários limites de serviço. Por exemplo, uma solicitação de usuário normalmente se origina na camada da web e é passada para a camada de negócios e finalmente persistentes na camada de dados. Em cenários mais complexos, uma solicitação de usuário pode ser distribuída a várias lojas diferentes de serviços e dados. Certifique-se de que seu sistema de log correlaciona chamadas entre limites de serviço para que você possa rastrear a solicitação em todo o aplicativo.

##  <a name="azure-resources"></a>Recursos do Azure 

- **Use modelos do Gerenciador de recursos do Azure para provisionar recursos.** Gerenciador de recurso modelos facilitam automatizar implantações por meio do PowerShell ou CLI Azure, que leva a um processo de implantação mais confiável. Para obter mais informações, consulte [Visão geral do Gerenciador de recursos do Azure][resource-manager].

- **Dê nomes significativos de recursos.** Atribuindo recursos nomes significativos facilita localizar um recurso específico e compreender sua função. Para obter mais informações, consulte [recomendado convenções para recursos Azure de nomenclatura](guidance-naming-conventions.md) 

- **Usar o controle de acesso baseado em função (RBAC)**. Use RBAC para controlar o acesso aos recursos do Azure que você implantar. RBAC permite que você atribuir funções de autorização aos membros da equipe DevOps, para impedir exclusões acidentais ou alterações a recursos implantados. Para obter mais informações, consulte [Introdução ao gerenciamento de acesso no portal do Azure](../active-directory/role-based-access-control-what-is.md) 

- **Use bloqueios de recursos para recursos essenciais, como VMs.** Bloqueios de recursos impedem que um operador acidentalmente excluir um recurso. Para obter mais informações, consulte [recursos de bloqueio com o Gerenciador de recursos do Azure](../resource-group-lock-resources.md) 

- **Pares regionais.** Ao implantar duas regiões, escolha regiões o mesmo par regional. Em caso de falta de ampla, recuperação de uma região é priorizamos fora cada par. Alguns serviços, como armazenamento geográfica redundante fornecem duplicação automática para a região par. Para obter mais informações, consulte [desastre e continuidade de negócios recuperação (BCDR): Azure par regiões](../best-practices-availability-paired-regions.md) 

- **Grupos de recursos de organizar pela função e ciclo de vida**.  Em geral, um grupo de recursos deve conter recursos que compartilham o mesmo ciclo de vida. Isso torna mais fácil gerenciar implantações, excluir implantações de teste e atribuir direitos de acesso, reduzindo a chance que uma implantação de produção for excluída acidentalmente ou modificada. Criar grupos de recursos separado para produção, desenvolvimento e ambientes de teste. Em uma implantação de várias regiões, coloca recursos para cada região em grupos de recursos separado. Isso facilita reimplantar uma região sem afetar as outras regiões. 

## <a name="azure-services"></a>Serviços do Azure 

Os itens de lista de verificação a seguir se aplicam a serviços específicos no Azure.

###  <a name="app-service"></a>Serviço de aplicativo 

- **Use camadas Standard ou Premium.** Esses níveis slots de preparação de suporte e backups automatizados. Para obter mais informações, consulte [Visão geral detalhada de planos de serviço de aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) 

- **Evite dimensionamento para cima ou para baixo.** Em vez disso, selecione uma camada e tamanho da instância que atendam a seus requisitos de desempenho em carga típica e, em seguida, [escala](../app-service-web/web-sites-scale.md) as instâncias para lidar com alterações no volume de tráfego. Dimensionamento de cima e para baixo pode disparar uma reinicialização do aplicativo.  

- **Armazenar configuração como configurações de aplicativo.** Use configurações de aplicativo para armazenar definições de configuração, como configurações de aplicativo. Defina as configurações de seus modelos do Gerenciador de recursos ou uso do PowerShell, para que você possa aplicá-los como parte de uma implantação automatizada / Atualizar processo, que é mais confiável. Para obter mais informações, consulte [Configurar web apps no serviço de aplicativo do Azure](../app-service-web/web-sites-configure.md). 

- **Crie planos de serviço de aplicativo separados para teste e produção.** Não use slots na sua implantação de produção para teste.  Todos os aplicativos no mesmo plano de serviço de aplicativo compartilham as mesmas instâncias de máquina virtual. Se você colocar implantações de teste e produção no mesmo plano, ele pode afetar negativamente a implantação de produção. Por exemplo, testes de carga podem prejudicar o site de produção ao vivo. Colocando implantações de teste em um plano separado, isolá-los da versão de produção.  

- **Aplicativos web separado da web APIs**. Se a solução tiver um web front-end e uma web API, considere a possibilidade de Decomposição-los em aplicativos de serviço de aplicativo separados. Este design facilita a decompor a solução por carga de trabalho. Você pode executar o aplicativo da web e a API em planos de serviço de aplicativo separados, para que eles podem ser dimensionados de maneira independente. Se não precisar esse nível de escalabilidade no primeiro, você pode implantar os aplicativos para o mesmo plano e transfira-os para planos separados posteriormente, se necessário.

- **Evite usar o recurso de backup do serviço de aplicativo para fazer backup de bancos de dados do SQL Azure.** Em vez disso, use [backups automatizados do banco de dados SQL][sql-backup]. Backup de serviço de aplicativo exporta o banco de dados para um arquivo de .bacpac SQL, que custa DTUs.  

- **Implante um slot temporário.** Crie um slot de implantação para preparação. Implantar atualizações de aplicativo para o slot de teste e verifique a implantação antes de trocá-lo em produção. Isso reduz as chances de uma atualização de mau em produção. Ele também garante que todas as instâncias são aquecidas antes de ser trocado em produção. Muitos aplicativos têm uma significativo aquecimento e a hora de início resfriado. Para obter mais informações, consulte [configurar ambientes web Apps em um serviço de aplicativo do Azure temporários](../app-service-web/web-sites-staged-publishing.md). 

-  **Crie um slot de implantação para manter a implantação do último válida (LKG).** Quando você implantar uma atualização em produção, mova a implantação de produção anterior no slot LKG. Isso facilita reverter uma implantação incorreta. Se você descobrir um problema mais tarde, você pode reverter rapidamente a versão LKG. Para obter mais informações, consulte [arquitetura de referência Azure: aplicativo web básico](guidance-web-apps-basic.md). 

- **Habilitar o log de diagnóstico**, incluindo o log de aplicativo e o registro de servidor web. Registro em log é importante para monitoramento e diagnóstico. Consulte [Ativar o diagnóstico de registro em log para web apps em um serviço de aplicativo do Azure](../app-service-web/web-sites-enable-diagnostic-log.md)

- **Log de armazenamento de blob**. Isso facilita coletar e analisar os dados. 

- **Crie uma conta de armazenamento separado para logs.** Não use a mesma conta de armazenamento para logs e dados de aplicativo. Isso ajuda a impedir log reduzindo o desempenho do aplicativo. 

- **Monitorar o desempenho.** Use um serviço como [Novo Relíquia](http://newrelic.com/) ou [Aplicativo ideias](../application-insights/app-insights-overview.md) para monitorar o desempenho do aplicativo e comportamento sob carga de monitoramento de desempenho.  Monitoramento do desempenho fornece o aplicativo de percepção em tempo real. Ele permite que você diagnosticar problemas e executar análise de causas raiz de falhas. 


###  <a name="application-gateway"></a>Gateway de aplicativo 

- **Provisione pelo menos duas instâncias.** Implante o Gateway de aplicativo com pelo menos duas instâncias. Uma única instância é um ponto único de falha. Use duas ou mais ocorrências para redundância e escalabilidade. Para poder para o [SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/), você deve provisionar duas ou mais ocorrências de média ou maiores. 

### <a name="azure-search"></a>Pesquisa do Azure

- **Provisione mais de uma réplica.** Use pelo menos duas réplicas para leitura alta disponibilidade ou três para leitura e gravação alta disponibilidade.

- **Configure indexadores para implantações de várias regiões.** Se você tiver uma implantação de várias regiões, considere suas opções para continuidade na indexação.

    - Se a fonte de dados for replicado geográfica, aponte cada indexador de cada serviço de pesquisa do Azure regional para sua réplica de fonte de dados local.  

    - Se a fonte de dados não estiver replicado geográfica, aponte vários indexadores a mesma fonte de dados, para que os serviços de pesquisa do Azure em vários regiões continuamente e independente indexar da fonte de dados. Para obter mais informações, consulte [Considerações de desempenho e otimização de pesquisa do Azure][search-optimization].

###  <a name="azure-storage"></a>Armazenamento do Azure 

- **Dados do aplicativo, use o armazenamento de localização geográfica redundantes de acesso de leitura (ar-GRS).** Armazenamento de ar GRS replica os dados para uma região secundária e fornece acesso somente leitura da região secundário. Se houver uma queda de armazenamento na região primária, o aplicativo pode ler os dados da região secundário. Para obter mais informações, consulte [replicação de armazenamento do Azure](../storage/storage-redundancy.md).

- **Discos de máquina virtual para usar o armazenamento de Premium** Para obter mais informações, consulte [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](../storage/storage-premium-storage.md).

- **Para armazenamento de fila, crie uma fila backup em outra região.** Para armazenamento de fila, uma réplica somente leitura restringiu uso, porque você não conseguir fila ou retirar itens. Em vez disso, crie uma fila de backup em uma conta de armazenamento em outra região. Se houver uma queda de armazenamento, o aplicativo pode usar a fila de backup, até que a região principal fique disponível novamente. Dessa maneira, o aplicativo ainda pode processar novas solicitações.  

###  <a name="documentdb"></a>DocumentDB 

- **Replica o banco de dados em regiões.** Com uma conta de várias regiões, seu banco de dados DocumentDB tem região de uma gravação e várias regiões de leitura. Se houver uma falha na região de gravação, você pode ler de outra réplica. O SDK do cliente lida com isso automaticamente. Você também pode falhar sobre a região de gravação para outra região. Para obter mais informações, consulte [dados de distribuir globalmente com DocumentDB](../documentdb/documentdb-distribute-data-globally.md).


###  <a name="sql-database"></a>Banco de dados SQL 

- **Use camadas Standard ou Premium.** Esses níveis oferecem um período mais no momento restauração (35 dias). Para obter mais informações, consulte [desempenho e opções de banco de dados SQL](../sql-database/sql-database-service-tiers.md).

- **Habilite a auditoria de banco de dados SQL.** Auditoria pode ser usada para diagnosticar ataques mal-intencionados ou erros humanos. Para obter mais informações, consulte [Introdução ao auditoria de banco de dados do SQL](../sql-database/sql-database-auditing-get-started.md). 

- **Usar localização geográfica-replicação ativa** Use replicação de localização geográfica ativa para criar um secundário legível em uma região diferente.  Se seu banco de dados primário falhar, ou simplesmente precisa ser colocado offline, execute um failover manual ao banco de dados secundário.  Até que você não sobre, o banco de dados secundário permanece somente leitura.  Para obter mais informações, consulte [Replicação geográfica SQL banco de dados ativo](../sql-database/sql-database-geo-replication-overview.md). 

- **Fragmentação de uso**. Considere usar fragmentação para o banco de dados de partição horizontalmente. Fragmentação pode fornecer isolamento de falhas. Para obter mais informações, consulte [escala check-out com o Azure SQL Database](../sql-database/sql-database-elastic-scale-introduction.md). 

- **Use Restaurar no momento para recuperar de erros humanos.**  No momento restauração retorna seu banco de dados para um ponto anterior no tempo. Para obter mais informações, consulte [recuperar um banco de dados do SQL Azure usando backups automatizados do banco de dados][sql-restore].

- **Use localização geográfica restauração para recuperar uma interrupção do serviço.** Localização geográfica restauração restaura um banco de dados de um backup redundantes de localização geográfica.  Para obter mais informações, consulte [recuperar um banco de dados do SQL Azure usando backups automatizados do banco de dados][sql-restore].


###  <a name="sql-server-running-in-a-vm"></a>SQL Server (executando uma máquina virtual)

- **Replica o banco de dados.** Use o SQL Server sempre em grupos de disponibilidade para replicar o banco de dados. Fornece alta disponibilidade se uma instância do SQL Server falhar. Para obter mais informações, consulte [mais informações...](guidance-compute-n-tier-vm.md) 

- **Fazer backup do banco de dados**. Se você já estiver usando o [Backup do Azure](https://azure.microsoft.com/documentation/services/backup/) para fazer backup de suas VMs, considere o uso de [Backup do Azure para cargas de trabalho do SQL Server usando o DPM](../backup/backup-azure-backup-sql.md). Com essa abordagem, há uma função de administrador de backup para a organização e um procedimento de recuperação unificado para VMs e SQL Server. Caso contrário, use o [Backup de gerenciadas do SQL Server para Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx). 


###  <a name="traffic-manager"></a>Gerenciador de tráfego 

- **Realize failback manual.** Após um failover Gerenciador de tráfego, executar failback manual, em vez de automaticamente falhando novamente. Antes de falhar novamente, verifique se todos os subsistemas de aplicativo íntegros.  Caso contrário, você pode criar uma situação onde o aplicativo inverte trocadas entre data centers. Para obter mais informações, consulte [Executando VMs em vários regiões](guidance-compute-multiple-datacenters.md).

- **Criar um ponto de extremidade de teste de integridade**. Crie um ponto de extremidade personalizado que relatórios sobre a integridade geral do aplicativo. Isso permite que o gerente de tráfego falha sobre se qualquer caminho crítico falhar, não apenas o front-end. O ponto de extremidade deve retornar um código de erro HTTP se qualquer dependência crítica está com problemas ou inacessível. Não relate erros de serviços não-críticos, no entanto. Caso contrário, o teste de integridade pode acionar o failover quando não é necessária, criando falsos positivos. Para obter mais informações, consulte [monitoramento de ponto de extremidade do Gerenciador de tráfego e failover](../traffic-manager/traffic-manager-monitoring.md).


###  <a name="virtual-machines"></a>Máquinas virtuais 

- **Evite a execução de uma carga de trabalho de produção em uma única VM.** Uma única implantação de máquina virtual não é flexível a manutenção planejada ou não. Em vez disso, coloque várias VMs em um conjunto de disponibilidade ou conjunto de escala de máquina virtual, com um balanceador de carga na frente. Para poder para SLA, pelo menos duas máquinas virtuais deve ser implantado dentro do mesmo conjunto de disponibilidade. Para obter mais informações, consulte [Visão geral de conjuntos de escala de máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 

- **Especifica a disponibilidade definida quando você provisionar a máquina virtual.** Atualmente, não há nenhuma maneira de adicionar uma VM Gerenciador de recursos a uma disponibilidade definida após a máquina virtual está provisionada. Quando você adiciona uma nova VM para uma disponibilidade existente definida, verifique se criar uma NIC para a máquina virtual e adicionar a NIC ao pool de endereço de back-end no balanceador de carga. Caso contrário, o balanceador de carga não rotear o tráfego de rede para essa máquina virtual. 

- **Coloque cada camada de aplicativos em um conjunto de disponibilidade separada.** Em um aplicativo de N camadas, não coloque VMs de diferentes camadas o mesmo conjunto de disponibilidade. VMs em um conjunto de disponibilidade são colocadas em domínios de falha (FDs) e atualizar domínios (UD). No entanto, para obter o benefício de redundância de FDs e UDs, cada máquina virtual do conjunto de disponibilidade deve ser capaz de lidar com as mesmas solicitações de cliente. 

- **Escolha o tamanho correto de máquina virtual com base em requisitos de desempenho.** Ao mover uma carga de trabalho existente para o Azure, comece com o tamanho de máquina virtual que aproxima aos servidores local. Em seguida, medir o desempenho de sua carga de trabalho real relacionadas com CPU, memória e disco IOPS e ajustar o tamanho, se necessário. Isso ajuda a garantir que o aplicativo se comporta como esperado em um ambiente de nuvem. Além disso, se você precisar várias placas de rede, lembre-se do limite de tamanho de cada NIC. 

- **Use o armazenamento de premium para VHDs.** Armazenamento do Azure Premium fornece suporte de disco de alto desempenho e baixa latência. Para obter mais informações, consulte [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](../storage/storage-premium-storage.md) escolha um tamanho de máquina virtual que suporta o armazenamento de premium. 

- **Crie uma conta de armazenamento separada para cada máquina virtual.** Coloque os VHDs para uma máquina virtual para uma conta de armazenamento separado. Isso ajuda a evitar atingir os limites IOPS para contas de armazenamento. Para obter mais informações, consulte [metas de desempenho e escalabilidade de armazenamento do Azure](../storage/storage-scalability-targets.md). No entanto, se você estiver implantando um grande número de VMs, lembre-se do limite de contas de armazenamento por assinatura. Consulte [limites de armazenamento](../azure-subscription-service-limits.md#storage-limits).

- **Criar uma conta de armazenamento separado para os logs de diagnóstico**. Logs de diagnóstico de gravação para a mesma conta de armazenamento como os VHDs, para evitar o log de diagnóstico não afetam o IOPS para os discos de máquina virtual. Uma conta de armazenamento localmente redundante (LRS) padrão é suficiente para os logs de diagnóstico. 

- **Instale aplicativos em um disco de dados, não o disco de sistema operacional.** Caso contrário, você pode atingir o limite de tamanho de disco. 

- **Use o Backup do Azure para fazer backup de VMs.** Backups protegem contra perda de dados acidental. Para obter mais informações, consulte [Proteger o Azure VMs com um cofre de serviços de recuperação](../backup/backup-azure-vms-first-look-arm.md).

- **Ativar logs de diagnóstico**, incluindo métricas de integridade básica, logs de infraestrutura e [diagnósticos de inicialização][boot-diagnostics]. Diagnósticos de inicialização podem ajudá-lo a diagnosticar uma falha na inicialização se sua máquina virtual entra em um estado não-inicializável. Para obter mais informações, consulte [Visão geral do Azure Logs de diagnóstico][diagnostics-logs].

- **Use a extensão de AzureLogCollector**. (Windows VMs somente). Essa extensão agrega logs de plataforma Windows Azure e carrega-los para o armazenamento do Azure, sem o operador remotamente efetuando login a máquina virtual. Para obter mais informações, consulte [AzureLogCollector extensão](../virtual-machines/virtual-machines-windows-log-collector-extension.md).


###  <a name="virtual-network"></a>Rede virtual 

- **A lista branca ou bloco de endereços IP públicos, adicione um NSG à sub-rede.** Bloquear o acesso de usuários mal-intencionados, ou permitir o acesso somente a partir de usuários que têm privilégio para acessar o aplicativo.  

- **Crie um teste de integridade personalizado.** Testes de integridade de Balanceador de carga pode testar HTTP ou TCP. Se uma máquina virtual é executado em um servidor HTTP, o teste HTTP é um indicador melhor do status de integridade de um teste TCP. Para um teste HTTP, use um ponto de extremidade personalizado que relata a integridade geral do aplicativo, incluindo todas as dependências críticas. Para obter mais informações, consulte [Visão geral de Balanceador de carga do Azure](../load-balancer/load-balancer-overview.md).

- **Não bloquear o teste de integridade.** O teste de integridade de Balanceador de carga é enviado de um endereço IP conhecido, 168.63.129.16. Não bloquear o tráfego de ou para este IP em qualquer diretivas de firewall ou regras de grupo (NSG) de segurança de rede. Bloquear o teste de integridade faria com que o balanceador de carga remover a máquina virtual de rotação. 

- **Habilite o log de Balanceador de carga.** Os logs mostram quantas VMs no back-end não está recebendo o tráfego de rede devido a respostas de falha de teste. Para obter mais informações, consulte [análise de Log de Balanceador de carga do Azure](../load-balancer/load-balancer-monitor-log.md).


<!-- links -->
[app-service-autoscale]: ../monitoring-and-diagnostics/insights-how-to-scale.md
[asynchronous-c-sharp]:https://msdn.microsoft.com/library/mt674882.aspx
[availability-sets]:../virtual-machines/virtual-machines-windows-manage-availability.md
[azure-backup]: https://azure.microsoft.com/documentation/services/backup/
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[cloud-service-autoscale]: ../cloud-services/cloud-services-how-to-scale.md
[diagnostics-logs]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[fma]: guidance-resiliency-failure-mode-analysis.md
[guidance-resilient-deployment]: guidance-resiliency-overview.md#resilient-deployment
[load-balancer]: load-balancer/load-balancer-overview.md
[monitoring-and-diagnostics-guidance]: ../best-practices-monitoring.md
[resource-manager]: ../azure-resource-manager/resource-group-overview.md
[retry-pattern]: https://msdn.microsoft.com/library/dn589788.aspx
[retry-service-guidance]: ../best-practices-retry-service-specific.md
[search-optimization]: ../search/search-performance-optimization.md
[sql-backup]: ../sql-database/sql-database-automated-backups.md
[sql-restore]: ../sql-database/sql-database-recovery-using-backups.md
[traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[traffic-manager-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[vmss-autoscale]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md
