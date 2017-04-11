<properties
   pageTitle="Modelo de aplicativo de serviço tecidos | Microsoft Azure"
   description="Como modelo e descrever aplicativos e serviços em tecidos de serviço."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor="mani-ramaswamy"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"   
   ms.author="seanmck"/>

# <a name="model-an-application-in-service-fabric"></a>Modelar um aplicativo em estrutura de serviço

Este artigo fornece uma visão geral do modelo de aplicativo do Azure serviço tecidos. Ele também descreve como definir um aplicativo e serviço por meio de arquivos de manifesto e obter o aplicativo compactado e pronto para implantação.

## <a name="understand-the-application-model"></a>Entender o modelo de aplicativo

Um aplicativo é um conjunto de serviços de componentes que executam uma determinada função ou funções. Um serviço executa uma função concluída e autônomo (ele pode iniciar e executar independentemente dos outros serviços) e é composto de código, configuração e dados. Para cada serviço, código consiste nos binários executáveis, configuração consiste em configurações de serviço que podem ser carregadas em tempo de execução e dados consistem em aleatório dados estáticos para ser consumida pelo serviço. Cada componente neste modelo de aplicativo hierárquico pode ser atualizado e versão independentemente.

![O modelo de aplicativo de serviço tecidos][appmodel-diagram]


Um tipo de aplicativo é uma categorização de um aplicativo e consiste em um pacote de tipos de serviço. Um tipo de serviço é uma categorização de um serviço. A categorização pode ter diferentes configurações e configurações, mas a funcionalidade de núcleo permanece o mesmo. As instâncias de um serviço são as variações de configuração de serviço diferente do mesmo tipo de serviço.  

As classes (ou "tipos") de aplicativos e serviços são descritas através de arquivos XML (manifestos de aplicativo e manifestos de serviço) que são os modelos contra que aplicativos podem ser criados a partir de armazenamento de imagens do cluster. A definição de esquema para o arquivo ServiceManifest.xml e ApplicationManifest.xml é instalada com o serviço tecidos SDK e ferramentas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

O código para instâncias de aplicativo diferente será executado como processos separados mesmo quando hospedado pelo mesmo nó tecidos de serviço. Além disso, o ciclo de vida de cada instância do aplicativo pode ser gerenciado (isto é atualizado) independentemente. O diagrama a seguir mostra como os tipos de aplicativos são compostos dos tipos de serviço, que por sua vez, são compostos de código, configuração e pacotes. Para simplificar o diagrama, somente o código/configuração/dados pacotes de `ServiceType4` são mostradas, apesar de cada tipo de serviço incluiria alguns ou todos os tipos de embalagens.

![Tipos de aplicativo de serviço tecidos e serviço][cluster-imagestore-apptypes]

Dois arquivos de manifesto diferentes são usados para descrever aplicativos e serviços: o manifesto de serviço e manifesto do aplicativo. Estes são abordados detalhadamente nas seções exibidas.

Pode haver uma ou mais ocorrências de um tipo de serviço ativo no cluster. Por exemplo, instâncias de serviço com estado ou réplicas alcançar alta confiabilidade por Duplicar estado entre réplicas localizadas em nós diferentes no cluster. Essa replicação essencialmente fornece redundância no serviço estar disponível mesmo que a falha de um nó em um cluster. Um [serviço particionado](service-fabric-concepts-partitioning.md) ainda mais divide seu estado (e padrões de acesso a esse estado) em nós do cluster.

O diagrama a seguir mostra a relação entre aplicativos e instâncias de serviço, partições e réplicas.

![Partições e réplicas dentro de um serviço][cluster-application-instances]


>[AZURE.TIP] Você pode exibir o layout de aplicativos em um cluster usando a ferramenta de serviço tecidos Explorer disponível em http://&lt;yourclusteraddress&gt;: 19080/Explorer. Para obter mais detalhes, consulte [visualizando seu cluster com o serviço tecidos Explorer](service-fabric-visualizing-your-cluster.md).

## <a name="describe-a-service"></a>Descrever um serviço

O manifesto de serviço maneira declarativa define o tipo de serviço e versão. Especifica os metadados de serviço como o tipo de serviço, propriedades de integridade, métricas de balanceamento de carga, binários do serviço e arquivos de configuração.  Colocar outra maneira, ele descreve os pacotes de código, configuração e dados que compõem um pacote de serviço para oferecer suporte a um ou mais tipos de serviço. Aqui está um manifesto de serviço de exemplo simples:

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
~~~

Os atributos de **versão** são cadeias de caracteres não estruturadas e não analisado pelo sistema. Estas são usadas para versão cada componente para atualizações.

**ServiceTypes** declara que tipos de serviço são suportados pelo **CodePackages** neste manifesto. Quando um serviço é instanciado em relação a um desses tipos de serviço, todos os pacotes de código declarados neste manifesto estão ativados executando seus pontos de entrada. Os processos resultantes devem registrar os tipos de serviço com suporte em tempo de execução. Observe que os tipos de serviço são declarados no nível do manifesto e não no nível de pacote de código. Portanto quando há vários pacotes de código, eles são todos ativados sempre que o sistema procura por qualquer um dos tipos de serviço declarados.

**SetupEntryPoint** é um ponto de entrada com privilégios que é executado com as mesmas credenciais tecidos de serviço (normalmente a conta do *sistema local* ) antes de qualquer outro ponto de entrada. O executável especificado pelo **ponto de entrada** geralmente é o host de serviço de execução longa. A presença de um ponto de entrada de instalação separados evita que executar o host de serviço com privilégios de altos por longos períodos de tempo. O executável especificado pelo **ponto de entrada** é executado após **SetupEntryPoint** sai com êxito. O processo resultante é monitoramento e reiniciado (começando novamente com **SetupEntryPoint**) se ele nunca termina ou falha.

**DataPackage** declara uma pasta, chamada pelo **nome do** atributo, que contém dados estáticos aleatório para ser consumida pelo processo de tempo de execução.

**ConfigPackage** declara uma pasta, chamada pelo **nome do** atributo, que contém um arquivo de *Settings. XML* . Este arquivo contém seções de configurações de par chave-valor, definidos pelo usuário que o processo pode ler novamente em tempo de execução. Durante uma atualização, se apenas a **ConfigPackage** **versão** foi alterada, em seguida, o processo em execução não é reiniciado. Em vez disso, um retorno de chamada notifica o processo que configurações foram alterados para que eles possam ser recarregados dinamicamente. Aqui está um exemplo de arquivo de *Settings. XML* :

~~~
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
~~~

> [AZURE.NOTE] Um manifesto de serviço pode conter várias código, configuração e pacotes de dados. Cada um deles pode ser versão independentemente.

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>Descrever um aplicativo


Manifesto do aplicativo maneira declarativa descreve o tipo de aplicativo e versão. Especifica os metadados de composição de serviço como nomes estáveis, partição esquema, fator de contagem/replicação de instância, política de segurança/isolamento, restrições de posicionamento, substituições de configuração e tipos de componentes de serviço. Os domínios de balanceamento de carga no qual o aplicativo é colocado também são descritos.

Portanto, um manifesto de aplicativo descreve elementos no nível do aplicativo e faz referência a um ou mais manifestos de serviço para compor um tipo de aplicativo. Aqui está um manifesto de aplicativo de exemplo simples:

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
~~~

Como o serviço manifestos, os atributos de **versão** são cadeias de caracteres não estruturadas e não são analisados pelo sistema. Estas são também usado para versão cada componente para atualizações.

**ServiceManifestImport** contém referências a manifestos de serviço que compõem este tipo de aplicativo. Manifestos de serviço importados determinam quais tipos de serviço são válidos dentro desse tipo de aplicativo.

**DefaultServices** declara instâncias de serviço que são criadas automaticamente sempre que um aplicativo é criada uma instância contra esse tipo de aplicativo. Serviços padrão são apenas uma conveniência e se comportam como serviços normais em todos os aspectos depois que eles foram criados. Eles são atualizados junto com outros serviços na instância do aplicativo e podem ser removidos também.

> [AZURE.NOTE] Um manifesto de aplicativo pode conter várias importações manifesto de serviço e serviços padrão. Cada importação de manifesto de serviço pode ser versão independentemente.

Para saber como manter o aplicativo diferente e parâmetros de serviço para ambientes individuais, consulte [Gerenciando parâmetros de aplicativo para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->

## <a name="package-an-application"></a>Empacotar um aplicativo

### <a name="package-layout"></a>Layout do pacote

Manifesto do aplicativo, manifest(s) de serviço e outros arquivos de pacote necessárias devem ser organizados em um layout específico para a implantação em um cluster de estrutura de serviço. Os manifestos de exemplo neste artigo precisaria ser organizados na estrutura de diretório a seguir:

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
~~~

As pastas são nomeadas para coincidir com os atributos de **nome** de cada elemento correspondente. Por exemplo, se o manifesto de serviço contido dois pacotes de código com os nomes **MyCodeA** e **MyCodeB**, em seguida, duas pastas com os mesmos nomes seriam contêm os binários necessários para cada pacote de código.

### <a name="use-setupentrypoint"></a>Use SetupEntryPoint

Cenários comuns para uso **SetupEntryPoint** são quando necessário para executar um arquivo executável antes do serviço é iniciado ou precisa executar uma operação com privilégios elevados. Por exemplo:

- Configurar e inicializar variáveis de ambiente que precisa ser o executável do serviço. Isso não está limitado aos somente os executáveis gravados via os modelos de programação de serviço tecidos. Por exemplo, npm.exe precisa algumas variáveis de ambiente configurados para implantar um aplicativo Node.

- Configurando o controle de acesso pela instalação de certificados de segurança.

### <a name="build-a-package-by-using-visual-studio"></a>Criar um pacote usando o Visual Studio

Se você usar 2015 do Visual Studio para criar seu aplicativo, você pode usar o comando pacote para criar automaticamente um pacote que corresponda o layout descrito acima.

Para criar um pacote, clique com botão direito do projeto de aplicativo no Solution Explorer e escolha o comando pacote, conforme mostrado abaixo:

![Empacotando um aplicativo com o Visual Studio][vs-package-command]

Quando embalagem for concluída, você encontrará o local do pacote na janela de **saída** . Observe que a etapa de embalagem ocorre automaticamente quando você implantar ou depura seu aplicativo no Visual Studio.

### <a name="test-the-package"></a>Testar o pacote

Você pode verificar a estrutura do pacote localmente através do PowerShell, usando o comando **ServiceFabricApplicationPackage de teste** . Este comando verificar se há problemas de análise de manifesto e verifique se todas as referências. Observe que este comando somente verifica a correção estrutural das pastas e arquivos no pacote. Ele não verificará qualquer do conteúdo do pacote código ou dados além da verificação de que todos os arquivos necessários estão presentes.

~~~
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
~~~

Este erro mostra que o arquivo de *MySetup.bat* referenciado no manifesto do serviço **SetupEntryPoint** está ausente no pacote código. Após adicionar o arquivo ausente, passa a verificação de aplicativo:

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
~~~

Depois que o aplicativo é empacotado corretamente e passa verificação, está pronto para implantação.

## <a name="next-steps"></a>Próximas etapas

[Implantar e remover aplicativos][10]

[Gerenciando parâmetros de aplicativo para vários ambientes][11]

[RunAs: Executando um aplicativo de serviço tecidos com permissões de segurança diferente][12]

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
[vs-package-command]: ./media/service-fabric-application-model/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
