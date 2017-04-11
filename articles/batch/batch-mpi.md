<properties
    pageTitle="Executar aplicativos MPI no Azure lote com tarefas de várias instâncias | Microsoft Azure"
    description="Saiba como executar aplicativos de Interface de transmissão de mensagens (MPI) usando o tipo de tarefa de várias instâncias em lote do Azure."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-azure-batch"></a>Usar tarefas de várias instâncias para executar aplicativos de Interface de transmissão de mensagens (MPI) em lote do Azure

Tarefas de várias instâncias permitem que você executar uma tarefa de lote do Azure em vários nós de computação simultaneamente. Essas tarefas permitem cenários como aplicativos de Interface de transmissão de mensagens (MPI) no lote de computação de alto desempenho. Neste artigo, você aprende a executar tarefas de várias instâncias usando o [Lote .NET] [ api_net] biblioteca.

>[AZURE.NOTE] Embora os exemplos deste artigo se concentrar em lote .NET, MS-MPI, e nós de computação do Windows, os conceitos de tarefa de várias instâncias discutidos aqui são aplicáveis a outras plataformas e tecnologias (Python e Intel MPI em nós Linux, por exemplo).

## <a name="multi-instance-task-overview"></a>Visão geral da tarefa várias instâncias

Em lote, cada tarefa normalmente é executado em um nó de computação único – você enviar várias tarefas em um trabalho, e o serviço de lote agenda cada tarefa para execução em um nó. No entanto, com **configurações de várias instâncias**de uma tarefa, você informa ao lote em vez disso, criar uma tarefa primária e várias subtarefas que são executadas em vários nós.

![Visão geral da tarefa várias instâncias][1]

Quando você envia uma tarefa com configurações de várias instâncias para um trabalho, lote executa várias etapas exclusivas às tarefas de várias instâncias:

1. O serviço de lote cria um **primário** e várias **subtarefas** com base nas configurações de várias instâncias. O número total de tarefas (primárias além de todas as subtarefas) corresponde ao número de **instâncias** (nós de computação) especificado nas configurações de várias instâncias.
1. Lote designa um de nós de computação como **mestre**e agenda a tarefa principal para executar no mestre. Ele agenda as subtarefas para executar o restante de nós de computação alocados para a tarefa de várias instâncias, uma subtarefa por nó.
1. O principal e todas as subtarefas baixar quaisquer **arquivos de recurso comuns** que você especificar nas configurações de várias instâncias.
1. Após o recurso comum arquivos foram baixados, principal e subtarefas execute o **comando de coordenação** especificado nas configurações de várias instâncias. O comando coordenação normalmente é usado para preparar nós para executar a tarefa. Isso pode incluir iniciando os serviços de plano de fundo (como o [Microsoft MPI][msmpi_msdn]do `smpd.exe`) e verificar se os nós estão prontos para processar mensagens de nó entre.
1. A principal tarefa executa o **comando de aplicativo** no nó mestre *após* que o comando coordenação foi concluído com êxito, o principal e todas as subtarefas. O comando de aplicativo é a linha de comando da tarefa várias instâncias propriamente dito e é executado somente pela tarefa principal. Em um [MS-MPI][msmpi_msdn]-solução, isso é onde você executa seu aplicativo habilitado para MPI usando `mpiexec.exe`.

> [AZURE.NOTE] Embora seja funcionalidade distinta, a tarefa"várias instâncias" não é um tipo de tarefa exclusivos como o [StartTask] [ net_starttask] ou [JobPreparationTask][net_jobprep]. A tarefa de várias instâncias é simplesmente uma tarefa em lote padrão ([CloudTask] [ net_task] no lote .NET) cujas configurações de várias instâncias foram configuradas. Neste artigo, vamos consulte como a **tarefa de várias instâncias**.

## <a name="requirements-for-multi-instance-tasks"></a>Requisitos para tarefas de várias instâncias

Tarefas de várias instâncias exigem um pool com **comunicação entre nós habilitado**e **execução de tarefas simultâneas desabilitada**. Se você tentar executar uma tarefa de várias instâncias em um pool com na comunicação desabilitada ou com um valor de *maxTasksPerNode* maior que 1, a tarefa é agendada nunca – ele indefinidamente permanece no estado "ativo". Este trecho de código mostra a criação de tal um pool usando a biblioteca de lote .NET.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicated: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

Além disso, tarefas de várias instâncias podem executar *somente* em nós **pools criados após 14 de dezembro de 2015**.

### <a name="use-a-starttask-to-install-mpi"></a>Use um StartTask para instalar o MPI

Para executar aplicativos MPI com uma tarefa de várias instâncias, primeiro é necessário instalar uma implementação MPI (MS-MPI ou MPI Intel, por exemplo) nos nós de computação no pool. Este é um bom momento para usar uma [StartTask][net_starttask], que é executado sempre que um nó ingressa um pool ou seja reiniciado. Este trecho de código cria um StartTask que especifica o pacote de instalação do MS-MPI como um [arquivo de recurso][net_resourcefile]. Linha de comando do início da tarefa é executada depois que o arquivo de recurso é baixado para o nó. Nesse caso, a linha de comando executa uma instalação autônoma do MS-MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Remoto acesso direto à memória (RDMA)

Quando você escolhe um [tamanho habilitados para RDMA](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) como A9 nós de computadores no pool de lote, seu aplicativo MPI possa aproveitar rede de acesso (RDMA) de alto desempenho e baixa latência memória direta remoto do Azure.

Procure os tamanhos especificados como "RDMA capaz" nos seguintes artigos:

* Pools de **CloudServiceConfiguration**

  * [Tamanhos para serviços de nuvem](../cloud-services/cloud-services-sizes-specs.md) (Somente Windows)

* Pools de **VirtualMachineConfiguration**

  * [Tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux)

  * [Tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows)

>[AZURE.NOTE] Para tirar proveito da RDMA no [Linux nós de computadores](batch-linux-nodes.md), você deve usar **MPI Intel** em nós. Para obter mais informações sobre pools CloudServiceConfiguration e VirtualMachineConfiguration, consulte a seção de [Pool](batch-api-basics.md#Pool) de visão geral dos recursos do lote.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Criar uma tarefa de várias instâncias com lote .NET

Agora que estamos abordados os requisitos de pool e a instalação do pacote MPI, vamos criar a tarefa de várias instâncias. Neste trecho, podemos criar um padrão [CloudTask][net_task], em seguida, configure seu [MultiInstanceSettings] [ net_multiinstance_prop] propriedade. Conforme mencionado anteriormente, a tarefa de várias instâncias não é um tipo de tarefa distintos, mas uma tarefa em lote padrão configurado com configurações de várias instâncias.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Tarefa principal e subtarefas

Quando você cria as configurações de várias instâncias de uma tarefa, você pode especificar o número de nós de computação que estão executar a tarefa. Quando você envia a tarefa para um trabalho, o serviço de lote cria uma tarefa **primária** e suficiente **subtarefas** que juntos corresponde ao número de nós que você especificou.

Essas tarefas são atribuídas a um id de inteiro no intervalo de 0 a *numberOfInstances* - 1. A tarefa com id 0 é a tarefa principal e todas as outras identificações são subtarefas. Por exemplo, se você criar as seguintes configurações de várias instâncias de uma tarefa, a tarefa principal terá uma identificação 0 e as subtarefas teria ids de 1 a 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Nó mestre

Quando você envia uma tarefa de várias instâncias, o serviço de lote designa um de nós de computação como o nó "mestre" e agenda a tarefa principal para executar o nó mestre. As subtarefas são agendadas para executar o restante de nós alocados para a tarefa de várias instâncias.

## <a name="coordination-command"></a>Comando de coordenação

O **comando de coordenação** é executado por ambos os primário e subtarefas.

A invocação do comando coordenação está bloqueando - lote não executar o comando de aplicativos até que o comando coordenação retornou com êxito para todas as subtarefas. O comando coordenação, portanto, deve iniciar todos os serviços necessários do plano de fundo, verifique se eles estão prontos para uso e saia. Por exemplo, este comando coordenação para uma solução usando a versão do MS-MPI 7 inicia o serviço SMPD no nó, em seguida, sai:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Observe o uso da `start` neste comando coordenação. Isso é necessário porque o `smpd.exe` aplicativo não retornar imediatamente após a execução. Sem o uso de [Iniciar] [ cmd_start] comando, esse comando coordenação não poderia retornar e, portanto, bloquear a execução do comando aplicativo.

## <a name="application-command"></a>Comando de aplicativo

Depois que a tarefa principal e todas as subtarefas terminar a execução do comando de coordenação, linha de comando da tarefa várias instâncias é executada da tarefa principal *somente*. Chamamos isso o **comando de aplicativo** para distingui-lo do comando coordenação.

Para aplicativos do MS-MPI, use o comando de aplicativo para executar o aplicativo habilitado para MPI com `mpiexec.exe`. Por exemplo, aqui está um comando de aplicativo para uma solução usando MS-MPI versão 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

>[AZURE.NOTE] Porque MS-MPI `mpiexec.exe` usa o `CCP_NODES` variável por padrão (consulte [variáveis de ambiente](#environment-variables)) o exemplo acima da linha de comando de aplicativo exclui-lo.

## <a name="environment-variables"></a>Variáveis de ambiente

Lote cria diversas [variáveis de ambiente] [ msdn_env_var] específicas para tarefas de várias instâncias nos nós de computação alocados para uma tarefa de várias instâncias. As linhas de comando coordenação e aplicativo pode fazer referência a essas variáveis de ambiente, como os scripts e programas para que serem executados.

As seguintes variáveis de ambiente são criadas pelo serviço lote para uso por tarefas de várias instâncias:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Para obter detalhes completos sobre estes e as variáveis de ambiente outros lote computação nó, incluindo o conteúdo e a visibilidade, consulte [variáveis de ambiente de nó de computação][msdn_env_var].

>[AZURE.TIP] O exemplo de código do lote Linux MPI contém um exemplo de como várias dessas variáveis de ambiente podem ser usadas. [Coordenação-cmd] [ coord_cmd_example] script Bash downloads aplicativo comuns e entrada arquivos de armazenamento do Azure, permite que um compartilhamento de rede NFS (File System) no nó mestre e configura os outros nós alocados para a tarefa de várias instâncias como clientes NFS.

## <a name="resource-files"></a>Arquivos de recurso

Há dois conjuntos de arquivos de recursos a serem consideradas para tarefas de várias instâncias: **arquivos de recurso comuns** que o download de *todas as* tarefas (áreas principais e subtarefas) e os **arquivos de recurso** especificado para a instância de várias tarefas em si, qual tarefa *somente o primário* downloads.

Você pode especificar um ou mais **arquivos de recurso comuns** nas configurações de várias instâncias de uma tarefa. Esses arquivos de recurso comuns são baixados do [Armazenamento do Azure](./../storage/storage-introduction.md) para cada nó **diretório compartilhado tarefas** por primário e todas as subtarefas. Você pode acessar o diretório compartilhado da tarefa de linhas de comando do aplicativo e coordenação usando o `AZ_BATCH_TASK_SHARED_DIR` variável de ambiente. O `AZ_BATCH_TASK_SHARED_DIR` caminho é idêntico em cada nó alocado para a tarefa de várias instâncias, portanto, você pode compartilhar um comando único coordenação entre o principal e todas as subtarefas. Lote não "compartilhar" o diretório em um sentido de acesso remoto, mas você pode usá-la como uma montagem ou compartilhar ponto conforme mencionado anteriormente na dica de variáveis de ambiente.

Arquivos de recursos que você especificar para a tarefa de várias instâncias em si são baixados para o diretório de trabalho da tarefa, `AZ_BATCH_TASK_WORKING_DIR`, por padrão. Conforme mencionado, em contraste com arquivos de recurso comuns, apenas a tarefa primária baixa arquivos de recurso especificados para a tarefa de várias instâncias em si.

> [AZURE.IMPORTANT] Sempre usar as variáveis de ambiente `AZ_BATCH_TASK_SHARED_DIR` e `AZ_BATCH_TASK_WORKING_DIR` para consultar esses diretórios nas suas linhas de comando. Não tente construir caminhos de manualmente.

## <a name="task-lifetime"></a>Ciclo de vida de tarefa

O ciclo de vida da tarefa primária controla a vida útil da tarefa inteira várias instâncias. Quando o principal é encerrado, todas as subtarefas são encerradas. O código de saída do primário é o código de saída da tarefa e, portanto, é usado para determinar o sucesso ou falha da tarefa para fins de repetição.

Se qualquer uma das subtarefas falhar, saindo com um código de retorno diferente de zero, por exemplo, a tarefa inteira várias instâncias falhará. A tarefa de várias instâncias, em seguida, é encerrada e repetida, até o seu limite de repetição.

Quando você exclui uma tarefa de várias instâncias, o principal e todas as subtarefas também são excluídas pelo serviço lote. Todos subtarefa diretórios e seus arquivos são excluídos de nós de computação, assim como uma tarefa padrão.

[TaskConstraints] [ net_taskconstraints] para uma tarefa de várias instâncias, como o [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock]e [RetentionTime] [ net_taskconstraint_retention] propriedades, são cumpridos estão para uma tarefa padrão e aplicar a principal e todas as subtarefas. No entanto, se você alterar o [RetentionTime] [ net_taskconstraint_retention] propriedade depois de adicionar a tarefa de várias instâncias do trabalho, essa alteração é aplicada somente para a tarefa principal. Todas as subtarefas continuam a usar o original [RetentionTime][net_taskconstraint_retention].

Lista de tarefas recentes de um nó de computação reflete a id de uma subtarefa se a tarefa recente for parte de uma tarefa de várias instâncias.

## <a name="obtain-information-about-subtasks"></a>Obtenha informações sobre subtarefas

Para obter informações sobre subtarefas usando a biblioteca de lote .NET, ligue o [CloudTask.ListSubtasks] [ net_task_listsubtasks] método. Esse método retorna informações sobre todas as subtarefas e informações sobre o nó de computação que executadas as tarefas. Essas informações, você pode determinar o diretório raiz de cada da subtarefa, a id do pool, seu estado atual, código de saída e muito mais. Você pode usar essas informações em combinação com o [PoolOperations.GetNodeFile] [ poolops_getnodefile] método para obter os arquivos da subtarefa. Observe que este método não retornar informações para a tarefa principal (id 0).

> [AZURE.NOTE] A menos que explicitamente indicado, métodos .NET lote que operam a várias instâncias [CloudTask] [ net_task] próprio aplicam-se *somente* à tarefa principal. Por exemplo, quando você chama o [CloudTask.ListNodeFiles] [ net_task_listnodefiles] método em uma tarefa de várias instâncias, somente os arquivos da tarefa principal são retornados.

O trecho de código a seguir mostra como obter as informações sobre subtarefas, bem como solicitar o conteúdo do arquivo de nós no qual elas executadas.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Amostra de código

O [MultiInstanceTasks] [ github_mpi] código de exemplo no GitHub demonstra como usar uma tarefa de várias instâncias para executar um [MS-MPI] [ msmpi_msdn] nós de computadores do aplicativo em lote. Siga as etapas em [preparação](#preparation) e a [execução](#execution) para executar a amostra.

### <a name="preparation"></a>Preparação

1. Siga as duas primeiras etapas [como compilar e executar um programa simples do MS-MPI][msmpi_howto]. Isso satisfaz a prerequesites para a etapa seguinte.
1. Criar uma versão de *lançamento* do [MPIHelloWorld] [ helloworld_proj] programa de MPI de exemplo. Este é o programa que será executado em nós de computação pela tarefa várias instâncias.
1. Criar um arquivo zip contendo `MPIHelloWorld.exe` (que você criados etapa 2) e `MSMpiSetup.exe` (que você baixou etapa 1). Você carregará este arquivo zip como um pacote de aplicativos na próxima etapa.
1. Usar o [portal do Azure] [ portal] para criar um lote de [aplicativo](batch-application-packages.md) chamado "MPIHelloWorld" e especifique o arquivo zip criado na etapa anterior como versão "1.0" do pacote de aplicativo. Para obter mais informações, consulte [carregar e gerenciar aplicativos](batch-application-packages.md#upload-and-manage-applications) .

>[AZURE.TIP] Criar uma versão de *lançamento* do `MPIHelloWorld.exe` para que você não precisa incluir quaisquer dependências adicionais (por exemplo, `msvcp140d.dll` ou `vcruntime140d.dll`) no seu pacote de aplicativo.

### <a name="execution"></a>Execução

1. Baixe as [amostras de lote azure] [ github_samples_zip] do GitHub.
1. Abra a MultiInstanceTasks **solução** no Visual Studio de 2015. O `MultiInstanceTasks.sln` solução arquivo está localizado na:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`

1. Insira suas credenciais de conta de armazenamento e lote no `AccountSettings.settings` no projeto **Microsoft.Azure.Batch.Samples.Common** .
1. **Criar e executar** a solução de MultiInstanceTasks para executar o MPI o aplicativo de exemplo em calcular nós em um pool de lote.
1. *Opcional*: usar o [portal do Azure] [ portal] ou o [Lote Explorer] [ batch_explorer] para examinar o pool de exemplo, o trabalho e tarefas ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") antes de excluir os recursos.

>[AZURE.TIP] Você pode baixar a [Comunidade do Visual Studio] [ visual_studio] gratuitamente se você não tiver o Visual Studio.

Saída do `MultiInstanceTasks.exe` é semelhante à seguinte:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Próximas etapas

- Blog do Microsoft HPC & Azure lote equipe discute [MPI suporte para Linux no lote Azure][blog_mpi_linux]e inclui informações sobre como usar o [OpenFOAM] [ openfoam] com lotes. Você pode encontrar exemplos de código do Python para o [exemplo de OpenFOAM em GitHub][github_mpi].

- Saiba como [criar grupos de nós de computação Linux](batch-linux-nodes.md) para uso em suas soluções Azure lote MPI.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Visão geral de várias instâncias"
