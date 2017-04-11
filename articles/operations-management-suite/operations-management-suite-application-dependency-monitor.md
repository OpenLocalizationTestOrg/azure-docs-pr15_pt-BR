<properties
   pageTitle="Monitor de dependência de aplicativo (ADM) no pacote de gerenciamento de operações (OMS) | Microsoft Azure"
   description="Monitor de dependência de aplicativo (ADM) é uma solução de pacote de gerenciamento de operações (OMS) que detecta componentes de aplicativo em sistemas Windows e Linux automaticamente e mapeia a comunicação entre os serviços.  Este artigo fornece detalhes para implantação ADM em seu ambiente e usá-lo em uma variedade de cenários."
   services="operations-management-suite"
   documentationCenter=""
   authors="daseidma"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2016"
   ms.author="daseidma;bwren" />

# <a name="using-application-dependency-monitor-solution-in-operations-management-suite-oms"></a>Usar a solução de Monitor de dependência de aplicativos no pacote de gerenciamento de operações (OMS)
![Ícone de gerenciamento de alerta](media/operations-management-suite-application-dependency-monitor/icon.png) Monitor de dependência de aplicativo (ADM) detecta componentes de aplicativo em sistemas Windows e Linux automaticamente e mapeia a comunicação entre os serviços. Ele permite que você exiba seus servidores conforme você pensar – como sistemas interconectados que oferecem serviços essenciais.  Monitor de dependência de aplicativos mostra conexões entre servidores, processos, e portas em qualquer arquitetura TCP conectados com nenhuma configuração necessária diferente de instalação de um agente.

Este artigo descreve os detalhes do uso Monitor de dependência de aplicativos.  Para obter informações sobre como configurar agentes ADM e integração, consulte [solução de Configurando o Monitor de dependência de aplicativos no pacote de gerenciamento de operações (OMS)](operations-management-suite-application-dependency-monitor-configure.md)

>[AZURE.NOTE]Monitor de dependência de aplicativo está no modo de visualização particular.  Você pode solicitar acesso a visualização particular ADM em [https://aka.ms/getadm](https://aka.ms/getadm).
>
>Durante a visualização particular, todas as contas OMS têm acesso ilimitado ao ADM.  Nós ADM são gratuitos, mas os dados de análise de Log para os tipos de AdmComputer_CL e AdmProcess_CL irá ser limitados como qualquer outra solução.
>
>Depois de ADM insere demonstração pública, ele estará disponível somente para clientes pagos e gratuitos de visão e análise no OMS preços plano de.  Contas de nível gratuito serão limitadas ao 5 nós ADM.  Se você está participando de visualização particular e não está inscrito no OMS preços plano de quando ADM entra demonstração pública, ADM será desabilitado nesse momento. 


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Casos de uso: Faça seu TI processa ciente de dependência

### <a name="discovery"></a>Descoberta
ADM cria automaticamente um mapa de referência comuns de dependências entre seus servidores, processos e serviços de terceiros 3º.  Ele descobre e mapeia todas as dependências TCP, identificando conexões surpresa, remoto 3º sistemas de terceiros que depende e dependências tradicionais áreas escuras da sua rede como o DNS e AD.  ADM detecta conexões de rede que falhou que sistemas gerenciados estão tentando tornar, ajudando você identificar problemas de rede, interrupções de serviço e possíveis erros de configuração do servidor.

### <a name="incident-management"></a>Gerenciamento de incidente
ADM ajuda a eliminar as suposições de isolamento de problema por mostrando como sistemas estão conectados e que afetam os outros.  Além de conexões falhou, informações sobre clientes conectados ajudar a identificar balanceadores de carga estão configurados incorretamente, carga surpresa ou excessiva em serviços essenciais e maliciosas clientes como máquinas de desenvolvedor falando para sistemas de produção.  Fluxos de trabalho integrados com OMS alterar rastreamento também permite que você veja se um evento de alteração em um computador de back-end ou serviço explica a causa de um incidente.

### <a name="migration-assurance"></a>Garantia de migração
ADM permite planejar efetivamente, acelerar e validar migrações Azure, garantindo que nada será deixado e há sem interrupções de surpresa.  Você pode descobrir todos os sistemas interdependentes que precisam migrar juntas, avaliar capacidade e configuração do sistema e identificar se um sistema em execução ainda está servindo usuários ou é um candidato para encerrar em vez de migração.  Após a movimentação, você pode verificar carga de cliente e identidade para verificar sistemas de teste e clientes estão se conectando.  Se suas definições de planejamento e firewall sub-rede tiverem problemas, conexões com falha em mapas ADM redirecionará para os sistemas que precisam de conectividade.

### <a name="business-continuity"></a>Continuidade de negócios
Se você estiver usando a recuperação de Site do Azure e precisa de ajuda definindo a sequência de recuperação para seu ambiente do aplicativo, ADM pode automaticamente mostrar como sistemas dependem de outro para garantir que seu plano de recuperação é confiável.  Escolhendo um servidor crítico e exibindo seus clientes, você pode identificar os sistemas de front-end que devem ser recuperados somente depois que o servidor crítico é restaurado e disponível.  Por outro lado, olhando dependências de back-end do servidor crítico, você pode identificar os sistemas que devem ser recuperados antes de seu sistema de foco for restaurado.

### <a name="patch-management"></a>Gerenciamento de patches
ADM melhora o uso da avaliação de atualização de sistema do OMS mostrando que outras equipes e servidores dependem do serviço, para que você pode notificá-los com antecedência, antes de colocar seus sistemas para baixo para corrigir.  ADM também melhora o gerenciamento de patches no OMS mostrando se os seus serviços estão disponíveis e conectados corretamente depois que eles são corrigidos e reiniciados. 


## <a name="mapping-overview"></a>Visão geral de mapeamento
Agentes ADM coletam informações sobre todos os processos conectados TCP no servidor onde eles estiverem instalados, bem como detalhes sobre as conexões de entrada e saídas para cada processo.  Usando a lista de máquina no lado esquerdo da solução ADM, máquinas com agentes ADM podem ser selecionadas para visualizar suas dependências em um intervalo de tempo selecionado.  Dependência de máquina foco em uma máquina específica de mapas e mostram todas as máquinas que são os clientes TCP direta ou servidores da máquina.

![Visão geral ADM](media/operations-management-suite-application-dependency-monitor/adm-overview.png)

Máquinas podem ser expandidas no mapa para mostrar os processos em execução com conexões de rede ativa durante o intervalo de tempo selecionado.  Quando um computador remoto com um agente ADM é expandido para mostrar detalhes do processo, apenas os processos comunicar-se com o computador de foco são mostrados.  A contagem de agentes máquinas front-end conectando à máquina de foco é indicada no lado esquerdo dos processos que se conectarem ao.  Se a máquina de foco estiver fazendo uma conexão para uma máquina de back-end sem um agente, que back-end é representado com um nó no mapa que mostra seu endereço IPv4 e o nó pode ser expandido para mostrar portas individuais e serviços que máquina foco está se comunicando.

Por padrão, os mapas ADM exibir os últimos 10 minutos de informações de dependência.  Usando os controles de tempo no canto superior esquerdo, mapas podem ser consultados para intervalos de tempo histórico, até a toda uma hora, para mostrar como dependências procurou no passado, por exemplo, durante um incidente ou antes que ocorreu uma alteração.    Dados ADM são armazenados por 30 dias nos espaços de trabalho pagos e por 7 dias em espaços de trabalho gratuitos.

![Mapa de máquina com propriedades de máquina selecionada](media/operations-management-suite-application-dependency-monitor/machine-map.png)

## <a name="failed-connections"></a>Falha de conexões
Conexões com falha são mostradas em mapas ADM para processos e computadores, com mostrando uma linha vermelha tracejada se um sistema cliente está falhando alcançar um processo ou porta.  Conexões com falha são relatados de qualquer sistema com um agente ADM implantado se esse sistema for a tentar a conexão com falha.  ADM medidas isso observando sockets TCP que falham para estabelecer uma conexão.  Isso pode ser devido a um firewall, um erro de configuração no cliente ou servidor ou um serviço remoto não está disponível. 

![Falha de conexões](media/operations-management-suite-application-dependency-monitor/failed-connections.png)

Noções básicas sobre conexões com falha podem ajudar na solução de problemas, validação de migração, análise de segurança e Noções básicas sobre arquitetura geral.  Às vezes, falha conexões são inofensivas, mas eles geralmente apontam diretamente para um problema, como um ambiente de failover inesperadamente se tornar inacessível, … ou dois níveis de aplicativos não ser capaz de falar após a migração de nuvem.  Na imagem acima, IIS e WebSphere estiver executando, mas eles não consegue se conectar. 

## <a name="computer-and-process-properties"></a>Propriedades do processo e computador
Ao navegar um mapa ADM, você pode selecionar máquinas e processos para obter mais contexto sobre suas propriedades.  Máquinas fornecem informações sobre o nome DNS, endereços IPv4, capacidade de CPU e memória, tipo de máquina virtual, versão do sistema operacional, reinicie de última hora e as identificações dos seus agentes OMS e ADM.

Detalhes do processo são coletados de metadados de sistema operacional sobre processos em execução, incluindo o nome do processo, descrição do processo, nome de usuário e domínio (no Windows), nome da empresa, nome do produto, versão do produto, pasta de trabalho, linha de comando e hora de início do processo.

![Propriedades do processo](media/operations-management-suite-application-dependency-monitor/process-properties.png)

O painel de resumo do processo fornece informações adicionais sobre conectividade do processo, incluindo sua portas acopladas, conexões de entrada e saída e conexões com falha. 

![Resumo do processo](media/operations-management-suite-application-dependency-monitor/process-summary.png)

## <a name="oms-change-tracking-integration"></a>Integração de controle de alterações OMS
A integração do ADM com controle de alterações é automática quando ambas as soluções estão habilitadas e configuradas em seu espaço de trabalho do OMS.

O painel de resumo de máquina indica se eventos de controle de alterações ocorreram no computador selecionado durante o intervalo de tempo selecionado.

![Painel de resumo de máquina](media/operations-management-suite-application-dependency-monitor/machine-summary.png)

O painel de controle de alteração de máquina mostra uma lista de todas as alterações, com a primeira mais recente, juntamente com um link para analisar a pesquisa de Log para obter detalhes adicionais.
![Painel de controle de alteração de máquina](media/operations-management-suite-application-dependency-monitor/machine-change-tracking.png)

A seguir é um modo de exibição de eventos de alteração de configuração de detalhamento após selecionar **Mostrar em análise de Log**.
![Evento de alteração de configuração](media/operations-management-suite-application-dependency-monitor/configuration-change-event.png)


## <a name="log-analytics-records"></a>Registros de log de análise
Dados de inventário de computador e o processo do ADM estão disponíveis para [pesquisa](../log-analytics/log-analytics-log-searches.md) no Log de análise.  Isso pode ser aplicado a cenários, incluindo o planejamento de migração, análise de capacidade, descoberta e solução de problemas de desempenho ad-hoc. 

Um registro é gerado por hora para cada computador exclusivo e processo além dos registros quando gerado que processo ou computador for iniciado ou estiver em-hospedados para ADM.  Esses registros têm as propriedades nas tabelas a seguir. 

Existem geradas internamente propriedades que você pode usar para identificar computadores e processos exclusivos:

- PersistentKey_s é exclusivamente definido pela configuração do processo, por exemplo, linha de comando e o usuário ID.  Ele é exclusivo para um determinado computador, mas pode ser repetido entre computadores.
- ProcessId_s e ComputerId_s são globalmente exclusivo no modelo ADM.



### <a name="admcomputercl-records"></a>Registros de AdmComputer_CL
Registros com um tipo de **AdmComputer_CL** ter dados de inventário de servidores com agentes ADM.  Esses registros têm as propriedades da tabela a seguir.  

| Propriedade | Descrição |
|:--|:--|
| Tipo | *AdmComputer_CL* |
| SourceSystem | *OpsManager* |
| ComputerName_s | Nome do computador Windows ou Linux |
| CPUSpeed_d | Velocidade da CPU em MHz |
| DnsNames_s | Lista de todos os nomes DNS para este computador |
| IPv4s_s | Lista de todos os endereços IPv4 em uso por este computador |
| IPv6s_s | Lista de todos os endereços IPv6 em uso por este computador.  (ADM identifica endereços IPv6, mas não descobrir dependências de IPv6.) |
| Is64Bit_b | True ou false com base no tipo de sistema operacional |
| MachineId_s | Um GUID interno, exclusiva entre um espaço de trabalho do OMS  |
| OperatingSystemFamily_s | Windows ou Linux |
| OperatingSystemVersion_s | Longa cadeia de caracteres de versão do sistema operacional |
| TimeGenerated | Data e hora em que o registro foi criado. |
| TotalCPUs_d | Número de cores de CPU |
| TotalPhysicalMemory_d | Capacidade de memória em MB |
| VirtualMachine_b | True ou false com base no sistema operacional seja uma convidada |
| VirtualMachineID_g | ID de máquina virtual do Hyper-V |
| VirtualMachineName_g | Nome da máquina virtual do Hyper-V |
| VirtualMachineType_s | Hyper-v, Vmware, Xen, Kvm, Ldom, Lpar, Virtualpc |


### <a name="admprocesscl-type-records"></a>Registros de tipo de AdmProcess_CL 
Registros com um tipo de **AdmProcess_CL** tem dados de inventário de processos conectados TCP em servidores com agentes ADM.  Esses registros têm as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| Tipo | *AdmProcess_CL* |
| SourceSystem | *OpsManager* |
| CommandLine_s | Linha de comando completa do processo |
| CompanyName_s | Nome da empresa (a partir do Windows PE ou Linux RPM) |
| Description_s | Descrição de processo longo (do Windows PE ou Linux RPM) |
| FileVersion_s | Versão do arquivo executável (a partir do Windows PE, somente Windows) |
| FirstPid_d | ID do processo do sistema operacional |
| InternalName_s | Nome do arquivo executável interno (a partir do Windows PE, somente Windows) |
| MachineId_s | GUID interno exclusiva entre um espaço de trabalho do OMS  |
| Name_s | O nome executável do processo |
| Path_s | Caminho de sistema de arquivos do executável processo |
| PersistentKey_s | GUID interno exclusivo neste computador |
| PoolId_d | Identificação interna para agregar processos com base em linhas de comando semelhantes. |
| ProcessId_s | GUID interno exclusiva entre um espaço de trabalho do OMS  |
| ProductName_s | Cadeia de caracteres de nome de produto (a partir do Windows PE ou Linux RPM) |
| ProductVersion_s | Cadeia de caracteres de versão de produto (a partir do Windows PE ou Linux RPM) |
| StartTime_t | Hora de início de processo no relógio do computador local |
| TimeGenerated | Data e hora em que o registro foi criado. |
| UserDomain_s | Domínio do proprietário do processo (somente Windows) |
| UserName_s | Nome do proprietário do processo (somente Windows) |
| WorkingDirectory_s | Diretório de trabalho de processo |


## <a name="sample-log-searches"></a>Pesquisas de log de amostra

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Liste a capacidade de memória física de todos os computadores gerenciados. 
Tipo = AdmComputer_CL | Selecione TotalPhysicalMemory_d, ComputerName_s | Dedup ComputerName_s

![Exemplo de consulta ADM](media/operations-management-suite-application-dependency-monitor/adm-example-01.png)

### <a name="list-computer-name-dns-ip-and-os-version"></a>Nome de computador da lista, versão DNS, IP e sistema operacional.
Tipo = AdmComputer_CL | Selecione ComputerName_s, OperatingSystemVersion_s, DnsNames_s, IPv4s_s | dedup ComputerName_s

![Exemplo de consulta ADM](media/operations-management-suite-application-dependency-monitor/adm-example-02.png)

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Encontrar todos os processos com "sql" na linha de comando
Tipo = AdmProcess_CL CommandLine_s = \*sql\* | dedup ProcessId_s

![Exemplo de consulta ADM](media/operations-management-suite-application-dependency-monitor/adm-example-03.png)

### <a name="after-viewing-event-data-for-given-process-use-its-machine-id-to-retrieve-the-computers-name"></a>Após exibir dados de evento para determinada processo, usar sua ID de máquina para recuperar o nome do computador
Tipo = "m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b" AdmComputer_CL | ComputerName_s distintas

![Exemplo de consulta ADM](media/operations-management-suite-application-dependency-monitor/adm-example-04.png)

### <a name="list-all-computers-running-sql"></a>Lista todos os computadores que executam o SQL
Tipo = AdmComputer_CL MachineId_s em {tipo = AdmProcess_CL \*sql\* | MachineId_s distintas} | ComputerName_s distintas

![Exemplo de consulta ADM](media/operations-management-suite-application-dependency-monitor/adm-example-05.png)

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>Lista de todas as versões de produto exclusiva de ondulação em meu data center
Tipo = AdmProcess_CL Name_s = ondulação | ProductVersion_s distintas

![Exemplo de consulta ADM](media/operations-management-suite-application-dependency-monitor/adm-example-06.png)

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Criar um grupo de computador de todos os computadores que executam CentOS

![Exemplo de consulta ADM](media/operations-management-suite-application-dependency-monitor/adm-example-07.png)



## <a name="diagnostic-and-usage-data"></a>Dados de uso e de diagnóstico
A Microsoft coleta automaticamente dados de uso e desempenho por meio de seu uso do serviço Monitor de dependência de aplicativos. A Microsoft usa esses dados para fornecer e melhorar a qualidade, a segurança e a integridade do serviço Monitor de dependência de aplicativos. Dados incluem informações sobre a configuração de seu software como o sistema operacional e versão e endereço IP, nome DNS e nome de estação de trabalho para fornecer recursos de solução de problemas precisos e eficientes. Não coletamos nomes, endereços ou outras informações de contato.

Para obter mais informações sobre o uso e coleta de dados, consulte a [Política de privacidade do Microsoft Online Services](hhttps://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como [efetuar pesquisas](../log-analytics/log-analytics-log-searches.md] in Log Analytics to retrieve data collected by Application Dependency Monitor.)
