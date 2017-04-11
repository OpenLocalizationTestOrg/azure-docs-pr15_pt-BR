<properties 
    pageTitle="Introdução ao aplicativo ideias com Java no Eclipse" 
    description="Use o Eclipse plug-in para adicionar o monitoramento de desempenho e uso ao seu site de Java com ideias de aplicativo" 
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
    ms.date="03/02/2016" 
    ms.author="awills"/>
 
# <a name="get-started-with-application-insights-with-java-in-eclipse"></a>Introdução ao aplicativo ideias com Java no Eclipse

O SDK de ideias de aplicativo envia telemetria do seu aplicativo da web de Java para que você pode analisar o desempenho e uso. O Eclipse plug-in para obtenção de informações de aplicativo instala automaticamente o SDK no seu projeto para que você sair da telemetria a caixa, além de uma API que você pode usar para escrever telemetria personalizada.   


## <a name="prerequisites"></a>Pré-requisitos

Atualmente a plug-in funciona para Maven projetos e dinâmico da Web no Eclipse. ([Adicionar o aplicativo ideias para outros tipos de projeto Java][java].)

Você precisará:

* Oracle JRE 1,6 ou posterior
* Uma assinatura do [Microsoft Azure](https://azure.microsoft.com/). (Você pode começar com a [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).
* [Eclipse IDE para desenvolvedores do Java EE](http://www.eclipse.org/downloads/), Indigo ou posterior.
* Windows 7 ou posterior, ou Windows Server 2008 ou posterior

## <a name="install-the-sdk-on-eclipse-one-time"></a>Instalar o SDK Eclipse (uma vez)

Você só precisa fazer dessa vez por máquina. Esta etapa instala um kit de ferramentas que possa adicionar o SDK para cada projeto da Web dinâmico.

1. No Eclipse, clique em Ajuda, instale o novo Software.

    ![Ajuda, instalar Software novo](./media/app-insights-java-eclipse/0-plugin.png)

2. O SDK está em http://dl.windowsazure.com/eclipse, em Kit de ferramentas do Azure. 
3. Desmarque a opção de **contatar todos os sites de atualização...**

    ![Para a obtenção de informações do aplicativo SDK, desmarque contato todos os sites de atualização](./media/app-insights-java-eclipse/1-plugin.png)

Siga as etapas restantes para cada projeto Java.

## <a name="create-an-application-insights-resource-in-azure"></a>Criar um recurso de obtenção de informações de aplicativo no Azure

1. Entrar no [portal do Azure](https://portal.azure.com).
2. Crie um novo recurso de obtenção de informações do aplicativo.  

    ![Clique em + e escolha a obtenção de informações de aplicativo](./media/app-insights-java-eclipse/01-create.png)  
3. Defina o tipo de aplicativo para aplicativo web Java.  

    ![Preencher um nome, escolha Java web app e clique em criar](./media/app-insights-java-eclipse/02-create.png)  
4. Encontre a chave de instrumentação do novo recurso. Você precisará colar isto no seu projeto de código em breve.  

    ![Na visão geral novo recurso, clique em propriedades e copie a chave de instrumentação](./media/app-insights-java-eclipse/03-key.png)  


## <a name="add-application-insights-to-your-project"></a>Adicionar a obtenção de informações de aplicativo ao projeto

1. Adicione ideias de aplicativo no menu de contexto do seu projeto de web Java.

    ![Na visão geral novo recurso, clique em propriedades e copie a chave de instrumentação](./media/app-insights-java-eclipse/02-context-menu.png)

2. Cole a chave de instrumentação obtido do portal do Azure.

    ![Na visão geral novo recurso, clique em propriedades e copie a chave de instrumentação](./media/app-insights-java-eclipse/03-ikey.png)


A chave é enviada juntamente com todos os itens de telemetria e informa ideias de aplicativo para exibi-lo em seu recurso.

## <a name="run-the-application-and-see-metrics"></a>Executar o aplicativo e ver métricas

Execute o aplicativo.

Retornar para o recurso de obtenção de informações do aplicativo no Microsoft Azure.

Dados de solicitações HTTP aparecerão na lâmina visão geral. (Se ele não estiver lá, aguarde alguns segundos e clique em Atualizar.)

![Falhas, contagens de solicitação e resposta do servidor ](./media/app-insights-java-eclipse/5-results.png)
 

Clique em qualquer gráfico para ver mais detalhadas métricas. 

![Contagens de solicitação por nome](./media/app-insights-java-eclipse/6-barchart.png)


[Saiba mais sobre métricas.][metrics]

 

E ao exibir as propriedades de uma solicitação, você pode ver os eventos de telemetria associados a ele como solicitações e exceções.
 
![Todos os rastreamentos para esta solicitação](./media/app-insights-java-eclipse/7-instance.png)


## <a name="client-side-telemetry"></a>Telemetria do lado do cliente

Da lâmina do início rápido, clique em código de Get para monitorar minhas páginas da web: 

![Na sua lâmina de visão geral do aplicativo, escolha início rápido, receber o código para monitorar minhas páginas da web. Copie o script.](./media/app-insights-java-eclipse/02-monitor-web-page.png)

Inserir o trecho de código no cabeçalho do seus arquivos HTML.

#### <a name="view-client-side-data"></a>Exibir dados do lado do cliente

Abra suas páginas da web atualizado e usá-los. Espere um ou dois minutos, e em seguida, retornar para a obtenção de informações de aplicativo e abra a lâmina de uso. (Da lâmina Overview, role para baixo e clique em uso.)

Métricas de sessão, usuário e modo de exibição de página aparecerão na lâmina uso:

![Sessões, usuários e modos de exibição de página](./media/app-insights-java-eclipse/appinsights-47usage-2.png)

[Saiba mais sobre configuração de telemetria do lado do cliente.][usage]

## <a name="publish-your-application"></a>Publicar seu aplicativo

Agora publica seu aplicativo para o servidor, use permita que as pessoas e assistir a telemetria aparecem no portal.

* Verifique se seu firewall permite que o aplicativo enviar telemetria para essas portas:

 * DC.Services.VisualStudio.com:443
 * DC.Services.VisualStudio.com:80
 * F5.Services.VisualStudio.com:443
 * F5.Services.VisualStudio.com:80


* Em servidores Windows, instale:

 * [Microsoft Visual C++ redistribuível](http://www.microsoft.com/download/details.aspx?id=40784)

    (Isso permite contadores de desempenho.)

## <a name="exceptions-and-request-failures"></a>Falhas de solicitação e exceções

Exceções sem tratamento são coletadas automaticamente:

![](./media/app-insights-java-eclipse/21-exceptions.png)

Para coletar dados em outras exceções, você tem duas opções:

* [Inserir chamadas para TrackException em seu código](app-insights-api-custom-events-metrics.md#track-exception). 
* [Instalar o agente de Java no seu servidor](app-insights-java-agent.md). Especifique os métodos que você deseja inspecionar.


## <a name="monitor-method-calls-and-external-dependencies"></a>Chamadas de método de monitor e dependências externas

[Instalar o agente de Java](app-insights-java-agent.md) fazer logon especificado métodos internos e chamadas feitas por meio de JDBC, com dados de tempo.


## <a name="performance-counters"></a>Contadores de desempenho

Na sua blade de visão geral, role para baixo e clique no bloco de **servidores** . Você verá um intervalo de contadores de desempenho.


![Role para baixo até clique os servidores de bloco](./media/app-insights-java-eclipse/11-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Personalizar a coleta de contador de desempenho

Para desabilitar o conjunto do conjunto padrão de contadores de desempenho, adicione o seguinte código sob o nó raiz do arquivo ApplicationInsights.xml:

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### <a name="collect-additional-performance-counters"></a>Coletar contador de desempenho adicional

Você pode especificar o contador de desempenho adicional para serem coletados.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Contadores JMX (expostos por máquina Virtual Java)

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

*   `displayName`– O nome exibido no portal de obtenção de informações do aplicativo.
*   `objectName`– O nome de objeto JMX.
*   `attribute`– O atributo do nome do objeto JMX para buscar
*   `type`(opcional) - o tipo de atributo do objeto JMX:
 *  Padrão: um tipo simples como int ou longa.
 *  `composite`: os dados do contador de desempenho estão no formato de 'Attribute.Data'
 *  `tabular`: os dados do contador de desempenho estão no formato de uma linha de tabela



#### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows

Cada [contador de desempenho do Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) é um membro de uma categoria (da mesma forma que um campo é um membro de uma classe). Categorias podem ser globais, ou podem ter numerados ou instâncias nomeadas.

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

*   displayName – o nome exibido no portal de obtenção de informações do aplicativo.
*   nome de categoria – a categoria de contador de desempenho (objeto de desempenho) ao qual este contador de desempenho está associada.
*   counterName – o nome do contador de desempenho.
*   instanceName – o nome da instância de categoria de contador de desempenho, ou uma cadeia de caracteres vazia (""), se a categoria contém uma única instância. Se o nome de categoria é o processo e o contador de desempenho que você gostaria de coletar consta do processo de JVM atual de que seu aplicativo está em execução, especifique `"__SELF__"`.

Seus contadores de desempenho estão visíveis como personalizados de métricas no [Explorador de métricas][metrics].

![](./media/app-insights-java-eclipse/12-custom-perfs.png)


### <a name="unix-performance-counters"></a>Contadores de desempenho do UNIX

* [Instalar o collectd com o plug-in de obtenção de informações do aplicativo](app-insights-java-collectd.md) para obter uma ampla variedade de dados de sistema e de rede.

## <a name="availability-web-tests"></a>Testes de web de disponibilidade

Obtenção de informações de aplicativo pode testar seu site em intervalos regulares para verificar que cabe e responder bem. [Configurar][availability], role para baixo e clique em disponibilidade.

![Role para baixo, clique em disponibilidade, em seguida, teste de adicionar Web](./media/app-insights-java-eclipse/31-config-web-test.png)

Se seu site falhar, você receberá gráficos da tempos de resposta, além de notificações por email.

![Exemplo de teste da Web](./media/app-insights-java-eclipse/appinsights-10webtestresult.png)

[Saiba mais sobre testes de web de disponibilidade.][availability] 

## <a name="diagnostic-logs"></a>Logs de diagnóstico

Se você estiver usando Logback ou Log4J (v 1.2 ou v 2.0) para rastreamento, você pode ter os logs de rastreamento enviados automaticamente para a obtenção de informações de aplicativo onde você pode explorar e pesquise neles.

[Saiba mais sobre os logs de diagnóstico][javalogs]

## <a name="custom-telemetry"></a>Telemetria personalizada 

Inserir algumas linhas de código em seu aplicativo web do Java para descobrir o que os usuários estão fazendo com ele ou para ajudar a diagnosticar problemas. 

Você pode inserir código na página da web JavaScript e de Java do lado do servidor.

[Saiba mais sobre telemetria personalizada][track]



## <a name="next-steps"></a>Próximas etapas

#### <a name="detect-and-diagnose-issues"></a>Detectar e diagnosticar problemas

* [Adicionar telemetria de cliente do web] [ usage] obter telemetria de desempenho do cliente da web.
* [Configurar testes web] [ availability] para garantir que seu aplicativo permaneça ao vivo e ágil.
* [Pesquisar eventos e logs de] [ diagnostic] para ajudar a diagnosticar problemas.
* [Capturar traços Log4J ou Logback][javalogs]

#### <a name="track-usage"></a>Controlar o uso

* [Adicionar telemetria de cliente do web] [ usage] para modos de exibição de página de monitor e métricas de usuário básica.
* [Controlar eventos personalizados e métricas] [ track] para saber mais sobre como o seu aplicativo é usado, tanto no cliente e servidor.


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 