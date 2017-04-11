<properties 
    pageTitle="Iniciar e parar máquinas virtuais com a automação do Azure - fluxo de trabalho do PowerShell | Microsoft Azure"
    description="Versão gráfica do cenário de automação do Azure incluindo runbooks para iniciar e parar clássicas máquinas virtuais."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="07/06/2016"
    ms.author="bwren" />

# <a name="azure-automation-scenario---starting-and-stopping-virtual-machines"></a>Cenário de automação Azure - iniciar e parar máquinas virtuais

Este cenário de automação do Azure inclui runbooks para iniciar e parar clássicas máquinas virtuais.  Você pode usar esse cenário para qualquer um destes procedimentos:  

- Use o runbooks sem modificação no seu ambiente. 
- Modificar o runbooks para executar a funcionalidade personalizada.  
- Ligar o runbooks de outro runbook como parte de uma solução geral. 
- Use o runbooks como tutoriais para aprender runbook conceitos de criação. 

> [AZURE.SELECTOR]
- [Gráfica](automation-solution-startstopvm-graphical.md)
- [Fluxo de trabalho do PowerShell](automation-solution-startstopvm-psworkflow.md)

Esta é a versão de runbook de fluxo de trabalho do PowerShell desse cenário. Ele também está disponível usando [runbooks gráficas](automation-solution-startstopvm-graphical.md).

## <a name="getting-the-scenario"></a>Obtendo o cenário

Este cenário consiste em duas runbooks de fluxo de trabalho do PowerShell que você pode baixar do links a seguir.  Ver a [versão gráfica](automation-solution-startstopvm-graphical.md) desse cenário para obter links para as gráficas runbooks.

| Runbook | Link | Tipo | Descrição |
|:---|:---|:---|:---|
| Início-AzureVMs | [Iniciar o Azure VMs clássicas](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | Fluxo de trabalho do PowerShell | Inicia todas as máquinas virtuais clássicas em um subscriptionor Azure todas as máquinas virtuais com um nome de serviço específico. |
| Parar AzureVMs | [Interromper o Azure VMs clássicas](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | Fluxo de trabalho do PowerShell | Interrompe todas as máquinas virtuais em uma conta de automação ou todas as máquinas virtuais com um nome de serviço específico.  |


## <a name="installing-and-configuring-the-scenario"></a>Instalando e configurando o cenário

### <a name="1-install-the-runbooks"></a>1. Instale o runbooks

Depois de baixar o runbooks, você poderá importá-los usando o procedimento na [importação de um Runbook](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook).

### <a name="2-review-the-description-and-requirements"></a>2. Analise a descrição e requisitos
Os runbooks incluir texto de comentário ajuda que inclua uma descrição e ativos necessários.  Você também pode obter as mesmas informações deste artigo. 

### <a name="3-configure-assets"></a>3. configurar ativos
Os runbooks exigem os seguintes ativos que você deve criar e preencher com valores apropriados.

| Tipo de ativo | Nome do ativo | Descrição |
|:---|:---|:---|:---|
| Credencial | AzureCredential | Contém as credenciais para uma conta que tenha autoridade para iniciar e parar máquinas virtuais na assinatura do Azure.  Como alternativa, você pode especificar outro ativo de credencial no parâmetro **credencial** da atividade **AzureAccount de adicionar** . |
| Variável | AzureSubscriptionId | Contém a ID da assinatura da sua assinatura do Azure. |

## <a name="using-the-scenario"></a>Usando o cenário

### <a name="parameters"></a>Parâmetros

Os runbooks tem os seguintes parâmetros.  Você deve fornecer valores para todos os parâmetros obrigatórios e, opcionalmente, pode fornecer valores para outros parâmetros, dependendo das suas necessidades.

| Parâmetro | Tipo | Obrigatório | Descrição |
|:---|:---|:---|:---|
| ServiceName | cadeia de caracteres | Não | Se um valor é fornecido, todas as máquinas virtuais com esse nome de serviço são iniciadas ou interrompidas.  Se nenhum valor for fornecido, todas as máquinas virtuais clássicas na assinatura do Azure são iniciadas ou interrompidas. |
| AzureSubscriptionIdAssetName | cadeia de caracteres | Não | Contém o nome da [variável ativos](#installing-and-configuring-the-scenario) que contém a ID da assinatura da sua assinatura do Azure.  Se você não especificar um valor, *AzureSubscriptionId* será usado.  |
| AzureCredentialAssetName | cadeia de caracteres | Não | Contém o nome do [ativo de credencial](#installing-and-configuring-the-scenario) que contém as credenciais para runbook usar.  Se você não especificar um valor, *AzureCredential* será usado.  |

### <a name="starting-the-runbooks"></a>Iniciando o runbooks

Você pode usar qualquer um dos métodos no [Iniciando um runbook na automação do Azure](automation-starting-a-runbook.md) para iniciar um do runbooks neste cenário.

Os comandos de exemplo a seguir usa o Windows PowerShell para executar **StartAzureVMs** para iniciar todas as máquinas virtuais com o nome do serviço *MyVMService*.

    $params = @{"ServiceName"="MyVMService"}
    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### <a name="output"></a>Saída

Os runbooks será [uma mensagem de saída](automation-runbook-output-and-messages.md) para cada máquina virtual indicando estando ou não a instrução iniciar ou interromper o foi enviada com êxito.  Você pode procurar por uma cadeia de caracteres específica na saída para determinar o resultado de cada runbook.  As cadeias de caracteres de saída possíveis estão listadas na tabela a seguir.

| Runbook | Condição | Mensagem |
|:---|:---|:---|
| Início-AzureVMs | Máquina virtual já está em execução  | MyVM já está em execução |
| Início-AzureVMs | Iniciar solicitação para máquina virtual enviada com êxito | MyVM foi iniciado |
| Início-AzureVMs | Falha na solicitação de início de máquina virtual  | MyVM Falha ao iniciar |
| Parar AzureVMs | Máquina virtual já for interrompida  | MyVM já for interrompido |
| Parar AzureVMs | Interromper a solicitação de máquina virtual enviada com êxito | MyVM foi interrompida |
| Parar AzureVMs | Falha na solicitação de parada de máquina virtual  | MyVM Falha ao parar |

Por exemplo, o seguinte trecho de código de um runbook tenta iniciar todas as máquinas virtuais com o nome do serviço *MyServiceName*.  Se qualquer uma da falhar de solicitações de início, ações de erro podem ser realizadas. 

    $results = Start-AzureVMs -ServiceName "MyServiceName"
    foreach ($result in $results) {
        if ($result -like "* has been started" ) {
            # Action to take in case of success.
        }
        else {
            # Action to take in case of error.
        }
    }


## <a name="detailed-breakdown"></a>Divisão detalhada

A seguir está uma divisão detalhada do runbooks neste cenário.  Você pode usar essas informações para personalizar o runbooks ou apenas para saber para a criação de seus próprios cenários de automação.

### <a name="parameters"></a>Parâmetros

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

O fluxo de trabalho é iniciado por obter os valores para os [parâmetros de entrada](#using-the-scenario).  Se os nomes de ativos não são fornecidos nomes padrão são usados.

### <a name="output"></a>Saída

    # Returns strings with status messages
    [OutputType([String])]

Esta linha declara que a saída do runbook será uma cadeia de caracteres.  Isso não é necessário, mas é uma prática recomendada para quando runbook é usado como um [filho runbook](automation-child-runbooks.md) para que um runbook pai saibam o tipo de saída esperar.

### <a name="authentication"></a>Autenticação

    # Connect to Azure and select the subscription to work against
    $Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
    $null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
    $SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

As próximas linhas definem as [credenciais](automation-configuring.md#configuring-authentication-to-azure-resources) e a assinatura do Azure que será usada para o restante do runbook.
Primeiro, usamos **Get-AutomationPSCredential** para obter o ativo que detém as credenciais com acesso ao iniciar e parar máquinas virtuais na assinatura do Azure. **Adicionar AzureAccount** usa esse ativo para definir as credenciais.  A saída é atribuída a uma variável fictícia para que ela não está incluída na saída runbook.  

O variável ativo com a ID da assinatura será recuperado com **Get-AutomationVariable** e a assinatura definida com **Selecione AzureSubscription**.

### <a name="get-vms"></a>Obter VMs

    # If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName) 
    { 
        $VMs = Get-AzureVM -ServiceName $ServiceName
    }
    else 
    { 
        $VMs = Get-AzureVM
    }

**Get-AzureVM** é usado para recuperar as máquinas virtuais com que runbook funcionarão.  Se um valor é fornecido na variável de entrada **ServiceName** , somente as máquinas virtuais com esse nome de serviço são recuperadas.  Se **ServiceName** estiver vazia, todas as máquinas virtuais são recuperadas.

### <a name="startstop-virtual-machines-and-send-output"></a>Iniciar/Parar máquinas virtuais e enviar saída

    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
        if ($VM.PowerState -eq "Started")
        {
            # The VM is already started, so send notice
            Write-Output ($VM.InstanceName + " is already running")
        }
        else
        {
            # The VM needs to be started
            $StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

            if ($StartRtn.OperationStatus -ne 'Succeeded')
            {
                # The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
            }
            else
            {
                # The VM started, so send notice
                Write-Output ($VM.InstanceName + " has been started")
            }
        }
    }

A próxima etapa de linhas por meio de cada máquina virtual.  Primeiro o **PowerState** da máquina virtual é verificado para ver se ele já estiver executando ou interrompido, dependendo do runbook.  Se ele já estiver no estado de destino, uma mensagem é enviada para saída e as extremidades de runbook.  Caso contrário, então **AzureVM iniciar** ou **Interromper AzureVM** é usados para tentar iniciar ou interromper a máquina virtual com o resultado da solicitação armazenado em uma variável.  Então, uma mensagem é enviada à saída especificando se a solicitação para iniciar ou interromper foi enviada com êxito.


## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como trabalhar com filho runbooks, consulte [runbooks filho na automação do Azure](automation-child-runbooks.md) 
- Para saber mais sobre as mensagens de saída durante a execução de runbook e registro em log para ajudar a solucionar problemas, consulte [Runbook saída e mensagens em automação do Azure](automation-runbook-output-and-messages.md)
