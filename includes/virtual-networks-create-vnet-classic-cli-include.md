## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Como criar uma VNet clássica usando CLI do Azure

Você pode usar a CLI do Azure para gerenciar os recursos Azure do prompt de comando de qualquer computador executando o Windows, Linux ou OSX. Para criar um VNet usando a CLI do Azure, siga as etapas abaixo.

1. Se você nunca usou CLI do Azure, consulte [instalar e configurar o CLI do Azure](../articles/xplat-cli-install.md) e siga as instruções para cima até o ponto onde você selecionar sua conta do Azure e assinatura.
2. Execute o comando de **rede azure vnet criar** para criar um VNet e uma sub-rede, conforme mostrado abaixo. A lista mostrada após a saída explica os parâmetros usados.

            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    
    Saída esperada:

            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    - **-vnet**. Nome da VNet a ser criado. Para o nosso cenário, *TestVNet*
    - **-e (ou – espaço de endereço)**. Espaço de endereço de VNet. Para o nosso cenário, *192.168.0.0*
    - **-i (ou - cidr)**. Máscara de rede no formato CIDR. Para o nosso cenário, *16*.
    - **- n (ou --nome de sub-rede**). Nome da primeira sub-rede. Para o nosso cenário, *FrontEnd*.
    - **-p (ou - sub-rede-início-ip)**. Iniciando o endereço IP para sub-rede ou espaço de endereço de sub-rede. Para o nosso cenário, *192.168.1.0*.
    - **-r (ou - cidr sub-rede)**. Máscara de rede no formato CIDR para sub-rede. Para o nosso cenário, *24*.
    - **-l (ou - local)**. Azure região onde o VNet será criado. Para o nosso cenário, *Centro dos EUA*.

3. Execute o comando **criar sub-rede vnet azure** para criar uma sub-rede, conforme mostrado abaixo. A lista mostrada após a saída explica os parâmetros usados.

            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    
    Aqui está a saída esperada para o comando acima:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    - **-t (ou nome de – vnet**. Nome da VNet onde a sub-rede será criada. Para o nosso cenário, *TestVNet*.
    - **-n (ou - nome)**. Nome da nova sub-rede. Para o nosso cenário, *back-end*.
    - **-um (ou - prefixo de endereço)**. Bloco CIDR sub-rede. Quatro nosso cenário, *192.168.2.0/24*.

4. Execute o comando **Mostrar vnet de rede azure** para exibir as propriedades de vnet a nova, conforme mostrado abaixo.

            azure network vnet show

    Aqui está a saída esperada para o comando acima:

            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK
