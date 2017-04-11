<properties
    pageTitle="Planejar capacidade para proteger máquinas virtuais e servidores físicos em recuperação de Site do Azure | Microsoft Azure"
    description="Recuperação de Site Azure coordenadas a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos localizados no local no Azure ou para um site local secundário." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="07/12/2016" 
    ms.author="raynew"/>

# <a name="plan-capacity-for-protecting-virtual-machines-and-physical-servers-in-azure-site-recovery"></a>Capacidade de plano de proteção de máquinas virtuais e servidores físicos em recuperação de Site do Azure

A ferramenta de Planejador de capacidade de recuperação de Site Azure ajuda você a descobrir seus requisitos de capacidade para proteger VMs Hyper-V, VMs VMware e Windows/Linux servidores físicos com recuperação de Site do Azure.


## <a name="overview"></a>Visão geral

Use o Planejador de capacidade de recuperação de Site para analisar o ambiente de origem e cargas de trabalho e calcular as necessidades de largura de banda, recursos de servidor, que você precisará no seu local de origem e os recursos (máquinas virtuais e armazenamento etc), que você precisará no seu local de destino. 

Você pode executar a ferramenta de algumas modos:

- **Planejamento de rápida**: executar a ferramenta nesse modo para obter projeções de rede e servidor com base em um número médio de VMs, discos, armazenamento e taxa de alteração.
- **Planejamento detalhado**: execute a ferramenta nesse modo e forneça detalhes de cada carga de trabalho no nível de máquina virtual. Analisar compatibilidade de máquina virtual e obter projeções de rede e servidor.

## <a name="before-you-start"></a>Antes de começar

Antes de executar a ferramenta:

1. Reunir informações sobre seu ambiente, incluindo VMs, discos por máquina virtual, armazenamento por disco.
2. Identifique seu diárias taxa de alteração (rotatividade) de dados replicados. Para fazer isso:

    - Se você estiver replicar VMs Hyper-V, baixe a [ferramenta de planejamento de capacidade de Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para obter a taxa de alteração. [Saiba mais](site-recovery-capacity-planning-for-hyper-v-replication.md) sobre essa ferramenta. Recomendamos que você executar esta ferramenta mais de uma semana para capturar médias.
    - Se você estiver replicar máquinas virtuais VMware, use a [capacidade de vSphere aparelho de planejamento](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) para calcular a taxa de rotatividade.
    - Se você estiver replicação de servidores físicos, você precisará estimar manualmente.

## <a name="run-the-quick-planner"></a>Executar o planejador rápido
1.  Baixe e abra a ferramenta de [Planejador de capacidade de recuperação de Site do Azure](http://aka.ms/asr-capacity-planner-excel) . Você precisará executar macros escolha Habilitar a edição e habilitar o conteúdo quando solicitado. 
2.  Em **Selecione um tipo de Planejador** selecione **Planejador rápida** da caixa de listagem.

    ![Guia de Introdução](./media/site-recovery-capacity-planner/getting-started.png)

3.  Na planilha de **Planejador de capacidade** insira as informações necessárias. Você deve preencher todos os campos dentro de um círculo em vermelho na captura de tela abaixo.

    - Em **Selecionar seu cenário** escolha **Hyper-V no Azure** ou **VMware/física para Azure**.
    - Em **média de dados diária alterar taxa (%)** colocar as informações que você reunir usando a [ferramenta de planejamento de capacidade de Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) ou a [capacidade de vSphere planejamento aparelho](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).  
    - **Compactação** só se aplica a compactação oferecida quando replicação VMs VMware ou servidores físicos no Azure. Podemos estimar 30% ou mais, mas você pode modificar a configuração conforme necessário. Para duplicar VMs Hyper-V compactação Azure você pode usar um dispositivo de terceiros como Riverbed. 
    -  Em **Entradas de retenção** , especifique por quanto tempo réplicas devem ser mantidas. Se você estiver replicar VMware ou o valor de entrada de servidores físicos em dias. Se você estiver replicar Hyper-V especificar o tempo em horas.
    -  **Número de horas na qual replicação inicial para o lote de máquinas virtuais deve ser concluída** e o **número de máquinas virtuais por lote de replicação inicial** MyDomain configurações que são usadas para calcular os requisitos de replicação inicial.  Quando a recuperação de Site é implantada conjunto inteiro de dados inicial deve ser carregado. 

    ![Entradas](./media/site-recovery-capacity-planner/inputs.png)

2.  Após você colocar os valores para o ambiente de origem, saída exibida inclui:

    - **Largura de banda necessária para replicação delta** (MB/s). Largura de banda de rede para replicação delta é calculada sobre a taxa de alteração de dados diária média.
    - **Largura de banda necessária para replicação inicial** (MB/s). Largura de banda para replicação inicial é calculada sobre os valores de replicação inicial que você coloca em. 
    - **Armazenamento necessária (em GB)** é o armazenamento do Azure total obrigatório.
    - **IOPS total contas de armazenamento padrão** é calculado com base no tamanho da unidade 8 K IOPS contas de total de armazenamento padrão.  Para o planejador rápido que o número é calculado com base em todas as VMs de origem discos e dados diárias alterar taxa. Para o planejador detalhadas que o número é calculado com base no número total de VMs que são mapeados para Azure VMs padrão e dados altere taxa nessas VMs. 
    - **Número de contas de armazenamento padrão** fornece o número total de contas de armazenamento padrão necessários para proteger as VMs. Observe que uma conta de armazenamento padrão pode conter até 20000 IOPS em todas as VMs em um armazenamento padrão e máximo de 500 IOPS suportados por disco. 
    - **Número de discos blob necessários** retorna o número de discos que serão criados em armazenamento do Azure.
    - **Número de contas de armazenamento premium necessários** fornece o número total de contas de armazenamento premium necessários para proteger as VMs. Observe que uma fonte de máquina virtual com alta IOPS (maior que 20000) precisa de uma conta de armazenamento premium. Uma conta de armazenamento premium pode conter até 80000 IOPS.
    - **IOPS total no armazenamento premium** é calculado com base no tamanho da unidade 256 K IOPS contas de armazenamento total premium.  Para o planejador rápido que o número é calculado com base em todas as VMs de origem discos e dados diárias alterar taxa. Para o planejador detalhadas que o número é calculado com base no número total de VMs que são mapeados para premium Azure máquina virtual (série DS e GS) e os dados altere taxa nessas VMs. 
    - **Número de servidores de configuração necessários** mostra quantos servidores de configuração são necessários para a implantação (1)
    - **Número de servidores processo adicionais necessários** mostra se servidores processo adicionais são necessários além do servidor de processo que está configurado no servidor de configuração por padrão.
    - **armazenamento adicional de 100% na origem** mostra se armazenamento adicional é necessária no local de origem.
            
    ![Saída](./media/site-recovery-capacity-planner/output.png)
 
## <a name="run-the-detailed-planner"></a>Executar o planejador detalhado


1.  Baixe e abra a ferramenta de [Planejador de capacidade de recuperação de Site do Azure](http://aka.ms/asr-capacity-planner-excel) . Você precisará executar macros escolha Habilitar a edição e habilitar o conteúdo quando solicitado. 
2.  Em **Selecione um tipo de Planejador** selecione **Planejador detalhadas** da caixa de listagem.

    ![Guia de Introdução](./media/site-recovery-capacity-planner/getting-started-2.png)

3.  Na planilha de **Qualificação de carga de trabalho** , insira as informações necessárias. Você deve preencher todos os campos marcados.

    - Em **cores de processador** Especifica o número total de cores em um servidor de origem.
    - Na **alocação de memória em MB** , especifique o tamanho de RAM de um servidor de origem. 
    - O **Número de NICs** especificar o número de adaptadores de rede em um servidor de origem. 
    -  No **Total de armazenamento (em GB)** , especifique o tamanho total de armazenamento de máquina virtual. Por exemplo, se o servidor de origem tiver 3 discos com 500 GB, então o tamanho total de armazenamento está 1500 GB.
    -  Em **número de discos anexados** Especifica o número total de unidades de um servidor de origem.
    -  Em **utilização da capacidade de disco** , especifique a utilização média.
    -  Em **Alterar diariamente taxa (%)** , especifique os dados diários alteram taxa de um servidor de origem.
    -  O tamanho de **Mapeamento Azure** seja digite tamanho de máquina virtual do Azure que você deseja mapear. Se você não quiser fazer isso manualmente, clique em**Calcular IaaS VMs**. Observe que, se você a configuração manual de entrada e clique em calcular IaaS VMs sua configuração manual pode ser sobrescrita porque o processo de computação identifica automaticamente a melhor correspondência no tamanho da máquina virtual do Azure.

    ![Qualificação de carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification.png)

4.  Se você clicar em **Calcular IaaS VMs** aqui é o que ela faz:

    - Valida as entradas obrigatórias.
    - Calcula o IOPS e sugere o melhor VM Azure aize correspondência para cada VMs que está qualificada para replicação no Azure. Se um tamanho adequado da máquina virtual do Azure não pode ser detectado que um erro é emitido. Por exemplo se o número de discos 65 anexado em um erro for problemas desde o tamanho maior máquina virtual do Azure é 64.
    - Sugere uma conta de armazenamento que pode ser usada para uma máquina virtual do Azure.
    - Calcula o número total de armazenamento padrão contas e premium armazenamento necessários para a carga de trabalho. Role para baixo à direita para exibir o tipo de armazenamento do Azure e a conta de armazenamento que pode ser usada para um servidor de origem
    - Conclui e classifica o resto da tabela com base no tipo de armazenamento necessária (padrão ou premium) atribuído para uma máquina virtual e o número de discos anexados. Para todas as VMs que atendem aos requisitos para backup até Azure, coluna A (é máquina virtual qualificado?) mostra Sim. Se uma máquina virtual não pode ser feita até Azure um erro será mostrado.

Colunas AA AE são saída e fornecem informações para cada máquina virtual.

![Qualificação de carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification-2.png)


### <a name="example"></a>Exemplo
Como exemplo, para seis VMs com os valores mostrados na tabela, a ferramenta calcula e atribui a melhor correspondência de máquina virtual do Azure e os requisitos de armazenamento do Azure.

![Qualificação de carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification-3.png)

- Na saída para que o exemplo Observe o seguinte:
    
    - A primeira coluna é uma coluna de validação para VMs, discos e rotatividade.
    - Duas contas de armazenamento padrão e conta de armazenamento de um premium são necessários para cinco VMs. 
    -  VM3 não se qualifica para proteção porque um ou mais discos são mais de 1 TB.
    -  VM1 e VM2 podem usar a primeira conta de armazenamento padrão
    -  VM4 pode usar a segunda conta de armazenamento padrão.
    -  VM5 e VM6 precisam de uma conta de armazenamento premium e podem usar uma única conta.

    >[AZURE.NOTE]  IOPS armazenamento padrão e premium são calculados no nível de máquina virtual e não no nível de disco. Uma máquina virtual padrão pode lidar com até 500 IOPS por disco. Se IOPS de um disco são maiores que 500 você precisará armazenamento premium. No entanto se IOPS de um disco são mais de 500, mas IOPS para os discos de máquina virtual totais estão dentro dos limites de máquina virtual do Azure suporte padrão (tamanho de máquina virtual, número de discos, número de memória adaptadores, CPU,), em seguida, o planejador escolhe um padrão série de máquina virtual e não o DS ou GS. Você precisará atualizar manualmente a célula de tamanho Azure mapeamento com apropriado DS ou GS séries de máquina virtual.

5. Depois de todos os detalhes estão no lugar, clique em **Enviar dados para a ferramenta Planejador** para abrir o **Planejador de capacidade** cargas de trabalho são realçadas para mostrar se eles são qualificados para proteção ou não.


### <a name="submit-data-in-the-capacity-planner"></a>Enviar dados no planejador de capacidade

1.  Quando você abrir a planilha de **Planejador de capacidade** é preenchida com base nas configurações que você especificou. A palavra 'Carga' aparece na célula **Infra entradas fonte** para mostrar que a planilha de **Qualificação de carga de trabalho** de entrada. 
2.  Se você quiser fazer alterações, que você precisará modificar a planilha de **Qualificação de carga de trabalho** e clique em enviar dados para a ferramenta Planejador novamente.  

    ![Planejador de capacidade](./media/site-recovery-capacity-planner/capacity-planner.png)


