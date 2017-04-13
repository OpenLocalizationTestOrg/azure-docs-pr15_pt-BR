<properties
   pageTitle="Configurar uma conexão de gateway VPN de ponto-a-Site a uma rede Virtual do Azure usando o portal de Azure | Microsoft Azure"
   description="Conecte com segurança à sua rede Virtual do Azure, criando uma conexão de gateway VPN de ponto-a-Site usando o portal do Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc"/>

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurar uma conexão ponto-a-Site para um VNet usando o portal do Azure

> [AZURE.SELECTOR]
- [Gerenciador de recursos - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Gerenciador de recursos - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Clássico - Portal do Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Este artigo o orienta a criação de um VNet com uma conexão ponto-a-Site no modelo clássico de implantação usando o portal do Azure. Uma configuração de ponto-a-Site (P2S) permite que você criar uma conexão segura de um computador cliente individual para uma rede virtual. Uma conexão de P2S é útil quando você deseja se conectar seu VNet de um local remoto, como em casa ou uma conferência. Ou, quando tiver apenas alguns clientes que precisam se conectar a uma rede virtual.

Conexões ponto-a-Site não exigem um dispositivo VPN ou um endereço IP público para trabalhar. Uma conexão VPN é estabelecida iniciando a conexão do computador cliente. Para obter mais informações sobre conexões ponto-a-Site, consulte as [Perguntas Frequentes do Gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md#point-to-site).

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos de implantação e métodos para conexões P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

A tabela a seguir mostra os dois modelos de implantação e métodos de implantação disponíveis para P2S configurações. Quando um artigo com etapas de configuração estiver disponível, podemos vincular diretamente a ele desta tabela.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Fluxo de trabalho básico 

![Ponto-a-Site-diagrama] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "ponto-a-site")


As seções a seguir irão orientá-lo pelas etapas para criar uma conexão ponto-a-Site seguro a uma rede virtual. 

1. Criar uma rede virtual e gateway VPN
2. Gerar certificados
3. Carregue o arquivo. cer
4. Gerar o pacote de configuração de cliente VPN
5. Configurar o computador cliente
6. Conectar ao Azure

### <a name="example-settings"></a>Configurações de exemplo

Você pode usar as configurações de exemplo a seguir:

- **Nome: VNet1**
- **Espaço de endereço: 192.168.0.0/16**
- **Nome de sub-rede: FrontEnd**
- **Intervalo de endereços de sub-rede: 192.168.1.0/24**
- **Assinatura:** Se você tiver mais de uma assinatura, verifique se você está usando uma correta.
- **Grupo de recursos: TestRG**
- **Local: Leste EUA**
- **Tipo de Conexão: ponto-a-site**
- **Espaço de endereço do cliente: 172.16.201.0/24**. Clientes VPN que se conectam ao VNet usando esta conexão ponto-a-Site receber um endereço IP do pool especificado.
- **GatewaySubnet: 192.168.200.0/24**. A sub-rede de Gateway deve usar o nome "GatewaySubnet".
- **Tamanho:** Selecione o gateway SKU que você deseja usar.
- **Tipo de roteamento: dinâmico**

## <a name="vnetvpn"></a>Seção 1 - criar uma rede virtual e um gateway VPN

### <a name="createvnet"></a>Parte 1: Criar uma rede virtual

Se você ainda não tiver uma rede virtual, crie um. Capturas de tela são fornecidas como exemplos. Certifique-se de que substitua os valores por suas próprias. Para criar um VNet usando o portal do Azure, use as seguintes etapas: 

1. Em um navegador, navegue até o [portal do Azure](http://portal.azure.com) e, se necessário, entre com sua conta do Azure.

2. Clique em **novo**. No campo de **pesquisa de mercado** , digite "Rede Virtual". Localize **Uma rede Virtual** na lista fornecida e clique para abrir a lâmina de **Rede Virtual** .

    ![Procurar blade de rede virtual] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png "Procurar blade de rede virtual")

3. Na parte inferior da lâmina rede Virtual, na lista **Selecionar um modelo de implantação** , selecione **clássico**e clique em **criar**.

    ![Selecione o modelo de implantação] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png "Selecione o modelo de implantação")

4. Na lâmina **Criar rede virtual** , defina as configurações de VNet. Este blade, você adicionará o primeiro espaço de endereço e um intervalo de endereços de sub-rede única. Após terminar de criar a VNet, você pode voltar e adicionar sub-redes adicionais e espaços de endereço.

    ![Criar blade de rede virtual] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png "Criar blade de rede virtual")

5. Verifique se a **assinatura** está correto. Você pode alterar assinaturas usando na lista suspensa.

6. Clique em **grupo de recursos** e selecione um grupo de recursos existente ou crie um novo digitando um nome para o novo grupo de recursos. Se você estiver criando um novo grupo, nomeie o grupo de recursos acordo com os valores de configuração planejada. Para obter mais informações sobre os grupos de recursos, visite [Visão geral do Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md#resource-groups).

7. Em seguida, selecione as configurações de **local** para sua VNet. O local determinará onde os recursos que você implanta este VNet residirão.

8. Selecione **Fixar a painel** se você quiser ser capaz de localizar seu VNet facilmente no painel e clique em **criar**.
    
    ![Fixar em dashboard] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png "Fixar em dashboard")


9. Após clicar em criar, você verá um bloco no painel que reflita o andamento do seu VNet. O bloco é alterado conforme o VNet está sendo criada.

    ![Criando uma rede virtual lado a lado] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Criando uma rede virtual lado a lado")

10. Depois de criar sua rede virtual, você pode adicionar o endereço IP de um servidor DNS para lidar com resolução de nomes. Abrir as configurações para sua rede virtual, clique em servidores DNS e adicione o endereço IP do servidor DNS que você deseja usar. Essa configuração não criar um novo servidor DNS. Certifique-se de adicionar um servidor DNS que seus recursos possam se comunicar com.

Depois que sua rede virtual tiver sido criado, você verá **que criado** listado em **Status** na página redes no portal de clássico do Azure.

### <a name="gateway"></a>Parte 2: Criar um gateway de roteamento dinâmico e sub-rede de gateway

Nesta etapa, você criará uma sub-rede de gateway e um gateway de roteamento dinâmico. No portal do Azure para o modelo clássico de implantação, a criação da sub-rede de gateway e o gateway pode ser feito por meio das mesmas lâminas de configuração.

1. No portal do, navegue até a rede virtual para o qual você deseja criar um gateway.

2. Na lâmina para sua rede virtual, no blade **Visão geral** , na seção de conexões VPN, clique em **Gateway**.

    ![Clique aqui para criar um gateway] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png "Clique aqui para criar um gateway")


3. Na lâmina **Nova Conexão VPN** , selecione **ponto-a-site**.

    ![Tipo de conexão de P2S] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png "Tipo de conexão de P2S")

4. Para o **Espaço de endereço do cliente**, adicione o intervalo de endereços IP. Este é o intervalo do qual os clientes VPN receberão um endereço IP ao conectar. Excluir o intervalo de preenchimento automático e adicionar seu próprio.

    ![Espaço de endereço do cliente] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png "Espaço de endereço do cliente")

5. Selecione a caixa de seleção **criar gateway imediatamente** .

    ![Criar gateway imediatamente] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png "Criar gateway imediatamente")

6. Clique em **configuração do gateway opcional** para abrir a lâmina de **configuração do Gateway** .

    ![Clique em configuração do gateway opcional] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png "Clique em configuração do gateway opcional")

7. Clique em **Configurar sub-rede configurações necessárias** para adicionar a **sub-rede de gateway**. Embora seja possível criar uma sub-rede de gateway o menor /29, recomendamos que você crie uma sub-rede maior que inclui mais endereços selecionando pelo menos /28 ou /27. Isso permitirá endereços suficientes acomodar possíveis configurações adicionais que podem ser no futuro.

    >[AZURE.IMPORTANT] Ao trabalhar com sub-redes de gateway, evite associando um grupo de segurança de rede (NSG) à sub-rede gateway. Associar um grupo de segurança de rede para esta sub-rede pode causar o gateway VPN parar funcionando conforme esperado. Para obter mais informações sobre grupos de segurança de rede, consulte [o que é um grupo de segurança de rede?](../articles/virtual-network/virtual-networks-nsg.md)

    ![Adicionar GatewaySubnet] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png "Adicionar GatewaySubnet")

8. Selecione o **tamanho**do gateway. Este é o gateway SKU que você usará para criar o gateway de rede virtual. No portal do, o SKU padrão é **básico**. Para obter mais informações sobre o gateway SKUs, consulte [Sobre configurações de Gateway VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![tamanho de gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)

9. Selecione o **Tipo de roteamento** para seu gateway. Configurações de P2S requerem um tipo de roteamento **dinâmico** . Clique em **Okey** quando você terminar de configurar este blade.

    ![Configurar o tipo de roteamento] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png "Configurar o tipo de roteamento")

10. Na lâmina **Nova Conexão VPN** , clique em **Okey** na parte inferior da lâmina para começar a criar seu gateway de rede virtual. Isso pode levar até 45 minutos para ser concluída. 


## <a name="generatecerts"></a>Seção 2 - gerar certificados

Certificados são usados por Azure para autenticar os clientes VPN para VPNs ponto-a-Site. Exportar dados de certificação pública (não a chave privada) como uma Base-64 codificada x. 509. cer arquivo de certificado raiz gerado por uma solução de certificado corporativo ou de um certificado auto-assinado. Você importar os dados de certificado pública do certificado raiz no Azure. Além disso, você precisa gerar um certificado de cliente do certificado raiz para clientes. Cada cliente que deseja se conectar à rede virtual usando uma conexão de P2S deve ter instalado um certificado de cliente que foi gerado a partir do certificado raiz.

### <a name="cer"></a>Parte 1: Obter o arquivo. cer do certificado raiz


Se você estiver usando uma solução corporativa, você pode usar sua cadeia de certificados existente. Se você não estiver usando uma solução de autoridade de certificação de empresa, você pode criar um certificado auto-assinado. Um método para criar um certificado auto-assinado é makecert.

- Se você estiver usando um sistema de certificado corporativo, obtenha o arquivo. cer do certificado raiz que você deseja usar. 

- Se você não estiver usando uma solução de certificado de empresa, você precisa gerar um certificado auto-assinado. Para obter etapas do Windows 10, você pode consultar a [trabalhar com certificados raiz autoassinados para configurações de ponto-a-Site](vpn-gateway-certificates-point-to-site.md).

1. Para obter um arquivo. cer de um certificado, abra **certmgr. msc** e localize o certificado raiz. O certificado auto-assinado de atalho, clique em **todas as tarefas**e clique em **Exportar**. Isso abre o **Assistente de exportação de certificado**.

2. No assistente, clique em **Avançar**, selecione **não, não exportar a chave privada**e clique em **Avançar**.

3. Na página **Formato do arquivo de exportação** , selecione **Base 64 509 codificado (. CER).** Em seguida, clique em **Avançar**. 

4. No **arquivo de exportação**, **Navegue** até o local ao qual você deseja exportar o certificado. **Nome do arquivo**, nomeie o arquivo de certificado. Clique em **Avançar**.

5. Clique em **Concluir** para exportar o certificado.


### <a name="genclientcert"></a>Parte 2: Gerar um certificado de cliente

Você pode gerar um certificado exclusivo para cada cliente que se conectará ou você pode usar o mesmo certificado em vários clientes. A vantagem de geração de certificados de cliente exclusivo é a capacidade de revogar um único certificado, se necessário. Caso contrário, se estiver usando o mesmo certificado de cliente e descobrir que você precisa revogar o certificado para um cliente, você precisará gerar e instale novos certificados para todos os clientes que usam o certificado para autenticar.

- Se você estiver usando uma solução de certificado de empresa, gerar um certificado de cliente com o formato de valor de nome comum 'name@yourdomain.com', em vez de no formato 'domínio name\username'. 

- Se você estiver usando um certificado autoassinado, consulte [Trabalhando com certificados raiz autoassinados para configurações de ponto-a-Site](vpn-gateway-certificates-point-to-site.md) para gerar um certificado de cliente.

### <a name="exportclientcert"></a>Parte 3: Exportar o certificado de cliente

Instale um certificado de cliente em cada computador que você deseja conectar à rede virtual. Um certificado de cliente é necessário para autenticação. Você pode automatizar a instalação do certificado de cliente, ou você pode instalá-lo manualmente. As etapas a seguir orientam através de exportação e instalando o certificado de cliente manualmente.

1. Para exportar um certificado de cliente, você pode usar *certmgr. msc*. Clique com botão direito o certificado de cliente que você deseja exportar, clique em **todas as tarefas**e clique em **Exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um arquivo *. pfx* . Certifique-se lembrar da senha (chave) que você definiu para este certificado ou gravar.

## <a name="upload"></a>Seção 3: carregar o arquivo. cer do certificado raiz

Depois que o gateway foi criado, você pode carregar o arquivo. cer um certificado raiz confiável no Azure. Você pode carregar arquivos para até 20 certificados raiz. Você não carregar a chave particular para o certificado raiz no Azure. Uma vez que o arquivo. cer é carregado, Azure usa para autenticar clientes que se conectam à rede virtual.

1. Na seção **conexões VPN** da lâmina para sua VNet, clique no elemento gráfico **clientes** para abrir o **ponto-a-site VPN conexão** blade.

    ![Clientes] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png "Clientes")

2. Sobre a conexão ponto-a-site **** lâmina, clique em **Gerenciar certificados** para abrir a lâmina de **certificados** .<br>

    ![Blade de certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png "Certificates blade")<br><br>


3. Na lâmina **certificados** , clique em **carregar** para abrir a lâmina **carregar certificado** .<br>

    ![Carregar blade de certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png "Upload certificates blade")<br>


4. Clique no gráfico de pasta para procurar o arquivo. cer. Selecione o arquivo, clique em **Okey**. Atualize a página para ver o certificado carregado no blade **certificados** .

    ![Carregar certificado](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png "Upload certificate")<br>
    

## <a name="vpnclientconfig"></a>Seção 4 - gerar o pacote de configuração de cliente VPN

Para se conectar à rede virtual, você também precisa configurar um cliente VPN. O computador cliente requer um certificado de cliente e o pacote de configuração de cliente VPN adequado para se conectar.

O pacote do cliente VPN contém informações de configuração para configurar o software de cliente VPN integrado do Windows. O pacote não instalar software adicional. As configurações são específicas a rede virtual que você deseja se conectar. Para a lista de sistemas operacionais cliente que têm suporte, consulte as conexões de ponto-a-Site [](vpn-gateway-vpn-faq.md#point-to-site-connections) seção de perguntas frequentes sobre o VPN Gateway. 

### <a name="to-generate-the-vpn-client-configuration-package"></a>Para gerar o pacote de configuração de cliente VPN

1. No portal do Azure, na lâmina **Visão geral** do seu VNet, em **conexões VPN**, clique no gráfico de cliente para abrir o **ponto-a-site VPN conexão** blade.
2. Na parte superior do **ponto-a-site VPN conexão** lâmina, clique no pacote de download que corresponde ao sistema operacional do cliente no qual ele será instalado:

 - Para clientes de 64 bits, selecione **O cliente de VPN (64 bits)**.
 - Para clientes de 32 bits, selecione **O cliente de VPN (32 bits)**.

    ![Baixe o pacote de configuração de cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png "Download VPN client configuration package")<br>


3. Você verá uma mensagem que Azure está gerando o pacote de configuração do cliente VPN para a rede virtual. Após alguns minutos, o pacote é gerado e você verá uma mensagem em seu computador local que o pacote foi baixado. Salve o arquivo de pacote de configuração. Você irá instalá-lo em cada computador cliente que irão se conectar à rede virtual usando P2S.


## <a name="clientconfiguration"></a>Seção 5: configurar o computador cliente

### <a name="part-1-install-the-client-certificate"></a>Parte 1: Instalar o certificado de cliente

Cada computador cliente deve ter um certificado de cliente para autenticar. Ao instalar o certificado de cliente, você precisará da senha que foi criada quando o certificado de cliente foi exportado.

1. Copie o arquivo. pfx para o computador cliente.
2. Clique duas vezes no arquivo. pfx para instalá-lo. Não modifique o local de instalação.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>Parte 2: Instalar o pacote de configuração de cliente VPN

Você pode usar o mesmo pacote de configuração de cliente VPN em cada computador cliente, desde que a versão corresponde a arquitetura do cliente.

1. Copie o arquivo de configuração localmente no computador que você deseja se conectar à sua rede virtual e clique duas vezes no arquivo .exe. 

2. Quando tiver instalado o pacote, você pode iniciar a conexão VPN. O pacote de configuração não está assinado pela Microsoft. Você pode desejar assinar o pacote usando o serviço de autenticação de sua organização ou assiná-lo por conta própria usando [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). É Okey usar o pacote sem entrar. No entanto, se o pacote não estiver conectado, um aviso é exibido quando você instala o pacote.

3. No computador cliente, vá para **Configurações de rede** e clique em **VPN**. Você verá a conexão listado. Ele mostra o nome da rede virtual que se conectará a e terá uma aparência semelhante a esta: 

    ![Cliente VPN] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png "Cliente VNet VPN")

## <a name="connect"></a>Seção 6 - conectar ao Azure

### <a name="connect-to-your-vnet"></a>Conectar-se a sua VNet

1. Para se conectar ao seu VNet, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você criou. Ele é denominado o mesmo nome que sua rede virtual. Clique em **Conectar**. Pode aparecer uma mensagem pop-up que se refere a usando o certificado. Se isso acontecer, clique em **continuar** para usar privilégios elevados. 

2. Na página de status de **Conexão** , clique em **Conectar** para iniciar a conexão. Se você vir uma tela de **Selecionar certificado** , verifique se o certificado de cliente mostrando é aquele que você deseja usar para se conectar. Se não estiver, use a seta suspensa para selecionar o certificado correto e clique em **Okey**.

    ![Conectar-se] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png "Conexão de cliente VPN")

3. Agora deve ser estabelecida sua conexão.

    ![Conexão estabelecido] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png "Conexão estabelecida")

### <a name="verify-the-vpn-connection"></a>Verifique se a conexão VPN

1. Para verificar se sua conexão de VPN está ativo, abra um prompt de comando e execute *ipconfig/all*.
2. Exiba os resultados. Observe que o endereço IP recebido é um dos endereços dentro do intervalo de endereço de conectividade ponto-a-Site que você especificou quando você criou sua VNet. Os resultados devem ser algo semelhante a esta:

Exemplo:



    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="next-steps"></a>Próximas etapas

Você pode adicionar máquinas virtuais à sua rede virtual. Veja [como criar uma máquina virtual personalizada](../virtual-machines/virtual-machines-windows-classic-createportal.md).