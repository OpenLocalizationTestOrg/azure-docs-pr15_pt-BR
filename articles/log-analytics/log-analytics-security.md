<properties
    pageTitle="Segurança de dados de análise de logon | Microsoft Azure"
    description="Saiba mais sobre como a análise de Log protege a sua privacidade e protege seus dados."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="banders"/>

# <a name="log-analytics-data-security"></a>Log de segurança de dados de análise

A Microsoft está comprometida em proteger sua privacidade e proteger seus dados, ao entregar software e serviços que ajudam você a gerenciar a infraestrutura de sua organização. Sabemos que, quando você confiar seus dados para outras pessoas, confiança requer rigorosos de segurança. Microsoft adere às diretrizes estritas de segurança e conformidade — da codificação à operação de um serviço.

Protegendo e proteção de dados é uma prioridade da Microsoft. Entre em contato conosco qualquer perguntas, sugestões ou problemas sobre qualquer uma das seguintes informações, incluindo nossas políticas de segurança em [Opções de suporte do Azure](http://azure.microsoft.com/support/options/).

Este artigo explica como dados coletados, processados e protegidos pela análise de Log no pacote de gerenciamento de operações (OMS). Você pode usar agentes para se conectar ao serviço da web, use o System Center Operations Manager para coletar dados operacionais ou recuperar dados de diagnóstico do Azure para ser usado pela análise de Log. Os dados coletados são enviados pela Internet usando a autenticação baseada em certificado SSL 3 para o serviço de análise de Log, que está hospedado no Microsoft Azure. Dados são compactados pelo agente antes de ser enviado.

O serviço de análise de Log gerencia seus dados baseado em nuvem com segurança usando os seguintes métodos:

- diferenciação de dados
- retenção de dados
- segurança física
- gerenciamento de incidente
- conformidade
- certificações de padrões de segurança


## <a name="data-segregation"></a>Diferenciação de dados

Dados do cliente são mantidos logicamente separados em cada componente em todo o serviço OMS. Todos os dados marcado por organização. Essa marcação persiste em todo o ciclo de vida de dados e ela é aplicada em cada camada do serviço. Cada cliente tem um blob do Microsoft Azure dedicado que armazena os dados de longo prazo

## <a name="data-retention"></a>Retenção de dados

Dados de pesquisa de log indexados são armazenados e mantidos de acordo com o seu plano de preços. Para obter mais informações, consulte [Preços de análise de Log](https://azure.microsoft.com/pricing/details/log-analytics/).

Microsoft exclui dados de cliente 30 dias após o espaço de trabalho do OMS estiver fechado. Microsoft também exclui a conta de armazenamento do Azure onde os dados residem. Quando dados do cliente são removidos, sem unidades físicas são destruídas.

A tabela a seguir lista algumas das soluções disponíveis OMS e exemplos dos tipos de dados coletados.

| **Solução** | **Tipos de dados** |
| --- | --- |
| Avaliação de configuração | Dados de configuração, metadados e dados de estado |
| Planejamento da capacidade | Metadados e dados de desempenho |
| Antimalware | Metadados e dados de configuração |
| Avaliação de atualização do sistema | Dados de estado e metadados |
| Gerenciamento de registros | Logs de eventos de definidas pelo usuário, Logs de eventos do Windows e/ou Logs do IIS |
| Controle de alterações | Inventário de software e os metadados do serviço do Windows |
| SQL e avaliação do Active Directory | Exibir os resultados de dados WMI, dados do registro, dados de desempenho e gerenciamento dinâmico do SQL Server |

A tabela a seguir mostra exemplos de tipos de dados:

| **Tipo de dados** | **Campos** |
| --- | --- |
| Alerta | Alertar nome, descrição do alerta, BaseManagedEntityId, identificação do problema, IsMonitorAlert, RuleId, ResolutionState, prioridade, gravidade, categoria, proprietário, ResolvedBy, TimeRaised, TimeAdded, LastModified, última modificação feita por, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configuração | CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, Valoratual, ChangeDate |
| Evento | Iddoevento, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, número, categoria, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Observação:** Quando fizer logon eventos com campos personalizados no log de eventos do Windows, OMS coleta-los. |
| Metadados | BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, endereço IP, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, endereço IP, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Desempenho | ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Estado | StateChangeEventId, StateId, NewHealthState, OldHealthState, contexto, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Segurança física

A análise de Log no serviço OMS é operávamos pelo pessoal da Microsoft e todas as atividades são registradas e podem ser auditadas. O serviço é executado completamente no Azure e cumpre com os critérios de engenharia comum Azure. Você pode exibir detalhes sobre a segurança física do Azure ativos na página 18 da [Visão geral de segurança do Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Direitos de acesso físico proteger áreas são alterados em um dia útil para qualquer pessoa que não tem mais responsabilidade para o serviço OMS, incluindo transferência e término. Você pode ler mais sobre a infraestrutura física global que usamos em [Data centers Microsoft](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Gerenciamento de incidente

OMS tem um processo de gerenciamento de incidente que todos os serviços Microsoft adicional. Para resumir, podemos:

- Usar um modelo de responsabilidade compartilhada onde uma parte da responsabilidade de segurança pertence Microsoft enquanto parte pertence ao cliente
- Gerenciar ocorrências de segurança do Azure
  - Detectar um incidente na indicação primeiro, para iniciar uma investigação
  - Avalie o impacto e gravidade de um incidente por um membro da equipe de resposta a incidente na chamada. Com base em evidências, a avaliação pode ou não pode resultar em ainda mais escalação para a equipe de resposta de segurança.
  - Diagnosticar um incidente de especialistas de resposta de segurança para conduzir a investigação técnica ou processo judicial, identificar estratégias de retenção, atenuação e solução alternativa. Se a equipe de segurança acredita que dados do cliente podem ter ficam expostos a um indivíduo ilegal ou não autorizado, execução paralela do processo de notificação do incidente de cliente começa em paralelo.  
  - Coloque e recuperar do incidente. A equipe de resposta a incidente cria um plano de recuperação para minimizar o problema. Etapas de redução de crise como colocar em quarentena sistemas afetados podem ocorrer imediatamente e em paralelo com diagnóstico. Mais reduções de termos podem ser planejadas que ocorrem após o risco imediato passou.  
  - Feche o incidente e realizar um post-mortem. A equipe de resposta a incidente cria um post-mortem que descreve os detalhes do incidente, com a intenção de revisar políticas, procedimentos e processos para evitar a recorrência do evento.
- Notificar os clientes de ocorrências de segurança
  - Determinar o escopo de clientes afetados e para fornecer a qualquer pessoa que é afetada detalhadas um aviso de possível
  - Crie um aviso para fornecer aos clientes detalhadas informações suficientes para que eles podem realizar uma investigação em seu final e atender aos compromissos que fizeram de seus usuários finais enquanto não indevidamente atrasar o processo de notificação.
  - Confirme e declarar o incidente, conforme necessário.
  - Notifica clientes com uma notificação de incidente sem atraso razoável e de acordo com qualquer compromisso jurídico ou contratual. Notificação de ocorrências de segurança sejam entregues em um ou mais dos administradores de um cliente por qualquer meio que Microsoft seleciona, incluindo via email.
- Conduzir treinamento e preparação de equipe
  - Equipe Microsoft é necessários para concluir a segurança e treinamento de percepção, que ajuda a identificar e relatar problemas de segurança suspeito.  
  - Operadores trabalhando no serviço Microsoft Azure têm obrigações de treinamento de adição ao redor de seu acesso a dados do cliente de hospedagem de sistemas confidenciais.
  - Equipe de resposta de segurança do Microsoft recebe treinamento especializado para suas funções


Em caso de perda de dados de qualquer cliente, podemos notificar cada cliente dentro de um dia. No entanto, nunca ocorreu perda de dados do cliente com o OMS. Além disso, vamos manter cópias dos dados que foi criados e é distribuído geograficamente.

Para obter mais informações sobre como a Microsoft responde a ocorrências de segurança, consulte [Resposta de segurança do Microsoft Azure na nuvem](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf).

## <a name="compliance"></a>Conformidade

Programa de segurança e de gestão de informações de OMS software desenvolvimento e serviço da equipe suporta suas necessidades comerciais e adere às leis e regulamentos conforme descrito na [Central de confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/) e [Conformidade de Central de confiabilidade da Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx). Como OMS estabelece requisitos de segurança, identifica os controles de segurança, gerencia e monitora riscos também são descritas lá. Anualmente, podemos conduzir uma análise de políticas, padrões, procedimentos e diretrizes.

Cada membro da equipe desenvolvimento OMS recebe treinamento de segurança de aplicativo formal. Internamente, usamos um sistema de controle de versão para desenvolvimento de software. Cada projeto de software é protegido pelo sistema de controle de versão.

A Microsoft tem uma equipe de segurança e conformidade que supervisiona e avalia todos os serviços no Microsoft. Gerentes de segurança de informações constituem a equipe e não são associadas com os departamentos de engenharia que desenvolvem OMS. Os gerentes de segurança têm sua próprias cadeia de gerenciamento e conduzir avaliações independentes de produtos e serviços para garantir a segurança e conformidade.

Diretoria da Microsoft é notificada por e relatório anual sobre todos a de segurança das informações programas da Microsoft.

A equipe de serviço e de desenvolvimento de software do OMS está trabalhando ativamente com as equipes Microsoft Legal e conformidade e outros parceiros de setor adquirir uma variedade de certificações.

## <a name="security-standards-certifications"></a>Certificações de padrões de segurança

Análise de log no OMS atualmente atender os padrões de segurança a seguir:

- [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm) e [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498) compatíveis
- Pagamento cartão (PCI compatível) dados segurança padrão do setor (PCI DSS) pelo Conselho de padrões de segurança PCI.
- [Tipo de serviço organização controles (SOC) 1 1 e 1 de tipo de 2 SOC](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) compatíveis
- Critérios de engenharia comum do Windows
- Certificação de computação confiável da Microsoft
- Como um serviço Azure, os componentes que utiliza OMS cumprir requisitos de conformidade Azure. Você pode ler mais em [Conformidade de Central de confiabilidade da Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx).


## <a name="cloud-computing-security-data-flow"></a>Fluxo de dados de segurança de computação em nuvem
O diagrama a seguir mostra uma arquitetura de segurança de nuvem como o fluxo de informações de sua empresa e como ele é protegido como está move para o serviço de análise de Log, basicamente visto por você no portal do OMS. Mais informações sobre cada etapa seguem o diagrama.

![Imagem de coleta de dados OMS e segurança](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. inscrever-se para a análise de Log e coletar dados

Para a sua organização enviar dados para a análise de Log, você configurar agentes de Windows, agentes em execução no Azure máquinas virtuais ou agentes de OMS para Linux. Se você usar agentes do Operations Manager, você vai usar um Assistente de configuração no console de operações configurá-los. Usuários (que podem ser você, outros usuários individuais ou um grupo de pessoas) criar uma ou mais contas OMS (espaços de trabalho OMS) e registrar agentes usando uma das contas a seguir:


- [ID organizacional](../active-directory/sign-up-organization.md)

- [Conta da Microsoft, Outlook, Office Live, MSN](http://www.microsoft.com/account/default.aspx)

Um espaço de trabalho do OMS é onde dados são coletados, agregados, analisados e apresentados. Um espaço de trabalho é usado principalmente como um meio de dados de partição, e cada espaço de trabalho é exclusivo. Por exemplo, talvez você queira ter seus dados de produção gerenciados com um espaço de trabalho do OMS e seus dados de teste gerenciados com outro espaço de trabalho. Espaços de trabalho também ajudam um administrador controle acesso do usuário aos dados. Cada espaço de trabalho pode ter várias contas de usuário associadas a ela, e cada conta de usuário pode acessar o espaço de trabalho OMS vários. Criar espaços de trabalho com base na região do data center. Cada espaço de trabalho é replicado para outros dos data centers na região, principalmente para disponibilidade do serviço OMS.

Para o Operations Manager, quando concluir o Assistente de configuração, cada grupo de gerenciamento do Operations Manager estabelece uma conexão com o serviço de análise de Log. Você, em seguida, use o Assistente para adicionar computadores para escolher quais computadores no grupo de gerenciamento têm permissão para enviar dados para o serviço. Para outros tipos de agente, cada se conecta com segurança para o serviço de OMS.

Todas as comunicações entre sistemas conectados e o serviço de análise de Log está criptografada.  O protocolo TLS (HTTPS) é usado para criptografia.  O processo SDL da Microsoft é seguido para garantir que a análise de Log está atualizada com os avanços mais recentes em protocolos de criptografia.

Cada tipo de agente de coleta de dados para análise de Log. O tipo de dados coletados depende de tipos de soluções usadas. Você pode ver um resumo de coleta de dados em [soluções de adicionar a análise de Log da Galeria de soluções](log-analytics-add-solutions.md). Além disso, as informações mais detalhadas de conjunto estão disponíveis para a maioria das soluções. Uma solução é um pacote de modos de exibição predefinidos, consultas de pesquisa de log, regras de conjunto de dados e lógica de processamento. Somente administradores podem usar a análise de Log para importar uma solução. Após a solução for importada, ele é movido para os servidores de gerenciamento do Operations Manager (se usado) e, em seguida, para quaisquer agentes que você escolheu. Depois disso, os agentes coletam os dados.

## <a name="2-send-data-from-agents"></a>2. enviar dados de agentes

Registre todos os tipos de agente com uma chave de registro e uma conexão segura é estabelecida entre o agente e o serviço de análise de Log usando a autenticação baseada em certificados e SSL com porta 443. OMS usa um armazenamento secreto para gerar e manter chaves. Chaves particulares são giradas cada 90 dias e são armazenadas no Azure e são gerenciadas pelas operações Azure que seguem práticas de regulamentação e conformidade estritas.

Com o Operations Manager, você registrar um espaço de trabalho com o serviço de análise de Log e uma conexão HTTPS segura é estabelecida entre o servidor de gerenciamento do Operations Manager.

Para agentes do Windows em execução no Azure máquinas virtuais, uma chave de armazenamento de somente leitura é usada para ler eventos de diagnóstico em tabelas do Azure.

Se qualquer agente não conseguir se comunicar com o serviço por algum motivo, os dados coletados são armazenados localmente em um cache temporário e o servidor de gerenciamento tenta reenviar os dados de cada 8 minutos por 2 horas. Dados armazenados em cache do agente são protegidos por armazenamento de credenciais do sistema operacional. Se o serviço não é possível processar os dados após 2 horas, os agentes ficarão na fila os dados. Se a fila ficar completa, OMS inicia soltando tipos de dados, começando com dados de desempenho. O limite de fila do agente é uma chave do registro para que você possa modificá-lo, se necessário. Os dados coletados são compactados e enviados para o serviço, ignorando os bancos de dados local, para que ela não adiciona qualquer carga a eles. Depois que os dados coletados são enviados, ele será removido do cache.

Conforme descrito acima, os dados de seus agentes são enviados sobre SSL dos data centers do Microsoft Azure. Opcionalmente, você pode usar a rota expressa para fornecer segurança adicional para os dados. Rota expressa é uma maneira de se conectar diretamente ao Azure da sua rede WAN existente, como um protocolo vários rotular mudança (MPLS) VPN, fornecida por um provedor de serviços de rede. Para obter mais informações, consulte [rota expressa](https://azure.microsoft.com/services/expressroute/).


## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. o serviço de Log Analytics recebe e processa dados

O serviço de análise de Log garante que os dados de entrada são de uma fonte confiável Validando a integridade dos dados com a autenticação do Azure e certificados. Os dados não processados não processados são armazenados como um blob no [Armazenamento do Microsoft Azure](../storage/storage-introduction.md) e não estão criptografados. No entanto, cada blob de armazenamento do Azure tem um conjunto de conjunto exclusivo de teclas que pode ser acessado somente para esse usuário. O tipo de dados que estão armazenados está depende dos tipos de soluções que foram importados e usadas para coletar dados. Em seguida, o serviço de análise de Log processa os dados brutos para o blog do armazenamento do Azure.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. usar a análise de Log para acessar os dados

Você pode entrar para a análise de Log no portal do OMS usando a conta organizacional ou uma conta da Microsoft que você configurou anteriormente. Todo o tráfego entre o portal OMS e a análise de Log no OMS é enviado por um canal HTTPS seguro. Ao usar o portal OMS, uma identificação de sessão é gerada no cliente do usuário (navegador da web) e dados são armazenados em um cache local até que a sessão seja finalizada. Quando encerrado, o cache é excluído. Cookies do lado do cliente, que não contêm informações pessoalmente identificáveis, não são automaticamente removidos. Cookies de sessão são marcados HTTPOnly e são protegidos. Após um período ocioso predeterminado, a sessão de portal de OMS é encerrada.

Usando o portal OMS, você pode exportar dados para um arquivo CSV e você pode acessar dados usando APIs de pesquisa. Exportação CSV está limitada a 50.000 linhas por exportação e dados de API são restritos em 5.000 linhas por pesquisa.

## <a name="next-steps"></a>Próximas etapas

- [Começar a usar a análise de Log](log-analytics-get-started.md) para saber mais sobre a análise de Log e get em funcionamento em minutos.
