<properties 
   pageTitle="Implantar uma máquina virtual com IP estático público usando o portal de Azure no Gerenciador de recursos | Microsoft Azure"
   description="Saiba como implantar VMs com IP estático público usando o portal de zure no Gerenciador de recursos"
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
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-the-azure-portal"></a>Implantar uma máquina virtual com IP estático público usando o portal do Azure

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Criar uma máquina virtual com IP estático público 

Para criar uma máquina virtual com um endereço IP público estático no portal do Azure, siga as etapas abaixo.

1. Em um navegador, navegue até o [portal do Azure](https://portal.azure.com) e, se necessário, entre com sua conta do Azure.
2. No canto superior esquerdo do portal, clique em **novo**>>**Calcular**>**O Centro de dados do Windows Server 2012 R2**.
3. Na lista **Selecionar um modelo de implantação** , selecione **Gerenciador de recursos** e clique em **criar**.
4. Na lâmina **Noções básicas** , insira as informações de máquina virtual, conforme mostrado abaixo e clique em **Okey**.

    ![Portal Azure - Noções básicas](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. Na lâmina **Escolher um tamanho** , clique em **A1 padrão** , conforme mostrado abaixo e, em seguida, clique em **Selecionar**.

    ![Portal Azure - escolher um tamanho](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. Na lâmina **configurações** , clique em **endereço IP público**, na lâmina **endereço IP público de criar** , em **atribuição**, clique **estático** conforme mostrado abaixo. E clique em **Okey**.

    ![Portal Azure - criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. Na lâmina **configurações** , clique em **Okey**.
8. Examine a lâmina de **Resumo** , conforme mostrado abaixo e clique em **Okey**.

    ![Portal Azure - criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. Observe que o novo bloco no seu painel.

    ![Portal Azure - criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. Assim que a máquina virtual for criada, a lâmina **configurações** será exibida conforme mostrado abaixo

    ![Portal Azure - criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)