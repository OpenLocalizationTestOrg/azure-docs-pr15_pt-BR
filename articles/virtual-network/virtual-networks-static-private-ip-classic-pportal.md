<properties 
   pageTitle="Como definir um IP particular estático no modo clássico no portal do Azure | Microsoft Azure"
   description="Compreendendo estático IPs particular e como gerenciá-los no modo clássico usando o portal do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-the-azure-portal"></a>Como definir um endereço IP privado estático (clássico) no portal do Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação clássico. Você também pode [Gerenciar um endereço IP privado estático no modelo de implantação do Gerenciador de recursos](virtual-networks-static-private-ip-arm-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

As etapas de exemplo abaixo esperam um ambiente simples que já criado. Se você quiser executar as etapas conforme eles são exibidos neste documento, primeiro crie o ambiente de teste descrito em [criar um vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP privado estático ao criar uma máquina virtual
Para criar uma máquina virtual chamada *DNS01* na sub-rede *FrontEnd* de um VNet chamado *TestVNet* com um IP estático particular de *192.168.1.101*, siga as etapas abaixo:

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **novo** > **Calcular** > **O Centro de dados do Windows Server 2012 R2**, observe que a lista **Selecionar um modelo de implantação** já mostra **clássico**e clique em **criar**.

    ![Criar máquina virtual no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)

3. Na **Máquina virtual criar** lâmina, insira o nome da máquina virtual seja criado (*DNS01* em nosso cenário), a conta de administrador local e senha.

    ![Criar máquina virtual no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)

4. Clique em **Configuração opcional** > **rede** > **Rede Virtual**e clique em **TestVNet**. Se **TestVNet** não estiver disponível, verifique se você estiver usando o local *Central EUA* e criou o ambiente de teste descrito no início deste artigo.

    ![Criar máquina virtual no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)

5. Na lâmina **rede** , certificar-se de que a sub-rede atualmente selecionada é *FrontEnd*, e em seguida, clique em **endereços IP**, em **atribuição de endereço IP** , clique em **estático**e digite *192.168.1.101* para o **Endereço IP** conforme mostrado abaixo.

    ![Criar máquina virtual no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)   

6. Clique **Okey** na lâmina **endereços IP** , clique **Okey** na **rede** lâmina e clique em **Okey** na lâmina **config opcional** .
7. Na **Máquina virtual criar** lâmina, clique em **criar**. Observe o bloco abaixo exibido no seu painel.

    ![Criar máquina virtual no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como recuperar informações de endereço IP particulares estático para uma máquina virtual

Para exibir as informações de endereço IP particulares estático para a máquina virtual criada com as etapas acima, execute as etapas abaixo.

1. No portal do Azure Azure, clique em **Procurar tudo** > **máquinas virtuais (clássico)** > **DNS01** > **todas as configurações** > **endereços IP** e observe a atribuição de endereço IP e endereço IP conforme mostrado abaixo.

    ![Criar máquina virtual no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático de uma máquina virtual
Para remover o endereço IP privado estático da máquina virtual criada acima, siga as etapas abaixo.
    
1. Da lâmina **endereços IP** mostrada acima, clique **dinâmicos** à direita da **atribuição de endereço IP**, e em seguida, clique em **Salvar**e, em seguida, clique em **Sim**.

    ![Criar máquina virtual no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático para uma máquina virtual existente
Para adicionar um endereço IP privado estático para a máquina virtual criada usando as etapas acima, siga as etapas abaixo:

1. Da lâmina **endereços IP** mostrada acima, clique em **estático** à direita da **atribuição de endereço IP**.
2. Digite *192.168.1.101* para o **endereço IP**, e em seguida, clique em **Salvar**e, em seguida, clique em **Sim**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre endereços [IP públicos reservado](virtual-networks-reserved-public-ip.md) .
- Saiba mais sobre endereços de [nível de instância pública IP (ILPIP)](virtual-networks-instance-level-public-ip.md) .
- Consulte as [APIs REST de IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx).