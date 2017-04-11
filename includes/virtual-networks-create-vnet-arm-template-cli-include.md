## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Implantar o modelo ARM usando a CLI do Azure

Para implantar o modelo ARM que você baixou usando Azure CLI, siga as etapas abaixo.

1. Se você nunca usou CLI do Azure, consulte [instalar e configurar o CLI do Azure](../articles/xplat-cli-install.md) e siga as instruções para cima até o ponto onde você selecionar sua conta do Azure e assinatura.
2. Executar o **`azure config mode`** comando para alternar para modo de Gerenciador de recursos, conforme mostrado abaixo.

        azure config mode arm

    Aqui está a saída esperada para o comando acima:

        info:    New mode is arm

3. Se necessário, execute o **`azure group create`** para criar um novo grupo de recursos, conforme mostrado abaixo. Observe que a saída do comando. A lista mostrada após a saída explica os parâmetros usados. Para obter mais informações sobre os grupos de recursos, visite [Visão geral do Gerenciador de recursos do Azure](../articles/resource-group-overview.md).

        azure group create -n TestRG -l centralus

    Aqui está a saída esperada para o comando acima:

        info:    Executing command group create
        + Getting resource group TestRG
        + Creating resource group TestRG
        info:    Created resource group TestRG
        data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            centralus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

    - **-n (ou - nome)**. Nome do novo grupo de recursos. Para o nosso cenário, *TestRG*.
    - **-l (ou - local)**. Azure região onde o novo grupo de recursos será criado. Para o nosso cenário, *centralus*.

4. Executar o **`azure group deployment create`** cmdlet para implantar o novo VNet usando o modelo e o parâmetro arquivos que você baixou e modificado acima. A lista mostrada após a saída explica os parâmetros usados.

        azure group deployment create -g TestRG -n TestVNetDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

    Aqui está a saída esperada para o comando acima:

        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "TestVNetDeployment"
        + Registering providers
        info:    Registering provider microsoft.network
        + Waiting for deployment to complete
        data:    DeploymentName     : TestVNetDeployment
        data:    ResourceGroupName  : TestRG
        data:    ProvisioningState  : Succeeded
        data:    Timestamp          : 2015-08-14T21:56:11.152759Z
        data:    Mode               : Incremental
        data:    Name           Type    Value
        data:    -------------  ------  --------------
        data:    location       String  Central US
        data:    vnetName       String  TestVNet
        data:    addressPrefix  String  192.168.0.0/16
        data:    subnet1Prefix  String  192.168.1.0/24
        data:    subnet1Name    String  FrontEnd
        data:    subnet2Prefix  String  192.168.2.0/24
        data:    subnet2Name    String  BackEnd
        info:    group deployment create command OK

    - **-g (ou - grupo de recursos)**. Nome do grupo de recursos o novo VNet será criado em.
    - **-f (ou --arquivo de modelo)**. Caminho para o arquivo de modelo ARM.
    - **-e (ou - arquivo de parâmetros)**. Caminho para o seu arquivo de parâmetros ARM.

5. Executar o **`azure network vnet show`** comando para exibir as propriedades de vnet a nova, conforme mostrado abaixo.

        azure network vnet show -g TestRG -n TestVNet

    Aqui está a saída esperada para o comando acima:

        info:    Executing command network vnet show
        + Looking up virtual network "TestVNet"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        data:    Subnets:
        data:      Name                          : FrontEnd
        data:      Address prefix                : 192.168.1.0/24
        data:
        data:      Name                          : BackEnd
        data:      Address prefix                : 192.168.2.0/24
        data:
        info:    network vnet show command OK
