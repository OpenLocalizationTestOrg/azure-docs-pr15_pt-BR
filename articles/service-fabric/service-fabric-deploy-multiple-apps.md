<properties
   pageTitle="Implantar um aplicativo de Node que usa MongoDB | Microsoft Azure"
   description="Explicação passo a passo sobre como empacotar vários executáveis de convidado para implantar um cluster de estrutura de serviço do Azure"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>


# <a name="deploy-multiple-guest-executables"></a>Implantar vários executáveis de convidado

Este artigo mostra como empacotar e implantar vários executáveis convidado à estrutura de serviço do Azure. Para criar e implantar um único pacote de serviço tecidos Leia como [implantar um convidado executável à estrutura de serviço](service-fabric-deploy-existing-app.md).

Embora este passo a passo mostra como implantar um aplicativo com um front-end de Node que usa MongoDB como o armazenamento de dados, você pode aplicar as etapas para qualquer aplicativo que possui dependências em outro aplicativo.   

Você pode usar o Visual Studio para gerar o pacote de aplicativo que contém vários executáveis de convidado. Consulte [Usando o Visual Studio para empacotar um aplicativo existente](service-fabric-deploy-existing-app.md#using-visual-studio-to-package-an-existing-executable). Depois de ter adicionado o primeiro executável de convidado, clique com o botão direito no projeto de aplicação e selecione **Adicionar -> nova estrutura de serviço do serviço** para adicionar o segundo projeto executável do convidado para a solução. Observação: Se você optar por vincular a fonte no projeto do Visual Studio, compilar a solução do Visual Studio, será garantir que seu pacote de aplicativo é atualizado com alterações na fonte. 

## <a name="manually-package-the-multiple-guest-executable-application"></a>Empacotar manualmente o aplicativo executável do convidado vários
Como alternativa, você pode empacotar manualmente o convidado executável. Para o pacote manual, este artigo usa a ferramenta de embalagem tecidos de serviço, que está disponível em [http://aka.ms/servicefabricpacktool](http://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Embalagem do aplicativo Node
Este artigo pressupõe que Node não está instalado em nós do cluster de serviço tecidos. Como consequência, você precisa adicionar Node.exe ao diretório raiz do seu aplicativo de nó antes de embalagem. A estrutura de diretório do aplicativo Node (usando estrutura de web Express e mecanismo de modelo Jade) deve parecer semelhante ao abaixo:

```
|-- NodeApplication
  	|-- bin
        |-- www
  	|-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
  	|-- public
        |-- images
        |-- etc.
  	|-- routes
        |-- index.js
        |-- users.js
  	|-- views
        |-- index.jade
        |-- etc.
  	|-- app.js
  	|-- package.json
  	|-- node.exe
```

Como a próxima etapa, você cria um pacote de aplicativos para o aplicativo Node. O código a seguir cria um pacote de aplicativo de serviço tecidos que contém o aplicativo Node.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Abaixo está uma descrição dos parâmetros que estão sendo usados:

- **/Source** aponta para o diretório do aplicativo que deve ser empacotado.
- **/Target** define o diretório em que o pacote deve ser criado. Este diretório deve ser diferente da pasta de origem.
- **/appname** define o nome do aplicativo do aplicativo existente. É importante entender o que isso significa para o nome do serviço no manifesto e não para o nome do aplicativo de serviço tecidos.
- **/exe** define o executável que serviço tecidos deveriam para iniciar, nesse caso `node.exe`.
- **/ma** define o argumento que está sendo usado para iniciar o executável. Como Node não estiver instalado, serviço tecidos precisa iniciar o servidor da web Node executando `node.exe bin/www`.  `/ma:'bin/www'`informa que a ferramenta de embalagem usar `bin/ma` como argumento para node.exe.
- **/AppType** define o nome de tipo de aplicativo de serviço tecidos.

Se você navegar para a pasta que foi especificada no parâmetro /Target., você pode ver que a ferramenta criou um pacote de serviço tecidos totalmente funcional, conforme mostrado abaixo:

```
|--[yourtargetdirectory]
  	|-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
O ServiceManifest.xml gerado agora tem uma seção que descreve como o servidor da web Node deve ser iniciado, conforme mostrado no trecho de código abaixo:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
Neste exemplo, o servidor da web Node ouve porta 3000, então você precisa atualizar as informações de ponto de extremidade no arquivo ServiceManifest.xml conforme mostrado abaixo.   

```xml
<Resources>
      <Endpoints>
        <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>O aplicativo MongoDB de embalagem
Agora que você compactar o aplicativo Node, você pode ir adiante e empacotar MongoDB. Conforme mencionado anteriormente, as etapas que você passam agora não são específicas node e MongoDB. Na verdade, elas se aplicam a todos os aplicativos que devem ser empacotados juntos como um aplicativo de serviço tecidos.  

Para criar pacotes MongoDB, você quer certificar-se de que pacote Mongod.exe e Mongo.exe. Ambos os binários estão localizados no `bin` diretório seu MongoDB do diretório de instalação. A estrutura de diretório procura semelhante ao abaixo.

```
|-- MongoDB
  	|-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Serviço tecidos precisa iniciar MongoDB com um comando semelhante à abaixo, então você precisa usar o `/ma` parâmetro quando MongoDB de embalagem.

```
mongod.exe --dbpath [path to data]
```
> [AZURE.NOTE] Os dados não estão sendo preservados no caso de uma falha de nó se você colocar o diretório de dados MongoDB no diretório local do nó. Você deve usar o armazenamento durável ou implementar uma réplica MongoDB definida para evitar perda de dados.  

No PowerShell ou o shell de comando, podemos executar a ferramenta de embalagem com os seguintes parâmetros:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Para adicionar MongoDB ao seu pacote de aplicativo de serviço tecidos, você precisa certificar-se de que os pontos de parâmetro /Target. para o mesmo diretório que já contém o aplicativo manifestam juntamente com o aplicativo Node. Você também precisa certificar-se de que você está usando o mesmo nome de ApplicationType.

Vamos navegue até o diretório e examinar a ferramenta que criou.

```
|--[yourtargetdirectory]
  	|-- MyNodeApplication
  	|-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
Como você pode ver, a ferramenta adicionado uma nova pasta, MongoDB, até a pasta que contém os binários MongoDB. Se você abrir o `ApplicationManifest.xml` arquivo, você pode ver que o pacote agora contém Node tanto o aplicativo MongoDB. O código a seguir mostra o conteúdo de manifesto do aplicativo.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>O aplicativo de publicação
A última etapa é publicar o aplicativo para o cluster de serviço tecidos local por meio dos scripts do PowerShell abaixo:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Depois que o aplicativo é publicado com êxito para o cluster local, você pode acessar o aplicativo Node na porta que estamos inseridas no manifesto do serviço do aplicativo Node – por exemplo http://localhost:3000.

Neste tutorial, você viu como empacotar facilmente os dois aplicativos existentes como um aplicativo de serviço tecidos. Você também aprendeu implantá-lo em estrutura de serviço para que ela pode se beneficiar alguns dos recursos tecidos de serviço, como alta disponibilidade e a integração do sistema de saúde.

## <a name="next-steps"></a>Próximas etapas

- Aprender sobre a implantação contêineres de visão geral de [serviço tecidos e contêineres](service-fabric-containers-overview.md)
