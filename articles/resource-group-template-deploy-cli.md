<properties
   pageTitle="Implantar recursos com CLI do Azure e modelo | Microsoft Azure"
   description="Use o Gerenciador de recursos do Azure e CLI do Azure para implantar um recursos no Azure. Os recursos são definidos em um modelo do Gerenciador de recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Implantar recursos com modelos do Gerenciador de recursos e CLI do Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Este tópico explica como usar CLI Azure com os modelos do Gerenciador de recursos para implantar seus recursos no Azure.  

> [AZURE.TIP] Para obter ajuda com um erro de depuração durante a implantação, consulte:
>
> - [Operações de implantação do modo de exibição com CLI do Azure](resource-manager-troubleshoot-deployments-cli.md) para saber mais sobre a obtenção de informações que ajuda você a solucionar o erro
> - [Solucionar erros comuns ao implantar recursos para Azure com o Gerenciador de recursos do Azure](resource-manager-common-deployment-errors.md) para saber como resolver erros comuns de implantação

Seu modelo pode ser um arquivo local ou um arquivo externo que está disponível por meio de um URI. Quando seu modelo reside em uma conta de armazenamento, você pode restringir o acesso ao modelo e fornecer um token de assinatura (SAS) acesso compartilhado durante a implantação.

## <a name="quick-steps-to-deployment"></a>Etapas rápidas para implantação

Este artigo descreve todas as diferentes opções disponíveis para você durante a implantação. No entanto, muitas vezes você só precisará de dois comandos simples. Para rapidamente começar com implantação, use os seguintes comandos:

    azure group create -n ExampleResourceGroup -l "West US"
    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

Para saber mais sobre as opções de implantação que pode ser mais adequado para seu cenário, continue lendo este artigo.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-azure-cli"></a>Implantar com CLI Azure

Se você não utilizou anteriormente Azure CLI com o Gerenciador de recursos, consulte [usando a CLI do Azure para Mac, Linux e Windows Azure no gerenciamento de recursos](xplat-cli-azure-resource-manager.md).

1. Faça logon em sua conta do Azure. Depois de fornecer suas credenciais, o comando retorna o resultado de seu login.

        azure login
  
        ...
        info:    login command OK

2. Se você tiver várias assinaturas, forneça a identificação de assinatura que você deseja usar para implantação.

        azure account set <YourSubscriptionNameOrId>

3. Alterne para o módulo do Gerenciador de recursos do Azure. Você recebe a confirmação de que o novo modo.

        azure config mode arm
   
        info:     New mode is arm

4. Se você não tiver um grupo de recursos existentes, crie um grupo de recursos. Fornece o nome do grupo de recursos e local que você precisa para sua solução. Você precisa fornecer um local para o grupo de recursos, porque o grupo de recursos armazena metadados sobre os recursos. Por razões de conformidade, talvez você queira especificar onde os metadados está armazenado. Em geral, recomendamos que você especifique um local onde a maioria dos seus recursos residirão. Usar o mesmo local pode simplificar o seu modelo.

        azure group create -n ExampleResourceGroup -l "West US"

     Um resumo do novo grupo de recursos é retornado.
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Valide sua implantação antes de executar-executando o comando de **modelo de grupo azure validar** . Ao testar a implantação, forneça parâmetros exatamente como você faria ao executar a implantação (mostrada na próxima etapa).

        azure group template validate -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup

5. Para implantar recursos em seu grupo de recursos, execute o seguinte comando e forneça os parâmetros necessários. Os parâmetros incluem um nome para sua implantação, o nome do seu grupo de recursos, o caminho ou a URL para o modelo e outros parâmetros necessários para seu cenário. 
   
     Você tem as seguintes três opções para fornecer valores de parâmetro: 

     1. Usar parâmetros de embutida e um modelo de local. Cada parâmetro está no formato: `"ParameterName": { "value": "ParameterValue" }`. O exemplo a seguir mostra os parâmetros com caracteres de escape.

            azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     2. Usar parâmetros de embutida e um link para um modelo.

            azure group deployment create --template-uri <LinkToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     3. Use um arquivo de parâmetro. Para obter informações sobre o arquivo de modelo, consulte o [arquivo de parâmetro](#parameter-file).
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Depois que os recursos foram implantados por meio de um dos três métodos acima, você verá um resumo da implantação.
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     Para executar uma implantação completa, defina o **modo** como **concluída**. Tenha cuidado ao usar esse modo como você pode excluir acidentalmente recursos que não estão em seu modelo.

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. Se você quiser registrar informações adicionais sobre a implantação que pode ajudar a solucionar problemas de erros de implantação, use o parâmetro de **configuração de depuração** . Você pode especificar que o conteúdo de solicitação, conteúdo de resposta ou ambos estar conectado com a operação de implantação.

        azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

## <a name="deploy-template-from-storage-with-sas-token"></a>Implantar o modelo de armazenamento com token SAS

Você pode adicionar seus modelos para uma conta de armazenamento e vinculá-los durante a implantação com um token SAS.

> [AZURE.IMPORTANT] Seguindo as etapas abaixo, o blob que contém o modelo é acessível para somente o proprietário da conta. No entanto, quando você cria um token SAS para o blob, o blob é acessível para qualquer pessoa com esse URI. Se outro usuário interceptar o URI, esse usuário é possível acessar o modelo. Usar um token SAS é uma boa maneira de limitar o acesso aos seus modelos, mas você não deve incluir dados confidenciais como senhas diretamente no modelo.

### <a name="add-private-template-to-storage-account"></a>Adicionar modelo particular à conta de armazenamento

As etapas a seguir configuram uma conta de armazenamento para os modelos:

1. Crie um grupo de recursos.

        azure group create -n "ManageGroup" -l "westus"

2. Crie uma conta de armazenamento. O nome da conta de armazenamento deve ser exclusivo entre Azure, portanto, forneça seu próprio nome da conta.

        azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates

3. Definir variáveis para a conta de armazenamento e chave.

        export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
        export AZURE_STORAGE_ACCESS_KEY={storage_account_key}

4. Crie um contêiner. A permissão é definida para **Desativar** o que significa que o contêiner somente é acessível para o proprietário.

        azure storage container create --container templates -p Off 
        
4. Adicione seu modelo ao contêiner.

        azure storage blob upload --container templates -f c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>Fornecer token SAS durante a implantação

Para implantar um modelo particular em uma conta de armazenamento, recuperar um token SAS e incluí-los no URI para o modelo.

1. Crie um token SAS com permissões de leitura e uma hora de expiração para limitar o acesso. Defina o tempo de expiração para permitir que o tempo suficiente para concluir a implantação. Recupere o URI completo do modelo incluindo o token SAS.

        expiretime=$(date -I'minutes' --date "+30 minutes")
        fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")

2. Implante o modelo, fornecendo o URI que inclui o token SAS.

        azure group deployment create --template-uri $fullurl -g ExampleResourceGroup

Para obter um exemplo do uso de um token SAS com modelos vinculados, consulte [usando modelos vinculados com o Gerenciador de recursos do Azure](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Próximas etapas
- Para um exemplo de implantação de recursos por meio da biblioteca de cliente .NET, consulte [implantar recursos usando bibliotecas .NET e um modelo](virtual-machines/virtual-machines-windows-csharp-template.md).
- Para definir parâmetros no modelo, consulte [modelos de criação de páginas](resource-group-authoring-templates.md#parameters).
- Para obter orientações sobre como implantar sua solução para diferentes ambientes, consulte [ambientes de desenvolvimento e teste no Microsoft Azure](solution-dev-test-environments.md).
- Para obter detalhes sobre como usar uma referência de KeyVault para passar valores seguros, consulte [passar valores seguros durante a implantação](resource-manager-keyvault-parameter.md).

