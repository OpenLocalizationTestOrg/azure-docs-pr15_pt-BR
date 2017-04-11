<properties
   pageTitle="Excluir um cluster Azure e seus recursos | Microsoft Azure"
   description="Saiba como excluir completamente uma estrutura de serviço cluster qualquer grupo de recursos que contém o cluster excluindo ou excluindo seletivamente recursos."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>

# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Excluir um cluster de serviço tecidos no Azure e os recursos que ele usa

Um cluster de estrutura de serviço é composto por muitos outros recursos Azure além do recurso de cluster em si. Portanto, para excluir completamente um cluster de estrutura de serviço que você também precise excluir todos os recursos que é formado.
Você tem duas opções: seja excluir o grupo de recursos que o cluster está em (que exclui o recurso de cluster e quaisquer outros recursos no grupo de recursos) ou excluir especificamente o recurso de cluster e associado recursos (mas não outros recursos no grupo de recursos).

>[AZURE.NOTE] Excluindo o recurso de cluster **não** excluir todos os outros recursos que é composto por seu cluster de estrutura de serviço.

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Excluir o grupo de recurso inteiro (RG) que o cluster de estrutura de serviço está em

Essa é a maneira mais fácil para garantir que você exclua todos os recursos associados a seu cluster, incluindo o grupo de recursos. Você pode excluir o grupo de recursos usando o PowerShell ou por meio do portal do Azure. Se seu grupo de recursos tem recursos que não estão relacionados ao cluster de estrutura de serviço, você pode excluir recursos específicos.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Excluir o grupo de recursos usando o PowerShell do Azure

Você também pode excluir o grupo de recursos executando os seguintes cmdlets do PowerShell do Azure. Certifique-se de que o Azure PowerShell 1.0 ou posterior está instalado no seu computador. Se você não tiver feito isso antes, siga as etapas descritas [como instalar e configurar o Azure PowerShell.](../powershell-install-configure.md)

Abra uma janela do PowerShell e execute os seguintes cmdlets PS:

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Você receberá um aviso para confirmar a exclusão se você não usou o *-força* opção. Na confirmação a RG e todos os recursos que ele contém são excluídos.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Excluir um grupo de recursos no portal do Azure  

1. Faça logon no [portal do Azure](https://portal.azure.com).
2. Navegue até o cluster de estrutura de serviço que você deseja excluir.
3. Clique no nome do grupo de recursos na página essentials cluster.
4. Isto traz a página de **Princípios básicos de grupo de recursos** .
5. Clique em **Excluir**.
6. Siga as instruções na página para concluir a exclusão do grupo de recursos.

![Excluir grupo de recursos][ResourceGroupDelete]


## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Excluir o recurso de cluster e os recursos que ele usa, mas não outros recursos no grupo de recursos

Se seu grupo de recursos tem apenas os recursos que estão relacionados ao cluster tecidos do serviço que você deseja excluir, é fácil excluir o grupo de recurso inteiro. Se você quiser excluir seletivamente os recursos um por um no seu grupo de recursos, em seguida, siga estas etapas.

Se você implantou o seu cluster no portal ou usando um dos modelos do Gerenciador de recursos do serviço tecidos da Galeria de modelos, todos os recursos que o cluster usa são marcados com as seguintes duas marcas. Você pode usá-los para decidir quais recursos que você deseja excluir.

***Marca #1:*** Chave = clusterName, valor = 'nome do cluster'

***Marca #2:*** Chave = resourceName, valor = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Excluir recursos específicos no portal do Azure

1. Faça logon no [portal do Azure](https://portal.azure.com).
2. Navegue até o cluster de estrutura de serviço que você deseja excluir.
3. Vá para **todas as configurações** na lâmina essentials.
4. Clique em **marcas** em **Gerenciamento de recursos** na lâmina configurações.
5. Clique em uma das **marcas** na lâmina marcas para obter uma lista de todos os recursos com essa marca.

    ![Marcas de recurso][ResourceTags]

6. Uma vez que a lista de recursos marcadas, clique em cada um dos recursos e excluí-las.

    ![Recursos marcados][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Excluir os recursos usando o PowerShell do Azure

Você pode excluir os recursos um por um executando os seguintes cmdlets do PowerShell do Azure. Certifique-se de que o Azure PowerShell 1.0 ou posterior está instalado no seu computador. Se você não tiver feito isso antes, siga as etapas descritas [como instalar e configurar o Azure PowerShell.](../powershell-install-configure.md)

Abra uma janela do PowerShell e execute os seguintes cmdlets PS:

```powershell
Login-AzureRmAccount
```
Para cada um dos recursos que você deseja excluir, execute o seguinte:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Para excluir o recurso de cluster, execute o seguinte:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Próximas etapas
Leia o seguinte também saber sobre atualizando um cluster e partição serviços:

- [Saiba mais sobre atualizações de cluster](service-fabric-cluster-upgrade.md)
- [Saiba mais sobre partição estados serviços para escala máxima](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG
