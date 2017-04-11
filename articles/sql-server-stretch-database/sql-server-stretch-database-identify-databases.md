<properties
    pageTitle="Identificar bancos de dados e tabelas de banco de dados de alongar executando o Supervisor de banco de dados de alongar | Microsoft Azure"
    description="Saiba como identificar bancos de dados e tabelas que são candidatos para alongar banco de dados."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="identify-databases-and-tables-for-stretch-database-by-running-stretch-database-advisor"></a>Identificar bancos de dados e tabelas de banco de dados de alongar executando Alongar Supervisor de banco de dados

Para identificar os bancos de dados e tabelas que são candidatos para banco de dados de alongar, baixe o Supervisor de atualização do SQL Server 2016 e executar o Supervisor de banco de dados Alongar. Alongar Supervisor de banco de dados também identifica problemas de bloqueio.

## <a name="download-and-install-upgrade-advisor"></a>Baixe e instale o Supervisor de atualização
Baixe e instale o Supervisor de atualização do [aqui](http://go.microsoft.com/fwlink/?LinkID=613421). Esta ferramenta não está incluída na mídia de instalação do SQL Server.

## <a name="run-the-stretch-database-advisor"></a>Executar o Supervisor de banco de dados estendido

1.  Execute o Supervisor de atualização.

2.  Selecione **cenários**e selecione **Executar SUPERVISOR de banco de dados de ALONGAR**.

3.  Na lâmina **Supervisor de banco de dados de alongar executar** , clique em **Selecionar bancos de dados para analisar**.

4.  Na lâmina **Selecione bancos de dados** , digite ou selecione o nome do servidor e as informações de autenticação. Clique em **Conectar**.

5.  Uma lista de bancos de dados no servidor selecionado aparece. Selecione os bancos de dados que você deseja analisar. Clique em **Selecionar**.

6.  Na lâmina **Supervisor de banco de dados de alongar executar** , clique em **Executar**.  A análise é executado.

## <a name="review-the-results"></a>Analise os resultados

1.  Quando a análise for concluída, no blade **Analyzed bancos de dados** , selecione um dos bancos de dados que você analisados para exibir a lâmina de **resultados da análise** .

    A lâmina de **resultados da análise** lista recomendadas tabelas de banco de dados selecionado que correspondem ao critério de recomendação de padrão.

2.  Na lista de tabelas na lâmina **resultados da análise** , selecione uma das tabelas recomendadas para exibir a lâmina de **resultados da tabela** .

    Se houver são problemas de bloqueio, a lâmina de **resultados da tabela** lista os problemas de bloqueio da tabela selecionada. Para obter informações sobre problemas de bloqueio detectado pelo Alongar Supervisor de banco de dados, consulte [limitações para alongar banco de dados](sql-server-stretch-database-limitations.md).

3.  Na lista de problemas de bloqueio na lâmina **resultados de tabela** , selecione um dos problemas para exibir mais informações sobre a questão selecionada e propõe etapas de atenuação. Implemente as etapas de atenuação sugerido se você quiser configurar a tabela selecionada para alongar banco de dados.

## <a name="next-step"></a>Próxima etapa
Habilite Alongar banco de dados.

-   Para habilitar Alongar banco de dados em um **banco de dados**, consulte [Habilitar Alongar de banco de dados para um banco de dados](sql-server-stretch-database-enable-database.md).

-   Para habilitar Alongar banco de dados em outra **tabela**, quando Alongar já está ativado no banco de dados, consulte [Habilitar Alongar de banco de dados para uma tabela](sql-server-stretch-database-enable-table.md).

## <a name="see-also"></a>Consulte também

[Limitações de banco de dados estendido](sql-server-stretch-database-limitations.md)

[Habilitar Alongar banco de dados para um banco de dados](sql-server-stretch-database-enable-database.md)

[Ativar o banco de dados de Alongar para uma tabela](sql-server-stretch-database-enable-table.md)

[Todos os tópicos de serviço de banco de dados do Azure SQL Server Alongar](sql-server-stretch-database-index-all-articles.md)
