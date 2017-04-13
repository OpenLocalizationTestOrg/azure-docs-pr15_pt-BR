<properties 
   pageTitle="Como definir um IP particular estático no modo ARM usando o portal do Azure | Microsoft Azure"
   description="Compreendendo IPs particular (queda) e como gerenciá-los no modo ARM usando o portal do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-in-the-azure-portal"></a>Como definir um endereço IP privado estático no portal do Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação do Gerenciador de recursos. Você também pode [Gerenciar o endereço IP privado estático no modelo clássico de implantação](virtual-networks-static-private-ip-classic-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

As etapas de exemplo abaixo esperam um ambiente simples que já criado. Se você quiser executar as etapas conforme eles são exibidos neste documento, primeiro crie o ambiente de teste descrito em [criar um vnet](virtual-networks-create-vnet-arm-pportal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Como criar uma máquina virtual para teste estáticos endereços IP privados

Você não pode definir um endereço IP privado estático durante a criação de uma máquina virtual no modo de implantação do Gerenciador de recursos usando o portal do Azure. Você deve criar a máquina virtual primeiro, tehn definir seu IP particular seja estático.

Para criar uma máquina virtual chamada *DNS01* na sub-rede *FrontEnd* de um VNet chamado *TestVNet*, siga as etapas abaixo.

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **novo** > **Calcular** > **O Centro de dados do Windows Server 2012 R2**, observe que a lista **Selecionar um modelo de implantação** já mostra o **Gerenciador de recursos**e clique em **criar**, conforme mostrado na figura abaixo.

    ![Criar máquina virtual no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)

3. Na lâmina **Noções básicas** , insira o nome da máquina virtual seja criado (*DNS01* em nosso cenário), a conta administrador local e a senha, conforme mostrado na figura abaixo.

    ![Blade de Noções básicas](./media/virtual-networks-static-ip-arm-pportal/figure02.png)

4. Certificar-se de que o **local** selecionado é *Centro dos EUA*, e em seguida, clique em **Selecionar existente** em **grupo de recursos**, e em seguida, clique novamente o **grupo de recursos** e em seguida, clique *TestRG*e clique em **Okey**.

    ![Blade de Noções básicas](./media/virtual-networks-static-ip-arm-pportal/figure03.png)

5. Na lâmina **Escolher um tamanho** , selecione **A1 padrão**e clique em **Selecionar**.

    ![Escolha um blade de tamanho](./media/virtual-networks-static-ip-arm-pportal/figure04.png) 

6. Na lâmina **configurações** , verifique se as seguintes propriedades estão definidas são definidas com os valores abaixo e clique em **Okey**.

    -**Conta de armazenamento**: *vnetstorage*
    - **Rede**: *TestVNet*
    - **Sub-rede**: *FrontEnd*

    ![Escolha um blade de tamanho](./media/virtual-networks-static-ip-arm-pportal/figure05.png)  

7. Na lâmina **Resumo** , clique em **Okey**. Observe o bloco abaixo exibido no seu painel.

    ![Criar máquina virtual no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como recuperar informações de endereço IP particulares estático para uma máquina virtual

Para exibir as informações de endereço IP particulares estático para a máquina virtual criada com as etapas acima, execute as etapas abaixo.

1. No portal do Azure Azure, clique em **Procurar tudo** > **máquinas virtuais** > **DNS01** > **todas as configurações** > **interfaces de rede** e em seguida, clique na interface de rede somente listados.

    ![Implantando o bloco de máquina virtual](./media/virtual-networks-static-ip-arm-pportal/figure07.png)

2. Na lâmina **interface de rede** , clique em **todas as configurações** > **endereços IP** e observe os valores de **atribuição** e o **endereço IP** .

    ![Implantando o bloco de máquina virtual](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático para uma máquina virtual existente
Para adicionar um endereço IP privado estático para a máquina virtual criada usando as etapas acima, siga as etapas abaixo:

1. Da lâmina **endereços IP** mostrada acima, clique em **estática** em **atribuição**.
2. Digite *192.168.1.101* endereço **IP**e clique em **Salvar**.

    ![Criar máquina virtual no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

>[AZURE.NOTE] Se depois de clicar em **Salvar** , você perceber que a atribuição ainda está definida para **dinâmico**, significa que o endereço IP digitado já está em uso. Tente um endereço IP diferente.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático de uma máquina virtual
Para remover o endereço IP privado estático da máquina virtual criada acima, siga a etapa abaixo.
    
1. Da lâmina **endereços IP** mostrada acima, clique em **dinâmico** em **atribuição**e clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre endereços [IP públicos reservado](virtual-networks-reserved-public-ip.md) .
- Saiba mais sobre endereços de [nível de instância pública IP (ILPIP)](virtual-networks-instance-level-public-ip.md) .
- Consulte as [APIs REST de IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx).