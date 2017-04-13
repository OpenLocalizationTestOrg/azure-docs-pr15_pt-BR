<properties
    pageTitle="Instalar o MongoDB em um máquina virtual do Windows | Microsoft Azure"
    description="Saiba como instalar MongoDB em uma VM Azure criados com o modelo de implantação clássico executando o Windows Server."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

#<a name="install-mongodb-on-a-windows-vm"></a>Instalar o MongoDB em um máquina virtual do Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para instalar e configurar MongoDB usando o modelo de implantação do Gerenciador de recursos, consulte [Este artigo](virtual-machines-windows-classic-install-mongodb.md).

[MongoDB] [ MongoDB] é um popular código-fonte aberto alto desempenho NoSQL banco de dados. Este artigo orienta você criando uma máquina virtual do Windows Server (máquina virtual) usando o [portal de clássico Azure][AzurePortal]. Em seguida, crie e anexar um disco de dados para a máquina virtual antes de instalar e configurar o MongoDB. Se você tiver uma máquina virtual existente no Azure que você gostaria de usar, você pode passar diretamente para [Instalando e configurando MongoDB](#install-and-run-mongodb-on-the-virtual-machine).


## <a name="create-a-virtual-machine-running-windows-server"></a>Criar uma máquina virtual executando o Windows Server

Siga estas instruções para criar uma máquina virtual.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE] Você pode adicionar um ponto de extremidade para MongoDB durante a criação da máquina virtual e configure-o da seguinte maneira: Nomeie-o como **Mongo**, use **TCP** como o protocolo e configurar as portas públicas e privadas para **27017**.

## <a name="attach-a-data-disk"></a>Anexar um disco de dados
Para fornecer armazenamento da máquina virtual, anexar um disco de dados e, em seguida, inicializá-lo para que o Windows pode usá-lo. Se você já tiver um disco de dados, você pode anexar disco existente, ou você pode anexar um disco vazio.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Para obter instruções sobre como inicializar o disco, consulte "como: inicializar um novo disco de dados no Windows Server" em [como anexar um disco de dados para uma máquina virtual Windows](virtual-machines-windows-classic-attach-disk.md).

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>Instalar e executar o MongoDB na máquina virtual

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>Resumo
Neste tutorial, você aprendeu a criar uma máquina virtual executando o Windows Server, conectá-la remotamente e anexar um disco de dados.  Você também aprendeu como instalar e configurar o MongoDB na máquina virtual baseado no Windows. Agora você pode acessar o MongoDB na máquina virtual baseado no Windows, seguindo os tópicos avançados na [documentação do MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com
