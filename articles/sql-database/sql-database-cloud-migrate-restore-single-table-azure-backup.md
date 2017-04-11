<properties
    pageTitle="Restaurar uma única tabela de backup do banco de dados do Azure SQL | Microsoft Azure"
    description="Saiba como restaurar uma única tabela de backup do banco de dados do Azure SQL."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>


# <a name="how-to-restore-a-single-table-from-an-azure-sql-database-backup"></a>Como restaurar uma única tabela de um backup do banco de dados do Azure SQL

Você pode encontrar uma situação na qual você acidentalmente modificado alguns dados em um banco de dados do SQL e agora você deseja recuperar a tabela afetada única. Este artigo descreve como restaurar uma única tabela em um banco de dados de um dos banco de dados SQL [backups automáticos](sql-database-automated-backups.md).

## <a name="preparation-steps-rename-the-table-and-restore-a-copy-of-the-database"></a>Etapas preparatórias: renomeie a tabela e restaurar uma cópia do banco de dados
1. Identifique a tabela em seu banco de dados do SQL Azure que você deseja substituir a cópia restaurada. Use o Microsoft SQL Management Studio para renomear a tabela. Por exemplo, renomeie a tabela como &lt;nome de tabela&gt;old.

    **Observação** Para evitar que sejam bloqueados, certifique-se de que não há nenhuma atividade executando na tabela que você está renomeando. Se você encontrar problemas, certifique-se de que executar esse procedimento durante uma janela de manutenção.

2. Restaure um backup do banco de dados a um ponto no tempo que você deseja recuperar usando as etapas de [Ponto-In_Time restaurar](sql-database-recovery-using-backups.md#point-in-time-restore) .

    **Anotações**:
    - O nome do banco de dados restaurado será no formato DBName + carimbo de hora; Por exemplo, **Adventureworks2012_2016-01-01T22-12Z**. Esta etapa não substituir o nome do banco de dados existente no servidor. Esta é uma medida de segurança, e ela tem a intenção de permitir que você verifique o banco de dados restaurado antes de descartar seu banco de dados atual e renomear o banco de dados restaurado para uso de produção.
    - Todos os níveis de desempenho de básico Premium são automaticamente copiados pelo serviço, com diversos métricas de retenção de backup, dependendo do nível de:

| Restauração do banco de dados | Nível básico | Níveis padrão | Níveis de Premium |
| :-- | :-- | :-- | :-- |
|  Restaurar de ponto no tempo |  Qualquer ponto de restauração dentro de 7 dias|Qualquer ponto de restauração em 35 dias| Qualquer ponto de restauração em 35 dias|

## <a name="copying-the-table-from-the-restored-database-by-using-the-sql-database-migration-tool"></a>Copiando a tabela do banco de dados restaurado usando a ferramenta de migração de banco de dados SQL
1. Baixe e instale o [Assistente de migração de banco de dados do SQL](https://sqlazuremw.codeplex.com).

2. Abrir o Assistente de migração do banco de dados do SQL, na página **Selecionar processo** , selecione o **banco de dados em análise/migrar**e clique em **Avançar**.
![Assistente de migração de banco de dados do SQL - Selecionar processo](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)
3. Na caixa de diálogo **conectar ao servidor** , aplica as configurações a seguir:
 - **Nome do servidor**: instância de seu SQL Azure
 - **Autenticação**: **autenticação do SQL Server**. Insira suas credenciais de login.
 - **Banco de dados**: **Master DB (lista todos os bancos de dados)**.
 - **Observação** Por padrão, o assistente salva suas informações de login. Se você não deseja, selecione **Esquecer informações de logon**.
![Etapa do assistente - Selecionar fonte - de migração de banco de dados do SQL 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. Na caixa de diálogo **Selecionar fonte** , selecione o nome do banco de dados restaurado da seção **etapas preparatórias** como sua fonte e clique em **Avançar**.

    ![Etapa 2 do assistente - Selecionar fonte - migração de banco de dados SQL](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)

5. Na caixa de diálogo **Escolher objetos** , selecione a opção de **Selecionar objetos de banco de dados específico** e, em seguida, selecione a table(or tables) que você deseja migrar para o servidor de destino.
![Assistente de migração de banco de dados do SQL - escolher objetos](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)

6. Na página de **Resumo do Assistente de Script** , clique em **Sim** quando solicitado sobre se você estiver pronto para gerar um script SQL. Você também tem a opção para salvar o Script TSQL para uso posterior.
![Assistente de migração de banco de dados do SQL - resumo do Assistente de Script](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)

7. Na página de **Resumo de resultados** , clique em **Avançar**.
![Assistente de migração de banco de dados do SQL - resumo de resultados](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)

8. Na página de **Configuração de Conexão de servidor de destino** , clique em **conectar ao servidor**e, em seguida, insira os detalhes da seguinte maneira:
    - **Nome do servidor**: instância do servidor de destino
    - **Autenticação**: **autenticação do SQL Server**. Insira suas credenciais de login.
    - **Banco de dados**: **Master DB (lista todos os bancos de dados)**. Esta opção lista todos os bancos de dados no servidor de destino.

    ![Assistente de migração de banco de dados do SQL - configuração de Conexão de servidor de destino](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)

9. Clique em **Conectar**, selecione o banco de dados de destino que você deseja mover a tabela e clique em **Avançar**. Isso deve concluir a execução do script gerado anteriormente e você verá a tabela recém movida copiada para o banco de dados de destino.

## <a name="verification-step"></a>Etapa de verificação
1. Consultar e testar a tabela recém copiada para garantir que os dados sejam intactos. Após confirmação, você pode soltar o formulário da tabela renomeados seção **etapas preparatórias** . (por exemplo, &lt;nome de tabela&gt;old).

## <a name="next-steps"></a>Próximas etapas

[Backups automáticos de banco de dados SQL](sql-database-automated-backups.md)
