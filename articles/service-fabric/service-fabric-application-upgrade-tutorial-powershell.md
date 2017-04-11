<properties
   pageTitle="Atualização de serviço tecidos aplicativo usando o PowerShell | Microsoft Azure"
   description="Este artigo descreve a experiência de implantar um aplicativo de serviço tecidos, alterando o código e implantar uma atualização usando o PowerShell."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>


# <a name="service-fabric-application-upgrade-using-powershell"></a>Atualização do aplicativo de serviço tecidos usando o PowerShell

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
- [O Visual Studio](service-fabric-application-upgrade-tutorial.md)

<br/>

Usados com mais frequência e abordagem atualização recomendada é a atualização sem interrupção monitorar.  Azure tecidos de serviço monitora a integridade do aplicativo sendo atualizado com base em um conjunto de políticas de integridade. Depois que um domínio de atualização (UD) é atualizado, serviço tecidos avalia a integridade do aplicativo e passa para o próximo domínio de atualização ou falha a atualização dependendo das políticas de integridade.

Uma atualização do aplicativo monitorar pode ser realizada usando o gerenciado ou nativo APIs, PowerShell ou restante. Para obter instruções sobre como executar uma atualização usando o Visual Studio, consulte [atualizar seu aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md).

Com atualizações sem interrupção do serviço tecidos monitorado, o administrador do aplicativo pode configurar a política de avaliação de integridade que serviço tecidos usa para determinar se o aplicativo está íntegro. Além disso, o administrador pode configurar a ação a ser executada quando a avaliação de integridade falha (por exemplo, fazendo uma reversão automática.) Esta seção percorre uma atualização monitorar por um dos exemplos do SDK que usa o PowerShell.

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Etapa 1: Criar e implantar a amostra de objetos visuais


Criar e publicar o aplicativo clicando em projeto de aplicativo, **VisualObjectsApplication,** e selecionando o comando **Publicar** .  Para obter mais informações, consulte [tutorial de atualização de aplicativo de serviço tecidos](service-fabric-application-upgrade-tutorial.md).  Como alternativa, você pode usar o PowerShell para implantar seu aplicativo.

> [AZURE.NOTE] Antes de qualquer um dos comandos tecidos de serviço podem ser usados no PowerShell, primeiro é necessário se conectar ao cluster usando o `Connect-ServiceFabricCluster` cmdlet. Da mesma forma, será considerado que o Cluster já foi configurado em sua máquina local. Consulte o artigo sobre como [configurar seu ambiente de desenvolvimento do serviço tecidos](service-fabric-get-started.md).

Depois de criar o projeto no Visual Studio, você pode usar o comando do PowerShell **ServiceFabricApplicationPackage copiar** para copiar o pacote de aplicativos para o ImageStore. A próxima etapa é registrar o aplicativo no tempo de execução do serviço tecidos usando o cmdlet **Register-ServiceFabricApplicationPackage** . A etapa final é iniciar uma instância do aplicativo usando o cmdlet **New-ServiceFabricApplication** .  Estas três etapas são análogas a usar o item de menu de **implantar** no Visual Studio.

Agora, você pode usar o [Gerenciador de estrutura de serviço para exibir o cluster e o aplicativo](service-fabric-visualizing-your-cluster.md). O aplicativo tem um serviço web que pode ser acessado no Internet Explorer, digitando [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) na barra de endereços.  Você deve ver alguns objetos visual flutuantes percorrendo a tela.  Além disso, você pode usar o **Get-ServiceFabricApplication** para verificar o status do aplicativo.

## <a name="step-2-update-the-visual-objects-sample"></a>Etapa 2: Atualizar a amostra de objetos visuais

Você pode perceber que com a versão que foi implantada na etapa 1, os objetos visuais não giram. Vamos atualizar este aplicativo para um onde os objetos visuais também girar.

Selecione o projeto VisualObjects.ActorService dentro da solução de VisualObjects e abra o arquivo StatefulVisualObjectActor.cs. Dentro desse arquivo, navegue até o método `MoveObject`, comente `this.State.Move()`e Tire comentários `this.State.Move(true)`. Essa alteração gira os objetos após o serviço é atualizado.

Precisamos também atualizar o arquivo de *ServiceManifest.xml* (em PackageRoot) do projeto **VisualObjects.ActorService**. Atualize o *CodePackage* e a versão de serviço 2.0 e as linhas correspondentes no arquivo *ServiceManifest.xml* .
Você pode usar a opção de Visual Studio *Editar arquivos de manifesto* após você com o botão direito na solução para fazer as alterações de arquivo de manifesto.


Depois que as alterações são feitas, o manifesto deve parecer com o seguinte (partes realçados mostram as alterações):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Agora o arquivo de *ApplicationManifest.xml* (encontrado sob o projeto **VisualObjects** sob a solução **VisualObjects** ) é atualizado para a versão 2.0 do projeto **VisualObjects.ActorService** . Além disso, a versão do aplicativo é atualizada para 2.0.0.0 de 1.0.0.0. O *ApplicationManifest.xml* deve parecer com o trecho a seguir:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```


Agora, crie o projeto selecionando apenas o projeto **ActorService** e, em seguida, clicando e selecionando a opção de **criar** no Visual Studio. Se você selecionar **reconstruir todos**, você deve atualizar as versões para todos os projetos, como o código deve ter sido alterada. Próximo, vamos empacotar o aplicativo atualizado clicando em ***VisualObjectsApplication***, selecionando o Menu de estrutura de serviço e escolhendo o **pacote**. Esta ação cria um pacote de aplicativos que pode ser implantado.  Seu aplicativo atualizado está pronto para ser implantado.


## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Etapa 3: Decida em políticas de saúde e parâmetros de atualização

Familiarize-se com os [parâmetros de atualização do aplicativo](service-fabric-application-upgrade-parameters.md) e o [processo de atualização](service-fabric-application-upgrade.md) para obter uma boa compreensão dos vários parâmetros de atualização, tempos limite e o critério de integridade aplicada. Para este passo a passo, o critério de avaliação de integridade do serviço é definido como o padrão (e recomendável) valores, o que significa que todos os serviços e instâncias devem ser _eficaz,_ após a atualização.  

Entretanto, vamos aumentar a *HealthCheckStableDuration* para 60 segundos (de forma que os serviços são eficaz, pelo menos 20 segundos antes que a atualização prossiga para o próximo domínio de atualização).  Vamos também definir o *UpgradeDomainTimeout* seja 1200 segundos e o *UpgradeTimeout* seja 3000 segundos.

Por fim, vamos também definir o *UpgradeFailureAction* para reversão. Esta opção requer tecidos de serviço reverter o aplicativo para a versão anterior se encontrar problemas durante a atualização. Portanto, ao iniciar a atualização (na etapa 4), os seguintes parâmetros são especificados:

FailureAction = reversão

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000


## <a name="step-4-prepare-application-for-upgrade"></a>Etapa 4: Preparar o aplicativo para atualização

Agora o aplicativo é criado e pronto para ser atualizado. Se você abrir uma janela do PowerShell como administrador e tipo **Get-ServiceFabricApplication**, ele deve informar você que é o tipo de aplicativo 1.0.0.0 do **VisualObjects** foi implantado.  

O pacote de aplicativo é armazenado no seguinte caminho relativo onde você descompactado o SDK do serviço tecidos - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Você deve encontrar uma pasta de "Pacote" nesse diretório, onde o pacote de aplicativo está armazenado. Verificar os carimbos de hora para garantir que ele seja a compilação mais recente (talvez você precise modifique os caminhos adequadamente também).

Agora vamos copiar o pacote de aplicativo atualizado para o ImageStore de estrutura de serviço (onde os pacotes do aplicativo são armazenados pelo serviço tecidos). O parâmetro *ApplicationPackagePathInImageStore* informa tecidos de serviço onde ele pode localizar o pacote de aplicativos. Criamos o aplicativo atualizado "VisualObjects\_V2" com o seguinte comando (talvez seja necessário modificar caminhos novamente adequadamente).

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

A próxima etapa é registrar este aplicativo com estrutura de serviço, que podem ser executadas usando o seguinte comando:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Se o comando anterior não tiver êxito, é provável que você tenha uma recriação de todos os serviços. Conforme mencionado na etapa 2, você pode precisar atualizar sua versão de Serviçoweb também.

## <a name="step-5-start-the-application-upgrade"></a>Etapa 5: Iniciar a atualização do aplicativo

Agora, podemos está tudo pronto para iniciar a atualização do aplicativo usando o seguinte comando:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


O nome do aplicativo é a mesma que ele foi descrito no arquivo *ApplicationManifest.xml* . Estrutura de serviço usa esse nome para identificar qual aplicativo está obtendo atualizado. Se você definir os tempos limite ser muito curta, você pode encontrar uma mensagem de falha afirmando o problema. Consulte a seção solução de problemas ou aumentar os tempos limite.

Agora, como a renda de atualização do aplicativo, você pode monitorar usando o serviço tecidos Explorer ou usando o PowerShell seguinte comando: **tecidos Get-ServiceFabricApplicationUpgrade: / VisualObjects**.

Em poucos minutos, o status obtido usando o comando anterior do PowerShell, deverá indicar que todos os domínios de atualização foram atualizados (concluído). E você deve encontrar se os objetos visuais na janela do navegador foram iniciados girando!

Você pode tentar atualizar da versão 2 para a versão 3 ou da versão 2 para a versão 1 como um exercício. Movendo da versão 2 para a versão 1 também é considerado uma atualização. Reproduzir com tempos limite e diretivas de integridade fazer por conta própria familiarizados com eles. Quando você estiver implantando um cluster Azure, os parâmetros precisam ser definidas adequadamente. É bom definir os tempos limite de maneira segura.


## <a name="next-steps"></a>Próximas etapas

[Atualizar seu aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) conduz você por uma atualização do aplicativo usando o Visual Studio.

Controle como o seu aplicativo atualizações usando [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Torne suas atualizações de aplicativo compatíveis aprender como usar a [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada durante a atualização de seu aplicativo fazendo referência a [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrigi problemas comuns em atualizações de aplicativo consultando as etapas em [atualizações de aplicativo de solução de problemas](service-fabric-application-upgrade-troubleshooting.md).
