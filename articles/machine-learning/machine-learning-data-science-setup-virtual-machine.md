<properties
    pageTitle="Configurar uma máquina virtual como um servidor de bloco de anotações de IPython | Microsoft Azure"
    description="Defina para cima uma Azure Máquina Virtual para uso em um ambiente de ciência de dados com o servidor de IPython para análise avançada."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Configurar uma máquina virtual Azure como um servidor de bloco de anotações de IPython para análise avançada

Este tópico mostra como provisionar e configurar uma máquina virtual Azure para análise avançada que pode ser usado como parte de um ambiente de ciência de dados. O computador virtual Windows está configurado com ferramentas como como bloco de anotações de IPython, Gerenciador de armazenamento do Azure, AzCopy, bem como outros utilitários que são úteis para projetos de análise avançada de suporte. Azure Storage Explorer e AzCopy, por exemplo, fornecem maneiras convenientes para carregar dados para o armazenamento de blob do Microsoft Azure do computador local ou baixá-la em sua máquina local do armazenamento de blob.

## <a name="create-vm"></a>Etapa 1: Criar uma finalidade geral Azure máquina virtual

Se você já tiver uma máquina virtual Azure e apenas deseja configurar um servidor de bloco de anotações de IPython nele, você pode ignorar esta etapa e vá para [etapa 2: adicionar um ponto de extremidade para blocos de anotações de IPython a uma máquina virtual existente](#add-endpoint).

Antes de iniciar o processo de criação de uma máquina virtual no Azure, é necessário determinar o tamanho da máquina que é necessária para processar os dados para seu projeto. Máquinas menores têm menos memória e menos cores de CPU que máquinas maiores, mas eles também são mais econômico. Para obter uma lista de tipos de máquina e preços, consulte a página de <a href="http://azure.microsoft.com/pricing/details/virtual-machines/" target="_blank">Preços de máquinas virtuais</a>

1. Faça logon no <a href="https://manage.windowsazure.com" target="_blank">Portal de clássico do Azure</a>e clique em **novo** no canto inferior esquerdo. Uma janela será exibida. Selecione **CALCULAR** -> **máquina VIRTUAL** -> **FROM GALERIA**.

    ![Criar espaço de trabalho][24]

2. Escolha uma das imagens a seguir:

    * Centro de dados do Windows Server 2012 R2
    * Experiência do Windows Server Essentials (Windows Server 2012 R2)

    Em seguida, clique na seta apontando para a direita na parte inferior direita para ir a próxima página de configuração.

    ![Criar espaço de trabalho][25]

3. Insira um nome para a máquina virtual que você deseja criar, selecione o tamanho da máquina (padrão: A3) com base no tamanho dos dados da máquina será processo e como poderosos desejado máquina ser (tamanho de memória e o número de cores de computação), insira um nome de usuário e senha para o computador. Clique na seta à direita para ir para a próxima página de configuração.

    ![Criar espaço de trabalho][26]

4. Selecione a **Região/AFINIDADE grupo/VIRTUAL rede** que contém a **Conta de armazenamento** que você planeja usar para esta máquina virtual e selecione essa conta de armazenamento. Adicione um ponto de extremidade na parte inferior do campo de **pontos de EXTREMIDADE** digitando o nome do ponto de extremidade ("IPython" aqui). Você pode escolher qualquer cadeia como o **nome** do ponto final e qualquer número inteiro entre 0 e 65536 que está **disponível** como a **Porta pública**. A **Porta particular** deve ser **9999**. Os usuários devem **evitar** o uso de portas públicas que já foram atribuídas para serviços de internet. <a href="http://www.chebucto.ns.ca/~rakerman/port-table.html" target="_blank">Portas de serviços de Internet</a> fornece uma lista de portas que foram atribuídas e deve ser evitado.

    ![Criar espaço de trabalho][27]

5. Clique na marca de seleção para iniciar a máquina virtual processo de provisionamento.

    ![Criar espaço de trabalho][28]


Pode levar 15-25 minutos para concluir a máquina virtual processo de provisionamento. Depois que a máquina virtual tiver sido criada, o status deste computador deve aparecer como **executando**.

![Criar espaço de trabalho][29]

## <a name="add-endpoint"></a>Etapa 2: Adicionar um ponto de extremidade para blocos de anotações de IPython a uma máquina virtual existente

Se você criou a máquina virtual seguindo as instruções na etapa 1, o ponto de extremidade para o bloco de anotações de IPython já foi adicionado e esta etapa pode ser ignorada.

Se já existe na máquina virtual e você precisa adicionar um ponto de extremidade para o bloco de anotações de IPython que serão instalados na etapa 3 abaixo, primeiro log no Portal de clássico do Azure, selecione a máquina virtual e adicionar o ponto de extremidade para o servidor de bloco de anotações de IPython. A figura a seguir contém uma captura de tela do portal após o ponto de extremidade para o bloco de anotações de IPython foi adicionado a uma máquina virtual Windows.

![Criar espaço de trabalho][17]

## <a name="run-commands"></a>Etapa 3: Instalar o bloco de anotações de IPython e outras ferramentas de suporte

Após a máquina virtual é criada, use o protocolo de área de trabalho remota (RDP) para efetuar logon na máquina virtual Windows. Para obter instruções, consulte [como faça logon no Máquina Virtual executando o Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md). Abra o **Prompt de comando** (**não na janela de comando do Powershell**) como **administrador** e execute o seguinte comando.

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Quando a instalação for concluída, o servidor de bloco de anotações de IPython é iniciado automaticamente na *c:\\usuários\\\<nome de usuário\>\\documentos\\blocos de anotações de IPython* diretório.

Quando solicitado, digite uma senha para o bloco de anotações de IPython e a senha do administrador do computador. Isso permite que o bloco de anotações de IPython executar como um serviço na máquina.

## <a name="access"></a>Etapa 4: Access IPython os blocos de anotações de um navegador da web
Para acessar o servidor de bloco de anotações de IPython, abra uma web navegador e a entrada *nome DNS de máquina https://&#60;virtual >: & #60; número da porta pública >* na caixa de texto URL. Aqui, o *& #60; número da porta pública >* deve ser o número de porta especificado quando o ponto de extremidade do bloco de anotações de IPython foi adicionado.

O *& #60; nome DNS de máquina virtual >* pode ser encontrado no Portal do Azure clássico. Depois de registro em log no portal do clássico, clique em **máquinas virtuais**, selecione o computador que você criou e selecione **Painel de controle**, o nome DNS será mostrado da seguinte maneira:

![Criar espaço de trabalho][19]

Você encontrará um aviso informando que _há um problema com o certificado de segurança deste site_ (Internet Explorer) ou a _sua conexão é não particular_ (Chrome), conforme mostrado nas figuras a seguir. Clique em **Continuar neste site (não recomendado)** (Internet Explorer) ou **Avançado** e, em seguida * *prosseguir para & #60;* Nome DNS*> (não seguro) * * (Chrome) para continuar. Introdução a senha que você especificou anteriormente para acessar os blocos de anotações de IPython.

**Internet Explorer:**
![criar espaço de trabalho][20]

**Chrome:**
![criar espaço de trabalho][21]

Após o logon do bloco de anotações de IPython, um diretório *DataScienceSamples* mostrará no navegador. Este diretório contém blocos de anotações de IPython de exemplo que são compartilhados pela Microsoft para ajudar os usuários a realizar tarefas de ciência de dados. Esses blocos de anotações de IPython de amostra são check-out do [**repositório de Github**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) máquinas virtuais durante o servidor de bloco de anotações de IPython configurar o processo. A Microsoft mantém e atualiza este repositório com frequência. Os usuários podem visitar o repositório Github para acessar os blocos de anotações de IPython de amostra atualizado mais recentemente.
![Criar espaço de trabalho][18]

## <a name="upload"></a>Etapa 5: Carregar uma IPython bloco de anotações existente de um computador local para o servidor de bloco de anotações de IPython

Blocos de anotações de IPython fornecem uma maneira fácil para os usuários carregar um bloco de anotações existente do IPython em suas máquinas locais para o servidor de bloco de anotações de IPython nas máquinas virtuais. Depois que os usuários fazem logon no bloco de anotações de IPython em um navegador da web, clique no **diretório** que o bloco de anotações de IPython será carregado. Em seguida, selecione um arquivo de .ipynb do bloco de anotações de IPython para carregar a partir da máquina local no **Explorador de arquivos**e arraste e solte-o no diretório IPython bloco de anotações no navegador da web. Clique no botão **carregar** para carregar o arquivo .ipynb para o servidor de bloco de anotações de IPython. Outros usuários, em seguida, podem começar a usá-lo de seus navegadores da web.

![Criar espaço de trabalho][22]

![Criar espaço de trabalho][23]


##<a name="shutdown"></a>Desligamento e eliminação alocar máquina virtual quando não estiver em uso

Azure máquinas virtuais têm preços como **pagar somente para o qual você usa**. Para garantir que você não seja sendo cobrado quando não usando sua máquina virtual, ele deve estar no estado **parado (Deallocated)** quando não estiver em uso.

> [AZURE.NOTE] Se você desligar a máquina virtual do dentro da VM (usando opções de energia do Windows), a máquina virtual for interrompida mas permanece alocada. Para garantir que você não continue será cobrado, sempre pare máquinas virtuais a partir do [Portal clássico do Azure](http://manage.windowsazure.com/). Você também pode interromper a máquina virtual através do Powershell chamando **ShutdownRoleOperation** com "PostShutdownAction" igual a "StoppedDeallocated".

Para desligar e desalocar a máquina virtual:

1. Faça logon no [Portal de clássico do Azure](http://manage.windowsazure.com/) usando sua conta.  

2. Selecione **máquinas virtuais** da barra de navegação à esquerda.

3. Na lista de máquinas virtuais, clique no nome da sua máquina virtual e vá para a página de **painel** .

4. Na parte inferior da página, clique em **Desligar**.

![Desligamento de máquina virtual][15]

A máquina virtual será desalocada mas não excluída. Você pode reiniciar sua máquina virtual a qualquer momento a partir do Portal de clássico do Azure.

## <a name="your-azure-vm-is-ready-to-use-whats-next"></a>Sua máquina virtual do Azure está pronto para uso: o que fazer a seguir?

Sua máquina virtual está pronta para usar em seus exercícios de ciência de dados. Na máquina virtual também está pronta para ser usado como um servidor de bloco de anotações de IPython a exploração e o processamento de dados e outras tarefas em conjunto com o aprendizado de máquina do Azure e o processo de ciência de dados de equipe.

As próximas etapas do processo de ciência de dados de equipe são mapeadas no [Caminho de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e podem incluir etapas que mover dados para HDInsight, processam e amostra-lo em preparação para aprender a partir dos dados com aprendizado de máquina do Azure.


[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png
