<properties
   pageTitle="Análise de modo de falha | Microsoft Azure"
   description="Diretrizes para realização de análises de modo de falha para soluções de nuvem com base no Azure."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="mwasson"/>

# <a name="azure-resiliency-guidance-failure-mode-analysis"></a>Orientação de resiliência Azure: análise de modo de falha

Análise de modo de falha (FMA) é um processo para a criação de resiliência em um sistema, identificando possíveis pontos de falha no sistema. O FMA deve ser parte das fases do projeto e arquitetura, para que você possa criar recuperação de falhas no sistema desde o começo.

Aqui está o processo geral para conduzir um FMA: 

1. Identificar todos os componentes do sistema. Inclua dependências externas, como como provedores de identidade, serviços de terceiros e assim por diante.   

2. Para cada componente, identifique possíveis falhas que podem ocorrer. Um único componente pode ter mais de um modo de falha. Por exemplo, você deve considerar falhas de leitura e gravar falhas separadamente, porque reduções impacto e possíveis serão diferentes.

3. Taxa cada modo de falha de acordo com o seu risco geral. Considere estes fatores:  

    - O que é a probabilidade da falha. É relativamente comum? Extrememly raro? Você não precisa exatos números; o objetivo é ajudar a classificar a prioridade.

    - Qual é o impacto sobre o aplicativo, em termos de disponibilidade, perda de dados, custo monetário e interrupção dos negócios? 

4. Para cada modo de falha, determine como o aplicativo responderá e recuperar. Considere compensações em complexidade de custo e de aplicativos.   

Como ponto de partida para o seu processo FMA, este artigo contém um catálogo de possíveis modos de falha e seus reduções. O catálogo é organizado por tecnologia ou serviço Azure, além de uma categoria geral para design de nível de aplicativo. O catálogo não é completo, mas abrange muitas das principais Azure services. 


## <a name="app-service"></a>Serviço de aplicativo

### <a name="app-service-app-shuts-down"></a>Desliga o aplicativo de serviço de aplicativo.

**Detecção**. Possíveis causas:

- Desligamento esperado
    
    - Um operador desliga o aplicativo; Por exemplo, usando o portal do Azure.

    - O aplicativo foi descarregado porque ela estava ociosa. (Somente se a `Always On` configuração é desativada.)

- Desligamento inesperado

    - O aplicativo falha.

    - Uma instância de máquina virtual de serviço de aplicativo ficará indisponível.


Log Application_End capturará o encerramento de domínio de aplicativo (Falha de processo suave) e é a única maneira de capturar encerrar o domínio os aplicativos.

**Recuperação**

- Se o desligamento era esperado, use o evento de desligamento do aplicativo para desligar normalmente. Por exemplo, no ASP.NET, use a `Application_End` método.

- Se o aplicativo foi descarregado enquanto ocioso, ele é automaticamente reiniciado na próxima solicitação. No entanto, você precisará pagar "Iniciar fria" custo. 

- Para impedir que o aplicativo está sendo carregado enquanto ocioso, habilite o `Always On` configuração no web app. Consulte [Configurar web apps no serviço de aplicativo do Azure][app-service-configure].

- Para impedir que um operador desligar o aplicativo, defina um bloqueio de recurso com `ReadOnly` nível. Consulte [recursos de bloqueio com o Gerenciador de recursos do Azure][rm-locks].

- Se o aplicativo trava ou um máquina virtual do aplicativo de serviço fica indisponível, o serviço de aplicativo automaticamente reiniciar o aplicativo. 


**Diagnóstico**. Logs de aplicativos e registros do servidor web. Consulte [Ativar o log para web apps em um serviço de aplicativo do Azure de diagnósticos][app-service-logging].


### <a name="a-particular-user-repeatedly-makes-bad-requests-or-overloads-the-system"></a>Um usuário específico repetidamente faz solicitações incorretas ou overloads do sistema. 

**Detecção**. Autenticar usuários e incluir ID de usuário em logs do aplicativo.

**Recuperação**

- Use o [Gerenciamento de API do Azure] [ api-management] a solicitações de controle do usuário. Consulte [solicitação avançada a otimização com gerenciamento de API do Azure][api-management-throttling]

- Impedir que o usuário.

**Diagnóstico**. Registrar todas as solicitações de autenticação.


### <a name="a-bad-update-was-deployed"></a>Uma atualização de mau foi implantada.

**Detecção**. Monitorar a saúde do aplicativo por meio do Portal do Azure (consulte [Monitor Azure web app desempenho][app-insights-web-apps]) ou implementar o [padrão de monitoramento de ponto de extremidade de integridade][health-endpoint-monitoring-pattern].

**Recuperação**. Usar vários [slots de implantação] [ app-service-slots] e reverter para a implantação do último válida. Para obter mais informações, consulte [arquitetura de referência de aplicativo web básico][ra-web-apps-basic].


## <a name="azure-active-directory"></a>Active Directory do Azure

### <a name="openid-connect-oidc-authentication-fails"></a>Autenticação de OpenID conectar (OIDC) falha.

**Detecção**. Modos de falha possíveis incluem:

1. Azure AD não está disponível ou não pode ser acessada devido a um problema de rede. Falha de redirecionamento para o ponto de extremidade de autenticação e a middleware OIDC gera uma exceção.
2. Azure locatário do AD não existe. Redirecionamento para o ponto de extremidade de autenticação retorna um código de erro HTTP e o middleware OIDC gera uma exceção.
3. Usuário não se autenticar. Nenhuma estratégia de detecção é necessária; Azure AD manipula falhas de login.

**Recuperação**

1. Capture exceções sem tratamento da middleware.
2. Lidar com `AuthenticationFailed` eventos.
3. Redirecione o usuário para uma página de erro.
4. Repetições de usuário.


## <a name="azure-search"></a>Pesquisa do Azure

### <a name="writing-data-to-azure-search-fails"></a>Gravar dados em pesquisa do Azure falha.

**Detecção**. Capturar `Microsoft.Rest.Azure.CloudException` erros.

**Recuperação**

O [SDK do .NET pesquisa] [ search-sdk] tentativas automaticamente após falhas temporárias. As exceções geradas pelo cliente SDK devem ser tratadas como erros não transitório.

A política padrão de repetição usa retirada exponencial. Para usar uma política de repetição diferentes, chamar `SetRetryPolicy` na `SearchIndexClient` ou `SearchServiceClient` classe. Para obter mais informações, consulte [Tentativas automáticas][auto-rest-client-retry].

**Diagnóstico**. Usar [a análise de tráfego pesquisa][search-analytics].


### <a name="reading-data-from-azure-search-fails"></a>Lendo dados de pesquisa do Azure falha.

**Detecção**. Capturar `Microsoft.Rest.Azure.CloudException` erros.

**Recuperação**

O [SDK do .NET pesquisa] [ search-sdk] tentativas automaticamente após falhas temporárias. As exceções geradas pelo cliente SDK devem ser tratadas como erros não transitório.

A política padrão de repetição usa retirada exponencial. Para usar uma política de repetição diferentes, chamar `SetRetryPolicy` na `SearchIndexClient` ou `SearchServiceClient` classe. Para obter mais informações, consulte [Tentativas automáticas][auto-rest-client-retry].

**Diagnóstico**. Usar [a análise de tráfego pesquisa][search-analytics].



## <a name="cassandra"></a>Cassandra


### <a name="reading-or-writing-to-a-node-fails"></a>Ler ou gravar um nó falha.

**Detecção**. Capture a exceção. Para clientes do .NET, isso geralmente será `System.Web.HttpException`. Outro cliente pode ter outros tipos de exceção.  Para obter mais informações, consulte [Cassandra tratamento de erro à direita](http://www.datastax.com/dev/blog/cassandra-error-handling-done-right).

**Recuperação**

- Cada [cliente Cassandra](https://wiki.apache.org/cassandra/ClientOptions) tem seu próprio diretivas de repetição e recursos. Para obter mais informações, consulte [Cassandra tratamento de erro direita][cassandra-error-handling].
- Use uma implantação ciente de rack, conosco de dados distribuídos entre os domínios de falha.
- Implante para várias regiões com consistência quorum local. Se ocorrer uma falha não transitório, não sobre outra região.

**Diagnóstico**. Logs de aplicativos


## <a name="cloud-service"></a>Serviço de nuvem

### <a name="web-or-worker-roles-are-unexpectedlybeing-shut-down"></a>Funções da Web ou de trabalho estão sendo encerradas inesperadamente.

**Detecção**. O [RoleEnvironment.Stopping] [ RoleEnvironment.Stopping] evento é acionado.

**Recuperação**. Substituir o [RoleEntryPoint.OnStop] [ RoleEntryPoint.OnStop] método para limpar normalmente. Para obter mais informações, consulte [A maneira de direito de lidar com o Azure OnStop eventos] [ onstop-events] (blog).


## <a name="documentdb"></a>DocumentDB

### <a name="reading-data-from-documentdb-fails"></a>Lendo dados de DocumentDB falhará.

**Detecção**. Capturar `System.Net.Http.HttpRequestException` ou `Microsoft.Azure.Documents.DocumentClientException`. 

**Recuperação**

- O SDK automaticamente tentativas tentativas. Para definir o número de tentativas e o tempo de espera máximo, configure `ConnectionPolicy.RetryOptions`. Exceções que eleva o cliente estão além da política de repetição ou não são temporárias erros. 

- Se DocumentDB acelera o cliente, ele retornará um erro de HTTP 429. Verifique o código de status `DocumentClientException`. Se você estiver recebendo o erro 429 consistentemente, considere aumentar o valor de produtividade da coleção DocumentDB.

- Replica o banco de dados DocumentDB em duas ou mais regiões. Todas as réplicas são legíveis. Usando o cliente SDKs, especifique o `PreferredLocations` parâmetro. Esta é uma lista ordenada de regiões Azure. Todas as leituras serão enviadas para a primeira região disponível na lista. Se a solicitação falhar, o cliente tentará as outras regiões na lista, em ordem. Para obter mais informações, consulte [desenvolvimento com contas de DocumentDB várias regiões][docdb-multi-region].

**Diagnóstico**. Registrar todos os erros no lado do cliente.


### <a name="writing-data-to-documentdb-fails"></a>Gravar dados DocumentDB falhar.

**Detecção**. Capturar `System.Net.Http.HttpRequestException` ou `Microsoft.Azure.Documents.DocumentClientException`. 

**Recuperação**

- O SDK automaticamente tentativas tentativas. Para definir o número de tentativas e o tempo de espera máximo, configure `ConnectionPolicy.RetryOptions`. Exceções que eleva o cliente estão além da política de repetição ou não são temporárias erros. 

- Se DocumentDB acelera o cliente, ele retornará um erro de HTTP 429. Verifique o código de status `DocumentClientException`. Se você estiver recebendo o erro 429 consistentemente, considere aumentar o valor de produtividade da coleção DocumentDB.

- Replica o banco de dados DocumentDB em duas ou mais regiões. Se a região primária falhar, outra região será promovido para escrever. Você também pode disparar um failover manualmente. O SDK faz a detecção automática e roteamento, para que o código do aplicativo continuarão funcionando após um failover. Durante o período de failover (normalmente minutos), as operações de gravação terá mais alta latência, como o SDK localiza a nova região de gravação. Para obter mais informações, consulte [desenvolvimento com contas de DocumentDB várias regiões][docdb-multi-region].

- Como fallback, se mantiver o documento para uma fila de backup e fila de processo mais tarde.

**Diagnóstico**. Registrar todos os erros no lado do cliente.


## <a name="elasticsearch"></a>Elasticsearch

### <a name="reading-data-from-elasticsearch-fails"></a>Lendo dados de Elasticsearch falhará.

**Detecção**. Capturar a exceção apropriada para a determinado [cliente Elasticsearch] [ elasticsearch-client] sendo usado. 

**Recuperação**

- Use um mecanismo de repetição. Cada cliente tem suas próprias diretivas de repetição. 

- Implantar vários nós Elasticsearch e usar a replicação para alta disponibilidade.

Para obter mais informações, consulte [Elasticsearch em execução no Azure][elasticsearch-azure].

**Diagnóstico**. Você pode usar ferramentas de monitoramento para Elasticsearch ou registrar todos os erros no lado do cliente com a carga. Consulte a seção 'Monitoração' [Elasticsearch em execução no Azure][elasticsearch-azure].


### <a name="writing-data-to-elasticsearch-fails"></a>Gravar dados Elasticsearch falhar.

**Detecção**. Capturar a exceção apropriada para a determinado [cliente Elasticsearch] [ elasticsearch-client] sendo usado.  

**Recuperação**

- Use um mecanismo de repetição. Cada cliente tem suas próprias diretivas de repetição. 
 
- Se o aplicativo pode suportar um nível de consistência reduzida, considere escrita com `write_consistency` configuração de `quorum`.

Para obter mais informações, consulte [Elasticsearch em execução no Azure][elasticsearch-azure].

**Diagnóstico**. Você pode usar ferramentas de monitoramento para Elasticsearch ou registrar todos os erros no lado do cliente com a carga. Consulte a seção 'Monitoração' [Elasticsearch em execução no Azure][elasticsearch-azure].


## <a name="queue-storage"></a>Armazenamento de fila

### <a name="writing-a-message-to-azure-queue-storage-fails-consistently"></a>Escrever uma mensagem para falha de armazenamento do Azure fila consistentemente.

**Detecção**. Após *N* tentativas de repetição, a operação de gravação ainda falhará.

**Recuperação**

- Armazenar os dados em um cache local e encaminhar as gravações ao armazenamento mais tarde, quando o serviço fica disponível.
- Criar uma fila secundária e gravar fila se a fila principal não estiver disponível.

**Diagnóstico**. Usar [métricas de armazenamento][storage-metrics].


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>O aplicativo não é possível processar uma mensagem específica da fila. 

**Detecção**. Aplicativo específico. Por exemplo, a mensagem contém dados inválidos ou a lógica comercial falha por algum motivo. 

**Recuperação**

Mova a mensagem para uma fila separada. Execute um processo separado para examinar as mensagens na fila.

Considere usar filas de mensagens de barramento de serviço do Azure, que fornece uma [fila de inatividade] [ sb-dead-letter-queue] funcionalidade para essa finalidade.

Observação: Se você estiver usando filas de armazenamento com WebJobs, o SDK de WebJobs fornece tratamento de mensagem suspeita interno. Veja [como usar o armazenamento de fila Azure com o SDK do WebJobs][sb-poison-message].

**Diagnóstico**. Use o log de aplicativo.


## <a name="redis-cache"></a>Relacionada Cache

### <a name="reading-from-the-cache-fails"></a>Falha de leitura do cache.

**Detecção**. Capturar `StackExchange.Redis.RedisConnectionException`.

**Recuperação**

1. Repetir sobre falhas temporárias. Cache de relacionada Azure suporta repetir interno através de de consulte [diretrizes de repetição de Cache relacionada][redis-retry].
2. Trate não transitório falhas como perda de cache e voltar para a fonte de dados original.

**Diagnóstico**. Usar o [Diagnóstico de Cache relacionada][redis-monitor].


### <a name="writing-to-the-cache-fails"></a>Falha de gravação para o cache.

**Detecção**. Capturar `StackExchange.Redis.RedisConnectionException`.

**Recuperação**

1. Repetir sobre falhas temporárias. Cache de relacionada Azure suporta repetir interno através de de consulte [diretrizes de repetição de Cache relacionada][redis-retry].
2. Se o erro for não transitório, ignorá-lo e permitir que outras transações gravar no cache mais tarde.

**Diagnóstico**. Usar o [Diagnóstico de Cache relacionada][redis-monitor].


## <a name="sql-database"></a>Banco de dados SQL

### <a name="cannot-connect-to-the-database-in-the-primary-region"></a>Não é possível se conectar ao banco de dados na região primária.

**Detecção**. Falha de Conexão.

**Recuperação**

Pré-requisito: O banco de dados deve ser configurado para replicação de localização geográfica ativa. Consulte [Replicação geográfica SQL banco de dados ativa][sql-db-replication].

- Para consultas, ler a partir de uma réplica secundária. 
- Para inserções e atualizações, manualmente alternar para uma réplica secundária. Consulte [Iniciar um failover planejado ou para o banco de dados do Azure SQL][sql-db-failover].

A réplica usa uma cadeia de conexão diferente, portanto, você precisará atualizar a cadeia de conexão em seu aplicativo.


### <a name="client-runs-out-of-connections-in-the-connection-pool"></a>Cliente executa fora conexões do pool de conexão.

**Detecção**. Capturar `System.InvalidOperationException` erros. 

**Recuperação**

- Repita a operação.
- Como um plano de atenuação, isole os pools de conexão para cada caso de uso, para que um caso de uso não pode dominar todas as conexões.
- Aumente os pools de conexão máximo.

**Diagnóstico**. Logs de aplicativos.


### <a name="database-connection-limit-is-reached"></a>Limite de conexão de banco de dados é alcançado. 

**Detecção**. Banco de dados do SQL Azure limita o número de operadores simultâneos, logon e sessões. Os limites dependem o nível de serviço. Para obter mais informações, consulte [limites de recursos do Azure SQL Database][sql-db-limits].

Para detectar esses erros, capturar `System.Data.SqlClient.SqlException` e verificar o valor de `SqlException.Number` para o código de erro SQL. Para obter uma lista de códigos de erro relevantes, consulte [códigos de erro SQL para aplicativos de cliente do banco de dados do SQL: erro de conexão e outros problemas de banco de dados][sql-db-errors].

**Recuperação**. Esses erros são considerados temporárias, então repetindo pode resolver o problema. Se você pressionar consistentemente esses erros, considere a possibilidade de dimensionamento o banco de dados.

**Diagnóstico**. -O [sys.event_log] [ sys.event_log] consulta retorna conexões bem-sucedida do banco de dados, falhas de conexão e travamentos.

- Criar uma [regra de alerta] [ azure-alerts] para conexões com falha.

- Habilitar a [auditoria de banco de dados SQL] [ sql-db-audit] e verificar se há falhas de logon.


## <a name="service-bus-messaging"></a>Barramento de mensagens do serviço

### <a name="reading-a-message-from-a-service-bus-queue-fails"></a>Ler uma mensagem de uma fila de barramento de serviço falhará.

**Detecção**. Capture exceções do cliente SDK. A classe base para exceções de barramento de serviço é [MessagingException][sb-messagingexception-class]. Se o erro for temporário, o `IsTransient` propriedade é verdadeira. 

Para obter mais informações, consulte [barramento de serviço mensagens exceções][sb-messaging-exceptions].

**Recuperação**

1. Repetir sobre falhas temporárias. Consulte [Barramento de serviço repetir diretrizes][sb-retry].

2. Mensagens que não podem ser entregues para qualquer receptor são colocadas na *fila de inatividade*. Use esta fila para ver quais mensagens não podem ser recebidas. Não há nenhuma limpeza automática da fila aceitas. Mensagens permanecem nela até que você explicitamente recuperá-los. Consulte [filas de inatividade barramento de visão geral do serviço][sb-dead-letter-queue].


### <a name="writing-a-message-to-a-service-bus-queue-fails"></a>Fila escrevendo uma mensagem para um serviço de barramento falhará.

**Detecção**. Capture exceções do cliente SDK. A classe base para exceções de barramento de serviço é [MessagingException][sb-messagingexception-class]. Se o erro for temporário, o `IsTransient` propriedade é verdadeira. 

Para obter mais informações, consulte [barramento de serviço mensagens exceções][sb-messaging-exceptions].

**Recuperação**

1. O cliente de barramento de serviço reenviar automaticamente após temporárias erros. Por padrão, ele usa retirada exponencial. Após a contagem de repetição máxima ou o período de tempo limite máximo, o cliente gera uma exceção. Para obter mais informações, consulte [Barramento de serviço repetir diretrizes][sb-retry].

2. Se a cota da fila for excedida, o cliente gera [QuotaExceededException][QuotaExceededException]. A mensagem de exceção fornece mais detalhes. Descarregar algumas mensagens da fila antes de tentar novamente e considere usando o separador de circuito padrão para evitar repetições contínuas enquanto a cota for excedida. Além disso, verifique se que a propriedade [BrokeredMessage.TimeToLive] não está definida muito alta. 

3. Dentro de uma região, resiliência pode ser melhorada, usando [filas particionadas ou tópicos][sb-partition]. Uma fila não particionados ou o tópico é atribuído a uma loja de mensagens. Se este armazenamento de mensagens não estiver disponível, todas as operações na fila ou tópico falhará. Uma fila particionada ou tópico é particionado em vários armazenamentos de mensagens. 

4. Para resiliência adicional, crie dois namespaces barramento de serviço em diferentes regiões e replicar as mensagens. Você pode usar replicação ativa ou replicação passiva.

    - Replicação ativa: O cliente envia todas as mensagens para ambas as filas. O receptor ouve as duas filas. Marcar mensagens com um identificador exclusivo, para que o cliente pode descartar mensagens duplicadas.

    - Replicação passiva: O cliente envia a mensagem para uma fila. Se houver um erro, o cliente de volta para a outra fila. O receptor ouve as duas filas. Essa abordagem reduz o número de mensagens duplicadas que são enviadas. No entanto, o destinatário ainda deve tratar mensagens duplicadas.

    Para obter mais informações, consulte [GeoReplication sample] [ sb-georeplication-sample] e [práticas recomendadas para aplicativos proteger contra interrupções de barramento de serviço e desastres][sb-outages].


### <a name="duplicate-message"></a>Mensagem duplicada.

**Detecção**. Examine os `MessageId` e `DeliveryCount` propriedades da mensagem.

**Recuperação**

- Se possível, projete sua mensagem operações de processamento para ser idempotentes. Caso contrário, armazene IDs de mensagem das mensagens que já estão processadas e verifique a ID antes de processamento de uma mensagem.

- Habilitar detecção de duplicidades, criando fila com `RequiresDuplicateDetection` definido como true. Com essa configuração, o barramento de serviço exclui automaticamente qualquer mensagem que é enviada com o mesmo `MessageId` como uma mensagem anterior.  Observe o seguinte:

    -  Esta configuração impede que mensagens duplicadas sendo colocado na fila de. Não impede que um receptor a mesma mensagem de processamento mais de uma vez.

    - Detecção de duplicidades tem uma janela de tempo. Se uma duplicata for enviada além esta janela, ele não será detectado.

**Diagnóstico**. Registrar mensagens duplicadas.


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>O aplicativo não é possível processar uma mensagem específica da fila. 

**Detecção**. Aplicativo específico. Por exemplo, a mensagem contém dados inválidos ou a lógica comercial falha por algum motivo. 

**Recuperação**

Há dois modos de falha a considerar. 

- O receptor detecta a falha. Nesse caso, mova a mensagem para a fila de inatividade. Posteriormente, execute um processo separado para examinar as mensagens na fila de inatividade.

- O receptor falha no meio de processamento da mensagem &mdash; por exemplo, devido a uma exceção não tratada. Para lidar com esse caso, use `PeekLock` modo. Nesse modo, se o bloqueio expirar, a mensagem fica disponível para outros destinatários. Se a mensagem exceder a contagem de entrega máxima ou o tempo de vida, a mensagem é movida automaticamente para a fila de inatividade.

Para obter mais informações, consulte [Visão geral do barramento de serviço filas de inatividade][sb-dead-letter-queue].

**Diagnóstico**. Sempre que o aplicativo move uma mensagem para a fila de inatividade, gravar um evento os logs do aplicativo.


## <a name="service-fabric"></a>Estrutura de serviço

### <a name="a-request-to-a-service-fails"></a>Falha de uma solicitação para um serviço.

**Detecção**. O serviço retornará um erro.

**Recuperação**

- Localize um proxy novamente (`ServiceProxy` ou `ActorProxy`) e chamar o método de serviço/ator novamente. 

- **Serviço de estado**. Quebre operações em conjuntos de confiáveis em uma transação. Se houver um erro, a transação será revertida. A solicitação, se extraídas de uma fila, será processada novamente. 
 
- **Serviço de estado**. Se o serviço persistir dados em um armazenamento externo, todas as operações devem ser idempotentes.


**Diagnóstico**. Log do aplicativo

### <a name="service-fabric-node-is-shut-down"></a>Serviço tecidos nó for desligado.

**Detecção**. Um token de cancelamento é passado para o serviço `RunAsync` método. Serviço tecidos cancela a tarefa antes de desligar o nó.

**Recuperação**. Use o token de cancelamento para detectar desligamento. Quando o cancelamento de solicitações de serviço tecidos, concluir qualquer trabalho e sair `RunAsync` assim que possível.

**Diagnóstico**. Logs de aplicativos


## <a name="storage"></a>Armazenamento

### <a name="writing-data-to-azure-storage-fails"></a>Falha de gravar dados ao armazenamento do Azure

**Detecção**. O cliente recebe erros ao escrever.

**Recuperação**

1. Repita a operação, recuperar de falhas temporárias. A [política de repetição] [ Storage.RetryPolicies] no cliente SDK lida com isso automaticamente.
2. Implemente o padrão de separador de circuito para evitar armazenamento impressionante.
3. Se N tentativas falharem, execute um fallback normal. Por exemplo:

    - Armazenar os dados em um cache local e encaminhar as gravações ao armazenamento mais tarde, quando o serviço fica disponível.
    - Se a ação de gravação foi em um escopo de transação, compensar a transação.

**Diagnóstico**. Usar [métricas de armazenamento][storage-metrics].


### <a name="reading-data-from-azure-storage-fails"></a>Lendo dados de armazenamento do Azure falha.

**Detecção**. O cliente recebe erros ao ler.

**Recuperação**

1. Repita a operação, recuperar de falhas temporárias. A [política de repetição] [ Storage.RetryPolicies] no cliente SDK lida com isso automaticamente.
2. Para armazenamento de ar GRS, se a leitura do ponto de extremidade primária falhar, tente leitura do ponto de extremidade secundário. Isso pode manipular o SDK cliente automaticamente. Consulte [replicação de armazenamento do Azure][storage-replication].
3. Se *N* tentativas falharem, execute uma ação fallback para prejudicar normalmente. Por exemplo, se uma imagem do produto não pode ser recuperada do armazenamento, mostra uma imagem de espaço reservado genérico.

**Diagnóstico**. Usar [métricas de armazenamento][storage-metrics].


## <a name="virtual-machine"></a>Máquina virtual

### <a name="connection-to-a-backend-vm-fails"></a>Falha de Conexão para um máquina virtual de back-end.

**Detecção**. Erros de conexão de rede.

**Recuperação**

- Implante VMs de pelo menos duas back-end em um conjunto de disponibilidade, atrás de um balanceador de carga.

- Se o erro de conexão for temporário, às vezes, TCP com êxito tentará enviando a mensagem. 

- Implemente uma diretiva de repetição no aplicativo. 

- Erros persistente ou não transitório, implementar o [separador de circuito] [ circuit-breaker] padrão.

- Se a máquina virtual chamada excede o limite de egresso de rede, fila de saída será preenchido. Se a fila de saída for consistentemente completa, considere a possibilidade de dimensionamento. 

**Diagnóstico**. Eventos de log em limites de serviço.

### <a name="vm-instance-becomes-unavailable-or-unhealthy"></a>Instância de máquina virtual se torna indisponível ou não íntegra.

**Detecção**. Configurar um balanceador de carga de [teste de integridade] [ lb-probe] que sinaliza se a instância de máquina virtual é íntegra. O teste deve verificar se funções críticas estão respondendo corretamente. 

**Recuperação**. Para cada camada de aplicativo, coloque várias instâncias de máquina virtual o mesmo conjunto de disponibilidade e coloque um balanceador de carga na frente de VMs. Se o teste de integridade falhar, o balanceador de carga interrompe o envio de novas conexões na instância não íntegra.

**Diagnóstico**. -Use balanceador de carga de [análise de log][lb-monitor].
- Configure seu sistema de monitoramento para monitorar todos a pontos de extremidade de monitoramento de integridade.


### <a name="operator-accidentally-shuts-down-a-vm"></a>Operador desliga acidentalmente uma máquina virtual.

**Detecção**. N/D

**Recuperação**. Definir um bloqueio de recurso com `ReadOnly` nível. Consulte [recursos de bloqueio com o Gerenciador de recursos do Azure][rm-locks].

**Diagnóstico**. Usar [Logs de atividades Azure][azure-activity-logs].


## <a name="webjobs"></a>WebJobs

### <a name="continuous-job-stops-running-when-the-scm-host-is-idle"></a>Trabalho contínuo parar executando quando o host SCM está ocioso.

**Detecção**. Passe um token de cancelamento para a função WebJob. Para obter mais informações, consulte [desligamento][web-jobs-shutdown]. 

**Recuperação**. Habilitar o `Always On` configuração no web app. Para obter mais informações, consulte [tarefas de executar o plano de fundo com WebJobs][web-jobs].


## <a name="application-design"></a>Design de aplicativo

### <a name="application-cant-handle-a-spike-in-incoming-requests"></a>Aplicativo não é possível manipular um pico no solicitações de entrada.

**Detecção**. Depende do aplicativo. Sintomas típicos:

- O site começa retornando códigos de erro 5xx HTTP.
- Serviços dependentes, como o banco de dados ou armazenamento, comece a solicitações de aceleração. Procure erros HTTP como 429 HTTP (muito muitas solicitações), dependendo do serviço.
- Comprimento da fila HTTP aumenta.

**Recuperação**

- Dimensione para lidar com aumento de carga. 

- Atenue falhas para evitar falhas em cascata interromper todo o aplicativo. Estratégias de atenuação incluem:

    - Implementar o [Padrão de otimização] [ throttling-pattern] para evitar sobrecarregar sistemas de back-end.
    - Use [carga baseado em fila nivelamento] [ queue-based-load-leveling] para solicitações de buffer e processá-los em um ritmo apropriado.
    - Priorize determinados clientes. Por exemplo, se o aplicativo tiver níveis pagos e gratuitos, controle os clientes na camada gratuito, mas não pagos clientes. Consulte [padrão de fila de prioridade][priority-queue-pattern].

**Diagnóstico**. Usar [o log de diagnóstico de serviço de aplicativo][app-service-logging]. Usar um serviço como o [Azure Log Analytics][azure-log-analytics], [Obtenção de informações de aplicativo][app-insights], ou [Novo Relíquia] [ new-relic] para ajudar a entender os logs de diagnóstico.


### <a name="one-of-the-operations-in-a-workflow-or-distributed-transaction-fails"></a>Uma das operações em um fluxo de trabalho ou transação distribuída falhará.

**Detecção**. Após *N* tentativas, ele ainda falha.

**Recuperação**

- Como um plano de atenuação, implementar o [Agendador do agente Supervisor] [ scheduler-agent-supervisor] padrão para gerenciar o fluxo de trabalho inteiro. 
- Não repetir em tempos limite. Não há uma taxa de sucesso baixa para esse erro. 
- Trabalho de fila, para tentar novamente mais tarde.

**Diagnóstico**. Faça logon todas as operações (com e sem êxito), incluindo compensating ações. Use correlação IDs, para que você possa acompanhar todas as operações na mesma transação.


### <a name="a-call-to-a-remote-service-fails"></a>Falha de uma chamada para um serviço remoto.

**Detecção**. Código de erro HTTP.

**Recuperação**

1. Repetir sobre falhas temporárias. 
2. Se a chamada falhar após *N* tentativas, executar uma ação de fallback. (Aplicativo específico).
3. Implementar o [padrão de separador de circuito] [ circuit-breaker] para evitar falhas em cascata. 

**Diagnóstico**. Registrar todas as falhas de chamada remota.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o processo de FMA, consulte [resiliência por design para serviços de nuvem] [ resilience-by-design-pdf] (download do PDF).

<!-- links -->

[api-management]: https://azure.microsoft.com/documentation/services/api-management/
[api-management-throttling]: ../api-management/api-management-sample-flexible-throttling.md
[app-insights]: ../application-insights/app-insights-overview.md
[app-insights-web-apps]: ../application-insights/app-insights-azure-web-apps.md
[app-service-configure]: ../app-service-web/web-sites-configure.md
[app-service-logging]: ../app-service-web/web-sites-enable-diagnostic-log.md
[app-service-slots]: ../app-service-web/web-sites-staged-publishing.md
[auto-rest-client-retry]: https://github.com/Azure/autorest/blob/master/Documentation/clients-retry.md
[azure-activity-logs]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[azure-alerts]: ../monitoring-and-diagnostics/insights-alerts-portal.md
[azure-log-analytics]: ../log-analytics/log-analytics-overview.md
[BrokeredMessage.TimeToLive]: https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
[cassandra-error-handling]: http://www.datastax.com/dev/blog/cassandra-error-handling-done-right
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[docdb-multi-region]: ../documentdb/documentdb-developing-with-multiple-regions.md
[elasticsearch-azure]: guidance-elasticsearch-running-on-azure.md
[elasticsearch-client]: https://www.elastic.co/guide/en/elasticsearch/client/index.html
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[onstop-events]: https://azure.microsoft.com/blog/the-right-way-to-handle-azure-onstop-events/
[lb-monitor]: ../load-balancer/load-balancer-monitor-log.md
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md#learn-about-the-types-of-probes
[new-relic]: https://newrelic.com/
[priority-queue-pattern]: https://msdn.microsoft.com/library/dn589794.aspx
[queue-based-load-leveling]: https://msdn.microsoft.com/library/dn589783.aspx
[QuotaExceededException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx
[ra-web-apps-basic]: guidance-web-apps-basic.md
[redis-monitor]: ../redis-cache/cache-how-to-monitor.md
[redis-retry]: ../best-practices-retry-service-specific.md#cache-redis-retry-guidelines
[resilience-by-design-pdf]: http://download.microsoft.com/download/D/8/C/D8C599A4-4E8A-49BF-80EE-FE35F49B914D/Resilience_by_Design_for_Cloud_Services_White_Paper.pdf
[RoleEntryPoint.OnStop]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[RoleEnvironment.Stopping]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx
[rm-locks]: ../resource-group-lock-resources.md
[sb-dead-letter-queue]: ../service-bus-messaging/service-bus-dead-letter-queues.md
[sb-georeplication-sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/GeoReplication
[sb-messagingexception-class]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
[sb-messaging-exceptions]: ../service-bus-messaging/service-bus-messaging-exceptions.md
[sb-outages]: ../service-bus/service-bus-outages-disasters.md#protecting-queues-and-topics-against-datacenter-outages-or-disasters
[sb-partition]: ../service-bus-messaging/service-bus-partitioning.md
[sb-poison-message]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#poison
[sb-retry]: ../best-practices-retry-service-specific.md#service-bus-retry-guidelines
[search-sdk]: https://msdn.microsoft.com/library/dn951165.aspx
[scheduler-agent-supervisor]: https://msdn.microsoft.com/library/dn589780.aspx
[search-analytics]: ../search/search-traffic-analytics.md
[sql-db-audit]: ../sql-database/sql-database-auditing-get-started.md
[sql-db-errors]: ../sql-database/sql-database-develop-error-messages.md#resource-governanance-errors
[sql-db-failover]: ../sql-database/sql-database-geo-replication-failover-portal.md
[sql-db-limits]: ../sql-database/sql-database-resource-limits.md
[sql-db-replication]: ../sql-database/sql-database-geo-replication-overview.md
[storage-metrics]: https://msdn.microsoft.com/library/dn782843.aspx
[storage-replication]: ../storage/storage-redundancy.md
[Storage.RetryPolicies]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.aspx
[sys.event_log]: https://msdn.microsoft.com/library/dn270018.aspx
[throttling-pattern]: https://msdn.microsoft.com/library/dn589798.aspx
[web-jobs]: ../app-service-web/web-sites-create-web-jobs.md
[web-jobs-shutdown]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful

