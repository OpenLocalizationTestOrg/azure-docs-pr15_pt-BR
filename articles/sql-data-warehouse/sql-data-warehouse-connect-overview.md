<properties
   pageTitle="Conectar ao depósito de dados do SQL Azure | Microsoft Azure"
   description="Como encontrar o servidor de nome e uma conexão de cadeia de caracteres para seu depósito de dados do SQL Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/26/2016"
   ms.author="sonyama;barbkess"/>

# <a name="connect-to-azure-sql-data-warehouse"></a>Conectar ao depósito de dados do SQL Azure

Este artigo ajuda você a ficar conectado ao SQL Data Warehouse pela primeira vez.

## <a name="find-your-server-name"></a>Localizar o nome do seu servidor

A primeira etapa para se conectar ao SQL Data Warehouse é saber como encontrar o nome do servidor.  Por exemplo, o nome do servidor no exemplo a seguir é sample.database.windows.net. Para localizar o nome do servidor totalmente qualificado:

1. Acesse o [portal do Azure][].
2. Clique em **bancos de dados SQL** 
3. Clique no banco de dados que você deseja se conectar.
4. Localize o nome completo do servidor.

    ![Nome completo do servidor][1]

## <a name="supported-drivers-and-connection-strings"></a>Drivers suportados e cadeias de caracteres de conexão

Depósito de dados SQL Azure suporta [ADO.NET][], [ODBC][], [PHP][]e [JDBC][]. Clique em um dos drivers anteriores para localizar a versão mais recente e a documentação. Para gerar automaticamente a cadeia de conexão para o driver que você está usando a partir do portal Azure, você pode clicar em **Mostrar cadeias de caracteres de conexão de banco de dados** do exemplo anterior.  A seguir também é alguns exemplos de aparência de uma cadeia de conexão para cada driver.

> [AZURE.NOTE] Considere a possibilidade de definir o tempo de limite de conexão para 300 segundos para permitir que sua conexão sobreviver curtos períodos de indisponibilidade.

### <a name="adonet-connection-string-example"></a>Exemplo de cadeia de conexão de ADO.NET

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Exemplo de cadeia de conexão ODBC

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Exemplo de cadeia de conexão PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Exemplo de cadeia de conexão JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Configurações de Conexão

SQL Data Warehouse padroniza algumas configurações durante a criação de objeto e de conexão. Essas configurações não podem ser substituídas e incluem:

| Configuração de banco de dados       | Valor                        |
| :--------------------- | :--------------------------- |
| [ANSI_NULLS][]         | ATIVADO                           |
| [QUOTED_IDENTIFIERS][] | ATIVADO                           |
| [FORMATO_DATA][]         | MDA                          |
| [DATEFIRST][]          | 7                            |

## <a name="next-steps"></a>Próximas etapas

Para conectar e da consulta com o Visual Studio, consulte [consulta com o Visual Studio][]. Para saber mais sobre opções de autenticação, consulte [autenticação depósito de dados do SQL Azure][].

<!--Articles-->
[Consulta com o Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Autenticação depósito de dados do SQL Azure]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[FORMATO_DATA]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Portal do Azure]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png


