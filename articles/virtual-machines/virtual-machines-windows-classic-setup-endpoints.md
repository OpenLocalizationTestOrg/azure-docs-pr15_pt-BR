<properties
    pageTitle="Configurar pontos de extremidade em uma VM clássico do Windows | Microsoft Azure"
    description="Aprenda a configurar pontos de extremidade para uma máquina de virtual do Windows no portal de clássico do Azure para permitir a comunicação com uma máquina virtual Windows no Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Como configurar pontos de extremidade em um computador de virtual clássico do Windows no Azure


Todas as janelas máquinas virtuais que você criar no Azure usando o modelo clássico de implantação pode automaticamente se comunicar através de uma privada canal de rede com outras máquinas virtuais no mesmo serviço de nuvem ou rede virtual. No entanto, os computadores na Internet ou outras redes virtuais requerem pontos de extremidade para direcionar o tráfego de rede de entrada para uma máquina virtual. Este artigo também está disponível para [máquinas virtuais Linux](virtual-machines-linux-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]No modelo de implantação do **Gerenciador de recursos** , os pontos de extremidade são configurados usando **Grupos de segurança de rede (NSGs)**. Para obter mais informações, consulte [permitir o acesso externo para sua máquina virtual usando o Portal do Azure] (virtual-machines-windows-nsg-quickstart-portal.md).

Quando você cria uma máquina virtual Windows no portal de clássico do Azure, pontos de extremidade comuns, como aqueles para área de trabalho remota e o Windows PowerShell remoto normalmente são criados para você automaticamente. Você pode configurar pontos de extremidade adicionais ao criar a máquina virtual ou, posteriormente, conforme necessário.



[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Próximas etapas

* Para usar um cmdlet do PowerShell do Azure para configurar um ponto de extremidade de máquina virtual, consulte [Adicionar AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

* Para usar um cmdlet do PowerShell do Azure para gerenciar uma ACL em uma empresa, consulte [Gerenciando listas de controle acesso (ACLs) para pontos de extremidade usando o PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

* Se você criou uma máquina virtual no modelo de implantação do Gerenciador de recursos, você pode usar o PowerShell do Azure para [criar grupos de segurança de rede](../virtual-network/virtual-networks-create-nsg-arm-ps.md) para o tráfego de controle para a máquina virtual.
