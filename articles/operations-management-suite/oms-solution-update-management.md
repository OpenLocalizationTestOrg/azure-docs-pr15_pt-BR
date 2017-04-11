<properties
    pageTitle="Atualizar a solução de gerenciamento do OMS | Microsoft Azure"
    description="Este artigo destina-se para ajudá-lo a compreender como usar esta solução para gerenciar as atualizações para os computadores Windows e Linux."
    services="operations-management-suite"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
    />
<tags
    ms.service="operations-management-suite"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="magoedte"/>

# <a name="update-management-solution-in-omsmediaoms-solution-update-managementupdate-management-solution-iconpng-update-management-solution-in-oms"></a>![Solução de gerenciamento de atualização em OMS](./media/oms-solution-update-management/update-management-solution-icon.png) Solução de gerenciamento de atualização em OMS

A solução de gerenciamento de atualização em OMS permite que você gerencie atualizações para os computadores Windows e Linux.  Você pode avaliar o status das atualizações disponíveis em todos os computadores de agente e iniciar o processo de instalar as atualizações necessárias para os servidores rapidamente. 

## <a name="prerequisites"></a>Pré-requisitos

-   Agentes Windows devem ser configurados para se comunicar com um servidor Windows Server Update Services (WSUS) ou ter acesso ao Microsoft Update.  

    >[AZURE.NOTE] Agente do Windows não pode ser gerenciado simultaneamente pelo Gerenciador de configuração do System Center.  
  
-   Agentes Linux devem ter acesso a um repositório de atualização.  O agente de OMS para Linux pode ser baixado do [GitHub](https://github.com/microsoft/oms-agent-for-linux). 

## <a name="configuration"></a>Configuração

Execute as seguintes etapas para adicionar a solução de gerenciamento de atualização ao seu espaço de trabalho do OMS e adicionar agentes Linux.  Agentes Windows são adicionados automaticamente com nenhuma configuração adicional.

1.  Adicione a solução de gerenciamento de atualização ao seu espaço de trabalho OMS usando o processo descrito em [Adicionar OMS soluções](../log-analytics/log-analytics-add-solutions.md) da Galeria de soluções.  
2.  No portal do OMS, selecione **configurações** e **Fontes conectadas**.  Observe a **identificação de espaço de trabalho** e a **chave primária** ou **chave secundária**.
3.  Execute as seguintes etapas para cada computador Linux.

    a.  Instale a versão mais recente do agente OMS para Linux executando os seguintes comandos.  Substituir <Workspace ID> com a ID de espaço de trabalho e <Key> com a chave primária ou secundária.

        cd ~
        wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.2.0-75/omsagent-1.2.0-75.universal.x64.sh
        sudo bash omsagent-1.2.0-75.universal.x64.sh --upgrade -w <Workspace ID> -s <Key>

     b. Para remover o agente, execute o seguinte comando.

        sudo bash omsagent-1.2.0-75.universal.x64.sh --purge

## <a name="management-packs"></a>Pacotes de gerenciamento

Se o seu grupo de gerenciamento do System Center Operations Manager está conectado ao seu espaço de trabalho do OMS, em seguida, os seguintes pacotes de gerenciamento serão instalados no Operations Manager quando você adiciona esta solução. Não há nenhuma configuração ou a manutenção desses pacotes de gerenciamento necessários. 

-   Pacote de inteligência de avaliação para atualização de Supervisor Microsoft System Center (Microsoft.IntelligencePacks.UpdateAssessment)
-   Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
-   Atualizar implantação MP

Para obter mais informações sobre como os pacotes de gerenciamento de solução são atualizados, consulte [Conectar o Operations Manager para análise de Log](../log-analytics/log-analytics-om-agents.md).

## <a name="data-collection"></a>Coleta de dados

### <a name="supported-agents"></a>Agentes com suporte

A tabela a seguir descreve as fontes conectadas que são suportadas por esta solução.

Fonte conectada | Com suporte | Descrição|
----------|----------|----------|
Agentes Windows | Sim | A solução coleta informações sobre as atualizações de sistema de agentes Windows e inicia a instalação de atualizações necessárias.|
Agentes Linux | Sim | A solução coleta informações sobre as atualizações de sistema do agentes Linux.|
Grupo de gerenciamento do Operations Manager | Sim | A solução coleta informações sobre as atualizações do sistema de agentes em um grupo de gerenciamento conectados.<br>Uma conexão direta do agente do Operations Manager para análise de Log não é necessária. Dados são encaminhados do grupo de gerenciamento do repositório de OMS.|
Conta de armazenamento do Azure | Não | Armazenamento do Azure não inclui informações sobre as atualizações de sistema.|  

### <a name="collection-frequency"></a>Frequência de conjunto

Para cada computador Windows gerenciado, é executada uma varredura duas vezes por dia.  Quando uma atualização é instalada, suas informações são atualizadas em 15 minutos.  

Para cada computador Linux gerenciado, uma verificação é executada a cada 3 horas.  

## <a name="using-the-solution"></a>Usando a solução

Quando você adiciona a solução de gerenciamento de atualização para o seu espaço de trabalho do OMS, o bloco de **Gerenciamento de atualização** será adicionado ao seu painel OMS. Esse bloco exibe a contagem e representação gráfica do número de computadores em seu ambiente atualmente exigindo atualizações do sistema.<br><br>
![Bloco de resumo de gerenciamento de atualização](media/oms-solution-update-management/update-management-summary-tile.png)  

## <a name="viewing-update-assessments"></a>Avaliações de atualização de visualização

Clique no bloco **Gerenciamento de atualização** para abrir o painel de **Gerenciamento de atualização** . O painel inclui as colunas da tabela a seguir. Cada coluna lista até dez itens que correspondam aos critérios da coluna para o intervalo de tempo e o escopo especificado. Você pode executar uma pesquisa de log que retorna todos os registros clicando em **ver todos** na parte inferior da coluna ou clicando no cabeçalho da coluna.

Coluna | Descrição|
----------|----------|
**Computadores atualizações ausentes** ||
Críticas ou atualizações de segurança | Na parte superior dez computadores que estão faltando atualiza classificada pelo número de atualizações de listas estão ausentes. Clique em um nome de computador para executar uma pesquisa de log retornando que todos os registros para o computador de atualização.|
Crítica ou atualizações de segurança mais de 30 dias| Identifica o número de computadores que estão faltando críticas ou atualizações de segurança agrupadas por período de tempo desde a atualização foi publicada. Clique em uma das entradas para executar uma pesquisa de log retornando todas as atualizações críticas e ausentes.|
**Atualizações ausentes necessárias**||
Críticas ou atualizações de segurança | Lista classificações de atualizações que computadores estão faltando classificados pelo número de computadores atualizações na categoria ausentes. Clique em uma classificação para executar uma pesquisa de log retornando que todos os registros para essa classificação de atualização.|
**Implantações de atualização**||
Implantações de atualização | Número de implantações de atualização agendada no momento e a duração até a próxima execução agendada.  Clique no bloco para exibir agendas, em execução e atualizações concluídas ou para agendar uma nova implantação.|  
<br>  
![Painel de resumo de gerenciamento de atualização](./media/oms-solution-update-management/update-management-deployment-dashboard.png)<br>  
<br>
![Atualizar a exibição de computador do painel de gerenciamento](./media/oms-solution-update-management/update-management-assessment-computer-view.png)<br>  
<br>
![Atualizar a exibição de pacote do painel de gerenciamento](./media/oms-solution-update-management/update-management-assessment-package-view.png)<br>  

## <a name="installing-updates"></a>Instalar as atualizações

Depois de tem sido avaliadas atualizações para todos os computadores Windows em seu ambiente, você pode ter atualizações necessárias instaladas pela criação de uma *Implantação de atualização*.  Implantar uma atualização é uma instalação agendada de atualizações necessárias para um ou mais computadores com Windows.  Você especificar a data e hora para a implantação além de um computador ou grupo de computadores que devem ser incluídos.  

As atualizações são instaladas pelo runbooks na automação do Azure.  Você não consegue visualizar esses runbooks e eles não exigem qualquer configuração.  Quando uma implantação atualizar é criada, ele cria um cronograma no que começa um runbook de atualização de mestres no horário especificado para os computadores incluídos.  Este runbook mestre inicia uma runbook filho em cada agente do Windows que realiza a instalação de atualizações necessárias.  

### <a name="viewing-update-deployments"></a>Implantações de atualização de visualização

Clique no bloco de **Implantação de atualização** para exibir a lista de implantações de atualização existentes.  Eles são agrupados por status – **agendada**, **execução**e **concluídas**.<br><br> ![Página de cronograma de implantações de atualização](./media/oms-solution-update-management/update-updatedeployment-schedule-page.png)<br>  

As propriedades exibidas para cada implantação de atualização são descritas na tabela a seguir.

Propriedade | Descrição|
----------|----------|
Nome | Nome da implantação da atualização.|
Cronograma | Tipo de cronograma.  *OneTime* atualmente é o valor só é possível.|
Hora de início|Data e hora a implantação de atualização agendada para começar.|
Duração | Número de minutos que a implantação de atualização tem permissão para executar.  Se todas as atualizações não são instaladas dentro desta duração, e em seguida, as atualizações restantes devem esperar até a próxima implantação de atualização.|
Servidores | Número de computadores afetados pela implantação da atualização.|
Status | Status atual da implantação da atualização.<br><br>Valores possíveis são:<br>-Não iniciado<br>-Em execução<br>-Concluído|  

Clique em uma implantação de atualização para exibir sua tela de detalhes que inclui as colunas da tabela a seguir.  Essas colunas não serão preenchidas se a implantação de atualização ainda não foi iniciada.<br>

Coluna | Descrição|
----------|----------|
**Resultados de computador**||
Foi concluída com êxito | Lista o número de computadores na implantação da atualização por status.  Clique em status para executar uma pesquisa de log retornando que todos atualizar registros com esse status para a implantação de atualização.|
Status de instalação do computador| Lista os computadores envolvidos na implantação da atualização e a porcentagem de atualizações que instalada com êxito. Clique em uma das entradas para executar uma pesquisa de log retornando todas as atualizações críticas e ausentes.|
**Resultados da instância de atualização**||
Status de instalação da instância | Lista classificações de atualizações que computadores estão faltando classificados pelo número de computadores atualizações na categoria ausentes. Clique em um computador para executar uma pesquisa de log retornando que todos os registros para o computador de atualização.|  
<br><br> ![Visão geral dos resultados de implantação de atualização](./media/oms-solution-update-management/update-la-updaterunresults-page.png)

### <a name="creating-an-update-deployment"></a>Criando uma implantação de atualização

Crie uma nova implantação de atualização clicando no botão **Adicionar** na parte superior da tela para abrir a página de **Implantação de atualização de novo** .  Você deve fornecer valores para as propriedades da tabela a seguir.

Propriedade | Descrição|
----------|----------|
Nome | Nome exclusivo para identificar a implantação de atualização.|
Fuso horário | Fuso horário para usar para a hora de início.|
Hora de início | Data e hora para iniciar a implantação da atualização.|
Duração | Número de minutos que a implantação de atualização tem permissão para executar.  Se todas as atualizações não são instaladas dentro desta duração, e em seguida, as atualizações restantes devem esperar até a próxima implantação de atualização.|
Computadores | Nomes de computadores ou grupos para incluir na implantação da atualização.  Selecione uma ou mais entradas na lista suspensa.|
<br><br> ![Nova página de implantação de atualização](./media/oms-solution-update-management/update-newupdaterun-page.png)

### <a name="time-range"></a>Intervalo de tempo

Por padrão, o escopo dos dados analisados na solução de gerenciamento de atualização é de todos os grupos de gerenciamento conectados gerados dentro do último dia 1. 

Para alterar o intervalo de tempo dos dados, selecione **dados com base** na parte superior do painel de controle. Você pode selecionar registros criada ou atualizada os últimos 7 dias, 1 dia ou 6 horas. Ou você pode selecionar **personalizado** e especificar um intervalo de datas personalizado.<br><br> ![Opção de intervalo de tempo personalizado](./media/oms-solution-update-management/update-la-time-range-scope-databasedon.png)  

## <a name="log-analytics-records"></a>Registros de log de análise

A solução de gerenciamento de atualização cria dois tipos de registros no repositório OMS.

### <a name="update-records"></a>Atualizar registros

Um registro com um tipo de **atualização** é criado para cada atualização que está instalada ou é necessário em cada computador. Atualizar registros têm as propriedades da tabela a seguir.

Propriedade | Descrição|
----------|----------|
Tipo | *Atualização*|
SourceSystem | A fonte que aprovadas instalação da atualização.<br>Valores possíveis são:<br>-Microsoft Update<br>-Windows Update<br>-SCCM<br>-Servidores de Linux (buscada no pacote gerentes)|
Aprovado | Especifica se a atualização foi aprovada para instalação.<br> Para servidores Linux isso é opcional atualmente como patches não é gerenciado pelo OMS.|
Classificação para Windows | Classificação da atualização.<br>Valores possíveis são:<br>-Aplicativos<br>-Atualizações críticas<br>-Atualizações de definição<br>-Pacotes de recurso<br>-Atualizações de segurança<br>-Os service Packs<br>-Cumulativo<br>-Atualizações|
Classificação para Linux | Cassification da atualização.<br>Valores possíveis são:<br>-Atualizações críticas<br>-Atualizações de segurança<br>-Outras atualizações|
Computador | Nome do computador.|
InstallTimeAvailable | Especifica se o tempo de instalação está disponível em outros agentes que instalou a mesma atualização.|
InstallTimePredictionSeconds | Tempo de instalação estimado em segundos com base em outros agentes que instalou a mesma atualização.|
KBID | ID do artigo KB que descreve a atualização.|
ManagementGroupName | Nome do grupo de gerenciamento de agentes SCOM.  Para outros agentes, isso é AOI -<workspace ID>.|
MSRCBulletinID | ID do boletim de segurança da Microsoft que descrevem a atualização.|
MSRCSeverity | Gravidade do boletim de segurança da Microsoft.<br>Valores possíveis são:<br>-Crítica<br>-Importante<br>-Moderada|
Opcional | Especifica se a atualização é opcional.|
Produto | Nome do produto para que a atualização é.  Clique em **modo de exibição** para abrir o artigo em um navegador.|
PackageSeverity | A gravidade da vulnerabilidade fixa nesta atualização, conforme relatado por fornecedores de distribuição do Linux. | 
PublishDate | Data e hora em que a atualização foi instalada.|
RebootBehavior | Especifica se a atualização força uma reinicialização.<br>Valores possíveis são:<br>-canrequestreboot<br>-neverreboots|
RevisionNumber | Número de revisão da atualização.|
SourceComputerId | GUID para identificar exclusivamente o computador.|
TimeGenerated | Data e hora em que o registro foi atualizado pela última vez.|
Título | Título da atualização.|
UpdateID | GUID para identificar exclusivamente a atualização.|
UpdateState | Especifica se a atualização está instalada neste computador.<br>Valores possíveis são:<br>-Instalado - a atualização está instalada neste computador.<br>-Necessária - a atualização não está instalada e é necessária neste computador.|  

<br>
Quando você executa qualquer pesquisa de log que retorna registros com um tipo de **atualização** , você pode selecionar o modo de exibição de **atualizações** que exibe um conjunto de blocos resumindo as atualizações retornadas pela pesquisa. Você pode clicar nas entradas no **ausente e atualizações aplicadas** e **atualizações obrigatórias e opcionais** blocos para definir o escopo de modo de exibição para esse conjunto de atualizações. Selecione o modo de exibição de **lista** ou **tabela** para retornar os registros individuais.<br> 

![Exibição de atualização de pesquisa de log com a atualização de tipo de registro](./media/oms-solution-update-management/update-la-view-updates.png)  

No modo de exibição de **tabela** , você pode clicar na **KBID** para qualquer registro para abrir um navegador com o artigo KB. Isso permite que você rapidamente leia sobre os detalhes da atualização de determinado.<br> 

![Exibição de tabela de pesquisa de log com atualizações de tipo de registro de blocos](./media/oms-solution-update-management/update-la-view-table.png)

No modo de exibição de **lista** , clicar no link de **exibição** ao lado do KBID para abrir o artigo KB.<br>

![Exibição de lista de pesquisa de log com atualizações de tipo de registro de blocos](./media/oms-solution-update-management/update-la-view-list.png)


###<a name="updatesummary-records"></a>Registros de UpdateSummary

Um registro com um tipo de **UpdateSummary** é criado para cada computador de agente do Windows. Este registro é atualizado toda vez que o computador é verificado para atualizações. **UpdateSummary** registros têm as propriedades da tabela a seguir.

Propriedade | Descrição|
----------|----------|
Tipo | UpdateSummary|
SourceSystem | OpsManager |
Computador | Nome do computador.|
CriticalUpdatesMissing | Número de atualizações críticas ausentes no computador.|
ManagementGroupName | Nome do grupo de gerenciamento de agentes SCOM. Para outros agentes, isso é AOI -<workspace ID>.|
NETRuntimeVersion | Versão do tempo de execução .NET instalada no computador.|
OldestMissingSecurityUpdateBucket | Balde para categorizar o tempo desde que a mais antiga ausente atualização de segurança neste computador foi publicado.<br>Valores possíveis são:<br>-Mais antigos<br>-180 dias atrás<br>-150 dias atrás<br>-120 dias atrás<br>-90 dias atrás<br>-60 dias atrás<br>-Vá 30 dias<br>-Recentes|
OldestMissingSecurityUpdateInDays | Número de dias desde a mais antiga ausente atualização de segurança neste computador foi publicado.|
OsVersion | Versão do sistema operacional instalado no computador.|
OtherUpdatesMissing | Número de outras atualizações ausentes no computador.|
SecurityUpdatesMissing | Número de atualizações de segurança ausentes no computador.|
SourceComputerId | GUID para identificar exclusivamente o computador.|
TimeGenerated | Data e hora em que o registro foi atualizado pela última vez.|
TotalUpdatesMissing |Número total de atualizações ausentes no computador.|
WindowsUpdateAgentVersion | Número de versão do Windows Update agent no computador.|
WindowsUpdateSetting | Configuração de como o computador irá instalar atualizações importantes.<br>Valores possíveis são:<br>-Desativado<br>-Avisar antes da instalação<br>-Instalação agendada|
WSUSServer | URL da independente se o computador está configurado para usar um.|  

## <a name="sample-log-searches"></a>Pesquisas de log de amostra

A tabela a seguir fornece pesquisas de log de amostra para atualizar registros coletadas por esta solução. 

Consulta | Descrição|
----------|----------|
Todos os computadores com atualizações ausentes | Tipo = UpdateState de atualização = necessário opcional = false & #124; Selecione o computador, título, KBID, classificação, UpdateSeverity, PublishedDate|
Atualizações ausentes do computador "COMPUTER01.contoso.com" (substituir com seu próprio nome de computador) | Tipo = UpdateState de atualização = necessário opcional = false computador = "COMPUTER01.contoso.com" & #124; Selecione o computador, título, KBID, produto, UpdateSeverity, PublishedDate|
Todos os computadores com ausentes críticos ou atualizações de segurança | Tipo = UpdateState de atualização = necessário opcional = false (classificação = classificação de ou "Atualizações de segurança" = "Atualizações críticas")|
Atualizações de segurança ou crítica necessárias por máquinas onde as atualizações são aplicadas manualmente | Tipo = UpdateState de atualização = necessário opcional = false (classificação = classificação de ou "Atualizações de segurança" = "Atualizações críticas") no computador {tipo = UpdateSummary WindowsUpdateSetting = #124; & Manual Computador distinta} & #124; KBID distinta|
Eventos de erro de máquinas que faltam crítico ou segurança atualizações necessárias | Tipo = EventLevelName de evento = erro computador em {tipo = atualização (classificação = classificação de ou "Atualizações de segurança" = "Atualizações críticas") UpdateState = necessário opcional = false & #124; Computador distinta}|
Todos os computadores com ausentes cumulativo | Tipo = atualização opcional = false classificação = "Cumulativo" UpdateState = necessário & #124; Selecione o computador, título, KBID, classificação, UpdateSeverity, PublishedDate|
Atualizações ausentes distintas em todos os computadores | Tipo = UpdateState de atualização = necessário opcional = false & #124; Título distinto|
Associação de computador WSUS | Tipo = UpdateSummary & #124; Count () medida por WSUSServer|
Configuração de atualização automática | Tipo = UpdateSummary & #124; Count () medida por WindowsUpdateSetting|
Computadores com atualização automática desativada | Tipo = UpdateSummary WindowsUpdateSetting = Manual|  
Lista de todas as máquinas Linux que tem uma atualização de pacote disponível | Tipo = atualização e OSType = Linux e UpdateState! = "Não necessárias" & #124; Count () medida por computador|
Lista de todas as máquinas Linux que têm uma atualização de pacote disponível que corrige vulnerabilidade crítica ou segurança | Tipo = atualização e OSType = Linux e UpdateState! = "Não necessárias" e (classificação = classificação de ou "Atualizações críticas" = "Atualizações de segurança") & #124; Count () medida por computador|
Lista de todos os pacotes que possuem uma atualização disponível | Tipo = atualização e OSType = Linux e UpdateState! = "Não necessárias"|
Lista de todos os pacotes que possuem uma atualização disponível que corrige vulnerabilidade crítica ou segurança | Tipo = atualização e OSType = Linux e UpdateState! = "Não necessárias" e (classificação = classificação de ou "Atualizações críticas" = "Atualizações de segurança")|
Lista de todas as máquinas "Ubuntu" com qualquer atualização disponível | Tipo = atualização e OSType = Linux e OSName = Ubuntu & #124; Count () medida por computador|

## <a name="next-steps"></a>Próximas etapas

- Use as pesquisas de Log da [Análise de Log](../log-analytics/log-analytics-log-searches.md) para exibir dados de atualização detalhadas.

- [Criar seus próprios painéis](../log-analytics/log-analytics-dashboards.md) conformidade de atualização mostrando para seus computadores gerenciados.

- [Criar alertas](../log-analytics/log-analytics-alerts.md) quando atualizações críticas são detectadas como falta de computadores ou um computador tem as atualizações automáticas desabilitadas.  




