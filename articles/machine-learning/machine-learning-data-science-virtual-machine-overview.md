<properties
    pageTitle="O que é uma máquina de Virtual de ciência de dados? | Microsoft Azure"
    description="Saiba cenários-chave, recursos e como começar a trabalhar com dados ciência máquinas virtuais, um ambiente e Kit de ferramentas pronta para análise."
    keywords="ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas de ciência de dados, linux ciência de dados"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="bradsev" />


# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>Introdução ao baseado em nuvem dados ciência Máquina Virtual para Linux e Windows

A máquina de Virtual de ciência de dados é uma imagem de máquina virtual personalizada na nuvem do Azure da Microsoft criado especificamente para fazendo ciência de dados. Ela tem muitos ciência de dados populares e outras ferramentas pré-instalado e previamente configurado para dar início a criação de aplicativos inteligentes para análise avançada. Ele está disponível no Windows Server 2012 ou no Linux baseados em OpenLogic 7.2 CentOS versões. 

Este tópico descreve o que pode fazer com a máquina virtual ciência de dados, descreve alguns dos principais cenários para usar a máquina virtual, detalha principais recursos disponíveis nas versões do Windows e Linux e fornece instruções sobre como começar a usá-los.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>O que posso fazer com a máquina Virtual dados ciência?

O objetivo da máquina de Virtual de ciência de dados é fornecer profissionais de dados e em todos os níveis de habilidade funções com um ambiente de ciência de dados sem conflito. Esta máquina virtual poupa tempo considerável que você levaria se você tinha implementadas em um ambiente comparável por conta própria. Em vez disso, inicie seu projeto de ciências dados imediatamente em uma instância de máquina virtual recém-criado. 

A máquina virtual ciência de dados foi criada e configurada para trabalhar com um cenários de uso amplo. Você pode dimensionar seu ambiente para cima ou para baixo conforme mudam de seu projeto. Você é capaz de usar seu idioma preferencial tarefas de ciência de dados do programa. Você pode instalar outras ferramentas e personalizar o sistema para exatamente às suas necessidades.
 
## <a name="key-scenarios"></a>Principais cenários
Esta seção sugere alguns cenários importantes para o qual a máquina virtual ciência de dados pode ser implantada.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Área de trabalho de análise pré-configurado na nuvem

A máquina virtual ciência de dados fornece uma configuração de linha de base para as equipes de ciência de dados pretendendo substitua suas áreas de trabalho locais uma área de trabalho de nuvem gerenciados. Essa linha de base garante que todos os cientistas de dados em uma equipe tenham uma configuração consistente com o qual deseja verificar experiências e promover colaboração. Ele também reduz os custos, reduzindo a carga de administrador do sistema e salvando no período de tempo necessário para avaliar, instalar e manter os vários pacotes de software necessários para fazer a análise avançada.  

### <a name="data-science-training-and-education"></a>Educação e treinamento de ciência de dados

Treinadores Enterprise e professores que ensinam ciência de dados classes geralmente fornecem uma imagem de máquina virtual para garantir que seus alunos tenham uma configuração consistente e que as amostras trabalhar previsíveis. A máquina virtual ciência de dados cria um ambiente sob demanda com uma instalação consistente que facilita os desafios de suporte e incompatibilidade. Casos onde esses ambientes precisam ser criados com frequência, especialmente para mais curtos treinamentos, beneficiam substancialmente.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacidade de elástica sob demanda para projetos em grande escala

Dados de ciências hackathons/competição ou exploração e modelagem de dados em grande escala exigem dimensionados capacidade de hardware, normalmente de curta duração. A máquina virtual ciência de dados pode ajudar a replicação do ambiente de ciência de dados rapidamente sob demanda, em servidores escaladas check-out que permitem experiências que exigem recursos de computação de alta capacidade para ser executado.

### <a name="short-term-experimentation-and-evaluation"></a>Avaliação e experimento curto prazo

A máquina virtual ciência de dados pode ser usado para avaliar ou aprender ferramentas como Microsoft R Server, SQL Server, Visual Studio tools, Jupyter, profunda de aprendizagem / ML toolkits e novas ferramentas populares na comunidade com mínimo esforço de instalação. Desde que a máquina virtual ciência de dados podem ser configurada rapidamente, ele pode ser aplicado em outros cenários de uso de curto prazo como replicar experiências publicadas, executando demonstrações, orientações a seguir em sessões on-line ou tutoriais de conferência.


## <a name="whats-included-in-the-data-science-vm"></a>O que está incluído na máquina virtual ciência de dados?

A máquina de Virtual de ciência de dados tem muitas ferramentas de ciência de dados populares já instalado e configurado. Ele também inclui ferramentas que tornam mais fácil trabalhar com vários produtos de dados e análise Azure. Você pode explorar e criar modelos de previsão em grande escala conjuntos de dados usando o Microsoft R Server ou SQL Server 2016. Um host de outras ferramentas da comunidade do código-fonte aberto e da Microsoft também são os blocos de anotações e o código incluído, bem como exemplo. A tabela a seguir relaciona e compara os componentes principais incluídos nas edições do Windows e Linux da máquina de Virtual de ciência de dados.


|**Windows Edition** | **Linux Edition** |
|----------------|---------------|
|Microsoft R Server Developer Edition | Microsoft R Server Developer Edition |
|Anaconda Python 2.7, 3.5 | Anaconda Python 2.7, 3.5 |
|Servidor de bloco de anotações de Jupyter (R, Python) | JupyterHub: Blocos de anotações Jupyter multiusuário (R, Python, Julia) |
|SQL Server 2016 Developer Edition: Análise de Scalable no banco de dados com os serviços de R | Postgres, esquilo SQL (ferramenta de banco de dados), drivers do SQL Server e linha de comando (bcp, sqlcmd) |
|Edição de comunidade do Visual Studio (IDE) de 2015 </br> -Azure HDInsight (Hadoop), Lucerne de dados, ferramentas de dados do SQL Server </br> -Ferramentas Node, Python e R para Visual Studio |IDEs e editores </br> -Eclipse com plug-in do Kit de ferramentas Azure </br> -Emacs (com ESS, auctex) gedit |
|Área de trabalho do Power BI | -- |
|Ferramentas de aprendizado de máquina </br> -Integração com o aprendizado de máquina Azure </br> -CNTK (profunda aprendizagem/AI) </br> -Xgboost (popular ferramenta ML na competição de ciência de dados) </br> -Vowpal Wabbit (aprendiz on-line rápido) </br> -Rattle (visual dados de início rápido e ferramenta de análise) </br> -Mxnet (profunda aprendizagem/AI) | Ferramentas de aprendizado de máquina </br> -Integrações com aprendizado de máquina Azure </br> -CNTK (profunda aprendizagem/AI) </br> -Xgboost (popular ferramenta ML na competição de ciência de dados) </br> -Vowpal Wabbit (aprendiz on-line rápido) </br> -Rattle (visual dados de início rápido e ferramenta de análise)  |
| SDKs para acessar o Azure e conjunto de inteligência de Cortana de serviços | SDKs para acessar o Azure e conjunto de inteligência de Cortana de serviços |
| Ferramentas para movimentação de dados e gerenciamento de recursos do Azure e grandes dados: Azure Storage Explorer, CLI, PowerShell, AdlCopy (Lucerne de dados do Azure), AzCopy, dtui (para DocumentDB), Gateway de gerenciamento de dados do Microsoft | Ferramentas para movimentação de dados e gerenciamento de recursos do Azure e grandes dados: Azure Storage Explorer, CLI |
| Gito, plug-in do Visual Studio Team Services | Gito |
| Porta do Windows de mais populares utilitários de linha de comando de Linux/Unix acessados por meio de prompt de comando/GitBash | -- |



## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>Como começar com a máquina virtual ciência de dados do Windows

- Crie uma instância da máquina virtual no Windows navegando até [esta página](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) e selecionando o botão **criar Virtual Machine** verde.
- Entrar para a máquina virtual da sua área de trabalho remota usando as credenciais que você especificou quando você criou a máquina virtual.
- Para descobrir e iniciar as ferramentas disponíveis, clique no menu **Iniciar** .


## <a name="get-started-with-the-linux-data-science-vm"></a>Começar com a máquina virtual ciência de dados Linux

- Crie uma instância da máquina virtual no Linux (baseado em OpenLogic CentOS) navegando até [esta página](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) e selecionando o botão **criar Virtual Machine** .
- Entrar para a máquina virtual de um cliente SSH, como acabamento ou SSH comando, usando as credenciais que você especificou quando você criou a máquina virtual.
- No prompt shell, insira informações de mais dsvm.
- Para uma área de trabalho gráfica baixar o cliente X2Go para sua plataforma de cliente [aqui](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) e siga as instruções do documento de máquina virtual do Linux dados ciência [provisionar a máquina Virtual de ciência de dados de Linux](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client).


## <a name="next-steps"></a>Próximas etapas

### <a name="for-the-windows-data-science-vm"></a>Para a ciência de dados do Windows máquina virtual

- Para obter mais informações sobre como executar ferramentas específicas disponíveis na versão do Windows, consulte [provisionar o Microsoft dados ciência Virtual Machine](machine-learning-data-science-provision-vm.md) e
-  Para obter mais informações sobre como realizar várias tarefas necessárias para o seu projeto de ciências dados sobre a máquina virtual Windows, consulte [Dez coisas que você pode fazer na ciência de dados Máquina Virtual](machine-learning-data-science-vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>Para a ciência de dados Linux máquina virtual

- Para obter mais informações sobre como executar ferramentas específicas disponíveis na versão Linux, consulte [provisionar a máquina Virtual de ciência de dados de Linux](machine-learning-data-science-linux-dsvm-intro.md).
- Para instruções passo a passo que mostra como realizar várias tarefas comuns de ciências dados com a VM Linux, consulte [ciência de dados no computador de Virtual em ciências Linux dados](machine-learning-data-science-linux-dsvm-walkthrough.md).
