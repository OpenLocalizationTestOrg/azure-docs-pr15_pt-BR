<properties
   pageTitle="Configurar uma conexão de gateway VPN de ponto-a-Site a uma rede Virtual do Azure usando o portal de clássico | Microsoft Azure"
   description="Conecte com segurança à sua rede Virtual do Azure, criando uma conexão de gateway VPN de ponto-a-Site."
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

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-classic-portal"></a>Configurar uma conexão ponto-a-Site para um VNet usando o portal de clássico

> [AZURE.SELECTOR]
- [Gerenciador de recursos - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Gerenciador de recursos - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Clássico - Portal do Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
- [Clássico - Portal clássico](vpn-gateway-point-to-site-create.md)

Uma configuração de ponto-a-Site (P2S) permite que você criar uma conexão segura de um computador cliente individual para uma rede virtual. Uma conexão de P2S é útil quando você deseja se conectar seu VNet de um local remoto, como em casa ou em uma conferência, ou quando tiver apenas alguns clientes que precisam se conectar a uma rede virtual.

Este artigo o orienta criando um VNet com uma conexão ponto-a-Site no **modelo clássico de implantação** usando o **portal clássico**.

Conexões ponto-a-Site não exigem um dispositivo VPN ou um endereço IP público para trabalhar. Uma conexão VPN é estabelecida iniciando a conexão do computador cliente. Para obter mais informações sobre conexões ponto-a-Site, consulte as [Perguntas Frequentes do Gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [planejamento e Design](vpn-gateway-plan-design.md).


### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos de implantação e métodos para conexões P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

A tabela a seguir mostra os dois modelos de implantação e métodos de implantação disponíveis para P2S configurações. Quando um artigo com etapas de configuração estiver disponível, podemos vincular diretamente a ele desta tabela.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 



## <a name="basic-workflow"></a>Fluxo de trabalho básico

![Ponto-a-Site-diagrama] (./media/vpn-gateway-point-to-site-create/p2sclassic.png "ponto-a-site")
 
As etapas a seguir irão orientá-lo pelas etapas para criar uma conexão ponto-a-Site seguro a uma rede virtual. 

A configuração de uma conexão ponto-a-Site é dividida em quatro seções. A ordem na qual você configurar cada uma dessas seções é importante. Não ignorar as etapas ou pular.

- **Seção 1** Crie uma rede virtual e gateway VPN.
- **Seção 2** Criar os certificados usados para autenticação e carregue-os.
- **Seção 3** Exportar e instalar os certificados de cliente.
- **Seção 4** Configure seu cliente VPN.

## <a name="vnetvpn"></a>Seção 1 - criar uma rede virtual e um gateway VPN

### <a name="part-1-create-a-virtual-network"></a>Parte 1: Criar uma rede virtual

1. Faça logon no [portal de clássico Azure](https://manage.windowsazure.com/). Essas etapas usam o portal clássico, não o portal do Azure. Atualmente, não é possível criar uma conexão de P2S usando o portal do Azure.

2. No canto inferior esquerdo da tela, clique em **novo**. No painel de navegação, clique em **Serviços de rede**e clique em **Rede Virtual**. Clique em **Criar personalizada** para iniciar o Assistente de configuração.

3. Na página **Detalhes de rede Virtual** , insira as seguintes informações e clique na seta próxima na parte inferior direita.
    - **Nome**: o nome de sua rede virtual. Por exemplo, 'VNet1'. Este é o nome que você vai se referir a quando você implanta VMs para este VNet.
    - **Local**: O local está relacionado diretamente para o local físico (região) onde deseja que seus recursos (VMs) para residem. Por exemplo, se desejar que as VMs que você implantar a esta rede virtual a ser localizado física em Leste EUA, selecione esse local. Você não pode alterar a região associada a sua rede virtual depois de criá-lo.

4. Na página **DNS Servers e a conectividade VPN** , insira as seguintes informações e clique na seta próxima na parte inferior direita.
    - **Servidores DNS**: digite o nome do servidor DNS e o endereço IP ou selecione um servidor DNS anteriormente registrado no menu de atalho. Essa configuração não cria um servidor DNS. Ele permite especificar os servidores DNS que você deseja usar para a resolução de nome para esta rede virtual. Se você quiser usar o serviço de resolução de nome do Azure padrão, deixe esta seção em branco.
    - **Configurar ponto-To-Site VPN**: marque a caixa de seleção.

5. Na página de **Conectividade de ponto-a-Site** , especifique o intervalo de endereços IP do qual seus clientes VPN receberão um endereço IP quando conectado. Existem algumas regras sobre os intervalos de endereços que você pode especificar. É importante verificar que o intervalo que você especificar não se sobreponham com qualquer um dos intervalos localizados na sua rede local.

6. Insira as seguintes informações e, em seguida, clique na seta próxima.
 - **Espaço de endereço**: incluir o IP inicial e CIDR (contagem de endereço).
 - **Adicionar espaço de endereço**: adicionar espaço de endereço somente se for necessário para o seu design de rede.

7. Na página de **Espaços de endereço de rede Virtual** , especifique o intervalo de endereços que você deseja usar para sua rede virtual. Esses são os endereços IP dinâmicos (queda) que serão atribuídos aos VMs e outras ocorrências de função que você implantar a esta rede virtual.<br><br>É especialmente importante selecionar um intervalo que não sobreponha com qualquer um dos intervalos que são usados para sua rede local. Você deve coordenar com seu administrador de rede, que pode ser necessário reservar um intervalo de endereços IP do seu espaço de endereço de rede local para ser usado para sua rede virtual.

8. Insira as seguintes informações e, em seguida, clique na marca de seleção para começar a criar sua rede virtual.
 - **Espaço de endereço**: adicionar o intervalo de endereços IP interno que você deseja usar para esta rede virtual, incluindo IP inicial e contagem. É importante selecionar um intervalo que não sobreponha com qualquer um dos intervalos que são usados para sua rede local. 
 - **Adicionar sub-rede**: sub-redes adicionais não são necessárias, mas talvez você queira criar uma sub-rede separada para VMs que terão queda estática. Ou talvez você queira ter suas VMs em uma sub-rede que é separada do seu outras instâncias de função.
 - **Adicionar sub-rede de gateway**: A sub-rede de gateway é necessária para uma VPN de ponto-a-Site. Clique para adicionar a sub-rede de gateway. A sub-rede de gateway é usada somente para o gateway de rede virtual.

9. Depois que sua rede virtual tiver sido criado, você verá **que criado** listado em **Status** na página redes no portal de clássico do Azure. Depois que sua rede virtual tiver sido criado, você pode criar seu gateway roteamento dinâmico.

### <a name="part-2-create-a-dynamic-routing-gateway"></a>Parte 2: Criar um gateway de roteamento dinâmico

O tipo de gateway deve ser configurado como dinâmica. Gateways roteamento estáticos não funcionam com esse recurso.

1. No portal do clássico Azure, na página de **redes** , clique na rede virtual que você criou e navegue até a página de **painel** .

2. Clique em **Criar Gateway**, localizado na parte inferior da página de **painel** . For exibida uma mensagem solicitando **que você deseja criar um gateway para uma rede virtual "VNet1"**. Clique em **Sim** para começar a criar o gateway. Pode levar cerca de 15 minutos para o gateway criar.

## <a name="generate"></a>Seção 2 - gerar e fazer upload de certificados

Certificados são usados para autenticar os clientes VPN para VPNs ponto-a-Site. Você pode usar um certificado raiz gerado por uma solução de certificado de empresa, ou você pode usar um certificado autoassinado. Você pode carregar até 20 certificados raiz no Azure. Uma vez que o arquivo. cer é carregado, Azure pode usar as informações contidas para autenticar clientes que tenham um certificado de cliente instalado. O certificado de cliente deve ser gerado do mesmo certificado que representa o arquivo. cer.

Nesta seção, você irá fazer o seguinte:

- Obtenha o arquivo. cer para um certificado raiz. Isso pode ser um certificado autoassinado, ou você pode usar seu sistema de certificado corporativo.
- Carregue o arquivo. cer no Azure.
- Gere certificados de cliente.

### <a name="root"></a>Parte 1: Obter o arquivo. cer do certificado raiz

Se você estiver usando um sistema de certificado corporativo, obtenha o arquivo. cer do certificado raiz que você deseja usar. Na [parte 3](#createclientcert), você pode gerar os certificados de cliente do certificado raiz.

Se você não estiver usando uma solução de certificado de empresa, você precisará gerar um certificado auto-assinado. Para obter etapas do Windows 10, você pode consultar a [trabalhar com certificados raiz autoassinados para configurações de ponto-a-Site](vpn-gateway-certificates-point-to-site.md). O artigo conduz você por usando makecert para gerar um certificado auto-assinado e, em seguida, exportar o arquivo. cer.

### <a name="upload"></a>Parte 2: Carregar o arquivo. cer portal do Azure clássico do certificado raiz

Adicione um certificado confiável no Azure. Quando você adiciona um arquivo codificado na Base 64 x. 509 (. cer) para o Azure, você está dizendo Azure para confiar no certificado raiz que representa o arquivo.

1. No portal do clássico Azure, na página **certificados** para sua rede virtual, clique em **carregar um certificado raiz**.

2. Na página **Carregar certificado** , procure o certificado de raiz. cer e, em seguida, clique na marca de seleção.

### <a name="createclientcert"></a>Parte 3: Gerar um certificado de cliente

Em seguida, gere os certificados de cliente. Você pode gerar um certificado exclusivo para cada cliente que se conectará ou você pode usar o mesmo certificado em vários clientes. A vantagem de geração de certificados de cliente exclusivo é a capacidade de revogar um único certificado, se necessário. Caso contrário, se estiver usando o mesmo certificado de cliente e descobrir que você precisa revogar o certificado para um cliente, você precisará gerar e instale novos certificados para todos os clientes que usam o certificado para autenticar.

- Se você estiver usando uma solução de certificado de empresa, gerar um certificado de cliente com o formato de valor de nome comum 'name@yourdomain.com', em vez de NetBIOS 'Domínio \ nomedeusuário' Formatar. 

- Se você estiver usando um certificado autoassinado, consulte [Trabalhando com certificados raiz autoassinados para configurações de ponto-a-Site](vpn-gateway-certificates-point-to-site.md) para gerar um certificado de cliente.

## <a name="installclientcert"></a>Seção 3 - exportar e instalar o certificado de cliente

Instale um certificado de cliente em cada computador que você deseja conectar à rede virtual. Um certificado de cliente é necessário para autenticação. Você pode automatizar a instalação do certificado de cliente, ou você pode instalar manualmente. As etapas a seguir orientam através de exportação e instalando o certificado de cliente manualmente.

1. Para exportar um certificado de cliente, você pode usar *certmgr. msc*. Clique com botão direito o certificado de cliente que você deseja exportar, clique em **todas as tarefas**e clique em **Exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um arquivo *. pfx* . Certifique-se lembrar da senha (chave) que você definiu para este certificado ou gravar.
3. Copie o arquivo *. pfx* para o computador cliente. No computador cliente, clique duas vezes no arquivo *. pfx* para instalá-lo. Insira a senha quando solicitado. Não modifique o local de instalação.

## <a name="vpnclientconfig"></a>Seção 4 - Configure seu cliente VPN

Para se conectar à rede virtual, você também precisa configurar um cliente VPN. O cliente requer um certificado de cliente e a configuração de cliente VPN adequada para se conectar. Para configurar um cliente VPN, execute as etapas a seguir, em ordem.

### <a name="part-1-create-the-vpn-client-configuration-package"></a>Parte 1: Criar o pacote de configuração de cliente VPN

1. No portal do clássico Azure, na página do **painel** para sua rede virtual, navegue até o menu rapidamente no canto direito. Para a lista de sistemas operacionais cliente que têm suporte, consulte as conexões de ponto-a-Site [](vpn-gateway-vpn-faq.md#point-to-site-connections) seção de perguntas frequentes sobre o VPN Gateway. O pacote do cliente VPN contém informações de configuração para configurar o software de cliente VPN integrado do Windows. O pacote não instalar software adicional. As configurações são específicas a rede virtual que você deseja se conectar.<br><br>Selecione o pacote de download que corresponde ao sistema operacional do cliente no qual ele será instalado:
 - Para clientes de 32 bits, selecione **baixar o pacote de VPN do cliente de 32 bits**.
 - Para clientes de 64 bits, selecione **baixar o pacote de VPN do cliente de 64 bits**.

2. Leva alguns minutos para criar o pacote do cliente. Depois que o pacote foi concluído, você pode baixar o arquivo. O arquivo de *.exe* que você baixe pode ser armazenado com segurança no computador local.

3. Depois de gerar e baixe o pacote do cliente VPN do portal clássico do Azure, você pode instalar o pacote do cliente no computador cliente do qual você deseja se conectar à sua rede virtual. Se você planeja instalar o pacote do cliente VPN para vários computadores cliente, certifique-se de que cada um deles também ter um certificado de cliente instalado.

### <a name="part-2-install-the-vpn-configuration-package-on-the-client"></a>Parte 2: Instalar o pacote de configuração de VPN no cliente

1. Copie o arquivo de configuração localmente no computador que você deseja se conectar à sua rede virtual e clique duas vezes no arquivo .exe. 

2. Quando tiver instalado o pacote, você pode iniciar a conexão VPN. O pacote de configuração não está assinado pela Microsoft. Você pode desejar assinar o pacote usando o serviço de autenticação de sua organização ou assiná-lo por conta própria usando [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). É Okey usar o pacote sem entrar. No entanto, se o pacote não estiver conectado, um aviso é exibido quando você instala o pacote.

3. No computador cliente, vá para **Configurações de rede** e clique em **VPN**. Você verá a conexão listado. Ele mostrará o nome da rede virtual que se conectará a e terá uma aparência semelhante a esta: 

    ![Cliente VPN] (./media/vpn-gateway-point-to-site-create/vpn.png "Cliente VPN")


### <a name="part-3-connect-to-azure"></a>Parte 3: Conectar ao Azure

1. Para se conectar ao seu VNet, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você criou. Ele é denominado o mesmo nome que sua rede virtual. Clique em **Conectar**. Pode aparecer uma mensagem pop-up que se refere a usando o certificado. Se isso acontecer, clique em **continuar** para usar privilégios elevados. 

2. Na página de status de **Conexão** , clique em **Conectar** para iniciar a conexão. Se você vir uma tela de **Selecionar certificado** , verifique se o certificado de cliente mostrando é aquele que você deseja usar para se conectar. Se não estiver, use a seta suspensa para selecionar o certificado correto e clique em **Okey**.

    ![Cliente VPN 2] (./media/vpn-gateway-point-to-site-create/clientconnect.png "Conexão de cliente VPN")

3. Agora deve ser estabelecida sua conexão.

    ![Cliente VPN 3] (./media/vpn-gateway-point-to-site-create/connected.png "Conexão de cliente VPN 2")

### <a name="part-4-verify-the-vpn-connection"></a>Parte 4: Verificar a conexão de VPN

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

Se desejar obter mais informações sobre redes virtuais, consulte a página de [Documentação de rede Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) .
