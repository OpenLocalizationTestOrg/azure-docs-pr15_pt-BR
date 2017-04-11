<properties 
   pageTitle="Fluxo de trabalho de PowerShell de aprendizagem"
   description="Este artigo é destinado como uma rápida lição autores familiarizados com o PowerShell para entender as diferenças entre o PowerShell e ao fluxo de trabalho do PowerShell."
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

# <a name="learning-windows-powershell-workflow"></a>Fluxo de trabalho do Learning Windows PowerShell

Runbooks na automação do Azure são implementadas como fluxos de trabalho do Windows PowerShell.  Um fluxo de trabalho do Windows PowerShell é semelhante a um script do Windows PowerShell, mas tem algumas diferenças significativas que podem ser confusas para um novo usuário.  Este artigo destina-se a usuários já familiarizados com o PowerShell e explica resumidamente os conceitos que você precisa se você estiver convertendo um script do PowerShell para um fluxo de trabalho do PowerShell para uso em um runbook.  

Um fluxo de trabalho é uma sequência de etapas programadas, conectadas que executar tarefas de execução demorada ou exigirem coordenação de várias etapas em vários dispositivos ou nós gerenciados. Os benefícios de um fluxo de trabalho ao longo de um script normal incluem a capacidade de executar simultaneamente uma ação em vários dispositivos e a capacidade de recuperar automaticamente de falhas. Um fluxo de trabalho do Windows PowerShell é um script do Windows PowerShell que utiliza o Windows Workflow Foundation. Enquanto o fluxo de trabalho é escrito com sintaxe do Windows PowerShell e iniciado pelo Windows PowerShell, ela é processada pelo Windows Workflow Foundation.

Para obter detalhes completos sobre os tópicos neste artigo, consulte [Introdução ao fluxo de trabalho do Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).

## <a name="types-of-runbook"></a>Tipos de runbook

Há três tipos de runbook no Azure automação, *Fluxo de trabalho do PowerShell*, *PowerShell* e *gráficos*.  Você define o tipo de runbook quando você cria runbook e não é possível converter um runbook para o outro tipo quando ele é criado.

Runbooks de fluxo de trabalho do PowerShell e PowerShell runbooks são para os usuários que preferem trabalhar diretamente com o código do PowerShell ou usando o editor de textual no Azure automação ou um editor de offline como o PowerShell ISE. Se você estiver criando um runbook de fluxo de trabalho do PowerShell, você deve compreender as informações neste artigo. 

Runbooks gráficas permitem que você crie um runbook usando as mesmas atividades e cmdlets, mas usando uma interface gráfica que oculta as complexidades do fluxo de trabalho subjacente do PowerShell.  Conceitos neste artigo como pontos de verificação e execução paralela ainda se aplicam ao runbooks gráfica, mas você não precisa se preocupar sobre a sintaxe detalhada. 

## <a name="basic-structure-of-a-workflow"></a>Estrutura básica de um fluxo de trabalho

A primeira etapa para converter um script do PowerShell para um fluxo de trabalho do PowerShell é envolve com a palavra-chave de **fluxo de trabalho** .  Um fluxo de trabalho começa com a palavra-chave de **fluxo de trabalho** seguida o corpo do script entre chaves. O nome do fluxo de trabalho segue a palavra-chave de **fluxo de trabalho** , conforme mostrado na seguinte sintaxe. 

    Workflow Test-Workflow
    {
       <Commands>
    }

O nome do fluxo de trabalho deve corresponder ao nome do runbook automação. Se runbook está sendo importado, o nome do arquivo deve corresponder ao nome do fluxo de trabalho e deve terminar em. ps1.

Para adicionar parâmetros ao fluxo de trabalho, use a palavra-chave de **parâmetro** , tal como faria para um script. 

## <a name="code-changes"></a>Alterações de código

Código de fluxo de trabalho do PowerShell parece quase idêntico ao código de script do PowerShell, com exceção de algumas alterações significativas.  As seções a seguir descrevem as alterações que você precisará fazer um script do PowerShell para que ele seja executado em um fluxo de trabalho.

### <a name="activities"></a>Atividades

Uma atividade é uma tarefa específica em um fluxo de trabalho. Assim como um script é composto por um ou mais comandos, um fluxo de trabalho é composto de uma ou mais atividades que são executadas em uma sequência. Fluxo de trabalho do Windows PowerShell converte automaticamente muitos dos cmdlets do Windows PowerShell para atividades quando ele é executado um fluxo de trabalho. Quando você especificar um desses cmdlets no seu runbook, atividade correspondente é realmente executada pelo Windows Workflow Foundation. Para esses cmdlets sem uma atividade correspondente, o fluxo de trabalho do Windows PowerShell automaticamente executa o cmdlet dentro de uma atividade de [InlineScript](#inlinescript) . Há um conjunto de cmdlets que são excluídos e não podem ser usados em um fluxo de trabalho, a menos que você explicitamente incluí-los em um bloco de InlineScript. Para obter mais detalhes sobre esses conceitos, consulte [Usando atividades em fluxos de trabalho de Script](http://technet.microsoft.com/library/jj574194.aspx).

Atividades de fluxo de trabalho compartilham um conjunto de parâmetros comuns para configurar sua operação. Para obter detalhes sobre os parâmetros comuns do fluxo de trabalho, consulte [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parâmetros posicionais

Você não pode usar parâmetros posicionais com atividades e cmdlets em um fluxo de trabalho.  Tudo isso significa é que você deve usar nomes de parâmetro.

Por exemplo, considere o seguinte código que obtém todos os serviços em execução.

     Get-Service | Where-Object {$_.Status -eq "Running"}

Se você tentar executar este código mesmo em um fluxo de trabalho, você receberá uma mensagem como "conjunto de parâmetro não pode ser resolvido usando os parâmetros nomeados especificado."  Para corrigir isso, simplesmente fornece o nome do parâmetro da seguinte.

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>Objetos desserializados

Objetos em fluxos de trabalho estão desserializados.  Isso significa que suas propriedades ainda estão disponíveis, mas não seus métodos.  Por exemplo, considere o seguinte código do PowerShell que interrompe um serviço usando o método de parada do objeto do serviço.

    $Service = Get-Service -Name MyService
    $Service.Stop()

Se você tentar executar este fluxo de trabalho, você receberá uma mensagem de erro informando "invocação de método não é suportada em um fluxo de trabalho do Windows PowerShell".  

Uma opção é quebrar essas duas linhas de código em um bloco de [InlineScript](#InlineScript) caso no qual $Service seria um objeto de serviço dentro do bloco. 

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    } 

Outra opção é usar o cmdlet outro que executa a mesma funcionalidade que o método, se disponível.  No caso de nosso exemplo, o cmdlet parada-Service fornece a mesma funcionalidade que o método de parada e você pode usar o seguinte para um fluxo de trabalho.

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>InlineScript

A atividade de **InlineScript** é útil quando você precisa executar um ou mais comandos como script PowerShell tradicional em vez de fluxo de trabalho do PowerShell.  Enquanto comandos de fluxo de trabalho são enviados para o Windows Workflow Foundation para processamento, comandos em um bloco de InlineScript são processados pelo Windows PowerShell. 

InlineScript usa a sintaxe mostrada abaixo.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Você pode retornar a saída de um InlineScript atribuindo a saída a uma variável. O exemplo a seguir interrompe um serviço e então apresenta o nome do serviço.

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Você pode passar valores em um bloco de InlineScript, mas você deve usar **$Using** modificadora de escopo.  O exemplo a seguir é idêntico ao exemplo anterior, exceto que o nome do serviço é fornecido por uma variável. 

    Workflow Stop-MyService
    {
        $ServiceName = "MyService"
    
        $Output = InlineScript {
            $Service = Get-Service -Name $Using:ServiceName
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Enquanto InlineScript atividades podem ser críticas em determinados fluxos de trabalho, eles não dão suporte a construções de fluxo de trabalho e só devem ser usados quando necessário pelos seguintes motivos:

- Você não pode usar [pontos de verificação](#Checkpoints) dentro de um bloco de InlineScript. Se ocorrer uma falha dentro do bloco, ele deve ser reiniciado desde o início do bloco.
- Você não pode usar [execução paralela](#parallel-execution) dentro de um InlineScriptBlock.
- InlineScript afeta escalabilidade do fluxo de trabalho, desde que ela mantém a sessão do Windows PowerShell para o comprimento do bloco InlineScript inteira.

Para obter mais detalhes sobre como usar InlineScript, consulte [Executando comandos do Windows PowerShell em um fluxo de trabalho](http://technet.microsoft.com/library/jj574197.aspx) e [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).


## <a name="parallel-processing"></a>Processamento paralelo

Uma vantagem de fluxos de trabalho do Windows PowerShell é a capacidade de executar um conjunto de comandos em paralelo, em vez de sequencialmente como com um script típico. 

Você pode usar a palavra-chave **paralelas** para criar um bloco de script com vários comandos que serão executadas simultaneamente. Isso usa a sintaxe mostrada abaixo. Nesse caso, Activity1 e Activity2 será iniciado ao mesmo tempo. Activity3 iniciará apenas após Activity1 e Activity2 concluíram.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


Por exemplo, considere os seguintes comandos do PowerShell que copiar vários arquivos para um destino de rede.  Esses comandos são executados sequencialmente para que um arquivo deve terminar copiando antes do próximo é iniciado.     

    $Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    $Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    $Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

O seguinte fluxo de trabalho é executado esses mesmos comandos em paralelo para que todos eles iniciarem copiando ao mesmo tempo.  Somente depois que todos eles estiverem completamente copiado é a mensagem de conclusão exibida.

    Workflow Copy-Files
    {
        Parallel 
        {
            $Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
        }

        Write-Output "Files copied."
    }


Você pode usar o **ForEach-paralelas** construção comandos de processo para cada item em um conjunto simultaneamente. Os itens na coleção são processados em paralelo, enquanto os comandos no bloco de script executados sequencialmente. Isso usa a sintaxe mostrada abaixo. Nesse caso, Activity1 será iniciado ao mesmo tempo para todos os itens na coleção. Para cada item, Activity2 será iniciado após Activity1 é concluída. Activity3 iniciará apenas após Activity1 e Activity2 concluíram para todos os itens.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

O exemplo a seguir é semelhante ao exemplo anterior copiando arquivos em paralelo.  Nesse caso, uma mensagem é exibida para cada arquivo depois de copiá.  Somente depois que todos eles estiverem completamente copiado é a mensagem de conclusão finais exibida.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }
        
        Write-Output "All files copied."
    }

> [AZURE.NOTE]  Não é recomendável executando runbooks filho em paralelo, pois isso demonstrou forneça resultados não confiáveis.  A saída do runbook filho às vezes não serão exibidos e configurações em um filho runbook podem afetar os outros runbooks filho paralelas 


## <a name="checkpoints"></a>Pontos de verificação

Um *ponto de verificação* é um instantâneo do estado atual do fluxo de trabalho que inclui o valor atual de variáveis e qualquer saída gerada a esse ponto. Se um fluxo de trabalho termina em erro ou está suspenso, em seguida, na próxima vez em que ele é executado-iniciará de seu último ponto de verificação em vez de início do fluxo de trabalho.  Você pode definir um ponto de verificação de fluxo de trabalho com a atividade de **Fluxo de trabalho de ponto de verificação** .

No código de exemplo a seguir, uma exceção ocorre após Activity2 causando o fluxo de trabalho encerrar. Quando o fluxo de trabalho é executado novamente, ele começa executando Activity2 desde que foi logo depois de definir o último ponto de verificação.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Você deve definir os pontos de verificação de fluxo de trabalho após as atividades que podem estar sujeito a exceção e não devem ser repetido se o fluxo de trabalho é retomado. Por exemplo, seu fluxo de trabalho pode criar uma máquina virtual. Você pode definir um ponto de verificação antes e depois os comandos para criar a máquina virtual. Se a criação falhar, então os comandos poderiam ser repetidos se o fluxo de trabalho é iniciado novamente. Se o o fluxo de trabalho falha após a criação estiver concluído, e em seguida, na máquina virtual não será criada novamente quando o fluxo de trabalho é retomado.

O exemplo a seguir copia vários arquivos para um local de rede e define um ponto de verificação após cada arquivo.  Se o local de rede for perdido, o fluxo de trabalho terminará em erro.  Quando ele for iniciado novamente, ele continuará no último ponto de verificação que significa que somente os arquivos que já foram copiados serão ignorados.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
            Checkpoint-Workflow
        }
        
        Write-Output "All files copied."
    }

Como as credenciais de nome de usuário não são mantidas depois de ligar para a atividade de [Fluxo de trabalho de suspender](https://technet.microsoft.com/library/jj733586.aspx) ou depois do último ponto de verificação, você precisa definir as credenciais para nulo e, em seguida, recuperá-los novamente do repositório ativo após **Suspender fluxo de trabalho** ou ponto de verificação é chamado.  Caso contrário, você pode receber a seguinte mensagem de erro: não é possível continuar *fluxo de trabalho, ou porque dados persistência podem não ser salvo completamente ou salvo persistência dados foram corrompidos. Reinicie o fluxo de trabalho.*

O mesmo código a seguir demonstra como lidar com isso em seus runbooks de fluxo de trabalho do PowerShell.

       
    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)
        
          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     } 


Isso não é necessário se você estiver autenticando usando uma conta executar como configurada com um serviço principal.  

Para obter mais informações sobre pontos de verificação, consulte [Adicionando pontos de verificação para um fluxo de trabalho de Script](http://technet.microsoft.com/library/jj574114.aspx).


## <a name="next-steps"></a>Próximas etapas

- Para começar a usar runbooks de fluxo de trabalho do PowerShell, consulte [Minha primeira runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md) 
