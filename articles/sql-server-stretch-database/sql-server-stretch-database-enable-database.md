<properties
    pageTitle="Habilitar Alongar banco de dados para um banco de dados | Microsoft Azure"
    description="Saiba como configurar um banco de dados para o banco de dados de Alongar."
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
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="enable-stretch-database-for-a-database"></a>Habilitar Alongar banco de dados para um banco de dados

Para configurar um banco de dados existente para alongar banco de dados, selecione tarefas **| Alongar | Habilitar** para um banco de dados do SQL Server Management Studio para abrir o Assistente de **Habilitar o banco de dados para alongar** . Você também pode usar Transact\-SQL para habilitar Alongar banco de dados para um banco de dados.

Se você selecionar tarefas **| Alongar | Habilitar** para uma tabela individual e você ainda não ativou o banco de dados do banco de dados de alongar, o assistente configura o banco de dados do banco de dados de alongar e permite que você selecione tabelas como parte do processo. Siga as etapas neste tópico em vez das etapas em [Habilitar Alongar de banco de dados para uma tabela](sql-server-stretch-database-enable-database.md).

Habilitando Alongar banco de dados em um banco de dados ou uma tabela requer db\_permissões de proprietário. Habilitando Alongar banco de dados em um banco de dados também requer permissões de banco de dados de controle.

 >   [AZURE.NOTE] Posteriormente, se você desabilitar o banco de dados de alongar, lembre-se de que desabilitando o banco de dados de Alongar para uma tabela ou um banco de dados não exclui o objeto remoto. Se você quiser excluir tabela remota ou banco de dados remoto, você precisa soltá-lo usando o portal de gerenciamento do Azure. Objetos remotos continuam provoca custos Azure até você excluí-los manualmente.

## <a name="before-you-get-started"></a>Antes de começar

-   Antes de configurar um banco de dados para alongar, recomendamos que você executar o Supervisor de banco de dados de Alongar para identificar bancos de dados e tabelas que estão qualificadas para alongar. O Supervisor de banco de dados Alongar também identifica problemas de bloqueio. Para obter mais informações, consulte [identificar bancos de dados e tabelas de banco de dados de alongar](sql-server-stretch-database-identify-databases.md).

-   Revise [limitações para alongar banco de dados](sql-server-stretch-database-limitations.md).

-   Banco de dados Alongar migra dados para Azure. Portanto, você precisa ter uma conta do Azure e uma assinatura para cobrança. Para obter uma conta do Azure, [clique aqui](http://azure.microsoft.com/pricing/free-trial/).

-   Ter as informações de conexão e faça logon necessárias para criar um novo servidor Azure ou para selecionar um servidor Azure existente.

## <a name="EnableTSQLServer"></a>Pré-requisito: Habilitar o banco de dados de alongar no servidor
Antes de habilitar Alongar banco de dados em um banco de dados ou uma tabela, você precisa ativá-lo no servidor local. Esta operação requer permissões de administrador do sistema ou serveradmin.

-   Se você tiver as permissões administrativas necessárias, o Assistente de **Habilitar o banco de dados para alongar** configura o servidor para alongar.

-   Se você não tiver as permissões necessárias, um administrador deve habilitar a opção manualmente executando **sp\_configurar** antes de executar o assistente, ou um administrador precisa executar o assistente.

Para ativar manualmente o banco de dados de alongar no servidor, execute **sp\_configurar** e ative a opção de **arquivamento de dados remoto** . O exemplo a seguir habilita a opção de **arquivamento de dados remoto** definindo seu valor como 1.

```
EXEC sp_configure 'remote data archive' , '1';
GO

RECONFIGURE;
GO
```
Para obter mais informações, consulte [Configurar os dados remotos arquivar opção de configuração de servidor](https://msdn.microsoft.com/library/mt143175.aspx) e [sp_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx).

## <a name="Wizard"></a>Use o Assistente para habilitar Alongar banco de dados em um banco de dados
Para obter informações sobre o banco de dados habilitar para alongar assistente, incluindo as informações que você precisa digitar e as opções que você precisa fazer, consulte [Introdução executando o banco de dados habilitar para alongar Assistente](sql-server-stretch-database-wizard.md).

## <a name="EnableTSQLDatabase"></a>Use Transact\-SQL para habilitar Alongar banco de dados em um banco de dados
Antes de habilitar Alongar banco de dados em tabelas individuais, você precisa ativá-lo no banco de dados.

Habilitando Alongar banco de dados em um banco de dados ou uma tabela requer db\_permissões de proprietário. Habilitando Alongar banco de dados em um banco de dados também requer permissões de banco de dados de controle.

1.  Antes de começar, escolha um servidor Azure existente para os dados que migra de banco de dados de alongar ou criar um novo servidor Azure.

2.  No servidor do Azure, crie uma regra de firewall com o intervalo de endereços IP do SQL Server que permite que o SQL Server se comunicar com o servidor remoto.

    Você pode localizar facilmente os valores que você precisa e cria a regra de firewall ao tentar conectar ao servidor do Azure no Pesquisador de objetos no SQL Server Management Studio (SSMS). SSMS ajuda você a criar a regra, abrindo a caixa de diálogo seguinte, que já inclui os valores de endereço IP necessários.

    ![Criar uma regra de firewall no SSMS][FirewallRule]

3.  Para configurar um banco de dados do SQL Server para alongar banco de dados, o banco de dados deve ter uma chave mestre de banco de dados. A chave mestra do banco de dados protege as credenciais de banco de dados de alongar usa para se conectar ao banco de dados remoto. Aqui está um exemplo que cria uma nova chave mestre de banco de dados.

    ```tsql
    USE <database>;
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>';
    GO
    ```

    Para obter mais informações sobre a chave mestra do banco de dados, consulte [criar chave mestre (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) e [criar uma chave mestre de banco de dados](https://msdn.microsoft.com/library/aa337551.aspx).

4.  Quando você configura um banco de dados do banco de dados de alongar, você precisa fornecer uma credencial de banco de dados de alongar a ser usado para comunicação entre o local no SQL Server e o servidor do Azure remoto. Você tem duas opções.

    -   Você pode fornecer uma credencial de administrador.

        -   Se você habilitar o banco de dados de alongar executando o assistente, você pode criar a credencial nesse momento.

        -   Se você planeja habilitar o banco de dados de alongar executando **ALTER DATABASE**, você precisa criar a credencial manualmente antes de executar **ALTER DATABASE** para habilitar Alongar banco de dados.

        Aqui está um exemplo que cria uma nova credencial.

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>';
        GO
        ```

        Para obter mais informações sobre a credencial, consulte [Criar banco de dados com escopo CREDENCIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx). Criando a credencial requer permissões de alterar qualquer CREDENCIAL.

    -   Você pode usar uma conta de serviço federado para o SQL Server para se comunicar com o servidor do Azure remoto quando as seguintes condições são verdadeiras.

        -   A conta de serviço em que a instância do SQL Server está em execução é uma conta de domínio.

        -   A conta de domínio pertence a um domínio federado cujo Active Directory com o Active Directory do Azure.

        -   O servidor do Azure remoto está configurado para oferecer suporte à autenticação do Active Directory do Azure.

        -   A conta de serviço em que a instância do SQL Server está em execução deve ser configurada como uma conta de administrador do sistema ou de dbmanager no servidor remoto Azure.

5.  Para configurar um banco de dados para o banco de dados de alongar, execute o comando ALTER DATABASE.

    1.  Para o argumento de servidor, forneça o nome de um servidor Azure existente, incluindo o `.database.windows.net` parte do nome de \- por exemplo, `MyStretchDatabaseServer.database.windows.net`.

    2.  Fornecer uma credencial de administrador existente com o argumento de CREDENCIAL ou especifique federados\_SERVICE\_conta = ON. O exemplo a seguir fornece uma credencial existente.

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = '<server_name>',
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO
    ```

## <a name="next-steps"></a>Próximas etapas
-   [Habilitar Alongar de banco de dados para uma tabela](sql-server-stretch-database-enable-table.md) para habilitar tabelas adicionais.

-   [Banco de dados do monitor Alongar](sql-server-stretch-database-monitor.md) para ver o status de migração de dados.

-   [Pausar e continuar Alongar banco de dados](sql-server-stretch-database-pause.md)

-   [Gerenciar e solucionar problemas de banco de dados de alongar](sql-server-stretch-database-manage.md)

-   [Bancos de dados habilitados para alongar backup](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Consulte também

[Identificar bancos de dados e tabelas de banco de dados de alongar](sql-server-stretch-database-identify-databases.md)

[Definir opções de banco de dados da ALTER (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[FirewallRule]: ./media/sql-server-stretch-database-enable-database/firewall.png
