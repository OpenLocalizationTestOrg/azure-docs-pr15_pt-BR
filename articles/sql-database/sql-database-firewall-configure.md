<properties
   pageTitle="Configurar uma visão geral firewall do SQL server | Microsoft Azure"
   description="Saiba como configurar um firewall de banco de dados do SQL com regras de firewall de servidor e níveis de banco de dados para gerenciar o acesso."
   keywords="firewall do banco de dados"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2016"
   ms.author="rickbyh"/>

# <a name="configure-azure-sql-database-firewall-rules---overview"></a>Configurar regras de firewall do Azure SQL Database \- visão geral


> [AZURE.SELECTOR]
- [Visão geral](sql-database-firewall-configure.md)
- [Portal do Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Banco de dados SQL do Microsoft Azure fornece um serviço de banco de dados relacional do Azure e outros aplicativos baseados na Internet. Para ajudar a proteger seus dados, firewalls impedir que todo o acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.

Para configurar seu firewall, você criar regras de firewall que especificam intervalos de endereços IP aceitáveis. Você pode criar regras de firewall nos níveis de servidor e banco de dados.

- **Regras de firewall do nível do servidor:** Essas regras habilitar acesso de clientes do servidor inteiro do SQL Azure, ou seja, todos os bancos de dados no mesmo servidor lógico. Essas regras são armazenadas no banco de dados **mestre** . Regras de firewall do nível do servidor podem ser configuradas usando o portal ou usando instruções Transact-SQL.
- **Regras de firewall do nível de banco de dados:** Essas regras habilitar clientes acessem bancos de dados individuais dentro de seu servidor do Azure SQL Database. Você pode criar essas regras para cada banco de dados e elas são armazenadas em bancos de dados individuais. (Você pode criar regras de firewall do nível de banco de dados para o banco de dados **mestre** .) Essas regras podem ser úteis para restringir o acesso a determinadas bancos de dados (seguros) no mesmo servidor lógico. Regras de firewall do nível de banco de dados só podem ser configuradas usando instruções Transact-SQL.

**Recomendação:** Recomendamos utilizar regras de firewall do nível de banco de dados sempre que possível para melhorar a segurança e para tornar seu banco de dados mais portátil. Use regras de firewall de nível de servidor para administradores e quando você tem muitos bancos de dados que tenham os mesmos requisitos de acesso e você não quer perder tempo configurando cada banco de dados individualmente.


## <a name="firewall-overview"></a>Visão geral do firewall

Inicialmente, todo o acesso de Transact-SQL ao seu servidor do SQL Azure está bloqueado pelo firewall. Para começar a usar seu servidor do SQL Azure, você deve ir para o portal do Azure e especifique uma ou mais regras de firewall de nível de servidor que habilitam o acesso ao seu servidor do SQL Azure. Use as regras de firewall para especificar quais intervalos de endereços IP da Internet são permitidos, e se aplicativos do Azure podem tentar se conectar ao seu servidor do SQL Azure.

Para seletivamente conceder acesso a apenas um banco de dados no seu servidor do SQL Azure, você deve criar uma regra de nível de banco de dados para o banco de dados necessário. Especifique um intervalo de endereços IP para a regra de firewall do banco de dados que está fora do intervalo de endereço IP especificado na regra de firewall de nível de servidor e certifique-se de que o endereço IP do cliente se encaixa no intervalo especificado na regra de nível de banco de dados.

Tentativas de Conexão da Internet e Azure devem primeiro passar pelo firewall antes de chegarem seu servidor do SQL Azure ou o banco de dados SQL, conforme mostrado no diagrama a seguir.

   ![Configuração de firewall descrevendo de diagrama.][1]

## <a name="connecting-from-the-internet"></a>Conexão da Internet

Quando um computador tenta se conectar ao seu servidor de banco de dados da Internet, o firewall primeiro verifica o endereço IP de origem da solicitação contra o conjunto completo de regras de firewall:

- Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall de nível de servidor, a conexão é concedido ao seu servidor do Azure SQL Database.

- Se o endereço IP da solicitação não estiver dentro de um dos intervalos especificados na regra de firewall de nível de servidor, as regras de firewall do nível de banco de dados são verificadas. Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall do nível de banco de dados, a conexão é concedido somente ao banco de dados que tem uma regra de nível de banco de dados correspondente.

- Se o endereço IP da solicitação não estiver nos intervalos especificados em qualquer uma das regras de firewall nível do servidor ou nível de banco de dados, a falha de solicitação de conexão.

> [AZURE.NOTE] Para acessar o banco de dados do Azure SQL do seu computador local, certifique-se de comunicação de saída na porta TCP 1433 permite que o firewall em sua rede e computador local.


## <a name="connecting-from-azure"></a>Conectando a partir do Azure

Quando um aplicativo do Azure tenta se conectar ao seu servidor de banco de dados, o firewall verifica que conexões Azure são permitidas. Um firewall definindo com inicial e final endereço igual a 0.0.0.0 indica que essas conexões são permitidas. Se a tentativa de conexão não é permitida, a solicitação não alcançar o servidor de banco de dados do Azure SQL.

> [AZURE.IMPORTANT] Esta opção configura o firewall para permitir todas as conexões do Azure conexões incluindo as assinaturas de outros clientes. Ao selecionar essa opção, verifique se seu login e permissões de usuário limitam o acesso a somente os usuários autorizados.

Você pode habilitar as conexões do Azure de duas maneiras:

- No [portal do Microsoft Azure](https://portal.azure.com/), selecione a caixa de seleção **Permitir que os serviços do azure ao servidor de acesso** ao criar um novo servidor.

- No [portal de clássico](http://go.microsoft.com/fwlink/p/?LinkID=161793), na guia **Configurar** em um servidor, na seção **Serviços permitidos** , clique em **Sim** para **Serviços do Microsoft Azure**.

## <a name="creating-the-first-server-level-firewall-rule"></a>Criar a primeira regra de firewall de nível de servidor

A primeira configuração de firewall do nível do servidor pode ser criada usando o [portal do Azure](https://portal.azure.com/) ou programaticamente usando a API REST ou Azure PowerShell. Regras de firewall subsequentes do nível do servidor podem ser criadas e gerenciadas usando esses métodos, bem como por meio de Transact-SQL. Para melhorar o desempenho, regras de firewall de nível de servidor são temporariamente armazenadas em cache no nível do banco de dados. Para atualizar o cache, consulte [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). Para obter mais informações sobre regras de firewall de nível de servidor, consulte [como: configurar um firewall de servidor do SQL Azure usando o portal de Azure](sql-database-configure-firewall-settings.md).

## <a name="creating-database-level-firewall-rules"></a>Criando regras de firewall do nível de banco de dados

Depois que você configurou o primeiro firewall de nível de servidor, você talvez queira restringir o acesso a determinadas bancos de dados. Se você especificar um intervalo de endereços IP na regra de firewall de nível de banco de dados que esteja fora do intervalo especificado na regra de firewall de nível de servidor, somente aqueles clientes que têm endereços IP do intervalo de nível de banco de dados podem acessar o banco de dados. Você pode ter no máximo 128 nível de banco de dados de regras de firewall para um banco de dados. Regras de firewall do nível de banco de dados para bancos de dados mestre e usuário podem ser criadas e gerenciadas por meio de Transact-SQL. Para obter mais informações sobre como configurar regras de firewall do nível de banco de dados, consulte [sp_set_database_firewall_rule (bancos de dados do SQL Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

## <a name="programmatically-managing-firewall-rules"></a>Gerenciando programaticamente regras de firewall

Além de portal do Azure, regras de firewall podem ser gerenciadas por programação usando Transact-SQL, API REST e Azure PowerShell. As tabelas a seguir descrevem o conjunto de comandos disponíveis para cada método.


### <a name="transact-sql"></a>Transact-SQL

| Modo de exibição de catálogo ou procedimento armazenado                                                           | Nível     | Descrição                                          |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx)                   | Servidor    | Exibe as regras de firewall de nível de servidor atual     |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx)             | Servidor    | Cria ou atualiza regras de firewall de nível de servidor       |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx)          | Servidor    | Remove as regras de firewall de nível de servidor                  |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx)        | Banco de dados  | Exibe as regras de firewall do nível de banco de dados atual   |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx)    | Banco de dados  | Cria ou atualiza as regras de firewall do nível de banco de dados |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) | Bancos de dados | Regras de firewall do nível de banco de dados de remove                |

### <a name="rest-api"></a>API REST

| API                  | Nível  | Descrição                                                      |
|----------------------|--------|------------------------------------------------------------------|
| [Regras de Firewall de lista](https://msdn.microsoft.com/library/azure/dn505715.aspx)  | Servidor | Exibe as regras de firewall de nível de servidor atual                 |
| [Criar regra de Firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx) | Servidor | Cria ou atualiza regras de firewall de nível de servidor                   |
| [Conjunto de regras de Firewall](https://msdn.microsoft.com/library/azure/dn505707.aspx)    | Servidor | Atualiza as propriedades de uma regra de firewall de nível de servidor existente |
| [Excluir regra de Firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx) | Servidor | Remove as regras de firewall de nível de servidor                              |


### <a name="azure-powershell"></a>PowerShell Azure

| Cmdlet                                                                                              | Nível  | Descrição                                                      |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)    | Servidor | Retorna as regras de firewall de nível de servidor atual                  |
| [Novo AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)    | Servidor | Cria uma nova regra de firewall de nível de servidor                         |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)    | Servidor | Atualiza as propriedades de uma regra de firewall de nível de servidor existente |
| [Remover AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | Servidor | Remove as regras de firewall de nível de servidor                              |

> [AZURE.NOTE] Pode haver o quanto atrasar um cinco minutos para as alterações nas configurações de firewall seja efetivada.

## <a name="troubleshooting-the-database-firewall"></a>Solução de problemas do firewall do banco de dados

Considere os seguintes pontos ao acesso ao serviço do Microsoft Azure SQL Database não se comportar conforme o esperado:

- **Configuração de firewall local:** Antes de seu computador pode acessar o banco de dados do SQL Azure, você talvez precise criar uma exceção de firewall em seu computador para a porta TCP 1433. Se você estiver criando conexões dentro do limite de nuvem Azure, você pode precisar abrir portas adicionais. Para obter mais informações, consulte o **V12 do banco de dados SQL: fora vs dentro** seção de [portas além 1433 para ADO.NET 4,5 e V12 de banco de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Conversão de endereço (NAT) de rede:** Devido a NAT, o endereço IP usado pelo seu computador para se conectar ao banco de dados do Azure SQL pode ser diferente do que o endereço IP exibido nas configurações de configuração de IP do computador. Para exibir o endereço IP que o computador está usando para se conectar ao Azure, logon no portal e navegue até a guia **Configurar** no servidor que hospeda seu banco de dados. Na seção **Endereços IP permitidos** , o **Endereço de IP do cliente atual** é exibido. Clique em **Adicionar** para os **Endereços IP permitidos** para permitir que este computador para acessar o servidor.

- **Alterações à lista Permitir não entraram em vigor ainda:** Pode haver quanto atrasar um cinco minutos para alterações na configuração de firewall do Azure SQL Database entrem em vigor.

- **Login não está autorizado ou uma senha incorreta foi usada:** Se um logon não tem permissões no servidor de banco de dados do Azure SQL ou a senha usada está incorreta, a conexão com o servidor de banco de dados do Azure SQL é negado. Criar uma configuração de firewall fornece apenas clientes a oportunidade de tentar se conectar ao seu servidor; cada cliente deve fornecer as credenciais de segurança necessárias. Para obter mais informações sobre como preparar logon, consulte gerenciar bancos de dados, logon e usuários no Azure SQL Database.

- **Endereço IP dinâmico:** Se você tiver uma conexão de Internet com endereçamento IP dinâmico e você está conseguindo obter através do firewall, você pode tentar uma das seguintes soluções:

 - Peça ao seu provedor de serviços de Internet (ISP) para o intervalo de endereços IP atribuído a computadores cliente que acessar o servidor de banco de dados do Azure SQL e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

 - Obtenha endereçamento IP estático, em vez para seus computadores cliente e, em seguida, adicione os endereços IP como regras de firewall.

## <a name="next-steps"></a>Próximas etapas

Como os artigos sobre como criar regras de firewall de servidor e níveis de banco de dados, consulte:

- [Configurar regras de firewall de nível de servidor de banco de dados de SQL Azure usando o portal do Azure](sql-database-configure-firewall-settings.md)
- [Configurar regras de firewall de nível de banco de dados e de servidor de banco de dados de SQL Azure usando T-SQL](sql-database-configure-firewall-settings-tsql.md)
- [Configurar regras de firewall de nível de servidor de banco de dados de SQL Azure usando o PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurar regras de firewall de nível de servidor de banco de dados do Azure SQL usando a API REST](sql-database-configure-firewall-settings-rest.md)

Para um tutorial sobre como criar um banco de dados, consulte [criar um banco de dados SQL em minutos usando o portal do Azure](sql-database-get-started.md).
Para obter ajuda sobre como se conectar a um banco de dados do SQL Azure de abrir origem ou aplicativos de terceiros, consulte [exemplos de código banco de dados SQL de início rápido de cliente](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para compreender como navegar para bancos de dados, consulte [Gerenciar a segurança de acesso e logon do banco de dados](https://msdn.microsoft.com/library/azure/ee336235.aspx).



## <a name="additional-resources"></a>Recursos adicionais

- [Proteger seu banco de dados](sql-database-security.md)
- [Centro de segurança para o mecanismo de banco de dados do SQL Server e o banco de dados do SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
