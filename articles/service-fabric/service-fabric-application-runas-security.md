<properties
   pageTitle="Noções básicas sobre o aplicativo de serviço tecidos e políticas de segurança do serviço | Microsoft Azure"
   description="Uma visão geral de como executar um aplicativo de serviço tecidos em sistema e contas de segurança local, incluindo o ponto de SetupEntry onde um aplicativo precisa executar alguma ação privilegiada antes de iniciar"
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
   ms.date="09/22/2016"
   ms.author="mfussell"/>

# <a name="configure-security-policies-for-your-application"></a>Configurar políticas de segurança do aplicativo
Azure tecidos de serviço fornece a capacidade de proteger aplicativos executados no cluster em contas de usuário diferentes. Serviço tecidos também protege os recursos usados pelos aplicativos no momento da implantação na conta de usuário, como arquivos, pastas e certificados. Isso tornará aplicativos em execução, mesmo em um ambiente hospedado compartilhado, seguro uns dos outros. 

Por padrão, os aplicativos de serviço tecidos executados sob a conta que executa o processo de Fabric.exe em. Estrutura de serviço também fornece a capacidade de executar aplicativos em uma conta de usuário local ou a conta do sistema local, especificado no manifesto do aplicativo. Tipos de conta do sistema local com suporte para são **LocalUser**, **serviço de rede**, **serviço local**e **sistema local**.

 Ao executar tecidos de serviço no Windows Server em seu centro de dados usando o instalador autônomo, você pode usar contas de domínio do Active Directory (AD).

Grupos de usuários podem ser definidos e criados para que um ou mais usuários podem ser adicionados a cada grupo a ser gerenciado juntos. Isso é útil quando há vários usuários para os pontos de entrada de serviço diferente e eles precisam ter certos privilégios comuns que estão disponíveis no nível de grupo.

## <a name="configure-the-policy-for-service-setupentrypoint"></a>Configurar a política de serviço SetupEntryPoint

Conforme descrito no [modelo de aplicativo](service-fabric-application-model.md) do **SetupEntryPoint** é um ponto de entrada com privilégios que é executado com as mesmas credenciais tecidos de serviço (normalmente a conta de *serviço de rede* ) antes de qualquer outro ponto de entrada. O executável especificado pelo **ponto de entrada** geralmente é host do serviço de execução demorada, para que ter uma entrada de instalação separados apontando evita que executar o host de serviço executável com privilégios de altos por longos períodos de tempo. O executável especificado pelo **ponto de entrada** é executado após **SetupEntryPoint** sai com êxito. O processo resultante é monitorado e reiniciado, começando novamente com **SetupEntryPoint**, se ele nunca termina ou falha.

A seguir é um exemplo de manifesto de serviço simples que mostra o SetupEntryPoint e o ponto de entrada principal para o serviço.

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### <a name="configure-the-policy-using-a-local-account"></a>Configurar a política usando uma conta local

Depois de configurar o serviço para ter uma entrada de configuração de ponto, você pode alterar as permissões de segurança que ele é executado no manifesto do aplicativo. O exemplo seguinte mostra como configurar o serviço para ser executado com privilégios de conta de administrador do usuário.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

Primeiro, crie uma seção de **objetos** com um nome de usuário, como SetupAdminUser. Isso indica que o usuário é um membro do grupo Administradores sistema.

Em seguida, na seção **ServiceManifestImport** , configure uma política para aplicar esta capital **SetupEntryPoint**. Isso informa ao serviço tecidos que, quando o arquivo **MySetup.bat** é executado, ele deve ser RunAs com privilégios de administrador. Considerando que você tem *não* aplicada uma política para o ponto de entrada principal, o código no **MyServiceHost.exe** compatível com o sistema de conta de **serviço de rede** . Esta é a conta padrão que todos os pontos de entrada do serviço são executados como.

Agora vamos adicionar o arquivo MySetup.bat ao projeto do Visual Studio para testar os privilégios de administrador. No Visual Studio, clique com botão direito no projeto serviço e adicione um novo arquivo chamado MySetup.bat.

Em seguida, certifique-se de que o arquivo de MySetup.bat está incluído no pacote de serviço. Por padrão, não é. Selecione o arquivo, clique com botão direito para acessar o menu de contexto e escolha **Propriedades**. Na caixa de diálogo Propriedades, certifique-se de que a opção **Copiar para diretório de saída** é definida como **Copiar se mais recente**. Consulte a tela a seguir captura.

![CopyToOutput Studio Visual para o arquivo em lotes de SetupEntryPoint][image1]

Agora, abra o arquivo MySetup.bat e adicione os seguintes comandos:

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

Em seguida, criar e implantar a solução em um cluster de desenvolvimento local.  Depois que o serviço foi iniciado, conforme mostrado no Explorador de tecidos do serviço, você pode ver que o MySetup.bat foi bem-sucedida de um duas maneiras. Abra um prompt de comando do PowerShell e digite:

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

Em seguida, observe o nome do nó onde o serviço foi implantado e iniciado no serviço tecidos Explorer, por exemplo, nó 2. Em seguida, navegue até a pasta de trabalho de instância do aplicativo para localizar o arquivo de out.txt que mostra o valor de **TestVariable**. Por exemplo, se esse serviço foi implantado no nó 2, em seguida, você pode ir para este caminho para o **MyApplicationType**:

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

###  <a name="configure-the-policy-using-local-system-accounts"></a>Configurar a política usando contas de sistema local
Muitas vezes é preferível para executar o script de inicialização usando uma conta do sistema local em vez de uma conta de administrador, conforme mostrado anterior. Executando o RunAs política como administradores geralmente não funciona bem como máquinas têm controle da acesso de usuário (UAC), ativado por padrão. Nesses casos, **a recomendação é para executar o SetupEntryPoint como sistema local em vez de um usuário local adicionado ao grupo Administradores**. O exemplo a seguir mostra a configuração do SetupEntryPoint seja executado como sistema local.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
~~~

##  <a name="launch-powershell-commands-from-a-setupentrypoint"></a>Iniciar comandos do PowerShell em uma SetupEntryPoint
Para executar o PowerShell do ponto de **SetupEntryPoint** , você pode executar **PowerShell.exe** em um arquivo de lote que aponta para um arquivo do PowerShell. Primeiro, adicione um arquivo do PowerShell para o projeto de serviço, como **MySetup.ps1**. Lembre-se de configurar a propriedade *Copiar se mais recente* para que o arquivo também está incluído no pacote de serviço. O exemplo a seguir mostra um arquivo em lotes de exemplo para iniciar um arquivo de PowerShell chamado MySetup.ps1, que define uma variável de ambiente de sistema chamada **TestVariable**.


MySetup.bat para iniciar o arquivo PowerShell.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

No arquivo do PowerShell, adicione o seguinte para definir uma variável de ambiente do sistema:

~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

**Observação:** Por padrão quando o arquivo em lotes é executado parece na pasta do aplicativo chamada **funcionam** para arquivos. Nesse caso, quando MySetup.bat é executado queremos isso para encontrar o MySetup.ps1 na mesma pasta, que é a pasta de **pacote de código** do aplicativo. Para alterar essa pasta, defina a pasta de trabalho, conforme mostrado a seguir.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
~~~

## <a name="using-console-redirection-for-local-debugging"></a>Usar o redirecionamento de console para depuração local
Ocasionalmente é útil ver a saída do console executem um script para fins de depuração. Para fazer isso, você pode definir uma política de redirecionamento de console, que grava a saída para um arquivo. A saída de arquivo é gravada para a pasta de aplicativo chamada **log** no nó onde o aplicativo é implantado e executado (consulte onde encontrar isso no exemplo anterior).

**Observação: nunca** usar a diretiva de redirecionamento de console em um aplicativo implantado em produção, pois isso pode afetar o failover do aplicativo. **Somente** use este comando para fins de depuração e desenvolvimento local.  

O exemplo a seguir mostra definindo o redirecionamento de console com um valor de FileRetentionCount.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
~~~

Se você alterar o arquivo de MySetup.ps1 para escrever um comando **eco** , isso gravará o arquivo de saída para fins de depuração.

~~~
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
~~~

**Depois que você tem depurado seu script, remova imediatamente esta política de redirecionamento de console**

## <a name="configure-policy-for-service-code-packages"></a>Configurar política para pacotes de código de serviço 
Nas etapas anteriores, você viu como aplicar política de RunAs a SetupEntryPoint. Vamos examinar um pouco mais sobre como criar diferentes objetos que podem ser aplicados como políticas de serviço.

### <a name="create-local-user-groups"></a>Criar grupos de usuários locais
Grupos de usuários podem ser definidos e criados para permitir que um ou mais usuários a serem adicionados a um grupo. Isso é especialmente útil se houver vários usuários para os pontos de entrada de serviço diferente e eles precisam ter certos privilégios comuns que estão disponíveis no nível de grupo. O exemplo a seguir mostra um grupo local chamado **LocalAdminGroup** com privilégios de administrador. Dois usuários, Customer1 e Customer2, são feitos membros deste grupo local.

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### <a name="create-local-users"></a>Criar usuários locais
Você pode criar um usuário local que pode ser usado para proteger um serviço dentro do aplicativo. Quando um tipo de conta **LocalUser** é especificado na seção de objetos do manifesto do aplicativo, o serviço tecidos cria contas de usuário locais em máquinas onde o aplicativo é implantado. Por padrão, essas contas não tem os mesmos nomes aquelas especificadas no manifesto do aplicativo (por exemplo, Customer3 no exemplo a seguir). Em vez disso, eles são gerados dinamicamente e tem senhas aleatórias.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

### <a name="assign-policies-to-the-service-code-packages"></a>Atribuir políticas para os pacotes de código de serviço
A seção **RunAsPolicy** para um **ServiceManifestImport** Especifica a conta da seção objetos que deve ser usada para executar um pacote de código. Ele também associa pacotes de código do serviço manifesto contas de usuário na seção de objetos. Você pode especificar isso para a configuração ou pontos de entrada principal, ou você pode especificar `All` para aplicá-lo a ambos. O exemplo a seguir mostra diferentes políticas sendo aplicadas:

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Se **EntryPointType** não for especificada, o padrão é definido como EntryPointType = "Principal". Especificando **SetupEntryPoint** é especialmente útil quando você quiser executar determinada operação de configuração de privilégio alto em uma conta do sistema. O código de serviço real pode executar em uma conta de privilégio inferior.

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>Aplicar uma política padrão para todos os pacotes de código de serviço
A seção **DefaultRunAsPolicy** é usada para especificar uma conta de usuário padrão para todos os pacotes de código que não têm um específicos **RunAsPolicy** definido. Se a maioria dos pacotes de código especificados no manifesto de serviço usado por um aplicativo precisa executar sob o mesmo usuário, o aplicativo apenas pode definir uma política padrão de RunAs com conta de usuário. O exemplo a seguir especifica que se um pacote de código não tiver um **RunAsPolicy** especificado, o pacote de código deve ser executado a **MyDefaultAccount** especificada na seção de objetos.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~
### <a name="using-an-active-directory-domain-group-or-user"></a>Usando um usuário ou grupo de domínio do Active Directory
Serviço tecidos instalado no Windows Server com o instalador autônomo, pode executar o serviço sob as credenciais para uma conta de grupo ou usuário do Active Directory (AD). Observação: Esse é o Active Directory no local em seu domínio e não é com o Azure Active Directory (AAD). Usando um domínio de usuário ou grupo, você pode acessar outros recursos do domínio (por exemplo, compartilhamentos de arquivos) que foram concedidos permissões.

O exemplo a seguir mostra um usuário do AD chamado *TestUser* com suas senhas de domínio criptografadas usando um certificado denominado *MyCert*. Você pode usar o `Invoke-ServiceFabricEncryptText` comando do Powershell para criar o texto de codificação secreta. Consulte este artigo [Gerenciando segredos em aplicativos de serviço tecidos](service-fabric-application-secret-management.md) para obter detalhes sobre como. A chave privada do certificado para descriptografar a senha deve ser implantada na máquina local em um método de fora da faixa (no Azure é via o Gerenciador de recursos). Em seguida, quando serviço tecidos implanta o pacote de serviço na máquina, é capaz de descriptografar o segredo e juntamente com o nome de usuário, autenticar com AD para executar nessas credenciais.

~~~
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
~~~


## <a name="assign-securityaccesspolicy-for-http-and-https-endpoints"></a>Atribuir SecurityAccessPolicy para pontos de extremidade HTTP e HTTPS
Se você aplica uma política de RunAs para um serviço e manifesto de serviço declara recursos de ponto de extremidade com o protocolo HTTP, você deverá especificar um **SecurityAccessPolicy** para garantir que portas alocadas para esses pontos de extremidade corretamente estão listado da conta de usuário de RunAs que o serviço é executado em controle de acesso. Caso contrário, **http. sys** não tem acesso ao serviço e obter falhas com chamadas do cliente. O exemplo seguinte aplica a conta de Customer3 para um ponto de extremidade chamado **ServiceEndpointName**, dando a ele direitos de acesso completo.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

O ponto de extremidade HTTPS, você também precisa indicar o nome do certificado para retornar para o cliente. Você pode fazer isso usando **EndpointBindingPolicy**, com o certificado definido em uma seção de certificados no manifesto do aplicativo.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## <a name="a-complete-application-manifest-example"></a>Um exemplo de manifesto de aplicativo completo
Manifesto do aplicativo a seguir mostra várias configurações diferentes:

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

* [Entender o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos em um manifesto de serviço](service-fabric-service-manifest-resources.md)
* [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
