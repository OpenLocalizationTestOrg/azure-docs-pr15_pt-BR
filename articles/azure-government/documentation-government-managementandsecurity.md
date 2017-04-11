<properties
    pageTitle="Documentação do governo Azure | Microsoft Azure"
    description="Isso fornece uma comparação de recursos e orientações sobre como desenvolver aplicativos para o governo do Azure"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="scooxl"
    manager="zakramer"
    editor=""/>
<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/25/2016"
    ms.author="scooxl"/>
#  <a name="azure-government-management-and-security"></a>Segurança e gerenciamento de governo azure

## <a name="automation"></a>Automação

Automação estará disponível no governo do Azure.

### <a name="variations"></a>Variações

Os seguintes recursos de automação não estão disponíveis atualmente no Azure governamentais.

+ Criação de uma credencial de princípio de serviço para autenticação

Para obter mais informações, consulte a [documentação de público de automação](../automation/automation-intro.md).


##  <a name="key-vault"></a>Chave cofre
Para obter detalhes sobre esse serviço e como usá-lo, consulte o <a href="https://azure.microsoft.com/documentation/services/key-vault">documentação pública do Azure chave cofre.</a>
### <a name="data-considerations"></a>Considerações de dados
As informações a seguir identificam o limite de governo do Azure para Azure Cofre de chave:

| Dados regulamentadas/controlados permitidos | Dados regulamentadas/controlados não permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos os dados criptografados com uma chave de Cofre de chave do Azure podem conter dados regulamentadas/controlados. | Metadados de chave cofre Azure não é permitido para conter dados de exportação controlada. Esses metadados incluem todos os dados de configuração inseridos quando criar e manter seu Cofre de chave.  Não digite dados regulamentadas/controlados nos seguintes campos: nomes de grupo de recursos, nomes de chave cofre, nome da assinatura |

Chave cofre estará disponível no governo do Azure. Como público, há sem extensão, chave cofre só está disponível através do PowerShell e CLI.
## <a name="log-analytics"></a>Análise de log
Análise de log estará disponível no governo do Azure. 

### <a name="variations"></a>Variações

Os seguintes recursos de análise de Log e soluções não estão disponíveis atualmente no Azure governamentais. Esta lista é atualizada quando o status dos recursos de alterações de soluções.

+ Soluções que estão na visualização Azure público, incluindo:
  - Solução de monitoramento de rede
  - Monitoramento de dependência de aplicativos
  - Solução do Office 365
  - Solução de análise de atualização do Windows 10
  - Obtenção de informações de aplicativo
  - Solução de análise de rede Azure
  - Análise de automação Azure
  - Análise de chave cofre
+ Soluções e recursos que exigem atualizações de software de locais, incluindo
  - Integração com sistema central Operations Manager 2016 (há suporte para versões anteriores do Operations Manager)
  - Grupos de computadores do Gerenciador de configuração do System Center
  - Solução de Hub de superfície
+ Recursos que estão na visualização Azure público, incluindo
  - Exportação de dados para PowerBI
+ Métricas Azure e diagnóstico do Azure
+ Aplicativos móveis OMS

As URLs para análise de Log são diferentes no Azure governo:

| Azure público | Governo Azure | Anotações |
|--------------|------------------|-------|
| MMS.microsoft.com | OMS.microsoft.US | Portal de análise de log |
| *workspaceId*. ods.opinsights.azure.com | *workspaceId*. ods.opinsights.azure.us | [API de Coletores de dados](../log-analytics/log-analytics-data-collector-api.md) 
| \*. ods.opinsights.azure.com | \*. ods.opinsights.azure.us | Comunicação do agente - [Configurando o firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. oms.opinsights.azure.com | \*. oms.opinsights.azure.us | Comunicação do agente - [Configurando o firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Comunicação do agente - [Configurando o firewall](../log-analytics/log-analytics-proxy-firewall.md) |


Os seguintes recursos de análise de Log têm um comportamento diferente no Azure governo:

+ O Windows agent deve ser baixado a partir do [portal de análise de Log](https://oms.microsoft.us) do governo do Azure.
+ Para conectar seu servidor de gerenciamento do System Center Operations Manager a análise de Log, você precisa baixar e importar pacotes de gerenciamento atualizado.
  1. Baixar e salvar os [pacotes de gerenciamento de atualização](http://go.microsoft.com/fwlink/?LinkId=828749)
  2. Descompacte o arquivo que você baixou
  3. Importe os pacotes de gerenciamento para o Operations Manager. Para obter informações sobre como importar um pacote de gerenciamento de um disco, consulte o tópico [como importar um pacote de gerenciamento do Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) no site do Microsoft TechNet.
  4. Para conectar o Operations Manager a análise de Log, siga as etapas em [Conectar o Operations Manager para análise de Log](../log-analytics/log-analytics-om-agents.md) 



### <a name="frequently-asked-questions"></a>Perguntas frequentes

+ Pode migrar dados de análise de Log no governo público do Azure para Azure?
  - Não. Não é possível mover dados ou seu espaço de trabalho do governo público do Azure para Azure.
+ Pode alternar entre públicos Azure e Azure governo espaços de trabalho a partir do portal de análise de Log de OMS?
  - Não. Os portais públicas do Azure e governo do Azure são separados e não compartilham informações. 

Para obter mais informações, consulte a [documentação de público de análise de Log](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Próximas etapas

Para informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure governamentais.</a>
 
