<properties
    pageTitle="Use tabelas de dados e pesquisa de referência da análise de fluxo | Microsoft Azure"
    description="Usar dados de referência em uma consulta de análise de fluxo"
    keywords="tabela de pesquisa, dados de referência"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="using-reference-data-or-lookup-tables-in-a-stream-analytics-input-stream"></a>Usando tabelas de dados ou pesquisa de referência em um fluxo de entrada de análise de fluxo

Dados de referência (também conhecido como uma tabela de pesquisa) são um conjunto de dados finito estáticas ou diminuindo alterando natureza, usado para realizar uma pesquisa ou correlação com seu fluxo de dados. Para utilizar dados de referência no seu trabalho de análise de fluxo de Azure você geralmente usará uma [Referência dados ingressar](https://msdn.microsoft.com/library/azure/dn949258.aspx) em sua consulta. Análise de fluxo usa o armazenamento de Blob do Azure como a camada de armazenamento para dados de referência e com referência de fábrica de dados do Azure dados podem ser transformados e/ou copiados para o armazenamento de Blob do Azure, para ser usado como dados de referência, de [qualquer número de nuvem com base e armazenamentos de dados locais](../data-factory/data-factory-data-movement-activities.md). Dados de referência são estabelecidos como uma sequência de blobs (definido na configuração de entrada) em ordem crescente da data/hora especificada no nome do blob. Ele oferece suporte **apenas** para adicionando até o final da sequência usando uma data/hora **maior** que especificado por último blob na sequência.

## <a name="configuring-reference-data"></a>Configurar dados de referência

Para configurar seus dados de referência, primeiro é necessário criar uma entrada de tipo de **Dados de referência**. A tabela a seguir explica cada propriedade que você precisará fornecer ao criar os entrada de dados de referência com sua descrição:

<table>
<tbody>
<tr>
<td>Nome da propriedade</td>
<td>Descrição</td>
</tr>
<tr>
<td>Alias de entrada</td>
<td>Um nome amigável que será usado na consulta trabalho para fazer referência a essa entrada.</td>
</tr>
<tr>
<td>Conta de armazenamento</td>
<td>O nome da conta de armazenamento onde estão localizados seus arquivos de blob. Se estiver na mesma assinatura do seu trabalho de análise de fluxo, você pode selecioná-la na lista suspensa para baixo.</td>
</tr>
<tr>
<td>Chave da conta de armazenamento</td>
<td>A chave secreta associada à conta de armazenamento. Isso é preenchido automaticamente se a conta de armazenamento está na mesma assinatura do seu trabalho de análise de fluxo.</td>
</tr>
<tr>
<td>Contêiner de armazenamento</td>
<td>Os contêineres fornecem um agrupamento lógico para blobs armazenados no serviço do Microsoft Azure Blob. Quando você carrega um blob serviço Blob, você deve especificar um contêiner de blob.</td>
</tr>
<tr>
<td>Padrão de caminho</td>
<td>O caminho de arquivo usado para localizar seu blobs dentro do contêiner especificado. Dentro do caminho, você pode optar por especificar instâncias de uma ou mais das seguintes 2 variáveis:<BR>{date}, {time}<BR>Exemplo 1: products/{date}/{time}/product-list.csv<BR>Exemplo 2: products/{date}/product-list.csv
</tr>
<tr>
<td>Formato de data [opcional]</td>
<td>Se você tiver usado {date} dentro do padrão de caminho que você especificou, você pode selecionar o formato de data em que os arquivos são organizados na lista suspensa de formatos com suporte. Exemplo: DD/MM/AAAA</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Se você tiver usado {time} dentro do padrão de caminho que você especificou, você pode selecionar o formato de hora em que os arquivos são organizados na lista suspensa de formatos com suporte. Exemplo: HH</td>
</tr>
<tr>
<td>Formato de serialização de evento</td>
<td>Para certificar-se de que suas consultas funcionam da forma esperada, a análise de fluxo precisa saber qual formato de serialização você está usando para fluxos de dados de entrada. Dados de referência, os formatos suportados são CSV e JSON.</td>
</tr>
<tr>
<td>Codificação</td>
<td>UTF-8 é o único formato de codificação com suporte no momento</td>
</tr>
</tbody>
</table>

## <a name="generating-reference-data-on-a-schedule"></a>Geração de dados de referência em uma agenda

Se seus dados de referência forem um conjunto de dados lentamente mudança, em seguida, suporte para atualizar a referência de dados estão habilitados, especificando um padrão de caminho na configuração de entrada usando a data de {} e {tempo} tokens de substituição. Análise de fluxo selecionará as definições de dados de referência atualizadas com base nesse padrão de caminho. Por exemplo, um padrão de `sample/{date}/{time}/products.csv` com um formato de data de **"aaaa-MM-DD"** e um tempo formato de **"Hh: mm"** instrui a análise de fluxo para retomar o blob atualizado `sample/2015-04-16/17:30/products.csv` em 5:30 PM em 16 de abril de 2015 UTC fuso horário.

> [AZURE.NOTE] Atualmente trabalhos de análise de fluxo procure a atualização de blob somente quando o tempo de máquina avança para o tempo codificado no nome do blob. Por exemplo o trabalho procurará `sample/2015-04-16/17:30/products.csv` assim que possível, mas não versões anteriores que 5:30 PM em 16 de abril de 2015 UTC fuso horário. Ele será *nunca* aparência para um arquivo com um tempo codificado anterior ao último aquele for detectado.
> 
> Por exemplo Depois que o trabalho encontra o blob `sample/2015-04-16/17:30/products.csv` ela irá ignorar todos os arquivos com uma data codificado anterior 5:30 PM 16 de abril de 2015 isso se um chegando atrasados `sample/2015-04-16/17:25/products.csv` blob é criado no mesmo contêiner o trabalho não usará-lo.
> 
> Da mesma forma se `sample/2015-04-16/17:30/products.csv` só é produzidos em 10:03 PM 16 de abril de 2015, mas sem blob com uma data anterior estiver presente no contêiner, o trabalho usará esse arquivo começando em 10:03 PM 16 de abril de 2015 e usar os dados de referência anterior até lá.
> 
> Uma exceção é iniciada quando o trabalho precisa novamente processar dados voltar no tempo ou quando o trabalho for primeiro. Na hora de início, que o trabalho está procurando o mais recente blob produzido antes do trabalho Iniciar tempo especificado. Isso é feito para garantir que não há um conjunto de dados de referência **não vazia** quando o trabalho é iniciado. Se um não foi encontrado, o trabalho exibirá o seguinte diagnóstico: `Initializing input without a valid reference data blob for UTC time <start time>`.


[Fábrica de dados do Azure](https://azure.microsoft.com/documentation/services/data-factory/) pode ser usado para coordenar a tarefa de criar os blobs atualizados necessários a análise de fluxo para atualizar definições de dados de referência. Fábrica de dados é um serviço de integração de dados baseada em nuvem que organiza e automatize a movimentação e transformação de dados. Dados fábrica oferece suporte [se conectando a um grande número de baseado na nuvem e armazenamentos de dados locais](../data-factory/data-factory-data-movement-activities.md) e mover dados facilmente regularmente que você especificar. Para obter mais informações e orientação passo a passo sobre como configurar um pipeline de fábrica de dados para gerar dados de referência para a análise de fluxo que atualiza em um cronograma predefinido, confira esta [GitHub amostra](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Dicas sobre como atualizar seus dados de referência ##

1. Substituir blobs de dados de referência não fará com que a análise de fluxo recarregar o blob e em alguns casos, ela pode causar a falha do trabalho. A maneira recomendada para alterar dados de referência é adicionar um novo blob usando o mesmo contêiner e padrão do caminho definido na entrada trabalho e usar uma data/hora **maior** que especificado por último blob na sequência.
2.  Dados de referência blobs não são ordenadas por tempo de "Última modificação" do blob, mas somente pela hora e a data especificada no blob nome usando a data de {} e {tempo} substituições.
3.  Em algumas ocasiões que um trabalho deve voltar no tempo, portanto blobs de dados de referência devem não ser alteradas ou excluídas.

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
Você já foram introduzidas análise de fluxo, um serviço gerenciado do streaming a análise de dados da Internet das coisas. Para saber mais sobre este serviço, consulte:

- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
