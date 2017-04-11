<properties
    pageTitle="Arquivar um banco de dados do SQL Azure para um arquivo BACPAC usando o Portal do Azure"
    description="Arquivar um banco de dados do SQL Azure para um arquivo BACPAC usando o Portal do Azure"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/15/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="archive-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Arquivar um banco de dados do SQL Azure para um arquivo BACPAC usando o Portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

Este artigo fornece instruções para arquivamento seu banco de dados do SQL Azure para um arquivo BACPAC (armazenado no armazenamento de blob do Microsoft Azure) usando o [portal do Azure](https://portal.azure.com).

Quando você precisa criar um arquivo de um banco de dados do SQL Azure, você pode exportar o esquema de banco de dados e dados para um arquivo BACPAC. Um arquivo BACPAC é simplesmente um arquivo ZIP com uma extensão de BACPAC. Um arquivo BACPAC mais tarde pode ser armazenado no armazenamento de blob do Microsoft Azure ou em armazenamento local em um local na e posterior importados Voltar no banco de dados do SQL Azure ou em um servidor SQL instalação local. 

***Considerações***

- Para um arquivo morto transacionalmente consistente, você não deve garantir um dos que nenhuma gravação está ocorrendo uma atividade durante a exportação, ou que você está exportando de uma [cópia transacionalmente consistente](sql-database-copy.md) de seu banco de dados do SQL Azure.
- O tamanho máximo de um arquivo BACPAC arquivado ao armazenamento de Blob do Azure é 200 GB. Para arquivar um arquivo BACPAC maior para o armazenamento local, use o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Este utilitário é fornecido com o Visual Studio e SQL Server. Você também pode [Baixar](https://msdn.microsoft.com/library/mt204009.aspx) a versão mais recente das ferramentas de dados do SQL Server para obter este utilitário.
- Não há suporte para arquivamento ao armazenamento de premium Azure usando um arquivo BACPAC.
- Se a operação de exportação exceder 20 horas, ela pode ser cancelada. Para aumentar o desempenho durante a exportação, você pode:
 - Aumente temporariamente o nível de serviço.
 - CESSAÇÃO todos ler e gravar atividade durante a exportação.
 - Use um [índice agrupado](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos em todas as tabelas grandes. Sem índices agrupados, uma exportação pode falhar se demora mais de 6 a 12 horas. Isso ocorre porque o serviço de exportação precisa concluir uma varredura de tabela para tentar exportar a tabela inteira. Uma boa maneira de determinar se as tabelas são otimizadas para exportação é executar **DBCC SHOW_STATISTICS** e verifique se o *RANGE_HI_KEY* não é nulo e seu valor tem boa distribuição. Para obter detalhes, consulte [SHOW_STATISTICS DBCC](https://msdn.microsoft.com/library/ms174384.aspx).


> [AZURE.NOTE] BACPACs não devem ser usadas para backup e restaurar operações. Banco de dados do SQL Azure cria automaticamente backups para cada banco de dados do usuário. Para obter detalhes, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md).

Para concluir este artigo, você precisa do seguinte:

- Uma assinatura do Azure.
- Um banco de dados do SQL Azure. 
- Uma [conta de armazenamento padrão do Azure](../storage/storage-create-storage-account.md) com um contêiner de blob para armazenar a BACPAC em armazenamento padrão.

## <a name="export-your-database"></a>Exportar seu banco de dados

Abra a lâmina de banco de dados SQL do banco de dados que você deseja exportar.

> [AZURE.IMPORTANT] Para garantir um arquivo BACPAC transacionalmente consistente, você deve primeiro [criar uma cópia de seu banco de dados](sql-database-copy.md) e exporte a cópia do banco de dados. 

1.  Acesse o [portal do Azure](https://portal.azure.com).
2.  Clique em **bancos de dados SQL**.
3.  Clique em banco de dados para arquivar.
4.  Na lâmina banco de dados SQL, clique em **Exportar** para abrir a lâmina **Exportar banco de dados** :

    ![botão Exportar][1]

5.  Clique em **armazenamento** e selecione sua conta e blob contêiner de armazenamento de onde o BACPAC será armazenado:

    ![Exportar banco de dados][2]

6. Selecione o tipo de autenticação. 
7.  Digite as credenciais de autenticação adequado para o servidor do SQL Azure que contém o banco de dados que você está exportando.
8.  Clique em **Okey** para arquivar o banco de dados. Clicar em **Okey** cria uma solicitação de banco de dados de exportação e envia para o serviço. Quanto tempo que levará a exportação depende do tamanho e complexidade de seu banco de dados e seu nível de serviço. Você receberá uma notificação.

    ![notificação de exportação][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>Monitorar o progresso da operação de exportação

1.  Clique em **servidores SQL**.
2.  Clique no servidor que contém o banco de dados (origem) original que você acabou de arquivados.
3.  Role para baixo até operações.
4.  No SQL lâmina do servidor, clique em **histórico de importação/exportação**:

    ![histórico de exportação de importação][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>Verifique se que o BACPAC está em seu contêiner de armazenamento

1.  Clique em **contas de armazenamento**.
2.  Clique na conta de armazenamento em que você armazenou o arquivamento BACPAC.
3.  Clique em **contêineres** e selecione o contêiner exportado no banco de dados em detalhes (você pode baixar e salvar o BACPAC daqui).

    ![detalhes do arquivo de .bacpac][5]  

## <a name="next-steps"></a>Próximas etapas

- Para saber sobre como importar um BACPAC um banco de dados do SQL Azure, consulte [Importar um BACPCAC um banco de dados do SQL Azure](sql-database-import.md)
- Para saber sobre a importação de um BACPAC para um banco de dados do SQL Server, consulte [Importar um BACPCAC um banco de dados do SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)



<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png

