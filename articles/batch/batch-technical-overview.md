<properties
    pageTitle="Noções básicas de serviço do Azure lote | Microsoft Azure"
    description="Saiba como usar o serviço de lote do Azure para paralelo grande escala e cargas de trabalho HPC"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/22/2016"
    ms.author="marsma"/>

# <a name="basics-of-azure-batch"></a>Noções básicas do lote Azure

Lote Azure permite executar aplicativos de grande escala paralelas e de alto desempenho computação (HPC) com eficiência na nuvem. É um serviço de plataforma que agenda o trabalho pesados para executar em um conjunto gerenciado de máquinas virtuais, e pode automaticamente escala de recursos de computação para atender às necessidades dos seus trabalhos.

Com o serviço de lote, você define recursos de computação Azure para executar seus aplicativos em paralelo e em escala. Você pode executar sob demanda ou agendada trabalhos e você não precisa criar manualmente, configurar e gerenciar um cluster de HPC, máquinas virtuais individuais, redes virtuais ou um trabalho complexo e infraestrutura de agendamento da tarefa.

## <a name="use-cases-for-batch"></a>Casos de uso para lote

Lote é um serviço Azure gerenciado que é usado para *processamento em lotes* ou *lote computação*– executando um grande volume de tarefas semelhantes para obter algumas resultado desejado. Computação de lote é mais comumente usado por organizações que processam regularmente, transformam e analisar grandes volumes de dados.

Lote funciona bem com cargas de trabalho e aplicativos (também conhecido como "paralelos") intrinsecamente paralelos. Cargas de trabalho paralelas intrinsecamente facilmente são divididas em várias tarefas que realizar o trabalho simultaneamente em vários computadores.

![Tarefas paralelas][1]<br/>

Alguns exemplos de cargas de trabalho que são processadas comumente usando essa técnica são:

* Modelagem de risco financeiro
* Análise de dados de clima e hydrology
* Processamento, análise e processamento de imagens
* Codificação de mídia e transcodificação
* Análise de sequência genética
* Análise de carga de engenharia
* Teste de software

Lote também pode executar cálculos paralelos com uma etapa de reduzir no final e executar mais complexas cargas de trabalho HPC como aplicativos de [Interface de transmissão de mensagens (MPI)](batch-mpi.md) .

Para uma comparação entre o lote e outras opções de solução HPC no Azure, consulte [soluções lote e HPC](batch-hpc-solutions.md).

## <a name="developing-with-batch"></a>Desenvolvendo com lote

Processamento paralelas cargas de trabalho com lote geralmente é feito por programação usando uma das [APIs de lote](#batch-development-apis). Com as APIs de lote, você criar e gerenciar pools de nós de computação (máquinas virtuais) e agendar trabalhos e tarefas para executar em nós. Um aplicativo cliente ou serviço que você cria o usa as APIs de lote para se comunicar com o serviço de lote.

Você pode com eficiência cargas de trabalho grande escala de processo para sua organização ou fornecer um front-end do serviço aos seus clientes para que eles podem ser executados trabalhos e tarefas, sob demanda, ou em um cronograma – em um, centenas ou mesmo milhares de nós. Você também pode usar o lote como parte de um fluxo de trabalho maior, gerenciado por ferramentas como [Fábrica de dados do Azure](../data-factory/data-factory-data-processing-using-batch.md).

> [AZURE.TIP] Quando você estiver pronto para se aprofundar no lote API para obter uma compreensão mais detalhadamente os recursos que ele oferece, confira a [Visão geral do recurso de lote para desenvolvedores](batch-api-basics.md).

### <a name="azure-accounts-youll-need"></a>Você precisará de contas Azure

Quando você desenvolver soluções de lote, você usará as contas a seguir no Microsoft Azure.

- **Assinatura e conta do azure** - se você ainda não tiver uma assinatura do Azure, você pode ativar o [assinante MSDN usufruir dos benefícios][msdn_benefits], ou inscrever-se para uma [conta gratuita do Azure][free_account]. Quando você cria uma conta, uma assinatura padrão é criada.

- **Conta de lote** - quando seus aplicativos interagem com o serviço de lote, o nome da conta, a URL da conta e uma tecla de acesso são usados como credenciais. Todos os recursos do lote como pools, calcular nós, trabalhos, e tarefas estão associadas uma conta de lote. Você pode [Criar conta de lote](batch-account-create-portal.md) no portal do Azure.

- **Conta de armazenamento** - lote inclui suporte interno para trabalhar com arquivos em [Armazenamento do Azure][azure_storage]. Quase todos os cenários de lote usa o armazenamento do Azure – para os programas que executam suas tarefas e os dados que eles do processo de teste e para o armazenamento de dados de saída que eles geram. Para criar uma conta de armazenamento, consulte [contas de armazenamento do Azure sobre](./../storage/storage-create-storage-account.md).

### <a name="batch-development-apis"></a>APIs de desenvolvimento do lote

Seus aplicativos e serviços podem emitir chamadas de API REST diretas, use um ou mais dos seguintes bibliotecas de cliente ou uma combinação de ambos para gerenciar computação recursos e executar cargas de trabalho paralelas em escala usando o serviço de lote.

| API    | Referência da API | Baixar | Exemplos de código |
| ----------------- | ------------- | -------- | ------------ |
| **Lote restante** | [MSDN][batch_rest] | N/D | [MSDN][batch_rest] |
| **Lote .NET**    | [MSDN][api_net] | [NuGet][api_net_nuget] | [GitHub][api_sample_net] |
| **Python em lotes**  | [readthedocs.IO][api_python] | [PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Lote Node** | [GitHub.IO][api_nodejs] | [NPM][api_nodejs_npm] | - |
| **Lote Java** (visualização) | [GitHub.IO][api_java] | [Maven][api_java_jar] | [GitHub][api_sample_java] |

### <a name="batch-resource-management"></a>Gerenciamento de recursos de lote

Além das APIs cliente, você também pode usar o seguinte para gerenciar recursos dentro de sua conta de lote.

- [Lote cmdlets do PowerShell][batch_ps]: O lote de Azure cmdlets no módulo [Azure PowerShell](../powershell-install-configure.md) permitem gerenciar recursos de lote com o PowerShell.

- [Azure CLI](../xplat-cli-install.md): A Interface de linha de comando do Azure (Azure CLI) é um conjunto de ferramentas de várias plataformas que fornece os comandos de shell de interação com muitos serviços Azure, incluindo o lote.

- Biblioteca de cliente [.NET de gerenciamento de lote](batch-management-dotnet.md) : também disponível via [NuGet][api_net_mgmt_nuget], você pode usar a biblioteca de cliente .NET de gerenciamento de lote programaticamente para gerenciar contas de lote, cotas e pacotes de aplicativos. Referência para a biblioteca de gerenciamento está no [MSDN][api_net_mgmt].

### <a name="batch-tools"></a>Ferramentas de lote

Embora não seja necessário para desenvolver soluções usando o lote, aqui estão algumas ferramentas valiosas para usar ao criar e depurar seus lote aplicativos e serviços.

 - [Portal do Azure][portal]: você pode criar, monitorar e excluir pools lote, trabalhos e tarefas em lâminas de lote do portal Azure. Você pode exibir as informações de status para esses e outros recursos enquanto você executar seus trabalhos e até mesmo baixar arquivos dos nós de computação em seus pools (baixar uma tarefa com falha `stderr.txt` durante a solução de problemas, por exemplo). Você também pode baixar arquivos de área de trabalho remota (RDP) que você pode usar para efetuar login em nós de computadores.

 - [Azure Explorer lote][batch_explorer]: lote Explorer fornece lote recurso Gerenciamento funcionalidade similar como o portal do Azure, mas em um aplicativo de cliente do Windows Presentation Foundation (WPF) autônomo. Um dos aplicativos de amostra do lote .NET disponíveis no [GitHub][github_samples], você pode criá-la com o Visual Studio 2015 ou acima e usá-lo para procurar e gerenciar os recursos na sua conta do lote enquanto você desenvolver e depurar suas soluções de lote. Exibir trabalho, pool e detalhes da tarefa, baixar arquivos de nós de computação e se conectar a nós remotamente usando arquivos de área de trabalho remota (RDP) você pode baixar com Explorer de lote.

 - [Gerenciador de armazenamento do Microsoft Azure][storage_explorer]: enquanto não terminantemente uma ferramenta de lote do Azure, o Gerenciador de armazenamento é outra ferramenta valiosa ter enquanto você estiver desenvolvendo e suas soluções de lote de depuração.

## <a name="scenario-scale-out-a-parallel-workload"></a>Cenário: Escala uma carga de trabalho paralelas

Uma solução comum que usa as APIs de lote para interagir com o serviço de lote envolve dimensionamento trabalho intrinsecamente paralelo, como o processamento de imagens para cenas 3D – em um pool de nós de computação. Este pool de nós de computação pode ser seu "farm de renderização" que fornece dezenas, centenas ou até mesmo milhares de cores para o trabalho de processamento, por exemplo.

O diagrama a seguir mostra um lote fluxo de trabalho comum, com um aplicativo cliente ou serviço hospedado usando lote para executar uma carga de trabalho paralela.

![Fluxo de trabalho de solução de lote][2]

Neste cenário comum, o aplicativo ou serviço processa uma carga de trabalho de computação em lote do Azure, executando as seguintes etapas:

1. Carregue os **arquivos de entrada** e o **aplicativo** que processará esses arquivos para sua conta de armazenamento do Azure. Os arquivos de entrada podem ser quaisquer dados que seu aplicativo processará, como dados de modelagem financeira ou arquivos de vídeo sejam transcodificados. Os arquivos do aplicativo podem ser qualquer aplicativo que é usado para processar os dados, como um aplicativo de processamento 3D ou transcodificador de mídia.

2. Criar um lote **pool** de nós de computação na sua conta de lote – esses nós são as máquinas virtuais que executará suas tarefas. Você especifica propriedades como o [tamanho de nó](./../cloud-services/cloud-services-sizes-specs.md), seu sistema operacional e o local de armazenamento do Azure do aplicativo para instalar quando os nós ingressar o pool (o aplicativo que você carregou na etapa #1). Você também pode configurar o pool [Dimensionar automaticamente](batch-automatic-scaling.md)- dinamicamente ajustar o número de nós de computação no pool – em resposta à carga de trabalho que geram suas tarefas.

3. Crie um lote de **trabalho** para executar a carga de trabalho no pool de nós de computação. Quando você cria um trabalho, você pode associá-lo com um pool de lote.

4. Adicione **tarefas** ao trabalho. Quando você adiciona tarefas a um trabalho, o serviço de lote automaticamente agenda as tarefas para execução em nós de computação no pool. Cada tarefa usa o aplicativo que você carregou para processar arquivos de entrada.

    - 4a. Antes de uma tarefa é executada, ela pode baixar os dados (arquivos de entrada) que é o processo para o nó de computação a que é atribuído. Se o aplicativo já não foi instalado no nó (consulte a etapa 2 #), ele pode ser baixado aqui, em vez disso. Quando os downloads são concluídos, as tarefas executar em seus nós atribuído.

5. Como executar as tarefas, você pode consultar lote para monitorar o andamento do trabalho e suas tarefas. Seu aplicativo cliente ou serviço se comunica com o serviço de lote em HTTPS e como você pode monitorar milhares de tarefas em execução no milhares de nós de computação, certifique-se de [consultar o serviço de lote com eficiência](batch-efficient-list-queries.md).

6. Como as tarefas completas, eles podem carregar seus dados de resultado ao armazenamento do Azure. Você também pode recuperar arquivos diretamente a partir de nós de computação.

7. Quando o monitoramento detecta que concluiu as tarefas no seu trabalho, seu aplicativo cliente ou serviço pode baixar os dados de saída para processamento posterior ou avaliação.

Manter em mente isso é apenas uma maneira de usar o lote e esse cenário só descreve alguns dos seus recursos disponíveis. Por exemplo, você pode executar [várias tarefas em paralelo](batch-parallel-node-tasks.md) em cada nó de computação e você pode usar [as tarefas de conclusão e preparação do trabalho](batch-job-prep-release.md) para preparar os nós para seus trabalhos, em seguida, limpar posteriormente.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do serviço do lote, é hora para investigar mais para saber como você pode usá-lo para processar suas cargas de trabalho paralelas pesados.

- Leia a [Visão geral do recurso de lote para desenvolvedores](batch-api-basics.md), informações essenciais para qualquer pessoa Preparando para usar em lotes. O artigo contém informações mais detalhadas sobre os recursos de serviço de lote como pools, nós, trabalhos e tarefas e muitos recursos API que você pode usar ao criar o seu aplicativo de lote.

- [Começar a usar a biblioteca de lote do Azure para .NET](batch-dotnet-get-started.md) para saber como usar c# e a biblioteca de lote .NET para executar uma carga de trabalho simple usando um fluxo de trabalho de lote comum. Este artigo deve ser um dos seus paradas primeira durante a aprender a usar o serviço de lote. Também há uma [versão de Python](batch-python-tutorial.md) do tutorial.

- Baixe os [exemplos de código no GitHub] [ github_samples] para ver como c# e Python pode interface com lote a agenda e processo cargas de trabalho de exemplo.

- Confira o [Caminho de aprendizagem do lote] [ learning_path] para obter uma ideia dos recursos disponíveis para você enquanto você aprende a trabalhar com lote.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png
