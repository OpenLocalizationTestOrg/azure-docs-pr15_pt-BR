<properties 
   pageTitle="Configurar uma conexão de gateway VPN de ponto-a-Site a rede virtual usando o modelo de implantação do Gerenciador de recursos e o portal do Azure | Microsoft Azure"
   description="Conecte com segurança à sua rede Virtual do Azure, criando uma conexão de gateway VPN de ponto-a-Site usando o Gerenciador de recursos e o portal do Azure."
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

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurar uma conexão ponto-a-Site para um VNet usando o Portal do Azure

> [AZURE.SELECTOR]
- [Gerenciador de recursos - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Gerenciador de recursos - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Clássico - Portal do Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Uma configuração de ponto-a-Site (P2S) permite que você criar uma conexão segura de um computador cliente individual para uma rede virtual. Uma conexão de P2S é útil quando você deseja se conectar seu VNet de um local remoto, como em casa ou em uma conferência, ou quando tiver apenas alguns clientes que precisam se conectar a uma rede virtual. 

Conexões ponto-a-Site não exigem um dispositivo VPN ou um endereço IP público para trabalhar. Uma conexão VPN é estabelecida iniciando a conexão do computador cliente. Para obter mais informações sobre conexões ponto-a-Site, consulte as [Perguntas Frequentes do Gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [planejamento e Design](vpn-gateway-plan-design.md).

Este artigo o orienta a criação de um VNet com uma conexão ponto-a-Site no modelo de implantação do Gerenciador de recursos usando o portal do Azure.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos de implantação e métodos para conexões P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

A tabela a seguir mostra os dois modelos de implantação e métodos de implantação disponíveis para P2S configurações. Quando um artigo com etapas de configuração estiver disponível, podemos vincular diretamente a ele desta tabela.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Fluxo de trabalho básico 

![Ponto-a-Site-diagrama] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "ponto-a-site")

### <a name="example"></a>Valores de exemplo

- **Nome: VNet1**
- **Espaço de endereço: 192.168.0.0/16**<br>Neste exemplo, usamos espaço apenas um endereço. Você pode ter mais de um espaço de endereço para seu VNet.
- **Nome de sub-rede: FrontEnd**
- **Intervalo de endereços de sub-rede: 192.168.1.0/24**
- **Assinatura:** Se você tiver mais de uma assinatura, verifique se você está usando uma correta.
- **Grupo de recursos: TestRG**
- **Local: Leste EUA**
- **GatewaySubnet: 192.168.200.0/24**
- **Nome do gateway de rede virtual: VNet1GW**
- **Tipo de gateway: VPN**
- **Tipo de VPN: baseada em roteiro**
- **Endereço IP público: VNet1GWpip**
- **Tipo de Conexão: ponto-a-site**
- **Pool de endereços do cliente: 172.16.201.0/24**<br>Clientes VPN que se conectam ao VNet usando esta conexão ponto-a-Site receber um endereço IP do pool de endereços de cliente.

## <a name="before-beginning"></a>Antes de começar

- Verifique se você tem uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, você pode ativar seus [benefícios de assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou sinal para cima, para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
## <a name="createvnet"></a>Parte 1 - criar uma rede virtual

Se você estiver criando esta configuração como um exercício, você pode fazer referência aos [valores de exemplo](#example).

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

### <a name="2-add-additional-address-space-and-subnets"></a>2. adicionar espaço de endereço adicional e sub-redes

Você pode adicionar espaço de endereço adicional e sub-redes a sua VNet depois que ela foi criada.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

### <a name="3-create-a-gateway-subnet"></a>3. criar uma sub-rede de gateway

Antes de conectar sua rede virtual para um gateway, primeiro é necessário criar a sub-rede de gateway para a rede virtual à qual você deseja se conectar. Se possível, é melhor criar uma sub-rede de gateway usando um bloco CIDR de /28 ou /27 para fornecer endereços IP suficientes para acomodar requisitos adicionais de configuração de futuras.

As capturas de tela nesta seção são fornecidas como um exemplo de referência. Certifique-se de usar o intervalo de endereços GatewaySubnet que corresponde com os valores necessários para a sua configuração.

#### <a name="to-create-a-gateway-subnet"></a>Para criar uma sub-rede de gateway

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="dns"></a>4. especificar um servidor DNS (opcional)

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>Parte 2 - criar um gateway de rede virtual

Conexões de ponto-a-site requerem as seguintes configurações:

- Tipo de gateway: VPN
- Tipo de VPN: baseada em roteiro

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>Parte 3 - gerar certificados

Certificados são usados por Azure para autenticar os clientes VPN para VPNs ponto-a-Site. Exportar dados de certificação pública (não a chave privada) como uma Base-64 codificada x. 509. cer arquivo de certificado raiz gerado por uma solução de certificado corporativo ou de um certificado auto-assinado. Você importar os dados de certificado pública do certificado raiz no Azure. Além disso, você precisa gerar um certificado de cliente do certificado raiz para clientes. Cada cliente que deseja se conectar à rede virtual usando uma conexão de P2S deve ter instalado um certificado de cliente que foi gerado a partir do certificado raiz.

### <a name="getcer"></a>1. obter o arquivo. cer do certificado raiz

Se você estiver usando uma solução corporativa, você pode usar sua cadeia de certificados existente. Se você não estiver usando uma solução de autoridade de certificação de empresa, você pode criar um certificado auto-assinado. Um método para criar um certificado auto-assinado é makecert.

- Se você estiver usando um sistema de certificado corporativo, obtenha o arquivo. cer do certificado raiz que você deseja usar. 

- Se você não estiver usando uma solução de certificado de empresa, você precisa gerar um certificado auto-assinado. Para obter etapas do Windows 10, você pode consultar a [trabalhar com certificados raiz autoassinados para configurações de ponto-a-Site](vpn-gateway-certificates-point-to-site.md).

1. Para obter um arquivo. cer de um certificado, abra **certmgr. msc** e localize o certificado raiz. O certificado auto-assinado de atalho, clique em **todas as tarefas**e clique em **Exportar**. Isso abre o **Assistente de exportação de certificado**.

2. No assistente, clique em **Avançar**, selecione **não, não exportar a chave privada**e clique em **Avançar**.

3. Na página **Formato do arquivo de exportação** , selecione **Base 64 509 codificado (. CER).** Em seguida, clique em **Avançar**. 

4. No **arquivo de exportação**, **Navegue** até o local ao qual você deseja exportar o certificado. **Nome do arquivo**, nomeie o arquivo de certificado. Clique em **Avançar**.

5. Clique em **Concluir** para exportar o certificado.

### <a name="generateclientcert"></a>2. gerar um certificado de cliente

Você pode gerar um certificado exclusivo para cada cliente que se conectará ou você pode usar o mesmo certificado em vários clientes. A vantagem de geração de certificados de cliente exclusivo é a capacidade de revogar um único certificado, se necessário. Caso contrário, se estiver usando o mesmo certificado de cliente e descobrir que você precisa revogar o certificado para um cliente, você precisará gerar e instale novos certificados para todos os clientes que usam o certificado para autenticar.

- Se você estiver usando uma solução de certificado de empresa, gerar um certificado de cliente com o formato de valor de nome comum 'name@yourdomain.com', em vez de no formato 'domínio name\username'. 

- Se você estiver usando um certificado autoassinado, consulte [Trabalhando com certificados raiz autoassinados para configurações de ponto-a-Site](vpn-gateway-certificates-point-to-site.md) para gerar um certificado de cliente.

### <a name="exportclientcert"></a>3. exporte o certificado de cliente

Um certificado de cliente é necessário para autenticação. Depois de gerar o certificado de cliente, exportá-lo. O certificado de cliente que exportar será instalado posteriormente em cada computador cliente.

1. Para exportar um certificado de cliente, você pode usar *certmgr. msc*. Clique com botão direito o certificado de cliente que você deseja exportar, clique em **todas as tarefas**e clique em **Exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um arquivo *. pfx* . Certifique-se lembrar da senha (chave) que você definiu para este certificado ou gravar.

## <a name="addresspool"></a>Parte 4: adicionar o pool de endereços do cliente

1. Depois que o gateway de rede virtual tiver sido criado, navegue até a seção **configurações** da lâmina gateway rede virtual. Na seção **configurações** , clique em configuração de **ponto-a-site** para abrir a lâmina de **configuração** .

    ![aponte para blade de site] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png "aponte para blade de site")

2. **Pool de endereços** é o pool de endereços IP do qual os clientes que se conectam receberão um endereço IP. Adicione o pool de endereços e clique em **Salvar**.

    ![pool de endereços do cliente] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png "pool de endereços do cliente")

## <a name="uploadfile"></a>Parte 5: carregar o arquivo. cer do certificado raiz

Depois que o gateway foi criado, você pode carregar o arquivo. cer um certificado raiz confiável no Azure. Você pode carregar arquivos para até 20 certificados raiz. Você não carregar a chave particular para o certificado raiz no Azure. Uma vez que o arquivo. cer é carregado, Azure usa para autenticar clientes que se conectam à rede virtual.

1. Navegue até a configuração de **ponto-a-site** blade. Você irá adicionar os arquivos. cer na seção de **certificado raiz** deste blade.

    ![aponte para blade de site] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcert.png "aponte para blade de site")

2. Certifique-se de que você exportou o certificado raiz como uma Base-64 codificado arquivo x. 509 (. cer). Você precisa exportá-lo neste formato, para que você possa abrir o certificado com editor de texto.
3. Abra o certificado com um editor de texto, como o bloco de notas. Copie apenas a seção a seguir:

    ![dados de certificado] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png "dados de certificado")

4. Cole os dados de certificado na seção **Dados públicos de certificado** do portal. Coloque o nome do certificado no espaço para **nome** e clique em **Salvar**. Você pode adicionar até 20 certificados raiz confiáveis.

    ![carregar certificado] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploadcert.png "carregar certificado")

## <a name="clientconfig"></a>Parte 6 - Baixe e instale o pacote de configuração de cliente VPN

Os clientes se conectam ao Azure usando P2S devem ter um certificado de cliente e um pacote de configuração de cliente VPN instalado. Pacotes de configuração de cliente VPN estão disponíveis para clientes do Windows. 

O pacote do cliente VPN contém informações para configurar o software de cliente VPN interna do Windows. A configuração é específica a VPN que você deseja se conectar. O pacote não instalar software adicional. Consulte as [Perguntas Frequentes do Gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) para obter mais informações.

1. Sobre a configuração de **ponto-a-site** lâmina, clique em **Baixar VPN cliente** para abrir a lâmina de **cliente VPN baixar** .

    ![Baixar o cliente VPN] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadclient.png "Baixar o cliente VPN")

2. Selecione o pacote correto para seu cliente e, em seguida, clique em **Baixar**. Para clientes de 64 bits, selecione **AMD64**. Para clientes de 32 bits, selecione **x86**.

3. Instale o pacote no computador cliente. Se você receber um popup SmartScreen, clique em **mais informações**, **Executar mesmo assim** para instalar o pacote.

4. No computador cliente, vá para **Configurações de rede** e clique em **VPN**. Você verá a conexão listado. Ele mostrará o nome da rede virtual que ele se conectará e aparência semelhante a este exemplo: 

    ![Cliente VPN] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png "Cliente VPN")

## <a name="installclientcert"></a>Parte 7 - instalar o certificado de cliente

Cada computador cliente deve ter um certificado de cliente para autenticar. Ao instalar o certificado de cliente, você precisará da senha que foi criada quando o certificado de cliente foi exportado.

1. Copie o arquivo. pfx para o computador cliente.
2. Clique duas vezes no arquivo. pfx para instalá-lo. Não modifique o local de instalação.

## <a name="connect"></a>Parte 8 - conectar ao Azure

1. Para se conectar ao seu VNet, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você criou. Ele é denominado o mesmo nome que sua rede virtual. Clique em **Conectar**. Pode aparecer uma mensagem pop-up que se refere a usando o certificado. Se isso acontecer, clique em **continuar** para usar privilégios elevados. 

2. Na página de status de **Conexão** , clique em **Conectar** para iniciar a conexão. Se você vir uma tela de **Selecionar certificado** , verifique se o certificado de cliente mostrando é aquele que você deseja usar para se conectar. Se não estiver, use a seta suspensa para selecionar o certificado correto e clique em **Okey**.

    ![Cliente VPN 2] (./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Conexão de cliente VPN")

3. Agora deve ser estabelecida sua conexão.

    ![Cliente VPN 3] (./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Conexão de cliente VPN 2")

## <a name="verify"></a>Parte 9 - Verifique se sua conexão

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

## <a name="add"></a>Para adicionar ou remover certificados raiz confiáveis

Você pode remover o certificado raiz confiável do Azure. Quando você remove um certificado confiável, os certificados de cliente geradas do certificado raiz será mais possível para se conectar ao Azure via ponto-a-Site. Se desejar que os clientes se conectem, ela precisam instalar um novo certificado de cliente que é gerado a partir de um certificado que é confiável no Azure.

Você pode gerenciar a lista de certificados revogados cliente na configuração **ponto-a-site** blade. Este é o blade que você usou para [carregar um certificado raiz confiável](#uploadfile).

## <a name="revokeclient"></a>Para gerenciar a lista de certificados revogados do cliente

Você pode revogar certificados de cliente. A lista de revogação de certificado permite que você negar seletivamente conectividade ponto-a-Site com base em certificados de cliente individuais. Se você remover um certificado de raiz. cer do Azure, ele revoga o acesso para todos os certificados de cliente gerado/assinado pelo certificado raiz revogado. Se você quiser revogar um certificado de cliente específico, não raiz, você pode fazer isso. Dessa forma os outros certificados geradas do certificado raiz ainda irá ser válida. 

A prática comum é usar o certificado raiz para gerenciar o acesso em níveis de equipe ou organização, durante o uso de certificados revogados cliente para controle de acesso refinadas usuários individuais.

Você pode gerenciar a lista de certificados revogados cliente na configuração **ponto-a-site** blade. Este é o blade que você usou para [carregar um certificado raiz confiável](#uploadfile).


## <a name="next-steps"></a>Próximas etapas

Você pode adicionar uma máquina virtual à sua rede virtual. Consulte [criar uma máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter as etapas.