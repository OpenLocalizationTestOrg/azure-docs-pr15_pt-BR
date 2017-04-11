<properties 
    pageTitle="Portas, além 1433 para banco de dados do SQL | Microsoft Azure"
    description="Conexões de cliente do ADO.NET para V12 de banco de dados do SQL Azure, às vezes, ignoram o proxy e interagem diretamente com o banco de dados. Portas diferente 1433 ficam importantes."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="" />


<tags 
    ms.service="sql-database" 
    ms.workload="drivers"
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016"
    ms.author="annemill"/>


# <a name="ports-beyond-1433-for-adonet-45-and-sql-database-v12"></a>Portas, além 1433 para ADO.NET 4,5 e V12 de banco de dados do SQL


Este tópico descreve as alterações que V12 de banco de dados do SQL Azure traz para o comportamento de conexão de clientes que usam o ADO.NET 4,5 ou uma versão posterior.


## <a name="v11-of-sql-database-port-1433"></a>V11 do banco de dados do SQL: porta 1433


Quando o programa cliente usa 4,5 ADO.NET para se conectar e da consulta com V11 de banco de dados do SQL, a sequência interna é a seguinte:


1. ADO.NET tenta se conectar ao banco de dados SQL.

2. ADO.NET usa a porta 1433 para chamar um módulo de middleware e a middleware se conecta ao banco de dados SQL.

3. Banco de dados SQL envia sua resposta de volta ao middleware, que encaminha a resposta para o ADO.NET a porta 1433.


**Terminologia:** Podemos descrever a sequência anterior dizendo que o ADO.NET interage com o banco de dados SQL usando a *rota de proxy*. Se nenhuma middleware foram envolvida, podemos diriam que a *rota direta* foi usada.


## <a name="v12-of-sql-database-outside-vs-inside"></a>V12 do banco de dados do SQL: fora vs dentro


Para conexões com V12, solicitamos que deve se seu programa cliente é executado *fora* ou *dentro* do limite de nuvem Azure. Subseções discutam dois cenários comuns.


#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Fora:* Cliente é executado em seu computador desktop


Porta 1433 é a única porta deve estar aberta em seu computador desktop que hospeda seu aplicativo de cliente do banco de dados SQL.


#### <a name="inside-client-runs-on-azure"></a>*Dentro:* Cliente é executado no Azure


Quando seu cliente é executado dentro do limite de nuvem Azure, ele usa o que chamamos de uma *rota direta* para interagir com o servidor de banco de dados SQL. Depois que uma conexão for estabelecida, ainda mais interações entre o cliente e o banco de dados não envolvem nenhum proxy de middleware.


A sequência é da seguinte maneira:


1. ADO.NET 4,5 (ou posterior) inicia uma breve interação com a nuvem Azure e recebe um número de porta dinamicamente identificados.
 - É o número da porta dinamicamente identificados no intervalo de 11000-11999 ou 14000-14999.

2. ADO.NET se conecta ao servidor de banco de dados SQL diretamente, com nenhum middleware entre.

3. Consultas são enviadas diretamente para o banco de dados e os resultados são retornados diretamente para o cliente.


Certifique-se de que a porta intervalos de 11999 11000 e 14000-14999 na máquina cliente Azure ficam disponíveis para interações de cliente ADO.NET 4,5 com V12 de banco de dados do SQL.

- Em particular, portas no intervalo devem ser livres de quaisquer outros bloqueadores de saída.

- Em sua máquina virtual do Azure, o **Firewall do Windows com segurança avançada** controla as configurações de porta.
 - Você pode usar a [interface de usuário do firewall](http://msdn.microsoft.com/library/cc646023.aspx) para adicionar uma regra para a qual você especifica o protocolo **TCP** juntamente com um intervalo de portas com a sintaxe como **11000-11999**.


## <a name="version-clarifications"></a>Esclarecimentos de versão


Esta seção explica os identificadores que se referem a versões do produto. Ela também lista alguns pares de versões entre os produtos.


#### <a name="adonet"></a>ADO.NET


- ADO.NET 4.0 é compatível com o protocolo TDS 7.3, mas não 7.4.
- ADO.NET 4,5 e posterior suporta o protocolo TDS 7.4.


#### <a name="sql-database-v11-and-v12"></a>V12 e V11 de banco de dados SQL


As diferenças de conexão de cliente entre V11 de banco de dados do SQL e V12 são realçadas neste tópico.


*Observação:* A instrução Transact-SQL `SELECT @@version;` retorna um valor que começam com um número como '11'. ou '12.' e essas correspondem nossos nomes de versão de V11 e V12 para banco de dados do SQL.


## <a name="related-links"></a>Links relacionados


- ADO.NET 4.6 foi lançado em 20 de julho de 2015. Um anúncio de blog da equipe do .NET está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).


- ADO.NET 4,5 foi lançado em 15 de agosto de 2012. Um anúncio de blog da equipe do .NET está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
 - Uma postagem de blog sobre ADO.NET 4.5.1 está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).


- [Lista de versões do protocolo TDS](http://www.freetds.org/userguide/tdshistory.htm)


- [Visão geral de desenvolvimento de banco de dados do SQL](sql-database-develop-overview.md)


- [Firewall de banco de dados do SQL Azure](sql-database-firewall-configure.md)


- [Como: definir configurações de firewall no banco de dados SQL](sql-database-configure-firewall-settings.md)

