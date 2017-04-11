<properties
   pageTitle="Migrando do Orchestrator à automação Azure | Microsoft Azure"
   description="Descreve como migrar pacotes runbooks e a integração do System Center Orchestrator para automação do Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />


# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrando do Orchestrator à automação Azure (Beta)

Runbooks no [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) são baseados em atividades de pacotes de integração que estão escritos especificamente para Orchestrator enquanto runbooks na automação do Azure são baseados no Windows PowerShell.  [Runbooks gráficas](automation-runbook-types.md#graphical-runbooks) no Azure automação ter uma aparência semelhante à Orchestrator runbooks com suas atividades representando cmdlets do PowerShell, runbooks filho e ativos.

O [Kit de ferramentas de migração do System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) inclui ferramentas para ajudá-lo a converter runbooks de Orchestrator para automação do Azure.  Além de converter as runbooks próprios, você deve converter os pacotes de integração com as atividades que usam os runbooks para módulos de integração com os cmdlets do Windows PowerShell.  

A seguir está o processo básico para a conversão Orchestrator runbooks para automação do Azure.  Cada uma dessas etapas é descrita detalhadamente nas seções a seguir.

1.  Baixe o [Kit de ferramentas de migração do System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) que contém as ferramentas e módulos abordados neste artigo.
2.  Importe o [módulo de atividades padrão](#standard-activities-module) para automação Azure.  Isso inclui versões convertidas de atividades Orchestrator padrão que podem ser usadas por runbooks convertido.
3.  Importe [Módulos de integração do System Center Orchestrator](#system-center-orchestrator-integration-modules) para automação do Azure para os pacotes de integração usados por seus runbooks que acessar System Center.
4.  Converter pacotes de integração de terceiros e personalizados usando o [Conversor de pacote de integração](#integration-pack-converter) e importar para automação do Azure.
5.  Converter runbooks Orchestrator usando o [Conversor de Runbook](#runbook-converter) e instale no Azure automação.
6.  Crie manualmente ativos de Orchestrator necessários na automação do Azure desde o conversor de Runbook não converter esses recursos.
7.  Configure um [Operador de Runbook híbrido](#hybrid-runbook-worker) no seu centro de dados locais para executar convertido runbooks que vai acessar recursos locais.

## <a name="service-management-automation"></a>Automação do gerenciamento de serviço

[Automação do gerenciamento de serviço](http://technet.microsoft.com/library/dn469260.aspx) (SMA) armazena e executa runbooks no seu centro de dados local como o Orchestrator e ele usa os mesmos módulos de integração como automação do Azure. O [Conversor de Runbook](#runbook-converter) converte Orchestrator runbooks em runbooks gráficas Apesar que não são suportados no SMA.  Você ainda pode instalar o [Módulo de atividades padrão](#standard-activities-module) e [Módulos de integração do System Center Orchestrator](#system-center-orchestrator-integration-modules) em SMA, mas você deve manualmente [regravar seus runbooks](http://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Operador de Runbook híbrido

Runbooks no Orchestrator são armazenados em um servidor de banco de dados e executados em servidores de runbook, tanto no seu centro de dados local.  Runbooks na automação do Azure são armazenados na nuvem Azure e podem ser executados no seu centro de dados locais usando um [Operador de Runbook híbrido](automation-hybrid-runbook-worker.md).  Isso é como você geralmente executará runbooks convertido de Orchestrator desde que eles foram projetados para ser executados em servidores locais.

## <a name="integration-pack-converter"></a>Conversor de pacote de integração

O conversor de pacote de integração converte pacotes de integração que foram criados usando o [Kit de ferramentas de integração de Orchestrator (OIT)](http://technet.microsoft.com/library/hh855853.aspx) para módulos de integração com base no Windows PowerShell, que podem ser importados em automação do Azure ou automação do gerenciamento de serviço.  

Quando você executa o conversor de pacote de integração, é apresentadas a você um assistente que permitirá que você selecione um arquivo de pacote (.oip) de integração.  O assistente então lista as atividades incluídas no que esse pacote de integração e permite que você selecione que serão migradas.  Quando você concluir o assistente, ele cria um módulo de integração que inclui um cmdlet correspondente para cada uma das atividades no pacote de integração original.


### <a name="parameters"></a>Parâmetros

As propriedades de uma atividade no pacote de integração são convertidos em parâmetros do cmdlet correspondente no módulo de integração.  Cmdlets do Windows PowerShell têm um conjunto de [parâmetros comuns](http://technet.microsoft.com/library/hh847884.aspx) que podem ser usadas com todos os cmdlets.  Por exemplo, o extenso parâmetro - faz com que um cmdlet obter informações detalhadas sobre sua operação de saída.  Nenhum cmdlet pode ter um parâmetro com o mesmo nome de um parâmetro comuns.  Se uma atividade tiver uma propriedade com o mesmo nome de um parâmetro comum, o assistente solicitará que você forneça outro nome para o parâmetro.

### <a name="monitor-activities"></a>Monitor de atividades

Monitor runbooks no Orchestrator iniciar com uma [monitorar a atividade](http://technet.microsoft.com/library/hh403827.aspx) e executar continuamente aguardando a ser chamado por um determinado evento.  Automação Azure não suporta runbooks monitor, para que todas as atividades de monitor no pacote de integração não serão convertidas.  Em vez disso, um cmdlet de espaço reservado é criado no módulo integração para monitorar a atividade.  Esse cmdlet não tem nenhuma funcionalidade, mas ele permite qualquer runbook convertido que usa para ser instalado.  Este runbook não será capaz de executar em automação do Azure, mas ele pode ser instalado para que você pode modificá-lo.

### <a name="integration-packs-that-cannot-be-converted"></a>Pacotes de integração que não podem ser convertidos

Pacotes de integração que não foram criados com OIT não podem ser convertidos com o conversor de pacote de integração. Também existem alguns pacotes de integração fornecidas pela Microsoft que atualmente não pode ser convertido com esta ferramenta.  Convertido versões desses pacotes de integração foram [fornecidas para download](#system-center-orchestrator-integration-modules) para que eles podem ser instalados no Azure automação ou automação do gerenciamento de serviço.


## <a name="standard-activities-module"></a>Módulo de atividades padrão

Orchestrator inclui um conjunto de [atividades padrão](http://technet.microsoft.com/library/hh403832.aspx) que não são incluídas em um pacote de integração, mas são usados por muitos runbooks.  O módulo de atividades padrão é um módulo de integração que inclui um cmdlet equivalente para cada uma dessas atividades.  Você deve instalar o módulo de integração no Azure automação antes de importar qualquer runbooks convertido que use uma atividade padrão.

Além de oferecer suporte runbooks convertido, os cmdlets no módulo atividades padrão pode ser usados por alguém que esteja familiarizado com Orchestrator criem novos runbooks na automação do Azure.  Enquanto a funcionalidade de todas as atividades padrão pode ser executada com cmdlets, eles podem operar diferente.  Os cmdlets no módulo convertido atividades padrão será funciona da mesma forma suas atividades correspondentes e usam os mesmos parâmetros.  Isso pode ajudar o autor de runbook Orchestrator existente na transição para runbooks de automação do Azure.

## <a name="system-center-orchestrator-integration-modules"></a>Módulos de integração do System Center Orchestrator

A Microsoft fornece [pacotes de integração](http://technet.microsoft.com/library/hh295851.aspx) para a criação de runbooks para automatizar componentes do System Center e outros produtos.  Alguns desses pacotes de integração atualmente são baseados em OIT mas atualmente não podem ser convertidos em módulos de integração devido a problemas conhecidos.  [Módulos de integração do System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) inclui versões convertidas desses pacotes de integração que podem ser importados em automação do Azure e automação do gerenciamento de serviço.  

Pela versão RTM desta ferramenta, versões atualizadas dos pacotes de integração com base em OIT que pode ser convertido com o conversor de pacote de integração serão publicadas.  Orientações também serão fornecidas para ajudar a converter runbooks usando atividades de pacotes de integração não baseados em OIT.

## <a name="runbook-converter"></a>Conversor de runbook

O conversor de Runbook converte Orchestrator runbooks em [runbooks gráficos](automation-runbook-types.md#graph-runbooks) que podem ser importados em automação do Azure.  

Conversor de runbook é implementado como um módulo do PowerShell com um cmdlet chamado **ConvertFrom SCORunbook** que executa a conversão.  Quando você instala a ferramenta, ele criará um atalho para uma sessão do PowerShell que carrega o cmdlet.   

A seguir está o processo básico para converter um runbook Orchestrator e importe-os para a automação do Azure.  As seções a seguir fornecem mais detalhes sobre usando a ferramenta e trabalhar com runbooks convertido.

1. Exporte um ou mais runbooks do Orchestrator.
2. Obter módulos de integração para todas as atividades no runbook.
3. Converta o runbooks Orchestrator no arquivo exportado.
4. Examine as informações em logs para validar a conversão e para determinar todas as tarefas manuais necessárias.
4. Importe runbooks convertido em automação do Azure.
5. Crie qualquer ativos necessários na automação do Azure.
6. Edite runbook na automação do Azure para modificar qualquer atividades necessárias.

### <a name="using-runbook-converter"></a>Usando o conversor de Runbook

A sintaxe para **ConvertFrom-SCORunbook** é da seguinte maneira:

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string> 

- RunbookPath - caminho para o arquivo de exportação contendo o runbooks converter.
- Módulo - lista delimitada por vírgulas módulos de integração contendo atividades no runbooks.
- OutputFolder - caminho da pasta para criar convertido runbooks gráficas. 


O comando de exemplo a seguir converte a runbooks em um arquivo de exportação chamado **MyRunbooks.ois_export**.  Esses runbooks usar os pacotes de integração com o Active Directory e Gerenciador de proteção de dados.

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks" 


### <a name="log-files"></a>Arquivos de log

O conversor de Runbook criará os seguintes arquivos de log no mesmo local como runbook convertido.  Se os arquivos já existirem, em seguida, elas serão substituídas com as informações da última conversão.

| Arquivo | Conteúdo |
|:---|:---|
| Conversor de runbook - Progress | Etapas detalhadas da conversão incluindo informações para cada atividade converteu e aviso para cada atividade não convertido. |
| Conversor de runbook - Summary. log  | Resumo da última conversão incluindo quaisquer avisos e acompanhar as tarefas que você precisará executar como criar uma variável necessária para runbook convertido.  |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportando runbooks do Orchestrator

O conversor de Runbook funciona com um arquivo de exportação do Orchestrator que contém uma ou mais runbooks.  Ele criará um runbook de automação do Azure correspondente para cada runbook Orchestrator no arquivo de exportação.  

Para exportar um runbook do Orchestrator, clique com botão direito no nome do runbook no Designer de Runbook e selecione **Exportar**.  Para exportar todos os runbooks em uma pasta, clique com botão direito no nome da pasta e selecione **Exportar**.


### <a name="runbook-activities"></a>Atividades de runbook

O conversor de Runbook converte cada atividade no runbook Orchestrator em uma atividade correspondente na automação do Azure.  As atividades que não podem ser convertidas, uma atividade de espaço reservado é criada no runbook com texto de aviso.  Depois de importar runbook convertido para automação do Azure, você deve substituir qualquer uma dessas atividades com atividades válidas que executam a funcionalidade necessária.

Quaisquer atividades Orchestrator no [Módulo de atividades padrão](#standard-activities-module) serão convertidas.  Há algumas atividades Orchestrator padrão que não estão neste módulo apesar e não são convertidas.  Por exemplo, **Enviar evento de plataforma** não tem nenhuma automação Azure equivalente desde que o evento é específico para Orchestrator.

[Atividades de monitor](https://technet.microsoft.com/library/hh403827.aspx) não são convertidos como não há nenhum equivalente a eles na automação do Azure.  A exceção são atividades do monitor [convertido pacotes de integração](#integration-pack-converter) que serão convertidos para a atividade de espaço reservado.

Qualquer atividade um [convertido pacote de integração](#integration-pack-converter) será convertida se você fornecer o caminho para o módulo de integração com o parâmetro de **módulos** .  Para pacotes de integração do System Center, você pode usar os [Módulos de integração do System Center Orchestrator](#system-center-orchestrator-integration-modules).


### <a name="orchestrator-resources"></a>Recursos de Orchestrator

O conversor de Runbook converte somente runbooks, não outros recursos Orchestrator como contadores, variáveis ou conexões.  Contadores não são compatíveis com a automação do Azure.  Há suporte para variáveis e conexões, mas você deve criá-los manualmente.  Os arquivos de log irá informá-lo a se runbook exigir esses recursos e especifique recursos correspondentes que você precisa criar no Azure automação para runbook convertido funcione corretamente.

Por exemplo, um runbook pode usar uma variável para preencher um determinado valor em uma atividade.  Convertido runbook converterá a atividade e especificar um variável ativo no Azure automação com o mesmo nome da variável Orchestrator.  Isso você observará no arquivo **Runbook conversor - Summary. log** criado após a conversão.  Você precisará criar manualmente essa variável ativo no Azure automação antes de usar runbook. 

 
### <a name="input-parameters"></a>Parâmetros de entrada

Runbooks no Orchestrator aceitar parâmetros de entrada com a atividade de **Inicializar dados** .  Se runbook sendo convertido inclui esta atividade, um [parâmetro de entrada](automation-graphical-authoring-intro.md#runbook-input-and-output) em runbook Azure automação é criado para cada parâmetro na atividade.  Uma atividade de [controle de Script de fluxo de trabalho](automation-graphical-authoring-intro.md#activities) é criada no runbook convertido que recupera e retorna cada parâmetro.  As atividades do runbook que usa um parâmetro de entrada referem-se à saída desta atividade.

O motivo que essa estratégia é usada é melhor espelhar a funcionalidade do runbook Orchestrator.  Atividades do novo runbooks gráficas devem consultar diretamente para usar uma fonte de dados de entrada do Runbook de parâmetros de entrada.


### <a name="invoke-runbook-activity"></a>Invocar Runbook atividade

Runbooks no Orchestrator iniciar outros runbooks com a atividade de **Runbook invocar** . Se runbook sendo convertido inclui esta atividade e a opção de **esperar para a conclusão** estiver definida, uma atividade de runbook é criada para ele no runbook convertido.  Se a opção **Aguardar conclusão** não estiver definida, uma atividade de Script de fluxo de trabalho é criada que usa **AzureAutomationRunbook iniciar** para iniciar o runbook.  Depois de importar runbook convertido para automação do Azure, você deve modificar esta atividade com as informações especificadas na atividade.




## <a name="related-articles"></a>Artigos relacionados

- [System Center 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [Automação do gerenciamento de serviço](https://technet.microsoft.com/library/dn469260.aspx)
- [Operador de Runbook híbrido](automation-hybrid-runbook-worker.md)
- [Atividades padrão do Orchestrator](http://technet.microsoft.com/library/hh403832.aspx)
- [Kit de ferramentas de migração do download System Center Orchestrator](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
 
