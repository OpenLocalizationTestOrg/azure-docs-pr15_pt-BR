<properties
    pageTitle="Fluxo de saídas de análise: opções de armazenamento, análise | Microsoft Azure"
    description="Saiba mais sobre opções de saídas de dados de análise de fluxo incluindo Power BI para resultados de análise de direcionamento."
    keywords="transformação de dados, resultados de análise, opções de armazenamento de dados"
    services="stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage"
    documentationCenter="" 
    authors="jeffstokes72"
    manager="jhubbard" 
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="stream-analytics-outputs-options-for-storage-analysis"></a>Fluxo de saídas de análise: opções de armazenamento, análise

Quando um trabalho de análise de fluxo de criação, considere como os dados resultantes serão consumidos. Como você exibe os resultados do trabalho a análise de fluxo e onde você armazenará?

Para habilitar uma variedade de padrões de aplicativo, a análise de fluxo Azure tem opções diferentes para armazenar a saída e exibir resultados da análise. Isso torna mais fácil exibir a saída de trabalho e fornece flexibilidade na consumo e armazenamento de saída do trabalho para data warehouse e outros fins. Qualquer saída configurada no trabalho deve existir antes do trabalho é iniciado e eventos iniciar fluindo. Por exemplo, se você usar o armazenamento de Blob como uma saída, o trabalho não criará uma conta de armazenamento automaticamente. Ele precisa ser criados pelo usuário antes do trabalho ASA é iniciado.

## <a name="azure-data-lake-store"></a>Armazenamento de Lucerne de dados do Azure

Análise de fluxo oferece suporte para [Armazenamento de Lucerne de dados do Azure](https://azure.microsoft.com/services/data-lake-store/). Esse armazenamento permite armazenar dados de qualquer tamanho, tipo e inclusão a velocidade para análise operacional e exploratório. No momento, criação e configuração de saídas de armazenamento de Lucerne de dados é suportada somente no Portal de clássico do Azure. Além disso, a análise de fluxo deve ser autorizado a acessar o repositório de Lucerne de dados. Para obter detalhes sobre como se inscrever para visualização de repositório de dados Lucerne (se necessário) e a autorização são discutidas no [artigo de saída Lucerne de dados](stream-analytics-data-lake-output.md).

### <a name="authorize-an-azure-data-lake-store"></a>Autorize uma loja de Lucerne de dados do Azure

Quando o armazenamento de Lucerne de dados for selecionado como uma saída no portal de gerenciamento do Azure, você será solicitado a autorizar uma conexão para um armazenamento de Lucerne de dados existente.  

![Autorize dados Lucerne Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

Em seguida, preencha as propriedades para a saída de dados Lucerne Store conforme mostrado abaixo:

![Autorize dados Lucerne Store](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

A tabela a seguir lista os nomes de propriedade e sua descrição necessárias para criar uma saída de dados Lucerne Store.

<table>
<tbody>
<tr>
<td><B>NOME DA PROPRIEDADE</B></td>
<td><B>DESCRIÇÃO</B></td>
</tr>
<tr>
<td>Alias de saída</td>
<td>Este é um nome amigável usado em consultas para direcionar a saída da consulta para esse armazenamento de Lucerne de dados.</td>
</tr>
<tr>
<td>Nome da conta</td>
<td>O nome da conta de armazenamento de Lucerne de dados onde você está enviando a saída. Você será apresentada com uma lista de contas de armazenamento de Lucerne de dados à qual o usuário conectado ao portal tem acesso ao suspensa.</td>
</tr>
<tr>
<td>Padrão de prefixo do caminho [<I>opcional</I>]</td>
<td>O caminho de arquivo usado para gravar seus arquivos na conta do repositório de Lucerne dados especificado. <BR>{date}, {time}<BR>Exemplo 1: Pasta1/logs / {date} / {tempo}<BR>Exemplo 2: Pasta1/logs / {date}</td>
</tr>
<tr>
<td>Formato de data [<I>opcional</I>]</td>
<td>Se o token de data é usado no caminho do prefixo, você pode selecionar o formato de data em que os arquivos são organizados. Exemplo: DD/MM/AAAA</td>
</tr>
<tr>
<td>Formato de hora [<I>opcional</I>]</td>
<td>Se o token de tempo é usado no caminho do prefixo, especifique o formato de hora em que os arquivos são organizados. Atualmente o único valor com suporte é HH.</td>
</tr>
<tr>
<td>Formato de serialização de evento</td>
<td>Formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro.</td>
</tr>
<tr>
<td>Codificação</td>
<td>Se o formato CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Só aplicável para serialização CSV. Análise de fluxo oferece suporte a um número de delimitadores comuns para publicar série de dados CSV. Valores com suporte são vírgula, ponto e vírgula, espaço, tabulação e barra vertical.</td>
</tr>
<tr>
<td>Formato</td>
<td>Só aplicável para serialização JSON. Linha separada Especifica que a saída seja formatada fazendo com que cada objeto JSON separado por uma nova linha. Matriz Especifica que a saída seja formatada como uma matriz de objetos JSON.</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Renovar dados Lucerne Store autorização

Você precisará autenticar novamente a sua conta de armazenamento de Lucerne de dados se a sua senha foi alterado desde que seu trabalho foi criado ou última autenticado.

![Autorize dados Lucerne Store](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  


## <a name="sql-database"></a>Banco de dados SQL

[Banco de dados do SQL Azure](https://azure.microsoft.com/services/sql-database/) podem ser usados como uma saída de dados que é relacionais natureza ou de aplicativos que dependem de conteúdo está sendo hospedado em um banco de dados relacional. Trabalhos de análise de fluxo gravará a uma tabela existente em um banco de dados do SQL Azure.  Observe que o esquema de tabela deve corresponder exatamente os campos e seus tipos sendo saída do seu trabalho. Um [Depósito de dados do SQL Azure](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) também pode ser especificado como uma saída via a banco de dados SQL saída opção também (Este é um recurso de visualização). A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída de banco de dados SQL.

| Nome da propriedade | Descrição |
|---------------|-------------|
| Alias de saída | Este é um nome amigável usado em consultas para direcionar a saída da consulta ao banco de dados. |
| Banco de dados | O nome do banco de dados onde você está enviando a saída |
| Nome do servidor | O nome do servidor de banco de dados SQL |
| Nome de usuário | O nome de usuário que tem acesso a gravar o banco de dados |
| Senha | A senha para se conectar ao banco de dados |
| Tabela | O nome da tabela onde a saída será gravada. O nome da tabela diferencia maiusculas de minúsculas e o esquema desta tabela deve corresponder exatamente ao número de campos e seus tipos sendo gerados pela saída de trabalho. |

> [AZURE.NOTE] Atualmente a oferta de banco de dados do Azure SQL tem suporte para uma saída de trabalho na análise de fluxo. No entanto, não há suporte para uma máquina Virtual do Azure executando SQL Server com um banco de dados anexado. Isso está sujeita a alterações em versões futuras.

## <a name="blob-storage"></a>Armazenamento de blob

Armazenamento de blob oferece uma solução econômica e escalável para armazenar grandes quantidades de dados não estruturados na nuvem.  Para obter uma introdução no armazenamento de Blob do Azure e seu uso, consulte a documentação em [como usar Blobs](../storage/storage-dotnet-how-to-use-blobs.md).

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída de blob.

<table>
<tbody>
<tr>
<td>NOME DA PROPRIEDADE</td>
<td>DESCRIÇÃO</td>
</tr>
<tr>
<td>Alias de saída</td>
<td>Este é um nome amigável usado em consultas para direcionar a saída de consulta para este armazenamento de blob.</td>
</tr>
<tr>
<td>Conta de armazenamento</td>
<td>O nome da conta de armazenamento onde você está enviando a saída.</td>
</tr>
<tr>
<td>Chave da conta de armazenamento</td>
<td>A chave secreta associada à conta de armazenamento.</td>
</tr>
<tr>
<td>Contêiner de armazenamento</td>
<td>Os contêineres fornecem um agrupamento lógico para blobs armazenados no serviço do Microsoft Azure Blob. Quando você carrega um blob serviço Blob, você deve especificar um contêiner de blob.</td>
</tr>
<tr>
<td>Padrão de prefixo do caminho [opcional]</td>
<td>O caminho de arquivo usado para gravar sua blobs dentro do contêiner especificado.<BR>Dentro do caminho, você pode optar por usar instâncias de uma ou mais das seguintes 2 variáveis para especificar a frequência blobs escritas:<BR>{date}, {time}<BR>Exemplo 1: cluster1/logs / {date} / {tempo}<BR>Exemplo 2: cluster1/logs / {date}</td>
</tr>
<tr>
<td>Formato de data [opcional]</td>
<td>Se o token de data é usado no caminho do prefixo, você pode selecionar o formato de data em que os arquivos são organizados. Exemplo: DD/MM/AAAA</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Se o token de tempo é usado no caminho do prefixo, especifique o formato de hora em que os arquivos são organizados. Atualmente o único valor com suporte é HH.</td>
</tr>
<tr>
<td>Formato de serialização de evento</td>
<td>Formato de serialização para dados de saída.  Há suporte para JSON, CSV e Avro.</td>
</tr>
<tr>
<td>Codificação</td>
<td>Se o formato CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Só aplicável para serialização CSV. Análise de fluxo oferece suporte a um número de delimitadores comuns para publicar série de dados CSV. Valores com suporte são vírgula, ponto e vírgula, espaço, tabulação e barra vertical.</td>
</tr>
<tr>
<td>Formato</td>
<td>Só aplicável para serialização JSON. Linha separada Especifica que a saída seja formatada fazendo com que cada objeto JSON separado por uma nova linha. Matriz Especifica que a saída seja formatada como uma matriz de objetos JSON.</td>
</tr>
</tbody>
</table>

## <a name="event-hub"></a>Hub de evento

[Hubs de evento](https://azure.microsoft.com/services/event-hubs/) é um altamente escaláveis publicação assinatura ingestor de evento. Ele pode coletar milhões de eventos por segundo.  Um uso de um Hub de evento como saída é quando a saída de um trabalho de análise de fluxo será a entrada de outro fluxo de trabalho.

Há alguns parâmetros que são necessários para configurar fluxos de dados do Hub de evento como uma saída.

| Nome da propriedade | Descrição |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de saída | Este é um nome amigável usado em consultas para direcionar a saída de consulta para este Hub de evento. |
| Namespace do barramento de serviço | Um namespace barramento de serviço é um contêiner para um conjunto de entidades de mensagens. Quando você criou um novo Hub de evento, você também criou um namespace barramento de serviço |
| Hub de evento | O nome da saída Hub de evento |
| Nome de política do Hub de evento | A política de acesso compartilhado, que pode ser criada na guia evento Hub configurar. Cada política de acesso compartilhado terá um nome, permissões que você definiu, e as teclas de acesso |
| Chave de política de Hub de evento | A tecla de acesso compartilhado usada para autenticar o acesso ao namespace barramento de serviço |
| Coluna de chave de partição [opcional] | Esta coluna contém a chave da partição para saída do Hub de evento. |
| Formato de serialização de evento | Formato de serialização para dados de saída.  Há suporte para JSON, CSV e Avro. |
| Codificação | Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento |
| Delimitador | Só aplicável para serialização CSV. Análise de fluxo oferece suporte a um número de delimitadores comuns para dados serializados no formato CSV. Valores com suporte são vírgula, ponto e vírgula, espaço, tabulação e barra vertical. |
| Formato | Só aplicável para tipo de JSON. Linha separada Especifica que a saída seja formatada fazendo com que cada objeto JSON separado por uma nova linha. Matriz Especifica que a saída seja formatada como uma matriz de objetos JSON. |

## <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com/) podem ser usados como uma saída de um trabalho de análise de fluxo para oferecer uma experiência avançada de visualização de resultados de análise. Esse recurso pode ser usado para painéis operacionais, geração de relatórios e métrica controlado pelo relatório.

### <a name="authorize-a-power-bi-account"></a>Autorize uma conta do Power BI

1.  Quando o Power BI é selecionado como uma saída no portal de gerenciamento do Azure, você será solicitado para autorizar um usuário de BI do Power existente ou criar uma nova conta do Power BI.  

    ![Autorizar usuário do Power BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  

2.  Crie uma nova conta se você não ainda tiver um, clique em autorizar agora.  Uma tela semelhante ao seguinte é apresentada.  

    ![Conta do Microsoft Azure Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  

3.  Nesta etapa, forneça a conta corporativa ou de estudante para autorizar a saída do Power BI. Se você não ainda tiver entrado para cima para o Power BI, escolha logon agora. A conta corporativa ou de estudante usada para o Power BI pode ser diferente da conta do Azure assinatura que você está conectado no momento.

### <a name="configure-the-power-bi-output-properties"></a>Configurar as propriedades de saída do Power BI

Uma vez que a conta de Power BI autenticada, você pode configurar as propriedades de saída do Power BI. A tabela a seguir é a lista de nomes de propriedade e sua descrição para configurar a saída do Power BI.

| Nome da propriedade | Descrição |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de saída | Este é um nome amigável usado em consultas para direcionar a saída da consulta a essa saída PowerBI. |
| Espaço de trabalho de grupo | Para habilitar o compartilhamento de dados com outros usuários do Power BI, você pode selecionar grupos dentro de sua conta do Power BI ou escolha "Meu espaço de trabalho" se não quiser escrever a um grupo.  Atualizar um grupo existente, é necessário renovando a autenticação do Power BI. | 
| Nome do conjunto de dados | Forneça um nome de conjunto de dados que ele seja aquele desejado para a saída de Power BI usar |
| Nome da tabela | Forneça um nome de tabela sob o conjunto de dados de saída do Power BI. Atualmente, saída do Power BI de trabalhos de análise de fluxo só pode ter uma tabela em um conjunto de dados |

Para uma Noções básicas de configuração de uma saída de Power BI e o painel de controle, consulte o artigo de [análise de fluxo de Azure & Power BI](stream-analytics-power-bi-dashboard.md) .

> [AZURE.NOTE] Não crie explicitamente o conjunto de dados e a tabela no painel do Power BI. O conjunto de dados e a tabela serão preenchidas automaticamente quando o trabalho é iniciado e o trabalho começa saída bombeamento no Power BI. Observe que se a consulta de trabalho não gera resultados, o conjunto de dados e a tabela não serão criados. Além disso, lembre-se de que se Power BI já tinha um conjunto de dados e a tabela com o mesmo nome fornecida neste trabalho de análise de fluxo, os dados existentes serão substituídos.

### <a name="renew-power-bi-authorization"></a>Renovar autorização do Power BI

Você precisará autenticar novamente a sua conta do Power BI se sua senha foi alterado desde que seu trabalho foi criado ou última autenticado. Se a autenticação multifator (MFA) está configurado no seu locatário do Azure Active Directory (AAD) você também precisará renovar autorização de Power BI cada 2 semanas. Um sintoma desse problema é nenhuma saída de trabalho e um "Erro de usuário autenticar" em Logs de operação:

  ![Erro de símbolo de atualização do Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Para resolver esse problema, interromper o seu trabalho em execução e vá para a saída do Power BI.  Clique no link "Renovar autorização" e reinicie o trabalho da última vez interrompido para evitar a perda de dados.

  ![Autorização de renovação do Power BI](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Armazenamento de tabela

[Armazenamento de tabela do Azure](../storage/storage-introduction.md) oferece armazenamento altamente disponível e amplamente escaláveis, para que um aplicativo pode dimensionar automaticamente para atender à demanda do usuário. Armazenamento de tabela é armazenamento de chave/atributo de NoSQL da Microsoft qual delas pode aproveitar para dados estruturados com menos restrições sobre o esquema. Armazenamento de tabela do Azure pode ser usado para armazenar dados para persistência e recuperação eficiente.

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída de tabela.

| Nome da propriedade | Descrição |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de saída | Este é um nome amigável usado em consultas para direcionar a saída de consulta para o armazenamento de tabela. |
| Conta de armazenamento | O nome da conta de armazenamento onde você está enviando a saída. |
| Chave da conta de armazenamento | A tecla de acesso associada à conta de armazenamento. |
| Nome da tabela | O nome da tabela. A tabela será obter criada se ele não existir. |
| Chave da partição | O nome da coluna de saída que contém a chave de partição. A chave de partição é um identificador exclusivo para a partição dentro de uma determinada tabela que a primeira parte da chave primária da entidade de formulários. É um valor de cadeia de caracteres que pode ser até 1 KB em tamanho. |
| Chave de linha | O nome da coluna de saída que contém a chave de linha. A chave de linha é um identificador exclusivo para uma entidade dentro de uma determinada partição. Ele forma a segunda parte da chave primária da entidade. A chave de linha é um valor de cadeia de caracteres que pode ser até 1 KB em tamanho. |
| Tamanho do lote | O número de registros para uma operação em lotes. Geralmente, o padrão é suficiente para a maioria dos trabalhos, consulte a [especificação da operação de lote de tabela](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) para obter mais detalhes sobre como modificar essa configuração. |

## <a name="service-bus-queues"></a>Filas do barramento de serviço

[Filas de barramento de serviço](https://msdn.microsoft.com/library/azure/hh367516.aspx) oferecem um primeiro In, entrega de mensagens de PEPS (primeiro) para um ou mais consumidores concorrência. Normalmente, as mensagens devem ser recebidos e processados pelos receptores na temporal ordem em que eles foram adicionados à fila, e cada mensagem é recebida e processada pelo consumidor de apenas uma mensagem.

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída de fila.

| Nome da propriedade | Descrição |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de saída | Este é um nome amigável usado em consultas para direcionar a saída da consulta para essa fila de barramento de serviço. |
| Namespace do barramento de serviço | Um namespace barramento de serviço é um contêiner para um conjunto de entidades de mensagens. |
| Nome da fila | O nome da fila de barramento de serviço. |
| Nome da política de fila | Quando você cria uma fila, você também pode criar políticas de acesso compartilhado na guia Configurar fila. Cada política de acesso compartilhado terá um nome, permissões que você definiu, e as teclas de acesso. |
| Chave de política de fila | A tecla de acesso compartilhado usada para autenticar o acesso ao namespace barramento de serviço |
| Formato de serialização de evento | Formato de serialização para dados de saída.  Há suporte para JSON, CSV e Avro. |
| Codificação | Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento |
| Delimitador | Só aplicável para serialização CSV. Análise de fluxo oferece suporte a um número de delimitadores comuns para dados serializados no formato CSV. Valores com suporte são vírgula, ponto e vírgula, espaço, tabulação e barra vertical. |
| Formato | Só aplicável para tipo de JSON. Linha separada Especifica que a saída seja formatada fazendo com que cada objeto JSON separado por uma nova linha. Matriz Especifica que a saída seja formatada como uma matriz de objetos JSON. |

## <a name="service-bus-topics"></a>Tópicos de barramento de serviço

Enquanto filas de barramento de serviço fornecem um método de comunicação de um-para-um remetente para o receptor, o [Serviço barramento tópicos](https://msdn.microsoft.com/library/azure/hh367516.aspx) fornecer um formulário de um-para-muitos de comunicação.

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída de tabela.

| Nome da propriedade | Descrição |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de saída | Este é um nome amigável usado em consultas para direcionar a saída da consulta este tópico de barramento de serviço. |
| Namespace do barramento de serviço | Um namespace barramento de serviço é um contêiner para um conjunto de entidades de mensagens. Quando você criou um novo Hub de evento, você também criou um namespace barramento de serviço |
| Nome do tópico | Tópicos são mensagens entidades, semelhantes às filas e hubs de evento. Eles foram projetados para coletar fluxos de eventos de um número de diferentes dispositivos e serviços. Quando um tópico for criado, ele também recebe um nome específico. As mensagens enviadas para um tópico não estará disponíveis a menos que uma assinatura for criada, isso certifique-se de que há uma ou mais assinaturas sob o tópico |
| Nome de política do tópico | Quando você cria um tópico, você também pode criar políticas de acesso compartilhado na guia Configurar tópico. Cada política de acesso compartilhado terá um nome, permissões que você definiu, e as teclas de acesso |
| Chave de política de tópico | A tecla de acesso compartilhado usada para autenticar o acesso ao namespace barramento de serviço |
| Formato de serialização de evento | Formato de serialização para dados de saída.  Há suporte para JSON, CSV e Avro. |
| Codificação | Se o formato CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento |
| Delimitador | Só aplicável para serialização CSV. Análise de fluxo oferece suporte a um número de delimitadores comuns para dados serializados no formato CSV. Valores com suporte são vírgula, ponto e vírgula, espaço, tabulação e barra vertical. |

## <a name="documentdb"></a>DocumentDB

[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) é um totalmente gerenciados NoSQL documento banco de dados serviço que oferece a consulta e transações via dados sem esquema, desempenho previsível e confiável e desenvolvimento rápido.

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída de DocumentDB.

<table>
<tbody>
<tr>
<td>NOME DA PROPRIEDADE</td>
<td>DESCRIÇÃO</td>
</tr>
<tr>
<td>Nome da conta</td>
<td>O nome da conta de DocumentDB.  Isso também pode ser o ponto de extremidade da conta.</td>
</tr>
<tr>
<td>Chave da conta</td>
<td>A chave de acesso compartilhado para a conta de DocumentDB.</td>
</tr>
<tr>
<td>Banco de dados</td>
<td>O nome do banco de dados de DocumentDB.</td>
</tr>
<tr>
<td>Conjunto padrão de nome</td>
<td>O padrão de nome de conjunto das coleções de arquivos a ser usado. O formato de nome do conjunto pode ser construído usando o token de opcional {partição}, onde partições Iniciar de 0.<BR>Por exemplo A seguir é entradas válidas:<BR>MyCollection {partição}<BR>MyCollection<BR>Observe que as coleções devem existir antes do trabalho de análise de fluxo é iniciado e não será criado automaticamente.</td>
</tr>
<tr>
<td>Chave da partição</td>
<td>O nome do campo em eventos de saída usado para especificar a chave para a partição de saída entre conjuntos.</td>
</tr>
<tr>
<td>ID do documento</td>
<td>O nome do campo em eventos de saída usado para especificar a chave primária que inserir ou atualizar operações se baseiam.</td>
</tr>
</tbody>
</table>


## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
Você já foram introduzidas análise de fluxo, um serviço gerenciado do streaming a análise de dados da Internet das coisas. Para saber mais sobre este serviço, consulte:

- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
