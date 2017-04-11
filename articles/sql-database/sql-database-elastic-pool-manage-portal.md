<properties
    pageTitle="Monitorar e gerenciar um pool de banco de dados elástica com o portal do Azure | Microsoft Azure"
    description="Saiba como usar o portal do Azure e inteligência interna do banco de dados SQL para gerenciar, monitorar e tamanho direita um pool de banco de dados elástica scalable para otimizar o desempenho do banco de dados e gerenciar o custo."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="ninarn"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/22/2016"
    ms.author="ninarn"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="monitor-and-manage-an-elastic-database-pool-with-the-azure-portal"></a>Monitorar e gerenciar um pool de banco de dados elástica com o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Você pode usar o portal do Azure para monitorar e gerenciar um pool de banco de dados elástica e os bancos de dados no pool. No portal do, você pode monitorar a utilização de um pool de Elástico e os bancos de dados dentro de pool. Você também pode fazer um conjunto de alterações ao seu pool elástica e enviar todas as alterações ao mesmo tempo. Essas alterações incluem adicionando ou removendo bancos de dados, alterando as configurações de pool elástica ou alterando as configurações de banco de dados.

A imagem abaixo mostra um pool de elástica de exemplo. O modo de exibição inclui:

*  Gráficos para monitorar o uso de recursos do pool de elástica e os bancos de dados contidos no pool.
*  O botão de pool de **Configurar** para fazer alterações no pool Elástico.
*  O botão **Criar banco de dados** que cria um novo banco de dados e adiciona ao pool Elástico atual.
*  Trabalhos Elástico que ajudarão-lo a gerenciem grandes números de bancos de dados executando scripts Transact SQL em relação a todos os bancos de dados em uma lista.

![Modo de exibição do pool][2]

Para trabalhar as etapas neste artigo, você precisará de um SQL server no Azure com pelo menos um banco de dados e um pool Elástico. Se você não tiver um pool de Elástico, consulte [criar um pool](sql-database-elastic-pool-create-portal.md); Se você não tiver um banco de dados, consulte o [tutorial de banco de dados do SQL](sql-database-get-started.md).

## <a name="elastic-pool-monitoring"></a>Monitoramento de pool elástica

Você pode ir para um determinado pool para ver sua utilização de recursos. Por padrão, o pool está configurado para mostrar o uso de armazenamento e eDTU de última hora. O gráfico pode ser configurado para mostrar métricas diferentes sobre várias janelas de tempo.

1. Selecione um grupo para trabalhar com.
2. Em **Monitoramento de Pool elástica** é um gráfico, rotulado **utilização de recursos**. Clique no gráfico.

    ![Monitoramento de pool elástica][3]

    A lâmina **métrica** será aberta, mostrando uma exibição detalhada das métricas especificadas sobre a janela de tempo especificado.   

    ![Blade métrica][9]

### <a name="to-customize-the-chart-display"></a>Para personalizar a exibição de gráfico

Você pode editar o gráfico e o blade métrico para exibir outras métricas como porcentagem de CPU, porcentagem de IO de dados e porcentagem de IO log usada.

2. Na lâmina métrica, clique em **Editar**.

    ![Clique em Editar][6]

- Na lâmina **Editar gráfico** , selecione um novo intervalo de tempo (passaram hora, hoje, ou semana passada) ou clique em **personalizado** para selecionar qualquer intervalo de datas nas duas últimas semanas. Selecione o tipo de gráfico (barra ou linha) e, em seguida, selecione os recursos para monitorar.

> Observação: Somente métricas com a mesma unidade de medida podem ser exibidas no gráfico ao mesmo tempo. Por exemplo, se você selecionar "eDTU porcentagem", em seguida, você só poderá selecionar outras métricas com porcentagem como a unidade de medida.

    ![Click edit](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- Clique em **Okey**.


## <a name="elastic-database-monitoring"></a>Banco de dados Elástico monitoramento

Também é possível monitorar bancos de dados individuais para problemas potenciais.

1. Em **Elástica monitoramento de banco de dados**, há um gráfico que exibe métricas para cinco bancos de dados. Por padrão, o gráfico exibe os 5 principais bancos de dados no pool por média eDTU uso na última hora. Clique no gráfico.

    ![Monitoramento de pool elástica][4]

2. A lâmina de **Utilização de recursos do banco de dados** é exibida. Isso fornece uma visão detalhada do uso do banco de dados no pool. Usando a grade na parte inferior da lâmina, você pode selecionar qualquer bancos de dados no pool para exibir seu uso no gráfico (até 5 bancos de dados). Você também pode personalizar a janela de tempo e métricas exibida no gráfico, clicando em **Editar gráfico**.

    ![Blade de utilização do recurso de banco de dados][8]

### <a name="to-customize-the-view"></a>Personalizar o modo de exibição

1. Na lâmina **utilização de recursos do banco de dados** , clique em **Editar gráfico**.

    ![Clique em Editar gráfico](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. Na lâmina gráfico **Editar** , selecione um novo intervalo de tempo (depois Hora ou nas últimas 24 horas) ou clique em **personalizado** para selecionar um dia diferente na últimas 2 semanas para exibir.

    ![Clique em personalizado](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. Clique na lista suspensa **Comparar bancos de dados** para selecionar uma métrica diferente para usar quando comparando bancos de dados.

    ![Editar o organograma](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Para selecionar bancos de dados para monitorar

Na lista de banco de dados na lâmina **Utilização de recursos do banco de dados** , você pode encontrar bancos de dados específicos, procurando por meio das páginas na lista ou digitando na nome de um banco de dados. Use a caixa de seleção para selecionar o banco de dados.

![Pesquisa para bancos de dados monitorar][7]


## <a name="add-an-alert-to-a-pool-resource"></a>Adicionar um alerta para um recurso de pool

Você pode adicionar regras para os recursos que envia email para as pessoas ou cadeias de alerta para pontos de extremidade de URL quando o recurso atinge um limite de utilização que você configurar.

> [AZURE.IMPORTANT]Utilização de recursos monitoramento para Pools elástica tem um atraso de pelo menos 20 minutos. Configuração de alertas de menos de 30 minutos para Pools elástica não é suportado atualmente. Conjunto de todos os alertas para Pools elástica com um período (parâmetro chamado "-Tamanho_da_janela" na API do PowerShell) de menos de 30 minutos pode não ser disparada. Certifique-se de que todos os alertas que você definir para Pools elástica usam um período (Tam_janela) de 30 minutos ou mais.

**Para adicionar um alerta para qualquer recurso:**

1. Clique no gráfico de **utilização de recursos** para abrir a lâmina **métrica** , clique em **Adicionar alerta**e, em seguida, preencha as informações na lâmina **Adicionar uma regra de alerta** (**recurso** é automaticamente configurada para ser o pool que você estiver trabalhando com).
2. Digite um **nome** e uma **Descrição** que identifica o alerta para você e os destinatários.
3. Escolha uma **métrica** que você deseja alertar na lista.

    O gráfico mostra dinamicamente a utilização de recursos para essa métrica para ajudá-lo a escolher um limite.

4. Escolha uma **condição** (maior que, menor que, etc) e um **limite**.
5. Clique em **Okey**.



## <a name="move-a-database-into-an-elastic-pool"></a>Mover um banco de dados em um pool Elástico

Você pode adicionar ou remover bancos de dados de um pool existente. Os bancos de dados podem estar em outros pools. No entanto, você só pode adicionar bancos de dados que estão no mesmo servidor lógico.

1. Na lâmina para o pool, em **Elástico bancos de dados** , clique em **Configurar pool**.

    ![Clique em Configurar pool][1]

2. Na lâmina **pool de configurar** , clique em **Adicionar ao pool**.

    ![Clique em Adicionar ao pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)


3. Na lâmina **adicionar bancos de dados** , selecione o banco de dados ou bancos de dados para adicionar ao pool. Clique em **Selecionar**.

    ![Selecione bancos de dados para adicionar](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    A lâmina de **pool de configurar** agora lista o banco de dados que você selecionou a ser adicionado, com seu status definido como **pendente**.

    ![Adições de pool pendente](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. Na **lâmina de pool de configurar**, clique em **Salvar**.

    ![Clique em Salvar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="move-a-database-out-of-an-elastic-pool"></a>Tira um banco de dados um pool Elástico

1. Na lâmina **pool de configurar** , selecione o banco de dados ou bancos de dados para remover.

    ![lista de bancos de dados](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Clique em **Remover de pool**.

    ![lista de bancos de dados](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    A lâmina de **pool de configurar** agora lista o banco de dados que você selecionou para ser removido com seu status definido como **pendente**.

    ![adição de banco de dados de visualização e remoção](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. Na **lâmina de pool de configurar**, clique em **Salvar**.

    ![Clique em Salvar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="change-performance-settings-of-a-pool"></a>Alterar as configurações de desempenho de um pool

Conforme você monitora a utilização de um pool de recursos, você pode descobrir que alguns ajustes são necessárias. Talvez o pool precisa de uma alteração nos limites de armazenamento ou desempenho. Possivelmente você deseja alterar as configurações de banco de dados no pool. Você pode alterar a configuração do pool a qualquer momento para obter o melhor equilíbrio de desempenho e custo. Consulte [quando um pool de banco de dados elástica deve ser usado?](sql-database-elastic-pool-guidance.md) para obter mais informações.

**Para alterar os limites de armazenamento ou eDTUs por pool e eDTUs por banco de dados:**

1. Abra a lâmina de **pool de configurar** .

    Em **configurações do pool de banco de dados elástica**, use o controle deslizante para alterar as configurações do pool.

    ![Utilização de recursos do pool elástica](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Quando a configuração alterada, a exibição mostra o custo mensal estimado da alteração.

    ![Atualizando o novo custo mensal e um pool](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)


## <a name="create-and-manage-elastic-jobs"></a>Criar e gerenciar trabalhos Elástico

Trabalhos Elástico permitem executar scripts Transact-SQL em relação a qualquer número de bancos de dados no pool. Você pode criar novos trabalhos ou gerenciar trabalhos existentes no portal.

![Criar e gerenciar trabalhos Elástico][5]


Antes de usar trabalhos, instalar componentes de trabalhos elástica e forneça suas credenciais. Para obter mais informações, consulte [Visão geral de trabalhos de elástica banco de dados](sql-database-elastic-jobs-overview.md).

Consulte [escala check-out com o Azure SQL Database](sql-database-elastic-scale-introduction.md): usar ferramentas de banco de dados elástica para fora de escala, mover dados, consultar ou criar transações.



## <a name="additional-resources"></a>Recursos adicionais

- [Pool de elástica de banco de dados SQL](sql-database-elastic-pool.md)
- [Criar um pool de banco de dados elástica com o portal](sql-database-elastic-pool-create-csharp.md)
- [Criar um pool de banco de dados elástica com PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Criar um pool de banco de dados elástica com c#](sql-database-elastic-pool-create-csharp.md)
- [Considerações de preço e desempenho para pools de banco de dados elástica](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png
