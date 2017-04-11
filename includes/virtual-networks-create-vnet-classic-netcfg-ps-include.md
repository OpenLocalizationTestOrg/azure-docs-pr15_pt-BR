## <a name="how-to-create-a-vnet-using-a-network-config-file-from-powershell"></a>Como criar um VNet usando um arquivo de configuração de rede do PowerShell

Azure usa um arquivo xml para definir todas as VNets disponíveis para uma assinatura. Você pode baixar este arquivo e editá-lo para modificar ou excluir VNets existentes e criar novos. Neste documento, você vai aprender a baixar este arquivo, conhecido como arquivo de configuração (ou netcgf) de rede e editá-lo para criar um novo VNet. Verifique o [esquema de configuração de rede virtual Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) para saber mais sobre o arquivo de configuração de rede.

Para criar um VNet usando um arquivo de netcfg usando o PowerShell, siga as etapas abaixo.

1. Se você nunca usou o PowerShell do Azure, veja [como instalar e configurar o Azure PowerShell](../articles/powershell-install-configure.md) e siga as instruções totalmente ao final para entrar no Azure e selecione sua assinatura.
2. No console do PowerShell do Azure, use o cmdlet **Get-AzureVnetConfig** para baixar o arquivo de configuração de rede, execute o comando a seguir. 

        Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml

    Saída esperada:

        XMLConfiguration                                                                                                     
        ----------------                                                                                                     
        <?xml version="1.0" encoding="utf-8"?>...  

3. Abra o arquivo que você salvou na etapa 2 acima usando qualquer aplicativo de editor de texto ou XML e procure o **<VirtualNetworkSites>** elemento. Se você tiver quaisquer redes já criadas, cada rede será exibido como seu próprio **<VirtualNetworkSite>** elemento.
4. Para criar a rede virtual descrita nesse cenário, adicione o seguinte XML apenas na **<VirtualNetworkSites>** elemento:

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

9.  Salve o arquivo de configuração de rede.
10. No console do PowerShell do Azure, use o cmdlet **Set-AzureVnetConfig** para carregar o arquivo de configuração de rede, execute o comando a seguir. Observe a saída sob o comando, você deve ver **êxito** em **OperationStatus**. Se isto é não o caso, verifique o arquivo xml de erros.

        Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml

    Aqui está a saída esperada para o comando acima:

        OperationDescription OperationId                          OperationStatus
        -------------------- -----------                          ---------------
        Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
    
11. No console do PowerShell do Azure, use o cmdlet **Get-AzureVnetSite** para verificar se a nova rede foi adicionada executando o comando a seguir. 

        Get-AzureVNetSite -VNetName TestVNet

    Aqui está a saída esperada para o comando acima:

        AddressSpacePrefixes : {192.168.0.0/16}
        Location             : Central US
        AffinityGroup        : 
        DnsServers           : {}
        GatewayProfile       : 
        GatewaySites         : 
        Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
        InUse                : False
        Label                : 
        Name                 : TestVNet
        State                : Created
        Subnets              : {FrontEnd, BackEnd}
        OperationDescription : Get-AzureVNetSite
        OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
        OperationStatus      : Succeeded