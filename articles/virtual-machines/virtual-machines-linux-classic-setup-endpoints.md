<properties
    pageTitle="Configurar pontos de extremidade em uma VM Linux clássico | Microsoft Azure"
    description="Aprenda a configurar pontos de extremidade de uma VM Linux no portal de clássico do Azure para permitir a comunicação com um computador virtual Linux no Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/13/2016"
    ms.author="cynthn"/>

# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Como configurar pontos de extremidade em uma máquina virtual clássico de Linux no Azure

Todas as máquinas virtuais Linux que você cria no Azure usando o modelo clássico de implantação automaticamente podem se comunicar através de um canal de rede privada com outras máquinas virtuais no mesmo serviço de nuvem ou rede virtual. No entanto, os computadores na Internet ou outras redes virtuais requerem pontos de extremidade para direcionar o tráfego de rede de entrada para uma máquina virtual. Este artigo também está disponível para [máquinas virtuais do Windows](virtual-machines-windows-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]No modelo de implantação do **Gerenciador de recursos** , os pontos de extremidade são configurados usando **Grupos de segurança de rede (NSGs)**. Para obter mais informações, consulte [abrindo portas e pontos de extremidade] (virtual-máquinas-linux-nsg-quickstart.md).

Quando você cria uma máquina virtual Linux no portal de clássico do Azure, um ponto de extremidade para SSH (Secure Shell) é geralmente criado automaticamente. Você pode configurar pontos de extremidade adicionais ao criar a máquina virtual ou, posteriormente, conforme necessário.
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Próximas etapas

* Você também pode criar um ponto de extremidade de máquina virtual usando a [Interface de linha de comando do Azure](../virtual-machines-command-line-tools.md). Execute o comando **criar de ponto de extremidade de máquina virtual azure** .

* Se você criou uma máquina virtual no modelo de implantação do Gerenciador de recursos, você pode usar a CLI Azure no modo do Gerenciador de recursos para [criar grupos de segurança de rede](../virtual-network/virtual-networks-create-nsg-arm-cli.md) para o tráfego de controle para a máquina virtual.