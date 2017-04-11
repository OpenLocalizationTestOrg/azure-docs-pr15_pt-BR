<properties 
    pageTitle="Bloquear recursos com o Gerenciador de recursos | Microsoft Azure" 
    description="Impedir usuários de atualizar ou excluir determinados recursos aplicando uma restrição para todos os usuários e funções." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="tomfitz"/>

# <a name="lock-resources-with-azure-resource-manager"></a>Recursos de bloqueio com o Gerenciador de recursos do Azure

Como administrador, você talvez precise bloquear uma assinatura, grupo de recursos ou recurso para impedir que outros usuários na sua organização acidentalmente excluam ou modifiquem recursos críticos. Você pode definir o nível de bloqueio **CanNotDelete** ou **somente leitura**. 

- **CanNotDelete** significa usuários autorizados podem ler e modificar um recurso ainda, mas eles não é possível excluí-la. 
- **Somente leitura** significa que os usuários autorizados podem ler de um recurso, mas eles não é possível excluí-la ou executar quaisquer ações nela. A permissão no recurso é restrita à função **leitor** . 

Aplicar **somente leitura** pode levar a resultados inesperados porque algumas operações que parecem ler operações realmente requerem ações adicionais. Por exemplo, colocando um bloqueio de **somente leitura** em uma conta de armazenamento impede que todos os usuários listando as teclas. Lista de operação de teclas é tratada por meio de uma solicitação POST porque as teclas retornadas estão disponíveis para operações de gravação. Outro exemplo, colocar um bloqueio de **somente leitura** em um recurso de serviço de aplicativo impede Visual Studio Server Explorer exibindo os arquivos para o recurso porque interação requer acesso de gravação.

Ao contrário de controle de acesso baseado em função, você pode usar bloqueios de gerenciamento para aplicar uma restrição entre todos os usuários e funções. Para saber mais sobre como configurar permissões para usuários e funções, consulte [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md).

Quando você aplica um bloqueio em um escopo pai, todos os recursos de filho herdam o mesmo bloqueio. Recursos pares adicionadas posteriormente herdam o bloqueio do pai. O bloqueio mais restritivo na herança tem precedência.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Quem pode criar ou excluir bloqueios em sua organização

Para criar ou excluir bloqueios de gerenciamento, você deve ter acesso ao **Microsoft.Authorization/\* ** ou **Microsoft.Authorization/locks/\* ** ações. Das funções internas, somente o **proprietário** e o **Administrador de acesso de usuário** são concedidos essas ações.

## <a name="creating-a-lock-through-the-portal"></a>Criando um bloqueio por meio do portal

[AZURE.INCLUDE [resource-manager-lock-resources](../includes/resource-manager-lock-resources.md)]

## <a name="creating-a-lock-in-a-template"></a>Criando um bloqueio em um modelo

O exemplo a seguir mostra um modelo que cria um bloqueio em uma conta de armazenamento. A conta de armazenamento na qual deseja aplicar o bloqueio é fornecida como um parâmetro. O nome do bloqueio é criado concatenando o nome de recurso com **/Microsoft.Authorization/** e o nome do bloqueio, neste caso **myLock**.

O tipo fornecido é específico para o tipo de recurso. Para armazenamento, defina o tipo para "Microsoft.Storage/storageaccounts/providers/locks".

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="creating-a-lock-with-rest-api"></a>Criando um bloqueio com a API REST

Você pode bloquear recursos implantados com a [API REST para bloqueios de gerenciamento](https://msdn.microsoft.com/library/azure/mt204563.aspx). A API REST permite criar e excluir bloqueios e recuperar informações sobre bloqueios existentes.

Para criar um bloqueio, execute:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

O escopo pode ser uma assinatura, grupo de recursos ou recurso. O bloqueio-nome é tudo o que você deseja chamar o bloqueio. Para a versão da api, use **2015-01-01**.

Na solicitação, inclua um objeto JSON que especifica as propriedades do bloqueio.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

Para obter exemplos, consulte [API REST para bloqueios de gerenciamento](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## <a name="creating-a-lock-with-azure-powershell"></a>Criando um bloqueio com PowerShell do Azure

Você pode bloquear recursos implantados com PowerShell do Azure usando o **Novo AzureRmResourceLock** conforme mostrado no exemplo a seguir.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup

Azure PowerShell fornece outros comandos para trabalhar bloqueios, como **Set-AzureRmResourceLock** para atualizar um bloqueio e **AzureRmResourceLock remover** para excluir um bloqueio.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre como trabalhar com bloqueios de recursos, consulte [Para baixo o Azure recursos de bloqueio](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- Para saber sobre logicamente organizar seus recursos, consulte [usando marcas para organizar seus recursos](resource-group-using-tags.md)
- Para alterar qual grupo de recursos um recurso reside, consulte [Mover recursos para o novo grupo de recursos](resource-group-move-resources.md)
- Você pode aplicar restrições e convenções em sua assinatura com políticas personalizadas. Para obter mais informações, consulte a [Política de uso para gerenciar recursos e controlar o acesso](resource-manager-policy.md).
