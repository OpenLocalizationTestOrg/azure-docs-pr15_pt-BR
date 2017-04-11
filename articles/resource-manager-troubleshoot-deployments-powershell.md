<properties
   pageTitle="Exibir operações de implantação com o PowerShell | Microsoft Azure"
   description="Descreve como usar o PowerShell do Azure para detectar problemas de implantação do Gerenciador de recursos."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/14/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-powershell"></a>Operações de implantação do modo de exibição com o PowerShell do Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Você pode exibir as operações para uma implantação através do PowerShell do Azure. Talvez seja mais interessado exibindo as operações quando você recebeu um erro durante a implantação para que este artigo aborda exibindo operações com falha. PowerShell fornece cmdlets que permitem localizar os erros e determinar possíveis correções facilmente.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Você pode evitar alguns erros validando seu modelo e infraestrutura antes da implantação. Você também pode registrar solicitação adicional e informações de resposta durante a implantação que pode ser úteis mais tarde para solução de problemas. Para saber sobre Validando e informações de solicitação e resposta de registro, consulte [implantar um grupo de recursos com o modelo do Gerenciador de recursos do Azure](resource-group-template-deploy.md).

## <a name="use-deployment-operations-to-troubleshoot"></a>Use operações de implantação para solucionar problemas

1. Para obter o status geral de uma implantação, use o comando **Get-AzureRmResourceGroupDeployment** . Você pode filtrar os resultados para apenas essas implantações com falha.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    Que retorna as implantações de falha no seguinte formato:
        
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1

        Outputs                 :
        DeploymentDebugLogLevel :

2. Cada implantação geralmente é composta de várias operações, com cada operação que representa uma etapa no processo de implantação. Para descobrir o que deu errado com uma implantação, você geralmente precisa ver detalhes sobre as operações de implantação. Você pode ver o status das operações com **Get-AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
        
    Que retorna várias operações com cada um no seguinte formato:
        
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}

3. Para obter mais detalhes sobre operações com falha, recupere as propriedades para operações com estado **Falha** .

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
        
    Que retorna todas as operações falhas com cada um no seguinte formato:
        
        provisioningOperation : Create
        provisioningState     : Failed
        timestamp             : 2016-06-14T21:54:55.1468068Z
        duration              : PT3.1449887S
        trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
        serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
        statusCode            : BadRequest
        statusMessage         : @{error=}
        targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                                Microsoft.Network/publicIPAddresses/myPublicIP;
                                resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}

    Observação a ID de acompanhamento para a operação. Você usará que na próxima etapa concentrar-se em uma determinada operação.

4. Para obter a mensagem de status de uma operação de falha específica, use o seguinte comando:

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
        
    Que retorna:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## <a name="use-audit-logs-to-troubleshoot"></a>Usar logs de auditoria para solucionar problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver erros para uma implantação, use as seguintes etapas:

1. Para recuperar entradas de log, execute o comando **Get-AzureRmLog** . Você pode usar os parâmetros **ResourceGroup** e **Status** para retornar somente os eventos que falhou um único grupo de recursos. Se você não especificar uma hora de início e término, entradas para a última hora serão retornadas.
Por exemplo, para recuperar as operações com falha de última hora executam:

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    Você pode especificar um período de tempo específico. No próximo exemplo, vamos para ações de falhas para o último dia. 

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
      
    Ou, você pode definir um exato início e a hora de término para falhas de ações:

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Se esse comando retornar muitas entradas e propriedades, você pode se concentrar seus esforços de auditoria Recuperando a propriedade **Properties** . Nós também deverá incluir o parâmetro **DetailedOutput** para ver as mensagens de erro.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
        
    Que retorna as propriedades das entradas de log no seguinte formato:
        
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...

3. Vamos com base nesses resultados, focalizar o segundo elemento. Você pode refinar os resultados examinando a mensagem de status para essa entrada.

        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
        
    Que retorna:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}



## <a name="next-steps"></a>Próximas etapas

- Para obter ajuda com resolução de erros de implantação específica, consulte [resolver erros comuns ao implantar recursos para Azure com o Gerenciador de recursos do Azure](resource-manager-common-deployment-errors.md).
- Para saber sobre como usar os logs de auditoria para monitorar a outros tipos de ações, consulte [operações com o Gerenciador de recursos de auditoria](resource-group-audit.md).
- Para validar sua implantação antes de executá-lo, consulte [implantar um grupo de recursos com o modelo do Gerenciador de recursos do Azure](resource-group-template-deploy.md).

