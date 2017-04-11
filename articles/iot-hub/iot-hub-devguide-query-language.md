<properties
 pageTitle="Guia do desenvolvedor - linguagem de consulta | Microsoft Azure"
 description="Guia de desenvolvedor do Azure IoT Hub - descrição da linguagem de consulta usada para recuperar informações sobre gêmeos, métodos e trabalhos de seu hub IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="reference---query-language-for-twins-and-jobs"></a>Referência - linguagem de consulta para gêmeos e trabalhos

## <a name="overview"></a>Visão geral

Hub de IoT fornece uma linguagem SQL semelhante poderosa para recuperar informações sobre [Gêmeos de dispositivo] [ lnk-twins] e [trabalhos][lnk-jobs]. Este artigo apresenta:

* Uma introdução aos recursos principais da linguagem de consulta do IoT Hub, e
* A descrição detalhada do idioma.

## <a name="getting-started-with-twin-queries"></a>Introdução ao consultas twin

[Gêmeos de dispositivo] [ lnk-twins] pode conter objetos JSON aleatório como marcas e propriedades. Hub de IoT permite Gêmeos de dispositivo de consulta como um único documento JSON contendo todas as informações de twin.
Por exemplo, suponha que seu Gêmeos de hub IoT tem a seguinte estrutura:

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                "location": {                                                      
                    "region": "US",                                                  
                    "plant": "Redmond43"                                             
                }                                                                  
            },                                                                   
            "properties": {                                                      
                "desired": {                                                       
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300                                          
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                     
                    },                                                               
                    "$version": 4                                                    
                },                                                                 
                "reported": {                                                      
                    "connectivity": {                                                
                        "type": "cellular"                            
                    },                                                               
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300,                                         
                        "status": "Success"                                            
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                
                    },                                                               
                    "$version": 7                                                    
                }                                                                  
            }                                                                    
        }

Hub de IoT expõe gêmeos o dispositivo como um conjunto de documentos chamado de **dispositivos**.
Portanto, a seguinte consulta recupera todo o conjunto de Gêmeos de dispositivo:

        SELECT * FROM devices

> [AZURE.NOTE] [IoT Hub SDKs] [ lnk-hub-sdks] suporte paginação de resultados grandes.

Hub de IoT permite recuperar gêmeos filtragem com condições aleatório. Por exemplo,

        SELECT * FROM devices
        WHERE tags.location.region = 'US'

recupera os gêmeos com a marca de **location.region** definir **conosco**.
Operadores booleanos e comparações aritméticas também são suportadas, por exemplo

        SELECT * FROM devices
        WHERE tags.location.region = 'US'
            AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60

recupera todos os gêmeos localizados nos EUA configurado para enviar telemetria menos frequência que cada minuto. Como uma conveniência, também é possível usar constantes de matriz em conjunto com **no** e operadores de **login** (não no). Por exemplo,

        SELECT * FROM devices
        WHERE property.reported.connectivity IN ['wired', 'wifi']

recupera todos os gêmeos relatados wifi ou com fio conectividade. Consulte a [cláusula WHERE] [ lnk-query-where] seção para a referência completa dos recursos de filtragem.

Também há suporte para o agrupamento e agregações. Por exemplo,

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

Retorna a contagem dos dispositivos em cada status de configuração de telemetria.

        [
            {
                "numberOfDevices": 3,
                "status": "Success"
            },
            {
                "numberOfDevices": 2,
                "status": "Pending"
            },
            {
                "numberOfDevices": 1,
                "status": "Error"
            }
        ]

O exemplo acima ilustra uma situação onde três dispositivos informado configuração bem-sucedida, dois ainda estão aplicando a configuração e um relatado um erro.

### <a name="c-example"></a>Exemplo de c#

A funcionalidade de consulta é exposta pelo [serviço c# SDK] [ lnk-hub-sdks] na classe **RegistryManager** .
Aqui está um exemplo de uma consulta simples:

        var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
        while (query.HasMoreResults)
        {
            var page = await query.GetNextAsTwinAsync();
            foreach (var twin in page) 
            {
                // do work on twin object
            }
        }

Observe como o objeto de **consulta** é instanciado com um tamanho de página (até 1000) e, em seguida, várias páginas podem ser recuperadas chamando os métodos de **GetNextAsTwinAsync** várias vezes.
É importante observar que o objeto de consulta expõe várias **próximo\***, dependendo da opção de desserialização necessária pela consulta, ou seja, macros ou objetos ou Json simples a ser usada ao usar projeções de trabalho.

### <a name="node-example"></a>Exemplo de nó

A funcionalidade de consulta é exposta pelo [serviço de nó SDK] [ lnk-hub-sdks] no objeto de **registro** .
Aqui está um exemplo de uma consulta simples:

        var query = registry.createQuery('SELECT * FROM devices', 100);
        var onResults = function(err, results) {
            if (err) {
                console.error('Failed to fetch the results: ' + err.message);
            } else {
                // Do something with the results
                results.forEach(function(twin) {
                    console.log(twin.deviceId);
                });

                if (query.hasMoreResults) {
                    query.nextAsTwin(onResults);
                }
            }
        };
        query.nextAsTwin(onResults);

Observe como o objeto de **consulta** é instanciado com um tamanho de página (até 1000) e, em seguida, várias páginas podem ser recuperadas chamando os métodos de **nextAsTwin** várias vezes.
É importante observar que o objeto de consulta expõe várias **próximo\***, dependendo da opção de desserialização necessária pela consulta, ou seja, macros ou objetos ou Json simples a ser usada ao usar projeções de trabalho.

### <a name="limitations"></a>Limitações

Atualmente, projeções são suportadas apenas quando usando agregações, ou seja, as consultas não agregado só podem usar `SELECT *`. Além disso, a agregação só há suporte em conjunto com o agrupamento.

## <a name="getting-started-with-jobs-queries"></a>Guia de Introdução com consultas de trabalhos

[Trabalhos] [ lnk-jobs] oferece uma maneira de executar operações em conjuntos de dispositivos. Cada twin dispositivo contém as informações dos trabalhos do qual ele faz parte em um conjunto chamado **trabalhos**.
Logicamente,

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                ...                                                              
            },                                                                   
            "properties": {                                                      
                ...                                                                 
            },
            "jobs": [
                { 
                    "deviceId": "myDeviceId",
                    "jobId": "myJobId",    
                    "jobType": "scheduleTwinUpdate",            
                    "status": "completed",                    
                    "startTimeUtc": "2016-09-29T18:18:52.7418462",
                    "endTimeUtc": "2016-09-29T18:20:52.7418462",
                    "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
                    "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
                    "outcome": {
                        "deviceMethodResponse": null   
                    }                                         
                },
                ...
            ]                                                             
        }

Atualmente, essa coleção é queriable como **devices.jobs** na linguagem de consulta IoT Hub.

Por exemplo, para obter todos os trabalhos (passados e agendados) que afetam um único dispositivo, você pode usar a seguinte consulta:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'

Observe como essa consulta fornece o status de dispositivo específico (e possivelmente a resposta do método direto) de cada trabalho retornado.
Também é possível filtrar com condições Boolianas aleatório em todas as propriedades dos objetos no conjunto de **devices.jobs** .
Por exemplo, a seguinte consulta:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'
            AND devices.jobs.jobType = 'scheduleTwinUpdate'
            AND devices.jobs.status = 'completed'
            AND devices.jobs.createdTimeUtc > '2016-09-01'

recupera todos os twin concluído trabalhos de atualização para o dispositivo **myDeviceId** criadas após setembro de 2016.

Também é possível recuperar os resultados por dispositivo de um único trabalho.

        SELECT * FROM devices.jobs
        WHERE devices.jobs.jobId = 'myJobId'

### <a name="limitations"></a>Limitações
Atualmente, não oferece suporte a consultas em **devices.jobs** :

* Projeções, ou seja, apenas `SELECT *` é possível;
* Condições que se referem ao twin dispositivo além das propriedades do trabalho conforme mostrado acima.
* Agregações que realizam, por exemplo, contagem, média, agrupar por.

## <a name="basics-of-an-iot-hub-query"></a>Noções básicas de uma consulta de IoT Hub

Cada consulta IoT Hub consiste em uma seleção e cláusulas FROM e WHERE opcional e agrupar por cláusulas. Cada consulta é executada em um conjunto de documentos JSON, por exemplo, Gêmeos de dispositivo. A cláusula FROM indica o conjunto de documentos para ser iteração no (**dispositivos** ou **devices.jobs**). Em seguida, o filtro na cláusula WHERE é aplicado. No caso de agregações, os resultados desta etapa são agrupados como especificado na cláusula GROUP BY e, para cada grupo, uma linha é gerada conforme especificado na cláusula SELECT.

        SELECT <select_list>
        FROM <from_specification>
        [WHERE <filter_condition>]
        [GROUP BY <group_specification>]

## <a name="from-clause"></a>Cláusula FROM

A cláusula **FROM < from_specification >** pode assumir apenas dois valores: **de dispositivos**, Gêmeos de dispositivo de consulta ou **FROM devices.jobs**, detalhes de cada dispositivo de trabalho de consulta.

## <a name="where-clause"></a>Cláusula WHERE

O **onde < filter_condition >** cláusula é opcional. Especifica as condições que os documentos JSON na coleção de devem satisfazer para ser incluídos como parte do resultado. Qualquer documento JSON deve avaliar as condições especificadas como "true" para ser incluídas no resultado.

As condições permitidas são descritas na seção [expressões e condições][lnk-query-expressions].

## <a name="select-clause"></a>Cláusula SELECT

Cláusula SELECT (**Selecionar < select_list >**) é obrigatória e especifica quais valores serão recuperados a partir da consulta. Especifica os valores JSON a ser usada para gerar novos objetos JSON para cada elemento da subconjunto filtrado (e, opcionalmente, agrupado) da coleção de, a fase de projeção gera um novo objeto JSON, construído com os valores especificados na cláusula SELECT.

Esta é a gramática de cláusula SELECT:

        SELECT [TOP <max number>] <projection list>

        <projection_list> ::=
            '*'
            | <projection_element> AS alias [, <projection_element> AS alias]+

        <projection_element> :==
            attribute_name
            | <projection_element> '.' attribute_name
            | <aggregate>

        <aggregate> :==
            count(<projection_element>) | count()
            | avg(<projection_element>) | avg()
            | sum(<projection_element>) | sum()
            | min(<projection_element>) | min()
            | max(<projection_element>) | max()

onde **attribute_name** refere-se a qualquer propriedade do documento na coleção de JSON. Alguns exemplos de cláusulas SELECT podem ser encontrados no [guia de Introdução com consultas de twin] [ lnk-query-getstarted] seção.

Atualmente, cláusulas de seleção diferentes **Selecione \* ** são suportadas apenas em consultas agregadas em gêmeos.

## <a name="group-by-clause"></a>Cláusula GROUP BY

Cláusula **GROUP BY < group_specification >** é uma etapa opcional que pode ser executada após o filtro especificado na cláusula WHERE e antes da projeção especificado em Selecionar. Ele agrupa documentos com base no valor de um atributo. Esses grupos são usados para gerar valores agregados conforme especificado na cláusula SELECT.

Um exemplo de uma consulta usando GROUP BY é:

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

A sintaxe formal para agrupar por é:

        GROUP BY <group_by_element>
        <group_by_element> :==
            attribute_name
            | < group_by_element > '.' attribute_name

onde **attribute_name** refere-se a qualquer propriedade do documento na coleção de JSON. 

Atualmente, a cláusula GROUP BY é suportada apenas quando consultando gêmeos.

## <a name="expressions-and-conditions"></a>Expressões e condições

Em um alto nível, uma *expressão*:

* Avalia para uma instância de um tipo JSON (ou seja booliano, número, cadeia de caracteres, matriz ou objeto), e
* É definido manipulando dados provenientes do dispositivo JSON documento e constantes usando funções e operadores internos.

*Condições* são expressões que é avaliada como um booliano, ou seja, qualquer constante diferente booliano **true** é considerado como **false** (incluindo **Nulo**, **indefinido**, qualquer instância do objeto ou matriz, qualquer cadeia de caracteres e claramente o booliana **false**).

A sintaxe para expressões é:

        <expression> ::=
            <constant> |
            attribute_name |
            unary_operator <expression> |
            <expression> binary_operator <expression> |
            <create_array_expression> |
            '(' <expression> ')'

        <constant> ::=
            <undefined_constant>
            | <null_constant> 
            | <number_constant> 
            | <string_constant> 
            | <array_constant> 

        <undefined_constant> ::= undefined
        <null_constant> ::= null
        <number_constant> ::= decimal_literal | hexadecimal_literal
        <string_constant> ::= string_literal
        <array_constant> ::= '[' <constant> [, <constant>]+ ']'

onde:

| Símbolo | Definição |
| -------------- | -----------------|
| attribute_name | Qualquer propriedade do documento na coleção de JSON. |
| unary_operator | Qualquer operador unário de acordo com a seção de operadores. |
| binary_operator | Qualquer operador binário de acordo com as seção de operadores. |
| decimal_literal | Um flutuar expresso em notação decimal. |
| hexadecimal_literal | Um número expresso pela cadeia de caracteres '0 x' seguido por uma cadeia de caracteres de dígitos hexadecimais. |
| literal_de_cadeia_de_caracteres | Cadeia de caracteres literais são cadeias de caracteres Unicode representadas por uma sequência de zero ou mais caracteres Unicode ou sequências de escape. Cadeia de caracteres literais são colocados entre aspas simples (apóstrofo: ') ou aspas duplas (aspas: "). Permitido sai: `\'`, `\"`, `\\`, `\uXXXX` para caracteres Unicode definidos por 4 dígitos hexadecimais. |

### <a name="operators"></a>Operadores

Os operadores a seguir são suportados:

| Família | Operadores |
| -------------- | -----------------|
| Aritmética | +,-,*,/,% |
| Lógico | E, OU, NÃO |
| Comparação |  =,! =, <>,, < =, > =, <> |

## <a name="next-steps"></a>Próximas etapas

Saiba como executar consultas em seus aplicativos usando [IoT Hub SDKs][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#getting-started-with-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md