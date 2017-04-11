<properties
   pageTitle="Implantação do aplicativo de serviço tecidos | Microsoft Azure"
   description="Como implantar e remover aplicativos em estrutura de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# <a name="deploy-and-remove-applications-using-powershell"></a>Implantar e remover aplicativos usando o PowerShell

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [O Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

Uma vez um [tipo de aplicativo foi empacotado][10], ele está pronto para implantação em um cluster de estrutura de serviço do Azure. Implantação envolve estas três etapas:

1. Carregar o pacote de aplicativo
2. Registrar o tipo de aplicativo
3. Criar a instância de aplicativo

>[AZURE.NOTE] Se você usa o Visual Studio para implantação e depuração de aplicativos em seu cluster de desenvolvimento local, todas as seguintes etapas são tratadas automaticamente por meio de um script do PowerShell encontrado na pasta Scripts do projeto do aplicativo. Este artigo fornece informações detalhadas sobre o que esses scripts estão fazendo para que você pode executar as mesmas operações fora do Visual Studio.

## <a name="upload-the-application-package"></a>Carregar o pacote de aplicativo

Carregar o pacote de aplicativo coloca em um local acessível pelos componentes de serviço tecidos internos. Você pode usar o PowerShell para executar o carregamento. Antes de executar qualquer comando do PowerShell neste artigo, sempre inicie usando [ServiceFabricCluster conectar](https://msdn.microsoft.com/library/mt125938.aspx) para conectar-se ao cluster tecidos de serviço.

Suponha que você tenha uma pasta chamada *MyApplicationType* que contém o manifesto do aplicativo necessário, manifestos de serviço e pacotes de dados/de configuração de código. O comando [Copiar ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125905.aspx) carrega o pacote para o armazenamento de imagens de cluster. O cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , que é parte do módulo do PowerShell do serviço tecidos SDK, é usado para obter a cadeia de conexão do repositório de imagem.  Para importar o módulo SDK, execute:

```
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Você pode copiar um pacote de aplicativos de *C:\users\ryanwi\Documents\Visual Studio 2015\Projects\MyApplication\myapplication\pkg\debug* para *c:\temp\MyApplicationType* (renomear o diretório "depuração" para "MyApplicationType"). O exemplo a seguir carrega o pacote:

~~~
PS C:\temp> dir

    Directory: c:\temp


Mode                LastWriteTime         Length Name                                                                                   
----                -------------         ------ ----                                                                                   
d-----        8/12/2016  10:23 AM                MyApplicationType                                                                          

PS C:\temp> tree /f .\Stateless1Pkg

C:\TEMP\MyApplicationType
│   ApplicationManifest.xml
|
└───Stateless1Pkg
  	|   ServiceManifest.xml
    │
    └───Code
    │   │  Microsoft.ServiceFabric.Data.dll
    │   │  Microsoft.ServiceFabric.Data.Interfaces.dll
    │   │  Microsoft.ServiceFabric.Internal.dll
    │   │  Microsoft.ServiceFabric.Internal.Strings.dll
    │   │  Microsoft.ServiceFabric.Services.dll
    │   │  ServiceFabricServiceModel.dll
    │   │  MyService.exe
    │   │  MyService.exe.config
    │   │  MyService.pdb
    │   │  System.Fabric.dll
    │   │  System.Fabric.Strings.dll
    │   │
    │   └───en-us
  	|         Microsoft.ServiceFabric.Internal.Strings.resources.dll
  	|         System.Fabric.Strings.resources.dll
  	|
    ├───Config
    │     Settings.xml
    │
    └───Data
          init.dat

PS C:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded

PS D:\temp>
~~~

## <a name="register-the-application-package"></a>Registrar o pacote de aplicativo

Registrar o pacote de aplicativo torna o tipo de aplicativo e versão declarados no manifesto do aplicativo disponível para uso. O sistema lê o pacote carregado na etapa anterior, verifique se o pacote (equivalente a execução [ServiceFabricApplicationPackage de teste](https://msdn.microsoft.com/library/mt125950.aspx) localmente), o conteúdo do pacote de processo e copie o pacote processado para um local de sistema interno.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

O comando [Register-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125958.aspx) retorna somente depois que o sistema foi copiado com êxito o pacote de aplicativos. Quanto tempo leva depende o conteúdo do pacote de aplicativo. Se necessário, o parâmetro **- TimeoutSec** pode ser usado para fornecer um tempo limite maior. (O tempo limite padrão é 60 segundos).

O comando [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125871.aspx) lista todas as versões de tipo de aplicativo registrado com êxito.

## <a name="create-the-application"></a>Criar o aplicativo

Você pode criar um aplicativo usando qualquer versão do tipo de aplicativo que foi registrada com êxito por meio do comando [New-ServiceFabricApplication](https://msdn.microsoft.com/library/mt125913.aspx) . O nome de cada aplicativo deve começar com o *tecidos:* esquema e ser exclusivo para cada instância do aplicativo. Quaisquer serviços padrão definidos no manifesto do aplicativo do tipo de aplicativo de destino são criados neste momento.

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

O comando [Get-ServiceFabricApplication](https://msdn.microsoft.com/library/mt163515.aspx) lista todas as instâncias de aplicativo que foram criadas com êxito, juntamente com seus status geral.

O comando [Get-ServiceFabricService](https://msdn.microsoft.com/library/mt125889.aspx) lista todas as instâncias de serviço que foram criadas com êxito dentro de uma instância de determinado aplicativo. Serviços de padrão (se houver) são listados aqui.

Várias instâncias do aplicativo podem ser criadas para qualquer determinada versão de um tipo de aplicativo registrado. Cada instância do aplicativo é executado em isolamento, com seu próprio diretório de trabalho e o processo.

## <a name="remove-an-application"></a>Remover um aplicativo

Quando uma instância do aplicativo não for mais necessário, você pode removê-lo permanentemente usando o comando [Remover ServiceFabricApplication](https://msdn.microsoft.com/library/mt125914.aspx) . Este comando remove automaticamente todos os serviços que pertencem ao aplicativo também, removendo permanentemente todo o estado de serviço. Esta operação não pode ser revertida e estado do aplicativo não poderão ser recuperado.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Quando uma versão específica de um tipo de aplicativo não é mais necessária, você deve cancelar o registro-lo usando o comando [Cancelar registro ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125885.aspx) . Cancelando o registro de tipos não utilizados libera espaço de armazenamento usado pelo conteúdo do pacote de aplicativo desse tipo no repositório de imagem. Um tipo de aplicativo pode ser cancelado, desde que nenhum aplicativo é criado em relação a ele e não pendentes aplicativo atualizações estão fazendo referência a ela.

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

## <a name="troubleshooting"></a>Solução de problemas

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Cópia ServiceFabricApplicationPackage solicita um ImageStoreConnectionString

O ambiente de serviço tecidos SDK já deve ter os padrões corretos configurar. Mas, se necessário, o ImageStoreConnectionString para todos os comandos deve corresponder ao valor que está usando o cluster de estrutura de serviço. Você pode encontrar isso no manifesto cluster recuperado por meio do comando [Get-ServiceFabricClusterManifest](https://msdn.microsoft.com/library/mt126024.aspx) :

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## <a name="next-steps"></a>Próximas etapas

[Atualização do aplicativo de serviço tecidos](service-fabric-application-upgrade.md)

[Introdução de integridade do serviço tecidos](service-fabric-health-introduction.md)

[Diagnosticar e solucionar problemas de um serviço de estrutura de serviço](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelar um aplicativo em estrutura de serviço](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
