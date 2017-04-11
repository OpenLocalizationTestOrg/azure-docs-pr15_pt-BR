<properties
    pageTitle="Linux convidados na pilha Azure | Microsoft Azure"
    description="Saiba como criar máquinas virtuais baseadas em Linux na pilha do Azure."
    services="azure-stack"
    documentationCenter=""
    authors="anjayajodha"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anajod"/>
    
# <a name="deploy-linux-virtual-machines-on-azure-stack"></a>Implantar máquinas virtuais Linux pilha do Azure

Você pode implantar máquinas virtuais Linux o Azure pilha VDC adicionando uma imagem baseada em Linux na pilha do Azure Marketplace. Vários fornecedores de Linux forneceu imagens que podem ser adicionadas a uma VDC de pilha Azure ou você pode criar seus próprios.

## <a name="download-an-image"></a>Baixar uma imagem

 1. Baixar e extrair uma imagem do Azure pilha compatível com os links a seguir ou preparar seu próprio:
  - [Bitnami](https://bitnami.com/azure-stack)
  - [CentOS](http://olstacks.cloudapp.net/latest/)
  - [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
  - [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
  - [Ubuntu 14.04 LTS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)
  
 2. Extraia a imagem VHD se necessário e [Adicionar a imagem para o Marketplace](azure-stack-add-vm-image.md). Verifique se o `OSType` parâmetro for definido como `Linux`.
 
 3. Depois de adicionar a imagem para o Marketplace, um item de Marketplace é criado e você pode implantar uma máquina virtual Linux.
  
## <a name="prepare-your-own-image"></a>Preparar sua própria imagem

1. Prepare sua própria imagem de Linux usando uma das instruções a seguir:
 - [Distribuições baseadas em centOS](../virtual-machines/virtual-machines-linux-create-upload-centos.md)
 - [Debian Linux](../virtual-machines/virtual-machines-linux-debian-create-upload-vhd.md)
 - [Oracle Linux](../virtual-machines/virtual-machines-linux-oracle-create-upload-vhd.md)
 - [Red Hat Enterprise Linux](../virtual-machines/virtual-machines-linux-redhat-create-upload-vhd.md)
 - [SLES & openSUSE](../virtual-machines/virtual-machines-linux-suse-create-upload-vhd.md)
 - [Ubuntu](../virtual-machines/virtual-machines-linux-create-upload-ubuntu.md)

2. Baixe e instale o [Agente de Linux do Azure](https://github.com/Azure/WALinuxAgent/)

    O agente do Azure Linux versão 2.1.3 ou posterior é necessário para provisionar sua VM Linux na pilha do Azure. Muitos das distribuições listadas acima já incluem esta versão do agente ou superior como um pacote nos seus repositórios (normalmente chamado `WALinuxAgent` ou `walinuxagent`). No entanto, se a versão do pacote do agente Azure é menor que 2.1.3 (ou seja, 2.0.18 ou inferior), e em seguida, você deve instalar o agente manualmente. A versão instalada pode ser determinada a partir do nome do pacote ou executando `/usr/sbin/waagent -version` sobre a máquina virtual.

    Siga as instruções abaixo para instalar o agente do Azure Linux manualmente-

 - Primeiro, baixe o agent Azure Linux mais recente do [Github](https://github.com/Azure/WALinuxAgent/releases), exemplo:

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.0.tar.gz

 - Descompacte o agente Azure:

            # tar -vzxf v2.2.0.tar.gz

 - Instalar o python setuptools

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools

 - Instale o agente do Azure:

            # cd WALinuxAgent-2.2.0
            # sudo python setup.py install --register-service

    Sistemas com Python 2. x e Python 3. x instalado lado a lado pode ser necessário executar o comando a seguir:

        # sudo python3 setup.py install --register-service

    Para obter mais informações, consulte o agente do Azure Linux [Leiame](https://github.com/Azure/WALinuxAgent/blob/master/README.md).

3. [Adicionar a imagem para o Marketplace](azure-stack-add-vm-image.md). Verifique se o `OSType` parâmetro for definido como `Linux`.

4. Depois de adicionar a imagem para o Marketplace, um item de Marketplace é criado e você pode implantar uma máquina virtual Linux.

## <a name="next-steps"></a>Próximas etapas

[Perguntas frequentes do Azure pilha](azure-stack-faq.md)