<properties
    pageTitle="Log de recursos de análise para provedores de serviços | Microsoft Azure"
    description="Análise de log pode ajudar provedores de serviços gerenciados (MSPs), grandes empresas, fornecedores de software independentes (ISVs) e provedores de serviços de hospedagem gerenciar e monitoram servidores local do cliente ou infraestrutura de nuvem."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="richrund"/>

# <a name="log-analytics-features-for-service-providers"></a>Recursos de análise de log para provedores de serviços

Análise de log pode ajudar provedores de serviços gerenciados (MSPs), grandes empresas, fornecedores de software independentes (ISVs) e provedores de serviço de hospedagem gerenciar e monitorar servidores local do cliente ou infraestrutura de nuvem. 

Grandes empresas compartilham várias semelhanças com provedores de serviço, especialmente quando há uma equipe de TI centralizado que é responsável por gerenciar IT para muitos diferentes unidades de negócios. Para simplificar, este documento usa o termo *provedor de serviço* , mas a mesma funcionalidade também está disponível para empresas e outros clientes.

## <a name="cloud-solution-provider"></a>Provedor de soluções de nuvem

Para parceiros e provedores de serviços que fazem parte do programa do [Provedor de solução da nuvem (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , a análise de Log é um dos serviços do Azure disponíveis em uma assinatura de CSP. 

Para análise de Log, os seguintes recursos estão ativados no *Provedor de solução de nuvem* assinaturas.

Como um *Provedor de soluções de nuvem* , você pode:

+ Crie espaços de trabalho de análise de Log na assinatura de um locatário (cliente).
+ Espaços de trabalho do Access criados por locatários. 
+ Adicionar e remover o acesso do usuário no espaço de trabalho usando o gerenciamento de usuário Azure. Quando no espaço de trabalho de um locatário no portal do OMS o gerenciamento de usuário página sob configurações não está disponível
  - Análise de log não suporta acesso baseado em função ainda - dando um usuário `reader` permissão no portal do Azure permite que eles façam alterações de configuração no portal do OMS

Para efetuar login à assinatura de um locatário, você precisa especificar o identificador de locatário. O identificador de locatário costuma essa última parte do endereço de email usado para entrar.

+ No portal do OMS, adicione `?tenant=contoso.com` na URL para o portal. Por exemplo,`mms.microsoft.com/?tenant=contoso.com`
+ No PowerShell, use o `-Tenant contoso.com` parâmetro ao usar `Add-AzureRmAccount` cmdlet
+ O identificador de locatário é adicionado automaticamente quando você usa o `OMS portal` link do portal do Azure para abrir e faça logon no portal do OMS para o espaço de trabalho selecionado

Como um *cliente* de um provedor de solução de nuvem, você pode:

+ Criar espaços de trabalho de análise de log em uma assinatura de CSP
+ Espaços de trabalho do Access criados pelo CSP
  -  Use o `OMS portal` link do portal do Azure para abrir e faça logon no portal do OMS para o espaço de trabalho selecionado
+ Exibir e usar a página de gerenciamento de usuário em configurações no portal do OMS

>[AZURE.NOTE] As soluções de Backup e recuperação de Site para análise de Log não são capazes de se conectar a um cofre de serviços de recuperação e não podem ser configuradas em uma assinatura de CSP.

## <a name="managing-multiple-customers-using-log-analytics"></a>Gerenciando vários clientes usando a análise de Log 

É recomendável que você criar um espaço de trabalho de análise de Log para cada cliente que você gerenciar. Fornece um espaço de trabalho de análise de Log:

+ Uma localização geográfica para os dados sejam armazenados. 
+ Detalhamento de cobrança 
+ Isolamento de dados 
+ Configuração exclusiva

Criando um espaço de trabalho por cliente, você é capaz de manter dados de cada cliente separados e também controlar o uso de cada cliente.

Mais detalhes sobre quando e por que criar vários espaços de trabalho é descrito na [gerenciar o acesso para efetuar a análise] (log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

Criação e configuração de espaços de trabalho do cliente podem ser automatizadas usando o [PowerShell](log-analytics-powershell-workspace-configuration.md), [modelos do Gerenciador de recursos](log-analytics-template-workspace-configuration.md), ou usar a [API REST](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

O uso de modelos do Gerenciador de recursos de configuração de espaço de trabalho permite que você tenha uma configuração mestre que pode ser usada para criar e configurar espaços de trabalho. Você pode ter certeza de que como espaços de trabalho são criados para os clientes que eles são configurados automaticamente às suas necessidades. Quando você atualizar seus requisitos, o modelo é atualizado e, em seguida, aplicada novamente os espaços de trabalho existentes. Esse processo garante que espaços de trabalho mesmo existentes atendem aos seus novos padrões.    

Ao gerenciar vários espaços de trabalho de análise de Log, recomendamos a integração de cada espaço de trabalho com o sistema de tíquetes existente console operações usando a funcionalidade de [alertas](log-analytics-alerts.md) . Com a integração com os sistemas existentes, a equipe de suporte pode continuar acompanhar seus processos conhecidos. Análise de log regularmente verifica cada espaço de trabalho contra os alertas critérios especificados por você e gera um alerta quando a ação é necessária.

Para relatórios de nível executivos que resumir dados em espaços de trabalho, você pode usar a integração entre a análise de Log e [PowerBI](log-analytics-powerbi.md). Se você precisa integrar com outro sistema de relatórios, você pode usar a API de pesquisa (via PowerShell ou [REST](log-analytics-log-search-api.md)) para executar consultas e exportar resultados da pesquisa.

## <a name="next-steps"></a>Próximas etapas

+ Automatizar a criação e configuração de espaços de trabalho usando o [recurso Gerenciador de modelos](log-analytics-template-workspace-configuration.md)
+ Automatizar a criação de espaços de trabalho usando o [PowerShell](log-analytics-powershell-workspace-configuration.md) 
+ Usar [alertas](log-analytics-alerts.md) para integração com sistemas existentes
+ Gerar relatórios de resumo usando [PowerBI](log-analytics-powerbi.md)
