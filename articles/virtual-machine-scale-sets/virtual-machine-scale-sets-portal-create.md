<properties
    pageTitle="Criar um conjunto de escala de máquina Virtual usando o portal de Azure | Microsoft Azure"
    description="Implante conjuntos de escala usando o portal do Azure."
    keywords="conjuntos de escala de máquina virtual" 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="gatneil"/>

# <a name="create-a-virtual-machine-scale-set-using-the-azure-portal"></a>Criar um conjunto de escala de máquina Virtual usando o portal do Azure

Este tutorial mostra como é fácil criar um conjunto de escala de máquina Virtual em apenas alguns minutos, usando o portal do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Escolha a imagem de máquina virtual do Marketplace

No portal do, você pode facilmente implantar uma escala definida com CentOS, CoreOS, Debian, abrir Suse, Red Hat Enterprise Linux, SUSE Linux Enterprise Server, servidor de Ubuntu ou imagens do Windows Server.

Primeiro, navegue até o [portal do Azure](https://portal.azure.com) em um navegador da web. Clique em `New`, pesquise por `scale set`e selecione o `Virtual machine scale set` entrada:

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## <a name="create-the-linux-virtual-machine"></a>Criar o computador virtual Linux

Agora você pode usar as configurações padrão e criar rapidamente a máquina virtual.

* Sobre o `Basics` lâmina, insira um nome para o conjunto de escala. Esse nome se torna a base do FQDN do balanceador de carga na frente do conjunto de escala, portanto, verifique se que o nome é exclusivo em todas as do Azure.

* Selecione seu sistema operacional desejado digite, insira seu nome de usuário desejado e selecione o tipo de autenticação do qual você prefere. Se você escolher uma senha, ele deve ser pelo menos 12 caracteres de comprimento e atender aos três fora os seguintes requisitos de complexidade quatro: caractere de uma letras minúsculas, um maiusculas caractere, um número e um caractere especial. Veja mais detalhes sobre [os requisitos do usuário e senha](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm). Se você escolher `SSH public key`, esteja colar-se apenas em sua chave pública, não sua chave privada:

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* Insira seu nome de grupo do recurso desejado e o local e clique em `OK`.

* Sobre o `Virtual machine scale set service settings` blade: digite o rótulo de nome de domínio desejado (a base do FQDN para o balanceador de carga na frente do conjunto de escala). Este rótulo deve ser exclusivo em todos do Azure.

* Escolha sua imagem de disco do sistema operacional desejado, a contagem de instância e o tamanho de máquina.

* Habilitar ou desabilitar escala automática e configurar se habilitado:

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* Sobre o `Summary` lâmina, quando a validação estiver concluída, clique em `OK`.

* Por fim, na `Purchase` lâmina, clique em `Purchase` iniciar a escala definir implantação.

## <a name="connect-to-a-vm-in-the-scale-set"></a>Conectar a uma máquina virtual do conjunto de escala

Depois que seu conjunto de escala for implantado, navegue até o `Inbound NAT Rules` guia de Balanceador de carga para o conjunto de escala:

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

Você pode se conectar para cada máquina virtual na escala de conjunto usando essas regras NAT. Por exemplo, para um conjunto de escala do Windows, se houver uma regra NAT na porta de entrada 50000, você pode conectar a máquina via RDP em `<load-balancer-ip-address>:50000`. Para um conjunto de escala Linux, você deve se conectar usando o comando `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Próximas etapas

Para obter documentação sobre como implantar conjuntos de escala da CLI, consulte [esta documentação](./virtual-machine-scale-sets-cli-quick-create.md).

Para obter documentação sobre como implantar conjuntos de escala do PowerShell, consulte [esta documentação](./virtual-machine-scale-sets-windows-create.md).

Para obter documentação sobre como implantar conjuntos de escala do Visual Studio, consulte [esta documentação](./virtual-machine-scale-sets-vs-create.md).

Para documentação geral, confira a [página de visão geral de escala documentação define](./virtual-machine-scale-sets-overview.md).

Para obter informações gerais, confira a [página inicial principal conjuntos de escala](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

