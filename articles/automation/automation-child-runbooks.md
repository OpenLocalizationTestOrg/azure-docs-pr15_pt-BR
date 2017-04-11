<properties 
   pageTitle="Runbooks filho na automação do Azure | Microsoft Azure"
   description="Descreve os diferentes métodos para iniciando um runbook no Azure automação da outra runbook e compartilhar informações entre elas."
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
   ms.date="08/17/2016"
   ms.author="magoedte;bwren" />

# <a name="child-runbooks-in-azure-automation"></a>Runbooks filho na automação do Azure

É uma prática recomendada no Azure automação escrever runbooks reutilizável, modular com uma função discreta que pode ser usada por outros runbooks. Um runbook pai geralmente chamará runbooks filho de uma ou mais para executar a funcionalidade necessária. Há duas maneiras para chamar um runbook filho e cada tem diferenças distintas que você deve compreender para que você possa determinar qual será melhor para seus cenários diferentes.

##  <a name="invoking-a-child-runbook-using-inline-execution"></a>Invocar um runbook filho usando execução embutido

Para chamar uma embutida runbook do runbook outro, use o nome do runbook e forneça os valores para seus parâmetros exatamente como você usaria uma atividade ou cmdlet.  Runbooks todos na mesma conta automação estarão disponíveis para todas as outras pessoas a ser usado dessa maneira. Runbook pai esperará runbook filho ser concluída antes de passar para a próxima linha, e qualquer saída é retornada diretamente para o pai.

Quando você invoca uma embutida runbook, ele é executado no mesmo trabalho como runbook pai. Não haverá nenhuma indicação no histórico de trabalho do runbook filho que executou a ele. As exceções e qualquer fluxo de saída do runbook filho será associados com o pai. Isso resulta em menos trabalhos e torna mais fácil para controlar e solucionar problemas de como as exceções geradas pelo runbook filho e qualquer de sua saída fluxo são associadas com o trabalho pai.

Quando um runbook é publicado, qualquer runbooks filho que ele chama já deve estar publicado. Isso ocorre porque o Azure automação cria uma associação com qualquer runbooks filho quando um runbook é compilada. Se não forem, runbook pai aparecerá para publicar corretamente, mas irá gerar uma exceção quando ele é iniciado. Se isso acontecer, você pode republicar runbook pai para referenciar corretamente o runbooks filho. Não é necessário republicar runbook pai se qualquer uma da runbooks filho forem alterados porque a associação terá já foi criada.

Os parâmetros de uma runbook filho chamado embutido podem ser qualquer tipo de dados, incluindo objetos complexos e há sem [serialização JSON](automation-starting-a-runbook.md#runbook-parameters) , pois há quando você inicia o runbook usando o Portal de gerenciamento do Azure ou com o cmdlet Start-AzureRmAutomationRunbook.


### <a name="runbook-types"></a>Tipos de runbook

Quais tipos podem chamar a si:

- Um [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) e [runbooks gráficas](automation-runbook-types.md#graphical-runbooks) podem chamar cada outra embutida (ambos são PowerShell com base).
- Um [fluxo de trabalho do PowerShell runbook](automation-runbook-types.md#powershell-workflow-runbooks) e gráficas fluxo de trabalho do PowerShell runbooks pode chamar cada outra embutida (ambos são fluxo de trabalho do PowerShell baseado)
- Os tipos de PowerShell os tipos de fluxo de trabalho do PowerShell não podem chamar a si embutida e devem usar AzureRmAutomationRunbook iniciar.
    
Quando publicar questão de pedido:

- A ordem de publicação de runbooks só é importante para o fluxo de trabalho do PowerShell e gráficas PowerShell runbooks.


Quando você chamar um runbook filho Graphical ou fluxo de trabalho do PowerShell usando embutida execução, você simplesmente usar o nome do runbook.  Quando você chamar um runbook de filho do PowerShell, você deve precedido seu nome com *.\\ * para especificar que o script está localizado no diretório local. 

### <a name="example"></a>Exemplo

O exemplo a seguir invoca um runbook filho de teste que aceita três parâmetros, um objeto complexo, um número inteiro e um booliano. A saída do runbook filho é atribuída a uma variável.  Nesse caso, runbook filho é um fluxo de trabalho do PowerShell runbook

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

Procedimento é o mesmo exemplo usando um runbook PowerShell como filho.

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = .\PS-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true



##  <a name="starting-a-child-runbook-using-cmdlet"></a>Iniciando um runbook filho usando cmdlet

Você pode usar o cmdlet [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) para iniciar uma runbook conforme descrito em [para iniciar uma runbook com o Windows PowerShell](../automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Há dois modos de uso para esse cmdlet.  Em um modo, o cmdlet retorna a identificação de trabalho assim que o trabalho filho é criado para runbook filho.  No outro modo, que permitem, especificando a **-aguardar** parâmetro, o cmdlet devem esperar até o filho trabalho termine e retornará a saída da runbook filho.

O trabalho de um runbook filho iniciado com um cmdlet será executado em um trabalho separado do runbook pai. Isso resulta em mais trabalhos que invocar o embutido runbook e torna mais difícil rastrear. O pai pode iniciar vários runbooks filho assíncrona sem aguardando para cada ser concluída. Para o mesmo tipo de execução paralela chamar o embutido de runbooks filho, runbook pai precisaria usar a [palavra-chave paralelo](automation-powershell-workflow.md#parallel-processing).

Parâmetros para um runbook filho iniciado com um cmdlet são fornecidos como uma tabela de hash, conforme descrito em [Runbook parâmetros](automation-starting-a-runbook.md#runbook-parameters). Tipos de dados simples só podem ser usados. Se runbook tem um parâmetro com um tipo de dados complexos, em seguida, ele deve ser chamado embutida.

### <a name="example"></a>Exemplo

O exemplo a seguir inicia uma runbook filho com parâmetros e aguarda a conclusão usando o início-AzureRmAutomationRunbook-aguardar parâmetro. Quando concluído, sua saída coletada do runbook filho.

    $params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
    $joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResouceGroupName "LabRG" –Parameters $params –wait


## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Comparação de métodos para chamar um runbook filho

A tabela a seguir resume as diferenças entre os dois métodos para chamar um runbook do runbook outra.

| | Embutido| Cmdlet|
|:---|:---|:---|
|Trabalho|Filho runbooks executar no mesmo trabalho como pai.|Um trabalho separado é criado para runbook filho.|
|Execução|Pai runbook aguarda runbook filho ser concluída antes de continuar.|Pai runbook continua imediatamente após runbook filho é iniciado *ou* runbook pai aguarda o trabalho filho concluir.|
|Saída|Pai runbook diretamente pode obter a saída do runbook filho.|Pai runbook deve recuperar saída do filho runbook trabalho *ou* pai runbook pode obter diretamente saída do runbook filho.|
|Parâmetros|Valores para os parâmetros de runbook filho são especificados separadamente e podem usar qualquer tipo de dados.|Valores para os parâmetros de runbook filho devem ser combinados em uma única tabela de hash e podem incluir apenas simples, matriz e, em seguida, tipos de dados de objeto que serialização JSON de aproveitar.|
|Conta de automação|Pai runbook só pode usar filho runbook na mesma conta de automação.|Pai runbook pode usar runbook filho de qualquer conta de automação da mesma assinatura do Azure e até mesmo uma assinatura diferente se você tiver uma conexão a ela.|
|Publicação|Filho runbook deve ser publicado antes de runbook pai é publicado.|Filho runbook deve ser publicado a qualquer hora antes de runbook pai é iniciado.|

## <a name="next-steps"></a>Próximas etapas

- [Iniciando um runbook no Azure automação](automation-starting-a-runbook.md)
- [Saída do runbook e mensagens em automação do Azure](automation-runbook-output-and-messages.md)
