<properties
    pageTitle="Usar a análise de armazenamento para coletar dados de logs e métricas | Microsoft Azure"
    description="Análise de armazenamento permite que você para controlar dados de métricas para todos os serviços de armazenamento e para coletar logs para armazenamento de Blob, fila e tabela."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="storage-analytics"></a>Análise de armazenamento

## <a name="overview"></a>Visão geral

Análise de armazenamento Azure executa log e fornece métricas dados para uma conta de armazenamento. Você pode usar esses dados para solicitações de rastreamento, analisar tendências de uso e diagnosticar problemas com sua conta de armazenamento.

Para usar a análise de armazenamento, você deve habilitá-lo individualmente para cada serviço que você deseja monitorar. Você pode habilitá-lo a partir do [Portal do Azure](https://portal.azure.com). Para obter detalhes, consulte [Monitor de uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md). Você também pode habilitar a análise de armazenamento por programação via a API REST ou a biblioteca de cliente. Use as operações de [Obter propriedades de serviço de Blob](https://msdn.microsoft.com/library/hh452239.aspx), [Obter propriedades de serviço de fila](https://msdn.microsoft.com/library/hh452243.aspx), [Obter propriedades de serviço de tabela](https://msdn.microsoft.com/library/hh452238.aspx)e [Obter propriedades de serviço de arquivo](https://msdn.microsoft.com/library/mt427369.aspx) para habilitar a análise de armazenamento para cada serviço.

Os dados agregados são armazenados em um blob conhecido (para o registro em log) e em tabelas conhecidas (para métricas), que podem ser acessadas usando os serviços de Blob e tabela APIs.

Análise de armazenamento tem um limite de 20 TB a quantidade de dados armazenados que seja independentes do limite total de sua conta de armazenamento. Para obter mais informações sobre faturamento e políticas de retenção de dados, consulte [análise de armazenamento e cobrança](https://msdn.microsoft.com/library/hh360997.aspx). Para obter mais informações sobre limites de armazenamento de conta, consulte [metas de desempenho e escalabilidade de armazenamento do Azure](storage-scalability-targets.md).

Para um guia detalhado sobre como usar a análise de armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao armazenamento do Azure, consulte [Monitor, diagnosticar e solucionar problemas de armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).


## <a name="about-storage-analytics-logging"></a>Sobre o log de análise de armazenamento

Análise de armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e para diagnosticar problemas com um serviço de armazenamento. Solicitações são registradas em uma base de esforço.

Entradas de log são criados somente se houver atividade de serviço de armazenamento. Por exemplo, se uma conta de armazenamento tem atividade no serviço Blob, mas não em seus serviços de tabela ou fila, somente os logs referentes ao serviço Blob serão criados.

Log de análise de armazenamento não está disponível para o serviço de arquivo do Azure.

### <a name="logging-authenticated-requests"></a>Solicitações de log autenticado

Os seguintes tipos de solicitações autenticadas são registrados:

- Solicitações com êxito.

- Falha solicitações, incluindo o tempo limite, a otimização, rede, autorização e outros erros.

- Solicitações usando um compartilhados acesso assinatura (SAS), incluindo solicitações de falhas e bem-sucedido.

- Solicitações de dados de análise.

Solicitações feitas por análise de armazenamento em si, como o log de criação ou exclusão, não estão conectadas. Uma lista completa dos dados registrados é documentada nos tópicos [operações de fez Analytics de armazenamento e mensagens de Status](https://msdn.microsoft.com/library/hh343260.aspx) e o [Formato de Log de análise de armazenamento](https://msdn.microsoft.com/library/hh343259.aspx) .

### <a name="logging-anonymous-requests"></a>Solicitações anônimas de log
Os seguintes tipos de solicitações anônimas são registrados:

- Solicitações com êxito.

- Erros do servidor.

- Erros de tempo limite para cliente e servidor.

- Falhas solicitações GET com código de erro 304 (não modificados).

Todas as outras solicitações anônimas falhas não estão conectadas. Uma lista completa dos dados conectados é documentada nos tópicos [operações de fez Analytics de armazenamento e mensagens de Status](https://msdn.microsoft.com/library/hh343260.aspx) e o [Formato de Log de análise de armazenamento](https://msdn.microsoft.com/library/hh343259.aspx) .

### <a name="how-logs-are-stored"></a>Como os registros são armazenados
Todos os logs são armazenados em bolhas de bloco em um contêiner chamado $logs, que é criada automaticamente quando a análise de armazenamento está habilitada para uma conta de armazenamento. O contêiner $logs está localizado no namespace blob da conta de armazenamento, por exemplo: `http://<accountname>.blob.core.windows.net/$logs`. Este contêiner não pode ser excluída depois que a análise de armazenamento tiver sido ativada, embora seu conteúdo possa ser excluído.

>[Azure.NOTE] O contêiner de $logs não é exibido quando um contêiner listando operação é executado, como o método de [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) . Ele deve ser acessado diretamente. Por exemplo, você pode usar o método [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) para acessar os blobs no `$logs` contêiner.
Como as solicitações são registradas, a análise de armazenamento serão carregadas resultados intermediários como blocos. Periodicamente, a análise de armazenamento será confirmar esses blocos e disponibilizá-los como um blob.

Podem existir registros duplicados de logs criados na mesma hora. Você pode determinar se um registro é uma duplicata verificando o número de **identificação da solicitação** e **operação** .

### <a name="log-naming-conventions"></a>Convenções de nomenclatura de log
Cada log será gravado no seguinte formato.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

A tabela a seguir descreve cada atributo no nome de log.

| Atributo         | Descrição                                                                                                                                                                                   |
|----------------   |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------   |
| < nome do serviço >    | O nome do serviço de armazenamento. Por exemplo: blob, tabela ou fila.                                                                                                                          |
| AAAA              | O ano de quatro dígitos para o log. Por exemplo: 2011.                                                                                                                                           |
| MM                | O mês de dois dígitos para o log. Por exemplo: 07.                                                                                                                                             |
| DD                | O mês de dois dígitos para o log. Por exemplo: 07.                                                                                                                                             |
| hh                | A hora de dois dígitos que indica a hora de início para que os logs, no formato de UTC de 24 horas. Por exemplo: 18.                                                                                     |
| mm                | O número de dois dígitos que indica o minuto inicial para que os logs. Este valor não é suportado na versão atual do armazenamento Analytics e seu valor sempre será 00.     |
| <counter>         | Um contador em zero com seis dígitos que indica o número de log blobs gerado para o serviço de armazenamento em um período de tempo de hora. Esse contador começa em 000000. Por exemplo: 000001.     |

A seguir é um nome de log de exemplo completos que combina os exemplos anteriores.

    blob/2011/07/31/1800/000001.log

A seguir é um exemplo de URI que pode ser usada para acessar o registro anterior.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Quando uma solicitação de armazenamento está conectada, o nome de log resultante corresponde a hora em que a operação solicitada é concluída. Por exemplo, se uma solicitação de GetBlob foi concluída às 6:30. em 31/7/2011, o log seria escrito com o prefixo a seguir:`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Log de metadados
Todos os blobs de log são armazenados com metadados que podem ser usados para identificar os dados de log que contém o blob. A tabela a seguir descreve cada atributo de metadados.

| Atributo     | Descrição                                                                                                                                                                                                                                                   |
|------------   |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| LogType       | Descreve se o log contém informações relativas para ler, gravar ou excluir operações. Esse valor pode incluir um tipo ou uma combinação de todos os três, separados por vírgulas. Exemplo 1: gravação; Exemplo 2: ler, gravar; Exemplo 3: ler, gravar, excluir.    |
| Hora de início     | O horário mais cedo de uma entrada do log, na forma de aaaa-MM-DDThh:mm:ssZ. Por exemplo: 2011-07-31T18:21:46Z.                                                                                                                                             |
| Hora de término       | A hora mais recente de uma entrada do log, na forma de aaaa-MM-DDThh:mm:ssZ. Por exemplo: 2011-07-31T18:22:09Z.                                                                                                                                               |
| LogVersion    | A versão do formato de log. Atualmente o único valor com suporte é 1.0.                                                                                                                                                                                     |

A lista a seguir exibe metadados de exemplo completos usando os exemplos anteriores.

- LogType = gravação

- Hora de início = 2011-07-31T18:21:46Z

- Hora de término = 2011-07-31T18:22:09Z

- LogVersion = 1.0

### <a name="accessing-logging-data"></a>Acessando dados de registro em log

Todos os dados a `$logs` contêiner podem ser acessadas usando o serviço de Blob APIs, incluindo as APIs do .NET fornecidas pelo Azure gerenciado biblioteca. O administrador de conta de armazenamento pode ler e excluir logs, mas não é possível criar ou atualizá-los. Metadados do log e o nome de registro podem ser usadas ao consultando para um log. É possível para logs de uma determinada hora apareça fora de ordem, mas os metadados sempre Especifica o período de tempo de entradas de log em um log. Portanto, você pode usar uma combinação de nomes de log e metadados ao procurar um log específico.

## <a name="about-storage-analytics-metrics"></a>Sobre métricas de armazenamento Analytics

Análise de armazenamento pode armazenar métricas que incluam dados de estatísticas e a capacidade de transação agregado sobre solicitações a um serviço de armazenamento. Transações são relatadas no nível da operação API, bem como o nível de serviço de armazenamento e capacidade é relatada no nível de serviço de armazenamento. Dados de métricas podem ser usados para analisar o uso do serviço de armazenamento, diagnosticar problemas com solicitações feitas em relação ao serviço de armazenamento e melhorar o desempenho de aplicativos que usam um serviço.

Para usar a análise de armazenamento, você deve habilitá-lo individualmente para cada serviço que você deseja monitorar. Você pode habilitá-lo a partir do [Portal do Azure](https://portal.azure.com). Para obter detalhes, consulte [Monitor de uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md). Você também pode habilitar a análise de armazenamento por programação via a API REST ou a biblioteca de cliente. Use as operações de **Obter propriedades de serviço** para habilitar a análise de armazenamento para cada serviço.

### <a name="transaction-metrics"></a>Métricas de transação

Um conjunto robusto de dados estiver gravado intervalos por hora ou minuto para cada serviço de armazenamento e solicitada a operação de API, incluindo entrada/saída, disponibilidade, erros e categorizado porcentagens de solicitação. Você pode ver uma lista completa dos detalhes da transação no tópico [Esquema de tabela de métricas de análise de armazenamento](https://msdn.microsoft.com/library/hh343264.aspx) .

Dados de transação são registrados em dois níveis – o nível de serviço e o nível de operação de API. Nível de serviço, estatísticas resumindo todos solicitada operações de API são gravadas em uma entidade de tabela cada hora mesmo se nenhum solicitações feitas ao serviço. No nível da operação de API, estatísticas só são gravadas em uma entidade se a operação foi solicitada dentro dessa hora.

Por exemplo, se você executar uma operação de **GetBlob** seu serviço Blob, métricas de análise de armazenamento registrar a solicitação e inclua-o nos dados agregados para o serviço de Blob, bem como a operação de **GetBlob** . No entanto, não se for solicitada nenhuma operação **GetBlob** durante a hora, uma entidade não será gravada para o `$MetricsTransactionsBlob` para essa operação.

Métricas de transação são registradas para solicitações de usuário e solicitações feitas por análise de armazenamento em si. Por exemplo, pedidos por análise de armazenamento para gravar logs e entidades de tabela são gravados. Para saber mais sobre como essas solicitações são cobradas, consulte [análise de armazenamento e cobrança](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Métricas de capacidade

>[AZURE.NOTE] Atualmente, métricas de capacidade estão disponíveis apenas para o serviço de Blob. Métricas de capacidade para os serviços de tabela e fila estarão disponíveis em versões futuras de análise de armazenamento.

Capacidade dados são gravados diariamente para o serviço de Blob de uma conta de armazenamento e duas entidades de tabela são gravadas. Uma entidade fornece estatísticas para dados de usuário e o outro fornece estatísticas sobre a `$logs` contêiner de blob usado pela análise de armazenamento. O `$MetricsCapacityBlob` tabela inclui as seguintes estatísticas:

- **Capacidade**: A quantidade de armazenamento usada pelo serviço de Blob da conta de armazenamento, em bytes.

- **ContainerCount**: O número de contêineres de blob no serviço de Blob da conta de armazenamento.

- **ObjectCount**: O número de blobs de página ou bloco dedicadas e não confirmados no serviço de Blob da conta de armazenamento.

Para saber mais sobre as métricas de capacidade, consulte [Esquema de tabela de métricas de análise de armazenamento](https://msdn.microsoft.com/library/hh343264.aspx).

### <a name="how-metrics-are-stored"></a>Como métricas são armazenadas

Todos os dados de métricas para cada um dos serviços de armazenamento são armazenados em três tabelas reservadas para esse serviço: uma tabela para obter informações de transação, uma tabela para obter informações de transação minuto e outra tabela para obter informações de capacidade. Informações de transação de transação e minuto consistem em dados de solicitação e resposta e informações de capacidade consistem em dados de uso de armazenamento. Métricas de hora, minutos métricas e capacidade de Blob serviço de uma conta de armazenamento podem ser acessadas em tabelas que são nomeadas conforme descrito na tabela a seguir.

| Nível de métricas                         | Nomes de tabela                                                                                                                   | Versões suportadas                                                                                                                        |
|------------------------------------   |-----------------------------------------------------------------------------------------------------------------------------  |---------------------------------------------------------------------------------------------------------------------------------------------- |
| Métricas por hora, local principal      |  $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue                                                  | Versões anteriores de 2013-08-15 somente. Embora esses nomes ainda são suportados, é recomendável que você alternar usando as tabelas listadas abaixo.  |
| Métricas por hora, local principal      | $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue               | Todas as versões, incluindo 2013-08-15.                                                                                                               |
| Minutos métricas, local principal      | $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue         | Todas as versões, incluindo 2013-08-15.                                                                                                               |
| Métricas por hora, local secundário    | $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue         | Todas as versões, incluindo 2013-08-15. Replicação de localização geográfica redundantes de acesso de leitura deve ser habilitada.                                                    |
| Minutos métricas, local secundário    | $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue   | Todas as versões, incluindo 2013-08-15. Replicação de localização geográfica redundantes de acesso de leitura deve ser habilitada.                                                    |
| Capacidade (somente serviço Blob)          | $MetricsCapacityBlob                                                                                                          | Todas as versões, incluindo 2013-08-15.                                                                                                               |


Estas tabelas são criadas automaticamente quando a análise de armazenamento está habilitada para uma conta de armazenamento. Eles são acessados através do espaço para nome da conta de armazenamento, por exemplo:`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Acessando dados de métricas

Todos os dados nas tabelas de métricas podem ser acessadas usando o serviço de tabela APIs, incluindo as APIs do .NET fornecidas pelo Azure gerenciada biblioteca. O administrador de conta de armazenamento pode ler e excluir entidades de tabela, mas não é possível criar ou atualizá-los.

## <a name="billing-for-storage-analytics"></a>Cobrança para análise de armazenamento

Análise de armazenamento está habilitada por um proprietário de conta de armazenamento; ele não está habilitado por padrão. Todos os dados de métricas escrito pelos serviços de uma conta de armazenamento. Como resultado, cada operação de gravação realizada por análise de armazenamento é faturável. Além disso, a quantidade de armazenamento usada pelos dados de métricas também é faturável.

As seguintes ações executadas pela análise de armazenamento são faturáveis:

- Solicitações para criar blobs de log. 

- Solicitações para criar entidades de tabela para métricas.

Se você tiver configurado uma política de retenção de dados, você não é cobrados para excluir transações quando a análise de armazenamento exclui dados de registro em log e métricas antigos. No entanto, as transações de exclusão de um cliente são faturáveis. Para obter mais informações sobre políticas de retenção, consulte [definir uma política de retenção de dados de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Noções básicas sobre solicitações faturáveis

Todas as solicitações feitas ao serviço de armazenamento da conta é faturáveis ou não faturáveis. Análise de armazenamento registra cada solicitação individual feita a um serviço, incluindo uma mensagem de status que indica como a solicitação foi tratada. Da mesma forma, a análise de armazenamento armazena métricas para um serviço e as operações de API do serviço, incluindo as porcentagens e a contagem de certas mensagens de status. Juntos, esses recursos podem ajudá-lo a analisar suas solicitações faturáveis, fazer melhorias em seu aplicativo e diagnosticar problemas com solicitações de seus serviços. Para obter mais informações sobre faturamento, consulte [Noções básicas sobre Azure armazenamento cobrança - largura de banda, transações e capacidade](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Ao analisar dados de análise de armazenamento, você pode usar as tabelas no tópico com as [operações de logon de análise de armazenamento e mensagens de Status](https://msdn.microsoft.com/library/azure/hh343260.aspx) para determinar quais solicitações são faturáveis. Em seguida, você pode comparar os logs e dados de métricas para as mensagens de status para ver se cobradas uma determinada solicitação. Você também pode usar as tabelas no tópico anterior para investigar a disponibilidade de um serviço de armazenamento ou operação de API individual.

## <a name="next-steps"></a>Próximas etapas

### <a name="setting-up-storage-analytics"></a>Configurando a análise de armazenamento
- [Monitorar uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md)
- [Habilitar e configurar a análise de armazenamento](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Log de análise de armazenamento  
- [Sobre o log de análise de armazenamento](https://msdn.microsoft.com/library/hh343262.aspx)
- [Formato de Log de análise de armazenamento](https://msdn.microsoft.com/library/hh343259.aspx)
- [Análise de armazenamento conectado operações e mensagens de Status](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Métricas de análise de armazenamento
- [Sobre métricas de análise de armazenamento](https://msdn.microsoft.com/library/hh343258.aspx)
- [Esquema de tabela de métricas de análise de armazenamento](https://msdn.microsoft.com/library/hh343264.aspx)
- [Análise de armazenamento conectado operações e mensagens de Status](https://msdn.microsoft.com/library/hh343260.aspx)  
