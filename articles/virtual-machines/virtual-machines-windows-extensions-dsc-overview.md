<properties
   pageTitle="Desejado estada configuração de visão geral do Azure | Microsoft Azure"
   description="Visão geral para usar o manipulador de extensão Microsoft Azure para configuração de estado do PowerShell desejado. Incluindo pré-requisitos, arquitetura, cmdlets.."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introdução ao manipulador de extensão de configuração de estado desejado do Azure #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

O agente de máquina virtual do Azure e extensões associadas são parte dos serviços de infraestrutura de Microsoft Azure. Extensões de máquina virtual são componentes de software que estendem a funcionalidade de máquina virtual e simplificam a várias operações de gerenciamento de máquina virtual. Por exemplo, a extensão de VMAccess pode ser usada para redefinir uma senha de administrador ou a extensão de Script personalizado pode ser usada para executar um script a máquina virtual.

Este artigo apresenta a extensão de configuração de estado de desejado (DSC) do PowerShell para VMs Azure como parte do SDK do PowerShell do Azure. Você pode usar novos cmdlets para carregar e aplicar uma configuração de DSC PowerShell em uma VM Azure habilitado com a extensão do PowerShell DSC. As extensão do PowerShell DSC chamadas em PowerShell DSC apliquem a configuração de DSC recebida na máquina virtual. Essa funcionalidade também está disponível por meio do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos ##
**Máquina local** Para interagir com a extensão de máquina virtual do Azure, você precisa usar o portal do Azure ou o SDK do PowerShell Azure. 

**Agente de convidado** A máquina virtual do Azure que serão configuradas pela configuração DSC precisa ser um sistema operacional compatível com o Windows Management Framework (WMF) 4.0 ou 5.0. A lista completa de versões com suporte do sistema operacional pode ser encontrada o [Histórico de versões de extensão DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termos e conceitos ##
Este guia pressupõe familiaridade com os seguintes conceitos:

Configuração - um documento de configuração de DSC. 

Nó - um destino para uma configuração de DSC. Neste documento, "nó" sempre se refere a uma máquina virtual do Azure.

Dados de configuração - um .psd1 contendo dados ambientais para uma configuração de arquivo

## <a name="architectural-overview"></a>Visão geral da arquitetura ##

A extensão do Azure DSC usa a estrutura de agente de máquina virtual do Azure para oferecer, ativar e gerar um relatório sobre configurações de DSC em execução no Azure VMs. A extensão de DSC espera um arquivo. zip que contém pelo menos um documento de configuração e um conjunto de parâmetros fornecido por meio de SDK do PowerShell do Azure ou o portal do Azure.

Quando a extensão é chamada pela primeira vez, ele executa um processo de instalação. Esse processo instala uma versão do Windows Management Framework (WMF) usando a seguinte lógica:

1. Se o sistema operacional máquina virtual do Azure for Windows Server 2016, nenhuma ação é executada. Windows Server 2016 já tem a versão mais recente do PowerShell instalado.
2. Se o `wmfVersion` propriedade for especificada, essa versão do WMF é instalado, a menos que ele é incompatível com o sistema operacional da VM.
3. Se nenhuma `wmfVersion` propriedade for especificada, a última versão aplicável do WMF está instalada.

Instalação do WMF requer a reinicialização. Após a reinicialização, a extensão de downloads do arquivo. zip especificado na `modulesUrl` propriedade. Se esse local está no armazenamento de blob do Microsoft Azure, um token SAS pode ser especificado a `sasToken` propriedade para acessar o arquivo. Após. zip é baixado e descompactados, a função de configuração definidos no `configurationFunction` é executado para gerar o arquivo MOF. Em seguida, executa a extensão `Start-DscConfiguration -Force` no arquivo MOF gerado. A extensão captura gravações-os de volta para o canal de Status do Azure e saída. Neste ponto em diante, o MMC DSC manipula monitoramento e correção como normal. 

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell ##

Cmdlets do PowerShell pode ser usado com BRAÇO ou ASM empacotar, publicar e monitorar implantações de extensão DSC. Os seguintes cmdlets listados são os módulos ASM, mas "Azure" pode ser substituído por "AzureRm" para usar o modelo ARM. Por exemplo, `Publish-AzureVMDscConfiguration` usa ASM, onde `Publish-AzureRmVMDscConfiguration` usa ARM. 

`Publish-AzureVMDscConfiguration`leva em um arquivo de configuração, ela procura recursos DSC dependentes e cria um arquivo. zip que contém a configuração e recursos de DSC necessários para ativar a configuração. Ele também pode criar o pacote localmente usando o `-ConfigurationArchivePath` parâmetro. Caso contrário, ele publica o arquivo. zip ao Azure blob storage e protegê-lo com um token SAS.

O arquivo. zip criado por esse cmdlet tem o script de configuração. ps1 na raiz da pasta de arquivamento. Recursos têm a pasta de módulo colocada na pasta de arquivo morto. 

`Set-AzureVMDscExtension`Insira as configurações necessárias pela extensão do PowerShell DSC em um objeto de configuração de máquina virtual, que pode ser aplicado a uma VM Azure com `Update-AzureVM`.

`Get-AzureVMDscExtension`recupera o status de extensão DSC de uma determinada VM. 

`Get-AzureVMDscExtensionStatus`recupera o status da configuração do DSC aprovado pelo manipulador de extensão de DSC. Esta ação pode ser executada em uma única VM ou grupo de VMs.

`Remove-AzureVMDscExtension`Remove o manipulador de extensão de uma determinada máquina virtual. Esse cmdlet **não** remover a configuração, desinstalar o WMF, ou alterar as configurações aplicadas na máquina virtual. Ela remove somente o manipulador de extensão. 

**Principais diferenças nos cmdlets ASM e ARM**

- Cmdlets ARM são síncronos. Cmdlets ASM são assíncronas.
- ResourceGroupName, VMName, ArchiveStorageAccountName, versão e local são todos os novos parâmetros necessários.
- ArchiveResourceGroupName é um novo parâmetro opcional para ARM. Você pode especificar esse parâmetro quando sua conta de armazenamento pertence a um grupo de recursos diferente daquela onde a máquina virtual é criada.
- ConfigurationArchive é chamado ArchiveBlobName em ARM
- ContainerName é chamado ArchiveContainerName em ARM
- StorageEndpointSuffix é chamado ArchiveStorageEndpointSuffix em ARM
- A opção de AutoUpdate foi adicionada para BRAÇO para ativar a atualização automática do manipulador de extensão para a versão mais recente como e quando ela estiver disponível. Observe que esse parâmetro tem o potencial de causar reinicializa na VM quando for lançada uma nova versão do WMF. 


## <a name="azure-portal-functionality"></a>Funcionalidade de portal Azure ##
Navegue até uma máquina virtual clássica. Em Configurações -> clique geral "Extensões." Um novo painel é criado. Clique em "Adicionar" e selecione PowerShell DSC.

O portal precisa de entrada.
**Módulos de configuração ou Script**: esse campo é obrigatório. Requer um arquivo. ps1 contendo um script de configuração ou um arquivo. zip com um script de configuração. ps1 em raiz e todos os recursos dependentes em pastas de módulo dentro do. zip. Ele pode ser criado com o `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` cmdlet incluído no SDK do PowerShell do Azure. O arquivo. zip é carregado em seu armazenamento de blob do usuário protegido por um token SAS. 

**Arquivo de PSD1 de dados de configuração**: esse campo é opcional. Se sua configuração requer um arquivo de dados de configuração em .psd1, use este campo para selecioná-la e carregue-o no seu armazenamento de blob do usuário, onde ele é protegido por um token SAS. 
 
**Nome do Module-Qualified da configuração**: arquivos. ps1 podem ter várias funções de configuração. Insira o nome do script. ps1 configuração seguido por um '\' e o nome da função configuração. Por exemplo, se seu script. ps1 tem o nome "configuration.ps1" e a configuração é "IisInstall", digite:`configuration.ps1\IisInstall`

**Argumentos de configuração**: se a função de configuração leva argumentos, insira-os aqui no formato `argumentName1=value1,argumentName2=value2`. Observe que o formato é um formato diferente como argumentos de configuração são aceitos pelos cmdlets do PowerShell ou Gerenciador de recursos de modelos. 

## <a name="getting-started"></a>Guia de Introdução ##

A extensão do Azure DSC leva em documentos de configuração de DSC e torna realidade-los em VMs do Azure. Segue um exemplo simple de uma configuração. Salve localmente como "IisInstall.ps1":

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

As etapas a seguir colocar o script IisInstall.ps1 na VM especificada, executar a configuração e voltar relatório sobre o status.
 
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "demo.ps1.zip" -StorageContext $storageContext -ConfigurationName "runScript" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```

## <a name="logging"></a>Registro em log ##

Logs são colocados na:

C:\WindowsAzure\Logs\Plugins\Microsoft.PowerShell.DSC\[número da versão]

## <a name="next-steps"></a>Próximas etapas ##

Para obter mais informações sobre DSC do PowerShell, [visite o Centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Examine o [modelo do Gerenciador de recursos do Azure para a extensão de DSC](virtual-machines-windows-extensions-dsc-template.md
). 

Para localizar a funcionalidade adicional que você pode gerenciar com DSC do PowerShell, [navegar na Galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) para obter mais recursos de DSC.

Para obter detalhes sobre passando parâmetros confidenciais em configurações, consulte [Gerenciar credenciais com segurança com o manipulador de extensão DSC](virtual-machines-windows-extensions-dsc-credentials.md).
