<properties
    pageTitle="Linux e código-fonte aberto computação no Azure | Microsoft Azure"
    description="Lista Linux e computação de código-fonte aberto artigos no Azure, incluindo o uso de Linux básico, alguns conceitos básicos sobre executando ou carregar as imagens de Linux no Azure e outros conteúdos sobre tecnologias específicas e otimizações."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="06/27/2016"
    ms.author="rasquill"/>



# <a name="linux-and-open-source-computing-on-azure"></a>Linux e código-fonte aberto computação no Azure

Encontre toda a documentação que você precisa criar e gerenciar baseados em Linux máquinas virtuais no modelo clássico de implantação.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="get-started"></a>Introdução
- [Introdução para Linux no Azure](virtual-machines-linux-intro-on-azure.md)
- [Perguntas frequentes sobre máquinas virtuais do Azure criado com o modelo clássico de implantação](virtual-machines-linux-classic-faq.md)
- [Sobre imagens para máquinas virtuais](virtual-machines-linux-classic-about-images.md)
- [Carregar sua própria imagem de distribuição](virtual-machines-linux-classic-create-upload-vhd.md) (e também instruções usando uma [Distribuição de Azure-Endorsed](virtual-machines-linux-endorsed-distros.md))
- [Faça logon em uma máquina virtual do Linux usando o portal de clássico do Azure](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="set-up"></a>Configurar o

- [Instalar o Azure Interface de linha (comando Azure)](../xplat-cli-install.md)


## <a name="tutorials"></a>Tutoriais

- [Instalar a pilha de LÂMPADA no computador virtual Linux no Azure](virtual-machines-linux-create-lamp-stack.md)
- [Ruby no aplicativo da Web de Rails em uma máquina virtual do Azure](linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)
- [Como: instalar os massa do próton-C Apache Qpid para AMQP e barramento de serviço](../service-bus-messaging/service-bus-amqp-apache.md)

### <a name="databases"></a>Bancos de dados
- [Otimizar o desempenho do MySQL no Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [MySQL Clusters](virtual-machines-linux-classic-mysql-cluster.md)
- [Executando Cassandra com Linux no Azure e acessá-lo de Node](virtual-machines-linux-classic-cassandra-nodejs.md)
- [Criar um cluster de vários mestres de MariaDbs](virtual-machines-linux-classic-mariadb-mysql-cluster.md)

### <a name="hpc"></a>HPC
- [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
- [Executar NAMD com Microsoft HPC Pack em nós de computação Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
- [Configurar um cluster Linux RDMA para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md)

### <a name="docker"></a>Docker
- [Usando a extensão de máquina virtual Docker do Azure de linha de comando a Interface (Azure CLI)](virtual-machines-linux-classic-cli-use-docker.md)
- [Usando a extensão de máquina virtual Docker do portal do Azure](virtual-machines-linux-classic-portal-use-docker.md)
- [Como usar docker máquina no Azure](virtual-machines-linux-docker-machine.md)

### <a name="ubuntu"></a>Ubuntu
- [Como: MySQL Clusters](virtual-machines-linux-classic-mysql-cluster.md)
- [Como: node e Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)

### <a name="opensuse"></a>OpenSUSE
- [Como: instalar e executar o MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)

### <a name="coreos"></a>CoreOS
- [Como: usar CoreOS no Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="planning"></a>Planejamento
- [Diretrizes de implementação de serviços de infraestrutura Azure](virtual-machines-linux-infrastructure-subscription-accounts-guidelines.md)
- [Selecionar nomes de usuário Linux](virtual-machines-linux-usernames.md)
- [Como configurar uma disponibilidade definido para máquinas virtuais no modelo clássico de implantação](virtual-machines-linux-classic-configure-availability.md)
- [Como agendar manutenção planejada na VMs Azure](virtual-machines-linux-planned-maintenance-schedule.md)
- [Gerenciar a disponibilidade de máquinas virtuais](virtual-machines-linux-manage-availability.md)
- [Manutenção planejada para máquinas virtuais de Linux no Azure](virtual-machines-linux-planned-maintenance.md)


## <a name="deployment"></a>Implantação
- [Criar uma máquina virtual personalizada executando Linux](virtual-machines-linux-classic-createportal.md)
- [Noções básicas: capturando uma VM Linux para criar um modelo](virtual-machines-linux-classic-capture-image.md)
- [Informações para as distribuições não aprovado](virtual-machines-linux-create-upload-generic.md)


## <a name="management"></a>Gerenciamento

- [SSH](virtual-machines-linux-mac-create-ssh-keys.md)
- [Como redefinir uma senha ou SSH propriedades para Linux](virtual-machines-linux-classic-reset-access.md)
- [Usar raiz](virtual-machines-linux-use-root-privileges.md)


## <a name="azure-resources"></a>Recursos do Azure

- [O agente de Linux Azure](virtual-machines-linux-agent-user-guide.md)
- [Recursos e extensões de máquina virtual azure](virtual-machines-windows-extensions-features.md)
- [Inserindo dados personalizados em uma máquina virtual para usar com inicialização de nuvem](virtual-machines-windows-classic-inject-custom-data.md)


## <a name="storage"></a>Armazenamento

- [Anexar um disco de dados a uma máquina virtual Linux](virtual-machines-linux-classic-attach-disk.md)
- [Desanexar um disco de dados de uma máquina virtual Linux](virtual-machines-linux-classic-detach-disk.md)
- [RAID](virtual-machines-linux-configure-raid.md)


## <a name="networking"></a>Rede
- [Como configurar pontos de extremidade em uma máquina virtual clássico no Azure](virtual-machines-linux-classic-setup-endpoints.md)


## <a name="troubleshooting"></a>Solução de problemas
- [Solucionar problemas de conexões SSH (Secure Shell) a uma máquina virtual baseados em Linux Azure](virtual-machines-linux-troubleshoot-ssh-connection.md)
- [Solucionar problemas de implantação clássico com a criação de uma nova máquina virtual Linux no Azure](virtual-machines-linux-classic-troubleshoot-deployment-new-vm.md)  
- [Solucionar problemas de implantação clássico com reinicialização ou redimensionando an existing Linux Virtual Machine no Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md) 


## <a name="reference"></a>Referência

- [Azure comandos no modo de gerenciamento de serviço do Azure (asm)](../virtual-machines-command-line-tools.md)
- [Gerenciamento de serviço Azure API REST](https://msdn.microsoft.com/library/azure/ee460799.aspx)




## <a name="general-links"></a>Links de geral
Os links a seguir são para blogs da Microsoft, páginas do Technet e sites externos em vez de documentação Azure.com acima. Como Azure e o mundo de computação de código-fonte aberto são avanço destinos, é quase certo que os links a seguir são fora data, *apesar* do fato de que podemos deverá fazer nossa melhor continuamente Adicionar tópicos mais recentes e remover aquelas desatualizadas. Se nós tenha esquecido um, por favor, deixe-nos saber nos comentários ou enviar uma solicitação de recepção para nosso [GitHub repo](https://github.com/Azure/azure-content/).

- [Executando o ASP.NET 5 no Linux usando contêineres de Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
- [Como implantar uma imagem de máquina virtual CentOS de OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
- [Infraestrutura de atualização SUSE](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
- [SUSE Linux Enterprise Server de biblioteca de dispositivo de nuvem do SAP](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
- [Construção Linux altamente disponível no Azure em 12 etapas](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
- [Automatizar provisionamento Linux no Azure com CLI do Azure, Node, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
- [GlusterFS no Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### <a name="freebsd"></a>FreeBSD
- [FreeBSD em execução no Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
- [Fácil implantar FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
- [Implantar uma imagem personalizada do FreeBSD](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
- [Kaspersky AV para servidor de arquivos Linux](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a name="nosql"></a>NoSQL

- [8 bancos de dados de código-fonte aberto NoSql do Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
- [Slideshare (MSOpenTech): Experiências com CouchDb no Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
- [Executando CouchDB-como um serviço com Node, CORS e pesado](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)

- [Relacionada no Windows no serviço de Cache do Azure relacionada](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
- [Anunciando o provedor de estado de sessão ASP.NET para a versão de visualização relacionada](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)

- [Blog: RavenHQ agora disponível no Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### <a name="big-data"></a>Grande volume
- [Instalando o Hadoop em VMs Linux Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
- [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### <a name="relational-database"></a>Banco de dados relacional
- [Arquitetura de disponibilidade do MySQL alta no Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
- [Instalando Postgres com corosync, pg_bouncer usando ILB](https://github.com/chgeuer/postgres-azure)

### <a name="linux-high-performance-computing-hpc"></a>Linux alto desempenho computação HPC)

- [Modelo de início rápido: rotação um cluster de SLURM](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm) (e [postagem de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
- [Modelo de início rápido: criar um cluster de HPC conosco de computação Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### <a name="devops-management-and-optimization"></a>DevOps, gerenciamento e otimização

Como o mundo do devops, gerenciamento e otimização é muito extenso e alterar rapidamente, você deve considerar a lista a seguir um ponto de partida.

- [Vídeo: Máquinas virtuais Azure: usando chefe, Puppet e Docker para gerenciar VMs Linux](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)

- [Guia completo para implantação de cluster Kubernetes automatizada com CoreOS e trançada](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
- [Visualizador de Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)

- [Auxiliar de Jenkins plug-in do Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
- [GitHub repo: Jenkins armazenamento plug-in do Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)

- [Terceiros: Auxiliar de Hudson plug-in do Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
- [Terceiros: Armazenamento de Hudson plug-in do Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)

- [Vídeo: O que é chefe e como ele funciona?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

- [Vídeo: Como usar a automação Azure com Linux VMs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)

- [Blog: Como fazer DSC do Powershell para Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
- [GitHub: Docker cliente DSC](https://github.com/anweiss/DockerClientDSC)

- [Packer plug-in do Azure](https://github.com/msopentech/packer-azure)
