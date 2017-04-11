<properties
    pageTitle="Copiar um banco de dados do SQL Azure | Microsoft Azure"
    description="Criar uma cópia de um banco de dados do SQL Azure"
    services="sql-database"
    documentationCenter=""
    authors="anosov1960"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/24/2016"
    ms.author="sstein; sashan"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="copy-an-azure-sql-database"></a>Copiar um banco de dados do SQL Azure

> [AZURE.SELECTOR]
- [Visão geral](sql-database-copy.md)
- [Portal do Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Você pode usar o Azure [backups automatizados do banco de dados SQL](sql-database-automated-backups.md) para criar uma cópia do banco de dados SQL. A cópia do banco de dados usa a mesma tecnologia como o recurso de replicação de localização geográfica. Mas, ao contrário de replicação geográfica ele termina o link de replicação como depois que a fase de propagação é concluída. Portanto, o banco de dados de cópia é um instantâneo do banco de dados de origem no momento da solicitação de cópia.  
Você pode criar a cópia do banco de dados no mesmo servidor ou em um servidor diferente. O nível e o desempenho do nível de serviço (camada preços) da cópia do banco de dados são a mesma que o banco de dados de origem por padrão. Ao usar a API, você pode selecionar um nível de desempenho diferentes dentro do mesmo nível de serviço (edição). Após a cópia for concluída, a cópia torna-se um banco de dados totalmente funcional, independente. Neste ponto, você pode atualizar ou fazer downgrade-la para qualquer edição. O logon, os usuários e permissões podem ser gerenciadas independentemente.  

Quando você copia um banco de dados no mesmo servidor lógico, o mesmo logon pode ser usado em ambos os bancos de dados. O objeto que você usa para copiar o banco de dados de segurança se torna o proprietário do banco de dados (DBO) no novo banco de dados. Todos os usuários de banco de dados, suas permissões e seus identificadores de segurança (SIDs) são copiados para a cópia do banco de dados.  

Quando você copia um banco de dados para um servidor lógico diferente, a segurança principal no novo servidor se torna o proprietário do banco de dados no novo banco de dados. Se você usar [contidos usuários banco de dados](sql-database-manage-logins.md) para o acesso a dados assegura bancos de dados principais e secundários sempre tem as mesmas credenciais de usuário para após a cópia imediatamente poder acessá-lo com as mesmas credenciais. Se você usa o [Active Directory do Azure](../active-directory/active-directory-whatis.md), você pode eliminar completamente a necessidade de gerenciamento de credenciais na cópia. No entanto, quando você copia o banco de dados para um novo servidor, o acesso de logon com base geralmente não funcionará porque o logon não irá existir no novo servidor. Veja [como gerenciar a segurança de banco de dados do SQL Azure após a recuperação de dados](sql-database-geo-replication-security-config.md) para aprender sobre o gerenciamento de logon ao copiar um banco de dados para um servidor lógico diferente. 

Para copiar um banco de dados do SQL, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure simplesmente clique **Avaliação gratuita** na parte superior desta página e, em seguida, volte ao fim deste artigo.
- Um banco de dados SQL para copiar. Se você não tiver um banco de dados do SQL, criar uma seguindo as etapas neste artigo: [criar seu banco de dados do SQL Azure primeiro](sql-database-get-started.md).

## <a name="next-steps"></a>Próximas etapas

- Consulte [Copiar um banco de dados do SQL Azure usando o portal do Azure](sql-database-copy-portal.md) para copiar um banco de dados usando o portal do Azure.
- Consulte [Copiar um banco de dados do SQL Azure usando o PowerShell](sql-database-copy-powershell.md) para copiar um banco de dados usando o PowerShell.
- Consulte [Copiar um banco de dados do SQL Azure usando T-SQL](sql-database-copy-transact-sql.md) para copiar um banco de dados com Transact-SQL.
- Veja [como gerenciar a segurança de banco de dados do SQL Azure após a recuperação de dados](sql-database-geo-replication-security-config.md) para saber mais sobre gerenciamento de usuários e logon ao copiar um banco de dados para um servidor lógico diferente.



## <a name="additional-resources"></a>Recursos adicionais

- [Gerenciar logon](sql-database-manage-logins.md)
- [Conectar ao banco de dados do SQL com o SQL Server Management Studio e executar uma consulta de T-SQL de amostra](sql-database-connect-query-ssms.md)
- [Exportar o banco de dados para um BACPAC](sql-database-export.md)
- [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
- [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)
