<properties
    pageTitle="Limites de aplicativo de lógica e configuração | Microsoft Azure"
    description="Visão geral dos limites de serviço e valores de configuração disponíveis para aplicativos de lógica."
    services="logic-apps"
    documentationCenter=".net,nodejs,java"
    authors="jeffhollan"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>

# <a name="logic-app-limits-and-configuration"></a>Limites de aplicativo de lógica e configuração

A seguir estão informações sobre os atuais limites e detalhes de configuração para os aplicativos do Azure lógica.

## <a name="limits"></a>Limites

### <a name="http-request-limits"></a>Limites de solicitação HTTP

Estas são limites para uma única chamada de solicitação e/ou conector HTTP

#### <a name="timeout"></a>Tempo limite

|Nome|Limite|Anotações|
|----|----|----|
|Tempo limite da solicitação|1 minuto|Um [padrão assíncrono](app-service-logic-create-api-app.md) ou [até loop](app-service-logic-loops-and-scopes.md) pode compensar conforme necessário|

#### <a name="message-size"></a>Tamanho da mensagem

|Nome|Limite|Anotações|
|----|----|----|
|Tamanho da mensagem|50 MB|Alguns conectores e APIs podem não suportar 50MB.  Gatilho de solicitação suporta até 25MB|
|Limite de avaliação de expressão|131.072 caracteres|`@concat()`, `@base64()`, `string` não pode ser maior que o comprimento|

#### <a name="retry-policy"></a>Repetir a política

|Nome|Limite|Anotações|
|----|----|----|
|Tentativas de repetição|4|Pode configurar com o [parâmetro de política de repetição](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Repetir atraso máximo|1 hora|Pode configurar com o [parâmetro de política de repetição](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Repetir atraso mínimo|20 min|Pode configurar com o [parâmetro de política de repetição](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|

### <a name="run-duration-and-retention"></a>Políticas de retenção e a duração da execução

Esses são os limites para um aplicativo de lógica única executar.

|Nome|Limite|Anotações|
|----|----|----|
|Duração da execução|90 dias||
|Retenção de armazenamento|90 dias|Isso é de início de execução|
|Intervalo de recorrência min|15 s||
|Intervalo de recorrência max|500 dias||


### <a name="looping-and-debatching-limits"></a>Um loop e debatching limites

Estas são limites para um aplicativo de lógica única executar.

|Nome|Limite|Anotações|
|----|----|----|
|Itens de ForEach|5.000|Você pode usar a [ação de consulta](../connectors/connectors-native-query.md) para filtrar maiores matrizes conforme necessário|
|Até iterações|10.000||
|Itens de SplitOn|5.000||
|ForEach paralelismo|20|Você pode definir para um foreach sequencial adicionando `"operationOptions": "Sequential"` para o `foreach` ação|


### <a name="throughput-limits"></a>Limites de produtividade

Estas são limites para uma instância de aplicativo lógica única. 

|Nome|Limite|Anotações|
|----|----|----|
|Disparadores por segundo|100|Pode distribuir fluxos de trabalho entre vários aplicativos conforme necessário|

### <a name="definition-limits"></a>Limites de definição

Estas são limites para uma definição de aplicativo de lógica única.

|Nome|Limite|Anotações|
|----|----|----|
|Ações em ForEach|1|Você pode adicionar aninhados fluxos de trabalho para estender esse conforme necessário|
|Ações por fluxo de trabalho|60|Você pode adicionar aninhados fluxos de trabalho para estender esse conforme necessário|
|Permitido profundidade de aninhamento de ação|5|Você pode adicionar aninhados fluxos de trabalho para estender esse conforme necessário|
|Fluxos por região por assinatura|1000||
|Disparadores por fluxo de trabalho|10||
|Caracteres de max por expressão|8.192||
|Max `trackedProperties` tamanho em caracteres|16.000|
|`action`/`trigger`limite de nome|80||
|`description`limite de tamanho|256||
|`parameters`limite|50||
|`outputs`limite|10||

## <a name="configuration"></a>Configuração

### <a name="ip-address"></a>Endereço IP

Chamadas feitas a partir de um [conector](../connectors/apis-list.md) serão proveniente o endereço IP especificado abaixo.

Chamadas feitas a partir de um aplicativo de lógica diretamente (isto é via [HTTP](../connectors/connectors-native-http.md) ou [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) podem vir de qualquer os [Intervalos de IP do Azure data center](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

|Região de aplicativo de lógica|IP de saída|
|-----|----|
|Austrália Oriental|40.126.251.213|
|Austrália Sudeste|40.127.80.34|
|Brasil Sul|191.232.38.129|
|Índia central|104.211.98.164|
|Centro dos EUA|40.122.49.51|
|Da Ásia Oriental|23.99.116.181|
|Leste EUA|191.237.41.52|
|Leste dos EUA 2|104.208.233.100|
|Japão Leste|40.115.186.96|
|Japão Oeste|40.74.130.77|
|Centro Norte dos EUA|65.52.218.230|
|Norte da Europa|104.45.93.9|
|Centro Sul dos EUA|104.214.70.191|
|Sudeste Asiático|13.76.231.68|
|Índia Sul|104.211.227.225|
|Europa Ocidental|40.115.50.13|
|Índia Oeste|104.211.161.203|
|Oeste EUA|104.40.51.248|


## <a name="next-steps"></a>Próximas etapas  

- Para começar com aplicativos de lógica, siga o tutorial de [criar um aplicativo de lógica](app-service-logic-create-a-logic-app.md) .  
- [Exibir exemplos e cenários comuns](app-service-logic-examples-and-scenarios.md)
- [Você pode automatizar processos de negócios com aplicativos de lógica](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Aprenda a integrar seus sistemas com aplicativos de lógica](http://channel9.msdn.com/Events/Build/2016/P462)