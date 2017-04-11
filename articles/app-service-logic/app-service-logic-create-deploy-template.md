<properties
   pageTitle="Criar um modelo de implantação do aplicativo de lógica | Microsoft Azure"
   description="Saiba como criar um modelo de implantação do aplicativo de lógica e usá-lo para o gerenciamento de lançamento"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="create-a-logic-app-deployment-template"></a>Criar um modelo de implantação do aplicativo de lógica

Depois de um aplicativo de lógica tiver sido criado, talvez você queira criá-lo como um modelo do Gerenciador de recursos do Azure. Dessa forma, você pode implantar facilmente o aplicativo de lógica para qualquer ambiente ou grupo de recursos onde você pode precisar. Para obter uma introdução aos modelos do Gerenciador de recursos, certifique-se de conferir os artigos sobre [a criação de modelos do Gerenciador de recursos do Azure](../resource-group-authoring-templates.md) e [implantar recursos usando modelos do Gerenciador de recursos do Azure](../resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Modelo de implantação do aplicativo de lógica

Um aplicativo de lógica tem três componentes básicos:

* **Recurso de aplicativo de lógica**. Este recurso contém informações sobre coisas como preços plano, local e a definição de fluxo de trabalho.
* **Definição de fluxo de trabalho**. Este é o que é mostrado no modo de exibição de código. Ele inclui a definição das etapas do fluxo e como o mecanismo deve executar. Este é o `definition` propriedade do recurso aplicativo lógica.
* **Conexões**. Estes são recursos separados que armazenam com segurança metadados sobre qualquer conexões de conector, como uma cadeia de conexão e um token de acesso. Você se referir a eles em um aplicativo de lógica no `parameters` seção do recurso aplicativo lógica.

Você pode exibir todas essas partes para os aplicativos de lógica existentes usando uma ferramenta como o [Gerenciador de recursos do Azure](http://resources.azure.com).

Para criar um modelo para um aplicativo de lógica usar com implantações de grupo de recursos, você precisa definir os recursos e parametrizar conforme necessário. Por exemplo, se você estiver implantando em um ambiente de desenvolvimento, teste e produção, você provavelmente desejará utilizar cadeias de caracteres de conexão diferentes para um banco de dados do SQL em cada ambiente. Ou, talvez você queira implantar em diferentes assinaturas ou grupos de recursos.  

## <a name="create-a-logic-app-deployment-template"></a>Criar um modelo de implantação do aplicativo de lógica

A maneira mais fácil ter um modelo de implantação do aplicativo de lógica válido é usar o [Visual Studio Tools para aplicativos de lógica](./app-service-logic-deploy-from-vs.md).  Ferramentas do Visual Studio geram um modelo de implantação válido que pode ser usado em qualquer assinatura ou local.

Outras ferramentas podem ajudá-lo a criar um modelo de implantação do aplicativo de lógica. Você pode criar manualmente, ou seja, usando os recursos que já discutidos aqui para criar parâmetros conforme necessário. Outra opção é usar um módulo do PowerShell do [criador de modelo de aplicativo de lógica](https://github.com/jeffhollan/LogicAppTemplateCreator) . Este módulo de código-fonte aberto primeiro avalia o aplicativo de lógica e todas as conexões que está usando e, em seguida, gera recursos de modelo com os parâmetros necessários para implantação. Por exemplo, se você tiver um aplicativo de lógica que recebe uma mensagem de uma fila de barramento de serviço do Azure e adiciona dados a um banco de dados do SQL Azure, a ferramenta irá preservar toda a lógica de coordenação e parametrizar as cadeias de caracteres de conexão do SQL e barramento de serviço para que eles podem ser definidos na implantação.

>[AZURE.NOTE] Conexões devem estar dentro do mesmo grupo de recursos como o aplicativo de lógica.

### <a name="install-the-logic-app-template-powershell-module"></a>Instalar o módulo do PowerShell de modelo de aplicativo de lógica

A maneira mais fácil para instalar o módulo é por meio da [Galeria do PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), usando o comando `Install-Module -Name LogicAppTemplate`.  

Você também pode instalar o módulo do PowerShell manualmente:

1. Baixe a versão mais recente do [criador de modelo de aplicativo de lógica](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
1. Extrair a pasta na sua pasta de módulo do PowerShell (geralmente `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Para o módulo trabalhar com qualquer acesso de assinatura e locatário token, é recomendável que você usá-lo com a ferramenta de linha de comando [ARMClient](https://github.com/projectkudu/ARMClient) .  Esta [postagem de blog](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) discute ARMClient mais detalhadamente.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Gerar um modelo de aplicativo de lógica usando o PowerShell

Após a instalação do PowerShell, você pode gerar um modelo, usando o seguinte comando:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId`é a ID do Azure assinatura. Esta linha primeiro obtém um acesso token via ARMClient, e em seguida, pipes-la por meio do script PowerShell e cria o modelo em um arquivo JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Adicionar parâmetros a um modelo de aplicativo de lógica

Depois de criar seu modelo de aplicativo de lógica, você pode continuar a adicionar ou modificar os parâmetros que você pode precisar. Por exemplo, se sua definição inclui uma ID de recurso a uma função Azure ou aninhado fluxo de trabalho que você planeja implantar em uma única implantação, você pode adicionar mais recursos para o modelo e parametrizar IDs conforme necessário. O mesmo se aplica a todas as referências a personalizado APIs ou Swagger pontos de extremidade que você espera implantar com cada grupo de recursos.

## <a name="deploy-a-logic-app-template"></a>Implantar um modelo de aplicativo de lógica

Você pode implantar o seu modelo usando qualquer número de ferramentas, incluindo PowerShell, API REST, gerenciamento de lançamento do Visual Studio e a implantação de modelo de Portal do Azure. Consulte este artigo sobre a [implantação de recursos usando modelos do Gerenciador de recursos do Azure](../resource-group-template-deploy.md) para obter informações adicionais. Além disso, é recomendável que você crie um [arquivo de parâmetro](../resource-group-template-deploy.md#parameter-file) para armazenar valores para o parâmetro.

### <a name="authorize-oauth-connections"></a>Autorize OAuth conexões

Após a implantação, o aplicativo de lógica funciona ponta a ponta com parâmetros válidos. No entanto, OAuth conexões ainda precisa estar autorizado para gerar um token de acesso válido. Você pode fazer isso abrindo o aplicativo de lógica no designer e, em seguida, autorizar conexões. Ou, se você quiser automatizar, você pode usar um script para consentimento para cada conexão de OAuth. Há um script de exemplo no GitHub sob o projeto [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) .

## <a name="visual-studio-release-management"></a>Gerenciamento de lançamento do Visual Studio

Um cenário comum para implantar e gerenciar um ambiente é usar uma ferramenta como gerenciamento de lançamento do Visual Studio, com um modelo de implantação do aplicativo de lógica. Serviços de equipe do Visual Studio inclui uma tarefa de [Grupo de recursos do Azure implantar](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) que você pode adicionar qualquer construir ou pipeline de lançamento. Você precisa ter um [serviço principal](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) para autorização para implantar e, em seguida, você pode gerar a definição de lançamento.

1. Em gerenciamento de lançamento, para criar uma nova definição, selecione **vazia** para começar com uma definição vazia.

    ![Criar uma definição de nova e vazia][1]   

1. Escolha os recursos que necessários para isso. Isso provavelmente será o modelo de aplicativo de lógica gerado manualmente ou como parte do processo de compilação.
1. Adicione uma tarefa de **Implantação de grupo de recursos do Azure** .
1. Configurar com um [serviço principal](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)e os arquivos de modelo e parâmetros de modelo de referência.
1. Continue construir as etapas do processo de lançamento para qualquer outro ambiente, teste automatizado ou aprovadores conforme necessário.

<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG
