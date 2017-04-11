<properties 
    pageTitle="Ponta a ponta usando métricas de armazenamento do Azure e registro em log, AzCopy e analisador de mensagem de solução de problemas | Microsoft Azure" 
    description="Um tutorial demonstrando a solução de problemas de ponta a ponta com a análise de armazenamento do Azure, AzCopy e analisador de mensagem do Microsoft" 
    services="storage" 
    documentationCenter="dotnet" 
    authors="robinsh" 
    manager="carmonm"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/03/2016" 
    ms.author="robinsh"/>

# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Solução de problemas de ponta a ponta usando métricas de armazenamento do Azure e registro em log, AzCopy e analisador de mensagem 

[AZURE.INCLUDE [storage-selector-portal-e2e-troubleshooting](../../includes/storage-selector-portal-e2e-troubleshooting.md)]

## <a name="overview"></a>Visão geral

Diagnosticar e solução de problemas são uma habilidade importante para a construção e aplicativos cliente com armazenamento do Microsoft Azure de suporte. Devido a natureza distribuída de um aplicativo do Azure, diagnosticar e solucionar problemas de desempenho e erros podem ser mais complexas do que em ambientes tradicionais.

Neste tutorial, demonstraremos como identificar cliente determinados erros que podem afetar o desempenho e solucionar esses erros de ponta a ponta usando ferramentas fornecidas pela Microsoft e armazenamento do Azure, para otimizar o aplicativo cliente. 

Esse tutorial fornece uma exploração prática de um cenário de solução de problemas de ponta a ponta. Para um guia conceitual detalhado para solucionar problemas de aplicativos de armazenamento do Azure, consulte [Monitor, diagnosticar e solucionar problemas de armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md). 

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Ferramentas para solucionar problemas de aplicativos de armazenamento do Azure

Para solucionar aplicativos cliente usando o armazenamento do Microsoft Azure, você pode usar uma combinação das ferramentas para determinar quando ocorreu um problema e o que pode ser a causa do problema. Essas ferramentas incluem:

- **Análise de armazenamento do azure**. [Análise de armazenamento do Azure](http://msdn.microsoft.com/library/azure/hh343270.aspx) fornece métricas e registro em log para o armazenamento do Azure.
    - **Métricas de armazenamento** rastreia métricas de transação e métricas de capacidade para a sua conta de armazenamento. Usando métricas, você pode determinar o desempenho do seu aplicativo de acordo com uma variedade de medidas diferentes. Consulte o [Esquema de tabela de métricas de análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343264.aspx) para obter mais informações sobre os tipos de métricas acompanhados pelo análise de armazenamento. 

    - **Armazenamento de registro em log** registra cada solicitação para os serviços de armazenamento do Azure em um log do lado do servidor. O log rastreia dados detalhados para cada solicitação, incluindo a operação executada, o status da operação e informações de latência. Consulte o [Formato de Log de análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343259.aspx) para obter mais informações sobre os dados de solicitação e resposta escrito para os logs pela análise de armazenamento.

> [AZURE.NOTE] Contas de armazenamento com um tipo de replicação de armazenamento redundantes de zona (ZRS) não tem o métricas ou o recurso de log habilitado no momento. 

- **Azure Portal clássico**. Você pode configurar métricas e registro em log para sua conta de armazenamento no [Portal de clássico do Azure](https://manage.windowsazure.com). Você também pode exibir gráficos e diagramas que mostram como seu aplicativo é o desempenho ao longo do tempo e configure alertas para notificá-lo a se seu aplicativo executa de forma diferente do esperado de uma métrica especificada. 
    
    Consulte [Monitor de uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md) para obter informações sobre como configurar o monitoramento no Portal de clássico do Azure.

- **AzCopy**. Logs do servidor para o armazenamento do Azure são armazenados como blobs, para que você possa usar AzCopy para copiar os blobs de log para um diretório local para análise usando o analisador de mensagem do Microsoft. Consulte [transferir dados com o utilitário de AzCopy de linha de comando](storage-use-azcopy.md) para obter mais informações sobre AzCopy.

- **Analisador de mensagens da Microsoft**. Analisador de mensagem é uma ferramenta que consome arquivos de log e exibe dados de log em um formato visual que torna mais fácil para filtrar, pesquisar e dados de log do grupo em conjuntos úteis que você pode usar para analisar os erros e problemas de desempenho. Consulte [O guia de operando de análise do Microsoft mensagem](http://technet.microsoft.com/library/jj649776.aspx) para obter mais informações sobre o analisador de mensagem.

## <a name="about-the-sample-scenario"></a>Sobre o cenário de amostra

Neste tutorial, examinaremos um cenário onde métricas de armazenamento do Azure indica uma taxa de sucesso de porcentagem baixa para um aplicativo que chama o armazenamento do Azure. A métrica de taxa de sucesso de porcentagem baixa (mostrada como **PercentSuccess** no Portal de clássico do Azure e nas tabelas de métricas) rastreia operações que êxito, mas que retornam um código de status HTTP que seja maior que 299. Nos arquivos de log de armazenamento do servidor, essas operações são registradas com um status de transação de **ClientOtherErrors**. Para obter mais detalhes sobre a métrica de sucesso porcentagem baixa, consulte [métricas mostram PercentSuccess baixa ou entradas de log de análise tem operações com status de ClientOtherErrors transação](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Operações de armazenamento Azure podem retornar códigos de status HTTP maior 299 como parte de sua funcionalidade normal. Mas esses erros em alguns casos indicam que você poderá otimizar seu aplicativo cliente para melhorar o desempenho. 

Neste cenário, consideraremos uma taxa de sucesso de porcentagem baixa sejam nada abaixo de 100%. Você pode escolher um nível de métrica diferente, no entanto, acordo com suas necessidades. Recomendamos que durante o teste do seu aplicativo, você estabelece uma tolerância de linha de base para seu métricas de chave de desempenho. Por exemplo, você pode determinar, com base nos testes, que o seu aplicativo deve ter uma taxa de sucesso de porcentagem consistente de 90% ou 85%. Se seus dados de métricas mostram que o aplicativo é desviam-se desse número, você pode investigar o que pode estar causando o aumento. 

Para o nosso cenário de amostra, depois que já estabelecidos a métrica de taxa de porcentagem de sucesso é abaixo de 100%, examinaremos os logs para localizar os erros que correlação para as métricas e usá-los para descobrir o que está causando a taxa de sucesso porcentagem inferior. Vamos examinar especificamente erros no intervalo 400. Em seguida, vamos mais detalhadamente investigar 404 erros (não encontrado).

### <a name="some-causes-of-400-range-errors"></a>Algumas causas dos erros de 400-intervalo

Os exemplos abaixo mostra uma amostra de alguns erros de 400-intervalo para solicitações contra o armazenamento de Blob do Azure e suas possíveis causas. Qualquer um desses erros, bem como erros no intervalo 300 e o intervalo de 500, podem contribuir para uma taxa de sucesso de porcentagem baixa. 

Observe que as listas abaixo estão longe de ser concluída. Consulte [Status e códigos de erro](http://msdn.microsoft.com/library/azure/dd179382.aspx) para obter detalhes sobre erros gerais de armazenamento do Azure e sobre erros específicos para cada um dos serviços de armazenamento.

**Exemplos de código 404 (não encontrado) de status**

Ocorre quando uma operação de leitura em relação a um contêiner ou blob falha porque o blob ou recipiente não foi encontrado.

- Ocorre se um contêiner ou blob foi excluído por outro cliente antes desta solicitação. 
- Ocorre se você estiver usando uma chamada de API que cria o contêiner ou blob após verificando se ele existe. As APIs CreateIfNotExists fazer uma chamada de cabeça primeiro para verificar a existência do recipiente ou blob; Se não existir, será retornado um erro 404 e, em seguida, uma segunda colocar chamada é feita para gravar o contêiner ou blob.

**Código de status 409 exemplos (conflito)**

- Ocorre se você usar uma API criar para criar um novo contêiner ou blob, sem verificar existência primeiro e já existe um contêiner ou blob com esse nome. 
- Ocorre se um contêiner está sendo excluído, e você tentar criar um novo recipiente com o mesmo nome antes de concluir a operação de exclusão.
- Ocorre se você especificar uma concessão em um contêiner ou blob e já existe uma concessão presente.
 
**Código de status 412 (pré falhou) exemplos**

- Ocorre quando a condição especificada por um cabeçalho condicional não foi atendida.
- Ocorre quando a ID de concessão especificada não coincidir com a ID de concessão no contêiner ou blob.

## <a name="generate-log-files-for-analysis"></a>Gerar arquivos de log para análise

Neste tutorial, usaremos analisador de mensagem para trabalhar com três tipos diferentes de arquivos de log, embora você poderia escolher trabalhar com qualquer um destes procedimentos:

- O **log do servidor**, que é criado quando você habilitar o log de armazenamento do Azure. O log do servidor contém dados sobre cada operação chamado em relação a um dos serviços de armazenamento do Azure - blob, fila, tabela e arquivo. O log do servidor indica qual operação foi chamada e o código de status foi retornados, bem como outros detalhes sobre a solicitação e resposta.
- O **log de cliente do .NET**, que é criado quando você habilitar o log do lado do cliente de dentro de seu aplicativo .NET. O log de cliente inclui informações detalhadas sobre como o cliente prepara a solicitação e recebe e processa a resposta. 
- O **log de rastreamento de rede HTTP**, que coleta dados em dados de solicitação e resposta HTTP/HTTPS, incluindo para operações contra o armazenamento do Azure. Neste tutorial, podemos vai gerar o rastreamento de rede por meio de analisador de mensagem.

### <a name="configure-server-side-logging-and-metrics"></a>Configurar o log de servidor e métricas

Primeiro, vamos precisará configurar o log de armazenamento do Azure e métricas, para que tenhamos dados do aplicativo cliente para analisar. Você pode configurar o registro em log e métricas de várias maneiras - através de do [Portal de clássico do Azure](https://manage.windowsazure.com), usando o PowerShell, ou programaticamente. Consulte [Habilitando métricas de armazenamento e exibindo dados de métricas](http://msdn.microsoft.com/library/azure/dn782843.aspx) e [Habilitando o log de armazenamento e acessando dados de Log](http://msdn.microsoft.com/library/azure/dn782840.aspx) para obter detalhes sobre como configurar o registro em log e métricas.

**Através do Portal de clássico Azure**

Para configurar o registro em log e métricas para sua conta de armazenamento usando o portal, siga as instruções no [Monitor de uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md).

> [AZURE.NOTE] Não é possível definir métricas minutos usando o Portal de clássico do Azure. No entanto, recomendamos que você defina-los para os fins deste tutorial e para investigar problemas de desempenho com seu aplicativo. Você pode definir métricas minutos usando o PowerShell conforme mostrado abaixo, ou programaticamente ou por meio do Portal de clássico do Azure.
>
> Observe que o Portal de clássico do Azure não é possível exibir minutos métricas, apenas métricas por hora. 

**Por meio do PowerShell**

Para começar a usar o PowerShell para Azure, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

1. Use o cmdlet [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) para adicionar sua conta de usuário Azure na janela do PowerShell:

    ```
    Add-AzureAccount
    ```

2. Na janela de **entrar no Microsoft Azure** , digite o endereço de email e a senha associada a sua conta. Azure autentica salva as informações de credencial e fecha a janela.
3. Defina a conta de armazenamento padrão para a conta de armazenamento que você está usando para o tutorial executando esses comandos na janela do PowerShell:

    ```
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount' 
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName 
    ```

4. Habilite o log de armazenamento para o serviço Blob: 
 
    ```
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0 
    ```
5. Habilitar métricas de armazenamento para o serviço de Blob, lembrando-se de definir **- MetricsType** para `Minute`:

    ```
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0 
    ```

### <a name="configure-net-client-side-logging"></a>Configurar log do lado do cliente .NET

Para configurar o log do lado do cliente para um aplicativo .NET, habilite o diagnóstico de .NET no arquivo de configuração do aplicativo (Web. config ou App). Para obter detalhes, consulte [log do lado do cliente com a biblioteca de cliente de armazenamento do .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx) e [log com o Microsoft Azure armazenamento SDK para Java do lado do cliente](http://msdn.microsoft.com/library/azure/dn782844.aspx) .

O log do lado do cliente inclui informações detalhadas sobre como o cliente prepara a solicitação e recebe e processa a resposta.

A biblioteca de cliente do armazenamento armazena dados de log do lado do cliente no local especificado no arquivo de configuração do aplicativo (Web. config ou App). 

### <a name="collect-a-network-trace"></a>Coletar um rastreamento de rede

Você pode usar o analisador de mensagem para coletar um rastreamento de rede HTTP/HTTPS enquanto seu aplicativo cliente está em execução. Analisador de mensagem usa [Fiddler](http://www.telerik.com/fiddler) no back-end. Antes de coletar o rastreamento de rede, recomendamos que você configure Fiddler para registrar o tráfego HTTPS não criptografado:

1. Instale o [Fiddler](http://www.telerik.com/download/fiddler).
2. Inicie o Fiddler.
2. Selecione ferramentas **| Opções de Fiddler**.
3. Na caixa de diálogo Opções, certifique-se que **Capturar HTTPS conecta** e **Descriptografar o tráfego HTTPS** estão selecionados, conforme mostrado abaixo.

![Configurar opções de Fiddler](./media/storage-e2e-troubleshooting-classic-portal/fiddler-options-1.png)

Para o tutorial, coletar e salvar um rastreamento de rede primeiro no analisador de mensagem e criar uma sessão de análise para analisar o rastreamento e os logs. Para coletar um rastreamento de rede no analisador de mensagem:

1. No analisador de mensagem, selecione **arquivo | Rastreamento rápido | Não criptografado HTTPS**.
2. O rastreamento será iniciado imediatamente. Selecione **Parar** para parar o rastreamento, para que nós podem ser configurados para tráfego de armazenamento de rastreamento somente.
3. Selecione **Editar** para editar a sessão de rastreamento.
4. Selecione o link **Configurar** à direita do provedor ETW **Microsoft-Pef-WebProxy** .
5. Na caixa de diálogo **Configurações avançadas** , clique na guia **provedor** .
6. No campo de **Filtro Hostname** , especifique os pontos de extremidade de armazenamento, separados por espaços. Por exemplo, você pode especificar os pontos de extremidade da seguinte maneira; alterar `storagesample` para o nome da sua conta de armazenamento:
    
    ``` 
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net 
    ```

7. Sair da caixa de diálogo e clique em **Reiniciar** para começar a coleta de rastreamento com o filtro hostname no local, para que somente tráfego de rede de armazenamento do Azure seja incluído no rastreamento.

>[AZURE.NOTE] Após terminar de coletar seu rastreamento de rede, é altamente recomendável que você reverter as configurações que você pode ter alterado no Fiddler para tráfego HTTPS descriptografar. Na caixa de diálogo Opções de Fiddler, desmarque as caixas de seleção **Capturar HTTPS conecta** e **Descriptografar o tráfego HTTPS** .

Consulte [usando os recursos de rastreamento de rede](http://technet.microsoft.com/library/jj674819.aspx) no Technet para obter mais detalhes.

## <a name="review-metrics-data-in-the-azure-classic-portal"></a>Revisar dados de métricas no Portal de clássico do Azure

Depois que seu aplicativo tem foi executado por um período de tempo, você pode examinar os gráficos de métricas que aparecem no Portal de clássico do Azure para observar como o seu serviço tiver sido desempenho. Primeiro, vamos adicionar a métrica de **Porcentagem de sucesso** para a página de monitoramento:

1. Navegue até o painel de controle para sua conta de armazenamento no [Portal de clássico do Azure](https://manage.windowsazure.com)e, em seguida, selecione **Monitor** para exibir a página de monitoramento.
2. Clique em **Adicionar métricas** para exibir a caixa de diálogo **Escolher métricas** .
3. Role para baixo para localizar o grupo de **Porcentagem de sucesso** e expandi-la, selecione **agregadas**, conforme mostrado na figura abaixo. Esta métrica agrega dados de porcentagem de sucesso de todas as operações de Blob.

![Escolha métricas](./media/storage-e2e-troubleshooting-classic-portal/choose-metrics-portal-1.png)

No Portal de clássico do Azure, você verá agora a **Porcentagem de sucesso** no gráfico de monitoramento, juntamente com quaisquer outras medidas que você pode ter adicionado (até seis pode ser exibido no gráfico de uma só vez). Na imagem abaixo, você pode ver que a taxa de porcentagem de sucesso é um pouco abaixo de 100%, que é o cenário que vamos investigar próximo analisando os logs no analisador de mensagem:

![Gráfico de métricas no portal](./media/storage-e2e-troubleshooting-classic-portal/portal-metrics-chart-1.png)

Para obter mais detalhes sobre a adição de métricas para a página de monitoramento, consulte [como: adicionar métricas à tabela métricas](storage-monitor-storage-account.md#how-to-add-metrics-to-the-metrics-table).

> [AZURE.NOTE] Pode levar algum tempo para os seus dados de métricas apareça no Portal de clássico do Azure depois de habilitar métricas de armazenamento. Isso ocorre porque métricas por hora para hora anterior não são exibidas no Portal de clássico do Azure até que a hora atual tenha decorrido. Além disso, métricas de minuto não são exibidas no Portal de clássico do Azure. Portanto dependendo do quando você habilita métricas, pode levar até duas horas para ver dados de métricas.

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Use AzCopy para copiar logs do servidor para um diretório local

Armazenamento do Azure grava dados de log do servidor blobs, enquanto métricas são gravadas em tabelas. Log blobs estão disponíveis no conhecidos `$logs` contêiner para sua conta de armazenamento. Log blobs são nomeados hierarquicamente por ano, mês, dia e hora, para que você possa localizar facilmente o intervalo de tempo que você deseja investigar. Por exemplo, na `storagesample` conta, o contêiner para os blobs de log para 01/02/2015, de 8 e 9 am, é `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Os blobs individuais neste contêiner são nomeados sequencialmente, começando com `000000.log`.

Você pode usar a ferramenta de linha de comando AzCopy para baixar esses arquivos de log do lado do servidor para um local de sua escolha em sua máquina local. Por exemplo, você pode usar o seguinte comando para baixar os arquivos de log para operações de blob ocorridos em 2 de janeiro de 2015 à pasta `C:\Temp\Logs\Server`; substituir `<storageaccountname>` com o nome da sua conta de armazenamento, e `<storageaccountkey>` com a chave de acesso de conta:

    AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V

AzCopy está disponível para download na página [Downloads do Azure](https://azure.microsoft.com/downloads/) . Para obter detalhes sobre como usar AzCopy, consulte [transferir dados com o utilitário de AzCopy de linha de comando](storage-use-azcopy.md).

Para obter informações adicionais sobre download logs do lado do servidor, consulte [log de armazenamento Baixando dados de log](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata). 

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Use o analisador de mensagem do Microsoft para analisar dados de log

Analisador de mensagem da Microsoft é uma ferramenta para capturar, exibir e analisar o tráfego, eventos e outras mensagens do sistema ou aplicativo em cenários de diagnósticos e solução de problemas de mensagens de protocolo. Analisador de mensagem também permite que você carregar, agregar e analisar dados de log e arquivos de rastreamento salvos. Para obter mais informações sobre o analisador de mensagem, consulte [O guia de operando de analisador do Microsoft mensagem](http://technet.microsoft.com/library/jj649776.aspx).

Analisador de mensagem inclui ativos para o armazenamento do Azure que ajudam você a analisar logs de rede, cliente e servidor. Nesta seção, abordaremos como usar essas ferramentas para resolver o problema de sucesso porcentagem baixo em logs de armazenamento.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Baixe e instale o analisador de mensagem e os ativos de armazenamento do Azure

1. Baixar o [Analisador de mensagem](http://www.microsoft.com/download/details.aspx?id=44226) do Microsoft Download Center e execute o instalador.
2. Inicie o analisador de mensagem.
3. No menu **Ferramentas** , selecione **Gerenciador de ativo**. Na caixa de diálogo **Gerenciador de ativos** , selecione **Downloads**e filtrar em **Armazenamento do Azure**. Você verá os ativos de armazenamento do Azure, conforme mostrado na figura abaixo.
4. Clique em **Sincronizar todos os itens exibidos** para instalar os ativos de armazenamento do Azure. Os ativos disponíveis incluem:
    - **Regras de cor de armazenamento do azure:** Regras de cor de armazenamento Azure permitem definir filtros especiais que usar cor, texto e estilos de fonte para realçar mensagens que contêm informações específicas em um rastreamento.
    - **Gráficos de armazenamento do azure:** Gráficos de armazenamento Azure são gráficos predefinidos que o gráfico de dados de log do servidor. Observe que para usar gráficos de armazenamento do Azure neste momento, você pode apenas carregar o log do servidor em grade de análise.
    - **Analisadores de armazenamento do azure:** Os analisadores de armazenamento do Azure analisar o cliente de armazenamento do Azure, servidor e logs HTTP para exibi-las na grade de análise.
    - **Filtros de armazenamento do azure:** Os filtros de armazenamento Azure são critérios predefinidos que você pode usar para seus dados na grade de análise de consulta.
    - **Layouts do modo de armazenamento do azure:** Layouts de modo de exibição de armazenamento Azure são layouts de coluna predefinidos e agrupamentos na grade de análise.
4. Reinicie o analisador de mensagem depois de instalar os ativos.

![Gerenciador de ativos de analisador de mensagem](./media/storage-e2e-troubleshooting-classic-portal/mma-start-page-1.png)

> [AZURE.NOTE] Instale todos os ativos de armazenamento do Azure mostrados para os fins deste tutorial.

### <a name="import-your-log-files-into-message-analyzer"></a>Importar seus arquivos de log para o analisador de mensagem

Você pode importar todos os arquivos de log salvos (servidor, cliente e rede) para uma única sessão no analisador de mensagem do Microsoft para análise.

1. No menu **arquivo** no analisador de mensagem do Microsoft, clique em **Nova sessão**e, em seguida, clique em **Branco na sessão**. Na caixa de diálogo **Nova sessão** , insira um nome para a sua sessão de análise. No painel de **Detalhes da sessão** , clique no botão **arquivos** . 
1. Para carregar os dados de rastreamento de rede gerados pela análise de mensagem, clique em **Adicionar arquivos**, navegue até o local onde você salvou o arquivo de .matp da sua sessão de rastreamento de web, selecione o arquivo .matp e clique em **Abrir**. 
1. Para carregar os dados de log do lado do servidor, clique em **Adicionar arquivos**, navegue até o local onde você baixou os logs do lado do servidor, selecione os arquivos de log para o intervalo de tempo que você deseja analisar e clique em **Abrir**. Em seguida, no painel de **Detalhes da sessão** , defina a **Configuração de Log de texto** suspenso para cada arquivo de log do lado do servidor como **AzureStorageLog** para garantir que o analisador de mensagem do Microsoft pode analisar o arquivo de log corretamente.
1. Para carregar os dados de log do lado do cliente, clique em **Adicionar arquivos**, navegue até o local onde você salvou seus logs do lado do cliente, selecione os arquivos de log que você deseja analisar e clique em **Abrir**. Em seguida, no painel de **Detalhes da sessão** , defina a **Configuração de Log de texto** suspensa para cada arquivo de log do lado do cliente para **AzureStorageClientDotNetV4** para garantir que o analisador de mensagem do Microsoft pode analisar o arquivo de log corretamente.
1. Clique em **Iniciar** na caixa de diálogo **Nova sessão** para carregar e analisar os dados de log. Exibe os dados do log na grade de análise de analisador de mensagem.

A imagem abaixo mostra uma sessão de exemplo configurada com servidor, cliente e arquivos de log de rastreamento de rede.

![Configurar a sessão de analisador de mensagem](./media/storage-e2e-troubleshooting-classic-portal/configure-mma-session-1.png)

Observe que o analisador de mensagem carrega os arquivos de log na memória. Se você tiver um grande conjunto de dados de log, você desejará filtrá-lo para obter o melhor desempenho do analisador de mensagem.

Primeiro, determine o período de tempo que você está interessado em revisão e manter esse período o menor possível. Em muitos casos, você desejará examinar um período de minutos ou horas no máximo. Importe o menor conjunto de logs que pode atender às suas necessidades.

Se você ainda tiver uma grande quantidade de dados de log, você talvez queira especificar uma sessão de filtro para filtrar seus dados de log antes de carregá-lo. Na caixa **Filtro de sessão** , selecione o botão de **biblioteca** para escolher um filtro predefinido; Por exemplo, escolha **globais horário filtro I** dos filtros de armazenamento do Azure para filtrar em um intervalo de tempo. Em seguida, você pode editar os critérios de filtro para especificar os inicial e final carimbo de hora para o intervalo que você deseja ver. Você também pode filtrar um código de status específico; Por exemplo, você pode optar por carregar apenas entradas de log onde o código de status é 404.

Para obter mais informações sobre como importar dados de log para o analisador de mensagem do Microsoft, consulte [Recuperar dados de mensagem](http://technet.microsoft.com/library/dn772437.aspx) no TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Usar a identificação de solicitação de cliente para correlação dados de arquivos de log

A biblioteca de cliente de armazenamento do Azure gera automaticamente um ID de solicitação de cliente exclusivo para cada solicitação. Esse valor é escrito o log de cliente, o log do servidor e o rastreamento de rede, para poder usá-lo para correlação dados entre três logs no analisador de mensagem. Consulte o [ID de solicitação do cliente](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) para obter informações adicionais sobre a solicitação do cliente ID.

As seções a seguir descrevem como usar modos de exibição de layout pré-configurado e personalizados para correlação e agrupar dados com base na identificação de solicitação de cliente.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Selecione um layout de exibição para exibir na grade de análise

Os ativos de armazenamento para analisador de mensagem incluem Layouts de modo de exibição de armazenamento do Azure, que são pré-configurado modos de exibição que você pode usar para exibir seus dados com agrupamentos úteis e colunas para cenários diferentes. Você também pode criar layouts de modo de exibição personalizado e salvá-los para uso posterior. 

A imagem abaixo mostra o menu de **Exibição de Layout** , disponível selecionando **Exibir Layout** na faixa de opções da barra de ferramentas. Os layouts de modo de exibição para o armazenamento do Azure são agrupados sob o nó de **Armazenamento do Azure** no menu. Você pode procurar `Azure Storage` na caixa de pesquisa para filtrar o armazenamento do Azure exibir somente os layouts. Você também pode selecionar a estrela ao lado de um layout de exibição para torná-lo um favorito e exibi-la na parte superior do menu.

![Menu de Layout do modo de exibição](./media/storage-e2e-troubleshooting-classic-portal/view-layout-menu.png)

Começar com, selecione **agrupado por ClientRequestID e módulo**. Este layout de grupos de modo de exibição faça logon dados de três logs primeiro por ID de solicitação de cliente, depois por arquivo de log de origem (ou **módulo** no analisador de mensagem). Com este modo de exibição, você pode fazer busca detalhada em uma ID de solicitação de cliente específico e ver os dados de todos os três arquivos de log para essa solicitação de cliente ID.

A imagem abaixo mostra este modo de exibição de layout aplicado aos dados de log de exemplo, com um subconjunto de colunas exibidas. Você pode ver que, para uma ID de solicitação de cliente específico, a grade de análise exibe dados do log de cliente, o log do servidor e o rastreamento de rede.

![Exibir Layout de armazenamento do Azure](./media/storage-e2e-troubleshooting-classic-portal/view-layout-client-request-id-module.png)

>[AZURE.NOTE] Arquivos de log diferentes têm diferentes colunas, para que quando dados de vários arquivos de log são exibidos na grade de análise, algumas colunas não podem conter quaisquer dados para uma determinada linha. Por exemplo, na imagem acima, as linhas de log de cliente não mostram todos os dados para o **carimbo de hora**, **TimeElapsed**, **origem**e **destino** colunas, porque essas colunas não existem no registro de cliente, mas existe no rastreamento de rede. Da mesma forma, a coluna de **carimbo de hora** exibe dados de carimbo de hora do log de servidor, mas nenhum dado é exibido para as colunas **TimeElapsed**, **origem**e **destino** , que não fazem parte de log do servidor. 

Além de usar os layouts de modo de exibição de armazenamento do Azure, você também pode definir e salvar seus próprios layouts de modo de exibição. Você pode selecionar outros campos desejados para agrupar dados e salvar o agrupamento como parte do seu layout personalizado também. 

### <a name="apply-color-rules-to-the-analysis-grid"></a>Aplicar regras de cor para a grade de análise

Os ativos de armazenamento também incluem regras de cor, que oferecem que um visual significa identificar diferentes tipos de erros na grade de análise. As regras de cores predefinidas se aplicam a erros de HTTP, para que elas apareçam apenas para o rastreamento de rede e de log do servidor.

Para aplicar regras de cor, selecione **Regras de cor** na faixa de opções da barra de ferramentas. Você verá as regras de cor de armazenamento do Azure no menu. Para o tutorial, selecione **Erros de cliente (StatusCode entre 400 e 499)**, conforme mostrado na figura abaixo.

![Exibir Layout de armazenamento do Azure](./media/storage-e2e-troubleshooting-classic-portal/color-rules-menu.png)

Além de usar as regras de cor de armazenamento do Azure, você também pode definir e salvar suas próprias regras de cor.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Agrupar e filtrar dados de log para encontrar erros 400-intervalo

Em seguida, vamos agrupar e filtrar os dados de log para encontrar todos os erros no intervalo 400.

1. Localize a coluna **StatusCode** na grade de análise, clique com botão direito no título da coluna e selecione o **grupo**.
2. Grupo próximo, na coluna **ClientRequestId** . Você verá que os dados na grade de análise agora são organizados por código de status e ID de solicitação de cliente.
1. Exiba a janela da ferramenta de filtro de exibição se ela não ainda estiver visível. Na faixa de ferramentas, selecione a **Ferramenta Windows**, em seguida, o **Filtro de exibição**.
2. Para filtrar os dados de log para exibir somente os erros de 400-intervalo, adicione os seguintes critérios de filtro para a janela de **Filtro de exibição** e clique em **Aplicar**:

        (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)

A imagem abaixo mostra os resultados desse agrupamento e filtro. Por exemplo, expanda o campo **ClientRequestID** sob o agrupamento de código de status 409, mostra uma operação que resultou em que código de status.

![Exibir Layout de armazenamento do Azure](./media/storage-e2e-troubleshooting-classic-portal/400-range-errors1.png)

Depois de aplicar esse filtro, você verá que são excluídas linhas do log do cliente, como o cliente de log não inclui uma coluna de **StatusCode** . Em primeiro lugar, vamos examinar o servidor e logs de rastreamento de rede para localizar 404 erros e, em seguida, podemos vai retornar para o log de cliente para examinar as operações de cliente que resultaram-los.

>[AZURE.NOTE] Você pode filtrar a coluna **StatusCode** e ainda exibir dados de todas as três logs, incluindo o log de cliente, se você adicionar uma expressão para o filtro que inclui entradas de log onde o código de status é nulo. Para construir essa expressão de filtro, use:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code> 
>
> Esse filtro retorna todas as linhas do cliente de log e apenas linhas dos logs do servidor e HTTP onde o código de status é maior que 400. Se você aplicá-lo para o layout de exibição agrupado por módulo e ID de solicitação de cliente, você pode pesquisar ou role por meio das entradas de log para encontrar aquelas onde todos os três logs são representados.   

### <a name="filter-log-data-to-find-404-errors"></a>Filtrar dados de log para encontrar 404 erros

Os ativos de armazenamento incluem filtros predefinidos que você pode usar para restringir os dados de log para localizar os erros ou tendências que você está procurando. Em seguida, podemos aplicará dois filtros predefinidos: um que filtra o servidor e logs de rastreamento de rede para 404 erros e um que filtra os dados em um intervalo de tempo especificado.

1. Exiba a janela da ferramenta de filtro de exibição se ela não ainda estiver visível. Na faixa de ferramentas, selecione a **Ferramenta Windows**, em seguida, o **Filtro de exibição**.
2. Na janela Filtro de exibição, selecione **biblioteca**e pesquise em `Azure Storage` para encontrar o armazenamento do Azure filtros. Selecione o filtro para **404 (não encontrado) mensagens em todos os logs**.
3. Exibir o menu de **biblioteca** novamente e localize e selecione o **Filtro de tempo Global**.
4. Edite os carimbos de hora mostrados no filtro para o intervalo que você deseja exibir. Isso ajudará para restringir o intervalo de dados de análise.
5. Filtrar deve parecer semelhante ao exemplo abaixo. Clique em **Aplicar** para aplicar o filtro à grade de análise.

        ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And 
        (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)

![Exibir Layout de armazenamento do Azure](./media/storage-e2e-troubleshooting-classic-portal/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analisar seus dados de log

Agora que você tiver agrupados e seus dados filtrados, você pode examinar os detalhes de solicitações individuais que gerou 404 erros. No layout de modo de exibição atual, os dados são agrupados por ID de solicitação de cliente, em seguida por fonte log. Como podemos estiver filtrando em solicitações onde o campo StatusCode contém 404, você verá apenas o servidor e os dados de rastreamento de rede, não os dados de log de cliente.

A imagem abaixo mostra uma solicitação específica onde uma operação de obter Blob gerou um 404 porque o blob não existe. Observe que algumas colunas foram removidas do modo de exibição padrão para exibir os dados relevantes.

![Servidor filtrado e Logs de rastreamento de rede](./media/storage-e2e-troubleshooting-classic-portal/server-filtered-404-error.png)

Em seguida, podemos será correlação esta ID de solicitação do cliente com os dados de log de cliente para ver as ações que o cliente estava fazendo quando ocorreu o erro. Você pode exibir um novo modo de exibição de grade de análise para esta sessão exibir os dados de log de cliente, que é aberta em uma segunda guia:

1. Primeiro, copie o valor do campo **ClientRequestId** na área de transferência. Você pode fazer isso selecionando qualquer linha, localizando o campo **ClientRequestId** , clicando no valor de dados e escolhendo **Copiar 'ClientRequestId'**. 
1. Na faixa de ferramentas, selecione **Novo Visualizador**e selecione **Grade de análise** para abrir uma nova guia. A nova guia mostra todos os dados em seus arquivos de log, sem agrupamento, filtragem ou regras de cor. 
2. Na faixa de ferramentas, selecione **Exibir Layout**e selecione **Todas as colunas de cliente .NET** sob a seção de **Armazenamento do Azure** . Esse layout do modo de exibição mostra os dados do cliente de log, bem como os logs de rastreamento de rede e servidor. Por padrão, ela é classificada na coluna **MessageNumber** .
3. Em seguida, pesquise o log de cliente o ID de solicitação de cliente. Na faixa de ferramentas, selecione **Localizar mensagens**e especificar um filtro personalizado na identificação de solicitação de cliente no campo **Localizar** . Use esta sintaxe para o filtro, especificando sua própria identificação de solicitação de cliente:

        *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"

Analisador de mensagem localiza e seleciona a primeira entrada de log onde os critérios de pesquisa corresponde a ID da solicitação de cliente. No registro de cliente, há várias entradas para cada identificação de solicitação de cliente, para que você talvez queira agrupá-los no campo **ClientRequestId** para tornar mais fácil vê-los juntos. A imagem abaixo mostra todas as mensagens no log de cliente para o cliente especificado identificação da solicitação. 

![Mostrando de log de cliente 404 erros](./media/storage-e2e-troubleshooting-classic-portal/client-log-analysis-grid1.png)

Usando os dados mostrados nos layouts de exibição nessas duas guias, você pode analisar os dados de solicitação para determinar o que pode ter causado o erro. Você também pode examinar solicitações que precedidos este um para ver se um evento anterior pode ter resultaram o erro 404. Por exemplo, você pode examinar as entradas de log de cliente anterior essa identificação de solicitação de cliente para determinar se o blob pode ter sido excluído ou se o erro foi devido a aplicação cliente chamar uma API CreateIfNotExists em um contêiner ou blob. No registro de cliente, você pode encontrar o endereço do blob no campo **Descrição** ; no servidor e logs de rastreamento de rede, esta informação aparece no campo **Resumo** .

Quando você souber o endereço do blob que gerou o erro 404, você pode investigar ainda mais. Se você procurar as entradas de log para outras mensagens associadas a operações no mesmo blob, você pode verificar se o cliente excluídos a entidade. 

## <a name="analyze-other-types-of-storage-errors"></a>Analisar outros tipos de erros de armazenamento

Agora que você está familiarizado com o uso de analisador de mensagem para analisar seus dados de log, você pode analisar outros tipos de erros usando o modo de exibição layouts, regras de cor e pesquisa/filtragem. As tabelas a seguir lista alguns problemas que você pode encontrar e os critérios de filtro que você pode usar para localizá-las. Para obter mais informações sobre como construir filtros e o analisador de mensagem filtragem idioma, consulte [Filtragem de dados de mensagens](http://technet.microsoft.com/library/jj819365.aspx).

|    Para investigar...                                                                                               |    Use a expressão de filtro...                                                                                                                                                                                                                                        |    Expressão se aplica ao Log (cliente, servidor, rede, tudo)    |
|------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
|    Atrasos inesperados na entrega de mensagens em uma fila                                                              |    AzureStorageClientDotNetV4.Description contém "Repetindo Falha na operação."                                                                                                                                                                                |    Cliente                                                        |
|    Aumento de HTTP no PercentThrottlingError                                                                       |    HTTP. Response.StatusCode = = 500, #124; & #124; HTTP. Response.StatusCode = = 503                                                                                                                                                                                          |    Rede                                                       |
|    Aumentar em PercentTimeoutError                                                                               |    HTTP. Response.StatusCode = = 500                                                                                                                                                                                                                             |    Rede                                                       |
|    Aumentar em PercentTimeoutError (todos)                                                                         |    * StatusCode = = 500                                                                                                                                                                                                                                          |    Todos os                                                           |
|    Aumentar em PercentNetworkError                                                                               |    AzureStorageClientDotNetV4.EventLogEntry.Level < 2                                                                                                                                                                                                          |    Cliente                                                        |
|    Mensagens HTTP 403 (proibido)                                                                                 |    HTTP. Response.StatusCode = = 403                                                                                                                                                                                                                             |    Rede                                                       |
|    HTTP 404 (não encontrado) mensagens                                                                                 |    HTTP. Response.StatusCode = = 404                                                                                                                                                                                                                             |    Rede                                                       |
|    404 (todos)                                                                                                     |    * StatusCode = = 404                                                                                                                                                                                                                                          |    Todos os                                                           |
|    Compartilhado problema de autorização de assinatura de acesso (SAS)                                                             |    AzureStorageLog.RequestStatus = = "SASAuthorizationError"                                                                                                                                                                                                     |    Rede                                                       |
|    HTTP 409 (conflito) mensagens                                                                                  |    HTTP. Response.StatusCode = = 409                                                                                                                                                                                                                             |    Rede                                                       |
|    409 (todos)                                                                                                     |    * StatusCode = = 409                                                                                                                                                                                                                                          |    Todos os                                                           |
|    Entradas de log de baixo PercentSuccess ou analytics tem operações com status de transação da ClientOtherErrors    |    AzureStorageLog.RequestStatus = = "ClientOtherError"                                                                                                                                                                                                         |    Servidor                                                        |
|    Aviso de Nagle                                                                                               |    ((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1,5)) e (AzureStorageLog.RequestPacketSize < 1460) e (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200)        |    Servidor                                                        |
|    Intervalo de tempo em logs de servidor e de rede                                                                    |    #Carimbo de hora > = 2014-10-20T16:36:38 e #Timestamp < = 2014-10-20T16:36:39                                                                                                                                                                                     |    Servidor, rede                                               |
|    Intervalo de tempo em logs do servidor                                                                                |    AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 e AzureStorageLog.Timestamp < = 2014-10-20T16:36:39                                                                                                                                                     |    Servidor                                                        |


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre solução de problemas cenários de ponta a ponta no armazenamento do Azure, consulte estes recursos:

- [Monitorar, diagnosticar e solucionar problemas de armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)
- [Análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343270.aspx)
- [Monitorar uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md)
- [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md)
- [Guia de operando de analisador de mensagens da Microsoft](http://technet.microsoft.com/library/jj649776.aspx)
 
 
