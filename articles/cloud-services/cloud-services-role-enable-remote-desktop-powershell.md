<properties
pageTitle="Habilitar a Conexão de área de trabalho remota para uma função em serviços de nuvem do Azure usando o PowerShell"
description="Como configurar seu aplicativo de serviço de nuvem azure usando o PowerShell para permitir conexões de área de trabalho remotas"
services="cloud-services"
documentationCenter=""
authors="thraka"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/05/2016"
ms.author="adegeo"/>

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Habilitar a Conexão de área de trabalho remota para uma função em serviços de nuvem do Azure usando o PowerShell

>[AZURE.SELECTOR]
- [Azure portal clássico](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [O Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


Área de trabalho remota permite que você acesse a área de trabalho de uma função em execução no Azure. Você pode usar uma conexão de área de trabalho remota para solucionar problemas e diagnosticar problemas com seu aplicativo enquanto ele é executado.

Este artigo descreve como habilitar a área de trabalho remota em suas funções de serviço de nuvem usando o PowerShell. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para os pré-requisitos necessários para este artigo. PowerShell utiliza a extensão de área de trabalho remota para que você pode habilitar a área de trabalho remota depois que o aplicativo for implantado.


## <a name="configure-remote-desktop-from-powershell"></a>Configurar a área de trabalho remota do PowerShell

O cmdlet [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) permite que você habilite a área de trabalho remota em funções especificadas ou todas as funções da sua implantação do serviço de nuvem. O cmdlet permite especificar o nome de usuário e senha para o usuário de desktop remoto por meio do parâmetro de *credencial* que aceita um objeto PSCredential.

Se você estiver usando o PowerShell interativamente, você pode definir facilmente o objeto PSCredential ligando para o cmdlet [Get-credenciais](https://technet.microsoft.com/library/hh849815.aspx) .

```
$remoteusercredentials = Get-Credential
```

Este comando exibe uma caixa de diálogo permitindo que você insira o nome de usuário e senha para o usuário remoto de maneira segura.

Como PowerShell Ajuda em cenários de automação, você também pode configurar o objeto **PSCredential** de uma maneira que não exige a interação do usuário. Primeiro, você precisa configurar uma senha segura. Você começa com especificando uma senha de texto sem formatação convertê-la em uma cadeia de caracteres segura usando [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Em seguida, você precisa converter essa cadeia de caracteres segura em uma cadeia de caracteres padrão criptografada usando [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Agora você pode salvar essa cadeia de caracteres padrão criptografada para um arquivo usando [Set-Content](https://technet.microsoft.com/library/ee176959.aspx).

Você também pode criar um arquivo de senha de segurança para que você não precisa digitar a senha toda vez. Além disso, um arquivo de senha de segurança é melhor do que um arquivo de texto sem formatação. Use o PowerShell seguinte para criar um arquivo de senha de segurança:

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

>[AZURE.IMPORTANT] Ao definir a senha, certifique-se de que você atende aos [requisitos de complexidade](https://technet.microsoft.com/library/cc786468.aspx).

Para criar o objeto de credencial do arquivo de senha de segurança, você deve ler o conteúdo do arquivo e convertê-las novamente uma cadeia de caracteres segura usando [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

O cmdlet [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) também aceita um parâmetro de *expiração* , que especifica a **Data e hora** em que a conta de usuário expira. Por exemplo, você pode definir a conta expire alguns dias entre a data e hora atuais.

Este exemplo PowerShell mostra como configurar a extensão de área de trabalho remota em um serviço de nuvem:

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Você também pode especificar o slot de implantação e funções que você deseja ativar a área de trabalho remota. Se esses parâmetros não forem especificados, o cmdlet permite que a área de trabalho remota em todas as funções no slot de implantação de **produção** .

A extensão de área de trabalho remota está associada uma implantação. Se você criar uma nova implantação do serviço, você precisa habilitar a área de trabalho remota nessa implantação. Se você quiser sempre habilitado para a área de trabalho remota, você deve considerar integrar dos scripts do PowerShell seu fluxo de trabalho de implantação.


## <a name="remote-desktop-into-a-role-instance"></a>Área de trabalho remota em uma instância de função
O cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) é usado para área de trabalho remota em uma instância de função específica do seu serviço de nuvem. Você pode usar o parâmetro *LocalPath* para baixar o arquivo RDP localmente. Ou você pode usar o parâmetro *Iniciar* para iniciar diretamente a caixa de diálogo Conexão de área de trabalho remota para acessar a instância de função do serviço de nuvem.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Verifique se a extensão de área de trabalho remota está ativado em um serviço
O cmdlet [Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) exibe que área de trabalho remota está habilitada ou desabilitada em uma implantação de serviço. O cmdlet retorna o nome de usuário para o usuário de desktop remoto e as funções que a extensão de área de trabalho remota está habilitada para. Por padrão, isso acontece no slot implantação e você pode optar por usar o slot preparação em vez disso.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Remover extensão de área de trabalho remota de um serviço
Se você já tiver ativado a extensão da área de trabalho remota em uma implantação e precisa atualizar as configurações de desktop remoto, primeiro remova a extensão. E ativá-lo novamente com as novas configurações. Por exemplo, se você deseja definir uma nova senha da conta de usuário remoto ou a conta expirou. Isso é necessário em implantações existentes que têm a extensão de desktop remota habilitada. Para implantações de novo, você pode simplesmente aplicar a extensão diretamente.

Para remover a extensão da área de trabalho remota de implantação, você pode usar o cmdlet [Remove-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx) . Você também pode especificar o slot de implantação e a função do qual você deseja remover a extensão da área de trabalho remota.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

>[AZURE.NOTE] Para remover completamente a configuração de extensão, você deve ligar para o cmdlet *Remover* com o parâmetro **UninstallConfiguration** .
>
>O parâmetro **UninstallConfiguration** é desinstalado qualquer configuração de extensão que é aplicada ao serviço. Todas as configurações de extensão está associada com a configuração do serviço. Portanto, chamar o cmdlet *Remover* sem **UninstallConfiguration** desassocia a <mark>implantação</mark> da configuração de extensão, removendo efetivamente a extensão. No entanto, a configuração de extensão permanecerão associada com o serviço.



## <a name="additional-resources"></a>Recursos adicionais

[Como configurar os serviços de nuvem](cloud-services-how-to-configure.md)
