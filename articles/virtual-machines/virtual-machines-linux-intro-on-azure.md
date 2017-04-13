<properties
    pageTitle="Introdução ao Linux no Azure | Microsoft Azure"
    description="Saiba como usar máquinas virtuais Linux no Azure."
    services="virtual-machines-linux"
    documentationCenter="python"
    authors="szarkos"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

#<a name="introduction-to-linux-on-azure"></a>Introdução ao Linux no Azure

Este tópico fornece uma visão geral de alguns aspectos de máquinas virtuais Linux na nuvem Azure. Implantar um computador virtual Linux é um processo simples usando uma imagem da Galeria.


## <a name="authentication-usernames-passwords-and-ssh-keys"></a>Autenticação: Nomes de usuário, senhas e chaves SSH

Ao criar uma máquina virtual do Linux usando o portal de clássico Azure, você é solicitado a fornecer um nome de usuário, senha ou uma chave pública SSH. A escolha de um nome de usuário para implantar o computador virtual Linux no Azure está sujeito a restrição a seguir: nomes de contas do sistema (UID < 100) já estão presentes na máquina virtual não são permitidas, 'raiz' por exemplo.


 - Consulte [criar uma máquina Virtual executando Linux](virtual-machines-linux-quick-create-cli.md)
 - Veja [como usar SSH com Linux no Azure](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="obtaining-superuser-privileges-using-sudo"></a>Obtendo superusuário usando`sudo`

A conta de usuário especificado durante a implantação de instância de máquina virtual no Azure é uma conta com privilégios. Essa conta é configurada pelo agente de Linux Azure sejam capazes de elevar privilégios raiz (conta superusuário) usando o `sudo` utilitário. Quando conectado usando essa conta de usuário, você poderá executar comandos como raiz usando a sintaxe de comando

    # sudo <COMMAND>

Opcionalmente, você pode obter um shell raiz usando **sudo -s**.

- Consulte [privilégios de raiz usando nas máquinas virtuais Linux Azure](virtual-machines-linux-use-root-privileges.md)


## <a name="firewall-configuration"></a>Configuração de firewall

Azure fornece um filtro de pacote de entrada que restringe a conectividade portas especificadas no portal de clássico do Azure. Por padrão, a única porta permitida é SSH. Você pode abrir o acesso a portas adicionais no computador virtual Linux Configurando pontos de extremidade no portal de clássico do Azure:

 - Consulte: [como configurar pontos de extremidade para uma máquina Virtual](virtual-machines-windows-classic-setup-endpoints.md)

As imagens de Linux na Galeria Azure não habilite o firewall *iptables* por padrão. Se desejar, o firewall pode ser configurado para fornecer a filtragem adicional.


## <a name="hostname-changes"></a>Alterações de nome do host

Quando você implanta inicialmente uma instância de uma imagem de Linux, são necessárias para fornecer um nome de host para a máquina virtual. Quando estiver em execução na máquina virtual, este hostname é publicado para os servidores DNS de plataforma para que várias máquinas virtuais conectadas entre si possa realizar pesquisas de endereço IP usando nomes de host.

Se as alterações de nome do host são desejadas após ter sido implantada uma máquina virtual, use o comando

    # sudo hostname <newname>

O agente de Linux Azure inclui funcionalidade para detectar automaticamente essa alteração de nome e definir adequadamente a máquina virtual para persistir essa alteração e publicar essa alteração para os servidores DNS de plataforma.

 - [Guia de usuário do agente de Linux Azure](virtual-machines-linux-agent-user-guide.md)

### <a name="cloud-init"></a>Inicialização de nuvem
Imagens **Ubuntu** e **CoreOS** utilizam nuvem-inicialização no Azure, que fornece recursos adicionais para uma máquina virtual de inicialização.

 - [Como inserir dados personalizados](virtual-machines-windows-classic-inject-custom-data.md)
 - [Dados personalizados e nuvem-inicialização no Microsoft Azure](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
 - [Criar partições de troca Azure usando a inicialização de nuvem](https://wiki.ubuntu.com/AzureSwapPartitions)
 - [Como usar CoreOS no Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="virtual-machine-image-capture"></a>Captura de imagem de máquina virtual

Azure fornece a capacidade para capturar o estado de uma máquina virtual existente em uma imagem que subsequentemente pode ser usada para implantar instâncias de máquinas virtuais adicionais. O agente do Azure Linux pode ser usada para reverter algumas das personalizações que foi executada durante o processo de provisionamento. Você pode seguir as etapas abaixo para capturar uma máquina virtual como uma imagem:

1. Executar **waagent-deprovision** para desfazer a personalização de provisionamento. Ou **waagent-deprovision + user** opcionalmente, excluir a conta de usuário especificada durante provisionamento e todos os respectivos dados.

2. Desligar para baixo/Desligue a máquina virtual.

3. Clique em *captura* no portal de clássico do Azure ou use as ferramentas do Powershell ou CLI para capturar a máquina virtual como uma imagem.

 - Consulte: [como capturar uma máquina Virtual Linux usar como um modelo](virtual-machines-linux-classic-capture-image.md)


## <a name="attaching-disks"></a>Anexar discos

Cada máquina virtual tem um temporário local *disco do recurso* anexada. Como dados em um disco de recursos podem não ser duráveis reinicializações, geralmente é usado por aplicativos e processos em execução na máquina virtual para armazenamento de dados transitório e **temporário** . Ele também é usado para armazenar a página ou troque arquivos para o sistema operacional.

No Linux, o disco de recurso é geralmente gerenciado pelo agente de Linux do Azure e automaticamente montado **/mnt/resource** (ou **/mnt** em imagens Ubuntu).


>[AZURE.NOTE] Observe que o disco de recurso é um disco **temporário** e pode ser excluído e reformatado quando a máquina virtual é reinicializada.

No Linux o disco de dados pode ser nomeado de acordo com o núcleo como `/dev/sdc`, e os usuários precisarão partição, formatar e montar esse recurso. Isso é coberto passo a passo no tutorial: [como anexar um disco de dados para uma máquina Virtual](virtual-machines-linux-classic-attach-disk.md).

 - **Consulte também:** [Configurar o Software RAID no Linux](virtual-machines-linux-configure-raid.md)  &  [Configurar LVM em uma VM Linux no Azure](virtual-machines-linux-configure-lvm.md)

