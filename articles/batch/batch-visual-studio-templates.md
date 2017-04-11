<properties
    pageTitle="Modelos do Visual Studio para Azure lote | Microsoft Azure"
    description="Saiba como esses modelos de projeto do Visual Studio podem ajudá-lo a implementar e executar as cargas de trabalho pesados em lote do Azure"
    services="batch"
    documentationCenter=".net"
    authors="fayora"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="09/07/2016"
    ms.author="marsma" />

# <a name="visual-studio-project-templates-for-azure-batch"></a>Modelos de projeto do Visual Studio para lote do Azure

O **Gerenciador de trabalho** e **modelos de tarefa processador Visual Studio** para lote fornecem código para ajudar você a implementar e executar as cargas de trabalho pesados em lote com o mínimo de esforço. Este documento descreve esses modelos e fornece orientação sobre como usá-las.

>[AZURE.IMPORTANT] Este artigo discute somente informações aplicáveis a estes dois modelos e supõe que você esteja familiarizado com o serviço de lote e conceitos-chave relacionados a ela: pools, calcular nós, trabalhos e tarefas, Gerenciador de tarefas, variáveis de ambiente e outras informações relevantes. Você pode encontrar mais informações no [Lote de Noções básicas do Azure](batch-technical-overview.md), [Visão geral do recurso de lote para os desenvolvedores](batch-api-basics.md)e [começar a usar a biblioteca de lote do Azure para .NET](batch-dotnet-get-started.md).

## <a name="high-level-overview"></a>Visão geral

Os modelos de Gerenciador de trabalho e processador de tarefas podem ser usados para criar dois componentes úteis:

* Uma tarefa do Gerenciador de trabalho que implementa um divisor de trabalho que pode dividir um trabalho em várias tarefas que podem ser executados de maneira independente, em paralelo.

* Processador de tarefas que pode ser usado para realizar pré-processamento e pós-processamento em torno de uma linha de comando do aplicativo.

Por exemplo, em um cenário de processamento de filme, o divisor de trabalho poderia transformar um trabalho único filme em centenas ou milhares de tarefas separadas que faria processar quadros individuais separadamente. Forma correspondente, processador da tarefa seria chamar o aplicativo de processamento e todos os processos dependentes que são necessários para processar cada quadro, bem como executam quaisquer ações adicionais (por exemplo, copiando o quadro renderizado para um local de armazenamento).

>[AZURE.NOTE] Os modelos de Gerenciador de trabalho e processador de tarefa são independentes umas das outras, para que possa escolher usar ambos ou apenas um deles, dependendo dos requisitos do seu trabalho de computação e suas preferências.

Como mostrado no diagrama abaixo, um trabalho de computação que usa esses modelos passará por três estágios:

1. O código do cliente (por exemplo, aplicativo, serviço da web, etc.) envia um trabalho para o serviço de lote no Azure, especificando como o programa do Gerenciador de trabalho da tarefa de seu Gerenciador de trabalho.

2. O serviço de lote executa a tarefa do Gerenciador de trabalho em um nó de computação e o divisor de trabalho inicia o número especificado de tarefas de processador, em como muitos nós de computadores conforme necessário, com base nos parâmetros e especificações no código de divisão de trabalho.

3. As tarefas de processador tarefa independentemente, executadas em paralelo, para processar os dados de entrada e gerar os dados de saída.

![Diagrama mostrando como o código do cliente interage com o serviço de lote][diagram01]

## <a name="prerequisites"></a>Pré-requisitos

Para usar os modelos de lote, você precisará do seguinte:

* Um computador com o Visual Studio de 2015, ou mais recente, já instalado nele.

* Os modelos de lote, que estão disponíveis na [Galeria do Visual Studio] [ vs_gallery] como extensões do Visual Studio. Há duas maneiras de obter os modelos:

  * Instale os modelos usando a caixa de diálogo **extensões e atualizações** no Visual Studio (para obter mais informações, consulte [Localizar e usando o Visual Studio Extensions][vs_find_use_ext]). Na caixa de diálogo **extensões e atualizações** , pesquisar e baixar as seguintes extensões de dois:

    * Gerenciador de trabalhos em lotes Azure com divisor de trabalho
    * Processador de tarefa de lotes Azure

  * Baixar os modelos da Galeria de online para Visual Studio: [Modelos de projeto do Microsoft Azure em lotes][vs_gallery_templates]

* Se você planeja usar o recurso de [Pacotes de aplicativos](batch-application-packages.md) para implantar o Gerenciador de trabalho e processador de tarefas para o lote de nós de computadores, você precisa vincular uma conta de armazenamento à sua conta do lote.

## <a name="preparation"></a>Preparação

Recomendamos criar uma solução que pode conter seu gerente de trabalho bem como seu processador de tarefa, pois isto pode tornar mais fácil compartilhar código entre seu gerente de trabalho e programas de processador de tarefa. Para criar esta solução, siga estas etapas:

1. Abra o Visual Studio 2015 e selecione **arquivo** > **novo** > **projeto**.

2. Em **modelos**, expanda **Outros tipos de projeto**, clique em **Visual Studio Solutions**e selecione **Solução em branco**.

3. Digite um nome que descreva o seu aplicativo e a finalidade desta solução (por exemplo, "LitwareBatchTaskPrograms").

4. Para criar a nova solução, clique em **Okey**.

## <a name="job-manager-template"></a>Modelo do Gerenciador de trabalho

O modelo de trabalho Manager ajuda você a implementar uma tarefa do Gerenciador de trabalho que pode executar as seguintes ações:

* Divida um trabalho em várias tarefas.
* Envie essas tarefas para executar em lote.

>[AZURE.NOTE] Para obter mais informações sobre o Gerenciador de tarefas, consulte [Visão geral do recurso de lote para desenvolvedores](batch-api-basics.md#job-manager-task).

### <a name="create-a-job-manager-using-the-template"></a>Criar um Gerenciador de trabalho usando o modelo

Para adicionar um gerente de trabalho para a solução que você criou anteriormente, siga estas etapas:

1. Abra sua solução existente no Visual Studio 2015.

2. No Solution Explorer, clique com botão direito a solução, clique em **Adicionar** > **Novo projeto**.

3. Em **Visual c#**, clique em **nuvem**e clique em **Gerenciador de trabalhos em lotes Azure com divisor de trabalho**.

4. Digite um nome que descreve seu aplicativo e identifica este projeto como o Gerenciador de trabalhos (por exemplo, "LitwareJobManager").

5. Para criar o projeto, clique em **Okey**.

6. Finalmente, crie o projeto para forçar o Visual Studio para carregar todos os pacotes NuGet referenciados e para verificar se o projeto é válido antes de começar a modificá-lo.

### <a name="job-manager-template-files-and-their-purpose"></a>Arquivos de modelo do Gerenciador de trabalho e sua finalidade

Quando você cria um projeto usando o modelo do Gerenciador de trabalho, ela gera três grupos de arquivos de código:

* O arquivo de programa principal (Program.cs). Esta página contém o ponto de entrada do programa e a manipulação de exceção de nível superior. Normalmente, você não deve precisa modificar isso.

* O diretório de Framework. Esta página contém os arquivos responsáveis pelo trabalho 'clichê' até o programa Gerenciador de trabalho – descompactar parâmetros, adicionando tarefas para o trabalho em lotes, etc. Normalmente, você não precisa modificar esses arquivos.

* O arquivo de divisão de trabalho (JobSplitter.cs). Isso é onde você colocará sua lógica específicos do aplicativo para dividir um trabalho em tarefas.

Claro, você pode adicionar arquivos adicionais conforme necessário para oferecer suporte a seu código do divisor de trabalho, dependendo da complexidade do trabalho divisão lógica.

O modelo também gera arquivos de projeto padrão do .NET como um arquivo. csproj App, packages.config, etc.

O restante desta seção descreve os diferentes arquivos e sua estrutura de código e explica o que faz cada classe.

![Visual Studio Solution Explorer mostrando o Gerenciador de trabalhos solução de modelo][solution_explorer01]

**Arquivos de estrutura**

* `Configuration.cs`: Encapsula o carregamento dos dados de configuração de trabalho como detalhes de conta do lote, credenciais de conta de armazenamento vinculado, trabalho e informações sobre a tarefa e parâmetros do trabalho. Ele também fornece acesso a variáveis de ambiente definidas pelo lote (Consulte configurações de ambiente para tarefas, na documentação do lote) por meio da classe Configuration.EnvironmentVariable.

* `IConfiguration.cs`: Elimina a implementação da classe de configuração, para que você possa teste de unidade seu divisor de trabalho usando um objeto de configuração de falsos ou fictícios.

* `JobManager.cs`: Organiza os componentes do programa de Gerenciador de trabalho. Ele é responsável pela inicializar o divisor de trabalho, invocar o divisor de trabalho e expedir as tarefas retornadas pelo divisor de trabalho para o emissor de tarefa.

* `JobManagerException.cs`: Representa um erro que requer o Gerenciador de trabalhos finalizar. JobManagerException é usada para quebrar erros 'esperados' onde as informações de diagnóstico específico podem ser fornecidas como parte de término.

* `TaskSubmitter.cs`: Essa classe é responsável por adicionando tarefas retornadas pelo divisor de trabalho para o trabalho em lotes. As agregações de classe JobManager a sequência de tarefas em lotes para inclusão eficiente mas pontual no trabalho, em seguida, chama TaskSubmitter.SubmitTasks em um segmento de plano de fundo para cada lote.

**Divisor de trabalho**

`JobSplitter.cs`: Essa classe contém lógica específica do aplicativo para dividir o trabalho em tarefas. A estrutura chama o método JobSplitter.Split para obter uma sequência de tarefas, que ela adiciona ao trabalho conforme o método retorna-los. Esta é a classe onde você irá inserir a lógica do seu trabalho. Implemente o método de divisão para retornar uma sequência de instâncias de CloudTask representando as tarefas em que você deseja partição o trabalho.

**Arquivos de projeto de linha de comando .NET padrão**

* `App.config`: Arquivo de configuração padrão do aplicativo .NET.

* `Packages.config`: Arquivo de dependência de pacote de NuGet padrão.

* `Program.cs`: Contém o ponto de entrada do programa e a manipulação de exceção de nível superior.

### <a name="implementing-the-job-splitter"></a>Implementando o divisor de trabalho

Quando você abre o projeto de modelo do Gerenciador de trabalho, o projeto terá o arquivo JobSplitter.cs abertos por padrão. Você pode implementar a lógica de divisão para as tarefas na sua carga de trabalho usando a slides de método split () abaixo:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

>[AZURE.NOTE] A seção anotada o `Split()` método é a única seção do código do modelo de Gerenciador de trabalho que destina modificar, adicionando a lógica para dividir os trabalhos em tarefas diferentes. Se você deseja modificar uma seção diferente do modelo, verifique se você estiver trazido com o funcionamento do lote e experimentar alguns dos [exemplos de código do lote][github_samples].

A implementação de split () tem acesso a:

* Os parâmetros de trabalho, por meio do `_parameters` campo.
* O objeto de CloudJob que representa o trabalho, por meio do `_job` campo.
* O objeto de CloudTask que representa a tarefa do Gerenciador de trabalho, por meio do `_jobManagerTask` campo.

Seu `Split()` implementação não precisa adicionar tarefas ao trabalho diretamente. Em vez disso, seu código deverá retornar uma sequência de objetos CloudTask, e eles serão adicionados ao trabalho automaticamente pelas classes framework que invocar o divisor de trabalho. É comum para uso do iterador # (`yield return`) recurso implementar divisores de trabalho, pois isso permite que as tarefas iniciar a execução assim que possível, em vez da aguardando para todas as tarefas para serem calculadas.

**Falha de divisão de trabalho**

Se divisor seu trabalho encontrar um erro, ele deve:

* Encerrar a sequência usando a c# `yield break` instrução, no qual caso o Gerenciador de trabalhos será tratado como bem-sucedida; ou

* Lança uma exceção, em cujo caso que o Gerenciador de trabalhos será tratado como falha e pode ser repetido dependendo de como o cliente configurou-lo).

Em ambos os casos, as tarefas já retornados pelo divisor de trabalho e adicionado ao trabalho em lotes será qualificadas executar. Se você não quiser que isso aconteça, em seguida, você pode:

* Encerrar o trabalho antes de retornar do divisor de trabalho

* Formula coleção tarefa inteira antes de retorná-lo (ou seja, retornar um `ICollection<CloudTask>` ou `IList<CloudTask>` em vez de implementar o divisor de trabalho usando um iterador c#)

* Usar dependências entre tarefas para fazer com que todas as tarefas dependem da conclusão bem-sucedida de Gerenciador de trabalho

**Repetições de Gerenciador de trabalho**

Se o Gerenciador de trabalhos falhar, ele pode ser repetido pelo serviço lote dependendo das configurações de repetição do cliente. Em geral, isso é seguro, porque quando o framework adiciona tarefas ao trabalho, ignora qualquer tarefa que já existe. No entanto, se o cálculo de tarefas é caro, talvez você não queira provoca o custo de recalculando tarefas que já foram adicionadas ao trabalho; Por outro lado, se o reexecutar não garantimos gerar a mesma tarefa IDs, em seguida, o comportamento de 'Ignorar duplicatas' será não iniciada. Nesses casos, você deve projetar seu divisor de trabalho para detectar o trabalho que já foi feito e não repetir, por exemplo, executando uma CloudJob.ListTasks antes de começar a produzir tarefas.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Códigos de saída e exceções no modelo do Gerenciador de trabalho

Códigos de saída e exceções fornecem um mecanismo para determinar o resultado da execução de um programa, e eles podem ajudar a identificar problemas com a execução do programa. O modelo do Gerenciador de trabalhos implementa as exceções descritas nesta seção e códigos de saída.

Uma tarefa do Gerenciador de trabalho que é implementada com o modelo do Gerenciador de trabalho pode retornar três códigos de saída possíveis:

| Código | Descrição |
|------|-------------|
| 0    | O Gerenciador de trabalho que foi concluído com êxito. Seu código de divisão de trabalho executado para conclusão e todas as tarefas foram adicionadas ao trabalho. |
| 1    | A tarefa do Gerenciador de trabalho falhou com uma exceção em uma parte 'esperada' do programa. A exceção foi convertida em um JobManagerException com informações de diagnóstico e, quando possível, sugestões para resolver a falha. |
| 2    | A tarefa do Gerenciador de trabalho falha com uma exceção 'inesperada'. A exceção foi registrada saída padrão, mas o Gerenciador de trabalhos conseguiu adicionar informações de diagnóstico ou correção adicionais. |

No caso de falha de tarefa do Gerenciador de trabalho, algumas tarefas ainda tenham sido adicionadas ao serviço antes do erro ocorreu. Essas tarefas serão executadas normalmente. Consulte "Falha de divisão de trabalho" acima para discussão deste caminho de código.

Todas as informações retornadas pela exceções escritas em arquivos stdout.txt e stderr.txt. Para obter mais informações, consulte [Manipulação de erro](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Considerações do cliente

Esta seção descreve alguns requisitos de implementação de cliente ao chamar um gerente de trabalho baseado neste modelo. Veja [como passar parâmetros e variáveis de ambiente de código do cliente](#pass-environment-settings) para obter detalhes sobre passando parâmetros e configurações de ambiente.

**Credenciais obrigatórias**

Para adicionar tarefas ao trabalho em lotes do Azure, a tarefa do Gerenciador de trabalho requer o URL de conta de lote do Azure e a chave. Você deve passar esses elementos no variáveis de ambiente chamadas YOUR_BATCH_URL e YOUR_BATCH_KEY. Você pode definir esses no Gerenciador de trabalho configurações de ambiente de tarefa. Por exemplo, em um cliente c#:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Credenciais de armazenamento**

Normalmente, o cliente não precisa fornecer as credenciais de conta de armazenamento vinculado à tarefa do Gerenciador de trabalho porque (a) mais gerentes de trabalho não precisa explicitamente acessar a conta de armazenamento vinculado e (b) a conta de armazenamento vinculado geralmente é fornecida para todas as tarefas como uma configuração de ambiente comuns para o trabalho. Se você não fornece a conta de armazenamento vinculado por meio das configurações de ambiente comuns e o Gerenciador de trabalho requer acesso ao armazenamento vinculado, você deve fornecer as credenciais de armazenamento vinculado da seguinte maneira:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Configurações de tarefa do Gerenciador de trabalho**

O cliente deve definir o sinalizador de *killJobOnCompletion* do Gerenciador de trabalho como **false**.

Geralmente é seguro para o cliente definir *runExclusive* como **false**.

O cliente deve usar o conjunto de *resourceFiles* ou *applicationPackageReferences* para que o trabalho manager executável (e suas DLLs necessários) implantado para o nó de computação.

Por padrão, o Gerenciador de trabalho não será repetido se falhar. Dependendo de sua lógica de Gerenciador de trabalho, o cliente talvez queira ativar tentativas por meio de *restrições*/*maxTaskRetryCount*.

**Configurações de trabalho**

Se o divisor de trabalho emite tarefas com dependências, o cliente deve definir usesTaskDependencies do trabalho para true.

No modelo de divisão de trabalho, é incomum para clientes queira adicionar tarefas à trabalhos sobre e acima, o que cria o divisor de trabalho. O cliente, portanto, deve definir normalmente do trabalho *onAllTasksComplete* para **terminatejob**.

## <a name="task-processor-template"></a>Modelo de processador de tarefas

Um modelo de tarefa processador ajuda você a implementar um processador de tarefa que pode executar as seguintes ações:

* Configure as informações necessárias por cada tarefa em lotes para executar.
* Execute todas as ações necessárias por cada tarefa em lotes.
* Salve saídas de tarefa para armazenamento persistente.

Embora um processador de tarefa não é necessário para executar tarefas em lote, a chave vantagem de usar um processador de tarefa é que ele fornece uma embalagem para implementar todas as ações de execução de tarefa em um local. Por exemplo, se você precisa executar vários aplicativos no contexto de cada tarefa, ou se você precisa copiar dados para o armazenamento persistente após a conclusão de cada tarefa.

As ações executadas pelo processador da tarefa podem ser como simples ou complexa e como muitos ou pouquíssimos, conforme a necessidade por sua carga de trabalho. Além disso, Implementando todas as ações de tarefa em um processador de tarefa, você pode prontamente atualizar ou adicionar ações com base nas alterações de aplicativos ou requisitos de carga de trabalho. No entanto, em alguns casos um processador tarefa talvez não seja a solução ideal para a implementação como ele pode adicionar complexidade desnecessária, por exemplo, quando executando trabalhos que podem ser iniciados rapidamente a partir de uma linha de comando simple.

### <a name="create-a-task-processor-using-the-template"></a>Criar um processador de tarefa usando o modelo

Para adicionar um processador de tarefa para a solução que você criou anteriormente, siga estas etapas:

1. Abra sua solução existente no Visual Studio 2015.

2. No Solution Explorer, clique com botão direito a solução, clique em **Adicionar**e, em seguida, clique em **Novo projeto**.

3. Em **Visual c#**, clique em **nuvem**e, em seguida, clique em **Processador de tarefa de lotes do Azure**.

4. Digite um nome que descreve seu aplicativo e identifica este projeto como processador da tarefa (por exemplo, "LitwareTaskProcessor").

5. Para criar o projeto, clique em **Okey**.

6. Finalmente, crie o projeto para forçar o Visual Studio para carregar todos os pacotes NuGet referenciados e para verificar se o projeto é válido antes de começar a modificá-lo.

### <a name="task-processor-template-files-and-their-purpose"></a>Arquivos de modelo de processador de tarefa e sua finalidade

Quando você cria um projeto usando o modelo de processador de tarefa, ele gera três grupos de arquivos de código:

* O arquivo de programa principal (Program.cs). Esta página contém o ponto de entrada do programa e a manipulação de exceção de nível superior. Normalmente, você não deve precisa modificar isso.

* O diretório de Framework. Esta página contém os arquivos responsáveis pelo trabalho 'clichê' até o programa Gerenciador de trabalho – descompactar parâmetros, adicionando tarefas para o trabalho em lotes, etc. Normalmente, você não precisa modificar esses arquivos.

* O arquivo de processador de tarefa (TaskProcessor.cs). Isso é onde você colocará a lógica específica do aplicativo para executar uma tarefa (normalmente chamando check-out para um executável existente). Código de processamento prévio e posterior, como Baixando dados adicionais ou carregar arquivos de resultado, também aqui.

Claro, você pode adicionar arquivos adicionais conforme necessário para oferecer suporte a seu código de processador de tarefa, dependendo da complexidade do trabalho divisão lógica.

O modelo também gera arquivos de projeto padrão do .NET como um arquivo. csproj App, packages.config, etc.

O restante desta seção descreve os diferentes arquivos e sua estrutura de código e explica o que faz cada classe.

![Visual Studio Solution Explorer mostrando a solução de modelo de processador de tarefa][solution_explorer02]

**Arquivos de estrutura**

* `Configuration.cs`: Encapsula o carregamento dos dados de configuração de trabalho como detalhes de conta do lote, credenciais de conta de armazenamento vinculado, trabalho e informações sobre a tarefa e parâmetros do trabalho. Ele também fornece acesso a variáveis de ambiente definidas pelo lote (Consulte configurações de ambiente para tarefas, na documentação do lote) por meio da classe Configuration.EnvironmentVariable.

* `IConfiguration.cs`: Elimina a implementação da classe de configuração, para que você possa teste de unidade o divisor de trabalho usando um objeto de configuração de falsos ou fictícios.

* `TaskProcessorException.cs`: Representa um erro que requer o Gerenciador de trabalhos finalizar. TaskProcessorException é usada para quebrar erros 'esperados' onde as informações de diagnóstico específico podem ser fornecidas como parte de término.

**Processador de tarefa**

* `TaskProcessor.cs`: Executa a tarefa. A estrutura chama o método TaskProcessor.Run. Esta é a classe onde você irá inserir a lógica de aplicativos específicos da sua tarefa. Implemente o método de execução para:
  * Analisar e validar os parâmetros de tarefa
  * Redija a linha de comando para qualquer programa externo que você deseja chamar
  * Faça quaisquer informações de diagnóstico que podem ser exigir para fins de depuração
  * Iniciar um processo usando essa linha de comando
  * Aguarde o processo sair
  * Capturar o código de saída do processo para determinar se ela foi bem-sucedida ou falha
  * Salve os arquivos de saída que você deseja manter para armazenamento persistente

**Arquivos de projeto de linha de comando .NET padrão**

* `App.config`: Arquivo de configuração padrão do aplicativo .NET.
* `Packages.config`: Arquivo de dependência de pacote de NuGet padrão.
* `Program.cs`: Contém o ponto de entrada do programa e a manipulação de exceção de nível superior.

## <a name="implementing-the-task-processor"></a>Implementando o processador da tarefa

Quando você abre o projeto de modelo de processador de tarefa, o projeto terá o arquivo TaskProcessor.cs abertos por padrão. Você pode implementar a lógica de execução para as tarefas na sua carga de trabalho usando o método Run mostrado abaixo:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
>[AZURE.NOTE] A seção anotada no método Run é a única seção do código do modelo de processador de tarefa que destina-se para modificar, adicionando a lógica de execução para as tarefas na sua carga de trabalho. Se você quiser modificar uma seção diferente do modelo, verifique primeiro familiarize com funcionamento do lote revisando a documentação de lote e experimentar alguns dos exemplos de código lote.

O método Run é responsável pela inicialização de linha de comando, iniciando um ou mais processos, esperando por todos os processos concluir, salvar os resultados e finalmente retornando com um código de saída. O método Run é onde você implementar a lógica de processamento de suas tarefas. A estrutura de processador tarefa invoca o método Run para você; Você não precisa chamá-lo.

A implementação de Run tem acesso a:

* Os parâmetros de tarefas, por meio do `_parameters` campo.
* Ids de trabalho e tarefas, por meio do `_jobId` e `_taskId` campos.
* Configuração da tarefa, por meio do `_configuration` campo.

**Falha da tarefa**

No caso de falha, você pode sair do método Run por lançar uma exceção, mas isso deixa o manipulador de exceção de nível superior no controle sobre o código de saída de tarefa. Se você precisar controlar o código de saída para que você pode distinguir diferentes tipos de falha, por exemplo para fins de diagnóstico ou porque alguns modos de falha devem terminar o trabalho e outras pessoas não devem, você deve sair o método Run retornando um código de saída diferente de zero. Isso se torna o código de saída de tarefa.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Códigos de saída e exceções no modelo de processador de tarefa

Códigos de saída e exceções fornecem um mecanismo para determinar o resultado da execução de um programa, e eles podem ajudar a identificar problemas com a execução do programa. O modelo de tarefa processador implementa as exceções descritas nesta seção e códigos de saída.

Uma tarefa de processador implementado com o modelo de tarefa processador pode retornar três códigos de saída possíveis:

| Código | Descrição |
|------|-------------|
|  [Process.ExitCode][process_exitcode] | Processador da tarefa é executado para conclusão. Observe que isso não significa que o programa que você chamou foi bem-sucedida – apenas que o processador da tarefa chamou com êxito e executada qualquer processamento POST sem exceções. O significado do código de saída depende do programa invocado – normalmente o código de saída 0 significa o êxito do programa e qualquer outro código de saída significa que o programa falha. |
| 1    | Processador da tarefa falhou com uma exceção em uma parte 'esperada' do programa. A exceção foi convertida em um `TaskProcessorException` com informações de diagnóstico e, quando possível, sugestões para resolver a falha. |
| 2    | Processador da tarefa falhou com uma exceção 'inesperada'. A exceção foi registrada saída padrão, mas o processador da tarefa não foi possível adicionar informações de diagnóstico ou correção adicionais. |

>[AZURE.NOTE] Se o programa que você invoca usa códigos de saída 1 e 2 para indicar modos de falha específico, usando códigos de saída 1 e 2 para erros de processador da tarefa é ambígua. Você pode alterar esses códigos de erro de processador de tarefa para códigos de saída distinto editando os casos de exceção no arquivo Program.cs.

Todas as informações retornadas pela exceções escritas em arquivos stdout.txt e stderr.txt. Para obter mais informações, consulte tratamento de erro, na documentação do lote.

### <a name="client-considerations"></a>Considerações do cliente

**Credenciais de armazenamento**

Se seu processador tarefa usa o armazenamento de blob do Microsoft Azure para persistir saídas, por exemplo, usando a biblioteca de auxiliar de convenções de arquivo, em seguida, ele precisa acessar *qualquer* nuvem armazenamento conta credenciais *ou* uma URL de contêiner de blob que inclui uma assinatura de acesso compartilhado (SAS). O modelo inclui suporte para fornecer credenciais por meio de variáveis de ambiente comuns. Seu cliente pode passar as credenciais de armazenamento da seguinte maneira:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

A conta de armazenamento estará disponível na classe TaskProcessor via o `_configuration.StorageAccount` propriedade.

Se você preferir usar uma URL de contêiner com SAS, você também pode passar isso por meio de uma configuração de ambiente comum de trabalho, mas o modelo de processador tarefa atualmente não inclui suporte interno para isso.

**Configuração de armazenamento**

É recomendável que a tarefa de Gerenciador de cliente ou trabalho criar qualquer contêineres requeridos pelo tarefas antes de adicionar as tarefas ao trabalho. Isso é obrigatório que se você usar uma URL de contêiner com SAS, assim uma URL não inclui permissão para criar o contêiner. É recomendável mesmo se você passar credenciais de conta de armazenamento, como ele salva cada tarefa precisar chamar CloudBlobContainer.CreateIfNotExistsAsync no contêiner.

## <a name="pass-parameters-and-environment-variables"></a>Passar parâmetros e variáveis de ambiente

### <a name="pass-environment-settings"></a>Configurações de ambiente de fase

Um cliente pode passar informações para a tarefa do Gerenciador de trabalho no formulário de configurações de ambiente. Essas informações, em seguida, podem ser usadas pela tarefa do Gerenciador de trabalho ao gerar as tarefas de processador de tarefa que serão executado como parte do trabalho computação. Exemplos de informações que você pode passar como configurações de ambiente são:

* Teclas de nome e uma conta da conta de armazenamento
* URL de conta de lote
* Chave da conta em lotes

O serviço de lote tem um mecanismo simples para passar configurações de ambiente para uma tarefa do Gerenciador de trabalho usando o `EnvironmentSettings` propriedade na [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Por exemplo, para obter o `BatchClient` instância de uma conta de lote, você pode passar como a URL e credenciais compartilhadas chaves da conta em lotes de código de variáveis de ambiente do cliente. Da mesma forma, para acessar a conta de armazenamento que esteja vinculada à conta do lote, você pode passar o nome da conta de armazenamento e a chave da conta de armazenamento como variáveis de ambiente.

### <a name="pass-parameters-to-the-job-manager-template"></a>Passar parâmetros para o modelo do Gerenciador de trabalho

Em muitos casos, é útil passar por trabalho parâmetros para a tarefa de Gerenciador de trabalho, para controlar o processo de divisão de trabalho ou configurar as tarefas para o trabalho. Você pode fazer isso carregar um arquivo JSON chamado parameters.json como um arquivo de recurso para a tarefa do Gerenciador de trabalho. Os parâmetros, em seguida, podem se tornar disponíveis na `JobSplitter._parameters` campo no modelo do Gerenciador de trabalhos.

>[AZURE.NOTE] Manipulador de parâmetro interno oferece suporte apenas dicionários de cadeia de caracteres à cadeia de caracteres. Se você quiser passar valores JSON complexos como valores de parâmetro, você precisará passe como cadeias de caracteres e analisá-los no divisor de trabalho ou modificar a estrutura `Configuration.GetJobParameters` método.

### <a name="pass-parameters-to-the-task-processor-template"></a>Passar parâmetros para o modelo de processador de tarefa

Você também pode passar parâmetros para tarefas individuais implementadas usando o modelo de processador de tarefa. Assim como com o modelo do Gerenciador de trabalho, o modelo de processador tarefa procura um arquivo de recurso denominado

Parameters.JSON e, se encontrá-la carrega-lo como o dicionário de parâmetros. Há algumas opções passar parâmetros para as tarefas de processador:

* Reutilize os parâmetros de trabalho JSON. Isso funciona bem se os únicos parâmetros são aquelas que todo o trabalho (por exemplo, uma renderização altura e largura). Para fazer isso, ao criar um CloudTask no divisor de trabalho, adicione uma referência para o objeto de arquivo de recurso de parameters.json de ResourceFiles a tarefa Gerenciador de trabalho (`JobSplitter._jobManagerTask.ResourceFiles`) ao conjunto de ResourceFiles do CloudTask.

* Gerar e carregar um documento de tarefas específicas parameters.json como parte da execução do divisor de trabalho e referência blob na coleção de arquivos de recurso da tarefa. Isso é necessário se tarefas diferentes têm parâmetros diferentes. Um exemplo pode ser um cenário de processamento 3D onde o índice de quadro é passado para a tarefa como um parâmetro.

>[AZURE.NOTE] Manipulador de parâmetro interno oferece suporte apenas dicionários de cadeia de caracteres à cadeia de caracteres. Se você quiser passar valores JSON complexos como valores de parâmetro, você precisará passe como cadeias de caracteres e analisá-los no processador da tarefa ou modificar a estrutura `Configuration.GetTaskParameters` método.

## <a name="next-steps"></a>Próximas etapas

### <a name="persist-job-and-task-output-to-azure-storage"></a>Se mantiver saída de trabalho e tarefa ao armazenamento do Azure

Outra ferramenta útil no desenvolvimento de soluções de lote é [Convenções de arquivo do Azure lote][nuget_package]. Use esta biblioteca de classes .NET (atualmente em preview) em seus aplicativos .NET lote facilmente armazenar e recuperar saídas de tarefa para e do armazenamento do Azure. [Trabalho persistir em lotes do Azure e tarefas de saída](batch-task-output.md) contém uma discussão completa sobre a biblioteca e o seu uso.

### <a name="batch-forum"></a>Fórum do lote

[Fórum do Azure lote] [ forum] no MSDN é um ótimo lugar para discutir lote e faça perguntas sobre o serviço. Vá diretamente para postagens "adesivas" úteis e envie suas perguntas conforme eles surgem enquanto você cria soluções lote.

[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
