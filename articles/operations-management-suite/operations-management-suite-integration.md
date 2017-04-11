<properties
   pageTitle="Integração com o pacote de gerenciamento de operações (OMS) | Microsoft Azure"
   description="Além de usar os recursos padrão do OMS, você pode integrar outros aplicativos de gerenciamento e serviços para fornecer um ambiente de gerenciamento híbrido, forneça cenários de gerenciamento personalizado exclusivos para o seu ambiente ou fornecer um gerenciamento personalizado experiência para seus clientes.  Este artigo fornece uma visão geral das diferentes opções de integração com OMS e links para artigos fornecendo informações técnicas detalhadas."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="bwren" />

# <a name="integrating-with-operations-management-suite-oms"></a>Integração com o pacote de gerenciamento de operações (OMS)

Pacote de gerenciamento de operações é na nuvem solução de gerenciamento da Microsoft que ajuda você a gerenciar e proteger seu local e infraestrutura de nuvem.  Além de usar os recursos padrão do OMS, você pode integrar outros aplicativos de gerenciamento e serviços para fornecer um ambiente de gerenciamento híbrido, forneça cenários de gerenciamento personalizado exclusivos para o seu ambiente ou fornecer um gerenciamento personalizado experiência para seus clientes.  Este artigo fornece uma visão geral das diferentes opções de integração com serviços OMS e links para artigos fornecendo informações técnicas detalhadas. 



## <a name="log-analytics"></a>Análise de log
Dados de gerenciamento coletados pela análise de Log são armazenados em um repositório que está hospedado no Azure.  Todos os dados armazenados no repositório está disponível em pesquisas de log que fornecem análise rápida em muito grandes quantidades de dados.  Seus requisitos de integração podem ser para preencher o repositório com novos dados, tornando-o disponível para análise, ou para extrair dados no repositório para fornecer uma nova visualização ou para integrar com outra ferramenta de gerenciamento.

Cada parte dos dados no repositório é armazenado como um registro.  Quando você preenche o repositório, você deve fornecer aos usuários o tipo de registro que sua solução utiliza e uma descrição de suas propriedades.  Ao recuperar dados, você precisará dessas informações sobre os dados que você está trabalhando.

![Preenchendo o repositório OMS](media/operations-management-suite-integration/repository.png)


### <a name="populate-the-log-analytics-repository"></a>Preencha o repositório de análise de Log
Há vários métodos para popular o repositório OMS.  O método que você usa dependerá de fatores como onde se encontra os dados de origem, o formato dos dados e que você precisa de suporte de clientes.  Depois que os dados são armazenados no repositório, ele não faz diferença como ele foi coletado.

As seções a seguir descrevem as diferentes opções para preencher o repositório OMS.

#### <a name="connected-sources-and-data-sources"></a>Fontes de dados e fontes conectadas 
Fontes conectadas são os locais onde os dados podem ser recuperados para o repositório OMS.  Fontes de dados e soluções executados em fontes conectadas e definem os dados específicos que são coletados.  Se seu aplicativo grava dados em uma destas fontes de dados, você pode coletar-Configurando a fonte de dados.  Por exemplo, se seu aplicativo cria Syslog eventos, em seguida, eles podem ser coletados pela fonte de dados Syslog em um agente de Linux.

- [Fontes de dados em análise de Log](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Soluções

Soluções estendem os recursos do OMS.  Uma solução pode coletar dados de origem conectada ou ela pode executar análise registros já coletados no repositório.  Cada solução fornecida pela Microsoft tem um artigo individual que fornece os detalhes sobre os dados que ele coleta.

- [Soluções de análise de Log](../log-analytics/log-analytics-add-solutions.md)



#### <a name="http-data-collector-api"></a>API de Coletores de dados de HTTP

A API de Coletores de dados do Log de análise de HTTP é uma API REST que permite que você adicione dados JSON para o repositório de análise de Log.  Você pode aproveitar essa API quando você tiver um aplicativo que não oferece dados por meio de uma das outras fontes de dados ou soluções.  Ele pode ser usado para preencher o repositório de qualquer cliente que pode chamar a API e não dependem o cronograma de conjunto de qualquer fonte de dados ou a solução.

- [API de Coletores de dados de HTTP de análise do log](../log-analytics/log-analytics-data-collector-api.md)


### <a name="retrieve-data-from-the-log-analytics-repository"></a>Recuperar dados a partir do repositório de análise de Log

Há vários métodos para recuperar dados a partir do repositório OMS.  Você pode desejar que os usuários para recuperar dados usando o console OMS e forneça a ele com diferentes tipos de visualizações e análise.  Você também pode recuperar os dados de um processo externo como outra solução de gerenciamento.

#### <a name="log-searches"></a>Pesquisas de log

Todos os dados armazenados no repositório OMS está disponível por meio de pesquisas de log.  Os usuários podem executar sua próprias análise ad hoc no console do OMS ou criar um painel com uma visualização para uma pesquisa de log específico.  Soluções pode conter exibições personalizadas com visualizações com base em pesquisas predefinidas.  Você pode usar a API de pesquisa de Log para acessar dados no repositório OMS de uma ferramenta de gerenciamento ou aplicativo externa.  

- [Pesquisas de log no Log de análise](../log-analytics/log-analytics-log-searches.md)
- [Pesquisa de log de análise de log API REST](../log-analytics/log-analytics-log-search-api.md)
- [Cmdlets de análise de log](https://msdn.microsoft.com/library/mt188224.aspx)



#### <a name="custom-views"></a>Exibições personalizadas 
O Designer de modo de exibição permite que você criar exibições personalizadas no console OMS que fornecem aos usuários com visualização e análise dos dados em sua solução.  Cada modo de exibição inclui um bloco que é exibido na página principal do console e qualquer número de partes de visualização que são baseados em pesquisas de log que você definir.
  
- [Designer de modo de exibição de análise de log](../log-analytics/log-analytics-view-designer.md)


#### <a name="power-bi"></a>Power BI

Análise de log automaticamente pode exportar dados do repositório OMS no Power BI para que você possa aproveitar seus visualizações e ferramentas de análise.  Ele executa esta exportação em um cronograma para que os dados são mantidos atualizados. 

- [Exportar dados de análise de Log para o Power BI](../log-analytics/log-analytics-powerbi.md)




## <a name="automation"></a>Automação

OMS pode automatizar processos reagir a dados coletados ou executar outras funções de gerenciamento.  Pode coletar dados de seu aplicativo e inseri-lo no repositório OMS, ou você pode automatizar a correção de um problema conhecido em resposta a dados encontrados no repositório. 

![Automação](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbooks

Runbooks no Azure automação executar scripts do PowerShell e fluxos de trabalho na nuvem Azure.  Você pode usá-los para gerenciar recursos no Azure ou quaisquer outros recursos que podem ser acessados da nuvem.  Runbooks também pode ser executado em um data center local usando o operador de Runbook híbrido.  Você pode iniciar uma runbook do portal do Azure ou de processos externos usando vários métodos como PowerShell ou a API de automação.

- [Iniciando um runbook no Azure automação](../automation/automation-starting-a-runbook.md)
- [Cmdlets de automação Azure](https://msdn.microsoft.com/library/dn690262.aspx)
- [API REST de automação](https://msdn.microsoft.com/library/mt662285.aspx)
- [Automação .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Alertas

Regras de alerta executar automaticamente pesquisas de log de acordo com uma agenda.  Se os resultados correspondem aos critérios determinados o alerta resultante pode iniciar uma runbook no Azure automação ou chamar uma webhook que pode iniciar um processo externo.  Ambos dessas respostas podem incluir detalhes de alerta, inclusive os dados retornados na pesquisa de log.

- [Alertas no Log de análise](../log-analytics/log-analytics-alerts.md)
- [API de alerta de análise de log](../log-analytics/log-analytics-api-alerts.md)


## <a name="backup-and-site-recovery"></a>Backup e recuperação de Site

Azure Backup e recuperação de Site fornecem serviços para proteger seus dados corporativos e garantir a disponibilidade dos servidores e aplicativos.  Você pode aproveitar esses serviços para realizar situações como fornecendo serviços de backup para seu aplicativo ou iniciando um failover de uma máquina virtual.

- [Cmdlets do Backup do Azure](https://msdn.microsoft.com/library/mt619253.aspx)
- [Recuperação de Site Azure API REST](https://msdn.microsoft.com/library/azure/mt750497.aspx)
- [Cmdlets de recuperação de sites Azure](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>Soluções personalizadas

Você pode encapsular lógica de integração em uma solução personalizada para executar no seu espaço de trabalho ou no espaço de trabalho de um cliente.  Sua solução pode incluir qualquer um dos métodos integração neste artigo, além de outros recursos para fornecer um cenário de gerenciamento completo.  Os recursos na solução são compactados, para que quando a solução for removida, todos os recursos que ela criou são removidos do espaço de trabalho do OMS e assinatura do Azure.

Por exemplo, sua solução pode incluir um runbook de automação para coletar e processar dados e, em seguida, preencha o repositório de análise de Log usando a API de coletor de dados de HTTP.  Você também pode incluir um modo de exibição personalizado que apresenta e analisa os dados coletados.  

- Criando soluções personalizadas (em breve)    

## <a name="next-steps"></a>Próximas etapas
- Referência do [OMS SDK](operations-management-suite-sdk.md) para obter informações técnicas sobre como automatizar os serviços OMS.  
