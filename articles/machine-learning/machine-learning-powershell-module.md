<properties
    pageTitle="Módulo do PowerShell para aprendizado de máquina | Microsoft Azure"
    description="O módulo do PowerShell para aprendizado de máquina do Azure está disponível no modo de visualização pública. Use o PowerShell para criar e gerenciar espaços de trabalho, experiências, serviços web e muito mais."
    keywords="experimentar, regressão linear, algoritmos de aprendizado de máquina, tutorial de aprendizado de máquina, técnicas de modelagem de previsão, experimento de ciência de dados"
    services="machine-learning"
    documentationCenter=""
    authors="hning86"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/05/2016"
    ms.author="garye;haining"/>

# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Módulo do PowerShell para aprendizado de máquina do Microsoft Azure

O módulo do PowerShell para aprendizado de máquina do Azure é uma ferramenta poderosa que permite usar o Windows PowerShell para gerenciar espaços de trabalho, experiências, conjuntos de dados, serivces web e muito mais.

Você pode exibir a documentação e baixar o módulo, juntamente com o código fonte completo, em [https://aka.ms/amlps](https://aka.ms/amlps). 

## <a name="what-is-the-machine-learning-powershell-module"></a>O que é o módulo do PowerShell de aprendizado de máquina?

O módulo do PowerShell de aprendizado de máquina é um. Baseada em líquido DLL módulo permite que você gerencie totalmente aprendizado de máquina do Azure espaços de trabalho, experiências, conjuntos de dados, serviços web e pontos de extremidade de serviço web do Windows PowerShell. Junto com o módulo, você pode baixar o código-fonte completo que inclui um integralmente separados [c# API camada](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). Isso significa que você pode fazer referência a essa DLL de seu próprio projeto .NET e gerenciar aprendizado de máquina do Azure por meio de código .NET. Além disso, a DLL depende subjacente APIs REST que você pode usufruir diretamente do seu cliente de favorito.

## <a name="what-can-i-do-with-the-powershell-module"></a>O que posso fazer com o módulo do PowerShell?

Aqui estão algumas das tarefas que você pode executar com este módulo do PowerShell. Confira a [documentação completa](https://aka.ms/amlps) para esses e muito mais funções.

- Provisionar um novo espaço de trabalho usando um certificado de gerenciamento ([Novo AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- Exportar e importar um arquivo JSON que represente um gráfico de experimento ([AmlExperimentGraph de exportação](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [Importação-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
- Executar um experimento ([Início-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
- Criar um serviço web fora um experimento previsão ([Novo AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- Criar um novo ponto de extremidade em um serviço da web publicado ([Adicionar-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- Invocar um RR e/ou BES web serviço ponto de extremidade ([Chamar AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) e [Chamar AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Aqui está um exemplo rápido do uso do PowerShell para executar um experimento existente:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Para um caso de uso mais detalhado, consulte o artigo sobre como usar o módulo do PowerShell para automatizar uma tarefa muito frequentemente solicitados: [criar muitos modelos de aprendizado de máquina e web pontos de extremidade do serviço de um experimento usando o PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Como faço para começar a?

Para começar a usar o PowerShell de aprendizado de máquina, baixe o [pacote de lançamento](https://github.com/hning86/azuremlps/releases) do GitHub e siga as [instruções de instalação](https://github.com/hning86/azuremlps/blob/master/README.md). Você precisará desbloquear a DLL baixados/descompactados e importe-os para seu ambiente do PowerShell. A maioria dos cmdlets exige que você forneça a identificação de espaço de trabalho, o token de autorização de espaço de trabalho e a região Azure que o espaço de trabalho está em. A maneira mais simples de oferecer estes é por meio de um arquivo de config.json padrão, que é coberto detalhadamente nas instruções de instalação. Claro, você pode também clonar a árvore gito e modificar/compilar o código localmente usando o Visual Studio.

## <a name="next-steps"></a>Próximas etapas

O módulo do PowerShell continuará a ser aprimorado e expandido durante esse período de visualização. Fique atento a [inteligência de Cortana e Blog de aprendizado de máquina](https://blogs.technet.microsoft.com/machinelearning/) para obter mais informações e notícias.
