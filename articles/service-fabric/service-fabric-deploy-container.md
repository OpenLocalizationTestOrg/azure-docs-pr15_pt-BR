<properties
   pageTitle="Serviço tecidos e contêineres Implantando | Microsoft Azure"
   description="Serviço tecidos e o uso de contêineres para implantar aplicativos de microservice. Este artigo descreve os recursos que o serviço tecidos fornece para contêineres e como implantar uma imagem de contêiner do Windows em um cluster"
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
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="preview-deploy-a-windows-container-to-service-fabric"></a>Visualização: Implantar um contêiner de Windows em tecidos de serviço

> [AZURE.SELECTOR]
- [Implantar o contêiner do Windows](service-fabric-deploy-container.md)
- [Implantar o contêiner de Docker](service-fabric-deploy-container-linux.md)

Este artigo explica a criação de serviços contidos em contêineres do Windows. 

>[AZURE.NOTE] Este recurso está no modo de visualização para Linux e não está atualmente disponível para uso com o Windows Server 2016. Isso estará disponível no preview para Windows Server 2016 na próxima versão da estrutura de serviço e suportados na versão subsequente depois disso.

Serviço tecidos tem vários recursos de contêiner que ajudarão-lo com a criação de aplicativos que são compostos de microservices que estão em contêiner. Estes são chamados contida em recipiente serviços. 

Os recursos incluem;

- Ativação e implantação de imagem de contêiner
- Gestão de recursos
- Autenticação de repositório
- Porta de contêiner para mapeamento de porta de host
- Comunicação e descoberta de recipiente para outro
- Capacidade de configurar e definir variáveis de ambiente

Vamos observar cada um dos recursos alternadamente quando um serviço contida em recipiente a ser incluída no seu aplicativo de embalagem.

## <a name="packaging-a-windows-container"></a>Empacotando um contêiner do Windows

Quando um contêiner de embalagem, você pode escolher para usar um modelo de projeto do Visual Studio ou [criar o pacote de aplicativo manualmente](#manually). Usando o Visual Studio, a estrutura do pacote de aplicativo e arquivos de manifesto são criados pelo Assistente para novo projeto para você (isso está chegando na próxima versão).

## <a name="using-visual-studio-to-package-an-existing-container-image"></a>Usando o Visual Studio para compactar uma imagem existente do contêiner

>[AZURE.NOTE] Em uma versão futura do Visual Studio ferramentas SDK, você poderá adicionar um contêiner para um aplicativo de maneira semelhante ao que você pode adicionar um convidado executável hoje. Consulte o tópico de [implantar um convidado executável à estrutura de serviço](service-fabric-deploy-existing-app.md) . Atualmente, você precisa fazer empacotamento manual conforme descrito abaixo.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-a-container"></a>Empacotando manualmente e implantando um contêiner
O processo de Empacotando manualmente um serviço contida em recipiente baseia-se nas seguintes etapas:

1. Publicado os contêineres para seu repositório.
2. Crie a estrutura de diretório do pacote.
3. Edite o arquivo de manifesto de serviço.
4. Edite o arquivo de manifesto do aplicativo.

## <a name="container-image-deployment-and-activation"></a>Implantação de imagem do contêiner e ativação.
No serviço tecidos [modelo de aplicativo](service-fabric-application-model.md), um contêiner representa um aplicativo host na qual várias réplicas de serviço são colocadas. Para implantar e ativar um contêiner, coloque o nome da imagem contêiner em um `ContainerHost` elemento no manifesto do serviço.

No manifesto do serviço adicionar um `ContainerHost` para a entrada aponte e definir o `ImageName` para o nome do repositório de contêiner e imagem. O manifesto parcial a seguir mostra um exemplo de Implantando o contêiner chamado *myimage:v1* de um repositório chamado *myrepo*

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

Você pode fornecer comandos de entrada para a imagem de contêiner, especificando o opcional `Commands` elemento com uma vírgula delimitado por conjunto de comandos executados dentro do contêiner. 

## <a name="resource-governance"></a>Gestão de recursos
Gestão de recursos é um recurso do contêiner e restringe os recursos que o contêiner pode usar no host. O `ResourceGovernancePolicy`, especificado no manifesto do aplicativo, que fornece a capacidade de declarar limites de recursos para um pacote de código de serviço. Limites de recursos podem ser definidos para;

- Memória
- MemorySwap
- CpuShares (peso relativo da CPU)
- MemoryReservationInMB  
- BlkioWeight (BlockIO importância relativa). 

>[AZURE.NOTE] Em uma versão futura, suporte para especificar limites de IO bloco específico como IOPs, BPS e outros serão possível de leitura/gravação.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## <a name="repository-authentication"></a>Autenticação de repositório
Para baixar um contêiner, você terá que fornecer credenciais de login no repositório de contêiner. As credenciais de login, especificadas no manifesto do *aplicativo* são usadas para especificar as informações de logon, ou chave SSH, para baixar a imagem de contêiner de repositório de imagens.  O exemplo a seguir mostra uma conta chamada *TestUser* juntamente com a senha em texto não criptografado. Isso **não** é recomendado.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

A senha pode e deve ser criptografada usando um certificado implantado na máquina.

O exemplo a seguir mostra uma conta chamada *TestUser* com a senha criptografada por um certificado denominado *MyCert*. Você pode usar o `Invoke-ServiceFabricEncryptText` comando do Powershell para criar o texto de codificação secreta para a senha. Consulte este artigo [Gerenciando segredos em aplicativos de serviço tecidos](service-fabric-application-secret-management.md) para obter detalhes sobre como. A chave privada do certificado para descriptografar a senha deve ser implantada na máquina local em um método de fora da faixa (no Azure é via ARM). Em seguida, quando serviço tecidos implanta o pacote de serviço para o computador, é capaz de descriptografar o segredo e juntamente com o nome da conta, autenticar com o repositório de contêiner usando estas credenciais.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

## <a name="container-port-to-host-port-mapping"></a>Porta de contêiner para mapeamento de porta de host
Você pode configurar uma porta de host usada para se comunicar com o contêiner especificando uma `PortBinding` no manifesto do aplicativo. A vinculação de porta mapeia a porta que o serviço é listening em dentro do contêiner para uma porta do host.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## <a name="container-to-container-discovery-and-communication"></a>Comunicação e descoberta de recipiente para outro
Usando o `PortBinding` política é possível mapear uma porta de contêiner para um `Endpoint` no manifesto do serviço, conforme mostrado no exemplo a seguir. O ponto de extremidade `Endpoint1` pode especificar uma porta fixa, por exemplo, a porta 80 ou não especificar nenhuma porta, nesse caso uma porta aleatória do intervalo de portas de aplicativo dos clusters está selecionada.

Para contêineres de convidado, especificando uma `Endpoint` como isso no manifesto do serviço permite tecidos de serviço publicar automaticamente esse ponto de extremidade para o serviço de nomenclatura para que outros serviços em execução no cluster possam detectar este contêiner usando as consultas de restante de serviço de resolver. 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

Registrando-se com o serviço de nomenclatura, você poderá facilmente fazer comunicação de recipiente para outro no código dentro de seu contêiner usando o [proxy reverso](service-fabric-reverseproxy.md). Tudo o que você precisa fazer é fornecer a porta ouvinte do proxy reverso http e o nome dos serviços que você deseja se comunicar com configurá-los como variáveis de ambiente. Consulte a próxima seção sobre como fazer isso.  

## <a name="configure-and-set-environment-variables"></a>Configurar e definir variáveis de ambiente
Variáveis de ambiente podem ser um inimigo especificado cada pacote de código no serviço manifesto para ambos os serviços implantados em contêineres ou como executáveis de processos/convidado. Estes valores de variável de ambiente podem ser substituídas especificamente no manifesto do aplicativo ou especificadas durante a implantação como parâmetros de aplicativo.

Manifesto do serviço seguinte trecho XML mostra um exemplo de como especificar variáveis de ambiente de um pacote de código. 

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>

Essas variáveis de ambiente podem ser substituídos no nível de manifesto do aplicativo:

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

No exemplo acima, podemos tiver especificado um valor explícito para a `HttpGateway` variável de ambiente (19000) enquanto o valor para `BackendServiceName` parâmetro é definido pelo `[BackendSvc]` parâmetro do aplicativo. Isso permite que você especifique o valor para `BackendServiceName`valor no momento de implantar o aplicativo e não tem um valor fixo no manifesto. 

## <a name="complete-examples-for-application-and-service-manifest"></a>Concluir exemplos de aplicativo e manifesto de serviço
A seguir é um manifesto de aplicativo de exemplo que mostra os recursos do contêiner.


    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>


A seguir está um exemplo manifesto de serviço (especificado no manifesto do aplicativo anterior) que mostra os recursos de contêiner

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>
