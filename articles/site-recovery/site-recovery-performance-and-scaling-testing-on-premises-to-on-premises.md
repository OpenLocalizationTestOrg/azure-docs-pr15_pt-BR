<properties
    pageTitle="Teste de desempenho e escala de resultados para o local para replicação de Hyper-V de local com recuperação de Site | Microsoft Azure"
    description="Este artigo fornece informações sobre o desempenho testes de locais para replicação local usando a recuperação de Site do Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/06/2016"
    ms.author="raynew"/>

# <a name="performance-test-and-scale-results-for-on-premises-to-on-premises-hyper-v-replication-with-site-recovery"></a>Desempenho testar e dimensionar resultados para locais para replicação de Hyper-V de local com recuperação de Site

Você pode usar a recuperação de Site do Microsoft Azure para organizar e gerenciar replicação de máquinas virtuais e servidores físicos Azure ou um data center secundário. Este artigo fornece os resultados de teste de desempenho que fizemos ao replicar máquinas virtuais Hyper-V entre dois locais dos data centers.



## <a name="overview"></a>Visão geral

O objetivo do teste era examinar como executa a recuperação de Site do Azure durante a replicação de estado estável. Replicação de estado estável ocorre quando máquinas virtuais concluiu replicação inicial e está sincronizando alterações delta. É importante medir o desempenho usando estável porque ele é o estado em que a maioria das máquinas virtuais permanecem a menos que ocorrem inesperadas interrupções.


A implantação de teste consistiu em dois sites de local com um servidor VMM em cada site. Essa implantação de teste é típica de uma implantação do office de cabeça/filial, com sede atuando como o local principal e filial de como o site secundário ou recuperação.

### <a name="what-we-did"></a>O que fizemos

Veja aqui o que estamos no teste passar:

1. Criado máquinas virtuais usando modelos VMM.

1. Introdução a máquinas virtuais e métricas de desempenho de linha de base de captura acima de 12 horas.

1. Nuvens criados em servidores VMM primário e recuperação.

1. Proteção de nuvem configurado na recuperação de Site do Azure, incluindo o mapeamento de nuvens de origem e de recuperação.

1. Habilitado proteção para máquinas virtuais e permitir que ele concluir a replicação inicial.

1. Esperava algumas das horas de estabilização de sistema.

1. Capturado métricas de desempenho mais de 12 horas, garantindo que todas as máquinas virtuais permaneceu em um estado de replicação esperada por esses 12 horas.

1. Meça o delta entre as métricas de desempenho de linha de base e as métricas de desempenho de replicação.

## <a name="test-deployment-results"></a>Resultados de implantação de teste

### <a name="primary-server-performance"></a>Desempenho do servidor primário

- Hyper-V Replica assíncrona rastreia alterações para um arquivo de log com sobrecarga de armazenamento mínima no servidor primário.

- Hyper-V Replica utiliza o cache de memória auto mantida para minimizar IOPS sobrecarga para acompanhamento. Ele armazena grava o VHDX na memória e libera-los no arquivo de log antes da hora que o log é enviado para o site de recuperação. Um disco alinhado também acontece se as gravações atingir um limite predeterminado.

- O gráfico abaixo mostra a sobrecarga IOPS estável para replicação. Podemos ver que o IOPS sobrecarga devido a replicação é de cerca de 5%, que é muito baixo.

![Principais resultados](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744913.png)

Hyper-V Replica utiliza memória no servidor primário para otimizar o desempenho do disco. Como mostra o gráfico a seguir, memória sobrecarga em todos os servidores no cluster principal é pequena. A memória sobrecarga mostrada é a porcentagem de memória usada pela replicação em comparação com o total de memória instalado no servidor Hyper-V.

![Principais resultados](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744914.png)

Hyper-V Replica tem sobrecarga de CPU mínima. Como mostrado no gráfico, sobrecarga de replicação está no intervalo de 2 a 3%.

![Principais resultados](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744915.png)

### <a name="secondary-recovery-server-performance"></a>Desempenho do servidor secundário (recuperação)

Hyper-V Replica usa uma pequena quantidade de memória no servidor de recuperação para otimizar o número de operações de armazenamento. O gráfico resume o uso de memória no servidor de recuperação. A memória sobrecarga mostrada é a porcentagem de memória usada pela replicação em comparação com o total de memória instalado no servidor Hyper-V.

![Resultados secundários](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744916.png)

A quantidade de operações de e/s no site de recuperação é uma função do número de operações de gravação no local principal. Vamos examinar as operações de i/o totais no site de recuperação em comparação com as operações de i/o totais operações e gravação no local principal. Os gráficos mostram que o total IOPS no site de recuperação é

- Ao redor de 1,5 vezes a IOPS de gravação na imagem principal.

- Cerca de 37% do IOPS total no local principal.

![Resultados secundários](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744917.png)

![Resultados secundários](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744918.png)

### <a name="effect-of-replication-on-network-utilization"></a>Efeito de replicação na utilização da rede

Uma média de 275 MB por segundo de largura de banda de rede foi usada entre os nós primário e recuperação (com compactação ativada) em relação a uma largura de banda existente de 5 GB por segundo.

![Utilização de rede de resultados](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744919.png)

### <a name="effect-of-replication-on-virtual-machine-performance"></a>Efeito de replicação no desempenho de máquina virtual

Uma consideração importante é o impacto de replicação em cargas de trabalho de produção executando nas máquinas virtuais. Se o site principal está configurado adequadamente para replicação, não deve haver qualquer impacto sobre as cargas de trabalho. Leve do Hyper-V Replica mecanismo de rastreamento garante que cargas de trabalho em execução nas máquinas virtuais não são afetadas durante a replicação de estado estável. Isso é ilustrado nos seguintes gráficos.

Este gráfico mostra IOPS realizado por máquinas virtuais executando diferentes cargas de trabalho antes e depois de replicação foi habilitada. Você pode observar que não há nenhuma diferença entre os dois.

![Resultados do efeito de réplica](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744920.png)

O gráfico a seguir mostra a taxa de transferência de máquinas virtuais executando diferentes cargas de trabalho antes e depois de replicação foi habilitada. Você pode observar que a replicação não tem impacto significativo.

![Efeitos de réplica de resultados](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744921.png)

### <a name="conclusion"></a>Conclusão

Os resultados mostram claramente escalas de recuperação de Site do Azure, juntamente com Hyper-V Replica, bem com mínimo sobrecarga para um cluster grande.  Recuperação de Site Azure fornece implantação simples, replicação, gerenciamento e monitoramento. Hyper-V Replica fornece a infraestrutura necessária para dimensionamento de replicação com êxito. Para planejar uma implantação ideal sugerimos que você baixe o [Planejador de capacidade de réplica do Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057).

## <a name="test-environment-details"></a>Detalhes do ambiente de teste

### <a name="primary-site"></a>Local principal

- O site principal possui um cluster que contém cinco servidores Hyper-V 470 máquinas virtuais em execução.

- As máquinas virtuais executar cargas de trabalho diferentes e todos têm proteção de recuperação de Site do Azure habilitada.

- Armazenamento para o nó de cluster é fornecido por uma SAN iSCSI. Modelo – Hitachi HUS130.

- Cada servidor cluster tem quatro placas de rede (NICs) de um Gbps.

- Dois dos cartões de rede estão conectados a uma rede privada iSCSI e dois estiver conectado à rede de uma empresa externa. Uma das redes externas é reservada para apenas comunicações cluster.

![Requisitos de hardware principal](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744922.png)

|Servidor|RAM|Modelo|Processador|Número de processadores|NIC|Software|
|---|---|---|---|---|---|---|
|Servidores de Hyper-V em cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25|128ESTLAB HOST25 tem 256|Dell™ PowerEdge™ R820|Intel Xeon CPU E5-4620 0 @ 2,20 GHz|4|Posso Gbps x 4|Windows Server Data Center 2012 R2 (x64) + função Hyper-V|
|Servidor VMM|2|||2|1 Gbps|Windows Server banco de dados 2012 R2 (x64) + VMM 2012 R2|

### <a name="secondary-recovery-site"></a>Site de secundário (recuperação)

- O site secundário tem um cluster de seis nós failover.

- Armazenamento para o nó de cluster é fornecido por uma SAN iSCSI. Modelo – Hitachi HUS130.

![Especificação de hardware principal](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744923.png)

|Servidor|RAM|Modelo|Processador|Número de processadores|NIC|Software|
|---|---|---|---|---|---|---|
|Servidores de Hyper-V em cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10|96|Dell™ PowerEdge™ R720|Intel Xeon CPU E5-2630 0 @ 2.30GHz|2|Posso Gbps x 4|Windows Server Data Center 2012 R2 (x64) + função Hyper-V|
|ESTLAB-HOST17|128|Dell™ PowerEdge™ R820|Intel Xeon CPU E5-4620 0 @ 2,20 GHz|4||Windows Server Data Center 2012 R2 (x64) + função Hyper-V|
|ESTLAB-HOST24|256|Dell™ PowerEdge™ R820|Intel Xeon CPU E5-4620 0 @ 2,20 GHz|2||Windows Server Data Center 2012 R2 (x64) + função Hyper-V|
|Servidor VMM|2|||2|1 Gbps|Windows Server banco de dados 2012 R2 (x64) + VMM 2012 R2|

### <a name="server-workloads"></a>Cargas de trabalho de servidor

- Para fins de teste escolhemos cargas de trabalho comumente usadas em cenários de atendimento ao cliente da empresa.

- Usamos [IOMeter](http://www.iometer.org) com as características de carga de trabalho resumidas na tabela a simulação.

- Todos os perfis de IOMeter são definidos para gravar bytes aleatórios para simular pior gravar padrões para cargas de trabalho.

|Carga de trabalho|Tamanho (KB)|% De acesso|% De leitura|E/SS pendentes|Padrão de e/s|
|---|---|---|---|---|---|
|Servidor de arquivos|48163264|60% 20 %5 %5% 10%|80% 80% 80% 80% 80%|88888|Todos os 100% aleatórios|
|SQL Server (volume 1) SQL Server (volume 2)|864|100% 100%|70% de %0|88|% de 100% random100 sequencial|
|Exchange|32|100%|67%|8|100% aleatórios|
|Estação de trabalho/VDI|464|66% 34%|70% 95%|11|Os dois aleatório de 100%|
|Servidor de arquivos de Web|4864|33% 34% 33%|% de 95% de 95 95%|888|Todos os 75% aleatórios|

### <a name="virtual-machine-configuration"></a>Configuração de máquina virtual

- máquinas virtuais 470 no cluster principal.

- Todas as máquinas virtuais com disco VHDX.

- Máquinas virtuais executando cargas de trabalho resumidas na tabela. Todos foram criados com os modelos VMM.

|Carga de trabalho|# VMs|RAM mínima (GB)|Máximo (GB) de RAM|Tamanho de disco lógico (GB) por máquina virtual|Máximo de IOPS|
|---|---|---|---|---|---|
|SQL Server|51|1|4|167|10|
|Servidor Exchange|71|1|4|552|10|
|Servidor de arquivos|50|1|2|552|22|
|VDI|149|.5|1|80|6|
|Servidor Web|149|.5|1|80|6|
|TOTAL|470|||96.83 TB|4108|

### <a name="azure-site-recovery-settings"></a>Configurações de recuperação de Site Azure

- Azure recuperação de Site foi configurada para o local para a proteção de local

- O servidor VMM tem quatro nuvens configurados, que contém os servidores de cluster Hyper-V e suas máquinas virtuais.

|Nuvem VMM principal|Protegido máquinas virtuais na nuvem|Frequência de replicação|Pontos de recuperação adicionais|
|---|---|---|---|
|PrimaryCloudRpo15m|142|15 min|Nenhum|
|PrimaryCloudRpo30s|47|30 segundos|Nenhum|
|PrimaryCloudRpo30sArp1|47|30 segundos|1|
|PrimaryCloudRpo5m|235|5 min|Nenhum|

### <a name="performance-metrics"></a>Métricas de desempenho

A tabela resume as avaliações de desempenho e contadores que foram medidos na implantação.

|Métrica|Contador|
|---|---|
|CPU|\Processor(_Total)\% tempo de processador|
|Memória disponível|\Memory\Available MBytes|
|IOPS|\PhysicalDisk ( total) \Disk transferências/seg|
|Máquina virtual operações de leitura (IOPS) / s|Dispositivo de armazenamento virtual \Hyper-V (<VHD>) \Read operações/s|
|Operações de gravação (IOPS) de máquina virtual/s|Dispositivo de armazenamento virtual \Hyper-V (<VHD>) \Write operações/S|
|Taxa de transferência de leitura de máquina virtual|Dispositivo de armazenamento virtual \Hyper-V (<VHD>) \Read Bytes/seg|
|Taxa de transferência de gravação de máquina virtual|Dispositivo de armazenamento virtual \Hyper-V (<VHD>) \Write Bytes/seg|


## <a name="next-steps"></a>Próximas etapas

- [Configurar a proteção entre dois locais VMM locais](site-recovery-vmm-to-vmm.md)
