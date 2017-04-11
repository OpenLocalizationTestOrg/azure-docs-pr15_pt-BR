<properties 
    pageTitle="Mover recursos para o novo grupo de recursos | Microsoft Azure" 
    description="Use o Gerenciador de recursos do Azure para mover recursos para um novo grupo de recursos ou assinatura." 
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
    ms.date="10/21/2016" 
    ms.author="tomfitz"/>

# <a name="move-resources-to-new-resource-group-or-subscription"></a>Mover recursos para o novo grupo de recursos ou assinatura

Este tópico mostra como mover recursos para uma nova assinatura ou um novo grupo de recursos na mesma assinatura. Você pode usar o portal, PowerShell, Azure CLI ou a API REST para mover recurso. As operações de mover neste tópico estão disponíveis para você sem qualquer assistência do Azure suporte.

Normalmente, você mover recursos quando você decide que:

- Para fins de faturamento, um recurso precisa live em uma assinatura diferente.
- Um recurso não está mais compartilha o ciclo de vida mesmo como os recursos que ela foi agrupada com anteriormente. Você deseja movê-la para um novo grupo de recursos para que você possa gerenciar esse recurso separadamente dos outros recursos.

Ao mover recursos, grupo de origem e o grupo de destino são bloqueados durante a operação. Gravar e excluir operações são bloqueados nos grupos de até que a mudança é concluída.

Você não pode alterar o local do recurso. Mover um recurso apenas move-a para um novo grupo de recursos. O novo grupo de recursos pode ter um local diferente, mas que não altera o local do recurso.

> [AZURE.NOTE] Este artigo descreve como mover recursos dentro de um existente Azure conta ofertas. Se realmente deseja alterar sua conta do Azure oferecendo (como a atualização do pré-pago previamente pagar) enquanto continua a trabalhar com recursos existentes, consulte [mudar sua assinatura do Azure para outra oferta](billing-how-to-switch-azure-offer.md). 

## <a name="checklist-before-moving-resources"></a>Lista de verificação antes de mover recursos

Há algumas etapas importantes para executar antes de passar um recurso. Verificando essas condições, você pode evitar erros.

1. O serviço deve habilitar a capacidade de mover os recursos. Consulte a lista abaixo para obter informações sobre quais [Serviços habilitam recursos movendo](#services-that-enable-move).
1. As assinaturas de origem e destino devem existir dentro do mesmo [locatário do Active Directory](./active-directory/active-directory-howto-tenant.md). Para mover para um novo locatário, contate o suporte.
2. A assinatura de destino deve ser registrada para o provedor de recursos do recurso sendo movido. Caso contrário, você recebe um erro informando que a **assinatura não está registrada para um tipo de recurso**. Você pode encontrar esse problema ao mover um recurso para uma nova assinatura, mas que assinatura nunca tenha sido usada com esse tipo de recurso. Para saber como verificar o status de registro e registrar provedores de recursos, consulte [tipos e provedores de recursos](../resource-manager-supported-services.md#resource-providers-and-types).
4. Se você estiver movendo o aplicativo de serviço de aplicativo, revisar [limitações do serviço de aplicativo](#app-service-limitations).
4. Se você estiver movendo recursos associados aos serviços de recuperação, revisar as [limitações de serviços de recuperação](#recovery-services-limitations)
5. Se você estiver movendo recursos implantados pelo modelo clássico, revisar [limitações de implantação de clássico](#classic-deployment-limitations).

## <a name="when-to-call-support"></a>Quando ligar para o suporte

Você pode mover a maioria dos recursos por meio de operações de autoatendimento mostradas neste tópico. Use as operações de autoatendimento para:

- Mova os recursos do Gerenciador de recursos.
- Mova recursos clássicos de acordo com as [limitações de implantação clássico](#classic-deployment-limitations). 

Ligar para o suporte quando você precisa:

- Mova seus recursos para uma nova conta do Microsoft Azure (e locatário do Active Directory).
- Mover os recursos clássicos, mas está tendo problemas com as limitações.

## <a name="services-that-enable-move"></a>Serviços que permitem mover

Por agora, os serviços que permitem a movimentação para um novo grupo de recursos e de assinatura são:

- Gerenciamento de API
- Aplicativos de serviço de aplicativo (web apps) - consulte [limitações do serviço de aplicativo](#app-service-limitations)
- Automação
- Em lotes
- CDN
- Serviços de nuvem - consulte [clássico limitações de implantação](#classic-deployment-limitations)
- Fábrica de dados
- DNS
- DocumentDB
- HDInsight clusters
- IoT Hubs
- Chave cofre 
- Serviços de mídia
- Contrato móvel
- Hubs de notificação
- Obtenção de informações operacionais
- Relacionada Cache
- Agendador
- Pesquisa
- Barramento de serviço
- Armazenamento
- Armazenamento (clássico) - consulte [limitações de implantação de clássico](#classic-deployment-limitations)
- Servidor de banco de dados do SQL - o banco de dados e o servidor deve residem no mesmo grupo de recursos. Quando você move um SQL server, todos seus bancos de dados também são movidos.
- Máquinas virtuais - no entanto, faz não oferece suporte para mover para uma nova assinatura quando seus certificados são armazenados em um cofre de chave
- Máquinas virtuais (clássico) - consulte [limitações de implantação de clássico](#classic-deployment-limitations)
- Redes virtuais

## <a name="services-that-do-not-enable-move"></a>Serviços que permitem não mover

Os serviços que atualmente não habilitam movendo um recurso são:

- Gateway de aplicativo
- Obtenção de informações de aplicativo
- Rota expressa
- Serviços de recuperação cofre - também não mover os recursos de computação, rede e armazenamento associados ao Cofre de serviços de recuperação, consulte [limitações de serviços de recuperação](#recovery-services-limitations).
- Máquinas virtuais com certificado armazenado em Cofre de chave
- Conjuntos de escala de máquinas virtuais
- Redes virtuais (clássico) - consulte [limitações de implantação de clássico](#classic-deployment-limitations)
- Gateway VPN

## <a name="app-service-limitations"></a>Limitações do serviço de aplicativo

Ao trabalhar com aplicativos de serviço de aplicativo, é possível mover apenas um plano de serviço de aplicativo. Para mover os aplicativos de serviço de aplicativo, as opções são:

- Mova o plano de serviço de aplicativo e todos os outros recursos de serviço de aplicativo desse grupo de recursos para um novo grupo de recursos que ainda não tiver recursos de serviço de aplicativo. Esse requisito significa que você deve mover até mesmo os recursos de serviço de aplicativo que não são associados com o plano de serviço de aplicativo. 
- Mova os aplicativos para um grupo de recursos diferentes, mas manter todos os planos de serviço de aplicativo no grupo de recursos original.

Se o seu grupo de recursos original também inclui um recurso de obtenção de informações do aplicativo, é possível mover esse recurso porque a obtenção de informações de aplicativo não habilita a operação de movimentação atualmente. Se você incluir o recurso de obtenção de informações de aplicativo ao mover aplicativos de serviço de aplicativo, a operação de mover inteiro falhará. No entanto, a obtenção de informações de aplicativo e o plano de serviço de aplicativo não precisa residem no mesmo grupo de recursos como o aplicativo para o aplicativo funcione corretamente.

Por exemplo, se seu grupo de recursos contém:

- **Web-a** que está associado ao **plano-a** e **aplicativo-ideias-a**
- **web-b** que está associado ao **plano-b** e **aplicativo-ideias-b**

As opções são:

- Mover **web-a**, **plano-a**, **web-b**e **b de plano**
- Mover **web-a** e **b de web**
- Mover **web-a**
- Mover **web-b**

Todas as outras combinações envolvem movendo um tipo de recurso que não é possível mover (aplicativo ideias) ou deixando para trás de um tipo de recurso que não pode ser deixado para trás ao mover um plano de serviço de aplicativo (qualquer tipo de recurso de serviço de aplicativo).

Se seu aplicativo web reside em um grupo de recursos diferentes de seu plano de serviço de aplicativo, mas que você deseja mover para um novo grupo de recursos, você deve realizar o movimento em duas etapas. Por exemplo:

- **a Web** reside no **grupo de web**
- **plano-a** reside no **grupo de plano**
- Você deseja **web-a** e **plano-a** para residem no **grupo combinados**

Para realizar essa mudança, execute duas operações separadas mover na sequência a seguir:

1. Mover a **web-** ao **grupo de plano**
2. Mover **web-a** e **plano-** ao **grupo combinados**.

Você pode mover um certificado de serviço de aplicativo para um novo grupo de recursos ou assinatura sem problemas. No entanto, se o seu aplicativo web inclui um certificado SSL que você comprou externamente e carregados para o aplicativo, você deve excluir o certificado antes de passar o web app. Por exemplo, você pode executar as seguintes etapas:

1. Excluir o certificado carregado do aplicativo web
2. Mover o aplicativo web
3. Carregar o certificado para o aplicativo web

## <a name="recovery-services-limitations"></a>Limitações de serviços de recuperação

Mover-se de que não está habilitado para armazenamento, rede, ou calcular recursos usados para configurar a recuperação de dados com recuperação de Site do Azure. 

Por exemplo, suponha que você configurou a replicação de suas máquinas locais para uma conta de armazenamento (Storage1) e quer que o computador protegido surgirem após failover no Azure como máquina virtual (VM1) anexada a uma rede virtual (Network1). Você não pode mover qualquer um desses recursos Azure Storage1, VM1 e Network1 - em grupos de recursos dentro da mesma assinatura ou em assinaturas.

## <a name="classic-deployment-limitations"></a>Limitações de implantação clássico

As opções para se deslocar recursos implantados por meio do modelo clássico diferem com base em se você estiver movendo os recursos dentro de uma assinatura ou a uma nova assinatura. 

### <a name="same-subscription"></a>Mesma assinatura

Ao mover os recursos de um grupo de recursos para outro grupo de recursos dentro da mesma assinatura, as seguintes restrições se aplicam:

- Redes virtuais (clássicas) não podem ser movidas.
- Máquinas virtuais (clássico) deve ser movidas com o serviço de nuvem. 
- Serviço de nuvem só pode ser movido quando a mudança inclui todas as suas máquinas virtuais.
- Serviço de nuvem apenas uma pode ser movido por vez.
- Apenas uma conta de armazenamento (clássica) pode ser movida por vez.
- Conta de armazenamento (clássica) não pode ser movida na mesma operação com uma máquina virtual ou um serviço na nuvem.

Para mover recursos clássicos para um novo grupo de recursos dentro da mesma assinatura, use as operações de movimentação padrão por meio do [portal](#use-portal), [Azure PowerShell](#use-powershell), [Azure CLI](#use-azure-cli)ou [API REST](#use-rest-api). Use as mesmas operações como usada para mover recursos do Gerenciador de recursos.

### <a name="new-subscription"></a>Nova assinatura

Ao mover recursos a uma nova assinatura, as seguintes restrições se aplicam:

- Todos os recursos clássicos na assinatura devem ser movidos na mesma operação.
- A assinatura de destino não deve conter quaisquer outros recursos clássicos.
- A movimentação só pode ser solicitada por meio de uma API REST separada para move clássico. Os comandos de movimentação padrão do Gerenciador de recursos não funcionam ao mover recursos clássicos para uma nova assinatura.

Para mover recursos clássicos para uma nova assinatura, você deve usar operações restante que são específicas para recursos clássicos. Execute as seguintes etapas para mover recursos clássicos para uma nova assinatura.

1. Verifique se a assinatura de fonte pode participar de uma transição entre-assinatura. Use a operação a seguir:

         POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    
     No corpo da solicitação, incluem:

         {
           "role": "source"
         }

     A resposta para a operação de validação é no seguinte formato:

         {
           "status": "{status}",
           "reasons": [
             "reason1",
             "reason2"
           ]
         }

2. Verifique se a assinatura de destino pode participar de uma transição entre-assinatura. Use a operação a seguir:

         POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01

     No corpo da solicitação, incluem:

         {
           "role": "target"
         }

     A resposta está no mesmo formato a validação de assinatura de origem.

3. Se ambas as assinaturas passam validação, mova todos os recursos clássicos de uma assinatura para outra assinatura com a operação a seguir:

         POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01

    No corpo da solicitação, incluem:

         {
           "target": "/subscriptions/{target-subscription-id}"
         }

A operação pode ser executada por vários minutos. 

## <a name="use-portal"></a>Usar o portal

Para mover os recursos para um novo grupo de recursos na **mesma assinatura**, selecione o grupo de recursos que contêm esses recursos e selecione o botão **Mover** .

![Mover recursos](./media/resource-group-move-resources/edit-rg-icon.png)

Ou, para mover os recursos para uma **nova assinatura**, selecione o grupo de recursos que contêm esses recursos e, em seguida, selecione o ícone de assinatura de editar.

![Mover recursos](./media/resource-group-move-resources/change-subscription.png)

Selecione os recursos para mover e o grupo de recursos de destino. Confirme que você precisa atualizar scripts para esses recursos e selecione **Okey**. Se você selecionou o ícone de assinatura Editar na etapa anterior, você deve selecionar também a assinatura de destino.

![Selecionar destino](./media/resource-group-move-resources/select-destination.png)

**Notificações**, você verá que a operação de movimentação está em execução.

![Mostrar status de mover](./media/resource-group-move-resources/show-status.png)

Quando tiver concluído, você será notificado do resultado.

![Mostrar resultado de mover](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Usar o PowerShell

Para mover os recursos existentes para outra assinatura ou grupo de recursos, use o comando **Mover AzureRmResource** .

O primeiro exemplo mostra como mover um recurso para um novo grupo de recursos.

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

O segundo exemplo mostra como mover vários recursos para um novo grupo de recursos.

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

Para mover para uma nova assinatura, inclua um valor para o parâmetro **DestinationSubscriptionId** .

Você será solicitado a confirmar que você deseja mover os recursos especificados.

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## <a name="use-azure-cli"></a>Usar a CLI do Azure

Para mover os recursos existentes para outra assinatura ou grupo de recursos, use o comando **Mover recurso azure** . Você precisa fornecer as identificações dos recursos dos recursos para mover. Você pode obter identificações de recurso com o seguinte comando:

    azure resource list -g sourceGroup --json

Que retorna o seguinte formato:

    [
      {
        "id": "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo",
        "name": "storagedemo",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "southcentralus",
        "tags": {},
        "kind": "Storage",
        "sku": {
          "name": "Standard_RAGRS",
          "tier": "Standard"
        }
      }
    ]

O exemplo a seguir mostra como mover uma conta de armazenamento para um novo grupo de recursos. No parâmetro **-i** , fornece uma lista separada por vírgulas da identificação do recurso mover.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo" -d "destinationGroup"
    
Você será solicitado a confirmar que você deseja mover o recurso especificado.

## <a name="use-rest-api"></a>Usar a API REST

Para mover os recursos existentes para outra assinatura ou grupo de recursos, execute:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

No corpo da solicitação, você pode especificar o grupo de recursos de destino e os recursos para mover. Para saber mais sobre a operação de restante de movimentação, consulte [Mover recursos](https://msdn.microsoft.com/library/azure/mt218710.aspx).


## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre cmdlets do PowerShell para gerenciar sua assinatura, consulte [Usando o PowerShell Azure com o Gerenciador de recursos](powershell-azure-resource-manager.md).
- Para saber mais sobre comandos do Azure para gerenciar sua assinatura, consulte [usando a CLI do Azure com o Gerenciador de recursos](xplat-cli-azure-resource-manager.md).
- Para saber sobre os recursos do portal de gerenciamento de sua assinatura, consulte [usando o portal do Azure para gerenciar recursos](./azure-portal/resource-group-portal.md).
- Para saber sobre a aplicação de uma organização lógica aos seus recursos, consulte [usando marcas para organizar seus recursos](resource-group-using-tags.md).
