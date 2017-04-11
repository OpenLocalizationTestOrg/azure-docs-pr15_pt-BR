 <properties
   pageTitle="Azure e Linux | Microsoft Azure"
   description="Descreve os serviços de computação Azure, armazenamento e rede com máquinas virtuais Linux."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/14/2016"
   ms.author="v-livech"/>

# <a name="azure-and-linux"></a>Azure e Linux

Microsoft Azure é uma coleção crescente de público integrado incluindo analytics, máquinas virtuais, bancos de dados, móveis, rede, armazenamento, os serviços de nuvem e web — ideal para suas soluções de hospedagem.  Microsoft Azure fornece uma plataforma de computação flexível que permite que você pagar somente pelo que você usar, quando quiser-sem precisar investir em locais hardware.  Azure está pronto quando você estiver dimensionar suas soluções para cima e check-out para qualquer escala você exige para atender às necessidades dos seus clientes.

Se você estiver familiarizado com os diversos recursos da Amazon AWS, você pode examinar vs Azure AWS [documento de definição de mapeamento](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).


## <a name="regions"></a>Regiões
Recursos do Microsoft Azure são distribuídos entre várias regiões geográficas em todo o mundo.  "Região" representa vários centros de dados em uma única área geográfica.  A partir de 1 de janeiro de 2016, isso inclui: 8 na América, 2 na Europa, 6 em Pacífico, 2 na China continente e 3 na Índia.  Se desejar uma lista completa de todas as regiões Azure, podemos manter que uma lista de existentes e recentemente anunciado regiões.

- [Regiões Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Disponibilidade
Em ordem para a sua implantação qualificar para nosso 99.95 contrato de nível de serviço de máquina virtual, você precisa implantar dois ou mais VMs executando sua carga de trabalho dentro de uma disponibilidade definido. Isso garante suas VMs são distribuídos em vários domínios de falhas em nossos data centers bem como implantados em hosts com janelas de manutenção diferentes. O total [Do Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) explica a disponibilidade de garantia do Azure como um todo.

## <a name="azure-virtual-machines--instances"></a>Azure máquinas virtuais & instâncias
Microsoft Azure suporta a execução de um número de Distribuições populares do Linux fornecidas e mantidas por um número de parceiros.  Você encontrará distribuições como Red Hat Enterprise, CentOS, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD e muito mais do Azure Marketplace. Estamos ativamente trabalhar com várias comunidades Linux para adicionar tipos ainda mais a lista [que Azure aprovado Linux Distros](virtual-machines-linux-endorsed-distros.md) .

Se sua distribuição Linux preferencial de escolha não está presente atualmente na galeria, você pode "trazer seu próprio Linux" máquina virtual [Criando](virtual-machines-linux-create-upload-generic.md)e carregando um VHD Linux no Azure.

Azure máquinas virtuais permitem que você implante uma ampla variedade de soluções de computação de forma ágil. Você pode implantar praticamente qualquer carga de trabalho e qualquer idioma em praticamente qualquer sistema operacional - Windows, Linux, ou um personalizado criado um a partir da nossa crescente lista de parceiros. Ainda não está vendo o que você está procurando?  Não se preocupe: você também pode trazer suas próprias imagens do local.

## <a name="vm-sizes"></a>Tamanhos de máquina virtual
Quando você implanta uma máquina virtual no Azure, você vai selecione um tamanho de máquina virtual dentro de uma das nossa série de tamanhos que é adequado à sua carga de trabalho. O tamanho também afeta a capacidade de energia, memória e armazenamento de processamento da máquina virtual. Você será cobrado com base na quantidade de tempo a máquina virtual está sendo executado e consumindo seus recursos alocados. Uma lista completa de [tamanhos de máquinas virtuais](virtual-machines-linux-sizes.md).

Aqui estão algumas diretrizes básicas para selecionar um tamanho de máquina virtual em um dos nosso série (A, D, DS, G e GS).

* A série VMs são nosso valor preço básica VMs light cargas de e cenários de desenvolvimento/teste. Eles estão amplamente disponíveis em todas as regiões e podem se conectar e usar todos os recursos padrão disponíveis para máquinas virtuais.
* Tamanhos de uma série (A8 - A11) são as configurações de intenso de computação especial adequadas para aplicativos de cluster de computação de alto desempenho.
* VMs de série D foram projetadas para executar aplicativos que exigem maior capacidade de computação e desempenho de disco temporário. Série D VMs fornecem processadores mais rápidos, uma taxa de memória-to-core superior e uma unidade de estado sólido (SSD) para o disco temporário.
* Dv2 série, é a versão mais recente do nosso série D, apresenta uma CPU mais eficazes. A CPU Dv2 série é aproximadamente 35% mais rápido que a CPU série D. Ele se baseia a última geração 2,4 GHz Intel Xeon® E5-2673 v3 processador (Haskell) e com o 2.0 de tecnologia de aumento de sensibilidade do Intel Turbo, pode ir até 3,2 GHz. A série de Dv2 tem as mesmas configurações de memória e disco como a série de D.
* Série de G VMs oferecem a maioria da memória e executado em hosts que têm processadores família Intel Xeon E5 V3.

Observação: Série DS e série GS VMs têm acesso ao armazenamento de Premium - nosso SSD feito o armazenamento de alto desempenho e baixa latência para cargas de trabalho intenso e/s. Armazenamento de Premium está disponível em determinadas regiões. Para obter detalhes, consulte:

- [: Premium alto desempenho de armazenamento para cargas de trabalho do Azure máquina virtual](../storage/storage-premium-storage.md)

## <a name="automation"></a>Automação
Para obter uma cultura de DevOps adequada, a infraestrutura de todos os deve ser código.  Quando todos a infraestrutura reside no código que pode ser facilmente recriada (rio servidores).  Azure funciona com todos os principal automação ferramentas como Ansible, chefe, SaltStack e Puppet.  Azure também tem seu próprio ferramentas para automação:

- [Modelos do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

- [VMAccess Azure](virtual-machines-linux-using-vmaccess-extension.md)

Azure está distribuindo o suporte para [inicialização de nuvem](http://cloud-init.io/) pela maioria dos Linux Distros que suporte a ele.  No momento Ubuntu VMs da Canonical são implantadas com nuvem-inicialização habilitado por padrão.  RedHats RHEL, CentOS e Fedora suportam inicial de nuvem, porém imagens Azure mantidos pelo RedHat não têm nuvem-inicialização instalado.  Para usar a inicialização de nuvem em uma família de RedHat SO, você deve criar uma imagem personalizada com inicialização de nuvem instalada.

- [Usando a inicialização de nuvem no Azure Linux VMs](virtual-machines-linux-using-cloud-init.md)

## <a name="quotas"></a>Cotas
Cada assinatura do Azure tem limites de cota padrão no local que poderia afetar a implantação de um grande número de VMs para seu projeto. O limite atual em uma base por inscrição é 20 VMs por região.  Limites de cota podem ser elevados arquivando um tíquete solicitando um aumento de limite.  Para obter mais detalhes sobre os limites de cota:

- [Limites de serviço de assinatura do Azure](../azure-subscription-service-limits.md)


## <a name="partners"></a>Parceiros

Microsoft trabalha em conjunto com nossos parceiros para garantir que as imagens disponíveis são atualizadas e otimizadas para um tempo de execução Azure.  Para obter mais informações sobre parceiros marque suas páginas de marketplace abaixo.

- [Linux em distribuições aprovado do Azure](virtual-machines-linux-endorsed-distros.md)

RedHat - [Azure Marketplace - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)

Canônico - [Azure Marketplace - Ubuntu servidor 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)

Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)

FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)

CoreOS - [Azure Marketplace - CoreOS (estável)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)

RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)

Bitnami - [Bitnami biblioteca do Azure](https://azure.bitnami.com/)

Mesosphere - [Azure Marketplace - Mesosphere DC/SO no Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)

Docker - [Azure Marketplace - serviço de contêiner Azure com por nuvem Docker](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)

Jenkins - [Azure Marketplace - CloudBees Jenkins plataforma](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)


## <a name="getting-setup-on-azure"></a>Obtendo configuração no Azure
Para começar a usar o Azure, é necessário uma conta do Azure, CLI Azure instalado e um par de chaves pública e privada de SSH.

## <a name="sign-up-for-an-account"></a>Inscrever-se para uma conta
A primeira etapa no uso de nuvem Azure é inscrever para uma conta do Azure.  Vá para a página de [Inscrição de conta do Azure](https://azure.microsoft.com/pricing/free-trial/) para começar.

## <a name="install-the-cli"></a>Instalar o CLI
Com sua nova conta do Azure, você pode começar imediatamente usando o portal do Azure, que é um painel de administração baseado na web.  Para gerenciar a nuvem Azure por meio da linha de comando, que você instale o `azure-cli`.  Instale o [Azure CLI ](../xplat-cli-install.md)em sua estação de trabalho Mac ou Linux.

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH
Agora você tem uma conta do Azure, o portal web Azure e a CLI do Azure.  A próxima etapa é criar um par de chaves de SSH que é usado para SSH em Linux sem usar uma senha.  [Criar SSH teclas Linux e Mac](virtual-machines-linux-mac-create-ssh-keys.md) para habilitar o logon de senha de menor e maior segurança.

## <a name="getting-started-with-linux-on-microsoft-azure"></a>Introdução ao Linux no Microsoft Azure
Com a configuração de conta do Azure, o Azure CLI instalado e chaves SSH criadas agora você está pronto para começar a criação de uma infraestrutura na nuvem do Azure.  A primeira tarefa é criar algumas VMs.

## <a name="create-a-vm-using-the-cli"></a>Criar uma máquina virtual utilizando a CLI
Criar uma VM Linux utilizando a CLI é uma maneira rápida de implantar uma máquina virtual sem deixar o terminal que está trabalhando.  Tudo o que você pode especificar no portal da web está disponível por meio de um sinalizador de linha de comando ou alternar.  

- [Criar uma VM Linux utilizando a CLI](virtual-machines-linux-quick-create-cli.md)

## <a name="create-a-vm-in-the-portal"></a>Criar uma máquina virtual no portal
Criar uma VM Linux no portal do Azure web é uma maneira facilmente apontar e clicar em pelas várias opções para acessar uma implantação.  Em vez de usar sinalizadores de linha de comando ou opções, será possível exibir um layout de web lindas das várias opções e configurações.  Tudo disponível por meio da interface de linha de comando também está disponível no portal.

- [Criar uma VM Linux usando o Portal](virtual-machines-linux-quick-create-portal.md)

## <a name="login-using-ssh-without-a-password"></a>Faça logon usando SSH sem uma senha
A máquina virtual está em execução no Azure e você está pronto para fazer logon no.  Usar senhas para efetuar logon via SSH for insegura e demorado.  Usando as teclas de SSH é a maneira mais segura e também a maneira mais rápida de login.  Quando você cria Linux VM por meio do portal ou CLI, você tem duas opções de autenticação.  Se você escolher uma senha para o SSH, o Azure configura a máquina virtual para permitir logon por meio de senhas.  Se você optar por usar uma chave pública SSH, Azure configura a máquina virtual para permitir apenas logon via SSH chaves e desativa o logon de senha. Para proteger sua VM Linux permitindo apenas logon chave SSH, use a opção de chave pública SSH durante a criação de máquina virtual do portal ou CLI.

- [Desabilitar senhas SSH em sua VM Linux Configurando SSHD](virtual-machines-linux-mac-disable-ssh-password-usage.md)

## <a name="related-azure-components"></a>Componentes de Azure relacionados

## <a name="storage"></a>Armazenamento

- [Introdução ao Microsoft Azure armazenamento](../storage/storage-introduction.md)

- [Adicionar um disco a uma VM Linux usando a cli do azure](virtual-machines-linux-add-disk.md)

- [Como anexar um disco de dados a uma VM Linux no portal do Azure](virtual-machines-linux-attach-disk-portal.md)

## <a name="networking"></a>Rede

- [Visão geral de rede virtual](../virtual-network/virtual-networks-overview.md)

- [Endereços IP no Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md)

- [Abrindo portas a uma VM Linux no Azure](virtual-machines-linux-nsg-quickstart.md)

- [Criar um nome de domínio totalmente qualificado no portal do Azure](virtual-machines-linux-portal-create-fqdn.md)


## <a name="containers"></a>Contêineres

- [Máquinas virtuais e contêineres no Azure](virtual-machines-linux-containers.md)

- [Introdução de serviço de contêiner Azure](../container-service/container-service-intro.md)

- [Implantar um cluster de serviço de contêiner do Azure](../container-service/container-service-deployment.md)

## <a name="next-steps"></a>Próximas etapas

Agora você tem uma visão geral do Linux no Azure.  A próxima etapa é começar a criar VMs alguns!

- [Criar uma VM Linux no Azure usando o Portal](virtual-machines-linux-quick-create-portal.md)

- [Criar uma VM Linux no Azure usando a CLI](virtual-machines-linux-quick-create-cli.md)
