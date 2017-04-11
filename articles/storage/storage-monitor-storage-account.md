<properties
    pageTitle="Como monitorar uma conta de armazenamento | Microsoft Azure"
    description="Saiba como monitorar uma conta de armazenamento no Azure usando o Portal do Azure."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorar uma conta de armazenamento no Portal do Azure

## <a name="overview"></a>Visão geral

Você pode monitorar a sua conta de armazenamento do [Portal do Azure](https://portal.azure.com). Ao configurar sua conta de armazenamento para monitoramento por meio do portal, o armazenamento do Azure usa [A análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343270.aspx) para controlar métricas de sua conta e registre os dados de solicitação.

> [AZURE.NOTE]Custos adicionais são associados com a análise de dados de monitoramento no [Portal do Azure](https://portal.azure.com). Para obter mais informações, consulte <a href="http://msdn.microsoft.com/library/azure/hh360997.aspx">análise de armazenamento e cobrança</a>. <br />

> Armazenamento de arquivos Azure atualmente suporta métricas de análise de armazenamento, mas ainda não dá suporte a log. Você pode habilitar métricas para armazenamento de arquivos do Azure através do [Portal do Azure](https://portal.azure.com).

> Contas de armazenamento com um tipo de replicação de armazenamento redundantes de zona (ZRS) não tem o métricas ou o recurso de log habilitado no momento. 

> Para um guia detalhado sobre como usar a análise de armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao armazenamento do Azure, consulte [Monitor, diagnosticar e solucionar problemas de armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).


## <a name="how-to-configure-monitoring-for-a-storage-account"></a>Como: configurar o monitoramento para uma conta de armazenamento

1. No [Portal do Azure](https://portal.azure.com), clique em **armazenamento**e clique no nome da conta de armazenamento para abrir o painel de controle.

2. Clique em **Configurar**e role para baixo até as configurações de **monitoramento** para o blob, tabela e os serviços de fila.

    ![MonitoringOptions](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3. No **monitoramento**, defina o nível de monitoramento e a política de retenção de dados para cada serviço:

    -  Para definir o nível de monitoramento, selecione um destes procedimentos:

      **Mínimo** - coleta métricas como entrada/saída, disponibilidade, latência e sucesso porcentagens, que são agregadas para o blob, tabela e os serviços de fila.

      **Detalhado** - além as métricas mínimo, coleta o mesmo conjunto de métricas para cada operação de armazenamento na API do serviço de armazenamento do Azure. Métricas detalhadas habilitar análise detalhada dos problemas que ocorrem durante as operações do aplicativo.

      **Desativar** - desativa monitoramento. Existentes monitoramento de dados é mantido até o final do período de retenção.

- Para definir a política de retenção de dados, em **retenção (em dias)**, digite o número de dias dos dados a serem retidos de 1 a 365 dias. Se você não deseja definir uma política de retenção, digite zero. Se não houver nenhuma política de retenção, é até excluir os dados de monitoramento. Recomendamos que você definir uma política de retenção com base em quanto tempo deseja manter os dados de análise de armazenamento da sua conta para que os dados de análise antigas e inúteis podem ser excluídos pelo sistema sem nenhum custo.

4. Quando terminar a configuração de monitoramento, clique em **Salvar**.

Você deve começar vendo monitoramento de dados no painel e na página de **Monitor** após cerca de uma hora.

Até que você configura o monitoramento para uma conta de armazenamento, nenhum dado de monitoramento é coletado e os gráficos de métricas na página do **Monitor** e dashboard estão vazios.

Depois de definir os níveis de monitoramento e políticas de retenção, você pode escolher quais medidas disponíveis para monitorar no [Portal do Azure](https://portal.azure.com)e quais medidas plotar em gráficos de métricas. Um conjunto padrão de métricas é exibido em cada nível de monitoramento. Você pode usar **Métricas de adicionar** para adicionar ou remover métricas da lista métricas.

Métricas são armazenadas na conta de armazenamento em quatro tabelas denominada $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue e $MetricsCapacityBlob. Para obter mais informações, consulte [Sobre métricas de análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343258.aspx).


## <a name="how-to-customize-the-dashboard-for-monitoring"></a>Como: personalizar o painel de controle para monitoramento

No painel, você pode escolher até seis métricas Plotar no gráfico de métricas de nove métricas disponíveis. Para cada serviço (blob, tabela e fila), as métricas de disponibilidade, porcentagem de sucesso e Total de solicitações estão disponíveis. As métricas disponíveis no painel são as mesmas para monitoramento mínimo ou detalhado.

1. No [Portal do Azure](https://portal.azure.com), clique em **armazenamento**e clique no nome da conta de armazenamento para abrir o painel de controle.

2. Para alterar as métricas que são plotadas no gráfico, execute uma das seguintes ações:

    - Para adicionar uma nova métrica ao gráfico, clique na caixa de seleção colorida ao lado do cabeçalho da métrica na tabela abaixo do gráfico.

    - Para ocultar uma métrica que é plotada no gráfico, desmarque a caixa de seleção colorida ao lado do cabeçalho da métrica.

        ![Monitoring_nmore](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)

3. Por padrão, o gráfico mostra tendências, exibindo apenas o valor atual de cada métrica (a opção **relativa** na parte superior do gráfico). Para exibir um eixo Y para que você possa ver valores absolutos, selecione **absoluto**.

4. Para alterar o intervalo de tempo exibe o gráfico métricas, selecione 6 horas, 24 horas, ou 7 dias na parte superior do gráfico.


## <a name="how-to-customize-the-monitor-page"></a>Como: personalizar a página de Monitor

Na página do **Monitor** , você pode exibir o conjunto completo de métricas de sua conta de armazenamento.

- Se sua conta de armazenamento tem monitoramento mínimo configurado, métricas como entrada/saída, disponibilidade, latência e porcentagens de sucesso são agregadas de blob, tabela e serviços de fila.

- Se sua conta de armazenamento tem monitoramento detalhado configurado, as métricas estão disponíveis em uma melhor resolução de operações de armazenamento individuais além as agregações de nível de serviço.

Use os procedimentos a seguir para escolher quais métricas de armazenamento para exibir na tabela e gráficos de métricas que são exibidos na página do **Monitor** . Essas configurações não afetam o conjunto, agregação e armazenamento de dados na conta de armazenamento de monitoramento.

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Como: adicionar métricas à tabela de métricas


1. No [Portal do Azure](https://portal.azure.com), clique em **armazenamento**e clique no nome da conta de armazenamento para abrir o painel de controle.

2. Clique em **Monitor**.

    A página **Monitor** abre. Por padrão, a tabela de métricas exibe um subconjunto das métricas que estão disponíveis para monitoramento. A ilustração mostra o monitor padrão para uma conta de armazenamento com monitoramento detalhado configurado para todos os três serviços. Use **Adicionar métricas** para selecionar as métricas que você deseja monitorar de todas as métricas disponíveis.

    ![Monitoring_VerboseDisplay](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

    > [AZURE.NOTE] Considere a possibilidade de custos quando você seleciona as métricas. Há transação e os custos de egresso associados atualizando exibe monitoramento. Para obter mais informações, consulte [análise de armazenamento e cobrança](http://msdn.microsoft.com/library/azure/hh360997.aspx).

3. Clique em **Adicionar métricas**.

    As métricas agregadas que estão disponíveis no mínimo monitoramento estão na parte superior da lista. Se a caixa de seleção estiver marcada, a métrica é exibida na lista de métricas.

    ![AddMetricsInitialDisplay](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)

4. Focalize o lado direito da caixa de diálogo para exibir uma barra de rolagem que você pode arrastar para rolar métricas adicionais para modo de exibição.

    ![AddMetricsScrollbar](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)


5. Clique na seta para baixo por uma métrica para expandir uma lista das operações que a métrica é delimitada para incluir. Selecione cada operação que você deseja exibir na tabela métricas no [Portal do Azure](https://portal.azure.com).

    Na ilustração a seguir, a porcentagem de erro de autorização métrica foi expandida.

    ![ExpandCollapse](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)


6. Depois de selecionar métricas para todos os serviços, clique em Okey (marca de seleção) para atualizar a configuração de monitoramento. As métricas selecionadas são adicionadas à tabela de métricas.

7. Para excluir uma métrica da tabela, clique a métrica para selecioná-la e clique em **Excluir métrica**.

    ![DeleteMetric](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

## <a name="how-to-customize-the-metrics-chart-on-the-monitor-page"></a>Como: personalizar o gráfico de métricas na página do Monitor

1. Na página do **Monitor** para a conta de armazenamento, na tabela métricas, selecione até 6 métricas Plotar no gráfico de métricas. Para selecionar uma métrica, clique na caixa de seleção em seu lado esquerdo. Para remover uma métrica do gráfico, desmarque a caixa de seleção.

2. Para alternar o gráfico entre valores relativos (valor final só exibido) e valores absolutos (eixo Y exibido), selecione **relativo** ou **absoluto** na parte superior do gráfico.

3.  Para alterar a hora intervalo a métricas gráfico exibe, selecione **6 horas**, **24 horas**ou **7 dias** na parte superior do gráfico.



## <a name="how-to-configure-logging"></a>Como: configurar o registro em log

Para cada um dos serviços de armazenamento disponíveis com sua conta de armazenamento (blob, tabela e fila), você pode salvar logs de diagnóstico para solicitações de leitura, solicitações de escrever e/ou excluir solicitações e pode definir a política de retenção de dados para cada um dos serviços.

1. No [Portal do Azure](https://portal.azure.com), clique em **armazenamento**e clique no nome da conta de armazenamento para abrir o painel de controle.

2. Clique em **Configurar**e use a seta para baixo no teclado para rolar para baixo até o **registro em log**.

    ![Storagelogging](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)


3. Para cada serviço (blob, tabela e fila), configure o seguinte:

    - Os tipos de solicitação para fazer logon: solicitações de leitura, solicitações de escrever e solicitações Delete.

    - O número de dias para manter os dados registrados. Inserir zero é se você não deseja definir uma política de retenção. Se você não definir uma política de retenção, é até você exclua os logs.

4. Clique em **Salvar**.

Os logs de diagnóstico são salvos em um contêiner de blob denominado $logs em sua conta de armazenamento. Para obter informações sobre como acessar o contêiner $logs, consulte [Sobre o armazenamento de análise de registro em log](http://msdn.microsoft.com/library/azure/hh343262.aspx).
