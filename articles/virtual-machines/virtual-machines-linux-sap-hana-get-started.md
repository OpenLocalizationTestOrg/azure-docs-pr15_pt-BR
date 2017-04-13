<properties
   pageTitle="Guia de início rápido para instalação manual da SAP HANA em VMs Azure | Microsoft Azure"
   description="Guia de início rápido para instalação manual da SAP HANA em VMs do Azure"
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

# <a name="quickstart-guide-for-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Guia de início rápido para instalação manual da instância única SAP HANA em VMs do Azure

## <a name="introduction"></a>Introdução

Este guia de início rápido ajudará para configurar um única instância SAP HANA protótipo/demonstração sistema em VMs Azure por uma instalação manual de SAP NetWeaver 7.5 e SAP HANA SP12.
Guia pressupõe que o leitor esteja familiarizado com as Noções básicas do Azure IaaS como implantar máquinas virtuais ou redes virtuais por meio do portal do Azure ou Powershell/CLI, incluindo a opção usar modelos de json. Além disso Esperamos que o leitor esteja familiarizado com HANA SAP, SAP NetWeaver e como instalá-lo no local.

Esperamos que o leitor esteja atento a documentação geral do Azure SAP mencionada na seção informações gerais no final do artigo.

Devido a restrição para sistemas de não produção neste guia não abordará tópicos como HA, fazer backup, DR, alto desempenho ou considerações de segurança especial.

A configuração de amostra foi feita usando duas máquinas virtuais para realizar uma instalação de SAP NetWeaver distribuída por meio do modelo de Gerenciador de recursos do Azure como SAP-Linux-Azure só é suportado no Gerenciador de recursos do Azure e não o modelo clássico. Links para informações adicionais sobre o Gerenciador de recursos do Azure podem ser encontradas na seção informações gerais no final deste artigo.

Esses foram VMs dois teste usadas para a instalação de amostra:

* Hana-arquivo appsrv (tipo DS3) para hospedar a instância de NW 7.5 ASCS + PAS
* Hana-dbsrv (tipo GS4) para o host HANA SP12
* ambas as VMs pertencem a uma rede virtual Azure (azure-hana-teste-vnet)
* Sistema operacional em ambos os casos foi SLES 12 SP1


Esteja ciente do fato Apesar que a partir de julho de 2016 HANA SAP é totalmente suportado apenas OLAP (BW) para sistemas de produção no tipo de máquina virtual do Azure GS5. Para testes onde um não está esperando suporte oficial do SAP, há problemas em usar algo menores como por exemplo, GS4.
O que sempre devem ser usado para SAP HANA no Azure é o armazenamento do Azure premium para arquivos de log e dados HANA - consulte "Configuração de disco" seção mais para baixo. Sobre detalhes adicionais sobre quais SAP produtos têm suporte no Azure, verifique a seção de informações gerais no final deste artigo.


O guia descreve duas maneiras diferentes para instalar manualmente o SAP HANA em VMs Azure:

* instalar o SAP HANA via Gerenciador de provisionamento de Software do SAP (SWPM) como parte de uma instalação NetWeaver distribuída na etapa "instância de banco de dados"
* instalar HANA SAP usando o Gerenciador de ciclo de vida do HANA ferramenta hdblcm e instalar NetWeaver posteriormente

Também é possível usar SWPM e instalar todos os componentes (HANA SAP, servidor de aplicativos do SAP, ASCS instância, interface usuário SAP) em uma única VM. Esta opção não está descrita neste artigo, mas os itens que devem ser considerados são os mesmos.

Antes de iniciar uma instalação seção depois as listas de verificação abaixo sobre como configurar as VMs de teste Azure deve ser lido evitar vários erros básicos que irá acontecer quando usando somente uma configuração de máquina virtual do Azure padrão.


## <a name="checklist-sap-hana-installation-via-sap-swpm"></a>Lista de verificação instalação de SAP HANA via SWPM do SAP

Esta é uma lista de verificação simple da chave de itens relacionados a uma instalação manual da SAP HANA instância única para demonstração ou protótipos pursposes via SWPM SAP fazendo um distribuído NW 7.5 da SAP instalar. Os itens individuais são explicados e mostrados no formulário de capturas de tela de forma mais detalhada em todo o artigo:

* criar uma rede virtual Azure que incluirá as VMs de dois teste 
* implantar duas VMs Azure com o sistema operacional SLES 12 SP1 através do modelo do Gerenciador de recursos do Azure 
* Anexar dois discos de armazenamento padrão para o servidor de aplicativo máquina virtual (por exemplo, 75GB e 500GB)
* quatro discos se conectar ao servidor HANA DB máquina virtual - tipo para o servidor de aplicativo máquina virtual de disco de armazenamento padrão 2 + 2 discos de armazenamento de premium (por exemplo, 2x512GB)
* Dependendo do tamanho e/ou produtividade requisitos anexar vários discos e criar volumes distribuídos ou usando lvm ou mdadm em nível de sistema operacional dentro da VM
* Crie sistemas de arquivos XFS os discos anexados / lógico volumes
* Monte os novos sistemas de arquivo XFS no nível do sistema operacional. Use um sistema de arquivos para manter todo o software do SAP e o outro por exemplo, para o diretório de sapmnt e talvez backups. Sobre o banco de dados do SAP HANA montagem do servidor o XFS sistemas de arquivos nos discos de armazenamento do premium como /hana e /usr/sap é tudo necessária para evitar que o sistema de arquivos de raiz que não é muito grande em Linux Azure VMs preenche para cima
* Digite os endereços de ip local do teste VMs no/etc/hosts
* Insira o parâmetro nofail no /etc/fstab.
* parâmetros de núcleo de conjunto de acordo com a anotação de SAP HANA-SLES-12 (veja detalhes adicionais para baixo na seção de parâmetros de núcleo)
* Adicionar espaço de troca
* Se quisesse - instale uma área de trabalho gráfica no teste VMs. Caso contrário, use uma instalação sapinst remoto
* baixar o software SAP do SAP service marketplace
* instalar a instância do SAP ASCS na máquina virtual do servidor de aplicativo
* compartilhar o diretório sapmnt via NFS entre o teste VMs (servidor de aplicativo máquina virtual é o servidor NFS)
* instalar a instância de banco de dados, incluindo HANA via SWPM na máquina virtual do servidor de banco de dados
* instalar o PAS no servidor do aplicativo máquina virtual
* Iniciar MC SAP e se conectar por exemplo, por meio de interface usuário SAP / HANA Studio 



## <a name="checklist-sap-hana-installation-via-hdblcm"></a>Lista de verificação instalação de SAP HANA via hdblcm

Esta é uma lista de verificação simple da chave de itens relacionados a uma instalação manual da SAP HANA instância única para demonstração ou protótipos pursposes via SWPM SAP fazendo um distribuído NW 7.5 da SAP instalar. Os itens individuais são explicados e mostrados no formulário de capturas de tela de forma mais detalhada em todo o artigo:

* criar uma rede virtual Azure que incluirá as VMs de dois teste 
* implantar duas VMs Azure com o sistema operacional SLES 12 SP1 através do modelo do Gerenciador de recursos do Azure 
* Anexar dois discos de armazenamento padrão para o servidor de aplicativo máquina virtual (por exemplo, 75GB e 500GB)
* Anexar quatro discos para o servidor de banco de dados do HANA máquina virtual - 2 armazenamento padrão como o servidor de aplicativo máquina virtual + 2 discos de armazenamento de premium (por exemplo, 2x512GB)
* Dependendo do tamanho e/ou produtividade requisitos anexar vários discos e criar volumes distribuídos ou usando lvm ou mdadm em nível de sistema operacional dentro da VM
* Crie sistemas de arquivos XFS os discos anexados / lógico volumes
* Monte os novos sistemas de arquivo XFS no nível do sistema operacional. Use um sistema de arquivos para manter todo o software do SAP e o outro por exemplo, para o diretório de sapmnt e talvez backups. Sobre o banco de dados do SAP HANA montagem do servidor o XFS sistemas de arquivos nos discos de armazenamento do premium como /hana e /usr/sap é tudo necessária para evitar que o sistema de arquivos de raiz que não é muito grande em Linux Azure VMs preenche para cima
* Digite os endereços de ip local do teste VMs no/etc/hosts
* Insira o parâmetro nofail no /etc/fstab.
* parâmetros de núcleo de conjunto de acordo com a anotação de SAP HANA-SLES-12 (veja detalhes adicionais para baixo na seção de parâmetros de núcleo)
* Adicionar espaço de troca
* Se quisesse - instale uma área de trabalho gráfica no teste VMs. Caso contrário, use uma instalação sapinst remoto
* baixar o software SAP do SAP service marketplace
* Criar grupo "sapsys" com a id do grupo 1001 na VM HANA DB Server
* instalar o SAP HANA no servidor DB máquina virtual usando hdblcm
* instalar a instância do SAP ASCS na máquina virtual do servidor de aplicativo
* compartilhar o diretório sapmnt via NFS entre o teste VMs (servidor de aplicativo máquina virtual é o servidor NFS)
* instalar a instância de banco de dados, incluindo HANA via SWPM na máquina virtual do servidor de banco de dados
* instalar o PAS no servidor do aplicativo máquina virtual
* Iniciar MC SAP e se conectar por exemplo, por meio de interface usuário SAP / HANA Studio 




## <a name="prepare-azure-vms-for-manual-installation-of-sap-hana"></a>Preparar Azure VMs instalação manual do HANA SAP

Este capítulo sobre como preparar as VMs do Azure para instalação manual do SAP HANA consiste em cinco seções que abrangem os seguintes tópicos:

* Configuração de disco
* Parâmetros de núcleo
* Sistemas de arquivos
* / etc/hosts
* / etc//etc/fstab


### <a name="disk-setup"></a>Configuração de disco

O sistema de arquivos raiz em uma VM Linux no Azure é de tamanho limitado. Portanto, é necessário anexar espaço em disco adicional a uma máquina virtual para a execução do SAP. No caso de um servidor de aplicativo do SAP que máquina virtual usado em um ambiente de protótipo/demonstração puro há problemas em usar discos de armazenamento do Azure padrão. Enquanto os arquivos de log e dados de SAP HANA DB - discos de armazenamento do Azure Premium serão utilizados mesmo em um cenário de não produção.

Veja alguns detalhes sobre como anexar discos a uma VM Linux [aqui](virtual-machines-linux-add-disk.md)

Quando se trata de cache de disco Azure - um deve usar "Nenhum" para discos que serão usados para armazenar os logs de transação HANA. Para HANA arquivos de dados é okey usar o cache de leitura. Como HANA é um banco de dados na memória depende o padrão de uso geral quanto o cache de leitura no nível de disco Azure melhora o desempenho (por exemplo, iniciando HANA e ler dados do disco na memória).

Ver detalhes sobre o armazenamento do Azure Premium [aqui](../storage/storage-premium-storage.md)

[Aqui](https://github.com/Azure/azure-quickstart-templates) você encontrar modelos de json de exemplo para criar VMs.
O "101-máquina virtual-simples-linux" mostra como um modelo básico parece incluindo a seção de armazenamento que adiciona um disco de dados de 100GB.

[Este artigo](virtual-machines-linux-sap-on-suse-quickstart.md) inclui algumas informações sobre como localizar uma imagem SUSE via Powershell ou CLI e a prioridade para anexar um disco via UUID.


Dependendo do tamanho dos requisitos de sistema e produtividade talvez seja necessário anexar vários discos em vez de um e posteriormente criar uma faixa entre esses discos no nível do sistema operacional. Estes são os dois aspectos por um criaria uma faixa de disco em vários discos Azure:

* aumentar a produtividade
* precisa de um único sistema de arquivos > 1TB como o limite de tamanho de disco Azure atual é 1TB (a partir de 2016 de julho)


Obter mais informações sobre as duas ferramentas principais para configurar distribuição podem ser encontradas aqui:

[Artigo sobre como usar mdadm para configurar raid de software Linux em uma máquina virtual do Azure](virtual-machines-linux-configure-raid.md)

[Artigo sobre como configurar o Gerenciador de Volume lógico em uma máquina virtual do Azure de Linux](virtual-machines-linux-configure-lvm.md)



![](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

No teste dois discos de armazenamento padrão Azure ambiente foram anexados ao servidor de aplicativo do SAP máquina virtual. Foi usada para armazenar todo o software do SAP para instalação (por exemplo, NetWeaver 7.5, interface de usuário do SAP, SAP HANA … ) e o outro para tem espaço suficiente para tudo o que podem ser necessário (por exemplo, fazer backup, dados de teste), além de diretório sapmnt (por exemplo, perfis SAP) para ser compartilhado entre todas as VMs que pertence à mesmo cenário SAP.

![](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

Ao contrário de servidor de aplicativo discos de máquina virtual quatro estavam conectados ao servidor SAP HANA máquina virtual. Como antes de dois discos eram usados para manter o software SAP (um também pode compartilhar o disco do software SAP via NFS) e ter espaço suficiente por exemplo, para backup. Os dois discos adicionais foram discos de armazenamento do Azure Premium para manter os arquivos de log e dados do SAP HANA bem como o diretório /usr/sap.


### <a name="kernel-parameters"></a>Parâmetros de núcleo


SAP HANA requer configurações específicas de núcleo de Linux que não fazem parte das imagens padrão Galeria Azure e precisam ser definida manualmente. Não há uma anotação específica do SAP que descreve as configurações. 


Observação SAP SAP HANA DB: SO configurações recomendadas para SLES 12 / SLES de 12 de aplicativos do SAP: [2205917 de anotação do SAP](https://launchpad.support.sap.com/#/notes/2205917)

Um tópico adicionais computação em cache de página relacionada à execução HANA SAP em SLES pode ser encontrado [aqui](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache) capítulo 6.1 núcleo: limite do Cache de página

Também há uma anotação de SAP sobre o limite de cache de página [1557506 de anotação do SAP](https://service.sap.com/sap/support/notes/1557506)

SLES 12 tem uma nova ferramenta que substitui o utilitário sapconf antigo. É "adm ajustado" e um perfil de SAP HANA especial está disponível. Um pode encontrar mais detalhes sobre esta ferramenta acompanhando os dois links abaixo.

Documentação SLES sobre adm ajustado perfil sap-hana pode ser encontrada [aqui](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)

Documentação SLES sobre adm ajustado perfil sap-hana - sistemas de ajuste de capítulo 6.2 para cargas de trabalho do SAP com ajustado-adm - podem ser encontradas [aqui](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)


![](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

Aqui um pode ver como "adm ajustado" alterado a transparent_hugepage, bem como os valores de numa_balancing acordo com as configurações necessárias do HANA SAP.


Para tornar as configurações de núcleo SAP HANA permanentes, é preciso usar grub2 em SLES 12. Mais informações sobre grub2 podem ser encontradas [aqui](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html)


![](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

Esta captura de tela mostra como as configurações de núcleo foram alteradas no arquivo de configuração e então "compiladas" via grub2-mkconfig


![](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

Outra opção seria alterar as configurações de via Yast e as configurações de parâmetro de núcleo de carregador de inicialização.


### <a name="filesystems"></a>Sistemas de arquivos 

![](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

Aqui um pode ver os sistemas de dois arquivos que foram criados no servidor de aplicativo de SAP máquina virtual na parte superior dos dois disquetes padrão de armazenamento do Azure anexado. Ambos os sistemas de arquivos são do tipo XFS e montado /sapdata e /sapsoftware.

Não é obrigatório para fazer isso. Há opções diferentes como estrutura o espaço em disco.
O aspecto mais importante é evitar que o sistema de arquivos raiz é executado sem espaço. 


![](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

Sobre a máquina virtual DB do SAP HANA é importante saber que durante uma instalação de banco de dados via sapinst (swpm) e usando apenas a opção de instalação "típica" simples-serão instalados questões por padrão em /hana e /usr/sap. A configuração padrão para backup de log do SAP HANA por exemplo está sob /usr/sap.
Como antes que ela seja chave para evitar que o sistema de arquivos raiz é executado sem espaço. Portanto, um deverá garantir que não há espaço suficiente em /hana e /usr/sap antes de instalar o SAP HANA via swpm.

[Este artigo](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) do SAP descreve o layout de sistema de arquivos padrão do HANA SAP 


![](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)

Ao instalar o SAP NetWeaver em uma imagem padrão de galeria SLES 12 Azure haverá uma mensagem que não há nenhum espaço de troca. Para eliminar essa mensagem um poderia ex.: adicionar manualmente um arquivo de troca conforme descrito neste documento via dd, mkswap e swapon. Basta procurar "Adicionando um trocar arquivo manualmente" [neste](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html) artigo

Outra opção é configurar o espaço de troca através do agente de Linux VM. Mais informações podem ser encontradas [aqui](virtual-machines-linux-agent-user-guide.md)


### <a name="etchosts"></a>/ etc/hosts

![](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

Outro aspecto importante antes de começar a instalar o SAP é para incluir os nomes de host e endereços IP das VMs SAP no arquivo /etc/hosts. Um deve implantar todas as VMs SAP dentro de uma rede virtual Azure e use os endereços IP internos.

### <a name="etcfstab"></a>/ etc//etc/fstab

![](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

Durante a fase de teste acontece seja uma boa ideia adicionar o parâmetro nofail para /etc/fstab. Se algo der errado com os discos a máquina virtual seria ainda surgirem e não travar no processo de inicialização. Mas uma tem serem observadas como nesse caso, o espaço em disco adicionais pode não estar disponível e processos podem preencher o sistema de arquivos raiz. Caso /hana seria ausentes HANA SAP não iniciar embora todo.


## <a name="install-graphical-gnome-desktop-on-sles-12"></a>Instalar gráfica desktop Gnome em SLES 12

Este capítulo consiste em dois secitions que abrange os seguintes tópicos:

* Instalação do desktop Gnome e xrdp no SLES 12
* Executando baseado em Java SAP MC usando o Firefox no SLES 12

Alternativas como Xterminal, VNC também poderia ser usada, mas a partir de setembro 2016 este artigo descreve somente xrdp.

### <a name="installation-of-gnome-desktop-and-xrdp-on-sles-12"></a>Instalação do desktop Gnome e xrdp no SLES 12

Especialmente para aqueles que têm o plano de fundo do Microsoft Windows e gostaria de usar uma área de trabalho gráfica diretamente no Linux VMs SAP para executar o Firefox, Sapinst, interface usuário SAP, SAP MC ou HANA Studio e talvez conectar-se para a máquina virtual via RDP de um computador Microsoft Windows, há uma maneira simples de fazer isso. Enquanto isso pode não ser apropriado, por exemplo, para um servidor de banco de dados de produção é okey para um ambiente de protótipo/demonstração puro. Aqui estão as etapas para instalar a área de trabalho Gnome em uma máquina virtual 12 do Azure SLES:

Instale o desktop gnome o seguinte comando (por exemplo, em uma janela de acabamento):

   zypper no padrão de -t gnome-basic

em seguida, instale o xrdp para permitir que a conexão para a máquina virtual via RDP:

   zypper em xrdp

Edite /etc/sysconfig/windowmanager e defina o gerenciador padrão do windows para Gnome:

   DEFAULT_WM = "gnome"

Execute o comando chkconfig para certificar-se de que xrdp é iniciado automaticamente após a reinicialização: 

  comando chkconfig-xrdp de nível 3 em

caso deve haver um problema com a conexão de RDP tente reiniciar (talvez ausência uma janela de acabamento):

  reiniciar /etc/xrdp/xrdp.sh

Caso a reinicialização xrdp como mencionado acima não funcionarão verificar se há um arquivo de .pid e removê-lo:

  Verifique /var/run e procure xrdp.pid   
  Remova-o e tente novamente a reinicialização



### <a name="sap-mc"></a>MC SAP


Para iniciar a gráfica MC de SAP baseada em Java sair do Firefox executado em uma máquina virtual 12 do Azure SLES depois de instalar o Gnome área conforme descrito na seção anterior um obterá um erro devido a falta navegador Java plug-in.

A URL para iniciar a MC SAP é <server>: 5 < instance_number > 13

Mais detalhes podem ser encontradas [aqui](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)


![](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

Na captura de tela acima um pode ver como a mensagem de erro poderá aparência quando o plug-in Java navegador está ausente. 

![](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

Uma opção para resolver o problema é simplesmente instalar o plug-in ausente via Yast.

![](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

A URL do SAP MC de repetição exibirá um pouco tempo de caixa de diálogo da primeira pedindo para ativar o plug-in.


Um problema adicional que talvez pop-up é uma mensagem de erro sobre um arquivo ausente: javafx.properties é bastante provável relacionados à instalação do Java 1,8 que é necessário para SAP interface gráfica 7.4

A versão do IBM Java Vista via Yast não inclui esse arquivo. A solução é um download de Java da Oracle.
Um artigo que fala sobre esse problema específico pode ser encontrado [aqui](https://scn.sap.com/thread/3908306)



## <a name="manual-sap-hana-installation-via-swpm-as-part-of-a-netweaver-75-installation"></a>Instalação manual do SAP HANA via SWPM como parte de uma instalação NetWeaver 7.5


Lista de capturas de tela a seguir mostra as principais etapas de instalação do SAP NetWeaver 7.5 e SAP HANA SP12 via SWPM (sapinst). Como parte de um 7.5 NW instalação SWPM tem os recursos de também instalar o banco de dados do HANA como uma única instância.


![](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

Para o teste de amostra apenas um servidor de aplicativo ABAP ambiente foi instalado. A opção "Sistema distribuído" foi usada para instalar a instância ASCS e a instância do servidor de aplicativo principal em uma máquina virtual do Azure e SAP HANA como o sistema de banco de dados em um outro VM do Azure.


![](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

Depois que a instância ASCS está instalada na máquina virtual do servidor de aplicativo e está definida como "verde" MC o SAP no diretório de sapmnt que inclui por exemplo, o diretório de perfil do SAP deve ser compartilhado com o servidor de banco de dados do SAP HANA máquina virtual.
Etapa de instalação DB precisa acessar essas informações. A melhor maneira é usar NFS, que podem ser configuradas usando Yast.


![](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

No aplicativo do servidor máquina virtual do diretório de sapmnt deve ser compartilhado por meio de NFS usando o opções "rw" bem como "no_root_squash". Padrão é "lin" e "root_squash", que pode levar a problemas ao instalar a instância de banco de dados.


![](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

No servidor de banco de dados do SAP HANA máquina virtual do sapmnt compartilhar do servidor aplicativo máquina virtual precisa ser configurada via "Cliente NFS" (por exemplo, com a Ajuda de Yast)


![](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

Depois, é preciso fazer login no servidor de banco de dados do SAP HANA máquina virtual e iniciar SWPM para executar a próxima etapa de uma instalação NW 7.5 distribuída - "Instância de banco de dados".


![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Relacionados à instalação SAP HANA lá não está na verdade muito inserir depois de selecionar uma instalação "típica". Além disso, o caminho na mídia de installatiom um tem que inserir um SID DB, o nome do host, o número de instância e a senha de administrador de sistema do banco de dados.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Próxima etapa é a digitar a senha para o esquema DBACOCKPIT.



![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Em seguida, vem a pergunta para a senha de esquema SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

No final deve haver apenas verdes marcas de seleção na frente de cada fase do processo de instalação DB e a caixa de mensagem que surge deve dizer "execução de... Instância de banco de dados foi concluída".

 
![](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

Após a instalação bem-sucedida a MC SAP também devem mostrar a instância de banco de dados como "verde" e a lista completa de processos de SAP HANA (por exemplo, hdbindexserver, hdbcompileserver)


![](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

Esta captura de tela mostra partes da estrutura de arquivos em /hana/shared que SWPM criada durante a instalação do HANA. Não houve nenhuma opção para especificar um caminho diferente. Portanto é tão importante montar espaço em disco adicional em /hana antes da instalação do SAP HANA via SWPM para evitar que o sistema de arquivos raiz é executado sem espaço livre.


![](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

Aqui um pode ver a mesma coisa conforme descrito antes do diretório de /usr/sap.


![](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

A última etapa da instalação do ABAP distribuída é a "instância de servidor de aplicativo de primária"


![](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

Assim que o PAS bem como interface usuário SAP tem instalado um pode verificar por meio da transação "dbacockpit" que tudo esteja direita com a instalação do HANA SAP.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Como uma última etapa um poderia instalar SAP HANA Studio no servidor de aplicativo de SAP máquina virtual e conectar-se a instância do SAP HANA executada na máquina virtual do servidor de banco de dados.




## <a name="manual-sap-hana-installation-via-hana-life-cycle-manager-tool-hdblcm"></a>Instalação manual do SAP HANA via HANA hdblcm de ferramenta de Gerenciador de ciclo de vida


Além de instalar o SAP HANA como parte de uma instalação distribuída via SWPM também é possibe primeiro instalar HANA autônomo usando hdblcm e instalar por exemplo, SAP NetWeaver 7.5 posteriormente. A lista de capturas de tela abaixo mostra como isso funciona.

Aqui estão três fontes de informações sobre a ferramenta de hdblcm HANA:

[Escolhendo a HDBLCM de HANA SAP correto para a sua tarefa](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)

[Ferramentas de gerenciamento de ciclo de vida do SAP HANA](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)

[SAP HANA servidor guia de instalação e atualização](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)



![](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

Para evitar problemas posteriormente com uma configuração de id do grupo padrão para o \<HANA SID\>usuário adm (criado pela ferramenta hdblcm) um deve definir um novo grupo chamado "sapsys" com a id do grupo 1001 antes de instalar HANA SAP usando hdblcm.




![](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

Iniciando hdblcm pela primeira vez haverá um menu Iniciar simples onde um tem para selecionar item 1 "instalar novo sistema"



![](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

Esta captura de tela um possível ver todas as opções de chave que foram inseridas antes. Importante - diretórios que foram nomeados para volumes de dados e log do HANA, bem como o caminho de instalação (/ hana/compartilhados neste exemplo) e sap/usr/não deve fazer parte do sistema de arquivos raiz mas pertencem Azure discos de dados que foram anexados para a máquina virtual, conforme descrito na seção de configuração de máquina virtual do Azure. Isso irá evitar o risco de que o sistema de arquivos raiz pode ficar sem espaço.
Um também pode ver que o usuário de administrador do HANA tem id de usuário 1005 e faz parte do grupo sapsys (id 1001) que foi definido antes da instalação.



![](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

Um pode verificar a HANA \<HANA SID\>detalhes do usuário adm (azdadm neste exemplo) no/etc/senhas



![](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

Depois de instalar o SAP HANA usando hdblcm pode ser visto no SAP HANA Studio. O esquema de SAPABAP1 que inclui por exemplo, todas as tabelas de SAP NetWeaver ainda não está disponível.



![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Depois de instalar o SAP HANA um pode instalar o SAP NetWeaver sobre elas. Neste exemplo, que ele foi feito usando uma "instalação distribuída" via SWPM conforme descrito na seção correspondente acima.
Quando a instalação a instância de banco de dados via SWPM um apenas tem que inserir os mesmos dados antes com hdblcm (por exemplo, nome do host, HANA SID, número de instância). SWPM será, em seguida, usar a instalação existente do HANA e adicionar esquemas adicionais.



![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Esta é a imagem da etapa de instalação SWPM onde uma tem de inserir dados sobre o esquema DBACOCKPIT.


![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Em seguida, SWPM espera inserir dados sobre o esquema de SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Após concluir a instalação de instância do banco de dados SWPM um poderá ver o esquema de SAPABAP1 no Studio HANA.



![](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

E finalmente após a instalação do servidor de aplicativo do SAP e interface usuário SAP um deve ser capaz de verificar a instância HANA DB com transação "dbacockpit".




## <a name="general-information-related-to-sap-azure-certifications-running-sap-hana-on-azure-and-sap-software-download"></a>Informações gerais relacionadas ao certificações Azure SAP, executando HANA SAP em download de software do Azure e SAP

* documento SAP Azure geral sobre como executar SAP no Azure com o sistema operacional Windows no modo clássico: [Usando o SAP em máquinas virtuais do Windows no Azure](virtual-machines-windows-classic-sap-get-started.md)

* informações sobre modelos SAP existentes para uso pelos clientes: [Modelos de início rápido do Azure para SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)

* documento SAP Azure geral sobre como executar SAP no Azure com SO Linux no Gerenciador de recursos do Azure modelo: [Usando o SAP em Linux VMs (máquinas virtuais)](virtual-machines-linux-sap-get-started.md)

* diretório de hardware SAP HANA certificado que lista os tipos de máquina virtual do Azure são suportados para produção: [Certified SAP HANA® Hardware Directory](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)

* obter informações sobre tamanhos de máquina virtual especialmente para cargas de trabalho de Linux: [tamanhos para máquinas virtuais no Azure](virtual-machines-linux-sizes.md)

* Observação SAP que lista todos com produtos SAP no Azure e suporte a tipos de máquina virtual do Azure para SAP: [1928533 de anotação do SAP](https://launchpad.support.sap.com/#/notes/1928533/E)

* Observação SAP sobre SAP "monitoramento avançado" com VMs Linux no Azure: [2191498 de anotação do SAP](https://launchpad.support.sap.com/#/notes/2191498/E)

* SAP HANA oferecendo no Azure "Instâncias grandes". É importante entender o que não se trata sobre como executar HANA SAP em VMs do Azure, mas em um híbrido ambiente onde os servidores de aplicativo SAP executam no Azure VMs mas HANA SAP é executado em servidores de depender: [2316233 de anotação do SAP](https://launchpad.support.sap.com/#/notes/2316233/E)

* Observação SAP com informações sobre SAPOSCOL no Linux: [1102124 de anotação do SAP](https://launchpad.support.sap.com/#/notes/1102124/E)

* Chave monitoramento métricas para SAP no Microsoft Azure: [SAP anotação 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)

* Informações sobre o Gerenciador de recursos do Azure: [Visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md)

* Informações sobre como implantar VMs Linux por meio de modelos: [implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de recursos do Azure e CLI Azure](virtual-machines-linux-cli-deploy-templates.md)

* Comparação de implantação modela entre o Gerenciador de recursos do Azure e clássico: [Gerenciador de recursos do Azure versus implantação clássica: Noções básicas sobre modelos de implantação e o estado de seus recursos](../resource-manager-deployment-model.md)

* Baixe NetWeaver 7.5 para Linux/HANA do SAP Service Marketplace:![](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)

* Baixe HANA SP12 plataforma Edition do SAP Service Marketplace:![](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

