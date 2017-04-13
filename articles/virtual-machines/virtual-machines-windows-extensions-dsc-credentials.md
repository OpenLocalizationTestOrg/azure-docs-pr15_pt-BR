<properties
   pageTitle="Transmissão de credenciais para Azure usando a DSC | Microsoft Azure"
   description="Visão geral sobre transmissão segura de credenciais para Azure máquinas virtuais usando a configuração de estado desejado do PowerShell"
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

# <a name="passing-credentials-to-the-azure-dsc-extension-handler"></a>Transmissão de credenciais para o manipulador de extensão DSC do Azure #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Este artigo discute a extensão de configuração de estado desejado para o Azure. Uma visão geral do manipulador de extensão DSC pode ser encontrada na [Introdução ao manipulador de extensão de configuração de estado do Azure desejado](virtual-machines-windows-extensions-dsc-overview.md). 


## <a name="passing-in-credentials"></a>Passando credenciais
Como parte do processo de configuração, você pode precisar configurar contas de usuário, acessar serviços ou instale um programa em um contexto de usuário. Para fazer essas coisas, você precisa fornecer credenciais. 

DSC possibilita parametrizadas configurações na qual as credenciais são passadas para a configuração e com segurança armazenadas em arquivos MOF. Manipulador de extensão Azure simplifica o gerenciamento de credencial, fornecendo gerenciamento automático de certificados. 

Considere o seguinte script de configuração de DSC que cria uma conta de usuário local com a senha especificada:

*user_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

É importante incluir *host nó local* como parte da configuração. Se esta política estiver ausente, as etapas a seguir não funcionam como o manipulador de extensão procura especificamente a instrução de host local de nó. Também é importante incluir typecast *[PsCredential]*, como esse tipo específico aciona a extensão para criptografar as credenciais. 

Publica esse script ao armazenamento de blob:

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Definir a extensão do Azure DSC e forneça a credencial:

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"
 
$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments
 
$vm | Update-AzureVM
```
## <a name="how-credentials-are-secured"></a>Como as credenciais são protegidas
Executar este código solicita uma credencial. Depois que ele for fornecido, ele é armazenado na memória brevemente. Quando ele for publicado com `Set-AzureVmDscExtension` cmdlet, ele é transmitido por HTTPS para a máquina virtual, onde Azure armazena criptografadas em disco, usando o certificado de máquina virtual local. Em seguida, brevemente é descriptografar na memória e criptografado novamente para passá-lo para DSC.

Esse comportamento é diferente de [usar configurações seguras sem o manipulador de extensão](https://msdn.microsoft.com/powershell/dsc/securemof). O ambiente do Azure oferece uma maneira para transmitir dados de configuração de forma segura por meio de certificados. Ao usar o manipulador de extensão DSC, não é necessário para fornecer $CertificatePath ou um $CertificateID / entrada $Thumbprint em ConfigurationData.


## <a name="next-steps"></a>Próximas etapas ##

Para obter mais informações sobre o manipulador de extensão do Azure DSC, consulte [Introdução ao manipulador de extensão de configuração de estado do Azure desejado](virtual-machines-windows-extensions-dsc-overview.md). 

Examine o [modelo do Gerenciador de recursos do Azure para a extensão de DSC](virtual-machines-windows-extensions-dsc-template.md).

Para obter mais informações sobre DSC do PowerShell, [visite o Centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Para localizar a funcionalidade adicional que você pode gerenciar com DSC do PowerShell, [navegar na Galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) para obter mais recursos de DSC.
