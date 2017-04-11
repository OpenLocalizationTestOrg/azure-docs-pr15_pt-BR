<properties 
    pageTitle="Provisionar a máquina de Virtual de ciência de dados Microsoft | Microsoft Azure" 
    description="Configurar e criar uma máquina de Virtual de ciência de dados no Azure fazer a análise e aprendizado de máquina." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="bradsev" />


# <a name="provision-the-microsoft-data-science-virtual-machine"></a>Provisionar a máquina de Virtual de ciência de dados Microsoft

O Microsoft dados ciência Virtual Machine é uma imagem do Azure virtuais de máquina pré-instalado e configurado com diversas ferramentas populares que são comumente usadas para a análise de dados e aprendizado de máquina. As ferramentas incluídas são:

- Microsoft R Server Developer Edition
- Distribuição de Python anaconda
- Bloco de anotações de Jupyter (com R, kernels Python)
- Edição de comunidade do Visual Studio
- Área de trabalho do Power BI
- SQL Server 2016 Developer Edition
- Ferramentas de aprendizado de máquina
    - [Kit de ferramentas de computação de rede (CNTK)](https://github.com/Microsoft/CNTK): uma profunda Kit de ferramentas de software da Microsoft Research de aprendizagem.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): uma sistema com suporte técnicas como online, hash, allreduce, reduções, learning2search, ativo, de aprendizado de máquina rápida e interativa de aprendizado.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): uma ferramenta fornecendo implementação de árvore aumentadas rápida e precisa.
    - [Rattle](http://rattle.togaware.com/) (a R analítico ferramenta para aprender facilmente): uma ferramenta que torna a introdução ao análises de dados e aprendizado em R fácil, com a exploração de dados baseada em interface gráfica, modelagem e análise com geração automática de código de R de máquina.
    - [mxnet](https://github.com/dmlc/mxnet): uma estrutura de aprendizagem profunda projetada para eficiência e flexibilidade
- Bibliotecas de R e Python para usam em aprendizado de máquina do Azure e outros serviços do Azure
- Gito incluindo Bash gito para trabalhar com repositórios de código de origem, incluindo o GitHub, serviços de equipe do Visual Studio
- Portas do Windows de várias populares Linux utilitários de comando (incluindo awk, sed, perl, grep, localizar, wget, Ondulação etc) acessado por meio de prompt de comando. 


Fazer ciência de dados envolve a iteração em uma sequência de tarefas: localizar, carregar e pré-processando dados, criação e teste modelos e implantar os modelos para consumo em aplicativos inteligentes. Cientistas de dados usam uma variedade de ferramentas para concluir essas tarefas. Pode ser muito demorado para encontrar as versões apropriadas do software e baixe e instale-os. O Microsoft dados ciência Virtual Machine pode facilitar essa carga, fornecendo uma imagem de prontos para uso que pode ser provisionada no Azure com todas as diversas populares ferramentas pré-instalado e configurado. 

O Microsoft dados ciência Virtual Machine jump-starts seu projeto de análise. Ele permite trabalhar em tarefas em vários idiomas, incluindo R, Python, SQL e c#. Visual Studio fornece um IDE para desenvolver e testar seu código que é fácil de usar. O SDK do Azure incluídos na máquina virtual permite que você crie seus aplicativos usando vários serviços na plataforma de nuvem da Microsoft. 

Não há nenhum encargos de software para esta imagem de máquina virtual de ciência de dados. Você só paga as taxas de uso Azure qual o tamanho da máquina virtual que você provisionar dependente. Obter mais detalhes sobre as taxas de computação podem ser encontradas na seção de detalhes de preços na página de [máquina de Virtual de ciência de dados](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) . 


## <a name="prerequisites"></a>Pré-requisitos

Antes de criar uma máquina Virtual do Microsoft dados ciência, você deve ter o seguinte:

- **Assinatura de um Azure**: para obter uma, consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Conta de armazenamento de um Azure**: para criar uma, consulte [criar uma conta de armazenamento do Azure](storage-create-storage-account.md#create-a-storage-account). Como alternativa, a conta de armazenamento pode ser criada como parte do processo de criação a máquina virtual se não quiser usar uma conta existente.


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Criar sua máquina de Virtual de ciência de dados da Microsoft

Aqui estão as etapas para criar uma instância dos dados ciência Máquina Virtual da Microsoft:

1.  Navegue até a máquina virtual listando no [portal do Azure](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2.   Selecione o botão **criar** na parte inferior para serem vistos em um assistente. ![configurar-dados-ciência-máquina virtual](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.   O assistente usado para criar a máquina Virtual do Microsoft dados ciência requer **entradas** para cada uma das **cinco etapas** enumerados à direita da figura. Aqui estão as entradas necessárias para configurar cada uma destas etapas:
    
    1.   **Noções básicas**
        1.   **Nome**: nome do seu servidor de ciência de dados que você está criando.
        2.   **Nome de usuário**: id de logon de conta de administrador.
        3.   **Senha**: senha da conta de administrador.
        4.   **Assinatura**: se você tiver mais de uma assinatura, selecione aquele no qual o computador está a serem criadas e cobrado.
        5.   **Grupo de recursos**: você pode criar um novo ou use um grupo existente.
        6.   **Local**: selecione o Centro de dados que é mais apropriado. Normalmente é o Centro de dados que tenha a maioria dos seus dados ou mais próximo ao seu local físico mais rápido para acesso de rede.
         
    2.   **Tamanho**: selecione um dos tipos de servidor que atenda às suas requisito funcional e restrições de custo. Você pode obter mais opções de tamanhos de máquina virtual selecionando "Exibir tudo".
    
    3.   **Configurações**:
        1.   **Tipo de disco**: escolha Premium se você preferir uma unidade de estado sólido (SSD), escolha "Padrão" else.
        2.   **Conta de armazenamento**: você pode criar uma nova conta de armazenamento do Azure em sua assinatura ou use um existente no mesmo *local* que foi escolhido na etapa **Noções básicas** do assistente.
        3.   **Outros parâmetros**: normalmente você simplesmente usar os valores padrão. Você pode focalizar o link informativo para obter ajuda sobre campos específicos, caso você queira considerar o uso de valores não padrão.

    4.   **Resumo**: Verifique se todas as informações inseridas estão corretas.
    5.   **Comprar**: clique em **comprar** para iniciar o provisionamento. Um link é fornecido com os termos da transação. A máquina virtual não tem quaisquer cobranças adicionais além a computação para o tamanho de servidor que você escolheu na etapa **tamanho** . 


>[AZURE.NOTE] O provisionamento deve levar cerca de 10 a 20 minutos. O status do provisionamento é exibido no portal do Azure.

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Como acessar a máquina Virtual do Microsoft dados ciências

Assim que a máquina virtual for criada, você pode área de trabalho remota nela, usando as credenciais de conta de administrador que você configurou na seção **Noções básicas do** anterior. 

Quando sua máquina virtual for criado e provisionado, você está pronto para começar a usar as ferramentas que estão instaladas e configuradas nele. Há blocos de menu Iniciar e ícones da área de trabalho para muitas das ferramentas. 

## <a name="how-to-create-a-strong-password-on-the-jupyter-notebook-server"></a>Como criar uma senha forte no servidor Jupyter bloco de anotações 

Para criar sua própria senha forte para o servidor de bloco de anotações de Jupyter instalado na máquina, execute o seguinte comando em um prompt de comando no computador de Virtual de ciência de dados.

    c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

Escolha uma senha forte quando solicitado.

Você vê o hash de senha no formato "sha1:xxxxxx" na saída. Copie esse hash de senha e substitua o hash existente que esteja no seu arquivo de configuração de bloco de anotações localizado em: **C:\ProgramData\jupyter\jupyter_notebook_config.py** com um nome de parâmetro ***c.NotebookApp.password***.

Substitua apenas a parte (xxxxxx) do valor de hash existente que está entre aspas. As aspas e o ***sha1:*** prefixo para o valor do parâmetro ambos precisam ser retidos.

Por fim, você precisa parar e reiniciar o servidor Jupyter, que está executando a máquina virtual como uma tarefa agendada do windows chamada **Start_IPython_Notebook**. Se a sua nova senha não for aceita após reiniciar esta tarefa, tente eliminar todos os processos de python em execução do Gerenciador de tarefas e reinicie a tarefa agendada. Como alternativa, tente reiniciar a máquina virtual.

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Ferramentas instaladas nos dados ciência Máquina Virtual da Microsoft

### <a name="microsoft-r-server-developer-edition"></a>Microsoft R Server Developer Edition
Se você quiser usar R para sua análise, a máquina virtual tem edition de desenvolvedor do Microsoft R Server instalado. Microsoft R Server é uma plataforma de análise amplamente implantadas corporativos com base em R que é suportado, escalável e segura. Suporte a uma variedade de estatísticas de grande volume, modelagem de previsão e recursos de aprendizado de máquina, o servidor de R oferece suporte a gama completa de análise – exploração, análise, visualização e modelagem. Usando e estendendo Abrir origem R, Microsoft R Server é totalmente compatível com scripts de R, funções e pacotes CRAN, para analisar dados em escala empresarial. Ele também endereços as limitações de memória do Abrir origem R adicionando processamento paralelo e em partes de dados. Isso permite executar a análise de dados muito maior do que o que couber na memória principal.  Visual Studio comunidade Edition incluído na VM contém as ferramentas de R para extensão do Visual Studio que fornece um IDE completo para trabalhar com R. Você também pode baixar e usar outros IDEs bem como [RStudio](http://www.rstudio.com). 

### <a name="python"></a>Python
Para o desenvolvimento usando Python, distribuição de Anaconda Python 2.7 e 3.5 foi instalada. Essa distribuição contém o Python base juntamente com cerca de 300 os pacotes de análise de dados, engenharia e matemática mais populares. Você pode usar ferramentas de Python para Visual Studio (PTVS) que está instalado dentro da edição de comunidade de 2015 do Visual Studio ou uma da IDEs agrupados com Anaconda como ocioso ou Spyder. Você pode iniciar um desses pesquisando na barra de pesquisa (**Win** + tecla**S** ).

>[AZURE.NOTE] Para apontar as ferramentas de Python para Visual Studio em Anaconda Python 2.7 e 3.5, você precisa criar ambientes personalizados para cada versão. Para definir esses caminhos de ambiente no Visual Studio 2015 comunidade Edition, navegue até **Ferramentas** -> **Python ferramentas** -> **Ambientes de Python** e clique em **+ personalizado**. 

Anaconda Python 2.7 estiver instalado em C:\Anaconda e Anaconda Python 3.5 é instalado em c:\Anaconda\envs\py35. Consulte a [documentação de PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) para obter etapas detalhadas. 

### <a name="jupyter-notebook"></a>Bloco de anotações de Jupyter
Distribuição de anaconda também vem com um bloco de anotações de Jupyter, um ambiente para compartilhar código e análise. Um servidor de bloco de anotações de Jupyter foi previamente configurado com Python 2, 3 Python e kernels R. Há um ícone da área de trabalho denominado "Jupyter bloco de anotações para iniciar o navegador para acessar o servidor de bloco de anotações. Se você estiver na máquina virtual por meio de área de trabalho remota, você também pode visitar [https://localhost:9999 /](https://localhost:9999/) para acessar o servidor de bloco de anotações de Jupyter quando conectado para a máquina virtual.
 
>[AZURE.NOTE] Continue se você receber quaisquer avisos de certificado. 

Podemos ter empacotados blocos de anotações de amostra em Python e em R. Os blocos de anotações de Jupyter mostram como trabalhar com a Microsoft R Server, SQL Server 2016 R Services (análise no banco de dados), Python e outras tecnologias Azure depois que você efetuar login no Jupyter. Você pode ver o link para os exemplos na home page do bloco de anotações depois autenticar no bloco de anotações de Jupyter usando a senha que você criou em uma etapa anterior. 

### <a name="visual-studio-2015-community-edition"></a>Edição do Visual Studio comunidade de 2015
Edição de comunidade Studio Visual instalada na máquina virtual. É uma versão gratuita do IDE popular da Microsoft que você pode usar para fins de avaliação e para pequenas equipes. Você pode fazer check-out os termos de licenciamento [aqui](https://www.visualstudio.com/support/legal/mt171547).  Abra o Visual Studio clicando duas vezes no ícone da área de trabalho ou no menu **Iniciar** . Você também pode procurar programas com **Win** + **S** e digitando "Visual Studio". Uma vez lá você pode criar projetos em linguagens como c#, Python, R, Node. Plug-ins também são instalados que tornam mais conveniente para trabalhar com os serviços do Azure como catálogo de dados do Azure e Azure HDInsight (Hadoop, Spark) Lucerne de dados do Azure. 

>[AZURE.NOTE] Você pode receber uma mensagem informando que seu período de avaliação expirou. Insira suas credenciais de conta da Microsoft ou crie uma nova conta gratuita para obter acesso para o Visual Studio comunidade Edition. 

### <a name="sql-server-2016-developer-edition"></a>Edição de desenvolvedor do SQL Server 2016
Uma versão de desenvolvedor do SQL Server 2016 com os serviços de R para executar a análise no banco de dados é fornecida na máquina virtual. Serviços de R fornecem uma plataforma para desenvolver e implantar aplicativos inteligentes. Você pode usar o idioma de R rico e poderoso e os muitos pacotes da comunidade para criar modelos e gerar previsões para os seus dados do SQL Server. Você pode manter analytics próxima dos dados porque R serviços (no banco de dados) integrar o idioma de R com o SQL Server. Isso elimina os custos e os riscos de segurança associados a movimentação de dados.

>[AZURE.NOTE] A edição de desenvolvedor do SQL Server 2016 só pode ser usada para fins de teste e desenvolvimento. Você precisa de uma licença para executá-lo em produção. 

Você pode acessar o SQL server abrindo o **SQL Server Management Studio**. Seu nome de máquina virtual é preenchida como nome do servidor. Usar a autenticação do Windows quando conectado como administrador no Windows. Uma vez no SQL Server Management Studio você pode criar outros usuários, criar bancos de dados, importar dados e executar consultas SQL. 

Para habilitar a análise no banco de dados usando o Microsoft R, execute o seguinte comando como uma ação no SQL Server management studio depois de fazer logon como administrador do servidor de horário. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
        
        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure 
Várias ferramentas Azure estão instaladas na máquina virtual:

- Há um atalho para acessar a documentação SDK do Azure. 
- **AzCopy**: usado para mover dados e sair de sua conta de armazenamento do Microsoft Azure. Para ver o uso, digite **Azcopy** em um prompt de comando para ver o uso. 
- **Gerenciador de armazenamento do Microsoft Azure**: usado para navegar pelos objetos que você armazenou dentro de sua conta de armazenamento do Azure e transferir dados para e do armazenamento do Azure. Você pode digitar o **Gerenciador de armazenamento** em pesquisa ou encontrá-lo no menu Iniciar do Windows para acessar essa ferramenta. 
- **Adlcopy**: usado para mover dados para Lucerne de dados do Azure. Para ver o uso, digite **adlcopy** em um prompt de comando. 
- **dtui**: usado para mover dados para e do Azure DocumentDB, um banco de dados NoSQL na nuvem. Digite **dtui** no prompt de comando. 
- **Gateway de gerenciamento de dados do Microsoft**: permite a movimentação de dados entre fontes de dados locais e nuvem. Ele é usado em ferramentas como fábrica de dados do Azure. 
- **Microsoft Azure Powershell**: uma ferramenta usada para administrar seus recursos Azure do Powershell linguagem de script também é instalada em sua máquina virtual. 

###<a name="power-bi"></a>Power BI

Para ajudá-lo a criar painéis e visualizações de ótima, o **Power BI Desktop** foi instalado. Use esta ferramenta para extrair dados de origens diferentes, criem seus painéis e relatórios e publicá-los na nuvem. Para obter informações, consulte o site de [Power BI](http://powerbi.microsoft.com) . Você pode encontrar a área de trabalho do Power BI no menu Iniciar. 

>[AZURE.NOTE] Você precisa de uma conta do Office 365 para acessar o Power BI. 

## <a name="additional-microsoft-development-tools"></a>Ferramentas de desenvolvimento do Microsoft adicionais
O [**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) pode ser usado para descobrir e fazer o download de outras ferramentas de desenvolvimento do Microsoft. Também há um atalho para a ferramenta fornecida na área de trabalho Microsoft dados ciência Virtual Machine.  

## <a name="important-directories-on-the-vm"></a>Diretórios importantes na máquina virtual

| Item                          | Diretório |
| ------------------------------| ---------------- |
|Configurações de servidor de bloco de anotações de Jupyter | C:\ProgramData\jupyter |
|Diretório inicial do bloco de anotações de Jupyter amostras| c:\dsvm\notebooks|
|Outras amostras | c:\dsvm\samples|
| Anaconda (padrão: Python 2.7) | c:\Anaconda |
| Ambiente de Python 3.5 anaconda | c:\Anaconda\envs\py35|
|Diretório de instância autônoma do servidor R (instância R padrão) | C:\Program Files\Microsoft SQL Server\130\R_SERVER |
| Diretório de instância do servidor de R no banco de dados | C:\Program Files\Microsoft SQL Server\MSSQL13. MSSQLSERVER\R_SERVICES |
| Diversas ferramentas | c:\dsvm\tools|

>[AZURE.NOTE] Instâncias da Microsoft dados ciência Máquina Virtual criada antes 1.5.0 (antes de 3 de setembro de 2016) usado uma estrutura de diretório um pouco diferente do especificado na tabela anterior. 

## <a name="next-steps"></a>Próximas etapas
Aqui estão algumas etapas para continuar sua aprendizagem e exploração. 

* Explore as diversas ferramentas de ciência de dados na ciência de dados máquina virtual clicando no menu Iniciar e check-out as ferramentas listadas no menu.
* Navegue até **C:\Program Files\Microsoft SQL Server\130\R_SERVER\library\RevoScaleR\demoScripts** para exemplos de uso da biblioteca de RevoScaleR em R que ofereça suporte a análise de dados em escala empresarial.  
* Leia o artigo: [10 coisas que você pode fazer na ciência de dados Máquina Virtual](http://aka.ms/dsvmtenthings)
* Aprenda a criar soluções analíticas de ponta a ponta sistemática usando o [Processo de ciência de dados de equipe](https://azure.microsoft.com/documentation/learning-paths/data-science-process/).
* Visite a [Galeria de inteligência de Cortana](http://gallery.cortanaintelligence.com) para máquina aprendizagem e dados analytics exemplos que usam o pacote de inteligência de Cortana. Também fornecemos um ícone no menu **Iniciar** e na área de trabalho da máquina virtual para esta galeria.

