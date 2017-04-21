
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### <a name="compare-the-connection-string"></a>Comparar a cadeia de conexão


A tabela a seguir compara as cadeias de caracteres de conexão que o programa c# precisa para se conectar à sua no SQL Server local versus seu banco de dados do SQL Azure na nuvem. As diferenças estão em negrito.


| Cadeia de Conexão para<br/>Banco de dados do SQL Azure | Cadeia de Conexão para<br/>Microsoft SQL Server |
| :-- | :-- |
| Servidor =**tcp:**{your_serverName_here}**. database.windows.net,1433**;<br/>ID de usuário = {your_loginName_here}**@{your_serverName_here}**;<br/>Senha = {your_password_here};<br/>**Banco de dados = {your_databaseName_here};**<br/>**Tempo limite de Conexão = 30**;<br/>**Criptografar = True**;<br/>**TrustServerCertificate = False**; | Servidor = {your_serverName_here};<br/>ID de usuário = {your_loginName_here};<br/>Senha = {your_password_here}; |


O **banco de dados =** é opcional para o SQL Server, mas é necessária para o banco de dados SQL.


[Propriedades de SqlConnectionStringBuilder ADO .NET](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) - discute todos os parâmetros em detalhes.


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
