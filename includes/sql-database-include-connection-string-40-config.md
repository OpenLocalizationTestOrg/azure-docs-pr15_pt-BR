
<!--
includes/sql-database-include-connection-string-40-config.md

Latest Freshness check:  2015-09-04 , GeneMi.

## Connection string
-->


### <a name="example-config-file-for-connection-string-security"></a>Arquivo de configuração de exemplo para segurança de cadeia de conexão


É unsound para colocar a cadeia de conexão como literais no seu código c#. É melhor colocar a cadeia de conexão em um arquivo de configuração. Lá você pode editar a cadeia de caracteres qualquer momento, sem a necessidade de recompilar.

Vamos supor que seu programa c# compilado é denominado **ConsoleApplication1.exe**, e que esta .exe reside em um **bin\debug\* * diretório.

Neste exemplo, a maioria das partes de sua cadeia de conexão são armazenados em um arquivo de configuração chamado exatamente **ConsoleApplication1.exe.config**. Este arquivo de configuração também deve reside em **bin\debug\**.

No XML o seguir do arquivo de configuração, você vê uma cadeia de conexão chamada **ConnectionString4NoUserIDNoPassword**. O código c# procura essa cadeia de caracteres.

Você deve editar nomes reais nos espaços reservados:

- {your_serverName_here}
- {your_databaseName_here}



        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
        
            <connectionStrings>
                <clear />
                <add name="ConnectionString4NoUserIDNoPassword"
                providerName="System.Data.ProviderName"
        
                connectionString=
                "Server=tcp:{your_serverName_here}.database.windows.net,1433;
                Database={your_databaseName_here};
                Connection Timeout=30;
                Encrypt=True;
                TrustServerCertificate=False;" />
            </connectionStrings>
        </configuration>



Para esta ilustração escolhemos omitir dois parâmetros:

- ID de usuário = {your_userName_here};
- Senha = {your_password_here};


Você pode incluí-los, mas, às vezes, é melhor ter seu programa Obtenha os valores de entrada de teclado pelo usuário. Isso depende.



<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
