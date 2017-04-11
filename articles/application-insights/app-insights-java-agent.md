<properties 
    pageTitle="Monitorar dependências, exceções e tempos de execução nos aplicativos web do Java" 
    description="Estendido o monitoramento do seu site de Java com ideias de aplicativo" 
    services="application-insights" 
    documentationCenter="java"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="monitor-dependencies-exceptions-and-execution-times-in-java-web-apps"></a>Monitorar dependências, exceções e tempos de execução nos aplicativos web do Java

*Obtenção de informações de aplicativo está no modo de visualização.*

Se você tiver [instrumentos seu aplicativo web do Java com ideias de aplicativo][java], você pode usar o agente de Java para obter informações mais detalhadas, sem qualquer alteração de código:


* **Dependências:** Dados sobre chamadas que seu aplicativo faz para outros componentes, incluindo:
 * **Chamadas REST** feitas via HttpClient, OkHttp e RestTemplate (Spring).
 * Chamadas **relacionada** feitas por meio do cliente do Jedis. Se a chamada levar mais de 10s, o agente também busca os argumentos de chamada.
 * **[Chamadas JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** - MySQL, SQL Server, PostgreSQL, SQLite, Oracle DB ou Apache Derby DB. há suporte para chamadas de "executeBatch". Para MySQL e PostgreSQL, se a chamada demora mais do que 10s, o agente reporta o plano de consulta. 
* **Detectada exceções:** Dados sobre exceções que são tratadas pelo seu código.
* **Tempo de execução do método:** Dados sobre o tempo necessário para executar métodos específicos.

Para usar o agente de Java, instalá-lo no seu servidor. Seus aplicativos web devem ser instrumentos com o [Aplicativo ideias Java SDK][java].

## <a name="install-the-application-insights-agent-for-java"></a>Instalar o agente de obtenção de informações do aplicativo para Java

1. No computador executando o seu servidor de Java, [Baixe o agente](https://aka.ms/aijavasdk).
2. Edite o script de inicialização do servidor de aplicativo e, em seguida, adicione a JVM a seguir:

    `javaagent:`*caminho completo para o arquivo JAR do agente*

    Por exemplo, em Tomcat em um computador Linux:

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. Reinicie o seu servidor de aplicativo.

## <a name="configure-the-agent"></a>Configurar o agente

Crie um arquivo denominado `AI-Agent.xml` e coloque-o na mesma pasta que o arquivo JAR agente.

Defina o conteúdo do arquivo xml. Edite o exemplo a seguir para incluir ou omitir os recursos que você deseja. 

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>
           
           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions 
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne" 
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

Você precisa habilitar a exceção de relatórios e o intervalo de método para métodos individuais.

Por padrão, `reportExecutionTime` for verdadeira e `reportCaughtExceptions` for falsa.

## <a name="view-the-data"></a>Exibir os dados

No recurso de obtenção de informações do aplicativo, agregados tempos de execução dependência e método remotos aparece [sob o bloco de desempenho][metrics]. 

Para pesquisar instâncias individuais de relatórios de dependência, exceção e método, abrir [pesquisa][diagnostic]. 

[Problemas de dependência diagnosticando - saber mais](app-insights-dependencies.md#diagnosis).



## <a name="questions-problems"></a>Dúvidas? Problemas?

* Nenhum dado? [Exceções do firewall do conjunto](app-insights-ip-addresses.md)
* [Solução de problemas de Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 
