<properties 
   pageTitle="Gerenciar NSGs usando o portal de visualização no Gerenciador de recursos | Microsoft Azure"
   description="Saiba como gerenciar existente NSGs usando o portal de visualização no Gerenciador de recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-the-preview-portal"></a>Gerenciar NSGs usando o portal de visualização

> [AZURE.SELECTOR]
- [Portal](virtual-network-manage-nsg-arm-portal.md)
- [PowerShell](virtual-network-manage-nsg-arm-ps.md)
- [CLI Azure](virtual-network-manage-nsg-arm-cli.md)

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>Recuperar informações

Você pode exibir seu NSGs existentes, recuperar regras para um NSG existente e descobrir quais recursos um NSG está associado a.

### <a name="view-existing-nsgs"></a>Modo de exibição existentes NSGs
Para exibir todos os NSGs existentes em uma assinatura, siga as etapas abaixo.

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **Procurar >** > **grupos de segurança de rede**.

![Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Verifique a lista de NSGs na lâmina **grupos de segurança de rede** .

![Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

Para exibir a lista de NSGs no grupo de recursos **RG-NSG** , siga as etapas abaixo. 

1. Clique em **grupos de recursos >** > **RG-NSG** > **…**.

![Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. Na lista de recursos, procure por itens exibindo o ícone NSG, conforme mostrado na lâmina **recursos** abaixo.

![Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure4.png)
         
### <a name="list-all-rules-for-an-nsg"></a>Lista todas as regras para um NSG

Para exibir as regras de um NSG chamado **NSG FrontEnd**, siga as etapas abaixo. 

1. A lâmina de **grupos de segurança de rede** ou a lâmina de **recursos** mostrada acima, clique em **NSG FrontEnd**.
2. Na guia **configurações** , clique em **regras de segurança de entrada**.

![Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. A lâmina de **regras de segurança de entrada** é exibida conforme mostrado abaixo.

![Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. Na guia **configurações** , clique em **regras de segurança de saída** para ver as regras de saída.

>[AZURE.NOTE] Para exibir regras padrão, clique no ícone de **regras padrão** na parte superior da lâmina que exibe as regras.

### <a name="view-nsgs-associations"></a>Exibir associações de NSGs

Para ver quais recursos que o NSG **NSG FrontEnd** é associar, siga as etapas abaixo.

1. A lâmina de **grupos de segurança de rede** ou a lâmina de **recursos** mostrada acima, clique em **NSG FrontEnd**.
2. Na guia **configurações** , clique em **sub-redes** para ver quais sub-redes são associados a NSG.

![Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. Na guia **configurações** , clique em **interfaces de rede** para ver quais NICs são associadas a NSG.

## <a name="manage-rules"></a>Gerenciar regras

Você pode adicionar regras para um NSG existente, editar regras existentes e remover regras.

### <a name="add-a-rule"></a>Adicionar uma regra

Para adicionar uma regra permitindo tráfego de **entrada** à porta **443** de qualquer máquina para **FrontEnd NSG** NSG, siga as etapas abaixo.

1. A lâmina de **grupos de segurança de rede** ou a lâmina de **recursos** mostrada acima, clique em **NSG FrontEnd**.
2. Na guia **configurações** , clique em **regras de segurança de entrada**.
3. Na lâmina **regras de segurança de entrada** , clique em **Adicionar**. Em seguida, na lâmina **Adicionar regra de segurança de entrada** , preencher os valores conforme mostrado abaixo e clique em **Okey**.

![Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

4. Após alguns segundos, observe que a nova regra na lâmina **regras de segurança de entrada** .

![Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>Alterar uma regra

Para alterar a regra criada acima para permitir o tráfego de entrada de apenas à **Internet** , siga as etapas abaixo.

1. A lâmina de **grupos de segurança de rede** ou a lâmina de **recursos** mostrada acima, clique em **NSG FrontEnd**.
2. Na guia **configurações** , clique na regra criada acima.
3. Na lâmina **Permitir-https** , altere a propriedade **fonte** conforme mostrado abaixo e, em seguida, clique em **Salvar**.

![Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>Excluir uma regra

Para excluir a regra criada acima, siga as etapas abaixo.

1. A lâmina de **grupos de segurança de rede** ou a lâmina de **recursos** mostrada acima, clique em **NSG FrontEnd**.
2. Na guia **configurações** , clique na regra criada acima.
3. Na lâmina **https permitir** , clique em **Excluir**e, em seguida, clique em **Sim**.

![Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>Gerenciar associações

Você pode associar um NSG para sub-redes e placas de rede. Você também pode dissociar um NSG de qualquer recurso que ele está associado.

### <a name="associate-an-nsg-to-a-nic"></a>Associar um NSG a uma NIC

Para associar o **FrontEnd NSG** NSG para **TestNICWeb1** NIC, siga as etapas abaixo.

1. A lâmina de **grupos de segurança de rede** ou a lâmina de **recursos** mostrada acima, clique em **NSG FrontEnd**.
2. Na guia **configurações** , clique em **interfaces de rede** > **associar** > **TestNICWeb1**.

![Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>Dissociar um NSG de uma NIC

Para dissociar **FrontEnd NSG** NSG de **TestNICWeb1** NIC, siga as etapas abaixo.

1. No portal do Azure, clique em **grupos de recursos >** > **RG-NSG** > **…**  >  **TestNICWeb1**.
2. Na lâmina **TestNICWeb1** , clique em **alterar segurança …**  > **None**.

![Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

>[AZURE.NOTE] Você também pode usar este blade associar a NIC qualquer NSG existente.

### <a name="dissociate-an-nsg-from-a-subnet"></a>Dissociar um NSG de uma sub-rede

Para dissociar **FrontEnd NSG** NSG da sub-rede **FrontEnd** , siga as etapas abaixo.

1. No portal do Azure, clique em **grupos de recursos >** > **RG-NSG** > **…**  >  **TestVNet**.
2. Na lâmina **configurações** , clique em **sub-redes** > **FrontEnd** > **grupo de segurança de rede** > **Nenhum**.

![Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. Na lâmina **FrontEnd** , clique em **Salvar**.

![Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>Associar um NSG a uma sub-rede

Para associar o **FrontEnd NSG** NSG à sub-rede **FronEnd** novamente, siga as etapas abaixo.

1. No portal do Azure, clique em **grupos de recursos >** > **RG-NSG** > **…**  >  **TestVNet**.
2. Na lâmina **configurações** , clique em **sub-redes** > **FrontEnd** > **grupo de segurança de rede** > **NSG-FrontEnd**.
3. Na lâmina **FrontEnd** , clique em **Salvar**.

>[AZURE.NOTE] Você também pode associar um NSG a uma sub-rede da lâmina de **configurações** do thh NSG.

## <a name="delete-an-nsg"></a>Excluir um NSG

Você só pode excluir uma NSG se ele não tiver associado a qualquer recurso. Para excluir um NSG, siga as etapas abaixo.

1. No portal do Azure, clique em **grupos de recursos >** > **RG-NSG** > **…**  >  **NSG-FrontEnd**.
2. Na lâmina **configurações** , clique em **interfaces de rede**.
3. Se houver quaisquer NICs listadas, clique a NIC e siga a etapa 2 em [Dissociate um NSG de uma NIC](#Dissociate-an-NSG-from-a-NIC).
4. Repita a etapa 3 para cada NIC.
5. Na lâmina **configurações** , clique em **sub-redes**.
6. Se houver quaisquer sub-redes listados, clique na sub-rede e siga as etapas 2 e 3 em [Dissociate um NSG de uma sub-rede](#Dissociate-an-NSG-from-a-subnet).
7. Rola para a esquerda na lâmina **FrontEnd NSG** , clique em **Excluir** > **Sim**.

[Portal Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>Próximas etapas

- [Habilitar o log](virtual-network-nsg-manage-log.md) para NSGs.
