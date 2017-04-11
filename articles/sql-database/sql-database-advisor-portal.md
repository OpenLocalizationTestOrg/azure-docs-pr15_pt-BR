<properties 
   pageTitle="Supervisor de banco de dados do SQL Azure usando o portal de Azure | Microsoft Azure" 
   description="Você pode usar o Supervisor de banco de dados do SQL Azure no portal do Azure para revisar e implementar recomendações para seus bancos de dados SQL existente que pode melhorar o desempenho de consulta atual." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="09/30/2016"
   ms.author="sstein"/>

# <a name="sql-database-advisor-using-the-azure-portal"></a>Supervisor de banco de dados do SQL usando o portal do Azure

> [AZURE.SELECTOR]
- [Visão geral de Supervisor de banco de dados do SQL](sql-database-advisor.md)
- [Portal](sql-database-advisor-portal.md)

Você pode usar o Supervisor de banco de dados do SQL Azure no portal do Azure para revisar e implementar recomendações para seus bancos de dados SQL existente que pode melhorar o desempenho de consulta atual.

## <a name="viewing-recommendations"></a>Exibindo recomendações

A página de recomendações é onde você pode exibir as principais recomendações para melhorar o desempenho com base em seu impacto potencial. Você também pode exibir o status das operações de históricos. Selecione uma recomendação ou status para ver mais detalhes.

Para ver e aplicar recomendações, você precisa as permissões de [controle de acesso baseado em função](../active-directory/role-based-access-control-configure.md) de correto no Azure. **Leitor**, permissões de **Colaborador de BD SQL** são necessárias para exibir recomendações e **proprietário**, são necessárias permissões de **Colaborador de banco de dados SQL** para executar qualquer ações; Criar ou descartar índices e cancelar a criação do índice.

1. Entrar no [portal do Azure](https://portal.azure.com/).
2. Clique em **mais serviços** > **bancos de dados SQL**e selecione o banco de dados.
5. Clique em **recomendação de desempenho** para exibir recomendações disponíveis para o banco de dados selecionado.

> [AZURE.NOTE] Para obter recomendações um banco de dados precisa ter sobre um dia do uso e deve haver algumas atividades. Também deve haver alguma atividade consistente. O Supervisor de banco de dados do SQL com mais facilidade pode otimizar para padrões de consulta consistente que ele para picos irregular aleatórios de atividade. Se recomendações não estiverem disponíveis, a página de **recomendação de desempenho** deve fornecer uma mensagem explicando o porquê.

![Recomendações](./media/sql-database-advisor-portal/recommendations.png)

Aqui está um exemplo de "Corrigir o problema de esquema" recomendação no portal do Azure.

![Corrigir o problema de esquema](./media/sql-database-advisor-portal/sql-database-advisor-schema-issue.png)

Recomendações são classificadas por seu impacto potencial no desempenho nas quatro categorias a seguir:

| Impacto | Descrição |
| :--- | :--- |
| Alto | Recomendações de alto impacto devem fornecer o impacto de desempenho mais significativo. |
| Média | Impacto médio recomendações devem melhorar o desempenho, mas não substancialmente. |
| Baixa | Recomendações de baixo impacto devem fornecer melhor desempenho que sem, mas melhorias podem não ser significativas. 


### <a name="removing-recommendations-from-the-list"></a>Removendo recomendações da lista

Se sua lista de recomendações contiver itens que você deseja remover da lista, você pode descartar a recomendação:

1. Selecione uma recomendação na lista de **recomendações**.
2. Clique em **Descartar** na lâmina **detalhes** .


Se desejar, você pode adicionar itens descartados volta para a lista de **recomendações** :

1. Na lâmina **recomendações** clique **descartadas de modo de exibição**.
1. Selecione um item descartado na lista para exibir seus detalhes.
1. Opcionalmente, clique em **Desfazer descartar** para adicionar o índice de volta para a lista principal de **recomendações**.



## <a name="applying-recommendations"></a>Aplicação de recomendações

Supervisor de banco de dados do SQL dá controle completo sobre como recomendações são habilitados usando qualquer uma das três opções a seguir: 

- Aplica recomendações individuais um por vez.
- Habilitar o Supervisor aplicar automaticamente recomendações (se aplica atualmente à somente recomendações de índice).
- Para implementar uma recomendação manualmente, execute o script de T-SQL recomendado em seu banco de dados.

Selecione qualquer recomendação para exibir os detalhes e clique em **Exibir script** para revisar os detalhes exatos de como a recomendação é criada.

O banco de dados permanece on-line enquanto o Supervisor aplica a recomendação – usar o Supervisor de banco de dados do SQL nunca leva um banco de dados offline.

### <a name="apply-an-individual-recommendation"></a>Aplicar uma recomendação individual

Você pode revisar e aceitar recomendações um por vez.

1. Na lâmina **recomendações** , clique em uma recomendação.
2. Na lâmina **detalhes** , clique em **Aplicar**.

    ![Aplicar recomendação](./media/sql-database-advisor-portal/apply.png)

### <a name="enable-automatic-index-management"></a>Ativar o gerenciamento de índice automático

Você pode definir o Supervisor de banco de dados SQL para implementar recomendações automaticamente. Conforme recomendações disponibilizados eles serão aplicados automaticamente. Como com todas as operações de índice gerenciadas pelo serviço se o impacto de desempenho for negativo a recomendação será revertida.

1. Na lâmina **recomendações** , clique em **automatizar**:

    ![Configurações de Supervisor](./media/sql-database-advisor-portal/settings.png)

2. Defina o Supervisor para automaticamente os índices de **criar** ou **soltar** :

    ![Recomendado índices](./media/sql-database-advisor-portal/automation.png)


### <a name="manually-run-the-recommended-t-sql-script"></a>Execute manualmente o script T-SQL recomendado

Selecione qualquer recomendação e clique em **Exibir script**. Execute esse script em seu banco de dados para aplicar manualmente a recomendação.

*Índices que são executados manualmente não monitoramento e validados para impacto no desempenho pelo serviço* para que ele é sugerido que você monitora esses índices após a criação para verificar se eles oferecem ganhos de desempenho e ajustar ou excluí-las, se necessário. Para obter detalhes sobre a criação de índices, consulte [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### <a name="canceling-recommendations"></a>Cancelando recomendações

Recomendações que estiverem em um status **pendente da equipe**, **Verificando**ou **sucesso** podem ser canceladas. Recomendações com um status de **execução** não podem ser canceladas.

1. Selecione uma recomendação na área do **Histórico de ajuste** para abrir a lâmina de **detalhes de recomendações** .
2. Clique em **Cancelar** para cancelar o processo de aplicar a recomendação.



## <a name="monitoring-operations"></a>Operações de monitoramento

Aplicar uma recomendação pode não ocorrer instantaneamente. O portal fornece detalhes sobre o status das operações de recomendação. Estes são possíveis estados que um índice pode estar em:

| Status | Descrição |
| :--- | :--- |
| Pendente | Aplica recomendação comando foi recebido e está agendado para execução. |
| Em execução | A recomendação está sendo aplicada. |
| Sucesso | Recomendação foi aplicada com êxito. |
| Erro | Ocorreu um erro durante o processo de aplicar a recomendação. Isso pode ser um problema temporário ou possivelmente um esquema alterar para a tabela e o script não é mais válido. |
| Revertendo | A recomendação foi aplicada, mas foi considerada não-desempenho e está sendo revertida automaticamente. |
| Revertido | A recomendação foi revertida. |

Clique em uma recomendação em processo na lista para ver mais detalhes:

![Recomendado índices](./media/sql-database-advisor-portal/operations.png)


### <a name="reverting-a-recommendation"></a>Reverter uma recomendação

Se você usou o Supervisor para aplicar a recomendação (significando que você não executou manualmente o script T-SQL)-la automaticamente reverterá ele se encontra o impacto de desempenho para ser negativo. Se por algum motivo, você simplesmente quiser reverter uma recomendação, você pode fazer o seguinte:


1. Na área de **histórico de ajuste** , selecione uma recomendação aplicada com êxito.
2. Clique em **Reverter** na lâmina **detalhes de recomendação** .

![Recomendado índices](./media/sql-database-advisor-portal/details.png)


## <a name="monitoring-performance-impact-of-index-recommendations"></a>Impacto monitoramento do desempenho de recomendações de índice

Depois de recomendações com êxito são implementadas (atualmente, operações de índice e parametrizar somente recomendações de consultas) você pode clicar em **Ideias de consulta** no blade recomendação detalhes para abrir a [Obtenção de informações de desempenho de consulta](sql-database-query-performance.md) e ver o impacto de desempenho das suas consultas mais importantes.

![Impacto de desempenho do monitor](./media/sql-database-advisor-portal/query-insights.png)



## <a name="summary"></a>Resumo

Supervisor de banco de dados do SQL fornece recomendações para melhorar o desempenho de banco de dados do SQL. Fornecendo scripts T-SQL, bem como individuais e totalmente-automático (atualmente índice somente), o Supervisor fornece assistência útil em otimizar seu banco de dados e, por fim, melhorar o desempenho de consulta.



## <a name="next-steps"></a>Próximas etapas

Monitore suas recomendações e continuar aplicá-las para refinar o desempenho. Cargas de trabalho de banco de dados são dinâmicas e altere continuamente. Supervisor de banco de dados SQL continuará monitorar e fornecer recomendações que potencialmente podem melhorar o desempenho de seu banco de dados. 

 - Consulte [Supervisor de banco de dados SQL](sql-database-advisor.md) para uma visão geral do Supervisor de banco de dados do SQL.
 - Consulte [Obtenção de informações de desempenho de consulta](sql-database-query-performance.md) para saber sobre como visualizar o impacto de desempenho das suas consultas mais importantes.

## <a name="additional-resources"></a>Recursos adicionais

- [Repositório de consulta](https://msdn.microsoft.com/library/dn817826.aspx)
- [CRIAR ÍNDICE](https://msdn.microsoft.com/library/ms188783.aspx)
- [Controle de acesso baseado em função](../active-directory/role-based-access-control-configure.md)






