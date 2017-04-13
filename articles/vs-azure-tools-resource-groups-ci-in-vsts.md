<properties
    pageTitle="Integração contínua em VS Team Services usando projetos de grupo de recursos do Azure | Microsoft Azure"
    description="Descreve como configurar integração contínua no Visual Studio Team Services usando projetos de implantação de grupo de recursos do Azure no Visual Studio."
    services="visual-studio-online"
    documentationCenter="na"
    authors="mlearned"
    manager="erickson-doug"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="mlearned" />

# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>Integração contínua no Visual Studio Team Services usando projetos de implantação de grupo de recursos do Azure

Para implantar um modelo do Azure, você precisará executar tarefas para percorrer os vários estágios: cópia de criação, teste, no Azure (também chamado de "Teste") e implantar o modelo.  Há duas maneiras diferentes para fazer isso no Visual Studio Team Services (VS Team Services). Os dois métodos fornecem os mesmos resultados, portanto escolha aquele que melhor se adapte seu fluxo de trabalho.

-   Adicione uma única etapa a sua definição de compilação que executa o script do PowerShell que está incluído no projeto de implantação de grupo de recursos do Azure (implantar-AzureResourceGroup.ps1). O script copia artefatos e, em seguida, implanta o modelo.
-   Adicione que vários serviços de equipe VS construir etapas, cada um deles executando uma tarefa de estágio.

Este artigo mostra como usar a primeira opção (use uma definição de compilação para executar o script do PowerShell). Uma vantagem dessa opção é que o script usado por desenvolvedores no Visual Studio é o mesmo script que é usado pelo VS Team Services. Este procedimento supõe que você já tem um projeto de implantação do Visual Studio verificado no VS Team Services.

## <a name="copy-artifacts-to-azure"></a>Copiar artefatos para Azure 

Independentemente do cenário, se você tiver qualquer artefatos que são necessários para implantação de modelo, você precisará dar acesso do Gerenciador de recursos do Azure para eles. Esses artefatos podem incluir arquivos, tais como:

-   Modelos aninhados
-   Scripts de configuração e DSC
-   Binários do aplicativo

### <a name="nested-templates-and-configuration-scripts"></a>Modelos aninhados e Scripts de configuração
Quando você usa os modelos fornecidos pelo Visual Studio (ou construídas com trechos de código do Visual Studio), o script do PowerShell estágios não apenas os artefatos, ele também parameterizes o URI para os recursos para implantações diferentes. O script e em seguida, copia os artefatos para um contêiner de seguro no Azure, cria um token de SaS para contêiner e passa essas informações para a implantação do modelo. Consulte [criar uma implantação de modelo](https://msdn.microsoft.com/library/azure/dn790564.aspx) para saber mais sobre modelos aninhados.

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>Configurar a implantação contínua no VS Team Services

Para chamar o script do PowerShell no VS Team Services, você precisa atualizar sua definição de compilação. Em resumo, as etapas são: 

1.  Edite a definição de compilação.
1.  Configure o Azure autorização em serviços de equipe do VS.
1.  Adicione uma etapa de compilação do PowerShell do Azure que referencia o script do PowerShell do projeto de implantação do grupo de recursos do Azure.
1.  Defina o valor do parâmetro *- ArtifactsStagingDirectory* para trabalhar com um projeto criado no VS Team Services.

### <a name="detailed-walkthrough"></a>Explicação passo a passo detalhada

As etapas a seguir apresentará as etapas necessárias para configurar a implantação contínua no VS Team Services 

1.  Edite sua definição de compilação VS Team Services e adicione uma etapa de compilação do PowerShell do Azure. Escolha a definição de compilação sob a categoria **Construir definições** e escolha o link **Editar** .

    ![][0]

1.  Adicionar uma nova etapa de compilação do **PowerShell do Azure** para a definição de compilação e escolha **Adicionar construir etapa...** botão.

    ![][1]

1.  Escolha a categoria de **tarefa de implantar** , selecione a tarefa do **PowerShell do Azure** e escolha o botão **Adicionar** .

    ![][2]

1.  Escolha a etapa de compilação do **PowerShell do Azure** e em seguida, preencha seus valores.

    1.  Se você já tiver um ponto de extremidade do serviço Azure adicionado ao VS Team Services, escolha a assinatura na caixa de listagem suspensa **Assinatura do Azure** e vá para a próxima seção. 

        Se você não tiver um ponto de extremidade do serviço Azure no VS Team Services, você precisará adicioná-lo. Esta subseção o guiará durante o processo. Se a sua conta do Azure usa uma conta da Microsoft (como Hotmail), você precisará levar as etapas a seguir para obter uma autenticação de capital de serviço.

    1.  Escolha o link **Gerenciar** ao lado da **Assinatura do Azure** suspensa de caixa de listagem.

        ![][3]

    1. Escolha **Azure** na caixa de listagem suspensa **Novo ponto de extremidade do serviço** .

        ![][4]

    1.  Na caixa de diálogo **Adicionar Azure assinatura** , selecione a opção de **Serviço Principal** .

        ![][5]

    1.  Adicione suas informações de assinatura Azure à caixa de diálogo **Adicionar a assinatura do Azure** . Você precisará fornecer os seguintes itens:
        -   Id da assinatura
        -   Nome da assinatura
        -   Id de entidade de serviço
        -   Chave de serviço Principal
        -   Id do locatário

    1.  Adicione um nome de sua preferência para a caixa de nome de **assinatura** . Esse valor será exibido mais tarde na lista no VS Team Services suspensa **Assinatura do Azure** . 

    1.  Se você não sabe sua ID do Azure assinatura, você pode usar um dos seguintes comandos para colocá-lo.
        
        Para scripts do PowerShell, use:

        `Get-AzureRmSubscription`

        Para CLI do Azure, use:

        `azure account show`
    

    1.  Para obter uma identificação de capital de serviço, chave de serviço Principal e ID de locatário, siga o procedimento no [aplicativo de criar Active Directory e capital de serviço usando o portal](resource-group-create-service-principal-portal.md) ou [autenticar um principal de serviço com o Gerenciador de recursos do Azure](resource-group-authenticate-service-principal.md).

    1.  Adicione os valores de ID de capital de serviço, chave de serviço Principal e ID de locatário para a caixa de diálogo **Adicionar a assinatura do Azure** e escolha o botão de **Okey** .

        Agora você tem um Principal de serviço válido para usar para executar o script do PowerShell do Azure.

1.  Editar a definição de compilação e escolha a etapa de compilação do **PowerShell do Azure** . Selecione a assinatura na caixa de listagem suspensa **Assinatura do Azure** . (Se a assinatura não aparecer, escolha o botão **Atualizar** próximo o link **Gerenciar** ). 

    ![][8]

1.  Fornece um caminho para o script do PowerShell implantar-AzureResourceGroup.ps1. Para fazer isso, escolha o botão de reticências (...) ao lado da caixa **Caminho do Script** , navegue até o script PowerShell implantar AzureResourceGroup.ps1 na pasta **Scripts** do seu projeto, selecione-o e, em seguida, escolha o botão de **Okey** . 

    ![][9]

1. Após selecionar o script, atualize o caminho para o script para que seja executado a partir do Build.StagingDirectory (o mesmo diretório que *ArtifactsLocation* está definido como). Você pode fazer isso adicionando "$(Build.StagingDirectory)/"para o início do caminho do script.

    ![][10]

1.  Na caixa **Argumentos de Script** , insira os seguintes parâmetros (em uma única linha). Quando você executa o script no Visual Studio, você pode ver como o VS usa os parâmetros na janela de **saída** . Você pode usar isso como ponto de partida para definir os valores de parâmetro em sua etapa de compilação.

  	| Parâmetro | Descrição|
  	|---|---|
  	| -ResourceGroupLocation           | O valor de localização geográfica onde se encontra, como **eastus** ou **' Leste EUA '**o grupo de recursos. (Adicionar aspas simples se houver um espaço no nome). Consulte [Regiões do Azure](https://azure.microsoft.com/en-us/regions/) para obter mais informações.|                                                                                                                                                                                                                              |
  	| -ResourceGroupName               | O nome do grupo de recursos usado para essa implantação.|                                                                                                                                                                                                                                                                                                                                                                                                                |
  	| -UploadArtifacts                 | Este parâmetro, quando presente, especifica que artefatos precisam ser carregado Azure do sistema local. Você só precisa definir essa opção se sua implantação do modelo requer artefatos extras que você deseja testar usando o script do PowerShell (como scripts de configuração ou modelos aninhados).                                                                                                                                                                 |
  	| -StorageAccountName              | O nome da conta de armazenamento usado para artefatos de estágio para essa implantação. Este parâmetro é necessário somente se você estiver copiando artefatos no Azure. Esta conta de armazenamento não será criada automaticamente pela implantação, já deve existir.|                                                                                                                                                                                                                     |
  	| -StorageAccountResourceGroupName | O nome do grupo de recursos associado à conta de armazenamento. Este parâmetro é necessário somente se você estiver copiando artefatos no Azure.|                                                                                                                                                                                                                                                                                                                               |
  	| -TemplateFile                    | O caminho para o arquivo de modelo do projeto de implantação do grupo de recursos do Azure. Para aumentar a flexibilidade, use um caminho para esse parâmetro que é relativo a localização do script PowerShell em vez de um caminho absoluto.|
  	| -TemplateParametersFile          | O caminho para o arquivo de parâmetros do projeto de implantação do grupo de recursos do Azure. Para aumentar a flexibilidade, use um caminho para esse parâmetro que é relativo a localização do script PowerShell em vez de um caminho absoluto.|
  	| -ArtifactStagingDirectory        | Este parâmetro permite que o PowerShell script conhece a pasta de onde os arquivos binários do projeto devem ser copiados. Esse valor substitui o valor padrão usado pelo script PowerShell. Para usar os serviços de equipe do VS, defina o valor como: - ArtifactStagingDirectory $(Build.StagingDirectory)                                                                                                                                                                                              |

    Aqui está um exemplo de argumentos de script (linha quebrada para legibilidade):

    ``` 
    -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
    -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
    –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)' 
    ```

    Quando tiver terminado, a caixa **Argumentos de Script** deve semelhante ao seguinte:

    ![][11]

1.  Depois de adicionar que todos os itens necessários para o PowerShell Azure etapa de construção, escolha **fila** build botão para construir o projeto. A tela **criar** mostra a saída do script PowerShell.

## <a name="next-steps"></a>Próximas etapas

Leia a [Visão geral do Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md) para saber mais sobre o Gerenciador de recursos do Azure e grupos de recursos Azure.


[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
