<properties
    pageTitle="Corrigir um erro de conexão de SQL, o erro temporário | Microsoft Azure"
    description="Saiba como solucionar problemas, diagnosticar e evitar que um erro de conexão de SQL ou temporárias no Azure SQL Database. "
    keywords="conexão de SQL, cadeia de conexão, problemas de conectividade, erro temporário, erro de conexão"
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="daleche"/>


# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Solucionar problemas, diagnosticar e evitar erros de conexão de SQL e temporárias do banco de dados SQL

Este artigo descreve como impedir, solucionar problemas, diagnosticar e reduzir erros de conexão e erros temporárias que seu aplicativo cliente encontra quando ele interage com o Azure SQL Database. Saiba como configurar a lógica de repetição, construir a cadeia de conexão e ajuste outras configurações de conexão.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Temporárias erros (falhas temporárias)

Um erro temporário - também, falhas temporárias - tem uma causa subjacente que resolverá breve em si. Uma causa eventual dos erros temporárias ocorre quando o sistema Azure rapidamente desloca recursos de hardware para melhor balanceamento de carga diversas cargas de trabalho. Maioria desses eventos de reconfiguração geralmente concluída em menos de 60 segundos. Durante esse período de tempo de reconfiguração, você pode ter problemas de conectividade de banco de dados do Azure SQL. Aplicativos conectando ao banco de dados do SQL Azure devem ser criados para esperar esses erros temporárias, lidará com eles implementando lógica de repetição no código, em vez de faceamento-los aos usuários como erros do aplicativo.

Se o programa cliente está usando o ADO.NET, seu programa será informado sobre o erro temporário pelo lançamento de uma **SqlException**. A propriedade de **número** pode ser comparada com a lista de erros temporárias próximo à parte superior do tópico: [códigos de erro SQL para aplicativos de cliente do banco de dados SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Conexão versus comando

Você vai repetir a conexão de SQL ou selecioná-lo novamente, dependendo do seguinte:

* **Ocorre um erro temporário durante uma tentativa de conexão**: A conexão deve ser repetida após atrasar por vários segundos.

* **Ocorre um erro temporário durante um comando de consulta do SQL**: O comando não deve ser repetido imediatamente. Em vez disso, após um atraso, a conexão deve ser recentemente estabelecida. Em seguida, o comando pode ser repetido.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

### <a name="retry-logic-for-transient-errors"></a>Uma lógica de erros temporárias de repetição


Programas de cliente que ocasionalmente encontrar um erro temporário são mais robustos quando elas contêm lógica de repetição.


Quando seu programa se comunica com o banco de dados do SQL Azure através de um 3º middleware de festa, consulte com o fornecedor se a middleware contém lógica de repetição de erros temporárias.

<a id="principles-for-retry" name="principles-for-retry"></a>

#### <a name="principles-for-retry"></a>Princípios de repetição


- Uma tentativa de abrir uma conexão deve ser repetida se o erro for temporário.


- Uma instrução SQL SELECT que falhará com um erro temporário não deve ser repetida diretamente.
 - Em vez disso, estabeleça uma nova conexão e tente selecionar.


- Quando uma instrução UPDATE SQL falha com um erro temporário, uma nova conexão deve ser estabelecida antes que a atualização será repetida.
 - A lógica de repetição deve garantir que a transação de banco de dados inteiro concluída, ou que a transação inteira é revertida.


#### <a name="other-considerations-for-retry"></a>Outras considerações de repetição


- Um programa em lotes que é iniciado automaticamente após o horário de trabalho e qual será concluída antes de manhã, pode pagar para pacientes muito com longos intervalos de tempo entre seus tentativas de repetição.


- Um programa de interface do usuário deve conta para a tendência humana a desistir após muito tempo de espera.
 - No entanto, a solução não deve ser repetir a cada poucos segundos, pois essa política pode sobrecarregar o sistema com solicitações.


#### <a name="interval-increase-between-retries"></a>Aumento de intervalo entre tentativas



Recomendamos que você espera por 5 segundos antes de sua primeira tentativa. Repetindo após um atraso menor do que os riscos de 5 segundos sobrecarregar o serviço de nuvem. Para cada tentativa subsequente o atraso deve crescimento exponencial, até no máximo 60 segundos.

Uma discussão do *bloqueio de período* para clientes que usam o ADO.NET está disponível no [Pool de Conexão do SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Convém também definir um número máximo de tentativas antes do programa auto termina.


#### <a name="code-samples-with-retry-logic"></a>Exemplos de código com lógica de repetição


Exemplos de código com lógica de repetição, em uma variedade de linguagens de programação, estão disponíveis em:

- [Bibliotecas de Conexão de banco de dados SQL e SQL Server](sql-database-libraries.md)


<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

#### <a name="test-your-retry-logic"></a>Testar sua lógica de repetição


Para testar sua lógica de repetição, você deve simular ou causar um erro que pode ser corrigido enquanto seu programa ainda está em execução.


##### <a name="test-by-disconnecting-from-the-network"></a>Testar desconectando da rede


Uma maneira de testar sua lógica de repetição é desconectar seu computador cliente da rede, enquanto o programa está em execução. O erro será:

- **SqlException.Number** = 11001
- Mensagem: "host é conhecido"


Como parte da primeira tentativa de repetição, seu programa pode corrigir o erro de ortografia e, em seguida, tente se conectar.


Para fazer isso prático, você desconecte o computador da rede antes de iniciar o programa. Em seguida, seu programa reconhece um parâmetro de tempo de execução que faz com que o programa:

1. Adicione temporariamente 11001 à sua lista de erros a serem consideradas como transitório.
2. Tente sua primeira conexão como de costume.
3. Depois que o erro é detectado, remova 11001 da lista.
4. Exiba uma mensagem informando ao usuário para conectar o computador na rede.
 - Pause ainda mais execução usando o método de **console. ReadLine** ou uma caixa de diálogo com um botão Okey. O usuário pressiona a tecla Enter depois que o computador conectado à rede.
5. Tente novamente se conectar, esperando sucesso.


##### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Testar o nome do banco de dados de erros de ortografia ao conectar


Seu programa pode ortografia intencionalmente o nome de usuário antes da primeira tentativa de conexão. O erro será:

- **SqlException.Number** = 18456
- Mensagem: "Falha de logon do usuário 'WRONG_MyUserName'."


Como parte da primeira tentativa de repetição, seu programa pode corrigir o erro de ortografia e, em seguida, tente se conectar.


Para fazer isso prático, seu programa pode reconhecer um parâmetro de tempo de execução que faz com que o programa:

1. Adicione temporariamente 18456 à sua lista de erros a serem consideradas como transitório.
2. Adicione intencionalmente 'WRONG_' para o nome de usuário.
3. Depois que o erro é detectado, remova 18456 da lista.
4. Remova o nome de usuário 'WRONG_'.
5. Tente novamente se conectar, esperando sucesso.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

### <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parâmetros de .NET SqlConnection para tentativa de conexão


Se o programa cliente se conecta ao banco de dados do Azure SQL usando o .NET Framework **SqlConnection**de classe, você deve usar o .NET 4.6.1 ou posterior para que você possa aproveitar seu recurso de repetição de conexão. Detalhes do recurso são [aqui](http://go.microsoft.com/fwlink/?linkid=393996).


<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


Quando você cria a [cadeia de conexão](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) para o objeto **SqlConnection** , você deve coordenar os valores entre os parâmetros a seguir:

- ConnectRetryCount &nbsp; &nbsp; *(o padrão é 1. Intervalo é de 0 a 255.)*
- ConnectRetryInterval &nbsp; &nbsp; *(o padrão é 1 segundo. O intervalo é 1 a 60.)*
- Tempo limite de Conexão &nbsp; &nbsp; *(o padrão é 15 segundos. Intervalo é 0 e 2147483647)*


Especificamente, os valores escolhidos devem fazer o seguinte verdadeiro de igualdade:

- Tempo limite de Conexão = ConnectRetryCount * ConnectionRetryInterval

Por exemplo, se a contagem = 3 e intervalo = 10 segundos, um tempo limite de somente 29 segundos prefere não bastante dar o sistema bastante tempo para sua repetição 3º e final no conectando: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Conexão versus comando


Os parâmetros **ConnectRetryCount** e **ConnectRetryInterval** deixe seu objeto **SqlConnection** repetir a operação de conectar sem informando ou incomodar seu programa, como retornar o controle ao seu programa. As tentativas podem ocorrer nas seguintes situações:

- chamada do método mySqlConnection.Open
- chamada do método mySqlConnection.Execute

Há um recurso. Se ocorrer um erro de temporário enquanto sua *consulta* é executado, seu objeto **SqlConnection** não repetir a operação de conectar e ele certamente não repetir sua consulta. No entanto, **SqlConnection** rapidamente verifica a conexão antes de enviar sua consulta para execução. Se a verificação rápida detectar um problema de conexão, **SqlConnection** tentativas a operação de conexão. Se a tentativa for bem-sucedida, você consulta é enviada para execução.


#### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount deve ser combinado com lógica de repetição de aplicativo?

Digamos que seu aplicativo tem lógica de repetição personalizado robusta. Ele pode repetir a operação de conectar 4 vezes. Se você adicionar **ConnectRetryInterval** e **ConnectRetryCount** = 3 para sua cadeia de conexão, você irá aumentar a contagem de repetição a 4 * 3 = 12 tentativas. Você não pode intenção um alto número de tentativas.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-azure-sql-database"></a>Conexões de banco de dados do SQL Azure

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Conexão: Cadeia de Conexão


A cadeia de conexão necessária para conectar ao banco de dados do SQL Azure é um pouco diferente da cadeia de conexão com o Microsoft SQL Server. Você pode copiar a cadeia de conexão do banco de dados a partir do [Portal do Azure](https://portal.azure.com/).


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Conexão: Endereço IP


Você deve configurar o servidor de banco de dados SQL para aceitar comunicação do endereço IP do computador que hospeda seu programa cliente. Você pode fazer isso editando as configurações de firewall por meio do [Portal do Azure](https://portal.azure.com/).


Se você esquecer de configurar o endereço IP, seu programa falhará com uma mensagem de erro prático afirmando o endereço IP necessário.


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


Para obter mais informações, consulte: [como: configurar as configurações de firewall no banco de dados do SQL](sql-database-configure-firewall-settings.md)


<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Conexão: portas


Normalmente você só precisa Certifique-se de que a porta 1433 está aberta para comunicação de saída, no computador que hospeda a você programa cliente.


Por exemplo, quando o programa cliente estiver hospedado em um computador com Windows, o Firewall do Windows no host permite que você abrir a porta 1433:


1. Abra o painel de controle
2. &gt;Todos os itens de painel de controle
3. &gt;Firewall do Windows
4. &gt;Configurações avançadas
5. &gt;Regras de saída
6. &gt;Ações
7. &gt;Nova regra


Se o programa cliente estiver hospedado em uma máquina virtual Azure (máquina virtual), você deve ler:<br/>[Portas além 1433 para ADO.NET 4,5 e V12 de banco de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


Para obter informações detalhadas sobre cofiguration de endereços IP e portas, consulte: [firewall do Azure SQL Database](sql-database-firewall-configure.md)


<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Conexão: ADO.NET 4.6.1


Se seu programa usa classes ADO.NET como **SqlConnection** para se conectar ao banco de dados do SQL Azure, é recomendável que você use .NET Framework versão 4.6.1 ou superior.


ADO.NET 4.6.1:

- Para o banco de dados do SQL Azure, há maior confiabilidade quando você abre uma conexão usando o método de **SqlConnection. Open** . O método **Abrir** agora incorpora melhores mecanismos de repetição de esforço em resposta a falhas temporárias, para determinados erros dentro do período de tempo limite de Conexão.
- Suporta o pool de conexão. Isso inclui uma verificação eficiente que o objeto de conexão que ele oferece a seu programa está funcionando.



Quando você usa um objeto de conexão de um pool de conexão, é recomendável que o seu programa temporariamente fechar a conexão quando não imediatamente usá-lo. Abrindo novamente uma conexão não é cara é a maneira de criar uma nova conexão.


Se você estiver usando o ADO.NET 4.0 ou anterior, recomendamos que você atualize para o ADO.NET mais recente.

- A partir de novembro de 2015, você pode [Baixar ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).


<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnósticos

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnóstico: Testar se utilitários podem se conectar


Se seu programa está falhando para se conectar ao banco de dados do SQL Azure, uma opção de diagnóstico é tenta se conectar com um programa utilitário. Ideal seria o utilitário faria se conectar usando a mesma biblioteca que seu programa usa.


Em qualquer computador com Windows, você pode tentar esses utilitários:

- SQL Server Management Studio (ssms.exe), que conecta-se usando o ADO.NET.
- Sqlcmd.exe, que conecta-se usando [ODBC](http://msdn.microsoft.com/library/jj730308.aspx).


Uma vez conectado, teste se uma consulta de SQL SELECT curta funciona.


<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnóstico: Verifique as portas abertas


Suponha que você suspeita que as tentativas de conexão estão falhando devido a problemas de porta. Em seu computador, você pode executar um utilitário que relata sobre as configurações de porta.


No Linux utilitários a seguir podem ser úteis:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`
 - (Alterar o valor de exemplo para seu endereço IP.)


No Windows a [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) utilitário pode ser útil. Aqui está uma execução de exemplo que consultado a situação de porta em um servidor de banco de dados de SQL Azure, e qual foi executado em um computador laptop:


```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnóstico: Log os erros


Às vezes, é melhor para um problema intermitente é diagnosticar pela detecção de um padrão de geral sobre dias ou semanas.


Seu cliente pode ajudar um diagnóstico efetuando todos os erros que ele encontra. Você talvez consiga correlação as entradas de log com dados de erro que registra-próprio internamente do Azure SQL Database.


Enterprise Library 6 (EntLib60) oferece classes .NET gerenciados para ajudá-lo com log:

- [5 - tão fácil quanto caiam desativar um Log: usando o bloco de aplicativo de log](http://msdn.microsoft.com/library/dn440731.aspx)


<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnóstico: Examine os logs de sistema de erros


Aqui estão algumas instruções SELECT de Transact-SQL que logs de consulta de erro e outras informações.


| Consulta de log | Descrição |
| :-- | :-- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` | O modo de exibição de [sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) oferece informações sobre eventos individuais, incluindo alguns que podem causar erros temporárias ou falhas de conectividade.<br/><br/>Ideal seria você pode correlacionar os valores **hora_de_início** ou **end_time** com informações sobre quando o seu programa cliente teve problemas.<br/><br/>**Dica:** Você deve se conectar ao banco de dados **mestre** executá-lo. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` | O modo de exibição de [sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) oferece agregadas contagens de tipos de eventos, diagnóstico adicional.<br/><br/>**Dica:** Você deve se conectar ao banco de dados **mestre** executá-lo. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnóstico: Pesquisar eventos de problema no log de banco de dados SQL


Você pode procurar por entradas sobre eventos de problema no log do Azure SQL Database. Experimente a seguinte instrução Transact-SQL SELECT no banco de dados **mestre** :


```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Algumas linhas retornadas de sys.fn_xe_telemetry_blob_target_read_file


Próximo é qual pode ser a aparência de uma linha retornada. Os valores nulos mostrados geralmente não são nulos em outras linhas.


```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Biblioteca de Enterprise 6


Enterprise Library 6 (EntLib60) é uma estrutura de classes .NET que ajuda você a implementar robustos clientes dos serviços de nuvem, um dos quais é o serviço do Azure SQL Database. Você pode localizar tópicos dedicados a cada área em que pode ajudar a EntLib60 visitando primeiro:

- [Biblioteca de Enterprise 6 – abril de 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)


Lógica de repetição para lidar com erros temporárias é uma área em que EntLib60 podem ajudar:

- [4 - perseverance, segredo de todos os Triumphs: usando o bloco de aplicativo de manipulação de falhas temporárias](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)


> [AZURE.NOTE] O código-fonte para EntLib60 está disponível para o público [Baixar](http://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft não tem planos para fazer mais atualizações do recurso ou manutenção EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Classes de EntLib60 para erros de temporárias e tente novamente


As seguintes classes EntLib60 são particularmente úteis para lógica de repetição. Todos esses são no ou posterior sob, o namespace **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

*No namespace* *Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling* *:*

- **RetryPolicy** classe
 - Método **ExecuteAction**


- **ExponentialBackoff** classe


- **SqlDatabaseTransientErrorDetectionStrategy** classe


- **ReliableSqlConnection** classe
 - Método de **ExecuteCommand**


No namespace **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- **AlwaysTransientErrorDetectionStrategy** classe

- **NeverTransientErrorDetectionStrategy** classe


Aqui estão links para informações sobre EntLib60:

- Livre [Download do livro: guia do desenvolvedor a biblioteca do Microsoft Enterprise, 2ª edição](http://www.microsoft.com/download/details.aspx?id=41145)

- Práticas recomendadas: [Repetir orientação geral](../best-practices-retry-general.md) tem uma excelente discussão detalhada sobre lógica de repetição.

- Download do NuGet da [Enterprise Library - bloco de aplicativo de tratamento de falhas temporárias 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: O bloco de log


- O bloco de log é uma solução altamente flexível e configurável que permite que você:
 - Criar e armazenar mensagens de log em uma ampla variedade de locais.
 - Categorize e filtrar mensagens.
 - Colete informações contextuais que são útil para depuração e rastreamento, bem como para auditoria e gerais log requisitos.


- O bloco de log elimina a funcionalidade de log de destino log para que o código do aplicativo seja consistente, independentemente do local e o tipo de armazenamento de log de destino.


Para obter detalhes, consulte: [5 - como fácil como caiam desativar um Log: usando o bloco de aplicativo log](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Código-fonte EntLib60 IsTransient método


Em seguida, da classe **SqlDatabaseTransientErrorDetectionStrategy** , é o código-fonte c# para o método **IsTransient** . O código-fonte explica quais erros foram considerados temporárias e pena repetir, a partir de abril de 2013.

Várias linhas de **//comment** foram removidas desta cópia para enfatizar a legibilidade.


```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## <a name="next-steps"></a>Próximas etapas

- Para outros problemas de conexão de banco de dados do Azure SQL comuns de solução de problemas, visite [Solucionar problemas de conexão ao banco de dados do SQL Azure](sql-database-troubleshoot-common-connection-issues.md).

- [SQL Server Conexão pool (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)


- [*Repetindo* é que um Apache 2.0 licenciado biblioteca tentando geral, escrita em **Python**, para simplificar a tarefa da adição de comportamento de repetir a quase qualquer coisa.](https://pypi.python.org/pypi/retrying)
