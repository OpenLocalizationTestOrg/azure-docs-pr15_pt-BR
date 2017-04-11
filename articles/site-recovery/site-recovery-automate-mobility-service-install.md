<properties
    pageTitle="Duplicar máquinas virtuais VMware para Azure usando a recuperação de Site com DSC de automação do Azure | Microsoft Azure"
    description="Descreve como usar DSC de automação do Azure para implantar automaticamente o serviço de mobilidade de recuperação de Site do Azure e o Azure agente para máquinas virtuais/físico no Azure."
    services="site-recovery"
    documentationCenter=""
    authors="krnese"
    manager="lorenr"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="krnese"/>

# <a name="replicate-vmware-virtual-machines-to-azure-by-using-site-recovery-with-azure-automation-dsc"></a>Duplicar máquinas virtuais VMware para Azure usando a recuperação de Site com DSC de automação do Azure

No conjunto de gerenciamento de operações, fornecemos um backup abrangente e solução de recuperação que você pode usar como parte do seu plano de continuidade de negócios.

Podemos iniciado essa jornada junto com Hyper-V usando Hyper-V Replica. Mas podemos ter expandido para oferecer suporte a uma configuração heterogênea porque clientes tem vários hipervisores e plataformas em suas nuvens.

Se você estiver executando VMware cargas de trabalho e/ou servidores físicos hoje, um servidor de gerenciamento executa todos os componentes de recuperação de Site do Azure em seu ambiente para lidar com a replicação de dados e comunicação com o Azure, quando Azure é o seu destino.

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>Implantar o serviço de mobilidade de recuperação de Site usando DSC de automação
Vamos começar fazendo uma análise rápida do que faz esse servidor de gerenciamento.

O servidor de gerenciamento executa várias funções de servidor. Uma dessas funções é *configuração*, que coordenadas comunicação e gerencia processos de replicação e a recuperação de dados.

Além disso, a função de *processo* atua como um gateway de replicação. Esta função recebe dados de replicação de máquinas de origem protegido, otimizá-lo com o cache, compactação e criptografia e envia para uma conta de armazenamento do Azure. Uma das funções para a função de processo também é para instalação do serviço de mobilidade por push para máquinas protegidas e executar a descoberta automática de VMs VMware.

Se houver um failback do Azure, a função de *destino mestre* irá manipular os dados de replicação como parte dessa operação.

Para as máquinas protegidas, contamos com o *serviço de mobilidade*. Este componente é implantado para cada máquina (VM VMware ou servidor físico) que você deseja duplicar para Azure. Ele captura gravações de dados na máquina e encaminha para o servidor de gerenciamento (função de processo).

Quando você está lidando com continuidade de negócios, é importante entender suas cargas de trabalho, sua infraestrutura e os componentes envolvidos. Em seguida, você pode cumprir os requisitos para seu objetivo de tempo de recuperação (RTO) e o objetivo de ponto de recuperação (RPO). Nesse contexto, o serviço de mobilidade é fundamental para garantir que as cargas de trabalho são protegidas conforme esperado.

Então, como você pode, de maneira otimizada, garantir que você tenha uma configuração protegida confiável com a Ajuda de alguns componentes do pacote de gerenciamento de operações?

Este artigo fornece um exemplo de como você pode usar o Azure automação desejado estado configuração (DSC), junto com a recuperação do Site, para garantir que:

- O serviço de mobilidade e agente de máquina virtual do Azure são implantados máquinas Windows que você deseja proteger.
- O serviço de mobilidade e agente de máquina virtual do Azure sempre estão executando quando Azure é o destino de replicação.

## <a name="prerequisites"></a>Pré-requisitos

- Um repositório para armazenar a configuração necessária
- Um repositório para armazenar a senha necessária para registrar com o servidor de gerenciamento

 > [AZURE.NOTE] Uma senha exclusiva é gerada para cada servidor de gerenciamento. Se você fizer implantar vários servidores de gerenciamento, é necessário garantir que a senha correta está armazenada no arquivo passphrase.txt.

- Windows Management Framework (WMF) 5.0 instalado nas máquinas que você deseja habilitar proteção (um requisito para automação DSC)

 > [AZURE.NOTE] Se você quiser usar máquinas DSC para Windows que possuem WMF 4.0 instalado, consulte a seção [Usar DSC em ambientes desconectados](#Use DSC in disconnected environments).

O serviço de mobilidade pode ser instalado por meio da linha de comando e aceita vários argumentos. É por isso que você precisa ter os binários (após a extração-los da sua configuração) e armazená-los em um lugar onde você poderá recuperá-las usando uma configuração de DSC.

## <a name="step-1-extract-binaries"></a>Etapa 1: Binários de extrair

1. Para extrair os arquivos que você precisa para essa configuração, navegue até o seguinte diretório no seu servidor de gerenciamento:

    **\Microsoft Site azure Recovery\home\svsystems\pushinstallsvc\repository**

    Nesta pasta, você deverá ver um arquivo MSI chamado:

    **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe**

    Use o seguinte comando para extrair o instalador:

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**

2. Selecione todos os arquivos e enviá-los para uma pasta compactada (zipada).

Agora você tem os binários que você precisa para automatizar a configuração do serviço mobilidade usando DSC de automação.

### <a name="passphrase"></a>Senha

Em seguida, você precisa determinar onde você deseja colocar essa pasta compactada. Você pode usar uma conta de armazenamento do Azure, conforme mostrado posterior, para armazenar a senha que você precisa para a configuração. O agente então registrará com o servidor de gerenciamento como parte do processo.

A senha que você recebeu quando você implantou o servidor de gerenciamento pode ser salvas em um arquivo de texto como passphrase.txt.

Coloque a pasta compactada e a senha em um contêiner de dedicado na conta de armazenamento do Azure.

![Local da pasta](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Se você preferir manter esses arquivos em um compartilhamento na sua rede, você poderá fazer isso. Basta garantir que o recurso de DSC que você usará posteriormente tem acesso e pode obter a configuração e a senha.

## <a name="step-2-create-the-dsc-configuration"></a>Etapa 2: Criar a configuração de DSC

A configuração depende WMF 5.0. Para a máquina aplicar com êxito a configuração através de automação DSC, WMF 5.0 precisa estar presente.

O ambiente usa a configuração de DSC de exemplo a seguir:

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
A configuração irá fazer o seguinte:

- As variáveis informará a configuração onde obter os binários para o serviço de mobilidade e o agente de máquina virtual do Azure, onde obter a senha e onde armazenar a saída.
- A configuração importará o recurso de xPSDesiredStateConfiguration DSC, para que você possa usar `xRemoteFile` para baixar os arquivos do repositório.
- A configuração criará um diretório onde você deseja armazenar os binários.
- O recurso de arquivo morto irá extrair os arquivos da pasta compactada.
- O pacote de instalação do recurso será instalado o serviço de mobilidade da UNIFIEDAGENT. Instalador EXE com os argumentos específicos. (As variáveis que construir os argumentos precisam ser alteradas para refletir o seu ambiente.)
- O pacote AzureAgent recurso irá instalar o agente de máquina virtual do Azure, que é recomendado em cada máquina virtual executado no Azure. O agente de máquina virtual do Azure também possibilita adicionar extensões para a máquina virtual após failover.
- O recurso de serviço ou recursos garantirá que os serviços de mobilidade relacionados e os serviços do Azure são sempre a execução.

Salve a configuração como **ASRMobilityService**.

>[AZURE.NOTE] Lembre-se de substituir o CSIP em sua configuração para refletir o servidor de gerenciamento real, para que o agente será conectado corretamente e usará a senha correta.

## <a name="step-3-upload-to-automation-dsc"></a>Etapa 3: Carregar a automação DSC

Porque a configuração de DSC que você fez importará um módulo de recurso DSC necessário (xPSDesiredStateConfiguration), você precisa importar módulo na automação antes de carregar a configuração de DSC.

Entre em sua conta de automação, navegue até **ativos** > **módulos**e clique em **Galeria de procurar**.

Aqui você pode procurar o módulo e importá-las à sua conta.

![Módulo de importação](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Quando terminar de isso, acesse sua máquina onde tem os módulos do Gerenciador de recursos do Azure instalados e prossiga para importar a configuração de DSC recém-criado.

### <a name="import-cmdlets"></a>Cmdlets de importação

No PowerShell, entrar à sua assinatura do Azure. Modifique os cmdlets para refletir seu ambiente e capture as informações da sua conta de automação em uma variável:

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Carregar a configuração DSC de automação usando o seguinte cmdlet:

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>Compilar a configuração no DSC de automação

Em seguida, você precisa compilar a configuração no DSC de automação, para que você possa iniciar para registrar nós a ele. Você pode obter que executando o seguinte cmdlet:

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Isso pode demorar alguns minutos, pois você está basicamente a configuração para implantar o serviço de recepção DSC hospedado.

Após compilar a configuração, você pode recuperar as informações de trabalho usando o PowerShell (Get-AzureRmAutomationDscCompilationJob) ou usando o [portal do Azure](https://portal.azure.com/).

![Recuperar o trabalho](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Você agora com êxito publicados e carregado sua configuração DSC DSC de automação.

## <a name="step-4-onboard-machines-to-automation-dsc"></a>Etapa 4: Máquinas integradas DSC de automação
>[AZURE.NOTE] Um dos pré-requisitos para concluir esse cenário é que suas máquinas do Windows são atualizadas com a versão mais recente do WMF. Você pode baixar e instalar a versão correta para a sua plataforma no [Centro de Download](https://www.microsoft.com/download/details.aspx?id=50395).

Agora, você criará um metaconfig para DSC que você aplicará a seus nós. Para ter êxito com isso, você precisa recuperar a URL do ponto de extremidade e a chave primária para sua conta de automação selecionada no Azure. Você pode encontrar esses valores em **chaves** na lâmina **todas as configurações** da conta de automação.

![Valores-chave](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

Neste exemplo, você tem um servidor físico do Windows Server 2012 R2 que você deseja proteger usando recuperação do Site.

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>Verificar se há qualquer pendente operações de renomeação de arquivo no registro

Antes de começar a associar o servidor com o ponto de extremidade de automação DSC, recomendamos que você verificar qualquer pendente operações de renomeação de arquivo no registro. Eles podem proibir a configuração de terminando devido a uma reinicialização pendente.

Execute o seguinte cmdlet para verificar que não há nenhuma reinicialização pendente no servidor:

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Se isso mostra vazio, você está Okey para continuar. Caso contrário, você deve lidar com isso reiniciando o servidor durante uma janela de manutenção.

Para aplicar a configuração no servidor, inicie o ambiente de script integrado do PowerShell (ISE) e execute o seguinte script. Isso é essencialmente uma configuração local do DSC que irá instruir o mecanismo de Gerenciador de configuração de Local para registrar com o serviço de automação DSC e recuperar a configuração específica (ASRMobilityService.localhost).

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Essa configuração fará com que o mecanismo de Gerenciador de configuração de Local para se registrar com DSC de automação. Ele também determinará como o mecanismo deve operar, o que ele deve fazer se houver um símbolo de configurações (ApplyAndAutoCorrect) e como ele deve prosseguir com a configuração se é necessário reinicializar.

Depois de executar este script, o nó deve começar a registrar com DSC de automação.

![Registro de nó em andamento](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Se você voltar para o portal do Azure, você pode ver que o nó recém registrado agora apareceu no portal.

![Nó registrado no portal](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

No servidor, você pode executar o seguinte cmdlet do PowerShell para verificar se o nó foi registrado corretamente:

```powershell
Get-DscLocalConfigurationManager
```

Depois que a configuração foi puxada e aplicada ao servidor, você pode verificar isso, execute o seguinte cmdlet:

```powershell
Get-DscConfigurationStatus
```

A saída mostra que o servidor tem puxado com êxito sua configuração:

![Saída](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

Além disso, a configuração de serviço de mobilidade tem seu próprio log que pode ser encontrado em \ProgramData\ASRSetupLogs *unidade do sistema*.

É isso. Agora você tem implantado-las com êxito e registrou o serviço de mobilidade na máquina que você deseja proteger usando recuperação do Site. DSC irá se certificar de que os serviços necessários são sempre executando.

![Implantação bem-sucedida](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Depois que o servidor de gerenciamento detecta a implantação bem-sucedida, você pode configurar a proteção e habilitar replicação na máquina usando a recuperação do Site.

## <a name="use-dsc-in-disconnected-environments"></a>Use DSC em ambientes desconectados

Se suas máquinas não estiverem conectadas à Internet, você ainda pode depender DSC implantar e configurar o serviço de mobilidade nas cargas de trabalho que você deseja proteger.

Você pode criar seu próprio servidor de recepção DSC em seu ambiente para fornecer essencialmente os mesmos recursos que obteve de automação DSC. Ou seja, os clientes busca a configuração (depois que ele está registrado) para o ponto de extremidade de DSC. No entanto, outra opção é por push manualmente a configuração de DSC para suas máquinas, local ou remotamente.

Observe que, neste exemplo, há um parâmetro adicional para o nome do computador. Os arquivos remotos agora estão localizados em um compartilhamento remoto que deve ser acessado por máquinas que você deseja proteger. O final do script torna realidade a configuração e, em seguida, começa a aplicar a configuração de DSC ao computador de destino.

### <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que o módulo do PowerShell xPSDesiredStateConfiguration está instalado. Para computadores Windows onde WMF 5.0 está instalado, você poderá instalar o módulo xPSDesiredStateConfiguration executando o cmdlet a seguir nas máquinas destino:

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

Você também pode baixar e salvar o módulo, caso você precise distribuí-lo para máquinas do Windows que têm WMF 4.0. Execute este cmdlet em um computador onde PowerShellGet (WMF 5.0) está presente:

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Também para 4.0 WMF, verifique se o [Windows 8.1 atualizar KB2883200](https://www.microsoft.com/download/details.aspx?id=40749) está instalado nas máquinas.

A seguinte configuração pode ser enviada a máquinas de Windows que têm WMF 5.0 e 4.0 de WMF:

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Se você quiser criar seu próprio servidor de recepção DSC na sua rede corporativa para simular os recursos que você pode acessar de automação DSC, consulte [Configurar um servidor de recepção DSC](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>Opcional: Implantar uma configuração de DSC usando um modelo do Gerenciador de recursos do Azure

Este artigo se concentrou em como você pode criar sua própria configuração DSC para implantar o serviço de mobilidade e o agente de máquina virtual do Azure – automaticamente e garantir que eles estejam em execução nas máquinas que você deseja proteger. Também temos um modelo do Gerenciador de recursos do Azure que implantará essa configuração DSC a uma conta de automação do Azure nova ou existente. O modelo usará parâmetros de entrada para criar ativos de automação que conterão as variáveis para seu ambiente.

Depois de implantar o modelo, você pode simplesmente consultar a etapa 4 neste guia para integrado suas máquinas.

O modelo irá fazer o seguinte:

1. Usar uma conta de automação existente ou crie um novo
2. Tome parâmetros de entrada:
    - ASRRemoteFile – o local em que você armazenou a configuração de serviço de mobilidade
    - ASRPassphrase – o local em que você armazenou no arquivo passphrase.txt
    - ASRCSEndpoint - o endereço IP do seu servidor de gerenciamento
3. Importar o módulo do PowerShell xPSDesiredStateConfiguration
4. Criar e compilar a configuração de DSC

Todas as etapas anteriores acontecerá na ordem correta, para que você possa iniciar integração suas máquinas para proteção.

O modelo, com instruções para implantação, está localizado no [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

Implante o modelo usando o PowerShell:

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>Próximas etapas

Depois de implantar os agentes de serviço de mobilidade, você pode [Habilitar a replicação](site-recovery-vmware-to-azure.md#step-6-replicate-applications) para as máquinas virtuais.
