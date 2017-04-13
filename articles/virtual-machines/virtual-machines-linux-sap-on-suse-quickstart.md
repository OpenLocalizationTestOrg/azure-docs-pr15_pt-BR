<properties
   pageTitle="Teste SAP NetWeaver no Microsoft Azure SUSE Linux VMs | Microsoft Azure"
   description="Teste SAP NetWeaver no Microsoft Azure SUSE Linux VMs"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Executando SAP NetWeaver no Microsoft Azure SUSE Linux VMs


Este artigo descreve várias coisas a serem consideradas quando você estiver executando o SAP NetWeaver no Microsoft Azure SUSE Linux VMs (máquinas virtuais). A partir 19 de maio de 2016 SAP NetWeaver é suportada oficialmente em SUSE Linux VMs no Azure. Todos os detalhes sobre versões Linux, versões de núcleo do SAP e assim por diante podem ser encontrados na SAP anotação 1928533 "aplicativos SAP no Azure: produtos suportados e tipos de máquina virtual Azure".
Mais documentação sobre SAP em Linux VMs pode ser encontrada aqui: [Usando o SAP em Linux VMs (máquinas virtuais)](virtual-machines-linux-sap-get-started.md).


As seguintes informações devem ajudá-lo a evitar algumas armadilhas potenciais.

## <a name="suse-images-on-azure-for-running-sap"></a>Imagens SUSE no Azure para execução de SAP

Para executar o SAP NetWeaver no Azure, usar somente SUSE Linux Enterprise Server SLES 12 (SPx) - Consulte também Observação SAP 1928533. Uma imagem SUSE especial é no Azure Marketplace ("SLES 11 SP3 para SAP CAL"), mas isso não se destina a uso geral. Não use esta imagem porque ela é reservada para a solução de [Biblioteca de dispositivo de nuvem do SAP](https://cal.sap.com/) .  

Você deve usar o Gerenciador de recursos do Azure para todos os novos testes e instalações no Azure. Para procurar imagens SUSE SLES e versões usando o PowerShell do Azure ou a interface de linha Azure (comando), use os seguintes comandos. Em seguida, você pode usar a saída, por exemplo, para definir a imagem de sistema operacional em um modelo JSON para implantar uma nova VM Linux SUSE.
Esses comandos do PowerShell são válidos para Azure PowerShell versão 1.0.1 e posterior.

* Procure por fornecedores existentes, incluindo SUSE:

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* Procure ofertas existentes do SUSE:

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* Procure ofertas SUSE SLES:

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* Procure por uma versão específica de uma SKU SLES:

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>Instalando WALinuxAgent em uma máquina virtual SUSE

O agente chamado WALinuxAgent faz parte de imagens SLES do Azure Marketplace. Para obter informações sobre como instalá-lo manualmente (por exemplo, ao carregar um sistema operacional SLES virtual VHD disco rígido () do local), consulte:

- [OpenSUSE] (http://software.opensuse.org/package/WALinuxAgent)

- [Azure] (virtual-máquinas-linux-aprovado-distros.md)

- [SUSE] (https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP "monitoramento avançado"

SAP "monitoramento avançado" é um pré-requisito obrigatório para executar o SAP no Azure. Verifique detalhes no SAP Observação 2191498 "SAP no Linux com o Azure: Enhanced Monitoring".

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Anexar discos de dados do Azure para uma máquina virtual Linux do Azure

Você nunca deve discos de dados do Azure montagem para uma máquina virtual Linux do Azure usando a identificação de dispositivo. Em vez disso, use o identificador exclusivo universal (UUID). Tenha cuidado ao usar ferramentas gráficas montagem Azure discos de dados, por exemplo. Verifique as entradas no /etc/fstab.

O problema com a ID de dispositivo é que ela pode ser alterada e, em seguida, a máquina virtual do Azure podem travar no processo de inicialização. Para reduzir o problema, você pode adicionar o parâmetro nofail no /etc/fstab. Mas cuidado com nofail porque aplicativos podem usar o ponto de montagem como antes e podem escrever no sistema de arquivos raiz no caso de um disco de dados externos de Azure não foi montado durante a inicialização.

A única exceção a montagem via UUID é anexar um disco de sistema operacional para solução de problemas, conforme descrito na seção a seguir.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Uma VM SUSE não puder ser acessado mais de solução de problemas

Pode haver situações em que uma VM SUSE no Azure trava no processo de inicialização (por exemplo, com um erro relacionado à montagem discos). Você pode verificar esse problema usando o recurso de diagnóstico de inicialização para máquinas virtuais do Azure v2 no portal do Azure. Para obter mais informações, consulte [diagnóstico de inicialização] (https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Uma maneira de resolver o problema é anexar o disco de sistema operacional da máquina virtual danificada a outra SUSE VM no Azure. Faça as alterações apropriadas como /etc/fstab de editar ou remover regras de udev de rede, conforme descrito na próxima seção.

Não há uma coisa importante levar em consideração. Implantar várias VMs SUSE da imagem do Azure Marketplace mesma (por exemplo, SLES 11 SP4) faz com que o disco de sistema operacional para sempre ser montado pelo mesmo UUID. Portanto, usando o UUID para anexar um disco do sistema operacional de uma máquina virtual diferente que foi implantada usando a mesma imagem do Azure Marketplace resultará em dois UUIDs idênticos. Isso causa problemas e significa que a máquina virtual são destinada a solução de problemas na verdade será inicializado do disco SO anexado e danificado em vez de original.

Há duas maneiras de evitar isso:

* Use uma imagem diferente do Azure Marketplace para a máquina virtual de solução de problemas (por exemplo, SLES 11 SPx em vez de SLES 12).
* Não anexar o disco danificado do sistema operacional da máquina virtual outro usando UUID – use algo mais.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Carregando uma VM SUSE do local para o Azure

Para obter uma descrição das etapas para carregar uma SUSE VM do local para Azure, consulte [preparar uma máquina virtual SLES ou openSUSE para Azure] (virtual-machines-linux-suse-create-upload-vhd.md).

Se você deseja carregar uma máquina virtual sem a etapa deprovision no final (por exemplo, para manter uma instalação existente do SAP, bem como o nome do host), verifique os seguintes itens:

* Certifique-se de que o disco de sistema operacional é montado usando UUID e não a identificação do dispositivo. Alterar para UUID apenas na /etc/fstab não é suficiente para o disco de sistema operacional. Além disso, não se esqueça de adaptar o carregador de inicialização através de YaST ou editando /boot/grub/menu.lst.
* Se você usa o formato VHDX para o disco de sistema operacional SUSE e convertê-la em VHD para carregamento em Azure, é provável que o dispositivo de rede será alterada da eth0 para eth1. Para evitar problemas quando você estiver inicialização no Azure posteriormente, reverter para eth0 conforme descrito em [corrigindo eth0 no VMware de 11 SLES clonado] (https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Além do que está descrito no artigo, recomendamos que você remova isto:

   /lib/udev/rules.d/75-persistent-NET-Generator.Rules

Você também pode instalar o agente de Linux Azure (waagent) para ajudar a evitar possíveis problemas, desde que não há várias placas de rede.

## <a name="deploying-a-suse-vm-on-azure"></a>Implantando uma VM SUSE no Azure

Você deve criar novas VMs SUSE usando arquivos de modelo JSON no novo modelo de Gerenciador de recursos do Azure. Após o arquivo de modelo JSON é criado, você pode implantar a máquina virtual usando o seguinte comando CLI como uma alternativa para PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Para obter mais detalhes sobre os arquivos de modelo JSON, consulte [modelos de coautoria Gerenciador de recursos do Azure] (recurso-grupo-autoria-templates.md) e [modelos de início rápido Azure] (https://azure.microsoft.com/documentation/templates/).

Para obter mais detalhes sobre CLI e Gerenciador de recursos do Azure, consulte [usar a CLI do Azure para Mac, Linux e Windows com o Gerenciador de recursos do Azure] (xplat-cli-azure-recurso-manager.md).

## <a name="sap-license-and-hardware-key"></a>Chave de licença e hardware do SAP

Para a certificação oficial do Azure SAP, um novo mecanismo foi introduzido para calcular a chave de hardware do SAP que é usada para a licença do SAP. O núcleo SAP tinha que ser adaptados para tornar use isso. Versões anteriores de núcleo SAP para Linux não incluem essa alteração de código. Portanto, em determinadas situações (por exemplo, máquina virtual do Azure redimensionamento), a chave de hardware do SAP alterada e a licença do SAP foi não ser mais válida. Isso é resolvido nos kernels Linux SAP mais recentes. Para detalhes, consulte observação SAP 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>Pacote de sapconf SUSE / adm ajustado

SUSE fornece um pacote chamado "sapconf" que gerencia um conjunto de configurações específicas do SAP. Para obter mais detalhes sobre o que esse pacote faz e como instalar e usá-lo, veja [usando sapconf para preparar um SUSE Linux Enterprise Server para executar sistemas SAP] (https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) e [o que é sapconf ou como preparar uma SUSE Linux Enterprise Server para execução de sistemas SAP?] (http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

Enquanto isso, há uma nova ferramenta que substitui sapconf - ADM ajustado. Um pode encontrar mais detalhes sobre esta ferramenta acompanhando os dois links abaixo.

Documentação SLES sobre adm ajustado perfil sap-hana pode ser encontrada [aqui](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

Sistemas de ajustes para cargas de trabalho do SAP com ajustado-adm - podem ser encontradas [aqui](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) capítulo 6.2


## <a name="nfs-share-in-distributed-sap-installations"></a>Compartilhar o NFS em instalações distribuídas do SAP

Se você tiver uma instalação distribuída – por exemplo, onde você deseja instalar o banco de dados e os servidores de aplicativo SAP em VMs separadas – você pode compartilhar o diretório /sapmnt via sistema de arquivo de rede (NFS). Se você tiver problemas com as etapas de instalação depois de criar o compartilhamento NFS para /sapmnt, verifique se "no_root_squash" é definido para o compartilhamento.

## <a name="logical-volumes"></a>Volumes lógicos

No passado se um necessário um grande volume lógico entre vários discos de dados Azure (por exemplo, para o banco de dados SAP), era recomendável usar mdadm como lvm não foi totalmente validada ainda no Azure. Para saber como configurar RAID Linux no Azure usando mdadm, consulte [configurar software RAID no Linux](virtual-machines-linux-configure-raid.md). Enquanto isso partir do início de maio de 2016 também lvm é totalmente suportada no Azure e pode ser usado como uma alternativa para mdadm. Para obter informações adicionais sobre lvm no Azure, consulte [Configurar LVM em uma VM Linux no Azure](virtual-machines-linux-configure-lvm.md).


## <a name="azure-suse-repository"></a>Repositório SUSE Azure

Se você tiver um problema com o acesso ao repositório do Azure SUSE padrão, você pode usar um comando simples para redefini-la. Isso pode acontecer se você criar uma imagem de SO particular em uma região Azure e, em seguida, copie a imagem para uma região diferente, onde você deseja implantar novas VMs que são baseadas nesta imagem de sistema operacional particular. Basta execute o seguinte comando dentro da VM:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Área de trabalho Gnome

Se você quiser usar a área de trabalho Gnome para instalar um sistema de demonstração SAP completo dentro de uma única máquina virtual – incluindo uma interface de usuário do SAP, o navegador e console de gerenciamento do SAP – usam essa dica instalá-lo nas imagens Azure SLES:

   Para SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Para SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>Suporte do SAP para Oracle no Linux na nuvem

Há uma restrição de suporte da Oracle no Linux em ambientes virtualizados. Embora não seja um tópico específico do Azure, é importante entender. SAP não suporta Oracle no SUSE ou Red Hat em uma nuvem pública como o Azure. Para discutir este tópico, contate Oracle diretamente.
