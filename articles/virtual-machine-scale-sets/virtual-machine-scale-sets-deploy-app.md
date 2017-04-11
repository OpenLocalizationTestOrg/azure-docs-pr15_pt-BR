<properties
    pageTitle="Implantar um aplicativo em conjuntos de escala de máquina Virtual | Microsoft Azure"
    description="Implantar um aplicativo em conjuntos de escala de máquina Virtual"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="guybo"/>

# <a name="deploy-an-app-on-virtual-machine-scale-sets"></a>Implantar um aplicativo em conjuntos de escala de máquina Virtual

Um aplicativo executado em um conjunto de escala de máquina virtual geralmente é implantado em uma das três maneiras:

- Instalar o novo software em uma imagem de plataforma no momento da implantação. Uma imagem de plataforma neste contexto é uma imagem de sistema operacional do Azure Marketplace, como o Ubuntu 16.04, Windows Server 2012 R2, etc.

Você pode instalar o software novo em uma imagem de plataforma usando uma [Extensão de máquina virtual](../virtual-machines/virtual-machines-windows-extensions-features.md). Uma extensão de máquina virtual é um software que é executada quando uma máquina virtual é implantada. Você pode executar qualquer código que você deseja no momento da implantação usando uma extensão de script personalizado. [Aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) está um exemplo de modelo de Gerenciador de recursos do Azure com duas extensões de máquina virtual: uma extensão de Script personalizado Linux para instalar o Apache e PHP e uma extensão de diagnóstico para emitir usados pelo escala Azure automática de dados de desempenho.

Uma vantagem dessa abordagem é que você tem um nível de separação entre o código de aplicativo e o sistema operacional e pode manter seu aplicativo separadamente. É claro que significa que há também é mais partes móveis e tempo de implantação de máquina virtual pode ser mais se houver muito para o script baixar e configurar.

**Se você passar informações confidenciais em seu comando de extensão de Script personalizado (como uma senha), certifique-se de especificar o `commandToExecute` no `protectedSettings` atributo da extensão de Script personalizado em vez da `settings` atributo.**

- Crie uma imagem de máquina virtual personalizada que inclui o sistema operacional e o aplicativo em um único VHD. Veja o conjunto de escala consiste em um conjunto de VMs copiada de uma imagem criada por você, que deseja manter. Essa abordagem não requer nenhuma configuração extra em tempo de implantação de máquina virtual. No entanto, o `2016-03-30` versão dos conjuntos de escala de máquina virtual (e versões anteriores), os discos do sistema operacional para as VMs no conjunto de escala limitam-se a uma conta de armazenamento único. Assim, você pode ter um máximo de 40 VMs em um conjunto de escala, em vez da máquina virtual 100 por escala Definir limite com imagens de plataforma. Consulte [Visão geral do projeto definir escala](./virtual-machine-scale-sets-design-overview.md) para obter mais detalhes.

- Implantar uma plataforma ou uma imagem personalizada que é basicamente um host de contêiner e instale o aplicativo como um ou mais contêineres que você gerenciar com uma ferramenta de gerenciamento orchestrator ou configuração. A vantagem dessa abordagem é que você tenha abstraídos sua infraestrutura de nuvem da camada de aplicativos e mantê-las separadamente.

## <a name="what-happens-when-a-vm-scale-set-scales-out"></a>O que acontece quando um conjunto de escala de máquina virtual escalas de check-out?

Quando você adiciona um ou mais VMs a uma escala definir aumentando a capacidade – se manualmente ou por meio de escala automática – o aplicativo é instalado automaticamente. Para exemplo se definir a escala tenha extensões definidas, eles executarem em uma nova VM sempre que ele for criado. Se o conjunto de escala é baseado em uma imagem personalizada, qualquer nova VM é uma cópia da imagem personalizada de origem. Se a escala definida VMs são hosts de contêiner, e em seguida, você pode ter código de inicialização para carregar os contêineres em uma extensão de Script personalizado ou uma extensão pode instalar um agente que registra com um orchestrator cluster (como o serviço de contêiner do Azure).

## <a name="how-do-you-manage-application-updates-in-vm-scale-sets"></a>Como você gerenciar atualizações de aplicativos em conjuntos de escala de máquina virtual?

Atualizações de aplicativo em conjuntos de escala de máquina virtual, três abordagens principais acompanhar de três métodos de implantação do aplicativo anterior:

* Atualizando com as extensões de máquina virtual. Quaisquer extensões de máquina virtual que são definidas para um conjunto de escala de máquina virtual são executadas cada vez que uma nova VM for implantada, uma máquina virtual existente é criada ou uma extensão de máquina virtual é atualizada. Se você precisa atualizar seu aplicativo, diretamente atualizando um aplicativo por meio de extensões é uma abordagem viável – você simplesmente atualiza a definição de extensão. Uma maneira simples de fazer isso é alterando o fileUris para apontar para o novo software.

* A abordagem imutável imagem personalizada. Quando você implantado o aplicativo (ou componentes de aplicativo) em uma imagem de máquina virtual, que você pode se concentrar na criação de um pipeline confiável para automatizar a criação, teste e implantação das imagens. Você pode projetar sua arquitetura para facilitar a troca rápida de um conjunto de escala em estágios em produção. Um bom exemplo dessa abordagem é o [driver de Azure Spinnaker funciona](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

Packer e Terraform também suportam o Gerenciador de recursos do Azure, para que você também pode definir suas imagens "como código" e criá-las no Azure, use o VHD no seu conjunto de escala. No entanto, fazendo assim se tornaria problemático para imagens Marketplace, onde scripts de extensões/personalizado se tornam mais importantes desde que você não manipula diretamente bits do Marketplace.

* Atualize contêineres. Abstratos o gerenciamento do ciclo de vida do aplicativo para um nível acima da infraestrutura de nuvem, por exemplo por aplicativos encapsular e componentes de aplicativo em contêineres e gerenciar esses contêineres por meio de orchestrators de contêiner e gerenciadores de configuração como chefe/Puppet.

A escala definido VMs se tornam um substrato estável para os contêineres e solicitar somente ocasionalmente atualizações de segurança e relacionados ao sistema operacional. Conforme mencionado, o serviço de contêiner do Azure é um bom exemplo de adotam essa abordagem e a criação de um serviço em torno dele.

## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>Como você reverter uma atualização de sistema operacional em domínios de atualização?

Suponha que você deseja atualizar sua imagem de SO mantendo a execução de máquina virtual escala definida. Uma maneira de fazer isso é atualizar a máquina virtual imagens máquina um virtual por vez. Você pode fazer isso com o PowerShell ou CLI do Azure. Há comandos separados para atualizar o modelo de máquina virtual escala definida (como sua configuração é definida) e para emitir chamadas de "atualização manual" em VMs individuais.

[Aqui](https://github.com/gbowerman/vmsstools) está um exemplo de script de Python que automatiza o processo de atualização de um domínio de atualização de um conjunto de escala de máquina virtual por vez. (Limitação: é mais de uma prova de conceito que uma solução pronta para produção robusto – você talvez queira adicionar alguns etc de verificação de erros.).
