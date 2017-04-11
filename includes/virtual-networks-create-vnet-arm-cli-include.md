## <a name="how-to-create-a-vnet-using-the-azure-cli"></a>Como criar um VNet utilizando a CLI do Azure

Você pode usar a CLI do Azure para gerenciar os recursos Azure do prompt de comando de qualquer computador executando o Windows, Linux ou OSX. Para criar um VNet usando a CLI do Azure, siga as etapas abaixo.

1. Se você nunca usou o CLI Azure, consulte [instalar e configurar o CLI do Azure](../articles/xplat-cli-install.md) e siga as instruções para cima até o ponto onde você selecionar sua conta do Azure e assinatura.
2. Execute o comando de **modo config azure** para alternar para modo de Gerenciador de recursos, conforme mostrado abaixo.

        azure config mode arm

    Aqui está a saída esperada para o comando acima:

        info:    New mode is arm

3. Se necessário, execute o **azure grupo criar** para criar um novo grupo de recursos, conforme mostrado abaixo. Observe que a saída do comando. A lista mostrada após a saída explica os parâmetros usados. Para obter mais informações sobre os grupos de recursos, visite [Visão geral do Gerenciador de recursos do Azure](../articles/virtual-network/resource-group-overview.md#resource-groups).

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

4. Execute o comando de **rede azure vnet criar** para criar um VNet e uma sub-rede, conforme mostrado abaixo. 

        azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

    Aqui está a saída esperada para o comando acima:

        info:    Executing command network vnet create
        + Looking up virtual network "TestVNet"
        + Creating virtual network "TestVNet"
        + Loading virtual network state
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        info:    network vnet create command OK

    - **-g (ou - grupo de recursos)**. Nome do grupo de recursos onde o VNet será criado. Para o nosso cenário, *TestRG*.
    - **-n (ou - nome)**. Nome da VNet a ser criado. Para o nosso cenário, *TestVNet*
    - **-um (ou - prefixos de endereço)**. Lista de blocos CIDR usado para o espaço de endereço de VNet. Para o nosso cenário, *192.168.0.0/16*
    - **-l (ou - local)**. Azure região onde o VNet será criado. Para o nosso cenário, *centralus*.

5. Execute o comando **criar sub-rede vnet azure** para criar uma sub-rede, conforme mostrado abaixo. Observe que a saída do comando. A lista mostrada após a saída explica os parâmetros usados.

        azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

    Aqui está a saída esperada para o comando acima:

        info:    Executing command network vnet subnet create
        + Looking up the subnet "FrontEnd"
        + Creating subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:
        info:    network vnet subnet create command OK

    - **-e (ou nome de – vnet**. Nome da VNet onde a sub-rede será criada. Para o nosso cenário, *TestVNet*.
    - **-n (ou - nome)**. Nome da nova sub-rede. Para o nosso cenário, *FrontEnd*.
    - **-um (ou - prefixo de endereço)**. Bloco CIDR sub-rede. Quatro nosso cenário, *192.168.1.0/24*.

6. Repita a etapa 5 acima para criar outras sub-redes, se necessário. Para o nosso cenário, execute o comando a seguir para criar a sub-rede de *back-end* .

        azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. Execute o comando **Mostrar vnet de rede azure** para exibir as propriedades de vnet a nova, conforme mostrado abaixo.

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
