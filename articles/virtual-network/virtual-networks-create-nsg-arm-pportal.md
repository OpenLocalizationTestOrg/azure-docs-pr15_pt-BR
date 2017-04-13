<properties 
   pageTitle="Como criar NSGs no modo ARM usando o portal do Azure | Microsoft Azure"
   description="Aprenda a criar e implantar NSGs no BRAÇO usando o portal do Azure"
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

# <a name="how-to-manage-nsgs-using-the-azure-portal"></a>Como gerenciar NSGs usando o portal do Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação do Gerenciador de recursos. Você também pode [criar NSGs no modelo clássico de implantação](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

O exemplo PowerShell comandos abaixo esperam um ambiente simples que já criado baseado no cenário acima. Se você quiser executar os comandos conforme eles são exibidos neste documento, primeiro criar o ambiente de teste implantando [Este modelo](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), clique em **implantar no Azure**, substitua os valores de parâmetro padrão, se necessário e siga as instruções no portal. As etapas abaixo usam **RG-NSG** como o nome do grupo de recursos que o modelo foi implantado.

## <a name="create-the-nsg-frontend-nsg"></a>Criar a NSG NSG FrontEnd

Para criar o **FrontEnd NSG** NSG, conforme mostrado no cenário acima, siga as etapas abaixo.

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **Procurar >** > **grupos de segurança de rede**.

    ![Portal Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)

3. Na lâmina **grupos de segurança de rede** , clique em **Adicionar**.
  
    ![Portal Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)

4. Na lâmina **Criar grupo de segurança de rede** , crie um NSG chamado *NSG FrontEnd* no grupo de recursos *RG-NSG* e clique em **criar**.

    ![Portal Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Criar regras em uma NSG existente

Para criar regras em uma NSG existente do portal do Azure, siga as etapas abaixo.

2. Clique em **Procurar >** > **grupos de segurança de rede**.

3. Na lista de NSGs, clique em **NSG FrontEnd** > **regras de segurança de entrada**

    ![Portal Azure - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. Na lista de **regras de segurança de entrada**, clique em **Adicionar**.

    ![Portal Azure - Adicionar regra](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. Na lâmina **Adicionar regra de segurança de entrada** , crie uma regra chamada *regra na web* com prioridade de *200* permitiu acesso via *TCP* porta *80* para qualquer máquina virtual de qualquer fonte e clique em **Okey**. Observe que a maioria dessas configurações são valores padrão já.

    ![Portal Azure - configurações de regra](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. Após alguns segundos, você verá a nova regra na NSG.

    ![Portal Azure - nova regra](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

7. Repita as etapas de 6 para criar uma regra de entrada denominada *rdp-regra* com uma prioridade de *250* permitir acesso via *TCP* a porta *3389* para qualquer máquina virtual de qualquer origem.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Associar o NSG à sub-rede FrontEnd

1. Clique em **Procurar >** > **grupos de recursos** > **RG-NSG**.
2. Na lâmina **RG-NSG** , clique em **…**  >  **TestVNet**.

    ![Portal Azure - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)

3. Na lâmina **configurações** , clique em **sub-redes** > **FrontEnd** > **grupo de segurança de rede** > **NSG-FrontEnd**.

    ![Portal Azure - configurações de sub-rede](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)

4. Na lâmina **FrontEnd** , clique em **Salvar**.

    ![Portal Azure - configurações de sub-rede](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Criar a NSG NSG-back-end

Para criar o **Back-end NSG** NSG e associá-lo à sub-rede **back-end** , siga as etapas abaixo.

1. Repita as etapas em [criar a NSG FrontEnd NSG](#Create-the-NSG-FrontEnd-NSG) para criar um NSG chamado *NSG-back-end*
2. Repita as etapas em [criar regras em uma NSG existente](#Create-rules-in-an-existing-NSG) para criar as regras de **entrada** na tabela abaixo.

  	|Regra de entrada|Regra de saída|
  	|---|---|
  	|![Portal Azure - regra de entrada](./media/virtual-networks-create-nsg-arm-pportal/figure17.png)|![Portal Azure - regra de saída](./media/virtual-networks-create-nsg-arm-pportal/figure18.png)|

3. Repita as etapas em [associar o NSG à sub-rede FrontEnd](#Associate-the-NSG-to-the-FrontEnd-subnet) associar o **Back-end NSG** NSG à sub-rede **back-end** .

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar NSGs existentes](virtual-network-manage-nsg-arm-portal.md)
- [Habilitar o log](virtual-network-nsg-manage-log.md) para NSGs.