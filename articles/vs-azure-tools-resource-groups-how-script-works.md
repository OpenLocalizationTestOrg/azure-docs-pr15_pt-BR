<properties
    pageTitle="Visão geral do script de implantação de projeto de grupo de recursos do Azure | Microsoft Azure"
    description="Descreve como funciona o script do PowerShell do projeto de implantação do grupo de recursos do Azure."
    services="visual-studio-online"
    documentationCenter="na"
    authors="tfitzmac"
    manager="timlt"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/26/2016"
    ms.author="tomfitz" />

# <a name="overview-of-the-azure-resource-group-project-deployment-script"></a>Visão geral do script de implantação de projeto de grupo de recursos do Azure

Implantação de grupo de recursos do Azure projetos ajudam você a testar e implantar arquivos e outros artefatos no Azure. Quando você cria um projeto de implantação do Gerenciador de recursos do Azure no Visual Studio, um script do PowerShell chamado **Implantar AzureResourceGroup.ps1** é adicionado ao projeto. Este tópico fornece detalhes sobre o que esse script faz e como executá-la dentro e fora do Visual Studio.

## <a name="what-does-the-script-do"></a>O que faz o script?

O script de implantação AzureResourceGroup.ps1 faz duas coisas importantes para o fluxo de trabalho de implantação.

- Carregar arquivos ou artefatos necessários para a implantação de modelo
- Implante o modelo

A primeira parte do script carrega os arquivos e artefatos para implantação e o último cmdlet no script realmente implantar o modelo. Por exemplo, se uma máquina virtual precisa ser configurada com um script, o script de implantação primeiro com segurança carrega o script de configuração para uma conta de armazenamento do Azure. Isso torna disponíveis ao Gerenciador de recursos do Azure para configurar a máquina virtual durante a configuração.

Porque não todas as implantações de modelo precisam ter artefatos adicionais que precisam ser carregado, um parâmetro de mudança chamado *uploadArtifacts* é avaliado. Se qualquer artefatos precisam ser carregado, defina a opção de *uploadArtifacts* ao chamar o script. Observe que o arquivo de modelo principal e o arquivo de parâmetros não precisam ser carregado. Somente outros arquivos, como scripts de configuração, aninhados modelos de implantação e os arquivos de aplicativo precisam ser carregados.

## <a name="detailed-script-description"></a>Descrição de script detalhado

A seguir é uma descrição das quais selecionadas seções do script do PowerShell do Azure implantar AzureResourceGroup.ps1 fazem.

>[AZURE.NOTE] Descreve versão 1.0 do script AzureResourceGroup.ps1 implantar.

1.  Declare parâmetros necessários para o projeto de implantação do Gerenciador de recursos do Azure. Alguns parâmetros têm valores padrão que foram definidos quando o projeto foi criado. Você pode alterar esses valores padrão no script ou adicionar valores de parâmetros diferentes antes de executar o script.

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

  	|Parâmetro|Descrição|
  	|---|---|
  	|$ResourceGroupLocation|A região ou data center local para o grupo de recursos, como **Oeste EUA** ou **da Ásia Oriental**.|
  	|$ResourceGroupName|O nome do grupo de recursos Azure.|
  	|$UploadArtifacts|Um valor binário que indica se artefatos precisam ser carregados no Azure em seu sistema.|
  	|$StorageAccountName|O nome da sua conta de armazenamento do Azure onde seu artefatos são carregados.|
  	|$StorageAccountResourceGroupName|O nome do grupo de recursos Azure que contém a conta de armazenamento.|
  	|$StorageContainerName|O nome do contêiner de armazenamento usado para carregar artefatos.|
  	|$TemplateFile|O caminho para o arquivo de implantação (`<app name>.json`) no seu projeto de grupo de recursos do Azure.|
  	|$TemplateParametersFile|O caminho para o arquivo de parâmetros (`<app name>.parameters.json`) no seu projeto de grupo de recursos do Azure.|
  	|$ArtifactStagingDirectory|O caminho em seu sistema onde artefatos localmente carregados, incluindo a pasta de raiz de script do PowerShell. Este caminho pode ser absoluto ou relativo a localização de script.|
  	|$AzCopyPath|O caminho onde a ferramenta de AzCopy.exe copia seus arquivos. zip, incluindo a pasta de raiz de script do PowerShell. Este caminho pode ser absoluto ou relativo a localização de script.|
  	|$DSCSourceFolder|O caminho para a pasta de origem DSC (configuração de estado desejado), incluindo a pasta de raiz de script do PowerShell. Este caminho pode ser absoluto ou relativo a localização de script. Consulte [Introdução a extensão do Azure PowerShell DSC (configuração de estado desejado)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx), se aplicável, para obter mais informações.|

1.  Verifique se artefatos precisam ser carregado Azure. Caso contrário, pule para a etapa 11. Caso contrário, execute as seguintes etapas.

1.  Converta todas as variáveis com caminhos relativos para caminhos absolutos. Por exemplo, alterar um caminho como `..\Tools\AzCopy.exe` para `C:\YourFolder\Tools\AzCopy.exe`. Além disso, inicialize as variáveis *ArtifactsLocationName* e *ArtifactsLocationSasTokenName* como nulo. *ArtifactsLocation* e *SaSToken* podem ser parâmetros para o modelo. Se seus valores nulos após leitura no arquivo de parâmetros, o script gera valores para eles.

    As ferramentas do Azure usam os valores de parâmetro *_artifactsLocation* e *_artifactsLocationSasToken* no modelo para gerenciar artefatos. Se o script do PowerShell localiza parâmetros com esses nomes, mas os valores de parâmetro não são fornecidos, o script carrega os artefatos e retorna valores adequados para esses parâmetros. Ela passa-los para o cmdlet via `@OptionsParameters`.

  	|Variável|Descrição|
  	|---|---|
  	|ArtifactsLocationName|O caminho para onde estão localizados os artefatos Azure.|
  	|ArtifactsLocationSasTokenName|O nome do token SAS (assinatura de acesso compartilhado) que é usado pelo script para autenticar barramento de serviço. Consulte [Autenticação de assinatura de acesso compartilhado com barramento de serviço](service-bus-shared-access-signature-authentication.md) para obter mais informações.|

    ```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.  Esta seção verifica se o <app name>. arquivo de parameters.json (referido como o "arquivo de parâmetros") tem um nó pai **parâmetros** nomeado (no `else` bloco). Caso contrário, ela tem nenhum nó pai. O formato é aceitável.
    
    ```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.  Percorrer a coleção de parâmetros JSON. Se um valor de parâmetro foi atribuído ao *_artifactsLocation* ou *_artifactsLocationSasToken*, em seguida, defina a variável *$OptionalParameters* com esses valores. Isso impede que o script substitua inadvertidamente quaisquer valores de parâmetro que você fornecer.

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.  Obter a chave da conta de armazenamento e o contexto para o recurso de conta de armazenamento usado para manter os artefatos para implantação.

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.  Se você estiver usando o PowerShell DSC para configurar uma máquina virtual, a extensão de DSC requer os artefatos estar em um arquivo zip único. Portanto, crie um arquivo. zip para a configuração de DSC. Para fazer isso, verifique se $DSCSourceFolder existe. Se existe uma configuração de DSC, removê-lo e, em seguida, criar um novo arquivo compactado chamado dsc.zip.

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.  Se nenhum caminho para artefatos Azure é fornecido no arquivo de parâmetros, defina um caminho para o script do PowerShell usar quando Carregando artefatos. Para fazer isso, crie um caminho usando uma combinação de caminho de ponto de extremidade da conta de armazenamento além do nome de contêiner de armazenamento. Em seguida, atualize o arquivo de parâmetros com este caminho de novo.

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.  Use o utilitário de **AzCopy** (incluído na pasta **Ferramentas** do seu projeto de implantação do grupo de recursos do Azure) para copiar todos os arquivos de seu caminho de soltar armazenamento local para sua conta de armazenamento do Azure online. Se esta etapa falhar, sair do script, desde que a implantação não é chances de sucesso sem os artefatos necessários.

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.  Se um token SAS para o local de artefatos não é fornecido no arquivo de parâmetros, crie um para fornecer acesso temporário de somente leitura para o contêiner de armazenamento online. Em seguida, passe esse token SAS-lo a cmdline como "optionalParameter." Observe que todos os parâmetros passados na cmdline terá precedência sobre valores fornecidos no arquivo de parâmetros.

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  Crie o grupo de recursos se ele ainda não existir e verifique o arquivo de modelo e parâmetros para os erros de validação que impedirá a implantação de sucesso.

    ```
    # Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

    Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. Por fim, implante o modelo. Esse código cria um nome exclusivo para a implantação usando um carimbo de hora.

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## <a name="deploy-the-resource-group"></a>Implantar o grupo de recursos

### <a name="to-deploy-the-resource-group-in-visual-studio"></a>Para implantar o grupo de recursos no Visual Studio

1. No menu de atalho do projeto grupo de recursos do Azure, escolha **implantar** > **Nova implantação**.

    ![][0]

1. Na caixa de diálogo **implantar ao grupo de recursos** , em Escolha um grupo existente do recurso na caixa de listagem suspensa para implantar ou escolha ** &lt;criar novo... &gt;** para criar um novo grupo de recursos.

    ![][1]

1. Se solicitado, insira um nome de grupo de recursos e um local na caixa de diálogo **Criar grupo de recursos** e, em seguida, escolha o botão **criar** .

    ![][2]

1. Escolha o botão **Editar parâmetros** para exibir a caixa de diálogo **Editar parâmetros** e insira os valores de parâmetro ausente.

    ![][3]

    >[AZURE.NOTE] Se precisam de todos os parâmetros obrigatórios valores, esta caixa de diálogo aparecerá automaticamente quando você implanta.

    ![][4]

1. Quando você terminar de inserir valores de parâmetro, escolha o botão **Salvar** e, em seguida, escolha o botão de **implantar** .

    O script de implantação (implantar-AzureResourceGroup.ps1) é executado e seu modelo, junto com qualquer artefatos, implanta Azure.

### <a name="to-deploy-the-resource-group-by-using-powershell"></a>Para implantar o grupo de recursos usando o PowerShell

Se você quiser executar o script sem usar o comando implantar o Visual Studio e a interface do usuário, no menu de atalho para o script, escolha **Abrir com o PowerShell ISE**.

![][5]


## <a name="command-deployment-examples"></a>Exemplos de implantação de comando

### <a name="deploy-using-default-values"></a>Implantar usando valores padrão

Este exemplo mostra como executar o script usando os valores de parâmetro padrão. (Porque o parâmetro de local não tem um valor padrão, você precisa fornecer um.)

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### <a name="deploy-overriding-the-default-values"></a>Implantar substituindo os valores padrão

Este exemplo mostra como executar o script para implantar arquivos de modelo e parâmetros diferentes dos valores padrão.

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### <a name="deploy-using-uploadartifacts-for-staging"></a>Implantar usando UploadArtifacts para preparação

Este exemplo mostra como executar o script para carregar artefatos da pasta lançamento e implantar modelos de não padrão.

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

Este exemplo mostra como executar o script em uma tarefa do Azure PowerShell no Visual Studio Online.

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o Gerenciador de recursos do Azure lendo [Visão geral do Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md).

Para obter mais exemplos de como trabalhar com projetos de grupo de recursos do Azure, consulte [implantar e gerenciar recursos Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Deploy-and-manage-Azure-resources) de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 conectar [demonstração](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Para mais guias de início rápido da demonstração de HealthClinic.biz, consulte [Início rápido de ferramentas de desenvolvedor do Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png
