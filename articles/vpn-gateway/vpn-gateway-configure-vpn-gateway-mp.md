<properties 
   pageTitle="Configure um Gateway VPN no Portal de clássico do Azure | Microsoft Azure"
   description="Este artigo conduz você por meio de configuração da sua rede virtual gateway VPN e alterar um tipo de roteamento de VPN de gateway."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/11/2016"
   ms.author="cherylmc" />

# <a name="configure-a-vpn-gateway-for-the-classic-deployment-model"></a>Configure um gateway VPN para o modelo clássico de implantação


Se você quiser criar uma conexão segura entre locais entre Azure e seu local na, você precisa configurar uma conexão de gateway VPN. No modelo clássico de implantação, um gateway pode ser um dos dois tipos de roteamento de VPN: estática ou dinâmica. O tipo escolhido depende de seu plano de design de rede, tanto o dispositivo VPN no local que você deseja usar. 

Por exemplo, algumas opções de conectividade, como uma conexão ponto-a-site, exigem um gateway de roteamento dinâmico. Se você quiser configurar seu gateway para oferecer suporte a conexões ponto-a-site (P2S) e uma conexão de (S2S)-to-site, você precisa configurar um gateway de roteamento dinâmico apesar-to-site pode ser configurado com qualquer tipo de roteamento de VPN de gateway. 

Além disso, deve garantir que o dispositivo que você deseja usar para sua conexão suporta o tipo de roteamento de VPN que você deseja criar. Consulte [sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md).


**Sobre este artigo** 

Este artigo foi escrito para o modelo de implantação clássico usando o [portal clássico](https://manage.windowsazure.com) (não o portal Azure). 

**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-overview"></a>Visão geral da configuração

As etapas a seguir irão orientá-lo a configurar o gateway VPN no portal de clássico do Azure. Estas etapas se aplicam ao gateways para redes virtuais que foram criados usando o modelo clássico de implantação. Atualmente, não de todas as definições de configuração para gateways estão disponíveis no portal do Azure. Quando estiverem, vamos criar um novo conjunto de instruções que se aplicam ao portal do Azure.


1. [Criar um gateway VPN para seu VNet](#create-a-vpn-gateway)

1. [Reunir informações para sua configuração de dispositivo VPN](#gather-information-for-your-vpn-device-configuration)

1. [Configurar seu dispositivo VPN](#configure-your-vpn-device)

1. [Verificar seu intervalos de rede local e o endereço IP do gateway VPN](#verify-your-local-network-ranges-and-vpn-gateway-ip-address)

### <a name="before-you-begin"></a>Antes de começar

Antes de configurar seu gateway, primeiro é necessário criar sua rede virtual. Para obter etapas criar uma rede virtual para conectividade entre locais, consulte [Configurar uma rede virtual com uma conexão de VPN - to-site](vpn-gateway-site-to-site-create.md)ou [Configurar uma rede virtual com uma conexão de VPN ponto-a-site](vpn-gateway-point-to-site-create.md). Em seguida, use as seguintes etapas para configurar o gateway VPN e coletar as informações necessárias configurar seu dispositivo VPN. 

Se você já tiver um gateway VPN e você deseja alterar o tipo de roteamento de VPN, veja [como alterar o tipo de roteamento de VPN para o gateway](#how-to-change-the-vpn-routing-type-for-your-gateway).

## <a name="create-a-vpn-gateway"></a>Criar um gateway VPN

1. No [portal de clássico Azure](https://manage.windowsazure.com), na página de **redes** , verifique se a coluna de status para sua rede virtual **criado**.

1. Na coluna **nome** , clique no nome da sua rede virtual.

1. Na página do **Dashboard** , observe que este VNet não tem um gateway configurado ainda. Você verá esse status periodicamente as etapas para configurar seu gateway.

![Gateway não criado](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)


Em seguida, na parte inferior da página, clique em **Criar o Gateway**. Você pode selecionar *Roteamento estático* ou *Roteamento dinâmico*. O tipo de roteamento de VPN selecionado depende de alguns fatores. Por exemplo, o que seu dispositivo VPN oferece suporte e se você precisa oferecer suporte a conexões ponto-a-site. Verifique [Os dispositivos sobre VPN para conectividade de rede Virtual](vpn-gateway-about-vpn-devices.md) para verificar o tipo de roteamento de VPN que você precisa. Depois que o gateway foi criado, você não pode alterar entre tipos de roteamento do gateway VPN sem excluir e recriar o gateway. Quando o sistema solicita que você confirme se deseja que o gateway criado, clique em **Sim**.

![Tipo de roteamento de VPN de gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

Quando seu gateway está criando, observe o gráfico de gateway na página altera para amarelo e diz *Criando Gateway*. Ele pode levar até 45 minutos para o gateway criar. Aguarde até que o gateway seja concluído antes de você pode avançar com outras configurações.

![Criação de gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

Quando o gateway muda para *conectar-se*, você pode coletar as informações que necessárias para o seu dispositivo VPN.

![Conexão de gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## <a name="gather-information-for-your-vpn-device-configuration"></a>Reunir informações para sua configuração de dispositivo VPN

Depois que o gateway foi criado, reúna as informações para a sua configuração de dispositivo VPN. Essas informações estão localizadas na página do **painel** para sua rede virtual:

1. **Endereço IP de gateway-** O endereço IP pode ser encontrado na página do **painel** . Você não será capaz de vê-las até depois que seu gateway acabou de criar.

1. **Chave compartilhada-** Clique em **Gerenciar chave** na parte inferior da tela. Clique no ícone ao lado da tecla para copiá-lo para sua área de transferência, cole e salve a chave. Esse botão só funciona quando há um único túnel VPN S2S. Se você tiver vários encapsulamentos VPN S2S, use a *Obter Virtual rede Gateway chave compartilhada* API ou o cmdlet do PowerShell.

![Gerenciar chave](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)


## <a name="configure-your-vpn-device"></a>Configurar seu dispositivo VPN

Depois de concluir as etapas anteriores, você ou o administrador de rede precisará configurar o dispositivo VPN para criar a conexão. Consulte [Sobre dispositivos de VPN para conectividade de rede Virtual](vpn-gateway-about-vpn-devices.md) para obter mais informações sobre dispositivos VPN.

Após o dispositivo VPN tiver sido configurado, você pode exibir suas informações de conexão atualizado na página do painel para sua VNet.

Você também pode executar um dos seguintes comandos para testar sua conexão:

|                      | Cisco ASA             | Cisco ISR/recuperação automatizada do sistema         | Juniper SSG/ISG | Juniper SRX/J                            |
|----------------------|-----------------------|-----------------------|-----------------|------------------------------------------|
| **Verificar a segurança de modo principal**  | Mostrar Cripto isakmp sa | Mostrar Cripto isakmp sa | obter cookie ike  | Mostrar segurança associação de segurança ike   |
| **Verificar a segurança de modo rápido** | Mostrar criptografia ipsec sa  | Mostrar criptografia ipsec sa  | obter sa          | Mostrar associação de segurança de ipsec de segurança |


## <a name="verify-your-local-network-ranges-and-vpn-gateway-ip-address"></a>Verificar seu intervalos de rede local e o endereço IP do gateway VPN

### <a name="verify-your-vpn-gateway-ip-address"></a>Verifique se seu endereço IP do gateway VPN

Para gateway conectar-se corretamente, o endereço IP do seu dispositivo VPN deve ser configurado corretamente para a rede Local que você especificou para a sua configuração entre locais. Normalmente, isso é configurado durante o processo de configuração de-to-site. No entanto, se você usava anteriormente dessa rede local com um dispositivo diferente ou o endereço IP foi alterado para esta rede local, edite as configurações para especificar o endereço IP de Gateway correto.

1. Para verificar seu endereço IP do gateway, clique em **redes** no painel esquerdo portal e selecione **Redes locais** na parte superior da página. Você verá o endereço de Gateway VPN para cada rede local que você criou. Para editar o endereço IP, selecione a VNet e clique em **Editar** na parte inferior da página.

1. Na página **especificar os detalhes da rede local** , edite o endereço IP e, em seguida, clique na seta próxima na parte inferior da página.

1. Na página **especificar o espaço de endereço** , clique na marca de seleção na parte inferior direita para salvar suas configurações.

### <a name="verify-the-address-ranges-for-your-local-networks"></a>Verifique se os intervalos de endereços para suas redes locais

Para o tráfego correto flua através do gateway em seu local de locais, você precisa verificar se cada intervalo de endereços IP está especificado. Cada intervalo deve estar listado em sua configuração do Azure **Redes locais** . Dependendo da configuração de rede do seu local de locais, isso pode ser uma tarefa relativamente grande. Tráfego vinculado para um endereço IP que está contido em intervalos listados será enviado por meio do gateway VPN rede virtual. Os intervalos de lista que você não precisa ser intervalos particulares, embora você desejará verificar se sua configuração de local pode receber o tráfego de entrada.

Para adicionar ou editar os intervalos para uma rede Local, use as etapas a seguir.

1. Para editar os intervalos de endereços IP para uma rede local, clique em **redes** no painel esquerdo portal e selecione **Redes locais** na parte superior da página. No portal do, a maneira mais fácil para exibir os intervalos listados é na página **Editar** . Para ver seus intervalos, selecione a VNet e clique em **Editar** na parte inferior da página.

1. Na página **especificar os detalhes do seu local de rede** , não faça as alterações. Clique na seta próxima na parte inferior da página.

1. Na página **especificar o espaço de endereço** , faça as alterações de espaço de endereço de rede. Clique na marca de seleção para salvar sua configuração.

## <a name="how-to-view-gateway-traffic"></a>Como exibir o tráfego de gateway

Você pode exibir seu gateway e o tráfego de gateway na sua página de rede Virtual **Dashboard** .

Na página do **painel** , você pode exibir o seguinte:

- A quantidade de dados que passam por meio de seu gateway, tanto dados e check-out.

- Os nomes dos servidores DNS que são especificados para a sua rede virtual.

- A conexão entre o gateway e seu dispositivo VPN.

- A chave compartilhada que é usada para configurar sua conexão de gateway para o seu dispositivo VPN.


## <a name="how-to-change-the-vpn-routing-type-for-your-gateway"></a>Como alterar o tipo de roteamento de VPN para seu gateway

Porque algumas configurações de conectividade estão disponíveis apenas para determinados tipos de roteamento de gateway, você descobrirá que você precisa alterar o tipo de roteamento de VPN de um gateway VPN existente do gateway. Por exemplo, você talvez queira adicionar conectividade ponto-a-site a uma conexão to-site já existente que tenha um gateway estático. Conexões de ponto-a-site exigem um gateway dinâmico. Isso significa para configurar uma conexão de P2S, você precisa alterar seu tipo de roteamento de VPN de gateway de estático para dinâmico.

Se você precisar alterar um tipo de roteamento de VPN de gateway, você deverá excluir o gateway existente e, em seguida, criar um novo gateway com o novo tipo de roteamento. Você não precisa excluir toda a rede virtual para alterar o tipo de roteamento de gateway.

Antes de alterar seu tipo de roteamento de VPN de gateway, certifique-se de verificar se o seu dispositivo VPN suporta o tipo de roteamento que você deseja usar. Para baixar novos exemplos de configuração de roteamento e verifique os requisitos de dispositivo VPN, consulte [Sobre dispositivos de VPN para conectividade de rede Virtual](vpn-gateway-about-vpn-devices.md).

>[AZURE.IMPORTANT] Quando você exclui um gateway VPN rede virtual, o VIP atribuído ao gateway é lançado. Quando você recriar o gateway, um novo VIP é atribuído a ele.

1. **Exclua o gateway VPN existente.**

    Na página **Dashboard** para sua rede virtual, navegue até a parte inferior da página e clique em **Excluir o Gateway**. Aguarde a notificação de que o gateway foi excluído. Quando você recebe a notificação na tela que seu gateway foi excluído, você pode criar um novo gateway.

1. **Crie um novo gateway VPN.**

    Use o procedimento na parte superior da página para criar um novo gateway: [criar um gateway VPN](#create-a-vpn-gateway).


## <a name="next-steps"></a>Próximas etapas

Você pode adicionar máquinas virtuais à sua rede virtual. Veja [como criar uma máquina virtual personalizada](../virtual-machines/virtual-machines-windows-classic-createportal.md).

Se você quiser configurar uma conexão de VPN ponto-a-site, consulte [Configurar uma conexão de VPN ponto-a-site](vpn-gateway-point-to-site-create.md).

 
