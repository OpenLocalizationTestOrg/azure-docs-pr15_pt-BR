<properties 
   pageTitle="Tipos de Runbook de automação Azure"
   description="Descreve os diferentes tipos de runbooks que você pode usar em automação do Azure e considerações que você deve levar em consideração ao determinar que tipo para usar. "
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/12/2016"
   ms.author="bwren" />

# <a name="azure-automation-runbook-types"></a>Tipos de runbook automação Azure

Automação Azure oferece suporte a quatro tipos de runbooks brevemente descritos na tabela a seguir.  As seções a seguir fornecem mais informações sobre cada tipo incluindo considerações sobre quando usar cada um.


| Tipo |  Descrição |
|:---|:---|
| [Gráfica](#graphical-runbooks) | Com base no Windows PowerShell e criado e editado completamente no editor gráfico no portal do Azure. | 
| [Fluxo de trabalho de PowerShell gráfica](#graphical-runbooks) | Com base em fluxo de trabalho do Windows PowerShell e criados e editados completamente no editor de gráficos no portal do Azure. 
| [PowerShell](#powershell-runbooks) | Runbook de texto com base em script do Windows PowerShell.
| [Fluxo de trabalho do PowerShell](#powershell-workflow-runbooks) | Runbook de texto com base em fluxo de trabalho do Windows PowerShell. |


## <a name="graphical-runbooks"></a>Runbooks gráfica

Runbooks [Graphical](automation-runbook-types.md#graphical-runbooks) e gráficas fluxo de trabalho do PowerShell são criados e editados com o editor de gráfico no portal do Azure.  Você pode exportá-los para um arquivo e, em seguida, importá-las para outra conta de automação, mas você não pode criar ou editá-los com outra ferramenta.  Runbooks gráficas gerar código do PowerShell, mas é possível exibir ou modificar o código diretamente. Runbooks gráfica não pode ser convertido em um dos [formatos de texto](automation-runbook-types.md), nem um runbook de texto pode ser convertido em formato gráfico. Runbooks gráfica pode ser convertido em gráficas fluxo de trabalho do PowerShell runbooks durante a importação e vice-versa.

### <a name="advantages"></a>Vantagens

- Crie runbooks com o mínimo de conhecimento do [PowerShell](automation-powershell-workflow.md).
- Representa visualmente os processos de gerenciamento.
- Inclua outros runbooks como filho runbooks para criar fluxos de trabalho de níveis altos.


### <a name="limitations"></a>Limitações

- Não é possível editar runbook fora do portal do Azure.
- Pode exigir uma atividade de código que contém o código do PowerShell para executar lógica complexa.
- Não é possível exibir ou editar diretamente o código do PowerShell que é criado pelo fluxo de trabalho de gráfico. Observe que você pode exibir o código que você cria em quaisquer atividades de código.


## <a name="powershell-runbooks"></a>PowerShell runbooks

PowerShell runbooks são baseados no Windows PowerShell.  Você editar diretamente o código do runbook usando o editor de texto no portal do Azure.  Você também pode usar qualquer editor de texto offline e [Importar runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) automação do Azure.

### <a name="advantages"></a>Vantagens

- Implemente toda a lógica complexa com código do PowerShell sem as complexidades adicionais de fluxo de trabalho do PowerShell. 
- Runbook inicia mais rápido que runbooks Graphical ou fluxo de trabalho do PowerShell, desde que ele não precise ser compilado antes da execução.

### <a name="limitations"></a>Limitações

- Devem estar familiarizados com script do PowerShell.
- Não é possível usar [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar várias ações em paralelo.
- Não use [pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar runbook no caso de erro.
- Fluxo de trabalho do PowerShell runbooks e runbooks gráficas só é possível incluído como filho runbooks usando o cmdlet Start-AzureAutomationRunbook que cria uma nova tarefa.

### <a name="known-issues"></a>Problemas conhecidos
A seguir está atual dos problemas conhecidos com o PowerShell runbooks.

- PowerShell runbooks não pode não é possível recuperar um não criptografado [ativo variável](automation-variables.md) com um valor nulo.
- PowerShell runbooks não é possível recuperar uma [variável ativo](automation-variables.md) com *~* no nome.
- Get-Process em um loop em um PowerShell runbook pode travar depois 80 iterações. 
- Um runbook PowerShell pode falhar se ele tenta gravar uma grande quantidade de dados para o fluxo de saída ao mesmo tempo.   Normalmente você pode contornar esse problema por saindo apenas as informações que necessárias ao trabalhar com objetos grandes.  Por exemplo, em vez de saída algo como *Get-Process*, você pode gerar uma saída apenas os campos obrigatórios com *Get-Process | Selecione ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>Fluxo de trabalho do PowerShell runbooks

Fluxo de trabalho do PowerShell runbooks são runbooks de texto com base em [Fluxo de trabalho do Windows PowerShell](automation-powershell-workflow.md).  Você editar diretamente o código do runbook usando o editor de texto no portal do Azure.  Você também pode usar qualquer editor de texto offline e [Importar runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) automação do Azure.

### <a name="advantages"></a>Vantagens

- Implemente toda a lógica complexa com código de fluxo de trabalho do PowerShell.
- Use [pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar runbook no caso de erro.
- Use o [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar várias ações em paralelo.
- Pode incluir outros runbooks gráficas e fluxo de trabalho do PowerShell runbooks como filho runbooks para criar fluxos de trabalho de níveis altos.


### <a name="limitations"></a>Limitações

- Autor deve estar familiarizado com o fluxo de trabalho do PowerShell.
- Runbook precisa lidar com a complexidade adicional de fluxo de trabalho do PowerShell como [desserializado objetos](automation-powershell-workflow.md#code-changes).
- Runbook demora mais para iniciar de runbooks do PowerShell, pois precisará ser compilado antes da execução.
- PowerShell runbooks só pode ser incluído como filho runbooks usando o cmdlet Start-AzureAutomationRunbook que cria uma nova tarefa.


## <a name="considerations"></a>Considerações

Você deve levar em conta as seguintes considerações adicionais ao determinar que tipo para usar para uma determinada runbook.

- Não é possível converter runbooks do gráfico para o tipo de textual ou vice-versa.
- Há limitações usando runbooks de diferentes tipos como um runbook filho.  Consulte [runbooks filho na automação do Azure](automation-child-runbooks.md) para obter mais informações.

  
## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a criação de runbook gráfica, consulte [Graphical authoring na automação do Azure](automation-graphical-authoring-intro.md)
- Entender as diferenças entre o PowerShell e PowerShell fluxos de trabalho para runbooks, consulte [Learning Windows PowerShell Workflow](automation-powershell-workflow.md)
- Para obter mais informações sobre como criar ou importar um Runbook, consulte [criar ou importar um Runbook](automation-creating-importing-runbook.md)



