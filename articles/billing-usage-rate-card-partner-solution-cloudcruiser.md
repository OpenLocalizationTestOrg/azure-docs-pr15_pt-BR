<properties
   pageTitle="Nuvem Cruiser e cobrança integração da API do Microsoft Azure | Microsoft Azure"
   description="Fornece uma perspectiva exclusiva de parceiro de cobrança do Microsoft Azure Cruiser de nuvem, em suas experiências integrar as APIs de cobrança do Azure seus produtos.  Isso é especialmente útil para clientes do Azure e Cruiser de nuvem que estão interessados em usando/experimentar Cruiser de nuvem para o pacote do Microsoft Azure."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"
   />

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="09/08/2016"
   ms.author="mobandyo;sirishap;bryanla"/>

# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Nuvem Cruiser e cobrança integração da API do Microsoft Azure

Este artigo descreve como as informações coletadas das novas Microsoft Azure cobrança APIs podem ser usadas em nuvem Cruiser para análise e simulação de custo de fluxo de trabalho.

## <a name="azure-ratecard-api"></a>API do Azure RateCard
A API RateCard fornece informações de taxa do Azure. Depois de autenticar com as credenciais apropriadas, você pode consultar a API para coletar metadados sobre os serviços disponíveis no Azure, juntamente com as taxas associadas a sua oferta identificação.

A seguir é uma resposta de amostra da API mostrando os preços para o A0 instância (Windows):

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Interface do Cruiser RateCard Azure API de nuvem
Nuvem Cruiser possa aproveitar as informações de RateCard API de maneiras diferentes. Neste artigo, mostraremos como ele pode ser usado para tornar IaaS custo análise e simulação de carga de trabalho.

Para demonstrar este caso de uso, imagine uma carga de trabalho de várias instâncias em execução no Microsoft Azure Pack (WAP). O objetivo é simular esse mesma carga de trabalho no Azure e estimar os custos de fazendo tal migração. Para criar esta simulação, há duas tarefas principais para ser executado:

1. **Processo e importar as informações de serviço coletadas da API RateCard.** Essa tarefa também é executada em pastas de trabalho, onde a extração da API RateCard é transformada e publicada para um novo plano de taxa. Este novo plano de taxa será usado em simulações para estimar os preços Azure.

2. **Normalize WAP serviços e Azure para IaaS.** Por padrão, os serviços WAP se baseiam em recursos individuais (CPU, tamanho de memória, tamanho do disco, etc.) ao Azure services são baseado no tamanho da instância (A0 A1, A2, etc.). A primeira tarefa pode ser executada pelo mecanismo ETL da nuvem Cruiser, chamado de pastas de trabalho, onde esses recursos podem ser incorporados em tamanhos de instância, análogos aos serviços de instância Azure.

### <a name="import-data-from-the-ratecard-api"></a>Importar dados da API RateCard

Pastas de trabalho de Cruiser de nuvem fornecem uma forma automatizada para coletar e processar informações da API RateCard.  Pastas de trabalho do ETL (extrair transformação-carregar) permitem que você configure o conjunto, transformação e publicação dos dados no banco de dados Cruiser de nuvem.

Cada pasta de trabalho pode ter um ou vários conjuntos, permitindo que você correlação informações de diferentes fontes para complementar ou aumentar os dados de uso. As duas capturas de tela a seguintes mostram como criar uma nova *coleção* em uma pasta de trabalho existente e importar informações para o *conjunto* da API RateCard:

![Figura 1 - criar um novo conjunto][1]

![Figura 2: importar dados do novo conjunto][2]

Após importar os dados para a pasta de trabalho, é possível criar várias etapas e processos de transformação, modificar e os dados de modelo. Neste exemplo, desde que estamos só estiver interessados em infraestrutura-como um serviço (IaaS), podemos usar as etapas de transformação para remover as linhas desnecessárias ou registros, relacionados aos serviços diferente IaaS.

A captura de tela a seguir mostra as etapas de transformação usadas para processar os dados coletados pelo RateCard API:

![Figura 3 - etapas de transformação para processar dados coletados da API RateCard][3]

### <a name="defining-new-services-and-rate-plans"></a>Planos de definição de taxa e novos serviços

Há diferentes maneiras de definir serviços na nuvem Cruiser. Uma das opções é importar os serviços dos dados de uso. Este método geralmente é usado ao trabalhar com nuvens públicas, onde os serviços já são definidos pelo provedor.

Um plano de taxa é um conjunto de taxas ou os preços que podem ser aplicados a diferentes serviços, com base em datas efetivas ou grupo de clientes, dentre outras opções. Planos de taxa também podem ser usados na nuvem Cruiser criar simulação ou cenários de "Hipóteses", para entender como alterações nos serviços podem afetar o custo total de uma carga de trabalho.

Neste exemplo, usaremos as informações de serviço da API RateCard para definir novos serviços em nuvem Cruiser. Da mesma forma, podemos usar as taxas associadas aos serviços para criar um novo plano de taxa na nuvem Cruiser.

No final do processo de transformação, é possível criar uma nova etapa e publicar os dados da API RateCard como taxas e novos serviços.

![Figura 4 - publicar os dados da API RateCard como novos serviços e taxas][4]

### <a name="verify-azure-services-and-rates"></a>Verifique se as taxas e os serviços do Azure

Após a publicação de serviços e taxas, você pode verificar a lista de serviços importados na guia de *Serviços* de nuvem do Cruiser:

![Figura 5 - verificando os novos serviços][5]

Na guia *Taxa de planos* , você pode verificar o novo plano de taxa chamado "AzureSimulation" com as taxas importado da API RateCard.

![Figura 6 - verificando o novo plano de taxa e taxas associadas][6]

### <a name="normalize-wap-and-azure-services"></a>Normalizar WAP e os serviços do Azure

Por padrão, WAP fornece informações de uso com base no uso de computação, memória e recursos de rede. No Cruiser de nuvem, você pode definir seus serviços baseados diretamente na alocação ou medido uso desses recursos. Por exemplo, você pode definir uma taxa básica de cada hora do uso da CPU ou cobrar os GB de memória alocada para uma instância.

Neste exemplo, para comparar os custos entre WAP e Azure, precisamos agregar o uso de recursos em WAP em pacotes, que pode ser mapeada para os serviços do Azure. Essa transformação pode ser facilmente implementada nas pastas de trabalho:

![Figura 7 - transformar dados WAP para normalizar serviços][7]

A última etapa na pasta de trabalho é publicar os dados no banco de dados Cruiser de nuvem. Durante esta etapa, os dados de uso agora é agrupados em serviços (que são mapeiam para os serviços do Azure) e vinculados a taxas padrão para criar os encargos.

Após concluir a pasta de trabalho, você pode automatizar o processamento dos dados, adicionando uma tarefa no Agendador e especificando a frequência e a hora da pasta de trabalho seja executado.

![Figura 8 - agendamento de pasta de trabalho][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Criar relatórios para análise de custo de simulação de carga de trabalho

Após o uso é coletado e encargos são carregados no banco de dados Cruiser de nuvem, podemos aproveitar o módulo de nuvem Cruiser ideias para criar a carga de trabalho simulação que estamos desejam de custo.

Para demonstrar esse cenário, criamos o relatório a seguir:

![Comparação de custos][9]

O gráfico superior mostra uma comparação de custo para os serviços, comparando o preço da execução a carga de trabalho para cada serviço específico entre WAP (azul-escuro) e Azure (azul claro).

O gráfico inferior mostra os mesmos dados, mas subdivididos por departamento. Isso mostra os custos para cada departamento executar a carga de trabalho em WAP e Azure, juntamente com a diferença entre elas na barra de poupança (verde).

## <a name="azure-usage-api"></a>API de uso do Azure


### <a name="introduction"></a>Introdução

Recentemente, a Microsoft introduziu a API de uso do Azure, permitindo aos assinantes programaticamente retirar dados de uso para obter ideias para seu consumo. Trata ótima notícia para clientes de nuvem Cruiser que podem tirar proveito do dataset mais sofisticado disponível por meio dessa API.

Cruiser de nuvem pode aproveitar a integração com a API do uso de várias maneiras. O detalhamento (por hora informações de uso) e informações de metadados de recursos disponíveis por meio da API fornecem o conjunto de dados necessário para oferecer suporte a modelos flexíveis de Showback ou devolução. 

Neste tutorial, apresentaremos um exemplo de como Cruiser de nuvem pode aproveitar as informações de API de uso. Mais especificamente, podemos irá criar um grupo de recursos no Azure, associe marcas de formatação para a estrutura de conta e descrever o processo de extração e as informações de marca em nuvem Cruiser de processamento.
 
O objetivo final é sejam capazes de criar relatórios como a seguir e ser capaz de analisar o custo e consumo com base na estrutura de conta preenchida pelas marcas.

![Figura 10 - relatório com divisões usando marcas][10]

### <a name="microsoft-azure-tags"></a>Marcas do Microsoft Azure

Os dados disponíveis por meio da API de uso do Azure incluem não apenas informações de consumo, mas também metadados de recurso, incluindo quaisquer marcas associadas a ele. Marcas fornecem uma maneira fácil de organizar seus recursos, mas para ser eficaz, é necessário garantir que:

- Marcas corretamente são aplicadas aos recursos em tempo de provisionamento
- Marcas corretamente são usadas no processo de Showback/devolução vincular o uso da estrutura de conta da organização.

Ambos esses requisitos podem ser desafiadoras, especialmente quando há um processo manual sobre o fornecimento ou carregar lado. Marcas de digitação, incorretas ou até mesmo ausentes são reclamações comuns dos clientes quando usando marcas e esses erros pode tornar a vida no lado Carregando muito difícil.

Com a nova API de uso do Azure, Cruiser de nuvem pode retirar as informações de marcação de recurso e por meio de uma ferramenta ETL sofisticada chamada pastas de trabalho, corrigir esses erros comuns de marcação. Por meio da transformação usando expressões regulares e correlação de dados, Cruiser de nuvem pode identificar recursos incorretamente marcados e aplicar as marcas corretas, garantindo que a associação correta dos recursos com o consumidor.

No lado do carregamento, Cruiser de nuvem automatiza o processo de Showback/devolução e pode aproveitar as informações de marca para vincular o uso para o consumidor apropriado (departamento, divisão, Project, etc.). Essa automação fornece um aprimoramento enorme e pode garantir um processo de cobrança consistente e podem ser auditado.
 

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Criando um grupo de recursos com marcas no Microsoft Azure
A primeira etapa neste tutorial é criar um grupo de recursos no portal do Azure, em seguida, criar novas marcas para associar aos recursos. Neste exemplo, estamos criando as seguintes marcas: departamento, ambiente, proprietário, projeto.

Captura de tela abaixo mostra um exemplo de grupo de recursos com as marcas associadas.

![Figura 11 - grupo de recursos com marcas associadas no portal do Azure][11]

A próxima etapa é extrair as informações da API do uso em nuvem Cruiser. A API de uso atualmente fornece respostas no formato JSON. Aqui está uma amostra dos dados recuperados:


    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importar dados de uso API em nuvem Cruiser

Pastas de trabalho de Cruiser de nuvem fornecem uma forma automatizada para coletar e processar informações da API do uso. Uma pasta de trabalho do ETL (extrair transformação-carregar) permite que você configure o conjunto, transformação e publicação dos dados no banco de dados Cruiser de nuvem.

Cada pasta de trabalho pode ter um ou vários conjuntos. Isso permite que você correlação informações de diferentes fontes para complementar ou aumentar os dados de uso. Neste exemplo, vamos criar uma nova planilha da pasta de trabalho do modelo do Azure (_UsageAPI)_ e configurar um novo _conjunto_ para importar informações de API do uso.

![Figura 3 - dados de uso API importadas para a planilha de UsageAPI][12]

Observe que esta pasta de trabalho já possui outras planilhas para importar os serviços do Azure (_ImportServices_) e processar as informações de consumo da API de cobrança (_PublishData_).

Nós será usar a API de uso para preencher a planilha de _UsageAPI_ e correlação as informações com os dados de consumo da API de cobrança na planilha _PublishData_ .

### <a name="processing-the-tag-information-from-the-usage-api"></a>As informações da marca da API do uso de processamento

Depois de importar os dados para a pasta de trabalho, vamos criar etapas de transformação na folha de _UsageAPI_ para processar as informações da API. Primeira etapa é usar um processador "JSON dividir" para extrair as marcas de um único campo, em seguida, criar campos para cada uma delas (departamento, Project, proprietário e ambiente).

![Figura 4 - criar novos campos para as informações de marca][13]

Observe que o serviço de "Rede" está faltando as informações de marca (caixa amarela), mas podemos verificar que ela faz parte do mesmo grupo de recursos examinando o campo _ResourceGroupName_ . Como temos as marcas de outros recursos desse grupo de recursos, podemos usar essas informações para aplicar as marcas ausentes para este recurso posteriormente no processo.

A próxima etapa é criar uma tabela de pesquisa associando as informações das marcas para o _ResourceGroupName_. Esta tabela de pesquisa será usada na próxima etapa para enriquecer os dados de consumo com informações de etiqueta.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Adicionando informações da marca para os dados de consumo

Agora podemos saltar para a folha de _PublishData_ , que processa as informações de consumo da API de cobrança e adicione os campos extraídos de marcas. Esse processo é executado olhando a tabela de pesquisa criada na etapa anterior, usando o _ResourceGroupName_ como a chave para pesquisas.

![Figura 5 - preenchendo a estrutura de conta com as informações de pesquisas][14]

Observe que os campos de estrutura de conta apropriada para o serviço de "Rede" foram aplicados, corrigir o problema com as marcas ausentes. Nós também preenchido campos de estrutura de conta para recursos diferente de nosso destino grupo de recursos com "Outros" para diferenciá-los nos relatórios.

Agora precisamos apenas adicionar uma etapa para publicar os dados de uso. Durante esta etapa, as taxas apropriadas para cada serviço definidas no nosso plano de taxa serão aplicadas para as informações de uso, com o encargo resultante carregado no banco de dados.

A melhor parte é que você só precisa passar por este processo uma vez. Quando a pasta de trabalho for concluída, basta adicioná-lo ao agendador e ele será executado por hora ou diariamente na hora agendada. Em seguida, é apenas uma questão de criação de novos relatórios ou personalizando existentes, para analisar os dados para obter ideias significativas do seu uso de nuvem.

### <a name="next-steps"></a>Próximas etapas

+ Para obter instruções detalhadas sobre como criar relatórios e pastas de trabalho de nuvem Cruiser, consulte da nuvem Cruiser on-line [documentação](http://docs.cloudcruiser.com/) (logon válido necessário).  Para obter mais informações sobre nuvem Cruiser, entre em contato com [info@cloudcruiser.com](mailto:info@cloudcruiser.com).
+ Para obter uma visão geral do uso de recursos do Azure e RateCard APIs, consulte [obter ideias para seu consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md) .
+ Confira a [Referência da API REST Azure cobrança](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para obter mais informações sobre as duas APIs, que fazem parte do conjunto de APIs fornecidas pelo Resource Manager do Azure.
+ Se você gostaria de mergulhar diretamente em código de exemplo, confira nossa Microsoft Azure cobrança API amostras de código em [Amostras de código do Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Saiba Mais
+ Consulte o artigo de [Visão geral do Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md) para saber mais sobre o Gerenciador de recursos do Azure.

<!--Image references-->
 
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Figura 1 - criar um novo conjunto"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Figura 2: importar dados do novo conjunto"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figura 3 - etapas de transformação para processar dados coletados da API RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figura 4 - publicar os dados da API RateCard como novos serviços e taxas"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figura 5 - verificando os novos serviços"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figura 6 - verificando o novo plano de taxa e taxas associadas"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figura 7 - transformar dados WAP para normalizar serviços"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figura 8 - agendamento de pasta de trabalho"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figura 9 - relatório de exemplo para o cenário de comparação de custos de carga de trabalho"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Figura 10 - relatório com divisões usando marcas"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Figura 11 - grupo de recursos com marcas associadas no portal do Azure"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Figura 12 - dados de uso API importadas para a planilha de UsageAPI"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Figura 13 - criar novos campos para as informações de marca"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Figura 14 - preenchendo a estrutura de conta com as informações de pesquisas"
