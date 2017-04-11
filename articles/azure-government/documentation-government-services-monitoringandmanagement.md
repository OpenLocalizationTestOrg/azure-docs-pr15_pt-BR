<properties
    pageTitle="Documentação do governo Azure | Microsoft Azure"
    description="Isso fornece uma comparação de recursos e orientações sobre como desenvolver aplicativos para o governo do Azure."
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/25/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-monitoring-and-management"></a>Azure governo monitoramento e gerenciamento

Este artigo descreve o monitoramento e gerenciamento de serviços variações e considerações para o ambiente do governo do Azure.

## <a name="automation"></a>Automação

Automação estará disponível no governo do Azure.

### <a name="variations"></a>Variações

Os seguintes recursos de automação não estão disponíveis atualmente no Azure governamentais.

+ Criação de uma credencial de princípio de serviço para autenticação

Para obter mais informações, consulte a [documentação de público de automação](../automation/automation-intro.md).

## <a name="log-analytics"></a>Análise de log

Análise de log estará disponível no governo do Azure.

### <a name="variations"></a>Variações

Os seguintes recursos de análise de Log e soluções não estão disponíveis atualmente no Azure governamentais.

+ Soluções que estão na visualização no Microsoft Azure, incluindo:
  - Solução de monitoramento de rede
  - Solução de monitoramento de dependência de aplicativos
  - Solução do Office 365
  - Solução de análise de atualização do Windows 10
  - Solução de obtenção de informações de aplicativo
  - Solução de análise de rede Azure
  - Solução de análise de automação Azure
  - Solução de análise de Cofre de chave
+ Soluções e recursos que exigem atualizações de software de locais, incluindo:
  - Integração com sistema central Operations Manager 2016 (há suporte para versões anteriores do Operations Manager)
  - Grupos de computadores do Gerenciador de configuração do System Center
  - Solução de Hub de superfície
+ Recursos que estão na visualização Azure público, incluindo:
  - Exportação de dados para Power BI
+ Métricas Azure e diagnóstico do Azure
+ Aplicativos móveis do pacote de gerenciamento de operações

As URLs para análise de Log são diferentes no Azure governo:

| Azure público | Governo Azure | Anotações |
|--------------|------------------|-------|
| MMS.microsoft.com | OMS.microsoft.US | Portal de análise de log |
| *workspaceId*. ods.opinsights.azure.com | *workspaceId*. ods.opinsights.azure.us | [API de Coletores de dados](../log-analytics/log-analytics-data-collector-api.md) 
| \*. ods.opinsights.azure.com | \*. ods.opinsights.azure.us | Comunicação do agente - [Configurando o firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. oms.opinsights.azure.com | \*. oms.opinsights.azure.us | Comunicação do agente - [Configurando o firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Comunicação do agente - [Configurando o firewall](../log-analytics/log-analytics-proxy-firewall.md) |


Os seguintes recursos de análise de Log se comportam de forma diferente no Azure governo:

+ O Windows Agent devem ser baixado a partir do [portal de análise de Log](https://oms.microsoft.us) do governo do Azure.
+ Para conectar seu servidor de gerenciamento do System Center Operations Manager a análise de Log, você precisa baixar e importar pacotes de gerenciamento atualizado.
  1. Baixar e salve os [pacotes de gerenciamento de atualização](http://go.microsoft.com/fwlink/?LinkId=828749).
  2. Descompacte o arquivo que você baixou.
  3. Importe os pacotes de gerenciamento para o Operations Manager. Para obter informações sobre como importar um pacote de gerenciamento de um disco, veja [como importar um pacote de gerenciamento do Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) no site do Microsoft TechNet.
  4. Para conectar o Operations Manager a análise de Log, siga as etapas em [Conectar o Operations Manager para análise de Log](../log-analytics/log-analytics-om-agents.md).


## <a name="frequently-asked-questions"></a>Perguntas frequentes

+ Posso migrar dados de análise de Log no Microsoft Azure ao Azure governo?
  - Não. Não é possível mover dados ou seu espaço de trabalho do Microsoft Azure para governo do Azure.
+ Posso alternar entre Microsoft Azure e governo do Azure espaços de trabalho a partir do portal de análise de Log de pacote de gerenciamento de operações?
  - Não. Os portais para Microsoft Azure e o Azure governamentais são separados e não compartilham informações.

Para obter mais informações, consulte a [documentação de público de análise de Log](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Próximas etapas

Para informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure governamentais.</a>
