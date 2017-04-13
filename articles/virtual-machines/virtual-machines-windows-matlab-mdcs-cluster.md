<properties
   pageTitle="MATLAB clusters em máquinas virtuais | Microsoft Azure"
   description="Usar máquinas virtuais do Microsoft Azure para criar os clusters de servidor de computação do MATLAB distribuído para executar suas cargas de trabalho MATLAB paralelas pesados"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mscurrell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Windows"
   ms.workload="infrastructure-services"
   ms.date="05/09/2016"
   ms.author="markscu"/>

# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Criar clusters de servidor de computação distribuída MATLAB em VMs do Azure 

Use máquinas virtuais do Microsoft Azure para criar um ou mais clusters MATLAB distribuído computação servidor para executar suas cargas de trabalho MATLAB paralelas pesados. Instale o software de servidor de computação distribuída MATLAB em uma máquina virtual para usar como uma imagem base e usar um modelo de início rápido Azure ou script do PowerShell do Azure (disponível no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) para implantar e gerenciar o cluster. Após a implantação, conectar-se ao cluster para executar suas cargas de trabalho. 

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Sobre MATLAB e servidor de computação de MATLAB distribuído 

A plataforma [MATLAB](http://www.mathworks.com/products/matlab/) é otimizada para solucionar problemas de engenharia e científicos. MATLAB com simulações em grande escala e tarefas de processamento de dados possível utilizar produtos de computação paralela de MathWorks para acelerar suas cargas de trabalho pesados aproveitando clusters de computadores e serviços de grade. [Ferramentas de computação paralela](http://www.mathworks.com/products/parallel-computing/) permite aos usuários MATLAB paralelo aplicativos e tirar proveito de núcleos de vários processadores, GPUs e calcular clusters. [Servidor de computação distribuída MATLAB](http://www.mathworks.com/products/distriben/) permite aos usuários MATLAB utilizar vários computadores em um cluster de cálculo. 


Usando o Azure máquinas virtuais, você pode criar clusters de servidor de computação distribuída MATLAB que possuem os mesmos mecanismos disponíveis para submeter o trabalho paralelo como clusters de local, como trabalhos interativos, trabalhos em lotes, tarefas independentes e tarefas de comunicação. Usando o Azure em conjunto com a plataforma MATLAB tem muitos benefícios em comparação com o provisionamento e usando tradicionais locais hardware: tamanhos de um intervalo de máquina virtual, criação de clusters sob demanda para que você paga somente para os recursos de computação uso e a capacidade de testar modelos em escala.  

## <a name="prerequisites"></a>Pré-requisitos

* **Computador cliente** - você precisará de um computador cliente baseado no Windows para se comunicar com o Azure e o cluster de servidor de computação distribuída MATLAB após a implantação. 

* **Azure PowerShell** - consulte [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) instalá-lo no computador cliente. 

* **Assinatura do azure** - se você não tiver uma assinatura, você pode criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos. Clusters maiores, considere uma assinatura flexível ou outras opções de compra. 

* **Cota de cores** - talvez você precise aumentar a cota de núcleo para implantar um cluster grande ou mais de um cluster de servidor de computação distribuída MATLAB. Aumentar uma cota, [Abra uma solicitação de suporte online do cliente](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sem nenhum custo. 

* **MATLAB, ferramentas de computação em paralelo e licenças de servidor de computação distribuída MATLAB** - os scripts presumem que o [Gerenciador de licenças hospedado MathWorks](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) é usado para todas as licenças.  

* **Software de servidor de computação distribuída MATLAB** - será instalado em uma máquina virtual que será usada como a imagem de máquina virtual base para o cluster VMs. 


## <a name="high-level-steps"></a>Etapas de nível altas

Para usar o Azure máquinas virtuais para os clusters de servidor de computação distribuída MATLAB, as seguintes etapas de alto nível são necessárias. Instruções detalhadas estão na documentação que acompanha o modelo de início rápido e scripts no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Criar uma imagem de máquina virtual base**  
    * Baixar e instalar o software de servidor de computação distribuída MATLAB nesta máquina virtual. 

    >[AZURE.NOTE]Esse processo pode levar algumas horas, mas você só precisa fazer isso vez para cada versão do MATLAB você usar.   
    
2. **Criar um ou mais clusters**  
    * Use o script PowerShell fornecido ou use o modelo de início rápido para criar um cluster da imagem máquina virtual base.   
    * Gerenciar os clusters usando o script PowerShell fornecido que permite que você listar, pausar, retomar e excluir clusters. 
 
## <a name="cluster-configurations"></a>Configurações de cluster 

Atualmente, o modelo e um script de criação de cluster permitem que você criar uma topologia de servidor de computação distribuída MATLAB única. Se desejar, crie um ou mais clusters adicionais, com cada cluster ter um número diferente de trabalhador VMs, usando diferentes tamanhos de máquina virtual e assim por diante. 

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB cliente e cluster no Azure 

O nó de cliente do MATLAB, nó Agendador de trabalho MATLAB e nós de "Trabalho" servidor de computação distribuída MATLAB são todos configurados como Azure VMs em uma rede virtual, conforme mostrado na figura a seguir. 

![Topologia de cluster](./media/virtual-machines-windows-matlab-mdcs-cluster/mdcs_cluster.png)

* Para usar o cluster, conecte área de trabalho remota para o nó de cliente. O nó de cliente executa o cliente MATLAB. 

* O nó de cliente possui um compartilhamento de arquivo que possa ser acessado por todos os funcionários.

* Gerenciador de licenças hospedado MathWorks é usado para as verificações de licença para todo o software MATLAB. 

* Por padrão, um operador de servidor de computação distribuída MATLAB por núcleo é criado no trabalhador VMs, mas você pode especificar qualquer número. 


## <a name="use-an-azure-based-cluster"></a>Use um Cluster com base no Azure 

Como com outros tipos de servidor de computação distribuída MATLAB clusters, você precisa usar o Gerenciador de perfis do Cluster no cliente do MATLAB (no cliente máquina virtual) para criar um perfil de cluster MATLAB Agendador de trabalho.

![Gerenciador de perfis do cluster](./media/virtual-machines-windows-matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Próximas etapas

* Para obter instruções detalhadas implantar e gerenciar clusters de servidor de computação distribuída MATLAB no Azure, consulte o repositório de [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) que contém os modelos e scripts. 

* Vá para o [site de MathWorks](http://www.mathworks.com/) para documentação detalhada para MATLAB e servidor de computação distribuída MATLAB.
