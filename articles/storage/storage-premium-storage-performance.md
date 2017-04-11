<properties
    pageTitle="Armazenamento do Azure Premium: Projetar para desempenho | Microsoft Azure"
    description="Aplicativos de alto desempenho usando o armazenamento do Azure Premium de design. Armazenamento de Premium oferece suporte de disco de alto desempenho e baixa latência para eu/O-que requer muita cargas de trabalho em execução em máquinas virtuais do Azure."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="aungoo"/>

# <a name="azure-premium-storage-design-for-high-performance"></a>Armazenamento do Azure Premium: Design de alto desempenho

## <a name="overview"></a>Visão geral  
Este artigo fornece diretrizes para criação de aplicativos de alto desempenho usando o armazenamento do Azure Premium. Você pode usar as instruções fornecidas neste documento combinado com práticas recomendadas de desempenho aplicáveis a tecnologias usadas pelo seu aplicativo. Para ilustrar as diretrizes, usamos SQL Server em execução no armazenamento Premium como exemplo neste documento.

Enquanto vamos abordar cenários de desempenho para a camada de armazenamento neste artigo, você precisará otimizar a camada de aplicativo. Por exemplo, se você estiver hospedando um Farm do SharePoint no armazenamento do Azure Premium, você pode usar os exemplos de SQL Server deste artigo para otimizar o servidor de banco de dados. Além disso, otimize o servidor Web e servidor de aplicativo para obter a maioria do desempenho do Farm do SharePoint.

Este artigo ajudará resposta acompanhando perguntas comuns sobre como otimizar o desempenho do aplicativo no armazenamento do Azure Premium,

-   Como medir o desempenho do aplicativo?  
-   Por que você não está vendo esperado alto desempenho?  
-   Quais fatores influenciam o desempenho do aplicativo no armazenamento Premium?  
-   Como esses fatores influenciam o desempenho do seu aplicativo no armazenamento Premium?  
-   Como você pode otimizar para IOPS, largura de banda e a latência?  

Fornecemos estas diretrizes especificamente para armazenamento de Premium porque cargas de trabalho em execução no armazenamento Premium são altamente confidenciais de desempenho. Fornecemos exemplos onde apropriado. Você também pode aplicar algumas dessas diretrizes para aplicativos executados em VMs IaaS com discos de armazenamento padrão.

Antes de começar, se você estiver começando a usar o armazenamento de Premium, primeiro leia o [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](storage-premium-storage.md) artigo e [metas de desempenho e escalabilidade de armazenamento de Premium do Azure](storage-scalability-targets.md#premium-storage-accounts).

## <a name="application-performance-indicators"></a>Indicadores de desempenho do aplicativo  
Podemos avaliar se um aplicativo está executando bem ou não usar, como indicadores de desempenho, a velocidade um aplicativo está processando uma solicitação de usuário, quanto um aplicativo é processamento por solicitação de dados, quantas solicitações é um processamento de aplicativos em um período específico de tempo, quanto tempo que um usuário tem de esperar obter uma resposta após enviar sua solicitação. Os termos técnicos para esses indicadores de desempenho são, IOPS, produtividade ou largura de banda e a latência.

Nesta seção, abordaremos os indicadores de desempenho comuns no contexto de armazenamento Premium. Na seção a seguir, a coleta de requisitos do aplicativo, você aprenderá como medir desses indicadores de desempenho para o seu aplicativo. Mais tarde otimizando o desempenho de aplicativo, você aprenderá sobre os fatores que afetam esses indicadores de desempenho e recomendações para otimizá-los.

## <a name="iops"></a>IOPS  
IOPS é o número de solicitações de que seu aplicativo está enviando para os discos de armazenamento em um segundo. Uma operação de entrada/saída poderia ser lido ou escrever, sequencial ou aleatório. Aplicativos de OLTP como um site de varejo online precisam processar muitas solicitações de usuário simultâneas imediatamente. As solicitações de usuário são inserir e atualizar transações de banco de dados intenso, o que o aplicativo deve processar rapidamente. Portanto, aplicativos OLTP requerem IOPS muito alto. Esses aplicativos manipulam milhões de solicitações de IO pequenas e aleatórias. Se você tiver um aplicativo desse tipo, você deve projetar a infraestrutura de aplicativo para otimizar para IOPS. Na seção posterior, *Otimizando o desempenho do aplicativo*, vamos abordar detalhadamente todos os fatores que você deve considerar obter IOPS alta.

Quando você anexa um disco de armazenamento premium à sua máquina virtual, de escala alta disposições Azure para você um número garantido de IOPS de acordo com as especificação do disco. Por exemplo, um disco P30 provisiona 5000 IOPS. Cada escala alta tamanho de máquina virtual também tem um limite IOPS específico que ele pode sustentar. Por exemplo, uma VM GS5 padrão tem 80.000 IOPS limitar.

## <a name="throughput"></a>Taxa de transferência  
Produtividade ou largura de banda é a quantidade de dados que seu aplicativo está enviando para os discos de armazenamento em um intervalo especificado. Se seu aplicativo estiver executando operações de entrada/saída com tamanhos grandes de unidade IO, ela requer alta taxa de transferência. Aplicativos de data warehouse tendem a execute operações de varredura intenso que acessar grandes partes de dados de cada vez e comumente executam operações em massa. Em outras palavras, esses aplicativos requerem maior produtividade. Se você tiver um aplicativo desse tipo, você deve projetar sua infraestrutura otimizar para taxa de transferência. A próxima seção, vamos abordar em detalhes os fatores que você deve ajustar para fazer isso.

Quando você anexa um disco de armazenamento premium em uma escala alta máquina virtual, disposições Azure produtividade de acordo com as especificação disco. Por exemplo, um disco P30 provisiona 200 MB por segundo disco produtividade. Cada escala alta tamanho de máquina virtual também tem como limite de produtividade específico que ele pode sustentar. Por exemplo, VM GS5 padrão tem uma taxa de transferência máxima de 2.000 MB por segundo.

Não há uma relação entre produtividade e IOPS conforme mostrado na fórmula abaixo.

![](media/storage-premium-storage-performance/image1.png)

Portanto, é importante determinar os valores de produtividade e IOPS ideais que seu aplicativo requer. Como você tentar otimizar um, o outro também obtém afetado. Em uma seção posterior, *Otimizando o desempenho do aplicativo*, abordaremos em mais detalhes sobre como otimizar IOPS e produtividade.

## <a name="latency"></a>Latência  
Latência é o tempo que leva um aplicativo para receber uma única solicitação, envie-o para os discos de armazenamento e enviar a resposta para o cliente. Esta é uma medida crítica de desempenho de um aplicativo além dos IOPS e produtividade. A latência de um disco de armazenamento premium é o tempo necessário para recuperar as informações de uma solicitação e comunicá-lo de volta para seu aplicativo. Armazenamento de Premium fornece consistentes latências baixos. Se você habilitar armazenamento em cache em mídias de armazenamento premium de host de somente leitura, você pode obter muito menor latência de leitura. Abordaremos cache de disco mais detalhadamente adiante *otimização de*desempenho do aplicativo.

Quando você estiver otimizando seu aplicativo para obter mais altos IOPS e produtividade, ela afetará a latência do seu aplicativo. Depois de ajustar o desempenho do aplicativo, sempre avalie a latência do aplicativo para evitar comportamento inesperado alta latência.

## <a name="gather-application-performance-requirements"></a>Reunir requisitos de desempenho do aplicativo  
A primeira etapa na criação de aplicativos de alto desempenho em execução no Azure Premium armazenamento é, entender os requisitos de desempenho do seu aplicativo. Depois de reunir requisitos de desempenho, você pode otimizar seu aplicativo para obter o melhor desempenho.

Na seção anterior, podemos explicado os indicadores de desempenho comuns, IOPS, produtividade e latência. Você deve identificar quais desses indicadores de desempenho são críticas ao seu aplicativo para fornecer a experiência de usuário desejada. Por exemplo, IOPS alta é mais relevante para aplicativos OLTP milhões de transações de processamento em um segundo. Enquanto o alta taxa de transferência é fundamental para aplicativos de Data Warehouse processar grandes quantidades de dados em um segundo. Muito baixa latência é essencial para aplicativos em tempo real, como sites de streaming de vídeo ao vivo.

Em seguida, medir os requisitos de desempenho máximo do seu aplicativo em todo o seu ciclo de vida. Use a lista de verificação de exemplo abaixo como um início. Grave os requisitos de desempenho máximo durante normal, pico e horários períodos de carga de trabalho. Identificando requisitos para todos os níveis de cargas de trabalho, você poderá determinar a exigência de desempenho geral do seu aplicativo. Por exemplo, a carga de trabalho normal de um site de comércio serão as transações que ele serve durante a maioria dos dias em um ano. A carga de trabalho de pico do site serão as transações que ele serve durante as férias ou eventos de venda especial. A carga de trabalho de pico é geralmente experiente por um período limitado, mas pode exigir seu aplicativo dimensionar duas ou mais vezes sua operação normal. Descubra o percentil 50 90 percentil requisitos e 99 percentil. Isso ajuda a filtrar qualquer destaques nos requisitos de desempenho e você pode focalizar seus esforços otimizando para os valores corretos.

**Lista de verificação de requisitos do aplicativo de desempenho**

| **Requisitos de desempenho** | **50 percentil** | **90 percentil** | **Percentil 99** |
|---|---|---|---|
| Máx. Transações por segundo | | | |
| Operações de leitura %            | | | |
| Operações de gravação de %           | | | |
| Operações aleatório %          | | | |
| Operações sequenciais %      | | | |
| Tamanho da solicitação de IO              | | | |
| Produtividade média           | | | |
| Máx. Taxa de transferência              | | | |
| Min. Latência                 | | | |
| Latência média              | | | |
| Máx. CPU                     | | | |
| CPU média                  | | | |
| Máx. Memória                  | | | |
| Memória média               | | | |
| Profundidade da fila                  | | | |

>**Observação importante:**  
>Você deve considerar dimensionamento esses números com base em crescimento futuro esperado do seu aplicativo. É uma boa ideia planejar o crescimento antecedência, porque ela pode ser mais difícil para alterar a infraestrutura para melhorar o desempenho mais tarde.

Se você tiver um aplicativo existente e deseja mover para armazenamento de Premium, primeiro crie a lista de verificação acima para o aplicativo existente. Em seguida, criar um protótipo do seu aplicativo Premium armazenamento e o aplicativo com base em diretrizes descritas no *Otimizando o desempenho do aplicativo* em uma seção posterior deste documento de design. A próxima seção descreve as ferramentas que você pode usar para reunir as medidas de desempenho.

Crie uma lista de verificação semelhante ao seu aplicativo existente para o protótipo. Usando ferramentas de Benchmarking pode simular as cargas de trabalho e medir o desempenho no aplicativo de protótipo. Consulte a seção sobre [Benchmarking](#benchmarking) para saber mais. Ao fazer isso, você pode determinar se o armazenamento de Premium pode corresponder ou ultrapasse seus requisitos de desempenho do aplicativo. Em seguida, você pode implementar as mesmas diretrizes para seu aplicativo de produção.

### <a name="counters-to-measure-application-performance-requirements"></a>Contadores para medir requisitos de desempenho do aplicativo  
A melhor maneira de medir requisitos de desempenho do seu aplicativo, é usar as ferramentas de monitoramento de desempenho fornecidas pelo sistema operacional do servidor. Você pode usar o desempenho para Windows e iostat para Linux. Essas ferramentas capturam contadores correspondente a cada medida explicada na seção acima. Você deve capturar os valores desses contadores quando seu aplicativo está executando sua normal, pico e horários cargas de trabalho.

Contadores de desempenho estão disponíveis para processador, memória, cada disco lógico e físico disco do seu servidor. Quando você usa discos de armazenamento premium com uma máquina virtual, os contadores de disco físico são para cada disco de armazenamento premium e contadores de disco lógico são para cada volume criado nas mídias de armazenamento premium. Você deve capturar os valores para os discos que hospedam sua carga de trabalho do aplicativo. Se houver um mapeamento de um-para-um entre discos lógicos e físicos, você pode consultar a contadores de disco físico; Caso contrário, consulte os contadores de disco lógico. No Linux, o comando iostat gera um relatório de utilização de CPU e disco. O relatório de utilização do disco fornece estatísticas por dispositivo físico ou partição. Se você tiver um servidor de banco de dados com seus dados e o log em discos separados, colete esses dados para ambos os discos. Tabela abaixo descreve contadores para discos, processadores e memória:

| Contador | Descrição | Desempenho | Iostat |
|---|---|---|---|
| **IOPS ou transações por segundo** | Número de solicitações de i emitido para o disco de armazenamento por segundo. | Leituras de disco/s <br> Gravações de disco/s | TPS <br> r/s <br> w/s |
| **Leituras de disco e gravações** | % de leituras e gravar operações executadas no disco. | % Tempo de leitura de disco <br> % De tempo de gravação de disco | r/s <br> w/s |
| **Taxa de transferência** | Quantidade de dados lido ou gravado no disco por segundo. | Disco Bytes lidos/s <br> Bytes de gravação de disco/s | kB_read/s <br> kB_wrtn/s |
| **Latência** | Tempo total para concluir uma solicitação de IO do disco. | Médio disco seg/leitura <br> Média de disco s/gravação | aguardar <br> svctm |
| **Tamanho de IO** | O tamanho de i/o solicitações de problemas para os discos de armazenamento. | Bytes de disco média/leitura <br> Bytes de disco média/gravação | avgrq-sz |
| **Profundidade da fila** | Número de e/s pendentes solicitações espera para leitura formulário ou gravados no disco de armazenamento. | Comprimento da fila de disco atual | avgqu-sz |
| **Máx. Memória** | Quantidade de memória necessária para executar o aplicativo tranquilamente | % Confirmada Bytes em uso | Use vmstat |
| **Máx. CPU** | Valor CPU necessária para executar o aplicativo tranquilamente | % Tempo de processador | camada de % |

Saiba mais sobre o [iostat](http://linuxcommand.org/man_pages/iostat1.html) e o [desempenho](https://msdn.microsoft.com/library/aa645516.aspx).


## <a name="optimizing-application-performance"></a>Otimização de desempenho de aplicativo  
Os principais fatores que influenciam o desempenho de um aplicativo em execução no armazenamento Premium são natureza de IO solicitações, tamanho de máquina virtual, tamanho de disco, número de discos, cache de disco, Multithreading e de profundidade de fila. Você pode controlar alguns desses fatores com botões fornecidos pelo sistema. A maioria dos aplicativos não pode apresentar uma opção para alterar o tamanho de IO e profundidade de fila diretamente. Por exemplo, se você estiver usando o SQL Server, você não pode escolher a intensidade da AE fila e tamanho. SQL Server escolhe os ideal IO fila profundidade valores de tamanho e para obter o desempenho a maioria dos. É importante entender os efeitos dos dois tipos de fatores no desempenho do aplicativo, para que você pode provisionar recursos apropriados para atender às necessidades de desempenho.

Ao longo desta seção, consulte a lista de verificação de requisitos de aplicativo que você criou, para identificar quanto você precisa otimizar o desempenho do aplicativo. Com base no que, você poderá determinar quais fatores desta seção você precisará ajustar. Para testemunhar os efeitos de cada fator no desempenho do aplicativo, execute ferramentas benchmarking em sua instalação do aplicativo. Consulte a seção [Benchmarking](#Benchmarking) no final deste artigo para obter as etapas executar ferramentas benchmarking comuns no Windows e Linux VMs.

### <a name="optimizing-iops-throughput-and-latency-at-a-glance"></a>Otimizar IOPS, a produtividade e a latência num relance  
A tabela abaixo resume todos os fatores de desempenho e as etapas para otimizar IOPS, a produtividade e a latência. As seções seguintes neste resumo descreverá cada fator é muito mais profundidade.

| | **IOPS** | **Taxa de transferência** | **Latência** |
|---|---|---|---|
| **Cenário de exemplo** | Aplicativo empresarial OLTP exigindo transações muito altas taxa de segundo.                                                                                                                                 | Dados da empresa warehouse aplicativo processamento grandes quantidades de dados. | Próximo aplicativos em tempo real que exigem instantâneas respostas para solicitações de usuário, como jogos online. |
| Fatores de desempenho  | | | |
| **Tamanho de IO** | Tamanho menor de IO produz IOPS superior.                                                                                                                                                                           | Tamanho maior de IO para produz maior produtividade. | |
| **Tamanho de máquina virtual** | Use um tamanho de máquina virtual que oferece IOPS maior do que a sua necessidade de aplicativo. Consulte tamanhos de máquina virtual e seus limites IOPS aqui. | Use um tamanho de máquina virtual com limite de produtividade maior do que a sua necessidade de aplicativo. Consulte tamanhos de máquina virtual e seus limites de produtividade aqui. | Use um tamanho de máquina virtual que oferece dimensionar limites maiores do que a sua necessidade de aplicativo. Consulte tamanhos de máquina virtual e seus limites aqui. |
| **Tamanho do disco** | Use um tamanho de disco que oferece IOPS maior do que a sua necessidade de aplicativo. Consulte tamanhos de disco e seus limites IOPS aqui. | Use um tamanho de disco com limite de produtividade maior do que a sua necessidade de aplicativo. Consulte tamanhos de disco e seus limites de produtividade aqui. | Use um tamanho de disco que oferece dimensionar limites maiores do que a sua necessidade de aplicativo. Consulte tamanhos de disco e seus limites aqui. |
| **Máquina virtual e limites de escala de disco** | Limite IOPS do tamanho da máquina virtual escolhido deve ser maior que IOPS total controlados por discos de armazenamento de premium anexados a ele. | Limite de produtividade do tamanho da máquina virtual escolhido deve ser maior do que a taxa de transferência total controlada por discos de armazenamento de premium anexados a ele. | Limites de escala do tamanho da máquina virtual escolhido devem ser maiores que limites de escala total de discos de armazenamento premium anexado. |
| **Cache de disco** | Habilite o Cache de somente leitura em mídias de armazenamento premium com operações pesadas de leitura para obter mais altos IOPS de leitura. | | Habilite o Cache de somente leitura em mídias de armazenamento premium com operações pesadas prontas para obter muito pouca leitura latências. |
| **Distribuição do disco** | Usar vários discos e de distribuição-las para obter um limite IOPS e produtividade mais alto combinado. Observe que o limite combinado por máquina virtual deve ser maior do que os limites combinados de discos premium anexado. | |
| **Tamanho de distribuição** | Menor tamanho de distribuição para pequeno padrão de IO aleatório visto em aplicativos OLTP. Por exemplo, use o tamanho de faixa de 64KB para aplicativo OLTP do SQL Server. | Tamanho de distribuição maior para grandes sequencial padrão de IO visto em aplicativos de Data Warehouse. Por exemplo, use o tamanho de distribuição de 256KB de aplicativo de depósito de dados do SQL Server. | |
| **Multithread** | Use multithread por push número maior de solicitações para armazenamento de Premium que conduzirá a maiores IOPS e produtividade. Por exemplo, no SQL Server definir um valor MAXDOP alto para alocar mais CPUs para SQL Server. | |
| **Profundidade da fila** | Maior profundidade de fila produz IOPS superior. | Maior profundidade de fila produz maior produtividade. | Menor profundidade de fila produz latências inferiores. |

## <a name="nature-of-io-requests"></a>Natureza das solicitações de IO  
Uma solicitação de IO é uma unidade de operação de entrada/saída que seu aplicativo executarão. Identificando a natureza das solicitações de IO, aleatórias ou sequenciais, ler ou gravar, pequeno ou grande, ajudará determinar os requisitos de desempenho do seu aplicativo. É muito importante entender a natureza das solicitações de IO, para tomar decisões direita ao criar sua infraestrutura de aplicativo.

Tamanho de IO é um dos fatores mais importantes. O tamanho de IO é o tamanho da solicitação de operação de entrada/saída gerado pelo seu aplicativo. O tamanho de IO tem um impacto significativo no desempenho especialmente na IOPS e largura de banda que o aplicativo seja capaz de alcançar. A fórmula a seguir mostra a relação entre IOPS, tamanho de IO e largura de banda/produtividade.  
    ![](media/storage-premium-storage-performance/image1.png)

Alguns aplicativos permitem que você altere seu tamanho de IO, enquanto alguns aplicativos não. Por exemplo, SQL Server determina o tamanho de IO ideal em si e não fornecer aos usuários qualquer botões alterá-lo. Por outro lado, Oracle oferece um parâmetro chamado [DB\_bloquear\_tamanho](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) usando o que você pode configurar o tamanho da solicitação de i/o do banco de dados.

Se você estiver usando um aplicativo, que não permite que você alterar o tamanho de IO, use as diretrizes neste artigo para otimizar o desempenho KPI que é mais relevante para seu aplicativo. Por exemplo,

-   Um aplicativo OLTP gera milhões de solicitações de IO pequenas e aleatórias. Para lidar com esses tipos de solicitações de IO, você deve projetar sua infraestrutura de aplicativo para obter mais altos IOPS.  
-   Uma data warehouse aplicativo gera solicitações de IO grandes e sequenciais. Para lidar com esses tipos de solicitações de IO, você deve projetar sua infraestrutura de aplicativo para obter maior largura de banda ou produtividade.

Se você estiver usando um aplicativo, que permite que você alterar o tamanho de IO, use esta regra geral para o tamanho de IO além das outras diretrizes de desempenho,

-   Tamanho menor de IO obter IOPS superior. Por exemplo, 8 KB para um aplicativo de OLTP.  
-   Tamanho maior de IO obter largura de banda/produtividade maior. Por exemplo, 1024 KB para um aplicativo de data warehouse.

Aqui está um exemplo em como você pode calcular o IOPS e produtividade/largura de banda para o seu aplicativo. Considere a possibilidade de um aplicativo usando um disco de P30. O máximo IOPS e produtividade/largura de banda um disco P30 podem alcançar é 5000 IOPS e 200 MB por segundo respectivamente. Agora, se seu aplicativo requer o máximo de IOPS do disco P30 e usar um tamanho menor de IO como 8 KB, a largura de banda resultante, você poderá obter é 40 MB por segundo. No entanto, se seu aplicativo requer a produtividade/largura de banda máxima do disco P30, e você usa um tamanho maior de IO como 1024 KB, o IOPS resultante será menor, 200 IOPS. Portanto, ajuste o tamanho de IO, de forma que ele atende aos requisitos de IOPS e produtividade/largura de banda de ambas do seu aplicativo. Tabela abaixo resume os diferentes tamanhos de IO seus correspondentes IOPS e transferência de um disco de P30.

| **Requisito de aplicativo** | **Tamanho** | **IOPS** | **Produtividade/largura de banda** |
|-----------------------------|--------------|----------|--------------------------|
| Max IOPS                    | 8 KB         | 5.000    | 40 MB por segundo         |
| Produtividade max              | 1024 KB      | 200      | 200 MB por segundo        |
| Max Throughput + IOPS alto  | 64 KB        | 3.200    | 200 MB por segundo        |
| Max IOPS + alta taxa de transferência  | 32 KB        | 5.000    | 160 MB por segundo        |

Para obter IOPS e largura de banda maior que o valor máximo de um disco de armazenamento premium único, use vários discos premium distribuídos juntos. Por exemplo, faixa dois P30 discos para obter uma IOPS combinado de 10.000 IOPS ou uma taxa de transferência combinada de 400 MB por segundo. Conforme explicado na próxima seção, você deve usar um tamanho de máquina virtual que ofereça suporte a combinado IOPS e transferência de disco.

>**Observação:**  
>À medida que você aumenta IOPS ou produtividade outro também aumenta, verifique se que você não clicar em produtividade ou limites IOPS do disco ou da máquina virtual ao aumentar um.

Para testemunhar os efeitos de tamanho de IO no desempenho do aplicativo, você pode executar ferramentas benchmarking no máquina virtual e o disco. Crie várias execuções de teste e use o tamanho de IO diferente para cada execução para ver o impacto. Consulte a seção [Benchmarking](#Benchmarking) no final deste artigo para obter mais detalhes.

## <a name="high-scale-vm-sizes"></a>Tamanhos de máquina virtual de alta escala  
Quando você começa a criação de um aplicativo, uma das primeiras coisas a fazer é, escolha uma máquina virtual para hospedar seu aplicativo. Premium armazenamento vem com tamanhos de máquina virtual de escala alta que podem executar aplicativos que exigem maior capacidade de computação e um alto desempenho e/s de disco local. Essas VMs fornecem processadores mais rápidos, uma taxa de memória-to-core superior e um Solid-State unidade SSD () para o disco local. Exemplos de alta VMs de escala de armazenamento Premium de suporte são a série DS, DSv2 e GS VMs.

Alta escala VMs estão disponíveis em tamanhos diferentes com um número diferente de cores de CPU, memória, sistema operacional e tamanho de disco temporário. O tamanho de cada máquina virtual também tem o número máximo de discos de dados que você pode anexar para a máquina virtual. Portanto, o tamanho de máquina virtual escolhido afetarão a quantidade de processamento, memória, e a capacidade de armazenamento está disponível para o seu aplicativo. Ele também afeta a computação e armazenamento de custo. Por exemplo, a seguir, as especificações do maior tamanho de máquina virtual em uma série de DS, série de DSv2 e uma série de GS:

| Tamanho de máquina virtual | Cores de CPU | Memória | Tamanhos de disco de máquina virtual | Máx. discos de dados | Tamanho do cache | IOPS | Limites de Cache IO de largura de banda |
|---|---|---|---|---|---|---|---|
| Standard_DS14 | 16 | GB 112 | SISTEMA OPERACIONAL = GB 1023 <br> SSD local = 224 GB | 32 | 576 GB | 50.000 IOPS <br> 512 MB por segundo | 4.000 IOPS e 33 MB por segundo |
| Standard_GS5 | 32 | 448 GB | SISTEMA OPERACIONAL = GB 1023 <br> SSD local = 896 GB | 64 | 4224 GB | 80.000 IOPS <br> 2.000 MB por segundo | 5.000 IOPS e 50 MB por segundo |

Para exibir uma lista completa de todos os tamanhos de máquina virtual do Azure disponíveis, consulte [tamanhos de máquina virtual do Windows](../virtual-machines/virtual-machines-windows-sizes.md) ou [tamanhos de máquina virtual Linux](../virtual-machines/virtual-machines-linux-sizes.md). Escolha um tamanho de máquina virtual que possa atender e dimensionar às suas necessidades de desempenho do aplicativo desejado. Além disso, leve em consideração acompanhando importantes considerações ao escolher tamanhos de máquina virtual.


*Limites de escala*  
Os limites máximos de IOPS por máquina virtual e por disco são diferentes e independentes uns dos outros. Certifique-se de que o aplicativo está orientando IOPS dentro dos limites da máquina virtual bem como discos premium conectados a ele. Caso contrário, o desempenho do aplicativo terão a otimização.

Como exemplo, suponha que um requisito de aplicativo é um máximo de 4.000 IOPS. Para fazer isso, você provisionar um disco P30 em uma máquina virtual DS1. O disco de P30 pode oferecer até 5.000 IOPS. No entanto, a máquina virtual DS1 está limitada aos 3.200 IOPS. Consequentemente, o desempenho do aplicativo serão restritos pelo limite de máquina virtual em 3.200 IOPS e haverá degradação do desempenho. Para evitar essa situação, escolha um tamanho de máquina virtual e disco que será ambos atende aos requisitos de aplicativo.

*Custo de operação*  
Em muitos casos, é possível que o custo total de operação usando armazenamento Premium seja menor do que usar o armazenamento padrão.

Por exemplo, considere um aplicativo que exigem 16.000 IOPS. Para obter este desempenho, você precisará de um padrão\_D14 Azure IaaS máquina virtual, que pode fornecer um máximo de IOPS de 16.000 usando 32 discos de 1TB de armazenamento padrão. Cada disco de armazenamento padrão de 1TB pode obter um máximo de 500 IOPS. O custo estimado desta máquina virtual por mês será r $1,570. O custo mensal de 32 discos de armazenamento padrão será r $1,638. O custo mensal total estimado será r $3,208.

No entanto, se você hospedado o mesmo aplicativo armazenamento Premium, será necessário um tamanho de máquina virtual menor e menos discos de armazenamento de premium, reduzindo o custo geral. Um padrão\_máquina virtual DS13 pode atender a exigência de IOPS 16.000 usando quatro discos P30. A máquina virtual DS13 tem um máximo de IOPS de 25,600 e cada disco P30 tem um máximo de IOPS de 5.000. Em geral, essa configuração pode alcançar 5.000 x 4 = 20.000 IOPS. O custo estimado desta máquina virtual por mês será r $1,003. O custo mensal do quatro discos de armazenamento do P30 premium será r $544.34. O custo mensal total estimado será $1,544.

Tabela abaixo resume a divisão de custo desse cenário para armazenamento de Premium e padrão.

| | **Padrão** | **Premium** |
|---|---|---|
| **Custo da máquina virtual por mês** | $1,570.58 (padrão\_D14)   | $1,003.66 (padrão\_DS13) |
| **Custo de discos por mês** | $1,638.40 (discos 32 x 1 TB) | $544.34 (4 x P30 discos) |
| **Custo total por mês**  | $3,208.98 | $1,544.34 |

*Linux Distros*  

Com o armazenamento do Azure Premium, você obtém o mesmo nível de desempenho para VMs executando o Windows e Linux. Oferecemos suporte para vários tipos de Linux distros, e você pode ver a lista completa [aqui](../virtual-machines/virtual-machines-linux-endorsed-distros.md). É importante observar que distros diferentes são mais adequados para tipos diferentes de cargas de trabalho. Você verá diferentes níveis de desempenho dependendo da distribuição que sua carga de trabalho está em execução. Teste a distros Linux com seu aplicativo e escolher o que funciona melhor.


Quando executando Linux com armazenamento Premium, verifique as atualizações mais recentes sobre drivers necessários para garantir alto desempenho.

## <a name="premium-storage-disk-sizes"></a>Tamanhos de disco de armazenamento Premium  
Atualmente, o armazenamento do Azure Premium oferece três tamanhos de disco. Tamanho de cada disco tem um limite de escala diferente para IOPS, largura de banda e armazenamento. Escolha a tamanho de disco de armazenamento Premium dependendo os requisitos do aplicativo e a escala de alta tamanho de máquina virtual direita. A tabela a seguir mostra os tamanhos de três discos e seus recursos.

| **Tipo de disco**       | **P10**           | **P20**           | **P30**           |
|---------------------|-------------------|-------------------|-------------------|
| Tamanho do disco           | Chaveta 128           | 512 Chaveta           | 1024 Chaveta (1 TB)   |
| IOPS por disco       | 500               | 2300              | 5000              |
| Produtividade por disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo |

Quantos discos você escolher depende do disco tamanho escolhido. Você pode usar um único disco P30 ou vários discos P10 para atender às suas necessidades de aplicativo. Leve em considerações de conta listadas abaixo ao fazer a escolha.

*Limites de escala (IOPS e produtividade)*  
Os limites de IOPS e produtividade de tamanho de cada disco Premium é diferente e independente dos limites de escala de máquina virtual. Certifique-se de que o total IOPS e produtividade dos discos estão dentro dos limites de escala do tamanho da máquina virtual escolhido.

Por exemplo, se um requisito de aplicativo é um máximo de 250 MB/s produtividade e você estiver usando uma máquina virtual DS4 com um único disco P30. A máquina virtual DS4 pode dar até 256 MB/seg produtividade. No entanto, um único disco P30 produtividade limite é de 200 MB/seg. Consequentemente, o aplicativo serão restritos em 200 MB/seg devido ao limite de disco. Para contornar esse limite, provisione mais discos de dados para a máquina virtual.

>**Observação:**  
>Leituras servidas pelo cache não são incluídas no disco IOPS e taxa de transferência, portanto, não está sujeita aos limites de disco. Cache tem seu limite IOPS e produtividade separada por máquina virtual.
>
>Por exemplo, inicialmente sua leituras e gravações são 60MB/seg e 40MB/seg respectivamente. Ao longo do tempo, o cache aquecido e serve mais das leituras do cache. Em seguida, você pode obter gravação maior produtividade do disco.

*Número de discos*  
Determine o número de discos, que você precisará avaliando requisitos do aplicativo. O tamanho de cada máquina virtual também tem um limite no número de discos que você pode anexar para a máquina virtual. Normalmente, isso é o dobro de cores. Certifique-se de que o tamanho de máquina virtual que você escolher pode oferece suporte para o número de discos necessários.

Lembre-se de que o disco de armazenamento de Premium tiver maiores recursos de desempenho em comparação com discos de armazenamento padrão. Portanto, se você estiver migrando seu aplicativo do Azure máquina virtual do IaaS usando armazenamento padrão para o armazenamento de Premium, provavelmente será necessário menos discos premium para atingir o desempenho igual ou superior do aplicativo.

## <a name="disk-caching"></a>Cache de disco  
Alta VMs de escala que utilizam o armazenamento do Azure Premium tem uma tecnologia de cache de vários nível chamada BlobCache. BlobCache usa uma combinação de RAM de máquina Virtual e SSD local para armazenamento em cache. Esse cache está disponível para o armazenamento de Premium persistente discos e na máquina virtual local. Por padrão, essa configuração de cache é definida para leitura/gravação para discos de sistema operacional e somente leitura para discos de dados hospedados em armazenamento Premium. Com disco cache ativado nos discos armazenamento Premium, a escala de alta VMs pode obter muito altos níveis de desempenho que excederem o desempenho de disco subjacente.

>[AZURE.WARNING] Alterando a configuração de cache de um disco Azure desconecta e anexa novamente o disco de destino. Se for o disco de sistema operacional, a máquina virtual seja reiniciada. Interrompa todos os aplicativos/serviços que podem ser afetados por este interrupção antes de alterar a configuração de cache de disco.

Para saber mais sobre o funcionamento do BlobCache, consulte dentro postagem de blog de [Armazenamento do Azure Premium](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) .

É importante habilitar o cache no conjunto correto de discos. Você deve habilitar o cache de disco em um disco premium ou não dependerá o padrão de carga de trabalho que o disco será ser manipulação. Tabela abaixo mostra o padrão de configurações de cache para discos de dados e sistema operacional.

| **Tipo de disco** | **Configuração de Cache padrão** |
|---|---|
| Disco do sistema operacional | ReadWrite |
| Disco de dados | Nenhum |

A seguir está as configurações de cache de disco recomendado para discos de dados,

| **Configuração de cache de disco** | **Recomendação sobre quando usar essa configuração** |
|---|---|
| Nenhum | Configure o cache de host como Nenhum para discos somente leitura e gravação pesado. |
| Somente leitura | Configure o cache de host como somente leitura para discos somente leitura e leitura / gravação. |
| ReadWrite | Configure o cache de host como ReadWrite somente se o seu aplicativo manipula adequadamente gravar dados armazenados em cache em discos persistentes quando necessário. |

*Somente leitura*  
Configurando o cache em dados de armazenamento de Premium discos de somente leitura, você pode atingir baixa latência de leitura e obter muito alto IOPS de leitura e produtividade do seu aplicativo. Isso é dois motivos de conclusão

1.  Leituras executadas do cache, que é a memória de máquina virtual e SSD local, são muito mais rápidas que lê a partir do disco de dados, que é o armazenamento de blob do Microsoft Azure.  
2.  Armazenamento de Premium não contar as leituras servidas do cache, na direção do disco IOPS e produtividade. Portanto, seu aplicativo é capaz de alcançar maior IOPS total e a produtividade.

*ReadWrite*  
Por padrão, os discos de SO têm ReadWrite cache habilitado. Recentemente adicionamos suporte ReadWrite cache em dados discos também. Se você estiver usando ReadWrite cache, você deve ter uma maneira adequada para gravar os dados do cache persistentes discos. Por exemplo, SQL Server manipula escrevendo dados armazenados em cache para os discos de armazenamento persistente por conta própria. Usando o cache de ReadWrite com um aplicativo que não processa persistir os dados necessários pode levar a perda de dados, se a máquina virtual falhar.

Como exemplo, você pode aplicar estas diretrizes para SQL Server em execução no armazenamento Premium, fazendo o seguinte,

1.  Configure "Somente leitura" cache em mídias de armazenamento premium hospedagem arquivos de dados.  
    a.  O fast lê do cache de reduzir o tempo de consulta do SQL Server desde que páginas de dados são recuperadas muito mais rápido do cache comparada a diretamente dos dados de discos.  
    b.  Servindo leituras do cache, significa que há produtividade adicional disponíveis de discos de dados premium. SQL Server pode usar este produtividade adicional em direção a recuperação mais páginas de dados e outras operações como backup e restauração, cargas de lote e recria de índice.  
2.  Configurar "Nenhum" cache em mídias de armazenamento premium os arquivos de log de hospedagem.  
    a.  Arquivos de log tem principalmente operações de write-pesado. Portanto, elas não se beneficiar do cache de somente leitura.

## <a name="disk-striping"></a>Distribuição do disco  
Quando uma escala alta que máquina virtual está anexado com vários discos persistente de armazenamento de premium, os discos podem ser distribuídos juntos agregar seus IOPs, largura de banda e a capacidade de armazenamento.

No Windows, você pode usar espaços de armazenamento para distribuição discos juntos. Você deve configurar uma coluna para cada disco em um pool. Caso contrário, o desempenho geral do volume distribuído pode ser menor que o esperado, devido a distribuição desigual de tráfego entre os discos.

Importante: Usando o Gerenciador de servidor UI, você pode definir o número total de colunas até 8 para um volume distribuído. Ao anexar mais de 8 discos, use o PowerShell para criar o volume. Usando o PowerShell, você pode definir o número de colunas igual ao número de discos. Por exemplo, se houver 16 discos em um conjunto de distribuição única; Especifica 16 colunas no parâmetro *NumberOfColumns* do cmdlet do PowerShell *VirtualDisk de novo* .


No Linux, use o utilitário MDADM para distribuição discos juntos. Para obter etapas detalhadas em discos de distribuição no Linux consulte [Configurar o Software RAID no Linux](../virtual-machines/virtual-machines-linux-configure-raid.md).


*Tamanho de distribuição*  
Uma configuração importante na distribuição do disco é o tamanho de distribuição. O tamanho de distribuição ou o tamanho de bloco é o menor fragmento de dados que o aplicativo pode abordar em um volume distribuído. O tamanho de faixa que você definir depende do tipo de aplicativo e seu padrão de solicitação. Se você escolher o tamanho de faixa errado, ele pode levar a alinhamento incorreto de IO, que leva a degradação do desempenho do seu aplicativo.

Por exemplo, se uma solicitação de IO gerada pelo seu aplicativo for maior do que o tamanho da faixa de disco, o sistema de armazenamento grava limites unidade de distribuição em mais de um disco. Quando é hora de acessar os dados, ele terá buscar em mais de um unidades de distribuição para concluir a solicitação. O efeito cumulativo de tal comportamento pode levar a degradação do desempenho substanciais. Por outro lado, se o tamanho da solicitação de IO for menor do que o tamanho de distribuição e se ele é de natureza aleatório, as solicitações de IO podem adicionar no mesmo disco causando gargalos e finalmente afetar o desempenho de IO.


Dependendo do tipo de carga de trabalho que seu aplicativo está executando, escolha um tamanho de distribuição apropriada. Para solicitações de IO pequenas aleatórias, use um tamanho menor de distribuição. Enquanto o para grande IO sequencial solicitações usam um tamanho maior de distribuição. Descubra as recomendações de tamanho de distribuição para o aplicativo que será executado em armazenamento Premium. Para SQL Server, configure o tamanho de faixa de 64KB para cargas de trabalho OLTP e 256KB warehouse cargas de trabalho de dados. Consulte [práticas recomendadas de desempenho para o SQL Server em VMs do Azure](../virtual-machines/virtual-machines-windows-sql-performance.md#disks-and-performance-considerations) para saber mais.


>**Observação:**  
>Você pode distribuição juntos um máximo de 32 premium armazenamento discos em uma série de DS máquina virtual e 64 premium armazenamento em uma série de GS máquina virtual.

## <a name="multi-threading"></a>Vários threads  
Azure foi projetado plataforma de armazenamento de Premium sejam paralelas em massa. Portanto, um aplicativo multithread atinge muito desempenho maior do que um aplicativo de segmento único. Um aplicativo multithread divide as suas tarefas entre vários threads e aumenta a eficiência de sua execução utilizando os recursos de máquina virtual e disco ao máximo.

Por exemplo, se seu aplicativo estiver em execução em um único núcleo máquina virtual usando dois threads, a CPU pode alternar entre dois segmentos alcançar a eficiência. Enquanto um segmento está esperando em um disco IO para concluir a CPU pode alternar para o outro segmento. Dessa forma, dois threads podem ser realizadas mais do que um único thread seria. Se a máquina virtual tiver mais de um núcleo, ele diminui ainda mais tempo de execução porque cada núcleo pode executar tarefas em paralelo.

Você não poderá alterar a maneira como um aplicativo comercial implementa único threads ou vários threads. Por exemplo, o SQL Server é capaz de lidar com várias CPUs e multi-core. No entanto, o SQL Server decide sob quais condições ela utilizará um ou mais threads para processar uma consulta. Ele pode executar consultas e criar índices usando vários threads. Para uma consulta que envolve ingressando em tabelas grandes e classificando dados antes de retornar ao usuário, SQL Server provavelmente usará vários threads. No entanto, um usuário não pode controlar se o SQL Server executa uma consulta usando um único thread ou vários threads.

Existem configurações que você pode alterar para influenciar isso, vários threads ou paralelos de processamento de um aplicativo. Por exemplo, no caso do SQL Server é a configuração de grau de paralelismo máxima. Esta configuração chamada MAXDOP, permite que você configure o número máximo de processadores que do SQL Server pode usar quando paralelas de processamento. Você pode configurar MAXDOP para consultas individuais ou operações de índice. Isso é útil quando você quiser saldo recursos do sistema para um aplicativo crítico de desempenho.

Por exemplo, digamos que seu aplicativo usando o SQL Server está executando uma consulta grande e uma operação de índice ao mesmo tempo. Vamos supor que você queria a operação de índice para ter mais de um bom desempenho em comparação com a consulta grande. Nesse caso, você pode definir o valor MAXDOP da operação de índice seja maior que o valor MAXDOP para a consulta. Dessa maneira, o SQL Server tem mais número de processadores que ele pode aproveitar para a operação de índice em comparação com o número de processadores-pode dedicar à consulta grande. Lembre-se de que você não controlar o número de segmentos SQL Server usará para cada operação. Você pode controlar o número máximo de processadores sendo dedicado para vários threads.

Saiba mais sobre [Graus de paralelismo](https://technet.microsoft.com/library/ms188611.aspx) no SQL Server. Descobrir essas configurações que influenciam vários threads em seu aplicativo e suas configurações para otimizar o desempenho.

## <a name="queue-depth"></a>Profundidade da fila  
A profundidade de fila ou o comprimento da fila ou o tamanho de fila é o número de solicitações de IO pendentes do sistema. O valor de profundidade de fila determina quantas operações de IO seu aplicativo pode alinhar, que os discos de armazenamento serão processamento. Ele afeta todos os indicadores de desempenho de três aplicativo que discutimos em viz. neste artigo, IOPS, produtividade e latência.

Fila profundidade e vários thread são estreitamente relacionados. O valor de profundidade de fila indica quanto vários thread pode ser obtido pelo aplicativo. Se o comprimento da fila for grande, aplicativo pode executar mais operações simultaneamente, em outras palavras, mais vários threads. Se a intensidade de fila for pequena, mesmo que o aplicativo é multithread, ele não terá suficiente solicitações alinhadas para execução simultânea.

Normalmente, loja de aplicativos não permitem que você altere a intensidade de fila, porque se definido incorretamente fará mais mal do que é bom. Aplicativos definirá o valor correto de profundidade de fila para obter o desempenho ideal. No entanto, é importante entender esse conceito para que você pode solucionar problemas de desempenho com seu aplicativo. Você também pode observar os efeitos de profundidade de fila executando ferramentas benchmarking no seu sistema.

Alguns aplicativos fornecem configurações para influenciar a profundidade de fila. Por exemplo, a configuração de (grau máximo de paralelismo) MAXDOP no SQL Server explicado na seção anterior. MAXDOP é uma forma de influenciar fila profundidade e vários threads, embora ele não altera o valor de profundidade de fila do SQL Server diretamente.

*Fila alta profundidade*  
Uma profundidade de fila alta linhas mais operações no disco. O disco sabe a próxima solicitação na fila de antecedência. Consequentemente, o disco pode programar operações antecedência e processá-las em uma sequência ideal. Desde que o aplicativo estiver enviando solicitações mais no disco, o disco pode processar mais IOs paralelos. Por fim, o aplicativo será capaz de alcançar IOPS superior. Desde que o aplicativo está processando mais solicitações, também aumenta a produtividade total do aplicativo.

Normalmente, um aplicativo pode obter transferência máxima com 8-16 + IOs pendentes por disco anexado. Se uma profundidade de fila, aplicativo não é insistem suficiente IOs ao sistema e processará menos quantidade de em um determinado período. Em outras palavras, menos taxa de transferência.

Por exemplo, no SQL Server, configurando o valor MAXDOP para uma consulta para "4" informa do SQL Server que ele pode usar até quatro cores para executar a consulta. SQL Server irá determinar o que é o melhor valor de profundidade de fila e o número de cores para a execução da consulta.

*Fila ideal profundidade*  
Valor de profundidade de fila muito alto também tem suas desvantagens. Se o valor de profundidade de fila é muito alto, o aplicativo tentará unidade IOPS muito alto. A menos que o aplicativo tem persistentes discos com IOPS provisionado suficientes, isso pode afetar negativamente latências de aplicativo. Acompanhar fórmula mostra a relação entre IOPS, de latência e de profundidade de fila.  
    ![](media/storage-premium-storage-performance/image6.png)

Você não deve configurar fila profundidade para qualquer valor alto, mas para um valor ideal, que pode oferecer suficiente IOPS para o aplicativo sem afetar latências. Por exemplo, se a latência de aplicativo precisa ser 1 milissegundos, a profundidade de fila necessária para atingir 5.000 IOPS é, QD = 5000 x 0.001 = 5.

*Fila profundidade para Volume distribuído*  
Para um volume distribuído, manter uma profundidade de fila alta o suficiente, de forma que, cada disco tem uma profundidade de fila pico individualmente. Por exemplo, considere um aplicativo que envia uma profundidade de fila de 2 e há 4 discos na faixa. As duas solicitações de IO irão para dois discos e restante dois discos será ocioso. Portanto, configure o comprimento da fila, para que todos os discos podem estar ocupados. Fórmula abaixo mostra como determinar o comprimento da fila de volumes distribuídos.  
    ![](media/storage-premium-storage-performance/image7.png)

## <a name="throttling"></a>A limitação  
Disposições de armazenamento Premium Azure especificado número de IOPS e produtividade dependendo os tamanhos de máquina virtual e tamanhos de disco que você escolher. Sempre que seu aplicativo tenta unidade IOPS ou produtividade acima esses limites de que a máquina virtual ou o disco pode manipular, armazenamento Premium será aceleração-lo. Isso manifestos na forma de desempenho degradado em seu aplicativo. Isso pode significam mais alta latência, diminuir a produtividade ou reduzir IOPS. Se Premium armazenamento não aceleração, seu aplicativo pode falhar completamente ultrapassando o que são capazes de atingir seus recursos. Portanto, para evitar problemas de desempenho devido a otimização, sempre Provisione recursos suficientes para seu aplicativo. Leve em consideração o que discutimos nas seções de tamanhos de disco acima e tamanhos de máquina virtual. Avaliações de desempenho é a melhor maneira de descobrir quais recursos que você precisará hospedar seu aplicativo.

## <a name="benchmarking"></a>Avaliações de desempenho  
Avaliações de desempenho é o processo de simular cargas de trabalho diferentes em seu aplicativo e medir o desempenho do aplicativo para cada carga de trabalho. Usando as etapas descritas em uma seção anterior, você reuniu os requisitos de desempenho do aplicativo. Executando ferramentas benchmarking nas VMs hospeda o aplicativo, você pode determinar os níveis de desempenho que seu aplicativo pode conseguir com Storage Premium. Nesta seção, nós fornecem exemplos de uma VM DS14 padrão provisionados com discos de armazenamento do Azure Premium de avaliações de desempenho.

Usamos ferramentas comuns benchmarking Iometer e FIO, para Windows e Linux respectivamente. Essas ferramentas geram vários threads simular uma produção como carga de trabalho e medem o desempenho do sistema. Usando as ferramentas que você também pode configurar parâmetros como bloquear tamanho e fila profundidade, que normalmente não pode ser alterada para um aplicativo. Isso lhe dá mais flexibilidade para direcionar o desempenho máximo em uma escala alta máquina virtual provisionado com discos premium para tipos diferentes de cargas de trabalho do aplicativo. Para saber que mais sobre cada ferramenta benchmarking visite [Iometer](http://www.iometer.org/) e [FIO](http://freecode.com/projects/fio).

Para acompanhar os exemplos abaixo, crie uma VM DS14 padrão e anexe 11 discos de armazenamento de Premium para a máquina virtual. De 11 discos, configure 10 discos com host cache como "Nenhum" e de distribuição-los em um volume chamado NoCacheWrites. Configurar host cache como "Somente leitura" no disco restante e criar um volume chamado CacheReads com este disco. Usando essa configuração, você poderá ver o máximo desempenho de leitura e gravação de uma VM DS14 padrão. Para obter etapas detalhadas sobre como criar uma máquina virtual DS14 com discos premium, vá para [criar e usar conta de armazenamento Premium para um disco de dados de máquina virtual](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

*Aquecimento o Cache*  
O disco com o host de somente leitura cache poderá dar IOPS maior que o limite de disco. Para obter este máximo desempenho de leitura do cache de host, primeiro você deve aquecimento do cache de disco. Isso garante que o IOs de leitura qual ferramenta benchmarking levarão em volume CacheReads realmente atinge o cache e não o disco diretamente. O resultado de visitas cache em IOPS adicionais do cache de único habilitado disco.

>**Importante:**  
>Você deve aquecimento o cache antes de executar avaliações de desempenho, sempre que máquina virtual seja reiniciado.

#### <a name="iometer"></a>Iometer   
[Baixar a ferramenta Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) na VM.

*Arquivo de teste*  
Iometer usa um arquivo de teste que está armazenado no volume no qual você executará o teste benchmark. Ele unidades leituras e gravações nesse arquivo de teste para medir o disco IOPS e produtividade. Iometer cria esse arquivo de teste se você não tiver fornecido um. Crie um arquivo de teste de 200GB chamado iobw.tst nos volumes CacheReads e NoCacheWrites.

*Especificações do Access*  
As especificações, solicitar tamanho de IO, % de leitura/gravação, % aleatório/sequenciais são configurados usando a guia "Especificações do Access" na Iometer. Crie uma especificação de acesso para cada um dos cenários descritos a seguir. Crie as especificações do access e "Salvar" com uma apropriado, nomeie como – RandomWrites\_8K, RandomReads\_8K. Selecione a especificação correspondente ao executar o cenário de teste.

Um exemplo de especificações do access para o cenário de escrever IOPS máximo é mostrado abaixo,  
    ![](media/storage-premium-storage-performance/image8.png)

*Especificações de teste do máximo de IOPS*  
Para demonstrar IOPs máximos, use tamanho menor de solicitação. Use o tamanho da solicitação de 8K e crie especificações para leituras e gravações aleatórias.

| Especificação de acesso | Tamanho da solicitação | % Aleatório | % De leitura |
|----------------------|--------------|----------|--------|
| RandomWrites\_8K     | 8K           | 100      | 0      |
| RandomReads\_8K      | 8K           | 100      | 100    |

*Especificações de teste de produção máximo*  
Para demonstrar transferência máxima, use tamanho maior de solicitação. Use o tamanho da solicitação de 64K e crie especificações para leituras e gravações aleatórias.

| Especificação de acesso | Tamanho da solicitação | % Aleatório | % De leitura |
|----------------------|--------------|----------|--------|
| RandomWrites\_64K    | 64K          | 100      | 0      |
| RandomReads\_64K     | 64K          | 100      | 100    |

*Executar o teste de Iometer*  
Execute as etapas abaixo para aquecimento do cache

1.  Criar duas especificações do access com valores mostrados abaixo,

  	| Nome              | Tamanho da solicitação | % Aleatório | % De leitura |
  	|-------------------|--------------|----------|--------|
  	| RandomWrites\_1MB | 1MB          | 100      | 0      |
  	| RandomReads\_1MB  | 1MB          | 100      | 100    |

2.  Execute o teste de Iometer para inicializar o disco de cache com parâmetros a seguir. Use três segmentos de trabalho para o volume de destino e uma profundidade de fila de 128. Defina a duração "Tempo de execução" do teste para 2hrs na guia "Configuração de teste".

  	| Cenário              | Volume de destino | Nome              | Duração |
  	|-----------------------|---------------|-------------------|----------|
  	| Inicializar disco de Cache | CacheReads    | RandomWrites\_1MB | 2hrs     |

3.  Execute o teste de Iometer para aquecimento disco de cache com parâmetros a seguir. Use três segmentos de trabalho para o volume de destino e uma profundidade de fila de 128. Defina a duração "Tempo de execução" do teste para 2hrs na guia "Configuração de teste".

  	| Cenário           | Volume de destino | Nome             | Duração |
  	|--------------------|---------------|------------------|----------|
  	| Aquecimento disco em Cache | CacheReads    | RandomReads\_1MB | 2hrs     |

Após o disco de cache é aquecido, continue com os cenários de teste listados abaixo. Para executar o teste de Iometer, use pelo menos três segmentos de trabalho para **cada** volume de destino. Para cada thread de trabalho, selecione o volume de destino, definir fila profundidade e selecione uma das especificações de teste salvo, conforme mostrado na tabela a seguir, execute o cenário de teste correspondente. A tabela também mostra os resultados esperados para IOPS e produtividade quando executando esses testes. Para todos os cenários, um tamanho pequeno de IO de 8KB e uma profundidade de fila alta de 128 são usados.

| Cenário de teste      | Volume de destino | Nome              | Resultado       |
|--------------------|---------------|-------------------|--------------|
| Máx. IOPS de leitura     | CacheReads    | RandomWrites\_8K  | 50.000 IOPS  |
| Máx. IOPS de gravação    | NoCacheWrites | RandomReads\_8K   | 64.000 IOPS  |
| Máx. IOPS combinado | CacheReads    | RandomWrites\_8K  | 100.000 IOPS |
|                    | NoCacheWrites | RandomReads\_8K   |              |
| Máx. Leia MB/seg   | CacheReads    | RandomWrites\_64K | 524 MB/seg   |
| Máx. Gravação MB/seg  | NoCacheWrites | RandomReads\_64K  | 524 MB/seg   |
| Combinado MB/seg    | CacheReads    | RandomWrites\_64K | 1.000 MB/seg  |
|                    | NoCacheWrites | RandomReads\_64K  |              |

Abaixo estão capturas de tela do Iometer resultados de teste para cenários combinados de IOPS e produtividade.

*Máximo de IOPS combinadas leituras e gravações*  
![](media/storage-premium-storage-performance/image9.png)

*Taxa de transferência máxima combinadas leituras e gravações*  
![](media/storage-premium-storage-performance/image10.png)

### <a name="fio"></a>FIO  
FIO é uma ferramenta popular para o armazenamento de referência nas VMs Linux. Ela tem a flexibilidade para selecionar tamanhos de IO diferentes, sequenciais ou leituras aleatórias e grava. Ele gera threads de trabalho ou processos para executar as operações de e/s especificadas. Você pode especificar o tipo de operações de e/s que cada thread de trabalho deve executar usando arquivos de trabalho. Criamos um arquivo de trabalho por cenário ilustrado nos exemplos a seguir. Você pode alterar as especificações nesses arquivos de trabalho para avaliar o desempenho de diferentes cargas de trabalho em execução no armazenamento Premium. Nos exemplos, estamos usando uma VM padrão de 14 DS executando **Ubuntu**. Use a mesma configuração descrita no início da [seção de Benchmarking](#Benchmarking) e quente o cache antes de executar testes benchmarking.

Antes de começar, [Baixe o FIO](https://github.com/axboe/fio) e instalá-lo em sua máquina virtual.

Execute o seguinte comando para Ubuntu,

        apt-get install fio

Usaremos quatro segmentos de trabalho para conduzir operações de gravação e quatro segmentos de trabalho para operações de leitura de carro nos discos. Os operadores de gravação serão levando tráfego no volume "nocache", que tem 10 discos com cache definido como "Nenhum". Os operadores de leitura serão levando tráfego no volume "readcache", que possui 1 disco com cache definida como "Somente leitura".

*Máximo IOPS de gravação*  
Crie o arquivo de trabalho com especificações a seguir para obter o máximo de IOPS de gravação. O nome "fiowrite.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Observe as mudanças de chave de acompanhamento que são alinhado com as diretrizes de design descritas nas seções anteriores. Estas especificações são essenciais para orientar o máximo de IOPS,  
-   Uma profundidade de fila alta de 256.  
-   Um tamanho pequeno bloco de 8KB.  
-   Vários threads realizando gravações aleatórias.

Execute o seguinte comando para iniciar o teste FIO para 30 segundos,  

    sudo fio --runtime 30 fiowrite.ini

Enquanto o teste executa, você poderá ver o número de gravação IOPS a máquina virtual e discos Premium estiver oferecendo. Como mostrado no exemplo abaixo, a máquina virtual DS14 está oferecendo sua gravação máximo limite de IOPS de 50.000 IOPS.  
    ![](media/storage-premium-storage-performance/image11.png)

*Leitura máxima IOPS*  
Crie o arquivo de trabalho com especificações a seguir para obter o máximo de IOPS de leitura. O nome "fioread.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Observe as mudanças de chave de acompanhamento que são alinhado com as diretrizes de design descritas nas seções anteriores. Estas especificações são essenciais para orientar o máximo de IOPS,

-   Uma profundidade de fila alta de 256.  
-   Um tamanho pequeno bloco de 8KB.  
-   Vários threads realizando gravações aleatórias.

Execute o seguinte comando para iniciar o teste FIO para 30 segundos,

    sudo fio --runtime 30 fioread.ini

Enquanto o teste é executado, você poderá ver o número de leitura IOPS a máquina virtual e discos Premium estiver oferecendo. Como mostrado no exemplo abaixo, a máquina virtual DS14 está oferecendo mais de 64.000 IOPS de leitura. Isso é uma combinação do disco e o desempenho de cache.  
    ![](media/storage-premium-storage-performance/image12.png)

*Máximo de leitura e gravação IOPS*  
Criar o arquivo de trabalho com seguinte especificações para obter o máximo combinado de leitura e gravação IOPS. O nome "fioreadwrite.ini".

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Observe as mudanças de chave de acompanhamento que são alinhado com as diretrizes de design descritas nas seções anteriores. Estas especificações são essenciais para orientar o máximo de IOPS,

-   Uma profundidade de fila alta de 128.  
-   Um tamanho pequeno de bloco de 4KB.  
-   Vários threads realizando aleatório leituras e gravações.

Execute o seguinte comando para iniciar o teste FIO para 30 segundos,

    sudo fio --runtime 30 fioreadwrite.ini

Enquanto o teste é executado, você poderá ver o número de combinado ler e gravar IOPS a máquina virtual e discos Premium estiver oferecendo. Como mostrado no exemplo abaixo, a máquina virtual DS14 está oferecendo mais de 100.000 leitura combinado e IOPS de gravação. Isso é uma combinação do disco e o desempenho de cache.  
    ![](media/storage-premium-storage-performance/image13.png)

*Máximo combinado produtividade*  
Para obter o máximo combinado de leitura e gravação produtividade, use um tamanho maior de bloco e profundidade de fila grande com vários threads realizando leituras e gravações. Você pode usar um tamanho de bloco de 64KB e profundidade de fila de 128.

## <a name="next-steps"></a>Próximas etapas  

Saiba mais sobre o armazenamento do Azure Premium:

- [Armazenamento de Premium: Armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](storage-premium-storage.md)  

Para usuários do SQL Server, leia artigos sobre as práticas recomendadas de desempenho para o SQL Server:

- [Práticas recomendadas de desempenho para SQL Server no Azure máquinas virtuais](../virtual-machines/virtual-machines-windows-sql-performance.md)
- [Armazenamento do Azure Premium fornece mais alto desempenho para o SQL Server na máquina virtual do Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx) 
