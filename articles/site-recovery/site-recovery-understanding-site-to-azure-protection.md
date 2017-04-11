<properties
    pageTitle="Replicação de Hyper-V com recuperação de Site do Azure | Microsoft Azure"
    description="Use este artigo para entender os conceitos técnicos que ajudá-lo com êxito instalar, configurar e gerenciar a recuperação de Site do Azure."
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="mkjain"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/12/2016"
    ms.author="rajanaki"/>  


# <a name="hyper-v-replication-with-azure-site-recovery"></a>Replicação de Hyper-V com recuperação de Site do Azure

Este artigo descreve os conceitos técnicos que podem ajudá-lo a configurar e gerenciar um site de Hyper-V ou em um site do System Center Virtual Machine Manager (VMM) para proteção por Azure usando recuperação de Site do Azure com êxito.

## <a name="setting-up-the-source-environment-for-replication-between-on-premises-and-azure"></a>Configurar o ambiente de origem para replicação entre locais e do Azure

Como parte da configuração de recuperação de dados entre locais e Azure, certifique-se de baixar e instalar o provedor de recuperação de Site do Azure do servidor VMM. Instale o agente de serviços de recuperação do Azure em cada host Hyper-V.

![Implantação de site VMM para replicação entre locais e do Azure](media/site-recovery-understanding-site-to-azure-protection/image00.png)

Configurar o ambiente de origem em uma infraestrutura de Hyper-V gerenciado é semelhante a configurar o ambiente de origem em uma infraestrutura gerenciado VMM. A única diferença é que o provedor e agente estão instalados no host Hyper-V em si. No ambiente VMM, o provedor está instalado no servidor VMM e o agente é instalado nos hosts Hyper-V (no caso de replicação no Azure).

## <a name="workflows"></a>Fluxos de trabalho

### <a name="enable-protection"></a>Habilitar proteção
Depois que você protege uma máquina virtual do portal do Azure ou local, inicia um trabalho de recuperação de Site denominado **Habilitar proteção** . Você pode monitorá-lo sob a guia de **trabalhos** .

![Trabalho de "Ativar proteção" na lista de trabalhos](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

O trabalho **Habilitar proteção** verifica os pré-requisitos antes de invocar o método [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) . Este método cria replicação no Azure usando entradas que são configuradas durante a proteção.

O trabalho **Habilitar proteção** inicia a replicação inicial do local chamando o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) . Este método envia discos virtuais da máquina virtual ao Azure.

![Detalhes do trabalho "Ativar proteção"](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### <a name="finalize-protection-on-the-virtual-machine"></a>Finalizar proteção na máquina virtual
Um [instantâneo de máquina virtual do Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) é feito quando replicação inicial é disparada. Discos virtuais são processados um por vez até que todos os discos são carregados no Azure. Normalmente, isso leva algum tempo para concluir, com base no tamanho do disco e a largura de banda. Para otimizar o uso da rede, consulte [como gerenciar locais para uso de largura de banda de rede de proteção Azure](https://support.microsoft.com/kb/3056159).

Após concluir a replicação inicial, o trabalho de **proteção de finalizar na máquina virtual** configura as configurações de rede e replicação POST. Enquanto replicação inicial está em andamento:

- Todas as alterações aos discos são rastreadas. 
- Armazenamento em disco adicional é consumido para os arquivos de Log de réplica do Hyper-V (HRL) e um instantâneo.

Após a conclusão da replicação inicial, o instantâneo de máquina virtual do Hyper-V é excluído. Esta exclusão resulta em Mesclar alterações de dados após replicação inicial para o disco pai.

![Trabalho de "Finalizar proteção na máquina virtual" na lista de trabalhos](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### <a name="delta-replication"></a>Replicação delta
Controlador de replicação de réplica do Hyper-V, que é parte do mecanismo de replicação de réplica do Hyper-V, faixas as alterações para um disco rígido virtual como arquivos de Log de réplica do Hyper-V (*.hrl). Arquivos HRL estão na mesma pasta como as discos associados.

Cada disco que está configurado para replicação tem um arquivo HRL associado. Esse log é enviado à conta de armazenamento do cliente após a conclusão da replicação inicial. Quando um log estiver em trânsito para Azure, as alterações no principal são rastreadas em outro arquivo de log no mesmo diretório.

Durante a replicação inicial ou replicação delta, você pode monitorar integridade de replicação de máquina virtual no modo de exibição de máquina virtual, como mencionado no [Monitor saúde de replicação para máquina virtual](./site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine).  

### <a name="resynchronization"></a>Nova sincronização
Uma máquina virtual está marcada para nova sincronização quando falha de replicação delta e replicação completa inicial é cara em termos de largura de banda de rede ou a hora. Por exemplo, quando o tamanho do arquivo HRL pilhas até 50% do tamanho total do disco, a máquina virtual está marcada para nova sincronização. Nova sincronização minimiza a quantidade de dados enviados pela rede Calculando somas de verificação dos discos máquina virtual origem e destino e enviando apenas o diferencial.

Após concluir a nova sincronização, replicação delta normal deve continuar. Você pode continuar a nova sincronização se ocorrer uma falha de rede ou outra interrupção.

Por padrão, a nova sincronização agendada automaticamente está configurada para ocorrer fora do horário de trabalho. Se a máquina virtual precisa ser sincronizados novamente manualmente, selecione a máquina virtual do portal e clique em **sincronizar novamente**.

![Nova sincronização manual](media/site-recovery-understanding-site-to-azure-protection/image04.png)

Nova sincronização usa um algoritmo de agrupamento de bloco fixo onde os arquivos de origem e destino são divididos em blocos fixos. Somas de verificação para cada bloco são geradas e então comparadas para determinar quais blocos da origem precisam ser aplicado ao destino.

### <a name="retry-logic"></a>Uma lógica de repetição
Não há lógica de repetição internos de erros de replicação. Essa lógica pode ser classificada em duas categorias:

| Categoria                  | Cenários                                    |
|---------------------------|----------------------------------------------|
| Erro não recuperável     | Nenhuma nova tentativa será feita. Status de replicação de máquina virtual é **crítica**e intervenção do administrador é necessária. Alguns exemplos incluem: <ul><li>Cadeia VHD quebrado</li><li>Estado inválido para a máquina virtual de réplica</li><li>Erro de autenticação de rede</li><li>Erro de autorização</li><li>Máquina virtual que não for localizada, no caso de um servidor autônomo Hyper-V</li></ul>|
| Erro recuperável         | Repetições ocorrerem cada intervalo de replicação, usando um exponencial retirada que aumenta o intervalo de repetição desde o início da primeira tentativa (1, 2, 4, 8, 10 minutos). Se o erro persistir, tente novamente a cada 30 minutos. Alguns exemplos incluem: <ul><li>Erro de rede</li><li>Pouco espaço em disco</li><li>Condição de memória baixa</li></ul>|

## <a name="hyper-v-virtual-machine-protection-and-recovery-life-cycle"></a>Ciclo de vida de proteção e recuperação de máquina virtual Hyper-V

![Ciclo de vida de proteção e recuperação de máquina virtual Hyper-V](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## <a name="other-references"></a>Outras referências

- [Monitorar e solucionar problemas de proteção para sites de VMware, VMM, Hyper-V e físicas](./site-recovery-monitoring-and-troubleshooting.md)
- [Alcançando-out Microsoft Support](./site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
- [Erros comuns de recuperação de Site do Azure e suas soluções](./site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)
