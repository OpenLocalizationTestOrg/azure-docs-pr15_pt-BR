<properties
    pageTitle="Automaticamente escala calcular nós em um pool de lote Azure | Microsoft Azure"
    description="Habilite o dimensionamento automático em um pool de nuvem para ajustar dinamicamente o número de nós de computação no pool."
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="batch"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="multiple"
    ms.date="10/14/2016"
    ms.author="marsma"/>

# <a name="automatically-scale-compute-nodes-in-an-azure-batch-pool"></a>Dimensionar automaticamente nós de computação em um pool de lote do Azure

Com o dimensionamento automático, é possível que o serviço do Azure lote dinamicamente pode adicionar ou remover nós de computação em um pool com base nos parâmetros que você definir. Você pode salvar potencialmente tempo e o dinheiro ajustando automaticamente a quantidade de energia de computação usada pelo seu aplicativo - Adicionar nós como aumento de exigências de tarefa do seu trabalho e, em seguida, removê-los quando eles diminuem.

Habilitar o dimensionamento automático em um pool de nós de computação associando-uma *fórmula de escala automática* que você define, tais como com a [PoolOperations.EnableAutoScale] [ net_enableautoscale] método na biblioteca de [Lote .NET](batch-dotnet-get-started.md) . O serviço de lote usa esta fórmula para determinar o número de nós de computação que são necessários para executar sua carga de trabalho. Lote responde às métricas de serviço amostras de dados que são coletadas periodicamente e ajusta o número de nós de computação no conjunto em um intervalo configurável com base em sua fórmula.

Você pode habilitar o dimensionamento automático quando um pool é criado, ou em um pool existente. Você também pode alterar uma fórmula existente em um pool "escala automática" ativada. Lote fornece a capacidade de avaliar suas fórmulas antes de atribuí-los aos pools, bem como monitorar o status de execuções de dimensionamento automáticos.

## <a name="automatic-scaling-formulas"></a>Fórmulas de dimensionamento automáticas

Uma fórmula de dimensionamento automática é um valor de cadeia de caracteres que você definir que contém uma ou mais instruções e é atribuído a de um pool [autoScaleFormula] [ rest_autoscaleformula] elemento (lote restante) ou [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] propriedade (lote .NET). Quando atribuído a um pool, o serviço de lote usa sua fórmula para determinar o número de destino de nós de computação no pool para o próximo intervalo de processamento (mais intervalos mais tarde). A cadeia de caracteres de fórmula não pode exceder 8 KB no tamanho, pode incluir até 100 instruções que são separadas por ponto e vírgula e podem incluir quebras de linha e comentários.

Você pode pensar em fórmulas de dimensionamento automáticas como usando uma escala de automática do lote "idioma". Instruções de fórmula são expressões formado livre que podem incluir tanto definidas pelo serviço variáveis (variáveis definidas pelo serviço lote) e definidos pelo usuário (variáveis que você definir). Eles podem realizar várias operações com esses valores usando funções, operadores e tipos internos. Por exemplo, uma instrução pode demorar da seguinte forma:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Fórmulas geralmente contêm várias instruções que realizam operações em valores que são obtidos nas instruções anterior. Por exemplo, primeiro podemos obter um valor para `variable1`, então, passe para uma função para preencher `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Com estas instruções em sua fórmula, seu objetivo é chegar a um número de nós de computação que o pool deve ser ajustado para-- o número de **destino** de **nós dedicado**. Esse número pode ser superior, inferior ou igual ao número atual de nós no pool. Lote avalia a fórmula de escala automática de um pool em um intervalo específico ([intervalos de dimensionamento automáticos](#automatic-scaling-interval) são discutido abaixo). Em seguida, ele irá ajustar o número de destino de nós do pool do número que especifica a fórmula de escala automática na ocasião da avaliação.

Como um exemplo rápido, essa fórmula escala automática de duas linhas Especifica que o número de nós deve ser ajustado de acordo com o número de tarefas ativas, até no máximo 10 nós de computação:

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

As próximas seções deste artigo discutam as várias entidades que irão compor o suas fórmulas de escala automática, incluindo variáveis, operadores, operações e funções. Você descobrirá como obter várias métricas de recurso e tarefa de computação em lote. Você pode usar essas métricas para ajustar inteligente contagem de nós do seu pool com base no status de tarefa e de uso do recurso. Em seguida, você aprenderá como construir uma fórmula e habilitar o dimensionamento automático em um pool usando o lote REST e APIs do .NET. Podemos vai terminar para cima com algumas fórmulas de exemplo.

> [AZURE.IMPORTANT] Cada conta do Azure lote está limitada a um número máximo de cores (e, portanto, nós de computação) que pode ser usado para processamento. O serviço de lote criará nós apenas até esse limite de núcleo. Portanto, ele não pode atingir o número de destino de nós de computação especificado por uma fórmula. Consulte [cotas e limites para o serviço de lote do Azure](batch-quota-limit.md) para obter informações sobre a visualização e aumentando suas cotas de conta.

## <a name="variables"></a>Variáveis

Você pode usar variáveis tanto **definidas pelo serviço** e **definidos pelo usuário** em suas fórmulas de escala automática. As serviço definido variáveis são criadas para o serviço de lote – alguns são leitura / gravação e algumas são somente leitura. Variáveis definidas pelo usuário são variáveis que *você* definir. Na fórmula de exemplo de duas linhas acima, `$TargetDedicated` é um serviço definido pelo variável, enquanto `$averageActiveTaskCount` é uma variável definida pelo usuário.

As tabelas abaixo mostram variáveis de leitura e gravação tanto somente leitura que são definidas pelo serviço lote.

Você pode **obter** e **Definir** os valores dessas variáveis definidas pelo serviço para gerenciar o número de nós de computação em um pool:

| Variáveis definidas pelo serviço de leitura e gravação | Descrição |
| --- | --- |
| $TargetDedicated | O número de **destino** de **dedicada nós de computação** para o pool. Este é o número de nós de computação que o pool deve ser dimensionado para. É um número de "destino" como é possível para um pool de não alcançar o número de destino de nós. Isso pode ocorrer se o número de destino de nós é modificado novamente por uma avaliação de escala automática subsequentes antes do pool atingiu o destino inicial. Ele também pode acontecer se uma cota de nó ou núcleo de conta do lote for atingida antes que o número de destino de nós é alcançado. |
| $NodeDeallocationOption | A ação que ocorre quando nós de computação são removidos de um pool. Valores possíveis são:<ul><li>**requeue**– encerra tarefas imediatamente e coloca na fila de trabalho para que eles são reprogramados.<li>**Encerrar**– encerra tarefas imediatamente e remove-os da fila de trabalho.<li>**taskcompletion**– aguarda em execução para concluir as tarefas e, em seguida, remove o nó do pool.<li>**retaineddata**– aguarda todos os dados locais mantidos tarefa no nó para ser limpos antes de remover o nó do pool.</ul> |

Você pode **obter** o valor dessas variáveis definidas pelo serviço para fazer ajustes que são baseados em métricas do serviço do lote:

| Variáveis definidas pelo serviço de somente leitura | Descrição |
| --- | --- |
| $CPUPercent | A porcentagem média de uso da CPU. |
| $WallClockSeconds | O número de segundos consumidas. |
| $MemoryBytes | O número médio de megabytes usado. |
| $DiskBytes | O número médio de gigabytes usados nos discos locais. |
| $DiskReadBytes | O número de bytes lidos. |
| $DiskWriteBytes | O número de bytes gravados. |
| $DiskReadOps | A contagem de leitura de disco operações executadas. |
| $DiskWriteOps | A contagem de gravação disco operações executadas. |
| $NetworkInBytes | O número de bytes de entrada. |
| $NetworkOutBytes | O número de bytes de saída. |
| $SampleNodeCount | A contagem de nós de computação. |
| $ActiveTasks | O número de tarefas em um estado ativo. |
| $RunningTasks | O número de tarefas em um estado de execução. |
| $PendingTasks | A soma de $ActiveTasks e $RunningTasks. |
| $SucceededTasks | O número de tarefas que foi concluída com êxito. |
| $FailedTasks | O número de tarefas que falhou. |
| $CurrentDedicated | O número atual de dedicado nós de computadores. |

> [AZURE.TIP] As variáveis de somente leitura, definidos pelo serviço mostrados acima são *objetos* que fornecem vários métodos para acessar os dados associados a cada. Consulte [obter dados de exemplo](#getsampledata) abaixo para obter mais informações.

## <a name="types"></a>Tipos de

Esses **tipos** são suportados em uma fórmula.

- duplo
- doubleVec
- doubleVecList
- cadeia de caracteres
- carimbo de hora, o carimbo de hora é uma estrutura composta que contém os seguintes membros:

    - ano
    - mês (1-12)
    - dia (1-31)
    - Weekday (no formato de número, por exemplo, 1 para segunda-feira)
    - hora (no formato de número de 24 horas, por exemplo, 13 significa 1 PM)
    - minuto (de 00 a 59)
    - segundo (de 00 a 59)
- timeInterval

    - TimeInterval_Zero
    - TimeInterval_100ns
    - TimeInterval_Microsecond
    - TimeInterval_Millisecond
    - TimeInterval_Second
    - TimeInterval_Minute
    - TimeInterval_Hour
    - TimeInterval_Day
    - TimeInterval_Week
    - TimeInterval_Year

## <a name="operations"></a>Operações

Essas **operações** são permitidas nos tipos que estão listados acima.

| Operação                             | Operadores com suporte   | Tipo de resultado   |
| ------------------------------------- | --------------------- | ------------- |
| Double *operador* duplo              | +, -, *, /            | duplo            |
| *operador* de dupla timeinterval        | *                     | timeInterval      |
| doubleVec *operador* duplo           | +, -, *, /            | doubleVec         |
| doubleVec doubleVec de *operador*        | +, -, *, /            | doubleVec         |
| timeInterval *operador* duplo        | *, /                  | timeInterval      |
| timeInterval *operador* timeinterval  | +, -                  | timeInterval      |
| carimbo de hora do timeInterval *operador*     | +                     | carimbo de hora         |
| carimbo de hora *operador* timeinterval     | +                     | carimbo de hora         |
| carimbo de hora de *operador* de carimbo de hora        | -                     | timeInterval      |
| *operador*duplo                      | -, !                  | duplo            |
| *operador*timeinterval                | -                     | timeInterval      |
| Double *operador* duplo              | < < =, = =, > =, >,! =  | duplo            |
| cadeia de caracteres de *operador* de cadeia de caracteres              | < < =, = =, > =, >,! =  | duplo            |
| carimbo de hora de *operador* de carimbo de hora        | < < =, = =, > =, >,! =  | duplo            |
| timeInterval *operador* timeinterval  | < < =, = =, > =, >,! =  | duplo            |
| Double *operador* duplo              | & &, & #124; & #124;      | duplo            |

Ao testar um duplo com um operador ternário (`double ? statement1 : statement2`), diferente de zero é **verdadeira**e zero for **falsa**.

## <a name="functions"></a>Funções

Essas **funções** de predefinidos estão disponíveis para serem usados na definição de uma fórmula de dimensionamento automática.

| Função                          | Tipo de retorno   | Descrição
| --------------------------------- | ------------- | --------- |
| Avg(doubleVecList)                | duplo        | Retorna o valor médio para todos os valores na doubleVecList.
| Len(doubleVecList)                | duplo        | Retorna o comprimento do vetor que é criado a partir do doubleVecList.
| LG(Double)                        | duplo        | Retorna o log de base 2 de duplas.
| LG(doubleVecList)                 | doubleVec     | Retorna o log de componentwise base 2 da doubleVecList. Um vec(double) explicitamente deve ser passado para o parâmetro. Caso contrário, a versão de lg(double) duplo será considerada.
| ln(Double)                        | duplo        | Retorna o log natural de duplas.
| ln(doubleVecList)                 | doubleVec     | Retorna o log de componentwise base 2 da doubleVecList. Um vec(double) explicitamente deve ser passado para o parâmetro. Caso contrário, a versão de lg(double) duplo será considerada.
| log(Double)                       | duplo        | Retorna o log de base 10 de duplas.
| log(doubleVecList)                | doubleVec     | Retorna o log de componentwise base 10 da doubleVecList. Um vec(double) explicitamente deve ser passado para o parâmetro duplo único. Caso contrário, a versão de log(double) duplo será considerada.
| Max(doubleVecList)                | duplo        | Retorna o valor máximo na doubleVecList.
| min(doubleVecList)                | duplo        | Retorna o valor mínimo na doubleVecList.
| NORM(doubleVecList)               | duplo        | Retorna a regra de dois do vetor que é criado a partir do doubleVecList.
| percentil (v doubleVec, p dupla) | duplo        | Retorna o elemento de percentil do vetor v.
| aleatório)                            | duplo        | Retorna um valor aleatório entre 0,0 e 1,0.
| Range(doubleVecList)              | duplo        | Retorna a diferença entre os valores mínimo e máximo na doubleVecList.
| STD(doubleVecList)                | duplo        | Retorna o desvio padrão da amostra dos valores na doubleVecList.
| Stop)                            |               | Paradas avaliação da expressão autoscaling.
| SUM(doubleVecList)                | duplo        | Retorna a soma de todos os componentes da doubleVecList.
| hora (dateTime de cadeia de caracteres = "")          | carimbo de hora     | Retorna o carimbo de hora da hora atual se forem passados sem parâmetros, ou o carimbo de hora da cadeia de caracteres dateTime se ele é passado. Formatos de dateTime compatível são W3C-DTF e RFC 1123.
| Val (v doubleVec, i duplo)        | duplo        | Retorna o valor do elemento que é local i em vetor v, com um índice inicial igual a zero.

Algumas das funções que são descritas na tabela acima podem aceitar uma lista como um argumento. A lista separados por vírgulas é qualquer combinação de *dupla* e *doubleVec*. Por exemplo:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

O valor de *doubleVecList* é convertido em um único *doubleVec* antes da avaliação. Por exemplo, se `v = [1,2,3]`, em seguida, chamar `avg(v)` equivale a chamar `avg(1,2,3)`. Chamando `avg(v, 7)` equivale a chamar `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obter dados de exemplo

Fórmulas de escala automática agem em dados de métricas (exemplos) que são fornecidos pelo serviço lote. Uma fórmula aumenta ou diminui o tamanho de pool com base nos valores que ele obtém do serviço. As variáveis definidas pelo serviço descritas acima são objetos que fornecem vários métodos para acessar os dados que está associados a esse objeto. Por exemplo, a expressão a seguir mostra uma solicitação para obter os últimos cinco minutos de uso da CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Método | Descrição |
| --- | --- |
| GetSample() | O `GetSample()` método retorna um vetor de exemplos de dados.<br/><br/>Uma amostra é 30 segundos patrimônio dos dados de métricas. Em outras palavras, amostras são obtidas a cada 30 segundos. Mas conforme descrito abaixo, há um atraso entre quando uma amostra é coletada e quando ela está disponível para uma fórmula. Assim, não todas as amostras para um determinado período de tempo podem estar disponíveis para avaliação por uma fórmula.<ul><li>`doubleVec GetSample(double count)`<br/>Especifica o número de amostras para obter a partir dos exemplos mais recentes que foram coletados.<br/><br/>`GetSample(1)`Retorna a última amostra disponível. Para métricas como `$CPUPercent`, no entanto, isso não deve ser usado porque é impossível saber *quando* a amostra foi coletada. Talvez seja recente ou, devido a problemas do sistema, pode ser muito mais antigo. É melhor nesses casos para usar um intervalo de tempo, conforme mostrado abaixo.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Especifica um intervalo de tempo para coleta de dados de exemplo. Opcionalmente, ele também especifica a porcentagem de amostras que devem estar disponíveis no período solicitado.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`Retorna 20 amostras se todas as amostras para últimos dez minutos estiverem presentes no histórico de CPUPercent. No entanto, se o último minuto do histórico não estava disponível, somente 18 exemplos seriam retornados. Neste caso:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`falhará porque somente 90% dos exemplos estão disponíveis.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`seria bem-sucedida.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Especifica um período de tempo de coleta de dados, com uma hora de início e uma hora de término.<br/><br/>Conforme mencionado acima, não há um atraso entre quando uma amostra é coletada e quando ela está disponível para uma fórmula. Isso deve ser considerado quando você usa o `GetSample` método. Consulte `GetSamplePercent` abaixo.|
| GetSamplePeriod() | Retorna o período de amostras feitas em um conjunto de dados de amostra histórica. |
| Count) | Retorna o número total de amostras no histórico de métrica. |
| HistoryBeginTime() | Retorna o carimbo de hora da amostra de dados disponíveis mais antiga para a métrica. |
| GetSamplePercent() |Retorna a porcentagem de amostras que estão disponíveis para um intervalo de tempo determinado. Por exemplo:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Porque o `GetSample` método falhará se a porcentagem de amostras retornada for menor do que o `samplePercent` especificado, você pode usar o `GetSamplePercent` método para verificar primeiro. Em seguida, você pode executar uma ação alternativa se amostras insuficientes estiverem presentes, sem parar a avaliação de dimensionamento automática.|

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Exemplos, a porcentagem de exemplo e o método de *GetSample()*

A operação de núcleo de uma fórmula de escala automática é obter dados de métrica tarefas e recursos e ajuste o tamanho do pool baseado nos dados. Assim, é importante ter uma compreensão clara de como fórmulas de escala automática interagem com dados de métricas ou "amostras".

**Amostras**

O serviço de lote periodicamente leva *exemplos* de métricas de tarefas e recursos e os disponibiliza suas fórmulas de escala automática. Esses exemplos são registrados a cada 30 segundos pelo serviço lote. No entanto, normalmente há alguns latência que causa um atraso entre quando esses exemplos foram registrados e quando eles são disponibilizados para (e podem ser lidos por) suas fórmulas de escala automática. Além disso, devido a vários fatores como rede ou outros problemas de infraestrutura, amostras podem não ter sido registradas por um determinado intervalo. Isso resulta em amostras "ausentes".

**Porcentagem de amostra**

Quando `samplePercent` é passado para o `GetSample()` método ou o `GetSamplePercent()` método é chamado, "%" se refere a uma comparação entre o número total *possível* de amostras que são registrados pelo serviço lote e o número de amostras realmente *disponíveis* para sua fórmula escala automática.

Vamos examinar um período de tempo de 10 minutos como exemplo. Como exemplos são registrados a cada 30 segundos, dentro de um período de tempo de 10 minutos, o número máximo de total de exemplos que são registrados por lote seria 20 amostras (2 por minuto). No entanto, devido a latência inerente do mecanismo de relatórios ou algum outro problema na infraestrutura do Azure, pode haver apenas 15 amostras que estão disponíveis para sua fórmula escala automática para leitura. Isso significa que, para esse período de 10 minutos, somente **75 por cento** do número total de amostras gravados são realmente disponíveis para sua fórmula.

**Intervalos de GetSample() e amostras**

As fórmulas de escala automática vai ser aumentando e diminuindo os pools – nós de adicionar ou remover nós. Como nós custam você, convém garantir que suas fórmulas usem um método inteligente de análise com base em dados suficientes. Portanto, recomendamos que você use uma análise de tipo de tendências em suas fórmulas. Esse tipo será ampliar ou reduzir os pools com base em um *intervalo* de amostras recolhidas.

Para fazer isso, use `GetSample(interval look-back start, interval look-back end)` para retornar um **vetor** de exemplos:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Quando a linha acima é avaliada por lote, ela retornará um intervalo de amostras como um vetor de valores. Por exemplo:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Depois que você já coletadas o vetor de amostras, você pode usar funções como `min()`, `max()`, e `avg()` para gerar valores significativos do intervalo coletado.

Para ter mais segurança, você pode forçar uma avaliação de fórmulas *Falha* se menor do que um determinado percentual de amostra está disponível para um determinado período. Quando você forçar uma avaliação de fórmulas falha, instruir o lote ainda mais parem avaliação da fórmula se a porcentagem especificada de amostras não estiver disponível, e não altera o tamanho do pool será feita. Para especificar uma porcentagem necessária de amostras para a avaliação tenha êxito, especifique-o como o terceiro parâmetro para `GetSample()`. Aqui, um requisito de 75% de amostras é especificado:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Também é importante, devido o mencionado anteriormente atraso na disponibilidade de amostra, sempre especificar um intervalo de tempo com uma hora de início de aparência-back anteriores a um minuto. Isso ocorre porque ele leva aproximadamente um minuto para exemplos propagar por meio do sistema, portanto amostras no intervalo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` geralmente não estarão disponíveis. Novamente, você pode usar o parâmetro de porcentagem de `GetSample()` para forçar um requisito de porcentagem de exemplo em particular.

> [AZURE.IMPORTANT] É **altamente recomendável** que você * *Evite contar *apenas* em `GetSample(1)` em suas fórmulas de escala automática * *. Isso ocorre porque `GetSample(1)` essencialmente diz para o serviço de lote, "Dê a última amostra que você tem, independentemente de como tempo atrás obtido." Como é apenas uma única amostra, e pode ser uma amostra mais antiga, talvez não seja representante da imagem maior de tarefa recente ou estado do recurso. Se você usar `GetSample(1)`, certifique-se de que ela faz parte de uma instrução maior e não o ponto de apenas os dados que depende de sua fórmula.

## <a name="metrics"></a>Métricas

Você pode usar métricas de **recurso** e **tarefa** quando você estiver definindo uma fórmula. Ajustar o número de destino de nós dedicado no pool baseado nos dados métricas que você obtém e avaliar. Consulte a seção de [variáveis](#variables) acima para obter mais informações sobre cada métrica.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><b>Recurso</b></td>
    <td><p><b>Métricas de recurso</b> são baseados no uso da CPU, largura de banda e memória de nós de computação, bem como o número de nós.</p>
        <p> Essas variáveis definidas pelo serviço são úteis para fazer ajustes com base na contagem de nós:</p>
    <p><ul>
      <li>$TargetDedicated</li>
            <li>$CurrentDedicated</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Essas variáveis definidas pelo serviço são úteis para fazer ajustes com base no uso de recursos de nó:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Tarefa</b></td>
    <td><p><b>Métricas de tarefas</b> estiver baseado no status de tarefas, como ativo, pendente e concluído. As seguintes variáveis definidas pelo serviço são úteis para fazer ajustes de tamanho do pool com base em métricas de tarefa:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Escrever uma fórmula de escala automática

Você cria uma fórmula de escala automática formando instruções que usam os componentes acima, e combina essas instruções em uma fórmula completa. Nesta seção, vamos criar uma fórmula de escala automática de exemplo que pode realizar algumas decisões de dimensionamento reais.

Primeiro, vamos definir os requisitos para nossa nova fórmula de escala automática. A fórmula deve:

1. **Aumentar** o número de destino de nós de computação em um pool se o uso da CPU é alto.
2. **Diminuir** o número de destino de nós de computação em um pool quando o uso da CPU for baixo.
3. Sempre restringir o número **máximo** de nós a 400.

Para *aumentar* o número de nós durante o uso de CPU alto, definimos a instrução que preenche uma variável definida pelo usuário (`$totalNodes`) com um valor que é 110 por cento do número destino atual de nós, mas apenas se o uso da CPU médio mínimo durante os últimos 10 minutos foi acima de 70%. Caso contrário, usamos o valor dedicado atual.

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
```

Para *Diminuir* o número de nós durante o uso de CPU baixo, a próxima instrução em nossa fórmula define a mesma `$totalNodes` variável para 90% do número de destino atual de nós se o uso da CPU médio nos últimos 60 minutos em 20%. Caso contrário, use o valor atual da `$totalNodes` que estamos preenchida na instrução acima.

```
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
```

Agora limite o número de destino de nós de computação dedicada e um **máximo** de 400:

```
$TargetDedicated = min(400, $totalNodes)
```

Aqui está a fórmula completa:

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
$TargetDedicated = min(400, $totalNodes)
```

## <a name="create-an-autoscale-enabled-pool"></a>Criar um pool de habilitados em escala automática

Para criar um novo pool com autoscaling ativado, você pode usar uma das seguintes técnicas:

**Lote .NET**

1. Crie o pool com [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx).
1. Defina a propriedade [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) como `true`.
1. Defina a propriedade de [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) com a fórmula de escala automática.
1. (Opcional) Defina a propriedade [CloudPool.AutoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoScaleevaluationinterval.aspx) (o padrão é de 15 minutos).
1. Confirme o pool com [CloudPool.Commit](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commit.aspx) ou [CommitAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commitasync.aspx).

**Lote API REST**

* [Adicionar um pool a uma conta](https://msdn.microsoft.com/library/azure/dn820174.aspx): especificar o `enableAutoScale` e `autoScaleFormula` elementos em sua solicitação da API REST para configurar o dimensionamento automático para um pool quando criá-lo.

O trecho de código a seguir cria um pool de habilitados em escala automática usando o [Lote .NET] [ net_api] biblioteca. Fórmula de escala automática do pool define o número de destino de nós como 5 em segundas e 1 em todos os outros dias da semana. O [intervalo de dimensionamento automático](#automatic-scaling-interval) está definido como 30 minutos. Neste e os outros trechos c# neste artigo, "myBatchClient" é uma instância corretamente inicializada do [BatchClient][net_batchclient].

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

Além da API REST de lote e .NET SDK, você pode usar qualquer um dos outros [SDKs lote](batch-technical-overview.md#batch-development-apis), [cmdlets do PowerShell do lote](batch-powershell-cmdlets-get-started.md)e o [Lote CLI](batch-cli-get-started.md) para trabalhar com autoscaling.

> [AZURE.IMPORTANT] Quando você cria um pool habilitados em escala automática, você deve **não** especificar o `targetDedicated` parâmetro. Além disso, se você quiser manualmente redimensionar um pool habilitados em escala automática (por exemplo, com [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool]), em seguida, você deve primeiro **Desabilitar** automática de dimensionamento no pool de, em seguida, redimensioná-la.

### <a name="automatic-scaling-interval"></a>Intervalo de dimensionamento automático

Por padrão, o serviço de lote ajusta tamanho de um pool acordo com sua fórmula escala automática a cada **15 minutos**. Esse intervalo é configurável, no entanto, usando as seguintes propriedades do pool:

* [CloudPool.AutoScaleEvaluationInterval] [ net_cloudpool_autoscaleevalinterval] (lote .NET)
* [autoScaleEvaluationInterval] [ rest_autoscaleinterval] (API REST)

O intervalo mínimo é de cinco minutos e o máximo é 168 horas. Se um intervalo fora desse intervalo for especificado, o serviço de lote retornará um erro de solicitação incorreta (400).

> [AZURE.NOTE] AutoScaling não se destina, atualmente, responder a alterações em menos de um minuto, mas em vez disso, destina-se para ajustar o tamanho do seu pool gradualmente como executar uma carga de trabalho.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Habilitar autoscaling em um pool existente

Se você já tiver criado um pool com um número definido de nós de computação usando o parâmetro *targetDedicated* , você ainda pode habilitar autoscaling no pool. Cada lote SDK fornece uma operação de "Habilitar escala automática", por exemplo:

* [BatchClient.PoolOperations.EnableAutoScale] [ net_enableautoscale] (lote .NET)
*  [Habilitar o dimensionamento automático em um pool de] [ rest_enableautoscale] (API REST)

Quando você habilita autoscaling em um pool existente, aplica o seguinte:

* Se o dimensionamento automático estiver **desabilitada** no pool de quando você emitiu a solicitação de "Habilitar escala automática", você *deve* especificar uma fórmula de escala automática válida quando você executa a solicitação. Você pode *opcionalmente* especificar um intervalo de avaliação de escala automática. Se você não especificar um intervalo, o valor padrão de 15 minutos é usado.

* Se a escala automática está **ativada** no pool, você pode especificar uma fórmula de escala automática, um intervalo de avaliação ou ambos. Você não pode omitir ambas as propriedades.

  * Se você especificar um novo intervalo de avaliação de escala automática, o cronograma de avaliação existente for interrompido e uma nova programação é iniciada. Hora de início do agendamento novo é a hora em que a solicitação de "Habilitar escala automática" foi emitida.

  * Se você omitir a fórmula de escala automática ou intervalo de avaliação, o serviço de lote continua a usar o valor atual da configuração.

> [AZURE.NOTE] Se um valor foi especificado para o parâmetro *targetDedicated* quando o pool foi criado, ele será ignorado quando a fórmula de dimensionamento automática é avaliada.

Neste trecho de código c# usa o [Lote .NET] [ net_api] biblioteca para habilitar autoscaling em um pool existente:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Atualizar uma fórmula de escala automática

Você usar a mesma solicitação "Habilitar escala automática" para *Atualizar* a fórmula em um pool existente habilitado para escala automática (por exemplo, com [EnableAutoScale] [ net_enableautoscale] no lote .NET). Não há nenhuma operação especial "atualizar a escala automática". Por exemplo, se autoscaling já está ativado "myexistingpool" quando o seguinte código é executado, sua fórmula escala automática será substituída com o conteúdo de `myNewFormula`.

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Atualizar o intervalo de escala automática

Como com a atualização de uma fórmula de escala automática, você usa a mesma [EnableAutoScale] [ net_enableautoscale] método para alterar o intervalo de avaliação de escala automática de um pool existente habilitado para escala automática. Por exemplo, para definir o intervalo de avaliação de escala automática para 60 minutos de um pool que já está habilitado para escala automática:

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Avaliar uma fórmula de escala automática

Você pode avaliar uma fórmula antes de aplicá-la a um pool. Dessa forma, você pode executar um "teste executar" da fórmula para ver como suas declarações avaliar antes de colocar a fórmula em produção.

Para avaliar uma fórmula de escala automática, você deve primeiro **Habilitar autoscaling** no pool com uma **fórmula válida**. Se você quiser testar uma fórmula em um pool que ainda não tem autoscaling ativado, você pode usar a fórmula de uma linha `$TargetDedicated = 0` quando você primeiro habilita autoscaling. Em seguida, use um destes procedimentos para avaliar a fórmula que você deseja testar:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) ou [EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)

    Esses métodos de lote .NET exigem a identificação de um pool existente e uma cadeia de caracteres que contém a fórmula de escala automática deve ser avaliada. Os resultados da avaliação estão contidos na instância [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) retornada.

* [Avaliar uma fórmula de dimensionamento automática](https://msdn.microsoft.com/library/azure/dn820183.aspx)

    Nesta solicitação de API REST, especifique a ID do pool no URI e a fórmula de escala automática no elemento *autoScaleFormula* do corpo da solicitação. A resposta da operação contém qualquer informação de erro que possam estar relacionada à fórmula.

Neste [Lote .NET] [ net_api] trecho de código, podemos avaliar uma fórmula antes de aplicá-la a [CloudPool][net_cloudpool]. Se o pool não tiver autoscaling habilitado, podemos habilitá-lo primeiro.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = batchClient.PoolOperations.GetPool("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    pool.EnableAutoScale(
        autoscaleFormula: $"$TargetDedicated = {pool.CurrentDedicated};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScale calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    pool.Refresh();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicated = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        batchClient.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        batchClient.PoolOperations.EnableAutoScale(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Avaliação bem-sucedida da fórmula neste trecho resultarão em saída similar ao seguinte:

```
AutoScaleRun.Results:
    $TargetDedicated=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Obter informações sobre execuções de escala automática

Para garantir que sua fórmula está executando conforme o esperado, recomendamos que você verificar periodicamente os resultados do autoscaling "executa" lote executa em seu pool. Fique (ou atualizar) uma referência para o pool e examinar as propriedades de sua última escala automática executar.

No lote .NET, a propriedade [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) tem várias propriedades fornecendo informações sobre a executar realizado no pool pelo serviço lote mais recente de dimensionamento automático.

* [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
* [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
* [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)

Na API REST, a solicitação de [obter informações sobre um pool](https://msdn.microsoft.com/library/dn820165.aspx) retorna informações sobre o pool, que inclui as informações de execução no [autoScaleRun](https://msdn.microsoft.com/library/dn820165.aspx#bk_autrun)mais recente de dimensionamento automático.

O seguinte trecho de código em c# usa a biblioteca de lote .NET para imprimir informações sobre a última autoscaling executado em pool "myPool":

```csharp
Cloud pool = myBatchClient.PoolOperations.GetPool("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Exemplo de saída do trecho precedente:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicated=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Fórmulas de escala automática de exemplo

Vamos dar uma olhada em algumas fórmulas que mostram as diferentes maneiras para ajustar a quantidade de recursos de computação em um pool.

### <a name="example-1-time-based-adjustment"></a>Exemplo 1: Baseada no tempo ajuste

Talvez você queira ajustar o tamanho de pool com base no dia da semana e a hora do dia, para aumentar ou diminuir o número de nós do pool de acordo.

Essa fórmula primeiro obtém a hora atual. Se for um dia da semana (1-5) e em horas de trabalho (para 6: 00), o tamanho do pool de destino está definido como 20 nós. Caso contrário, ela é definida como 10 nós.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicated = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Exemplo 2: Ajuste de baseado em tarefas

Neste exemplo, o tamanho do pool é ajustado com base no número de tarefas na fila. Observe que as quebras de linha e de comentários são aceitáveis em cadeias de caracteres de fórmulas.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicated = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Exemplo 3: Contabilidade para tarefas paralelas

Este é outro exemplo que ajusta o tamanho de pool com base no número de tarefas. Essa fórmula também leva em conta a [MaxTasksPerComputeNode] [ net_maxtasks] valor que foi definida para o pool. Isso é especialmente útil em situações onde [a execução de tarefa paralela](batch-parallel-node-tasks.md) foi ativado o pool.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicated * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicated + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicated = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Exemplo 4: Definindo um tamanho de pool inicial

Este exemplo mostra um trecho de código c# com uma fórmula de escala automática que define o tamanho de pool para um determinado número de nós para um período inicial. Depois ele ajusta o tamanho do pool com base no número de execução e ativo após decorrido o período de tempo inicial de tarefas.

A fórmula no trecho de código a seguir:

- Define o tamanho do pool inicial como quatro nós.
- Não ajustar o tamanho do pool dentro dos primeiros 10 minutos do ciclo de vida do pool.
- Após 10 minutos, obtém o valor máximo do número de tarefas ativas e em execução nos últimos 60 minutos.
  - Se os dois valores são 0 (indicando que não há tarefas foram executando ou ativo nos últimos 60 minutos), o tamanho do pool está definido como 0.
  - Se qualquer valor for maior que zero, nenhuma alteração é feita.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicated = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Próximas etapas

* [Maximizar Azure lote Calcular uso de recursos com tarefas de nó simultâneas](batch-parallel-node-tasks.md) contém detalhes sobre como você pode executar várias tarefas simultaneamente em nós computação seu pool. Além de auto-dimensionamento, esse recurso pode ajudar a reduzir a duração da tarefa para algumas cargas de trabalho, economizando.

* Para auxiliar de eficiência do outro, certifique-se de que seu aplicativo de lote consulta o serviço de lote da melhor forma. [Consultar o serviço do Azure lote com eficiência](batch-efficient-list-queries.md), você aprenderá limitar a quantidade de dados que cruza o fio quando você consulta o status de milhares de nós de computação ou tarefas.

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx
