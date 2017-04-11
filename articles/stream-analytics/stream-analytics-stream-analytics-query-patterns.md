<properties
    pageTitle="Exemplos de padrões comuns de uso na análise de fluxo de consulta | Microsoft Azure"
    description="Padrões de consulta de análise de fluxo Azure comuns "
    keywords="exemplos de consulta"
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
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Exemplos de padrões de uso de análise de fluxo comuns de consulta

## <a name="introduction"></a>Introdução

Consultas no Azure fluxo Analytics são expressas em uma linguagem de consulta do tipo SQL, que é documentada no guia de [Referência de linguagem de consulta de análise do fluxo](https://msdn.microsoft.com/library/azure/dn834998.aspx) .  Este artigo descreve soluções para diversos padrões comuns de consulta com base em cenários do mundo real.  É um trabalho em andamento e continuarão a ser atualizados com novos padrões continuamente.

## <a name="query-example-data-type-conversions"></a>Exemplo de consulta: conversões de tipo de dados
**Descrição**: definir os tipos de propriedades no fluxo de entrada.
Por exemplo, a espessura de carro está chegando no fluxo de entrada como cadeias de caracteres e precisa ser convertido em INT para executar soma-lo.

**Entrada**:

| Fazer | Tempo | Peso |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | "1000" |
| Honda | 2015-01-01T00:00:02.0000000Z | "2000" |

**Saída**:

| Fazer | Peso |
| --- | --- |
| Honda | 3000 |

**Solução**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Explicação**: Use uma instrução de CAST no campo peso para especificar seu tipo (consulte a lista de tipos de dados compatíveis [aqui](https://msdn.microsoft.com/library/azure/dn835065.aspx)).


## <a name="query-example-using-likenot-like-to-do-pattern-matching"></a>Exemplo de consulta: usando curtir/não gostaria de correspondência de padrões
**Descrição**: Verifique se o valor de um campo no evento corresponde a um determinado padrão por exemplo, retorno chapas de licença que começam com A e terminam com 9

**Entrada**:

| Fazer | LicensePlate | Tempo |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**Saída**:

| Fazer | LicensePlate | Tempo |
| --- | --- | --- |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**Solução**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**Explicação**: usar a instrução LIKE para verificar que o valor do campo LicensePlate começa com A depois tem qualquer cadeia de caracteres de zero ou mais e termina com 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Exemplo de consulta: especificar lógica para casos/valores diferentes (instruções maiusculas de MINÚSCULAS)
**Descrição**: forneça computação diferente para um campo com base em alguns critérios.
Por exemplo, forneça uma descrição de cadeia de caracteres para carros quantos passaram para fazer mesmo com um caso especial para 1.

**Entrada**:

| Fazer | Tempo |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Saída**:

| CarsPassed | Tempo |
| --- | --- | --- |
| 1 Honda | 2015-01-01T00:00:10.0000000Z |
| 2 Toyotas | 2015-01-01T00:00:10.0000000Z |

**Solução**:

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Explicação**: cláusula caso o nos permite fornecer um cálculo diferentes com base em alguns critérios (no nosso caso a contagem de carros na janela agregada).

## <a name="query-example-send-data-to-multiple-outputs"></a>Exemplo de consulta: enviar dados para várias saídas
**Descrição**: enviar dados para vários destinos de saída de um único trabalho.
Por exemplo, analisar os dados para um alerta baseado em limite e arquivar todos os eventos ao armazenamento de blob

**Entrada**:

| Fazer | Tempo |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output1**:

| Fazer | Tempo |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output2**:

| Fazer | Tempo | Contagem |
| --- | --- | --- |
| Toyota | 2015-01-01T00:00:10.0000000Z | 3 |

**Solução**:

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3

**Explicação**: cláusula o INTO informa a análise de fluxo de qual das saídas para gravar os dados da instrução.
A primeira consulta é uma passagem dos dados que recebemos uma saída que nomeamos ArchiveOutput.
A segunda consulta faz algumas agregação simple e filtragem e envia os resultados para um sistema de alertas downstream.
*Observação*: você também pode reutilizar resultados CTEs (isto é, com instruções) em várias instruções de saída – isso tem o benefício adicional de abertura menos leitores a origem da entrada.
Por exemplo, 

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## <a name="query-example-counting-unique-values"></a>Exemplo de consulta: contar valores exclusivos
**Descrição**: contar o número de valores de campo exclusivo que aparecem no fluxo de dentro de uma janela de tempo.
Por exemplo, quantos tornar exclusivo de carros passou o estande de Chamada Tarifada em uma segunda janela 2?

**Entrada**:

| Fazer | Tempo |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Saída:**

| Contagem | Tempo |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**Solução:**

    WITH Makes AS (
        SELECT
            Make,
            COUNT(*) AS CountMake
        FROM
            Input TIMESTAMP BY Time
        GROUP BY
              Make,
              TumblingWindow(second, 2)
    )
    SELECT
        COUNT(*) AS Count,
        System.TimeStamp AS Time
    FROM
        Makes
    GROUP BY
        TumblingWindow(second, 1)


**Explicação:** Fazemos uma agregação inicial para obter exclusivo faz com seu contagem sobre a janela.
Em seguida, fazemos uma agregação de quantos torna podemos adquiriu – fornecidos exclusivos valores em uma janela obter o carimbo de hora mesmo e a segunda janela de agregação precisa ser mínimo não agregar 2 windows da primeira etapa.

## <a name="query-example-determine-if-a-value-has-changed"></a>Exemplo de consulta: determinar se um valor foi alterado#
**Descrição**: Examine um valor anterior para determinar se ele é diferente do que o valor atual por exemplo, é o carro anterior viagens Chamada Tarifada a mesma fazer como o carro atual?

**Entrada**:

| Fazer | Tempo |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Saída**:

| Fazer | Tempo |
| --- | --- |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Solução**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Explicação**: uso RETARDO para inspecionar para a entrada transmitir um evento de volta e obter o valor de fazer. Em seguida, compará-lo com a fazer sobre o evento atual e o evento de saída se eles forem diferentes.

## <a name="query-example-find-first-event-in-a-window"></a>Exemplo de consulta: localizar primeiro evento em uma janela
**Descrição**: localizar primeiro carro em cada intervalo de 10 minutos?

**Entrada**:

| LicensePlate | Fazer | Tempo |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Saída**:

| LicensePlate | Fazer | Tempo |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |

**Solução**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

Agora vamos alterar o problema e localizar primeiro carro de determinada fazer em cada intervalo de 10 minutos.

| LicensePlate | Fazer | Tempo |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solução**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-last-event-in-a-window"></a>Exemplo de consulta: localizar último evento em uma janela
**Descrição**: localizar carro último em cada intervalo de 10 minutos.

**Entrada**:

| LicensePlate | Fazer | Tempo |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Saída**:

| LicensePlate | Fazer | Tempo |
| --- | --- | --- |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solução**:

    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**Explicação**: há duas etapas na consulta – primeiro localiza carimbo de hora mais recente no windows de 10 minutos. A segunda etapa une os resultados da primeira consulta com fluxo original para localizar eventos últimos carimbos de hora em cada janela de correspondência. 

## <a name="query-example-detect-the-absence-of-events"></a>Exemplo de consulta: detectar ausência de eventos
**Descrição**: Verifique se um fluxo não tem nenhum valor que corresponde a determinados critérios.
Por exemplo, a 2 carros consecutivos desde a mesma inseriu viagens Chamada Tarifada em 90 segundos?

**Entrada**:

| Fazer | LicensePlate | Tempo |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Honda | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Toyota | DEF-987 | 2015-01-01T00:00:03.0000000Z |
| Honda | GHI 345 | 2015-01-01T00:00:04.0000000Z |

**Saída**:

| Fazer | Tempo | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda | 2015-01-01T00:00:02.0000000Z | AAA-999 | ABC-123 | 2015-01-01T00:00:01.0000000Z |

**Solução**:

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**Explicação**: uso RETARDO para inspecionar para a entrada transmitir um evento de volta e obter o valor de fazer. Em seguida, compará-lo com a fazer sobre o evento atual e o evento de saída se eles são os mesmos e usam RETARDO para obter dados sobre o carro anterior.

## <a name="query-example-detect-duration-between-events"></a>Exemplo de consulta: detectar duração entre os eventos
**Descrição**: localizar a duração de um determinado evento. Por exemplo, dada uma sequência de cliques web determine o tempo gasto em um recurso.

**Entrada**:  
  
| Usuário | Recurso | Evento | Tempo |
| --- | --- | --- | --- |
| user@location.com | RightMenu | Iniciar | 2015-01-01T00:00:01.0000000Z |
| user@location.com | RightMenu | Final | 2015-01-01T00:00:08.0000000Z |
  
**Saída**:  
  
| Usuário | Recurso | Duração |
| --- | --- | --- |
| user@location.com | RightMenu | 7 |
  

**Solução**

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**Explicação**: a função última Use para recuperar o último valor de tempo ao tipo de evento foi 'Iniciar'. Observe que a última função usa partição por [usuário], para indicar que o resultado deverá ser calculado por usuário exclusivo.  A consulta tem um limite máximo de 1 hora para diferença de horário entre eventos 'Start' e 'Parar', mas é configurável como necessários (limite DURATION(hour, 1).

## <a name="query-example-detect-duration-of-a-condition"></a>Exemplo de consulta: detectar a duração de uma condição
**Descrição**: descobrir quanto tempo uma condição ocorreu para.
Por exemplo, suponha que um erro que resultou em todos os carros com um peso incorreto (acima de 20.000 libras) – queremos calcular a duração do bug.

**Entrada**:

| Fazer | Tempo | Peso |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | 2000 |
| Toyota | 2015-01-01T00:00:02.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:03.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:04.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:05.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:06.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:07.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:08.0000000Z | 2000 |

**Saída**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z | 2015-01-01T00:00:07.000Z |

**Solução**:

````
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
````

**Explicação**: uso RETARDO para exibir o fluxo de entrada de 24 horas e procure por instâncias onde StartFault e StopFault são ocupadas por peso < 20000.

## <a name="query-example-fill-missing-values"></a>Exemplo de consulta: preencher valores ausentes
**Descrição**: para o fluxo de eventos que têm valores ausentes, produzir um fluxo de eventos com intervalos regulares.
Por exemplo, gere evento 5 segundos que irá relatar o ponto de dados visto mais recentemente.

**Entrada**:

| t | valor |
|--------------------------|-------|
| "2014-01-01T06:01:00" | 1 |
| "2014-01-01T06:01:05" | 2 |
| "2014-01-01T06:01:10" | 3 |
| "2014-01-01T06:01:15" | 4 |
| "2014-01-01T06:01:30" | 5 |
| "2014-01-01T06:01:35" | 6 |

**Saída (10 primeiros linhas)**:

| windowend | lastevent.t | lastevent.Value |
|--------------------------|--------------------------|--------|
| 2014-01-01T14:01:00.000Z | 2014-01-01T14:01:00.000Z | 1 |
| 2014-01-01T14:01:05.000Z | 2014-01-01T14:01:05.000Z | 2 |
| 2014-01-01T14:01:10.000Z | 2014-01-01T14:01:10.000Z | 3 |
| 2014-01-01T14:01:15.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:20.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:25.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:30.000Z | 2014-01-01T14:01:30.000Z | 5 |
| 2014-01-01T14:01:35.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:40.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:45.000Z | 2014-01-01T14:01:35.000Z | 6 |

    
**Solução**:

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Explicação**: essa consulta gerará eventos cada segundo 5 e será o último evento que foi recebido antes de saída. [Janela de saltos] Duração da (https://msdn.microsoft.com/library/dn835041.aspx "Janela de saltos - Azure fluxo Analytics") determina quanto back ficará a consulta para localizar o evento mais recente (300 segundos neste exemplo).


## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 
