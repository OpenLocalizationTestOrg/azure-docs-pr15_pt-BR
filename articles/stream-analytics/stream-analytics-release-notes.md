<properties 
    pageTitle="Notas de versão de fluxo Analytics | Microsoft Azure" 
    description="Notas de versão do fluxo Analytics" 
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

#<a name="stream-analytics-release-notes"></a>Notas de versão do fluxo de análise

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Notas de versão do 04/15/2016 do fluxo de análise ##

Esta versão contém a seguinte atualização.

Título | Descrição
---|---
Disponibilidade geral para saídas de Power BI  | [Power BI saídas](stream-analytics-power-bi-dashboard.md) agora estão geralmente disponíveis. A expiração de autorização de 90 dias para o Power BI foi removida. Para obter mais informações sobre cenários onde autorização precisa ser renovada, consulte a seção de [autorização de renovação](stream-analytics-power-bi-dashboard.md#Renew-authorization) da criação de um painel do Power BI.

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Notas de versão do 03/03/2016 do fluxo de análise ##

Esta versão contém a seguinte atualização.

Título | Descrição
---|---
Novos itens de linguagem de consulta do fluxo Analytics  | SAQL agora tem [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "GetType MSDN página"), [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "TRY_CAST MSDN página") e [REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "REGEXMATCH MSDN página").

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Notas de versão 12/10/2015 do fluxo Analytics ##

Esta versão contém a seguinte atualização.

Título | Descrição
---|---
Atualização de versão da API REST | A versão da API REST foi atualizada 2015-10-01. Detalhes podem ser encontrados na MSDN em [Referência de API REST do gerenciamento de análise de fluxo](https://msdn.microsoft.com/library/azure/dn835031.aspx) e [a integração de aprendizado de máquina em análise de fluxo](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md).
Integração de aprendizado de máquina Azure | Com esta versão vem de suporte do Azure funções definidas pelo usuário de aprendizado de máquina. Consulte o [tutorial](stream-analytics-machine-learning-integration-tutorial.md) para obter mais informações, bem como o [lançamento de blog geral](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx).

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Notas de versão 11/12/2015 do fluxo de análise ##

Esta versão contém a seguinte atualização.

Título | Descrição
---|---
Novo comportamento de SELECT | Selecione na análise de fluxo foi estendido para permitir * como um acesso de propriedade de um registro aninhado. Para obter mais informações, consulte [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "Tipos de dados complexos").

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Notas de versão de 22/10/2015 do fluxo de análise ##

Esta versão contém as seguintes atualizações.

Título | Descrição
---|---
Recursos de linguagem de consulta adicionais | Análise de fluxo expandiu a linguagem de consulta, incluindo os seguintes recursos: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [teto](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [PLANTAS](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [entrada](https://msdn.microsoft.com/library/azure/mt605290.aspx), [Quadrado](https://msdn.microsoft.com/library/azure/mt605288.aspx)e [raiz](https://msdn.microsoft.com/library/azure/mt605238.aspx).
Limitações de agregação removidas  | Esta versão remove a limitação de 15 agregados em uma consulta. Agora, não há nenhum limite para o número de agregados por consulta.
Recurso de grupo por System.Timestamp adicional | A função [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) agora permite para window_type ou [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx).
DESLOCAMENTO adicionado para queda e saltos windows | Por padrão, o windows [queda](https://msdn.microsoft.com/library/azure/dn835055.aspx) e [Hopping](https://msdn.microsoft.com/library/azure/dn835041.aspx) alinhados contra tempo zero (1/1/0001 12:00:00 AM UTC). O novo parâmetro (opcional) 'offsetsize' permite especificar um deslocamento personalizado (ou alinhamento).


## <a name="notes-for-09292015-release-of-stream-analytics"></a>Notas de versão do 29/09/2015 do fluxo de análise ##

Esta versão contém as seguintes atualizações.

Título | Descrição
---|---
Demonstração pública do Azure IoT Suite | Análise de fluxo é incluída na visualização pública do Azure IoT pacote.
Integração com o Portal Azure | Além de contínua presença no portal de gerenciamento do Azure, a análise de fluxo agora está integrada no [Portal do Azure](https://azure.microsoft.com/overview/preview-portal/). Observe que a análise de fluxo de funcionalidade no portal de visualização está atualmente um subconjunto da funcionalidade oferecidos no portal de gerenciamento do Azure, sem suporte para o teste de consulta no navegador, Power BI saída configuração e navegando até ou criar nova entrada e recursos de saída em assinaturas a que tem acesso.
Suporte a saída de DocumentDB | Trabalhos de análise de fluxo agora podem saída para [DocumentDB](https://azure.microsoft.com/services/documentdb/).
Suporte para entrada no IoT Hub | Trabalhos de análise de fluxo agora podem incluir dados de IoT Hubs.
Carimbo de hora por para eventos heterogêneos | Quando um fluxo de dados único contém vários tipos de evento tendo carimbos de hora em campos diferentes, você pode agora usar [Carimbo de hora por](http://msdn.microsoft.com/library/mt573293.aspx) com expressões de especificar campos de carimbo de hora diferente para cada caso.

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Notas de versão do 10/09/2015 do fluxo de análise ##

Esta versão contém as seguintes atualizações.

Título|Descrição
---|---
Suporte para os grupos de PowerBI|Para habilitar compartilhar dados com outros usuários do Power BI, trabalhos de análise de fluxo agora podem escrever a [grupos de PowerBI](stream-analytics-define-outputs.md#power-bi) dentro de sua conta do Power BI.

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Notas de versão do 20/08/2015 do fluxo de análise ##

Esta versão contém as seguintes atualizações.

Título|Descrição
---|---
Função última adicionada |A [última](http://msdn.microsoft.com/library/mt421186.aspx) função agora está disponível no trabalhos de análise de fluxo, permitindo que você recuperar o evento mais recente em um fluxo de evento dentro de um determinado período.
Novas funções de matriz|Funções de matriz [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) e [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) agora estão disponíveis.
Novas funções de registro|Funções de registro [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) e [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) agora estão disponíveis.

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Notas de versão de 30/07/2015 do fluxo de análise ##

Esta versão contém as seguintes atualizações.

Título|Descrição
---|---
Id da organização Power BI desassociado do Id do Azure|Este recurso permite que a [saída do Power BI](stream-analytics-power-bi-dashboard.md) para trabalhos ASA em qualquer tipo de conta do Microsoft Azure (Live Id ou Id da organização). Além disso, você pode ter uma identificação de Org para sua conta do Azure e usar um diferente para autorizar saída do Power BI.
Suporte a saída de filas de barramento de serviço|[Serviço barramento filas](stream-analytics-define-outputs.md#service-bus-queues) saídas agora estão disponíveis em trabalhos de análise de fluxo.
Suporte a saída de tópicos do barramento de serviço|[Tópicos de barramento de serviço](stream-analytics-define-outputs.md#service-bus-topics) saídas agora estão disponíveis em trabalhos de análise de fluxo.

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Notas de versão do 07/09/2015 do fluxo de análise ##

Esta versão contém as seguintes atualizações.


Título|Descrição
---|---
Personalizar Blob partição de saída|Saídas de armazenamento de blob agora permitem uma opção para especificar a frequência essa saída blobs são gravados e a estrutura e o formato da estrutura de pasta de caminho de dados de saída. 

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Anotações 05/03/2015 versão do fluxo de análise ##

Esta versão contém as seguintes atualizações.


Título|Descrição
---|---
Maior valor máximo para fora de ordem tolerância janela|O tamanho máximo da janela de tolerância fora de ordem agora é 59:59 (mm: ss)
Formato de saída JSON: Linha separado ou matriz|Agora há uma opção na saída para armazenamento de Blob ou Hub de eventos de saída como uma matriz de objetos JSON ou separando objetos JSON com uma nova linha. 

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Notas de versão do 04/16/2015 do fluxo de análise ##


Título|Descrição
---|---
Atraso na configuração de conta de armazenamento do Azure|Ao criar um trabalho de análise de fluxo em uma região pela primeira vez, você será solicitado a criar uma nova conta de armazenamento ou especifique uma conta existente para monitoramento trabalhos de análise de fluxo na região. Devido a latência em Configurando o monitoramento, criar outro trabalho de análise de fluxo na mesma região em 30 minutos solicitará a especificação de uma segunda conta de armazenamento em vez de mostrando aquele recentemente configurado no menu suspenso monitoramento conta de armazenamento. Para evitar a criação de uma conta de armazenamento desnecessária, aguarde 30 minutos depois de criar um trabalho em uma região pela primeira vez antes de provisionar trabalhos adicionais nessa região.
Atualização de trabalho|No momento, a análise de fluxo não oferece suporte ao vivo edições para a definição ou a configuração de um trabalho em execução. Para alterar a entrada, saída, consulta, escala ou configuração de um trabalho em execução, você deve primeiro parar o trabalho.
Tipos de dados inferidos de fonte de entrada|Se uma instrução CREATE TABLE não for usada, o tipo de entrada é derivado de formato de entrada, por exemplo, todos os campos de CSV são cadeia de caracteres. Campos devem ser convertidas explicitamente para o tipo de direito usando a função CAST para evitar erros de incompatibilidade de tipo.
Campos ausentes são saídos como valores nulos|Fazendo referência a um campo que não está presente na fonte de entrada resultará em valores nulos no evento de saída.
COM instruções devem preceder instruções SELECT|Em sua consulta, instruções SELECT devem acompanhar subconsultas definidas com instruções.
Problema de falta de memória|Reinicie o streaming trabalhos de análise com uma grande tolerância para fora da ordem eventos e/ou manter que uma grande quantidade de estado pode causar o trabalho a ficar sem memória, resultando em um trabalho de consultas complexas. As operações de início e parada estarão visíveis em logs de operação do trabalho. Para evitar esse comportamento, dimensione a consulta check-out por várias partições. Em uma versão futura, essa limitação será inerentes afetar o desempenho em trabalhos afetados em vez de reiniciá-los.
Entradas de blob grande sem carimbo de hora de carga podem causar problema de falta de memória|Consumir grandes arquivos do armazenamento de Blob pode causar trabalhos de análise de fluxo falhar se não for especificado um campo de carimbo de hora por meio de carimbo de hora por. Para evitar esse problema, manter cada blob abaixo de 10MB no tamanho.
Limitação de volume do evento de banco de dados SQL|Ao usar o banco de dados SQL como um destino de saída, muito grandes volumes de dados de saída podem causar o trabalho de análise de fluxo tempo limite. Para resolver esse problema, reduza o volume de saída usando operadores de filtro ou agregados, ou escolha armazenamento de Blob do Azure ou Hubs de evento como um destino de saída.
Conjuntos de dados de PowerBI só podem conter uma tabela|PowerBI não dá suporte a mais de uma tabela em um determinado conjunto de dados.

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 
