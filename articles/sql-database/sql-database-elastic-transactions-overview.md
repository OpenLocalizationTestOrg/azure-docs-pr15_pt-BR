<properties
   pageTitle="Transações distribuídas em bancos de dados de nuvem"
   description="Visão geral das transações de banco de dados elástica com banco de dados do SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="torsteng"
   manager="jhubbard"
   editor="torsteng"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="sql-database"
   ms.date="05/27/2016"
   ms.author="torsteng"/>

# <a name="distributed-transactions-across-cloud-databases"></a>Transações distribuídas em bancos de dados de nuvem

Transações de banco de dados elástica para banco de dados do Azure SQL (SQL DB) permite que você executar transações que se estendem por vários bancos de dados no banco de dados do SQL. Transações de banco de dados elástica para SQL DB estão disponíveis para aplicativos .NET usando ADO .NET e integram a experiência de programação familiar usando as classes [Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) . Para acessar a biblioteca, consulte [.NET Framework 4.6.1 (instalador da Web)](https://www.microsoft.com/download/details.aspx?id=49981).

No local, tal cenário geralmente necessários executando o Microsoft Distributed Transaction Coordinator (MSDTC). Como MSDTC não está disponível para o aplicativo de plataforma como serviço no Azure, a capacidade de coordenar transações distribuídas agora foi diretamente integrada ao banco de dados do SQL. Aplicativos podem se conectar a qualquer banco de dados SQL para iniciar transações distribuídas e um banco de dados será transparente coordenar da transação distribuída, conforme mostrado na figura a seguir. 

  ![Transações distribuídas com o banco de dados do SQL Azure usando transações de banco de dados elástica ][1]

## <a name="common-scenarios"></a>Cenários comuns

Transações de banco de dados elástica para SQL DB permitem que aplicativos façam alterações atômicas dados armazenados em vários bancos de dados diferentes do SQL. A visualização se concentra em experiências de desenvolvimento do lado do cliente no c# e .NET. Uma experiência de servidor usando o T-SQL está planejada para posteriormente.  
Transações de banco de dados elástica destinos cenários a seguir:

* Aplicativos de vários bancos de dados no Azure: com esse cenário, dados verticalmente são particionados em vários bancos de dados no banco de dados do SQL, de forma que os diferentes tipos de dados residem em bancos de dados diferentes. Algumas operações exigem alterações aos dados que são mantidos em dois ou mais bancos de dados. O aplicativo usa as transações de banco de dados elástica para coordenar as alterações em bancos de dados e garantir a atomicidade.

* Aplicativos de banco de dados sharded no Azure: neste cenário, a camada de dados usa a [biblioteca de cliente do banco de dados elástica](sql-database-elastic-database-client-library.md) ou autofragmentação para partição horizontalmente os dados em vários bancos de dados no banco de dados do SQL. Um caso de uso perceptíveis é a necessidade de executar atômicas alterações para um aplicativo de vários locatário sharded quando alterações abrangem locatários. Pense por exemplo uma transferência de um locatário para outra, ambas as residente em bancos de dados diferentes. Um segundo caso é refinada fragmentação para acomodar as necessidades de capacidade um locatário grandes que por sua vez geralmente significa que algumas operações atômicas precisa Alongar para vários bancos de dados usados para o mesmo locatário. Um terceiro caso é atômicas atualizações fazer referência aos dados que são replicados em bancos de dados. Operações atômicas, realizadas, essas linhas agora podem ser coordenadas entre vários bancos de dados usando a visualização.
Transações de banco de dados elástica usam confirmação de duas fases para garantir a atomicidade transação em bancos de dados. É uma boa opção para as transações que envolvem menos de 100 bancos de dados por vez em uma única transação. Esses limites não são aplicados, mas um deve esperar desempenho e taxas de sucesso para as transações de banco de dados elástica sofrer ao exceder esses limites.


## <a name="installation-and-migration"></a>Instalação e migração

Os recursos para as transações de banco de dados elástica no banco de dados do SQL são fornecidos através de atualizações as bibliotecas .NET System.Data.dll e System.Transactions.dll. As DLLs assegure-se de que confirmação de duas fases onde necessário para garantir a atomicidade. Para começar a desenvolver aplicativos usando transações de banco de dados elástica, instale o [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) ou uma versão posterior. Quando você estiver em uma versão anterior do .NET framework, transações falhará promover para uma transação distribuída e uma exceção será gerada.

Após a instalação, você pode usar a transação distribuída APIs System. Transactions com conexões com o banco de dados do SQL. Se você tiver aplicativos de MSDTC existentes usando essas APIs, simplesmente reconstruir seus aplicativos existentes para .NET 4.6 depois de instalar o 4.6.1 Framework. Se seus projetos alvo .NET 4.6, eles automaticamente usarão as DLLs atualizadas da nova versão Framework e transação distribuída chamadas de API em combinação com conexões para SQL DB agora será bem-sucedida.

Lembre-se de que as transações de banco de dados elástica não exigem instalação MSDTC. Em vez disso, as transações de banco de dados elástica diretamente são gerenciadas por e dentro de banco de dados do SQL. Isso simplifica significativamente cenários de nuvem como uma implantação do MSDTC não é necessária usar transações distribuídas com o banco de dados do SQL. Seção 4 explica mais detalhadamente como implantar transações de banco de dados elástica e do .NET framework necessário junto com seus aplicativos de nuvem para Azure.

## <a name="development-experience"></a>Experiência de desenvolvimento

### <a name="multi-database-applications"></a>Aplicativos de vários bancos de dados

O código de exemplo a seguir usa a experiência de programação familiar com .NET System. Transactions. A classe TransactionScope estabelece uma transação de ambiente no .NET. (Uma "transação de ambiente" é aquele que reside no thread atual). Todas as conexões abertas dentro TransactionScope participarem da transação. Se participarem de bancos de dados diferentes, a transação é automaticamente elevada a uma transação distribuída. O resultado da transação é controlado definindo o escopo para concluir para indicar uma confirmação.

    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }

### <a name="sharded-database-applications"></a>Aplicativos de banco de dados sharded
 
Transações de banco de dados elástica para SQL DB também suportam coordenar transações distribuídas onde você usar o método OpenConnectionForKey da biblioteca cliente elástica banco de dados para abrir conexões para um dados escalado check-out camada. Considere a possibilidade de casos em que você precisa garantir a consistência transações para alterações em vários valores de chave de fragmentação diferentes. Conexões para os fragmentos que hospeda os valores da chave fragmentação diferentes são orientados usando OpenConnectionForKey. Em geral, as conexões podem ser diferentes fragmentos que garantir garantias transações requer uma transação distribuída. O exemplo de código a seguir ilustra essa abordagem. Ele pressupõe que uma variável chamada shardmap é usada para representar um mapa fragmentar da biblioteca de cliente do banco de dados elástica:

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }
        
        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>Instalação do .NET para serviços de nuvem do Azure

Azure fornece diversas ofertas para aplicativos de .NET de host. Uma comparação das diferentes ofertas está disponível no [serviço de aplicativo do Azure, serviços de nuvem e comparação de máquinas virtuais](../app-service-web/choose-web-site-cloud-service-vm.md). Se o sistema operacional convidado da oferta for menor que .NET 4.6.1 necessários para as transações elástica, você precisa atualizar o sistema operacional convidado para 4.6.1. 

Para serviços de aplicativo do Azure, atualizações para o sistema operacional convidado atualmente não são suportadas. Para o máquinas virtuais do Azure, simplesmente login a máquina virtual e execute o instalador do .NET Framework mais recente. Para serviços de nuvem do Azure, você precisa incluir a instalação de uma versão mais recente do .NET para as tarefas de inicialização de sua implantação. Os conceitos e as etapas documentadas no [.NET instalar em uma função de serviço de nuvem](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Observe que o instalador do .NET 4.6.1 pode exigir mais armazenamento temporário durante o processo de inicialização em serviços de nuvem Azure que o instalador do .NET 4.6. Para garantir uma instalação bem-sucedida, você precisa aumentar o armazenamento temporário de seu serviço de nuvem Azure no seu arquivo de ServiceDefinition.csdef na seção LocalResources e as configurações de ambiente de sua tarefa de inicialização, conforme mostrado no exemplo a seguir:

    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
    
## <a name="transactions-across-multiple-servers"></a>Transações em vários servidores

Transações de banco de dados elástica são suportadas em diferentes servidores lógicos Azure SQL Database. Quando as transações cross limites de servidor lógico, os servidores participantes primeiro precisam ser inseridos em uma relação de comunicação comum. Depois que a relação de comunicação tiver sido estabelecida, qualquer banco de dados em qualquer um dos dois servidores pode participar elástica transações com bancos de dados do outro servidor. Com transações que se estendem por mais de dois servidores lógicos, uma relação de comunicação precisa estar no lugar para qualquer par de servidores lógicos.

Use os seguintes cmdlets do PowerShell para gerenciar relações de comunicação entre servidores para as transações de banco de dados elástica:

* **Novo AzureRmSqlServerCommunicationLink**: Use esse cmdlet para criar uma nova relação de comunicação entre dois servidores lógicos em DB do SQL Azure. A relação é simétrico que significa que ambos os servidores podem iniciar transações com o outro servidor.
* **Get-AzureRmSqlServerCommunicationLink**: Use esse cmdlet para recuperar os relacionamentos de comunicação existentes e suas propriedades.
* **Remover AzureRmSqlServerCommunicationLink**: Use esse cmdlet para remover uma relação de comunicação existente. 


## <a name="monitoring-transaction-status"></a>Monitorar o status da transação

Use exibições de gerenciamento dinâmico (DMVs) no banco de dados SQL para monitorar o status e o andamento das suas transações de banco de dados elástica em andamento. Todas as DMVs relacionadas às transações são relevantes para transações distribuídas no banco de dados do SQL. Você pode encontrar a lista correspondente de DMVs aqui: [relacionados exibições de gerenciamento dinâmico transação e funções (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).
 
Esses DMVs são particularmente úteis:

* **sys.dm\_transação\_ativos\_transações**: lista transações ativas no momento e seus status. A coluna UOW (unidade de trabalho) pode identificar as transações de diferentes filho que pertencem à mesma transação distribuída. Todas as transações na mesma transação distribuída conter o mesmo valor UOW. Consulte a [documentação do departamento de trânsito](https://msdn.microsoft.com/library/ms174302.aspx) para obter mais detalhes.
* **sys.dm\_transação\_banco de dados\_transações**: fornece informações adicionais sobre transações, como o posicionamento da transação no log. Consulte a [documentação do departamento de trânsito](https://msdn.microsoft.com/library/ms186957.aspx) para obter mais detalhes.
* **sys.dm\_transação\_bloqueios**: fornece informações sobre os bloqueios que são mantidos atualmente pela transações em andamento. Consulte a [documentação do departamento de trânsito](https://msdn.microsoft.com/library/ms190345.aspx) para obter mais detalhes.

## <a name="limitations"></a>Limitações 

As seguintes limitações atualmente se aplicam a transações de banco de dados elástica no banco de dados do SQL:

* Há suporte apenas transações em bancos de dados no banco de dados do SQL. Outros [X / Open XA](https://en.wikipedia.org/wiki/X/Open_XA) provedores de recursos e bancos de dados fora do banco de dados do SQL não podem participar em transações elástica banco de dados. Isso significa que as transações de banco de dados elástica não podem alongar para no local do SQL Server e bancos de dados do SQL Azure. Para transações distribuídas no local, continue a usar MSDTC. 
* Apenas transações cliente coordenado de um aplicativo .NET são suportadas. Suporte do servidor para T-SQL como começar transação DISTRIBUÍDA é planejada, mas ainda não disponível. 
* Somente bancos de dados no Azure SQL DB V12 são suportados.
* Transações nos serviços do WCF não são suportadas. Por exemplo, você tem um método de serviço do WCF que executa uma transação. Colocar a chamada dentro de um escopo de transação falhará como um [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="additional-resources"></a>Recursos adicionais

Ainda não usando recursos elástica banco de dados de aplicativos do Azure? Confira nosso [Mapa da documentação](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/). Para perguntas, por favor, comunique-se no [Fórum de banco de dados SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e para solicitações de recurso, adicione-os para o [Fórum de comentários do banco de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png



