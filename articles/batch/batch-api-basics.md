<properties
    pageTitle="Visão geral do recurso Azure lote para desenvolvedores | Microsoft Azure"
    description="Aprenda sobre os recursos do serviço lote e suas APIs do ponto de vista desenvolvimento."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/29/2016"
    ms.author="marsma"/>

# <a name="batch-feature-overview-for-developers"></a>Visão geral do recurso de lote para desenvolvedores

Nesta visão geral dos componentes principais do serviço do Azure lote, vamos abordar os recursos de serviço primário e soluções de computação de recursos que lote desenvolvedores podem usar para construir paralelo grande escala.

Se você está desenvolvendo um aplicativo de computação distribuído ou serviço que emite direto [API REST] [ batch_rest_api] chamadas ou você estiver usando um dos [SDKs de lote](batch-technical-overview.md#batch-development-apis), você vai usar muitos dos recursos abordados neste artigo.

> [AZURE.TIP] Para uma introdução de alto nível para o serviço de lote, consulte [Noções básicas do Azure lote](batch-technical-overview.md).

## <a name="batch-service-workflow"></a>Fluxo de trabalho de serviço de lote

O fluxo de trabalho de alto nível seguinte é típico de quase todos os aplicativos e serviços que usam o serviço de lote para processamento paralelas cargas de trabalho:

1. Carregue os **arquivos de dados** que você deseja processar um [Armazenamento do Azure] [ azure_storage] conta. Lote inclui suporte interno para acessar o armazenamento de Blob do Azure e suas tarefas podem baixar esses arquivos para [Calcular nós](#compute-node) quando as tarefas são executadas.

2. Carregue os **arquivos do aplicativo** que suas tarefas serão executadas. Esses arquivos podem ser binários ou scripts e suas dependências e são executados pelas tarefas em seus trabalhos. Suas tarefas podem baixar esses arquivos da sua conta de armazenamento, ou você pode usar o recurso de [pacotes de aplicativos](#application-packages) do lote para implantação e gerenciamento de aplicativo.

3. Crie um [pool](#pool) de nós de computação. Quando você cria um pool, você pode especificar o número de nós de computação para o pool, seu tamanho e o sistema operacional. Quando cada tarefa no seu trabalho é executado, ele está designado para executar em um de nós no pool de.

4. Crie um [trabalho](#job). Um trabalho gerencia um conjunto de tarefas. Associar cada trabalho para um grupo específico onde as tarefas do trabalho serão executado.

5. Adicione [tarefas](#task) ao trabalho. Cada tarefa executa o aplicativo ou script carregado para processar os arquivos de dados-downloads de sua conta de armazenamento. Como cada tarefa é concluída, ela pode carregar sua saída ao armazenamento do Azure.

6. Monitorar o progresso de trabalho e recuperar a saída de tarefa do armazenamento do Azure.

As seções a seguir discutem esses e outros recursos do lote que habilitam o seu cenário de computação distribuído.

> [AZURE.NOTE] Você precisa de uma [conta de lote](batch-account-create-portal.md) para usar o serviço de lote. Além disso, quase todas as soluções usam um [Armazenamento do Azure] [ azure_storage] para armazenamento de arquivos e a recuperação de conta. Lote atualmente oferece suporte apenas o **finalidade geral** armazenamento tipo de conta, conforme descrito na etapa 5, [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md).

## <a name="batch-service-resources"></a>Recursos de serviço de lote

Alguns dos recursos a seguir – contas, calcular nós, pools, trabalhos e tarefas – necessários para todas as soluções que usam o serviço de lote. Outras pessoas, como agendas de trabalho e pacotes de aplicativos, são recursos úteis, mas opcionais.

- [Conta](#account)
- [Calcular nó](#compute-node)
- [Pool](#pool)
- [Trabalho](#job)

  - [Cronogramas de trabalho](#scheduled-jobs)

- [Tarefa](#task)

  - [Iniciar tarefa](#start-task)
  - [Gerenciador de tarefas](#job-manager-task)
  - [Tarefas de preparação e lançamento de trabalho](#job-preparation-and-release-tasks)
  - [Tarefa de várias instâncias (MPI)](#multi-instance-tasks)
  - [Dependências entre tarefas](#task-dependencies)

- [Pacotes de aplicativos](#application-packages)

## <a name="account"></a>Conta

Uma conta de lote é uma entidade identificada exclusivamente dentro do serviço de lote. Todo o processamento está associado uma conta de lote. Quando você executar operações com o serviço de lote, é necessário o nome de conta e uma das suas chaves de conta. Você pode [criar uma conta de lote do Azure usando o portal do Azure](batch-account-create-portal.md).

## <a name="compute-node"></a>Calcular nó

Um nó de computação é uma máquina virtual Azure (máquina virtual) dedicada a uma parte da carga de trabalho do seu aplicativo de processamento. O tamanho de um nó determina o número de cores CPU, capacidade de memória e tamanho do sistema de arquivo local que está alocado para o nó. Você pode criar grupos de nós Windows ou Linux usando os serviços de nuvem do Azure ou máquinas virtuais Marketplace imagens. Consulte a seção de [Pool](#pool) a seguir para obter mais informações sobre essas opções.

Nós podem executar qualquer executável ou script que é suportado pelo ambiente do sistema operacional do nó. Isso inclui \*.exe, \*. cmd, \*bat e scripts do PowerShell para Windows – e binários, Acabamento e Python scripts para Linux.

Todos calcular nós no lote também incluem:

- Um padrão [estrutura da pasta](#files-and-directories) e associados [variáveis de ambiente](#environment-settings-for-tasks) que estão disponíveis para referência por tarefas.
- Configurações de **firewall** que são configuradas para controlar o acesso.
- [Acesso remoto](#connecting-to-compute-nodes) para Windows (RDP Remote Desktop Protocol ()) e nós Linux (Secure Shell (SSH)).

## <a name="pool"></a>Pool

Um pool é uma coleção de nós que seu aplicativo é executado em. O pool pode ser criado manualmente por você ou automaticamente pelo serviço lote quando você especifica o trabalho a ser feito. Você pode criar e gerenciar um pool que atenda os requisitos de recursos do seu aplicativo. Um pool pode ser usado somente pela conta lote no qual ele foi criado. Uma conta de lote pode ter mais de um pool.

Construir Azure pools de lote na parte superior da plataforma de computação Azure core. Eles fornecem alocação de grande escala, instalação de aplicativos, distribuição de dados, monitoramento de integridade e ajuste flexível do número de nós de computação dentro de um pool ([dimensionamento](#scaling-compute-resources)).

Cada nó que é adicionado a um pool é atribuído um nome exclusivo e o endereço IP. Quando um nó é removido de um pool, quaisquer alterações feitas no sistema operacional ou arquivos são perdidas e seu nome e endereço IP são lançados para uso futuro. Quando um nó deixa um pool, sua vida útil está acima.

Quando você cria um pool, você pode especificar os seguintes atributos:

- Calcular o nó **sistema operacional** e **versão**

    Você tem duas opções quando você seleciona um sistema operacional para os nós no seu pool: **Configuração de máquina Virtual** e **Configuração de serviços de nuvem**.

    **Configuração de máquina virtual** fornece imagens Linux e Windows para calcular nós do [Azure Marketplace de máquinas virtuais][vm_marketplace].
    Quando você cria um pool que contém nós de configuração de máquina Virtual, você deve especificar não apenas o tamanho dos nós, mas também a **referência de imagem de máquina virtual** e o lote **agente de nó SKU** ser instalados em nós. Para obter mais informações sobre como especificar essas propriedades do pool, consulte [nós em lote Azure pools de computadores provisionar Linux](batch-linux-nodes.md).

    **Configuração de serviços de nuvem** fornece que computação nós *somente*do Windows. Sistemas operacionais disponíveis para pools de configuração de serviços de nuvem estão listados nas [versões de SO de convidado do Azure e matriz de compatibilidade SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Quando você cria um pool que contém nós de serviços de nuvem, você precisa especificar apenas o tamanho de nó e sua *Família do sistema operacional*. Quando você cria nós de computadores pools do Windows, você normalmente usar serviços de nuvem.

    - A *Família do sistema operacional* também determina quais versões do .NET são instalados com o sistema operacional.
    - Como com funções de trabalho nos serviços de nuvem, você pode especificar uma *Versão do sistema operacional* (para obter mais informações sobre funções de trabalho, consulte a seção [Avisar-me sobre os serviços de nuvem](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services) na [Visão geral dos serviços de nuvem](../cloud-services/cloud-services-choose-me.md)).
    - Como com as funções de trabalho, é recomendável que você especificar `*` para a *Versão do sistema operacional* para que os nós são automaticamente atualizados e não há nenhum trabalho necessário para atender recentemente lançada versões. O caso de uso principal para selecionar uma versão específica do sistema operacional é garantir a compatibilidade de aplicativos, que permite compatibilidade testes para ser executado antes de permitir a versão a ser atualizada. Após a validação, a *Versão do sistema operacional* para o pool podem ser atualizados e a nova imagem de sistema operacional pode ser instalado – as tarefas em execução são interrompidas e recolocado na fila.

- **Tamanho de nós**

    **Configuração de serviços de nuvem** tamanhos de nós de computação são listados em [tamanhos para os serviços de nuvem](../cloud-services/cloud-services-sizes-specs.md). Lote dá suporte a todos os tamanhos de serviços de nuvem, exceto `ExtraSmall`.

    **Configuração de máquina virtual** calcular nó tamanhos estão listados em [tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) e [tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Lote dá suporte a todos os tamanhos de máquina virtual do Azure, exceto `STANDARD_A0` e aqueles com armazenamento premium (`STANDARD_GS`, `STANDARD_DS`, e `STANDARD_DSV2` série).

    Ao selecionar um tamanho de nó de computação, considere as características e os requisitos dos aplicativos que você executará em nós. Aspectos como se o aplicativo é multithread e quanta memória consome pode ajudar determinam o tamanho de nó mais adequada e econômica. É comum para selecionar um tamanho de nó presumindo que uma tarefa será executado em um nó por vez. No entanto, é possível ter várias tarefas (e, portanto, várias instâncias do aplicativo) [executados em paralelo](batch-parallel-node-tasks.md) em nós de computadores durante a execução do trabalho. Nesse caso, é comum para escolher um tamanho maior de nó para acomodar a demanda crescente de execução de tarefa paralela. Consulte a [política de agendamento de tarefas](#task-scheduling-policy) para obter mais informações.

    Todos os nós em um pool são do mesmo tamanho. Se pretende executar aplicativos com diferentes requisitos de sistema e/ou carregar níveis, recomendamos que você use grupos separados.

- **Número de destino de nós**

    Este é o número de nós de computação que você deseja implantar no pool. Isso é conhecido como um *destino* porque, em algumas situações, o pool não pode alcançar o número de nós desejado. Um pool não pode acessar o número desejado de nós se atingir a [cota de núcleo](batch-quota-limit.md#batch-account-quotas) para sua conta de lote – ou se houver uma fórmula de escala automática que você aplicou ao pool que limita o número máximo de nós (consulte a seção "Dimensionamento política").

- **Política de escala**

    Além de especificar um número estático de nós, em vez disso, você pode escrever e aplicar uma [fórmula de escala automática](#scaling-compute-resources) para um pool. O serviço de lote periodicamente avalia sua fórmula e ajusta o número de nós dentro do pool com base em vários pool, trabalho e parâmetros da tarefa que você pode especificar.

- **Diretiva de agendamento da tarefa**

    A opção de configuração de [tarefas máx por nó](batch-parallel-node-tasks.md) determina o número máximo de tarefas que podem ser executados em paralelo em cada nó de computação dentro do pool.

    A configuração padrão é que uma tarefa por vez é executado em um nó, mas há situações em que é útil ter mais de uma tarefa executada em um nó simultaneamente. Consulte o [cenário de exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo [tarefas de nó simultâneas](batch-parallel-node-tasks.md) para ver como você pode se beneficiar várias tarefas por nó.

    Você também pode especificar um *tipo de preenchimento* que determina se lote distribui as tarefas uniformemente entre todos os nós em um pool ou pacotes de cada nó com o número máximo de tarefas antes de atribuir tarefas para outro nó.

- **Status de comunicação** de nós de computação

    Na maioria dos cenários, tarefas operam independentemente em não precisa se comunicar uns com os outros. No entanto, há alguns aplicativos nas quais tarefas devem se comunicar, como [cenários MPI](batch-mpi.md).

    Você pode configurar um pool para permitir a comunicação entre os nós dentro it –**na comunicação**. Quando estiver habilitada na comunicação, nós em pools de configuração de serviços de nuvem podem se comunicar com os outros em portas maiores que 1100 e pools de configuração de máquina Virtual não restringir o tráfego em qualquer porta.

    Observe que também habilitando na comunicação afeta o posicionamento de nós de clusters e pode limitar o número máximo de nós em um pool devido a restrições de implantação. Se seu aplicativo não exige a comunicação entre os nós, o serviço de lote pode alocar um potencialmente grande número de nós ao pool de muitos clusters diferentes e dos data centers para habilitar o maior capacidade de processamento paralelo.

- **Iniciar tarefa** para nós de computação

    Opcional *Iniciar tarefa* é executado em cada nó conforme o nó ingressa o pool e sempre que um nó for reiniciado ou recriar a imagem. A tarefa inicial é especialmente útil para preparar nós de computação para a execução de tarefas, como instalar os aplicativos que suas tarefas executados em nós de computação.

- **Pacotes de aplicativos**

    Você pode especificar [pacotes de aplicativos](#application-packages) para implantar os nós de computação no pool. Pacotes de aplicativos fornecem implantação simplificada e controle de versão dos aplicativos que executar suas tarefas. Pacotes de aplicativos que você especificar para um pool estão instalados em cada nó que associa esse pool e sempre que uma nó é reiniciado ou recriar a imagem. Pacotes de aplicativos estão atualmente não suportados em nós de computação Linux.

- **Configuração de rede**

    Você pode especificar a identificação de uma Azure [rede virtual (VNet)](../virtual-network/virtual-networks-overview.md) no qual o pool nós de computadores deve ser criada. Requisitos para especificar um VNet para seu pool pode ser encontrado em [Adicionar um pool a uma conta] [ vnet] na referência da API REST de lote.

> [AZURE.IMPORTANT] Todas as contas de lote têm padrão **cota** que limita o número de **cores** (e, assim, nós de computação) em uma conta de lote. Você pode encontrar as cotas padrão e instruções sobre como [Aumentar cota](batch-quota-limit.md#increase-a-quota) (como o número máximo de cores em sua conta do lote) [cotas e limites para o serviço de lote do Azure](batch-quota-limit.md). Se você achar que se perguntando "Por que não meu pool atinja mais de X nós?" Essa cota de núcleo pode ser a causa.

## <a name="job"></a>Trabalho

Um trabalho é um conjunto de tarefas. Ele gerencia como computação é executada por suas tarefas em nós de computação em um pool.

- O trabalho Especifica o **pool** no qual o trabalho for executado. Você pode criar um novo pool para cada tarefa ou usar um pool para muitos trabalhos. Você pode criar um pool para cada trabalho que está associado um plano de trabalho, ou para todos os trabalhos que estão associados um cronograma de trabalho.

- Você pode especificar um opcional **prioridade do trabalho**. Quando um trabalho é enviado com uma prioridade maior que trabalhos que estão atualmente em andamento, as tarefas para o trabalho com prioridade mais alta são inseridas na fila com antecedência tarefas para os trabalhos de prioridade mais baixa. Tarefas em trabalhos de prioridade mais baixa que já estejam em execução não são capturadas.

- Você pode usar o trabalho **restrições** especificar determinados limites para seus trabalhos:

    Você pode definir o **tempo máximo wallclock**, para que se um trabalho for executado por mais do tempo de wallclock máximo especificado, o trabalho e todas as suas tarefas são encerradas.

    Lote pode detectar e tente novamente tarefas com falha. Você pode especificar o **número máximo de tentativas de tarefa** como uma restrição, inclusive se uma tarefa é *sempre* ou *nunca* repetidas. Repetir uma tarefa significa que a tarefa foi recolocado na fila para ser executada novamente.

- Seu aplicativo cliente pode adicionar tarefas a um trabalho, ou você pode especificar uma [tarefa do Gerenciador de trabalho](#job-manager-task). Uma tarefa do Gerenciador de trabalho contém as informações necessárias criar as tarefas necessárias para um trabalho, com a tarefa do Gerenciador de trabalho em execução em um de nós de computação no pool. A tarefa do Gerenciador de trabalho é tratada especificamente por lote – ele está na fila assim que o trabalho for criado e seja reiniciado se falhar. Uma tarefa de Gerenciador de trabalho é *necessária* para trabalhos que são criados por um [cronograma de trabalho](#scheduled-jobs) porque ele é a única maneira de definir as tarefas antes do trabalho é criada uma instância.

- Por padrão, trabalhos permanecem no estado ativo quando todas as tarefas no trabalho forem concluídas. Você pode alterar esse comportamento para que o trabalho é encerrado automaticamente quando todas as tarefas no trabalho estão concluídas. Defina a propriedade de **onAllTasksComplete** do trabalho ([OnAllTasksComplete] [ net_onalltaskscomplete] no lote .NET) para *terminatejob* para finalizar automaticamente o trabalho quando todas as suas tarefas estão no estado concluído.

    Observe que o serviço de lote considera um trabalho *sem* tarefas com todas as suas tarefas concluídas. Portanto, essa opção é mais comumente usada com uma [tarefa do Gerenciador de trabalho](#job-manager-task). Se você quiser usar o encerramento de trabalho automáticas sem um gerente de trabalho, você deve inicialmente definir propriedade de **onAllTasksComplete** do novo trabalho para *noaction*, defini-lo como *terminatejob* somente depois que terminar de adicionar tarefas ao trabalho.

### <a name="job-priority"></a>Prioridade do trabalho

Você pode atribuir uma prioridade para trabalhos que você criar no lote. O serviço de lote usa o valor de prioridade do trabalho para determinar a ordem de planejamento de trabalho dentro de uma conta (isso não é devem ser confundidas com um [trabalho agendado](#scheduled-jobs)). O intervalo de valores de prioridade de -1000 a 1000,-1000 sendo a prioridade mais baixa e 1000 sendo o mais alto. Você pode atualizar a prioridade de uma tarefa usando a [atualizar as propriedades de um trabalho] [ rest_update_job] operação (lote restante) ou modificando o [CloudJob.Priority] [ net_cloudjob_priority] propriedade (lote .NET).

Na mesma conta, trabalhos de prioridade mais alta têm precedência de agendamento sobre trabalhos de prioridade mais baixa. Um trabalho com um valor de prioridade mais alta em uma conta não tem a precedência de agendamento sobre outro trabalho com um valor de prioridade mais baixa em uma conta diferente.

Agendamento em pools de trabalho é independente. Entre diferentes pools, não há garantia que uma prioridade mais alta está agendado primeiro se seu pool associado é menos nós ociosos. No mesmo pool, trabalhos com o mesmo nível de prioridade têm a mesma chance de agendado.

### <a name="scheduled-jobs"></a>Tarefas agendadas

[Cronogramas de trabalho] [ rest_job_schedules] permitem criar trabalhos recorrentes dentro do serviço de lote. Um cronograma de trabalho especifica quando executar trabalhos e inclui as especificações para os trabalhos a ser executado. Você pode especificar a duração da agenda, quanto tempo e quando o cronograma está em vigor – e com que frequência durante esse período de tempo que trabalhos devem ser criados.

## <a name="task"></a>Tarefa

Uma tarefa é uma unidade de computação que está associada um trabalho. Ele é executado em um nó. Tarefas atribuídas a um nó para execução ou são enfileiradas até que um nó fique livre. Resumindo, uma tarefa executa um ou mais programas ou scripts em um nó de computação para realizar o trabalho que você precisa concluído.

Quando você cria uma tarefa, você pode especificar:

- A **linha de comando** da tarefa. Esta é a linha de comando que executa o seu aplicativo ou script no nó de computadores.

    É importante observar que a linha de comando realmente não é executado em um shell. Portanto, ele não consegue nativamente tirar proveito dos recursos de shell como expansão de [variável de ambiente](#environment-settings-for-tasks) (Isso inclui o `PATH`). Para aproveitar esses recursos, você deve chamar o shell na linha de comando – por exemplo, iniciando `cmd.exe` em nós do Windows ou `/bin/sh` no Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Se suas tarefas precisam executar um aplicativo ou script que não esteja no nó `PATH` ou fazer referência a variáveis de ambiente, invocar o shell explicitamente na linha de comando da tarefa.

- **Arquivos de recursos** que contêm os dados sejam processados. Esses arquivos são automaticamente copiados para o nó do armazenamento de Blob em uma conta de armazenamento do Azure **finalidade geral** antes de linha de comando da tarefa é executada. Para obter mais informações, consulte as seções [Iniciar tarefa](#start-task) e [arquivos e pastas](#files-and-directories).

- As **variáveis de ambiente** que são necessárias pelo seu aplicativo. Para obter mais informações, consulte a seção [configurações de ambiente para tarefas](#environment-settings-for-tasks) .

- As **restrições** sob as quais a tarefa deve ser executada. Por exemplo, o tempo máximo que a tarefa tem permissão para executar, o número máximo de vezes que uma tarefa com falha deve ser repetida, e o máximo de horário que arquivos no diretório de trabalho da tarefa são mantidas.

- **Pacotes de aplicativos** para implantar o nó de computação na qual a tarefa é agendada para executar. [Pacotes de aplicativos](#application-packages) fornecem implantação simplificada e controle de versão dos aplicativos que executar suas tarefas. Pacotes de aplicativos de nível da tarefa são particularmente úteis em ambientes de pool compartilhado, onde diferentes trabalhos são executados em um pool e o pool não é excluído quando um trabalho for concluído. Se seu trabalho tem menos tarefas de nós no pool, pacotes de aplicativos de tarefa podem minimizá transferência de dados desde que o aplicativo é implantado apenas a nós que executar tarefas.

Além de tarefas que você definir para executar o cálculo em um nó, as seguintes tarefas especiais também são fornecidas pelo serviço lote:

- [Iniciar tarefa](#start-task)
- [Gerenciador de tarefas](#job-manager-task)
- [Tarefas de preparação e lançamento de trabalho](#job-preparation-and-release-tasks)
- [Tarefas de várias instâncias (MPI)](#multi-instance-tasks)
- [Dependências entre tarefas](#task-dependencies)

### <a name="start-task"></a>Iniciar tarefa

Associando um **Iniciar tarefa** um pool, você pode preparar o ambiente operacional de seus nós. Por exemplo, você pode executar ações como instalar os aplicativos que executar suas tarefas e iniciar processos de plano de fundo. A tarefa inicial seja executada toda vez um nó for iniciado, desde que ele permaneça no pool – incluindo quando o nó é primeiro adicionado para o pool e quando ele é reiniciado ou recriar a imagem.

Dos principais benefícios da tarefa iniciar é que ela pode conter todas as informações necessárias configurar um nó de computação e instalar os aplicativos necessários para execução da tarefa. Portanto, aumentando o número de nós em um pool é tão simple quanto especificando a nova contagem de nó de destino – lote já tem as informações necessárias para configurar os novos nós e preparar para a aceitação de tarefas.

Como com qualquer tarefa Azure lote, você pode especificar uma lista de **arquivos de recurso** no [Armazenamento do Azure][azure_storage], além de uma **linha de comando** a ser executada. Lote primeiro copia os arquivos de recursos para o nó do armazenamento do Azure e, em seguida, executa a linha de comando. Para uma tarefa de início do pool, a lista de arquivos normalmente contém o aplicativo de tarefa e suas dependências.

No entanto, ele também pode incluir dados de referência para ser usado por todas as tarefas que estejam em execução no nó de computadores. Por exemplo, linha de comando de início de uma tarefa pode realizar uma `robocopy` operação Copiar arquivos de aplicativo (que foram especificados como arquivos de recurso e baixados para o nó) da tarefa iniciar o [diretório de trabalho](#files-and-directories) para a [pasta compartilhada](#files-and-directories)e, em seguida, executar um MSI ou `setup.exe`.

> [AZURE.IMPORTANT] Lote atualmente suporta *apenas* o tipo de conta de armazenamento de **finalidade geral** , conforme descrito na etapa 5, [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md). Suas tarefas em lotes (incluindo tarefas padrão, as tarefas de início, tarefas de preparação do trabalho e tarefas de lançamento do trabalho) devem especificar arquivos de recursos que residem *somente* em contas de armazenamento de **finalidade geral** .

É geralmente desejável para o serviço de lote de esperar a tarefa iniciar seja concluída antes de considerar o nó pronto para ser atribuído a tarefas, mas você pode configurar isso.

Se uma tarefa Iniciar falha em um nó de computação, em seguida, o estado do nó é atualizado para refletir a falha e o nó não está atribuído a qualquer tarefa. Uma tarefa de iniciar pode falhar se há um problema copiando seus arquivos de recursos de armazenamento, ou se o processo executado pelo sua linha de comando retorna um código de saída diferente de zero.

Se você adicionar ou atualizar a tarefa inicial um pool *existente* , você deve reinicializar seus nós de computação para a tarefa de iniciar a ser aplicado a nós.

### <a name="job-manager-task"></a>Gerenciador de tarefas

Você normalmente usa uma **tarefa do Gerenciador de trabalho** para controlar e/ou monitorar a execução do trabalho – por exemplo, para criar e enviar as tarefas para um trabalho, determinar tarefas adicionais para executar e determinar quando o trabalho está concluído. Entretanto, uma tarefa do Gerenciador de trabalho não é restringida a essas atividades. É uma tarefa totalmente o que pode realizar ações que são necessárias para o trabalho. Por exemplo, uma tarefa do Gerenciador de trabalho pode baixar um arquivo especificado como um parâmetro, analisar o conteúdo do arquivo e enviar tarefas adicionais com base nesses conteúdos.

Uma tarefa do Gerenciador de trabalho é iniciada antes de todas as outras tarefas. Ele fornece os seguintes recursos:

- Ela será automaticamente enviada como uma tarefa pelo serviço lote quando o trabalho for criado.

- Ela está agendada para executar antes das outras tarefas em um trabalho.

- Seu nó associado é a última a ser removido de um pool quando o pool está sendo reduzido.

- Seu encerramento pode ser vinculado ao término de todas as tarefas no trabalho.

- Uma tarefa do Gerenciador de trabalho tem a prioridade mais alta quando ele precisa ser reiniciado. Se um nó ocioso não estiver disponível, o serviço de lote pode encerrar uma das outras tarefas em execução no pool para abrir espaço para a tarefa do Gerenciador de trabalho seja executado.

- Uma tarefa do Gerenciador de trabalho em um trabalho não têm prioridade sobre as tarefas de outros trabalhos. Em trabalhos, somente as prioridades de nível de trabalho forem observadas.

### <a name="job-preparation-and-release-tasks"></a>Tarefas de preparação e lançamento de trabalho

Lote fornece tarefas de preparação do trabalho para configuração de execução de pré-trabalho. Tarefas de lançamento de trabalho são para manutenção de trabalho POST ou limpeza.

- **Tarefa de preparação do trabalho**: uma tarefa de preparação de trabalho é executado em todos os nós de computação que estão agendados para executar tarefas, antes de qualquer uma das outras tarefas de trabalho são executados. Você pode usar uma tarefa de preparação do trabalho para copiar os dados que são compartilhados por todas as tarefas, mas é exclusivo para o trabalho, por exemplo.
- **Tarefa de lançamento do trabalho**: quando um trabalho concluiu, uma tarefa de lançamento do trabalho é executado em cada nó no pool que executadas pelo menos uma tarefa. Você pode usar uma tarefa de lançamento do trabalho para excluir dados que copiada pela tarefa de preparação do trabalho, ou para compactar e carregar dados do log de diagnóstico, por exemplo.

Ambos preparação de trabalho e tarefas de lançamento permitem que você especifique uma linha de comando para ser executada quando a tarefa é invocada. Eles oferecem recursos como o download do arquivo, execução elevada, variáveis de ambiente personalizado, duração máxima de execução, contagem de repetição e tempo de retenção de arquivo.

Para obter mais informações sobre as tarefas de preparação e lançamento de trabalho, consulte [nós de computadores de tarefas de conclusão e preparação de trabalho de executar em lote do Azure](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Tarefa de várias instâncias

Uma [tarefa de várias instâncias](batch-mpi.md) é uma tarefa que está configurada para ser executado em mais de um nó de computação simultaneamente. Com tarefas de várias instâncias, você pode habilitar cenários de computação de alto desempenho que exigem um grupo de nós de computação que estão alocados juntos para processar uma única carga de trabalho (como mensagem passando Interface (MPI)).

Para uma discussão detalhada sobre MPI trabalhos em execução no lote usando a biblioteca de lote .NET, confira [tarefas de várias instâncias de uso para executar aplicativos de Interface de transmissão de mensagens (MPI) em lote do Azure](batch-mpi.md).

### <a name="task-dependencies"></a>Dependências entre tarefas

[Dependências entre tarefas](batch-task-dependencies.md), como o nome indica, permitem que você especifique que uma tarefa depende da conclusão de outras tarefas antes de sua execução. Este recurso fornece suporte para situações em que uma tarefa "downstream" consome a saída de uma tarefa de "upstream" ou quando uma tarefa upstream executa algumas inicialização que é requerida por uma tarefa downstream. Para usar esse recurso, você deve primeiro habilitar dependências entre tarefas no seu trabalho em lotes. Em seguida, para cada tarefa que depende de outro (ou várias outras), você especifica as tarefas que essa tarefa depende.

Com dependências de tarefas, você pode configurar cenários semelhante ao seguinte:

* *taskB* depende *TarefaUma* (*taskB* não será iniciado execução até *TarefaUma* concluiu).
* *taskC* depende de *TarefaUma* e *taskB*.
* *taskD* depende de um intervalo de tarefas, como tarefas de *1* a *10*, antes que ele seja executado.

Fazer check-out [dependências de tarefas no lote do Azure](batch-task-dependencies.md) e o [TaskDependencies] [ github_sample_taskdeps] amostras de código nos [exemplos de lote azure] [ github_samples] repositório GitHub para mais detalhes detalhadas sobre esse recurso.

## <a name="environment-settings-for-tasks"></a>Configurações de ambiente para tarefas

Cada tarefa executada pelo serviço lote tem acesso a variáveis de ambiente que ele define nós de computação. Isso inclui variáveis de ambiente definidas pelo serviço em lotes ([definido pelo serviço][msdn_env_vars]) e variáveis de ambiente personalizado que você pode definir para as tarefas. Os aplicativos e scripts que executar suas tarefas tem acesso a essas variáveis de ambiente durante a execução.

Você pode definir variáveis de ambiente personalizado no nível de tarefa ou trabalho preenchendo a propriedade de *configurações de ambiente* para essas entidades. Por exemplo, consulte [Adicionar uma tarefa a um trabalho] [ rest_add_task] operação (API REST do lote) ou o [CloudTask.EnvironmentSettings] [ net_cloudtask_env] e [CloudJob.CommonEnvironmentSettings] [ net_job_env] propriedades no lote .NET.

Seu aplicativo cliente ou serviço pode obter variáveis de ambiente de uma tarefa, definidas pelo serviço e personalizadas, usando o [obter informações sobre uma tarefa] [ rest_get_task_info] operação (lote restante) ou acessando o [CloudTask.EnvironmentSettings] [ net_cloudtask_env] propriedade (lote .NET). Processos em execução em um nó de computação podem acessar essas e outras variáveis de ambiente no nó, por exemplo, usando familiar `%VARIABLE_NAME%` (Windows) ou `$VARIABLE_NAME` sintaxe (Linux).

Você pode encontrar uma lista completa de todas as variáveis de ambiente definidas pelo serviço [variáveis de ambiente de nó de computação][msdn_env_vars].

## <a name="files-and-directories"></a>Arquivos e pastas

Cada tarefa tem um *diretório de trabalho* em qual ele cria zero ou mais arquivos e pastas. Esta pasta de trabalho pode ser usada para armazenar o programa que é executado por tarefa, os dados que ele processa e a saída do processamento que ele executa. Todos os arquivos e pastas de uma tarefa pertencem o usuário da tarefa.

O serviço de lote expõe uma parte do sistema de arquivos em um nó como o *diretório raiz*. Tarefas podem acessar o diretório raiz referenciando o `AZ_BATCH_NODE_ROOT_DIR` variável de ambiente. Para obter mais informações sobre como usar variáveis de ambiente, consulte [configurações de ambiente para tarefas](#environment-settings-for-tasks).

O diretório raiz contém a estrutura de diretório a seguir:

![Calcular a estrutura do diretório de nó][1]

- **compartilhado**: este diretório fornece acesso de leitura/gravação para *todas as* tarefas que são executadas em um nó. Qualquer tarefa que é executada no nó pode criar, ler, atualizar e excluir arquivos nesse diretório. Tarefas podem acessar esse diretório referenciando o `AZ_BATCH_NODE_SHARED_DIR` variável de ambiente.

- **inicialização**: este diretório é usado por uma tarefa de iniciar como seu diretório de trabalho. Todos os arquivos que são baixados para o nó pela tarefa iniciar são armazenados aqui. A tarefa inicial pode criar, ler, atualizar e excluir arquivos nesta pasta. Tarefas podem acessar esse diretório referenciando o `AZ_BATCH_NODE_STARTUP_DIR` variável de ambiente.

- **Tarefas**: um diretório é criado para cada tarefa que é executada no nó. Ele é acessado referenciando o `AZ_BATCH_TASK_DIR` variável de ambiente.

    Dentro do diretório de cada tarefa, o serviço de lote cria uma pasta de trabalho (`wd`) cujo caminho exclusivo é especificado pela `AZ_BATCH_TASK_WORKING_DIR` variável de ambiente. Esta pasta fornece acesso de leitura/gravação à tarefa. A tarefa pode criar, ler, atualizar e excluir arquivos nesta pasta. Este diretório é mantido com base na restrição *RetentionTime* especificado para a tarefa.

    `stdout.txt`e `stderr.txt`: esses arquivos são gravados na pasta de tarefas durante a execução da tarefa.

>[AZURE.IMPORTANT] Quando um nó é removido do pool, *todos* os arquivos que são armazenados no nó são removidos.

## <a name="application-packages"></a>Pacotes de aplicativos

O recurso de [pacotes de aplicativos](batch-application-packages.md) fornece fácil gerenciamento e implantação de aplicativos para os nós de computação em seus pools. Você pode carregar e gerenciar várias versões dos aplicativos executados por suas tarefas, incluindo seus binários e arquivos de suporte. Em seguida, você pode implantar automaticamente um ou mais desses aplicativos para os nós de computação em seu pool.

Você pode especificar pacotes de aplicativos no nível do pool e tarefa. Quando você especificar pacotes de pool de aplicativos, o aplicativo é implantado para cada nó no pool. Quando você especificar pacotes de aplicativos de tarefa, o aplicativo é implantado apenas a nós que estão agendados para ser executado pelo menos uma das tarefas do trabalho, antes de linha de comando da tarefa é executada.

Lote trata os detalhes de trabalhar com o armazenamento do Azure para armazenar seus pacotes de aplicativos e implantá-los para calcular nós, para que seu código e o gerenciamento de sobrecarga pode ser simplificada.

Para saber mais sobre o recurso de pacote de aplicativo, confira a [implantação do aplicativo com pacotes de aplicativos do Azure lote](batch-application-packages.md).

>[AZURE.NOTE] Se você adicionar pacotes de pool de aplicativos a um pool *existente* , você deve reinicializar seus nós de computação para os pacotes de aplicativo seja implantado em nós.

## <a name="pool-and-compute-node-lifetime"></a>Vida útil de nó do pool e computação

Quando você cria sua solução Azure lote, você precisa tomar uma decisão de design sobre como e quando pools são criados e por quanto tempo calcular nós dentro desses pools são mantidas disponíveis.

Em uma extremidade do espectro, você pode criar um pool para cada trabalho enviados e exclua o pool assim suas tarefas terminar a execução. Isso maximiza a utilização porque os nós são alocados somente quando necessário e desligar assim que eles estão ociosos. Enquanto isso significa que o trabalho deve esperar para os nós a ser alocado, é importante observar que as tarefas são agendadas para execução assim nós estão disponíveis individualmente, alocado, e a tarefa de início foi concluída. Lote faz *não* aguardar até que todos os nós dentro de um pool disponíveis antes de atribuir tarefas a nós. Isso garante a utilização máxima de todos os nós disponíveis.

Na outra extremidade do espectro, se está ter trabalhos iniciar imediatamente é a maior prioridade, você pode criar um pool de antecedência e disponibilizar seus nós antes de trabalhos são enviados. Neste cenário, tarefas podem começar imediatamente, mas nós podem inativo durante a espera para que elas sejam atribuídas.

Uma abordagem combinada normalmente é usada para lidar com uma carga de variáveis, mas em andamento. Você pode ter um pool que vários trabalhos forem enviados à, mas podem aumentar o número de nós para cima ou para baixo de acordo com o trabalho carrega (consulte [recursos de computação de escala](#scaling-compute-resources) na seção a seguir). Você pode fazer isso reativamente, baseados em carga atual ou proativamente, se a carga pode ser prevista.

## <a name="scaling-compute-resources"></a>Recursos de computação de escala

Com o [dimensionamento automático](batch-automatic-scaling.md), você pode ter o serviço de lote dinamicamente ajustar o número de nós de computação em um pool de acordo com o uso de recursos e de carga de trabalho atual do seu cenário de computação. Isso permite que você reduza o custo total de executar o aplicativo usando somente os recursos que necessários e soltar aqueles que você não precisa.

Habilitar o dimensionamento automático escrever uma [fórmula de dimensionamento automática](batch-automatic-scaling.md#automatic-scaling-formulas) e associando essa fórmula com um pool. O serviço de lote usa a fórmula para determinar o número de destino de nós no pool para o próximo intervalo de escala (um intervalo que você pode configurar). Você pode especificar as configurações de dimensionamento automático para um pool ao criá-lo ou ativar o dimensionamento em um pool mais tarde. Você também pode atualizar as configurações de escala em um pool de dimensionamento de habilitados.

Como exemplo, talvez um trabalho requer que você enviar um grande número de tarefas a serem executadas. Você pode atribuir uma fórmula de dimensionamento para o pool que ajusta o número de nós no pool com base no número atual de tarefas na fila e a taxa de conclusão das tarefas no trabalho. O serviço de lote periodicamente avalia a fórmula e redimensiona o pool, com base na carga de trabalho (adicionar nós para muitas tarefas na fila e remover nós de nenhum tarefas enfileiradas ou em execução) e outras configurações sua fórmulas.

Uma fórmula de escala pode ser baseada nas seguintes métricas:

- **Métricas de tempo** são baseados em estatísticas coletadas a cada cinco minutos no número especificado de horas.

- **Métricas de recurso** são baseados em uso, o uso de largura de banda, o uso de memória e número de nós CPU.

- **Métricas de tarefa** são baseados no estado de tarefa, como *ativa* (na fila), *em execução*ou *concluído*.

Quando o dimensionamento automático diminui o número de nós de computação em um pool, você deve considerar como lidar com tarefas que estejam em execução no momento da operação de diminuição. Para acomodar isso, o lote fornece uma *opção de desalocação de nó* que você pode incluir em suas fórmulas. Por exemplo, você pode especificar que tarefas em execução são interrompidas imediatamente, interrompidas imediatamente e, em seguida, recolocado na fila para execução em outro nó ou permissão para terminar antes do nó é removido do pool.

Para saber mais sobre dimensionamento automaticamente um aplicativo, consulte [automaticamente escala calcular nós em um pool de lote do Azure](batch-automatic-scaling.md).

> [AZURE.TIP] Para maximizar a utilização de recursos de computação, defina o número de destino de nós zero no final de um trabalho, mas permitir a execução de tarefas para concluir.

## <a name="security-with-certificates"></a>Segurança com certificados

Normalmente, você precisa usar certificados quando você criptografar ou descriptografar informações confidenciais para tarefas, como a chave para uma [conta de armazenamento do Azure][azure_storage]. Para oferecer suporte a isso, você pode instalar certificados em nós. Segredos criptografados são passados para tarefas por meio de parâmetros de linha de comando ou incorporados em um dos recursos de tarefa e os certificados instalados podem ser usados para descriptografá-los.

Você usar o [certificado de adicionar] [ rest_add_cert] operação (lote restante) ou [CertificateOperations.CreateCertificate] [ net_create_cert] método (lote .NET) para adicionar um certificado para uma conta de lote. Em seguida, você pode associar o certificado para um pool de novo ou existente. Quando um certificado está associado um pool, o serviço de lote instala o certificado em cada nó no pool. O serviço de lote instala os certificados apropriados quando o nó for iniciado, antes de iniciar qualquer tarefa (incluindo a tarefa inicial e Gerenciador de tarefas).

Se você adicionar certificados a um pool *existente* , você deve reinicializar seus nós de computação para os certificados seja aplicada a nós.

## <a name="error-handling"></a>Manipulação de erro

Talvez seja necessário lidar com falhas de tarefa e o aplicativo em sua solução de lote.

### <a name="task-failure-handling"></a>Manipulação de falha de tarefa
Falhas na tarefa recaem nestas categorias:

- **Falhas de agendamento**

    Se a transferência de arquivos que são especificadas para uma tarefa falhar por algum motivo, um "Erro de agendamento" está definido para a tarefa.

    Agendamento de erros pode ocorrer se os arquivos de recurso da tarefa tiver movido, a conta de armazenamento não está mais disponível ou outro problema foi encontrado que impede a cópia bem-sucedida dos arquivos para o nó.

- **Falhas de aplicativo**

    O processo especificado por linha de comando da tarefa também pode falhar. O processo é considerado falha quando um código de saída diferente de zero é retornado pelo processo que é executado pela tarefa (consulte *códigos de saída de tarefa* na próxima seção).

    Falhas do aplicativo, você pode configurar lote para repetir automaticamente a tarefa até um número especificado de vezes.

- **Falhas de restrição**

    Você pode definir uma restrição que especifica a duração máxima de execução para uma tarefa ou tarefa, o *maxWallClockTime*. Isso pode ser útil para encerrar tarefas "suspenso".

    Quando a quantidade máxima de tempo foi excedida, a tarefa está marcada como *concluída*, mas o código de saída está definido como `0xC000013A` e o campo *schedulingError* é marcado como `{ category:"ServerError", code="TaskEnded"}`.

### <a name="debugging-application-failures"></a>Depuração falhas de aplicativo

- `stderr`e`stdout`

    Durante a execução, um aplicativo pode produzir saída de diagnóstico que você pode usar para solucionar problemas. Conforme mencionado na seção anterior, [arquivos e pastas](#files-and-directories), o serviço de lote grava saída padrão e saída de erro padrão para `stdout.txt` e `stderr.txt` arquivos no diretório de tarefa no nó de computadores. Você pode usar o portal do Azure ou uma das SDK do lote para baixar esses arquivos. Por exemplo, você pode recuperar esses e outros arquivos para fins de solução de problemas usando [ComputeNode.GetNodeFile] [ net_getfile_node] e [CloudTask.GetNodeFile] [ net_getfile_task] na biblioteca de lote .NET.

- **Códigos de saída de tarefa**

    Conforme mencionado anteriormente, uma tarefa está marcada como falha pelo serviço lote se o processo que é executado pela tarefa retorna um código de saída diferente de zero. Quando uma tarefa executa um processo, o lote preenche a propriedade de código de saída da tarefa com o *código do processo de retorno*. É importante observar que o código de saída de uma tarefa é **não** determinado pelo serviço lote – ele é determinado pelo sistema operacional no qual o processo executado ou o próprio processo.

### <a name="accounting-for-task-failures-or-interruptions"></a>Estatísticas de falhas na tarefa ou interrupções

Tarefas ocasionalmente podem falhar ou ser interrompidas. O próprio aplicativo tarefa pode falhar, o nó no qual a tarefa está executando pode ser reiniciado ou o nó pode ser removido do pool durante uma operação de redimensionamento se dealocação do pool estiver definida para remover nós imediatamente sem aguardar tarefas concluir. Em todos os casos, a tarefa pode ser automaticamente recolocado na fila por lote para execução em outro nó.

Também é possível para um problema intermitente fazer com que uma tarefa ou demora muito tempo para executar. Você pode definir o tempo de execução máximo de uma tarefa. Se for excedido, o lote interrompe o aplicativo à tarefa.

### <a name="connecting-to-compute-nodes"></a>Conectar-se para calcular nós

Você pode executar depuração e solução de problemas ao entrar no remotamente um nó de computação adicionais. Você pode usar o portal do Azure para baixar um arquivo do protocolo RDP (Remote Desktop) para nós do Windows e obter informações de conexão SSH (Secure Shell) para nós Linux. Você também pode fazer isso usando as APIs de lote – por exemplo, com o [Lote .NET] [ net_rdpfile] ou [Lote Python](batch-linux-nodes.md#connect-to-linux-nodes).

>[AZURE.IMPORTANT] Para se conectar a um nó via RDP ou SSH, primeiro você deve criar um usuário no nó. Para fazer isso, você pode usar o Azure portal, [Adicione uma conta de usuário para um nó] [ rest_create_user] usando a API REST lote, chame o [ComputeNode.CreateComputeNodeUser] [ net_create_user] método em lote .NET ou chamada a [add_user] [ py_add_user] método no módulo lote Python.

### <a name="troubleshooting-bad-compute-nodes"></a>Nós de computadores de solução de problemas "ruim"

Em situações em que algumas das suas tarefas estão falhando, seu aplicativo de cliente do lote ou serviço pode examinar os metadados das tarefas com falha para identificar um nó com comportamento inadequado. Cada nó em um pool recebe uma ID exclusiva, e o nó em que uma tarefa é executada está incluído nos metadados de tarefa. Depois de ter identificado um nó de problema, você poderá executar várias ações com ele:

- **Reinicie o nó** ([REST][rest_reboot] | [.NET][net_reboot])

    Reiniciar o nó às vezes pode limpar latentes problemas como processos presos ou falha. Observe que se seu pool usa uma tarefa de início ou seu trabalho usa uma tarefa de preparação do trabalho, eles são executados quando o nó for reiniciado.

- **Imagem do nó** ([REST][rest_reimage] | [.NET][net_reimage])

    Isso reinstala o sistema operacional no nó. Assim como reinicializar um nó, iniciar tarefas e tarefas de preparação do trabalho são novamente após o nó foi criado.

- **Remover o nó do pool** ([REST][rest_remove] | [.NET][net_remove])

    Às vezes, é necessário remover completamente o nó do pool.

- **Desabilitar o nó de agendamento de tarefas** ([REST][rest_offline] | [.NET][net_offline])

    Isso efetivamente coloca o nó "offline" para que nenhuma tarefa adicional é atribuída a ele, mas permite que o nó permaneça em execução e no pool. Isso permite executar mais investigação da causa das falhas de sem perder os dados da tarefa falhou-- e sem o nó causando falhas de tarefas adicionais. Por exemplo, você pode desativar o agendamento no nó, então [entrar remotamente](#connecting-to-compute-nodes) para examinar os logs de eventos do nó ou executar outras soluções de problemas de tarefas. Após terminar de sua investigação, em seguida, você pode trazer o nó online novamente habilitando o agendamento da tarefa ([REST][rest_online] | [.NET][net_online]), ou executar uma das outras ações discutidas anteriormente.

> [AZURE.IMPORTANT] Com cada ação que está descrita nesta seção, reinicie, nova imagem, remover e desativar agendamento de tarefas – você pode especificar como tarefas em execução no nó são tratadas quando você executa a ação. Por exemplo, quando você desativa o agendamento de tarefas em um nó usando a biblioteca de cliente .NET de lote, você pode especificar um [DisableComputeNodeSchedulingOption] [ net_offline_option] valor de enumeração para especificar se a **Terminate** executando tarefas, **Requeue** -los para agendamento em outros nós, ou permitir a execução de tarefas para ser concluída antes de executar a ação (**TaskCompletion**).

## <a name="next-steps"></a>Próximas etapas

- Percorra um aplicativo de lote de exemplo passo a passo em [começar a usar a biblioteca de lote do Azure para .NET](batch-dotnet-get-started.md). Também há uma [versão de Python](batch-python-tutorial.md) do tutorial que é executado uma carga de trabalho em nós de computação Linux.

- Baixar e criar o [Lote Explorer] [ github_batchexplorer] projeto de amostra para uso enquanto você desenvolver soluções em lotes. Usando o Explorador de lote, você pode executar o seguinte e muito mais:
  - Monitorar e manipular pools, trabalhos e tarefas em sua conta em lotes
  - Baixar `stdout.txt`, `stderr.txt`e outros arquivos de nós
  - Criar usuários em nós e baixar arquivos RDP para logon remoto

- Saiba como [criar pools de nós de computação Linux](batch-linux-nodes.md).

- Visite o [Fórum do Azure lote] [ batch_forum] no MSDN. O fórum é um bom lugar faça perguntas, se você estiver aprendendo apenas ou um especialista usando lote.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
