<properties
   pageTitle="Especificando pontos de extremidade do serviço tecidos service | Microsoft Azure"
   description="Como descrever os recursos de ponto de extremidade em um manifesto de serviço, incluindo como configurar pontos de extremidade HTTPS"
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

# <a name="specify-resources-in-a-service-manifest"></a>Especificar recursos em um manifesto de serviço

## <a name="overview"></a>Visão geral

O manifesto de serviço permite que os recursos que são usados pelo serviço para ser declarados/alterada sem alterar o código compilado. Azure tecidos de serviço oferece suporte a configuração de recursos de ponto de extremidade do serviço. O acesso aos recursos que são especificadas no manifesto do serviço pode ser controlado por meio do SecurityGroup no manifesto do aplicativo. A declaração de recursos permite que esses recursos sejam alteradas no momento da implantação, significando que o serviço não precise apresentar um novo mecanismo de configuração. A definição de esquema para o arquivo ServiceManifest.xml é instalada com o serviço tecidos SDK e ferramentas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Pontos de extremidade

Quando um recurso de ponto de extremidade é definido no manifesto do serviço, o serviço tecidos atribui portas do intervalo de portas reservadas de aplicativo quando não for especificada explicitamente uma porta. Por exemplo, examine o ponto de extremidade *ServiceEndpoint1* especificado no trecho de manifesto fornecido após este parágrafo. Além disso, os serviços também podem solicitar uma porta específica em um recurso. Réplicas de serviço em execução em nós de cluster diferentes podem ser atribuídas números de porta diferentes, enquanto réplicas de um serviço em execução no mesmo nó compartilham a porta. As réplicas de serviço podem usar essas portas conforme necessário para replicação e escutar solicitações de clientes.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Consulte [Configurando estado serviços confiáveis](service-fabric-reliable-services-configuration.md) para ler mais sobre referenciando pontos de extremidade de nas configurações do pacote de configuração de arquivo (Settings. xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exemplo: especifica um ponto de extremidade HTTP para o seu serviço

O seguinte manifesto de serviço define um recurso de ponto de extremidade TCP e dois recursos de ponto de extremidade HTTP na &lt;recursos&gt; elemento.

Pontos de extremidade HTTP são automaticamente que ACL seria pelo serviço tecidos.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Exemplo: especifica um ponto de extremidade HTTPS para seu serviço

O protocolo HTTPS fornece autenticação de servidor e também é usado para criptografar a comunicação de cliente-servidor. Para habilitar HTTPS seu serviço de estrutura de serviço, especifique o protocolo na *recursos -> pontos de extremidade -> ponto de extremidade* seção o manifesto de serviço, conforme mostrado anteriormente para o ponto de extremidade *ServiceEndpoint3*.

>[AZURE.NOTE] Protocolo de um serviço não pode ser alterado durante a atualização do aplicativo sem ele que constitui uma quebra de alterar.


Aqui está um exemplo ApplicationManifest que você precisa definir para HTTPS. A impressão digital do certificado deve ser fornecida. O EndpointRef é uma referência a EndpointResource em ServiceManifest, para o qual você definir o protocolo HTTPS. Você pode adicionar mais de um EndpointCertificate.  

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```
