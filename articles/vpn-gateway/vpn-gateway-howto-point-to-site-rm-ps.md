<properties 
   pageTitle="Configurar uma conexão de gateway VPN de ponto-a-Site para usar o modelo de implantação do Gerenciador de recursos de rede virtual | Microsoft Azure"
   description="Conecte com segurança à sua rede Virtual do Azure, criando uma conexão de gateway VPN de ponto-a-Site."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc" />

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Configurar uma conexão ponto-a-Site para um VNet usando o PowerShell

> [AZURE.SELECTOR]
- [Gerenciador de recursos - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Gerenciador de recursos - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Clássico - Portal do Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Uma configuração de ponto-a-Site (P2S) permite que você criar uma conexão segura de um computador cliente individual para uma rede virtual. Uma conexão de P2S é útil quando você deseja se conectar seu VNet de um local remoto, como em casa ou em uma conferência, ou quando tiver apenas alguns clientes que precisam se conectar a uma rede virtual. 

Conexões ponto-a-Site não exigem um dispositivo VPN ou um endereço IP público para trabalhar. Uma conexão VPN é estabelecida iniciando a conexão do computador cliente. Para obter mais informações sobre conexões ponto-a-Site, consulte as [Perguntas Frequentes do Gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [planejamento e Design](vpn-gateway-plan-design.md). 

Este artigo o orienta a criação de um VNet com uma conexão ponto-a-Site no modelo de implantação do Gerenciador de recursos usando o PowerShell.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos de implantação e métodos para conexões P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

A tabela a seguir mostra os dois modelos de implantação e métodos de implantação disponíveis para P2S configurações. Quando um artigo com etapas de configuração estiver disponível, podemos vincular diretamente a ele desta tabela.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Fluxo de trabalho básico 

![Ponto-a-Site-diagrama] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "ponto-a-site")

Neste cenário, você criará uma rede virtual com uma conexão ponto-a-Site. As instruções também ajudará você gerar certificados que são necessários para esta configuração. Uma conexão de P2S é composta dos seguintes itens: A VNet com um gateway VPN, um arquivo. cer do certificado raiz (chave pública), um certificado de cliente e a configuração de VPN no cliente. 

Usamos os seguintes valores para esta configuração. Definimos as variáveis na seção [1](#declare) do artigo. Você pode usar as etapas como uma passo a passo e use os valores sem alterá-los ou alterá-los para refletir o seu ambiente. 

### <a name="example"></a>Valores de exemplo

- **Nome: VNet1**
- **Espaço de endereço: 192.168.0.0/16** e **10.254.0.0/16**<br>Neste exemplo, usamos mais de um espaço de endereço para ilustrar que essa configuração funcione com vários espaços de endereço. No entanto, vários espaços de endereço não são necessários para esta configuração.
- **Nome de sub-rede: FrontEnd**
    - **Intervalo de endereços de sub-rede: 192.168.1.0/24**
- **Nome de sub-rede: back-end**
    - **Intervalo de endereços de sub-rede: 10.254.1.0/24**
- **Nome de sub-rede: GatewaySubnet**<br>O nome de sub-rede *GatewaySubnet* é obrigatório para o gateway VPN trabalhar.
    - **Intervalo de endereços de sub-rede: 192.168.200.0/24** 
- **Pool de endereços de cliente VPN: 172.16.201.0/24**<br>Clientes VPN que se conectam ao VNet usando esta conexão ponto-a-Site receber um endereço IP do pool de endereços do cliente VPN.
- **Assinatura:** Se você tiver mais de uma assinatura, verifique se você está usando uma correta.
- **Grupo de recursos: TestRG**
- **Local: Leste EUA**
- **Servidor DNS: endereço IP** do servidor DNS que você deseja usar para a resolução de nome.
- **Nome de GW: Vnet1GW**
- **Nome IP público: VNet1GWPIP**
- **VpnType: RouteBased**

## <a name="before-beginning"></a>Antes de começar

- Verifique se você tem uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, você pode ativar seus [benefícios de assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou sinal para cima, para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
- Instale a versão mais recente do Azure Gerenciador de recursos de cmdlets do PowerShell. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell. Ao trabalhar com o PowerShell para essa configuração, certifique-se de que você está executando como administrador. 

## <a name="declare"></a>Parte 1 - Log no e definir variáveis

Nesta seção, você login e declara os valores usados para essa configuração. Os valores declarados são usados nos scripts de exemplo. Altere os valores para refletir o seu ambiente. Ou, você pode usar os valores declarados e percorrer as etapas como um exercício.

1. No console do PowerShell, faça logon em sua conta do Azure. Esse cmdlet solicita as credenciais de logon para sua conta do Azure. Após fazer o login, ele baixa suas configurações de conta para que fiquem disponíveis para o PowerShell do Azure.

        Login-AzureRmAccount 

2. Obter uma lista das suas assinaturas Azure.

        Get-AzureRmSubscription

3. Especifica a assinatura que você deseja usar. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Declare as variáveis que você deseja usar. Use o exemplo a seguir, substituindo os valores para seu próprio quando necessário.

        $VNetName  = "VNet1"
        $FESubName = "FrontEnd"
        $BESubName = "Backend"
        $GWSubName = "GatewaySubnet"
        $VNetPrefix1 = "192.168.0.0/16"
        $VNetPrefix2 = "10.254.0.0/16"
        $FESubPrefix = "192.168.1.0/24"
        $BESubPrefix = "10.254.1.0/24"
        $GWSubPrefix = "192.168.200.0/26"
        $VPNClientAddressPool = "172.16.201.0/24"
        $RG = "TestRG"
        $Location = "East US"
        $DNS = "8.8.8.8"
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"
        
## <a name="ConfigureVNet"></a>Parte 2 - configurar um VNet 

1. Crie um grupo de recursos.

        New-AzureRmResourceGroup -Name $RG -Location $Location

2. Crie as configurações de sub-rede para a rede virtual, nomeando-os *front-end*e *back-end* *GatewaySubnet*. Esses prefixos devem fazer parte do espaço de endereço VNet que você declarados.

        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

3. Crie a rede virtual. O servidor DNS especificado deve ser um servidor DNS que pode resolver os nomes dos recursos que você está se conectando. Neste exemplo, usamos um endereço IP público. Certifique-se de usar seus próprios valores.
    
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

4. Especifica as variáveis para a rede virtual que você criou.

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

5. Solicite um endereço IP público atribuído dinamicamente. Esse endereço IP é necessário para o gateway funcione corretamente. Posteriormente, você conecta o gateway para a configuração de IP do gateway.
        
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="Certificates"></a>Parte 3 - certificados

Certificados são usados por Azure para autenticar os clientes VPN para VPNs ponto-a-Site. Exportar dados de certificação pública (não a chave privada) como uma Base-64 codificada x. 509. cer arquivo de certificado raiz gerado por uma solução de certificado corporativo ou de um certificado auto-assinado. Você importar os dados de certificado pública do certificado raiz no Azure. Além disso, você precisa gerar um certificado de cliente do certificado raiz para clientes. Cada cliente que deseja se conectar à rede virtual usando uma conexão de P2S deve ter instalado um certificado de cliente que foi gerado a partir do certificado raiz.

### <a name="cer"></a>1. obter o arquivo. cer do certificado raiz

Você precisará obter os dados de certificado pública para o certificado de raiz que você deseja usar.

- Se você estiver usando um sistema de certificado corporativo, obtenha o arquivo. cer do certificado raiz que você deseja usar. 

- Se você não estiver usando uma solução de certificado de empresa, você precisa gerar um certificado auto-assinado. Para obter etapas do Windows 10, você pode consultar a [trabalhar com certificados raiz autoassinados para configurações de ponto-a-Site](vpn-gateway-certificates-point-to-site.md).


1. Para obter um arquivo. cer de um certificado, abra **certmgr. msc** e localize o certificado raiz. O certificado auto-assinado de atalho, clique em **todas as tarefas**e clique em **Exportar**. Isso abre o **Assistente de exportação de certificado**.

2. No assistente, clique em **Avançar**, selecione **não, não exportar a chave privada**e clique em **Avançar**.

3. Na página **Formato do arquivo de exportação** , selecione **Base 64 509 codificado (. CER).** Em seguida, clique em **Avançar**. 

4. No **arquivo de exportação**, **Navegue** até o local ao qual você deseja exportar o certificado. **Nome do arquivo**, nomeie o arquivo de certificado. Clique em **Avançar**.

5. Clique em **Concluir** para exportar o certificado.

### <a name="generate"></a>2. gerar o certificado de cliente

Em seguida, gere os certificados de cliente. Você pode gerar um certificado exclusivo para cada cliente que se conectará ou você pode usar o mesmo certificado em vários clientes. A vantagem de geração de certificados de cliente exclusivo é a capacidade de revogar um único certificado, se necessário. Caso contrário, se estiver usando o mesmo certificado de cliente e descobrir que você precisa revogar o certificado para um cliente, você precisará gerar e instale novos certificados para todos os clientes que usam o certificado para autenticar. Os certificados de cliente são instalados em cada computador cliente posteriormente neste exercício.

- Se você estiver usando uma solução de certificado de empresa, gerar um certificado de cliente com o formato de valor de nome comum 'name@yourdomain.com', em vez de NetBIOS 'Domínio \ nomedeusuário' Formatar. 

- Se você estiver usando um certificado autoassinado, consulte [Trabalhando com certificados raiz autoassinados para configurações de ponto-a-Site](vpn-gateway-certificates-point-to-site.md) para gerar um certificado de cliente.

### <a name="exportclientcert"></a>3. exporte o certificado de cliente

Um certificado de cliente é necessário para autenticação. Depois de gerar o certificado de cliente, exportá-lo. O certificado de cliente que exportar será instalado posteriormente em cada computador cliente.

1. Para exportar um certificado de cliente, você pode usar *certmgr. msc*. Clique com botão direito o certificado de cliente que você deseja exportar, clique em **todas as tarefas**e clique em **Exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um arquivo *. pfx* . Certifique-se lembrar da senha (chave) que você definiu para este certificado ou gravar.

### <a name="upload"></a>4. carregue o arquivo. cer do certificado raiz

Declare a variável para o nome do seu certificado, substituindo o valor pelo seu próprio:

        $P2SRootCertName = "Mycertificatename.cer"

Adicione os dados de certificado pública para o certificado raiz no Azure. Você pode carregar arquivos para até 20 certificados raiz. Você não carregar a chave particular para o certificado raiz no Azure. Uma vez que o arquivo. cer é carregado, Azure usa para autenticar clientes que se conectam à rede virtual. 

Substitua o caminho de arquivo pelo seu próprio e, em seguida, execute os cmdlets.
    
        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="creategateway"></a>Parte 4 - criar o gateway VPN

Configurar e criar o gateway de rede virtual para sua VNet. A *-GatewayType* deve ser **Vpn** e a *-VpnType* deve ser **RouteBased**. Isso pode levar até 45 minutos para ser concluída.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="clientconfig"></a>Parte 5 - Baixe o pacote de configuração de cliente VPN

Os clientes se conectam ao Azure usando P2S devem ter um certificado de cliente e um pacote de configuração de cliente VPN instalado. Pacotes de configuração de cliente VPN estão disponíveis para clientes do Windows. O pacote do cliente VPN contém informações para configurar o software de cliente VPN que interna do Windows e é específico para a VPN que você deseja se conectar. O pacote não instalar software adicional. Consulte as [Perguntas Frequentes do Gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) para obter mais informações.

1. Depois que o gateway foi criado, você pode baixar o pacote de configuração do cliente. Este exemplo baixa o pacote para clientes de 64 bits. Se você deseja baixar o cliente de 32 bits, substitua 'Amd64' 'x86'. Você também pode baixar o cliente VPN usando o portal do Azure.

        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

2. O cmdlet do PowerShell retorna um link de URL. Este é um exemplo do que o retornado URL parece:

        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"

3. Copie e cole o link que será retornado um navegador da web para baixar o pacote. Em seguida, instale o pacote no computador cliente. Se você receber um popup SmartScreen, clique em **mais informações**, **Executar mesmo assim** para instalar o pacote.

4. No computador cliente, vá para **Configurações de rede** e clique em **VPN**. Você verá a conexão listado. Ele mostrará o nome da rede virtual que ele se conectará e aparência semelhante a este exemplo: 

    ![Cliente VPN] (./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "Cliente VPN")


## <a name="clientcertificate"></a>Parte 6 - Instale o certificado de cliente

Cada computador cliente deve ter um certificado de cliente para autenticar. Ao instalar o certificado de cliente, você precisará da senha que foi criada quando o certificado de cliente foi exportado.

1. Copie o arquivo. pfx para o computador cliente.
2. Clique duas vezes no arquivo. pfx para instalá-lo. Não modifique o local de instalação.

## <a name="connect"></a>Parte 7 - conectar ao Azure

1. Para se conectar ao seu VNet, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você criou. Ele é denominado o mesmo nome que sua rede virtual. Clique em **Conectar**. Pode aparecer uma mensagem pop-up que se refere a usando o certificado. Se isso acontecer, clique em **continuar** para usar privilégios elevados. 

2. Na página de status de **Conexão** , clique em **Conectar** para iniciar a conexão. Se você vir uma tela de **Selecionar certificado** , verifique se o certificado de cliente mostrando é aquele que você deseja usar para se conectar. Se não estiver, use a seta suspensa para selecionar o certificado correto e clique em **Okey**.

    ![Conexão de cliente VPN] (./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Conexão de cliente VPN")

3. Agora deve ser estabelecida sua conexão.

    ![Conexão estabelecida] (./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Conexão estabelecida")

## <a name="verify"></a>Parte 8 - verificar sua conexão

1. Para verificar se sua conexão de VPN está ativo, abra um prompt de comando e execute *ipconfig/all*.

2. Exiba os resultados. Observe que o endereço IP recebido é um dos endereços dentro do Pool de endereço de cliente VPN de ponto-a-Site especificada na sua configuração. Os resultados devem ser algo semelhante a esta:
    
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="addremovecert"></a>Para adicionar ou remover um certificado raiz confiável

Certificados são usados para autenticar os clientes VPN para VPNs ponto-a-Site. As etapas a seguir orientam através de adicionando e removendo certificados raiz. Quando você adiciona um arquivo codificado na Base 64 x. 509 (. cer) para o Azure, você está dizendo Azure para confiar no certificado raiz que representa o arquivo. 

Você pode adicionar ou remover certificados raiz confiáveis usando o PowerShell ou no portal do Azure. Se você quiser fazer isso usando o portal do Azure, vá para sua **gateway de rede virtual > Configurações > Configuração de ponto-a-site > raiz de certificados**. As etapas a seguir orientam com estas tarefas usando o PowerShell. 

### <a name="add-a-trusted-root-certificate"></a>Adicionar um certificado raiz confiável

Você pode adicionar até 20 arquivos. cer de certificado raiz confiável no Azure. Siga as etapas abaixo para adicionar um certificado raiz.

1. Criar e preparar o novo certificado raiz que você adicionará ao Azure. Exportar a chave pública como uma Base-64 509 codificado (. CER) e abra-o com um editor de texto. Copie apenas a seção mostrada abaixo. 
 
    Copie os valores, conforme mostrado no exemplo a seguir:

    ![certificado] (./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certificado")
    
2. Especifique o nome do certificado e informações importantes como uma variável. Substitua as informações pelo seu próprio, conforme mostrado no seguinte exemplo:

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"


3. Adicione o novo certificado raiz. Você só pode adicionar um certificado de cada vez.

        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2


4. Você pode confirmar que o novo certificado foi adicionado corretamente usando o cmdlet a seguir.

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado raiz confiável

Você pode remover o certificado raiz confiável do Azure. Quando você remove um certificado confiável, os certificados de cliente geradas do certificado raiz será mais possível para se conectar ao Azure via ponto-a-Site. Se desejar que os clientes se conectem, ela precisam instalar um novo certificado de cliente que é gerado a partir de um certificado que é confiável no Azure.

1. Para remover um certificado raiz confiável, modifique o exemplo a seguir:

    Declare as variáveis.

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Remova o certificado.

        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
 
3. Use o cmdlet a seguir para verificar se o certificado foi removido com êxito. 

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="revoke"></a>Para gerenciar a lista de certificados revogados do cliente

Você pode revogar certificados de cliente. A lista de revogação de certificado permite que você negar seletivamente conectividade ponto-a-Site com base em certificados de cliente individuais. Se você remover um certificado de raiz. cer do Azure, ele revoga o acesso para todos os certificados de cliente gerado/assinado pelo certificado raiz revogado. Se você quiser revogar um certificado de cliente específico, não raiz, você pode fazer isso. Dessa forma os outros certificados geradas do certificado raiz ainda irá ser válida.

A prática comum é usar o certificado raiz para gerenciar o acesso em níveis de equipe ou organização, durante o uso de certificados revogados cliente para controle de acesso refinadas usuários individuais.

### <a name="revoke-a-client-certificate"></a>Revogar um certificado de cliente

1. Obtenha a impressão digital do certificado do cliente para revogar.

        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Adicione a impressão digital à lista de impressão digital revogado.

        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Verifique se a impressão digital foi adicionada à lista de revogação de certificado. Você deve adicionar a impressão digital de um por vez.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>Restabelecer um certificado de cliente

Você pode reaplicar um certificado de cliente, removendo a impressão digital na lista de certificados revogados do cliente.

1.  Remova a impressão digital na lista de impressão digital de certificado de cliente revogado.

        Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Verifique se a impressão digital é removida da lista revogada.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## <a name="next-steps"></a>Próximas etapas

Você pode adicionar uma máquina virtual à sua rede virtual. Consulte [criar uma máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter as etapas.