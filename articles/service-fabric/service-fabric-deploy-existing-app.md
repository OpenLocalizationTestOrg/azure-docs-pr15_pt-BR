<properties
   pageTitle="Implantar um executável existente em tecidos de serviço do Azure | Microsoft Azure"
   description="Explicação passo a passo sobre como empacotar um aplicativo existente como um convidado executável, para que ele pode ser implantado em um cluster de estrutura de serviço"
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
   ms.workload="na"
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>

# <a name="deploy-a-guest-executable-to-service-fabric"></a>Implantar um convidado executável em tecidos de serviço

Você pode executar qualquer tipo de aplicativo, como Node, Java ou aplicativos nativos em estrutura de serviço do Azure. Serviço tecidos se refere a esses tipos de aplicativos como convidado executáveis.
Convidado executáveis são tratados pelo serviço tecidos como os serviços de estado. Como resultado, eles são colocados em nós em um cluster, com base na disponibilidade e outras métricas. Este artigo descreve como empacotar e implantar o convidado executável em um cluster de estrutura de serviço, usando o Visual Studio ou um utilitário de linha de comando.

Neste artigo, cobriremos as etapas para empacotar convidado executável e implantá-lo em estrutura de serviço.  

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Benefícios da execução do convidado executável na estrutura de serviço

Há várias vantagens que vêm com a execução do convidado executável em um cluster de estrutura de serviço:

- Alta disponibilidade. Aplicativos que são executados em estrutura de serviço são disponibilizados altamente. Serviço tecidos garante que instâncias de um aplicativo estão executando.
- Monitoramento de integridade. Monitoramento de integridade do serviço tecidos detecta se um aplicativo está sendo executado e fornece informações de diagnóstico, se houver uma falha.   
- Gerenciamento do ciclo de vida de aplicativos. Além de fornecer atualizações sem tempo de inatividade, serviço tecidos fornece reversão automática para a versão anterior, se houver um evento de integridade bad relatado durante uma atualização.    
- Densidade. Você pode executar vários aplicativos em um cluster, o que elimina a necessidade para cada aplicativo seja executado em seu próprio hardware.


## <a name="overview-of-application-and-service-manifest-files"></a>Visão geral do aplicativo e arquivos de manifesto de serviço

Como parte da implantação do convidado executável, é útil entender o pacote de serviço tecidos e modelo de implantação como descrito o [modelo de aplicativo](service-fabric-application-model.md). O modelo de empacotamento do serviço tecidos depende de dois arquivos XML: os manifestos de aplicativo e serviços. A definição de esquema para os arquivos ApplicationManifest.xml e ServiceManifest.xml é instalada com o SDK do serviço tecidos em *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifesto do aplicativo** Manifesto do aplicativo é usado para descrever o aplicativo. Ele lista os serviços que compõem a ele e outros parâmetros que são usados para definir como os um ou mais serviços devem ser implantados, como o número de instâncias.

  A estrutura de serviço um aplicativo é uma unidade de implantação e atualização. Um aplicativo pode ser atualizado como uma unidade única onde possíveis falhas e possíveis reversões são gerenciadas. Serviço tecidos garante que o processo de atualização é bem sucedida, ou, se a atualização falhar, não deixe o aplicativo em um estado desconhecido/instável.

* **Manifesto de serviço** O manifesto de serviço descreve os componentes de um serviço. Ele inclui dados, como o nome e tipo de serviço e seu código, configuração e dados. O manifesto de serviço também inclui alguns parâmetros adicionais que podem ser usados para configurar o serviço quando ele é implantado.


## <a name="application-package-file-structure"></a>Estrutura de arquivo de pacote de aplicativo
Para implantar um aplicativo para tecidos de serviço, o aplicativo precisa acompanhar uma estrutura de diretório predefinido. O exemplo a seguir dessa estrutura.

```
|-- ApplicationPackageRoot
  	|-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```

O ApplicationPackageRoot contém o arquivo de ApplicationManifest.xml que define o aplicativo. Uma subpasta para cada serviço incluído no aplicativo é usada para conter todos os artefatos que requer o serviço – o ServiceManifest.xml e normalmente, os seguintes três diretórios:

- *Código*. Este diretório contém o código do serviço.
- *Config*. Este diretório contém um arquivo de Settings. XML (e outros arquivos, se necessário) que o serviço pode acessar em tempo de execução para recuperar configurações específicas.
- *Dados*. Este é um diretório adicional para armazenar dados locais adicionais que o serviço pode precisar. Observação: Dados devem ser usados para armazenar apenas dados efêmeros. Serviço tecidos não copiar/replicar as alterações no diretório de dados se o serviço precisa ser realocação – por exemplo, durante o failover.

Observação: Não é necessário criar o `config` e `data` diretórios se você não precisar deles.

## <a name="packaging-an-existing-executable"></a>Empacotando um executável existente

Quando um executável convidado de embalagem, você pode escolher para usar um modelo de projeto do Visual Studio ou [criar o pacote de aplicativo manualmente](#manually). Usando o Visual Studio, a estrutura do pacote de aplicativo e arquivos de manifesto são criados pelo Assistente para novo projeto para você.

>[AZURE.NOTE] A maneira mais fácil para empacotar um executável em um serviço existente do Windows é usar o Visual Studio.

## <a name="using-visual-studio-to-package-an-existing-executable"></a>Usando o Visual Studio para empacotar um executável existente

Visual Studio fornece um modelo de serviço do serviço tecidos para ajudá-lo a implantar o convidado executável em um cluster de estrutura de serviço.

Percorra as etapas a seguir para concluir a publicação:

1. Escolha Arquivo -> Novo projeto e criar um aplicativo de serviço de tecidos.
2. Escolha convidado executável como o modelo de serviço.
3. Clique em Procurar para selecionar a pasta com o executável e preencha o restante dos parâmetros para criar o serviço.
    - *Comportamento de pacote de código* podem ser definidas para copiar todo o conteúdo da pasta para o projeto do Visual Studio, que é útil se o executável não é alterado. Se você espera o executável para alterar e deseja que a capacidade de buscar novas compilações dinamicamente, você pode optar por vincular à pasta em vez disso. Observe que você pode usar pastas vinculadas ao criar o projeto de aplicativo no Visual Studio. Esse links para o local de origem de dentro do projeto, tornando possível para que você atualize o convidado executável no seu destino de origem, com as atualizações tornam-se parte do pacote de aplicativo na compilação.
    - *Programa* - escolha o executável que deve ser executado para iniciar o serviço.
    - *Argumentos* - especificar os argumentos que devem ser passados para o executável. Pode ser uma lista de parâmetros com argumentos.
    - *Pasta de trabalho* - Especifica a pasta de trabalho para o processo que vai ser iniciado. Você pode especificar três valores:
        - `CodeBase`Especifica que o diretório de trabalho vai ser definidas para o diretório de código no pacote do aplicativo (`Code` diretório na estrutura de arquivo mostrado anterior).
        - `CodePackage`Especifica que o diretório de trabalho vai ser definidas na raiz do pacote de aplicativo (`GuestService1Pkg` na estrutura de arquivo mostrado anterior).
        - `Work`Especifica que os arquivos são colocados em uma subpasta chamada trabalho
4. Dê um nome para seu serviço e clique em Okey.
5. Se seu serviço precisa de um ponto de extremidade para comunicação, agora você pode adicionar o protocolo, porta e tipo para o arquivo de ServiceManifest.xml. e.g. `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Agora você pode usar o pacote e publicar ação contra seu cluster local por depurar a solução no Visual Studio. Quando estiver pronto pode publicar o aplicativo em um cluster remoto ou seleção-na solução ao controle de origem.
7. Vá até o final deste artigo para ver como exibir o serviço executável convidado executado no serviço tecidos Explorer.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-an-existing-executable"></a>Empacotando manualmente e implantar um executável existente
O processo de Empacotando manualmente um executável convidado baseia-se nas seguintes etapas:

1. Crie a estrutura de diretório do pacote.
2. Adicione arquivos de código e configuração do aplicativo.
3. Edite o arquivo de manifesto de serviço.
4. Edite o arquivo de manifesto do aplicativo.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Criar a estrutura de diretório do pacote
Você pode começar pela criação da estrutura de diretório, conforme descrito anteriormente.

### <a name="add-the-applications-code-and-configuration-files"></a>Adicionar arquivos de código e configuração do aplicativo
Após ter criado a estrutura de diretório, você pode adicionar o código do aplicativo e arquivos de configuração em diretórios de código e config. Você também pode criar diretórios adicionais ou subdiretórios sob os diretórios de código ou config.

Serviço tecidos faz um xcopy do conteúdo do diretório raiz do aplicativo, portanto, não há nenhuma estrutura predefinida usar diferente de criação de dois diretórios superiores, código e configurações. (Você pode escolher nomes diferentes, se desejar. Mais detalhes estão na próxima seção.)

>[AZURE.NOTE] Certifique-se de que você inclua todas as arquivos/dependências que o aplicativo precisa. Serviço tecidos copia o conteúdo do pacote de aplicativo em todos os nós do cluster onde os serviços do aplicativo estão indo para ser implantado. O pacote deve conter todo o código que o aplicativo precisa executar. Não recomendamos presumindo que as dependências já estão instaladas.

### <a name="edit-the-service-manifest-file"></a>Editar o arquivo de manifesto de serviço
A próxima etapa é editar o arquivo de manifesto de serviço para incluir as seguintes informações:

- O nome do tipo de serviço. Esta é uma identificação que serviço tecidos usa para identificar um serviço.
- O comando usar para iniciar o aplicativo (ExeHost).
- Qualquer script que precisa ser executado para definir up/configurar o aplicativo (SetupEntrypoint).

A seguir é um exemplo de um `ServiceManifest.xml` arquivo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Vamos falar sobre as diferentes partes do arquivo que você precisa atualizar:

#### <a name="updating-the-servicetypes"></a>Atualizando o ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- Você pode escolher qualquer nome que você deseja para `ServiceTypeName`. O valor é usado na `ApplicationManifest.xml` arquivo para identificar o serviço.
- Você precisa especificar `UseImplicitHost="true"`. Esse atributo instrui tecidos de serviço que o serviço se baseia em um aplicativo autônomo, portanto, todas as necessidades de serviço tecidos fazer é para iniciá-lo como um processo e monitorar sua integridade.

#### <a name="updating-the-codepackage"></a>Atualizando o CodePackage
O elemento CodePackage Especifica o local (e versão) do código do serviço.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

O `Name` elemento é usado para especificar o nome do diretório no pacote do aplicativo que contém o código do serviço. `CodePackage`também tem o `version` atributo. Isso pode ser usado para especificar a versão do código – e também potencialmente pode ser usado para atualizar o código do serviço, usando a infraestrutura de gerenciamento de ciclo de vida de aplicativo de serviço estrutura.
#### <a name="optional-updating-the-setupentrypoint"></a>Opcional: Atualizando o SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
O elemento SetupEntrypoint é usado para especificar qualquer arquivo executável ou em lotes que deve ser executado antes de código do serviço é iniciado. É uma etapa opcional, para que ele não precisa ser incluído se há uma inicialização/configuração obrigatória. O SetupEntryPoint é executada toda vez que o serviço for reiniciado.

Há apenas uma SetupEntrypoint, para que os scripts de instalação/configuração precisam ser agrupados em um arquivo único lote se a configuração/config do aplicativo requer vários scripts. O SetupEntrypoint pode executar qualquer tipo de arquivo--arquivos executáveis, arquivos em lotes e cmdlets do PowerShell. Para obter mais detalhes, consulte [Configurar SetupEntryPoint](service-fabric-application-runas-security.md).

No exemplo anterior, o SetupEntrypoint executa um arquivo em lote chamado `LaunchConfig.cmd` que está localizada no `scripts` subpasta da pasta de código (presumindo que o elemento de pasta de trabalho está definido como base de código).

#### <a name="updating-the-entrypoint"></a>Atualizando o ponto de entrada

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

O `Entrypoint` elemento no arquivo de manifesto de serviço é usado para especificar como iniciar o serviço. O `ExeHost` elemento Especifica o executável (e argumentos) que deve ser usado para iniciar o serviço.

- `Program`Especifica o nome do executável que deve ser executado para iniciar o serviço.
- `Arguments`Especifica os argumentos que devem ser passados para o executável. Pode ser uma lista de parâmetros com argumentos.
- `WorkingFolder`Especifica a pasta de trabalho para o processo que vai ser iniciado. Você pode especificar três valores:
    - `CodeBase`Especifica que o diretório de trabalho vai ser definidas para o diretório de código no pacote do aplicativo (`Code` diretório na estrutura de arquivo anterior).
    - `CodePackage`Especifica que o diretório de trabalho vai ser definidas na raiz do pacote de aplicativo (`GuestService1Pkg` na estrutura de arquivo anterior).
  - `Work`Especifica que os arquivos são colocados em uma subpasta chamada trabalho

A pasta de trabalho é útil para definir a pasta de trabalho correto para que os caminhos relativos podem ser usados por scripts do aplicativo ou inicialização.

#### <a name="updating-the-endpoints-and-registering-with-naming-service-for-communication"></a>Atualizando os pontos de extremidade e registrar com o serviço de nomenclatura para comunicação

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
No exemplo anterior a `Endpoint` elemento Especifica os pontos de extremidade que o aplicativo pode ouvir no. Neste exemplo, o aplicativo Node ouve http na porta 3000.

Além disso, você pode pedir tecidos de serviço para publicar neste ponto de extremidade para o serviço de nomenclatura para que outros serviços podem descobrir o endereço do ponto de extremidade para esse serviço. Isso permite que você seja capaz de comunicar-se entre os serviços que são executáveis de convidado.
O endereço do ponto de extremidade publicados é do formulário `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme`e `PathSuffix` são atributos opcionais. `IPAddressOrFQDN`é o endereço IP ou o nome de domínio totalmente qualificado do nó este executável é colocado na e é calculado para você.

No exemplo a seguir quando o serviço é implantado, no serviço tecidos Explorer você vir um ponto de extremidade semelhante ao `http://10.1.4.92:3000/myapp/` publicado para a instância do serviço ou se este for um computador local que você veja `http://localhost:3000/myapp/`. 

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Você pode usar esses endereços com o [proxy reverso](service-fabric-reverseproxy.md) para comunicação entre os serviços.

### <a name="edit-the-application-manifest-file"></a>Editar o arquivo de manifesto do aplicativo

Depois de ter configurado o `Servicemanifest.xml` arquivo, você precisa fazer algumas alterações para a `ApplicationManifest.xml` arquivo para garantir que o tipo correto de serviço e o nome são usadas.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

No `ServiceManifestImport` elemento, você pode especificar um ou mais serviços que você deseja incluir no aplicativo. Serviços são referenciados com `ServiceManifestName`, que especifica o nome da pasta onde o `ServiceManifest.xml` arquivo está localizado.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Configurar o registro em log
Para convidado executáveis, é útil poder ver logs do console para descobrir se os scripts de configuração de aplicativo e mostram todos os erros.
Redirecionamento de console pode ser configurado na `ServiceManifest.xml` arquivo usando o `ConsoleRedirection` elemento.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection`pode ser usado para redirecionar a saída de console (stdout e stderr) para uma pasta de trabalho para que possam ser usadas para verificar que não existem erros durante a instalação ou execução do aplicativo no cluster tecidos de serviço.

    * `FileRetentionCount`Determina quantos arquivos são salvos no diretório de trabalho. Um valor de 5, por exemplo, significa que os arquivos de log para cinco execuções anterior são armazenados no diretório de trabalho.
    * `FileMaxSizeInKb`Especifica o tamanho máximo dos arquivos de log.

Arquivos de log são salvos em uma das pastas de trabalho do serviço. Para determinar onde os arquivos estão localizados, você precisa usar o serviço tecidos Explorer para determinar qual nó que o serviço está em execução e qual pasta de trabalho está sendo usada. Esse processo é coberto neste artigo.

## <a name="deployment"></a>Implantação
A última etapa é implantar seu aplicativo. O seguinte script do PowerShell mostra como implantar seu aplicativo ao cluster local de desenvolvimento e iniciar um novo serviço de estrutura de serviço.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Um serviço de estrutura de serviço pode ser implantado em várias "configurações". Por exemplo, ele pode ser implantado como uma ou várias instâncias ou pode ser implantado de forma que não há uma instância do serviço em cada nó do cluster tecidos de serviço.

O `InstanceCount` parâmetro da `New-ServiceFabricService` cmdlet é usado para especificar quantas instâncias do serviço devem ser iniciadas no cluster tecidos de serviço. Você pode definir o `InstanceCount` valor, dependendo do tipo de aplicativo que você está implantando. Os dois cenários mais comuns são:

* `InstanceCount = "1"`. Nesse caso, apenas uma instância do serviço é implantada em cluster. Agendador do serviço tecidos determina qual nó o serviço vai ser implantado em.

* `InstanceCount ="-1"`. Nesse caso, uma instância do serviço é implantada em cada nó no cluster tecidos de serviço. O resultado está tendo um (e apenas um) instância do serviço para cada nó no cluster.

Esta é uma configuração útil para aplicativos de front-end (por exemplo, um ponto de extremidade REST) porque aplicativos cliente precisam "conectar" a qualquer um de nós no cluster para usar o ponto de extremidade. Esta configuração também pode ser usada quando, por exemplo, todos os nós do cluster tecidos de serviço estão conectados a um balanceador de carga para que o tráfego do cliente pode ser distribuído entre o serviço que está sendo executado em todos os nós no cluster.

## <a name="check-your-running-application"></a>Verificar seu aplicativo em execução

No Explorador de tecidos do serviço, identifica o nó onde o serviço está sendo executado. Neste exemplo, ele é executado no Node1:

![Nó onde o serviço está em execução](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Se você navegue até o nó e navegue até o aplicativo, você verá as informações de nó essenciais, incluindo sua localização no disco.

![Local do disco](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Se você procurar ao diretório usando Server Explorer, você pode encontrar a pasta de trabalho e a pasta de log do serviço como mostra a imagem a seguir.

![Local do log de](./media/service-fabric-deploy-existing-app/loglocation.png)


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como empacotar um executável de convidado e implantá-lo em estrutura de serviço. Como próxima etapa, você pode verificar conteúdo adicional para este tópico.

- [Exemplo de empacotamento e implantação convidado executável no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication), incluindo um link para o pré-lançamento da ferramenta embalagem
- [Implantar vários executáveis de convidado](service-fabric-deploy-multiple-apps.md)
- [Criar seu primeiro aplicativo de serviço tecidos usando o Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
