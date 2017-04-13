<properties
   pageTitle="Acessando o ID de máquina virtual"
   description="Descreve o acesso e o uso de identificação exclusiva da máquina virtual Azure"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="accessing-and-using-azure-vm-unique-id"></a>Acessar e usar a identificação exclusiva da máquina virtual Azure

Identificação exclusiva da máquina virtual Azure é um identificador de 128 codificado e armazenados em SMBIOS do todos os IaaS VM do Azure e atualmente pode ser lidos usando comandos de BIOS da plataforma.

Identificação exclusiva da máquina virtual Azure é uma propriedade de somente leitura. Identificação exclusiva da máquina virtual Azure não mudará durante a reinicialização desligamento (seja planejado para planejado), iniciar/parar eliminação alocar, reparo do serviço ou restaurar no lugar. No entanto, se a máquina virtual é um instantâneo e copiados para criar uma nova instância, nova ID do Azure máquina virtual está configurado.

> [AZURE.NOTE] Se você tiver mais antigos VMs criado e executando pois esse novo recurso tem implantado (18 de setembro de 2014), por favor reiniciar sua máquina virtual para obter automaticamente um Azure exclusivo identificação.


Para acessar o Azure identificação exclusiva da máquina virtual do dentro a máquina virtual:


## <a name="create-a-vm"></a>Criar uma máquina virtual
 

Para obter mais informações, consulte [criar uma máquina Virtual](virtual-machines-linux-creation-choices.md)


## <a name="connect-to-the-vm"></a>Conectar-se para a máquina virtual
 

Para obter mais informações, consulte [SSH do Linux](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="query-vm-unique-id"></a>Identificação exclusiva da máquina virtual de consulta

Comando (exemplo usa **Ubuntu**):

    sudo dmidecode | grep UUID
    
Exemplo de resultados esperados:

    UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
    
Devido a grande Endian bit ordenação, a identificação exclusiva da máquina virtual real nesse caso será:

    DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
    
    
Identificação exclusiva da máquina virtual Azure pode ser usada em cenários diferentes se a máquina virtual está em execução no Azure ou local e pode ajudar seus requisitos de acompanhamento licenciamento, relatórios ou geral que você pode ter nas suas implantações do Azure IaaS. Muitos fornecedores de software independentes compilação de aplicativos e certificação-los no Azure podem exigir para identificar uma VM Azure em todo o ciclo de vida e saber se a máquina virtual está em execução no Azure, local ou em outros provedores de nuvem. Esse identificador de plataforma por exemplo pode ajudar a detectar se o software está corretamente licenciado ou ajudam a correlação quaisquer dados de máquina virtual à sua fonte como para auxiliar na configuração as métricas certas para a plataforma certa e para controlar e correlação essas métricas entre outros usos.
