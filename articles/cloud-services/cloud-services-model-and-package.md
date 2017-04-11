<properties
    pageTitle="O que é um modelo de serviço de nuvem e pacote | Microsoft Azure"
    description="Descreve o pacote (.cspkg) no Azure e o modelo do serviço de nuvem (.csdef, .cscfg)"
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>
<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>

# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Qual é o modelo do serviço de nuvem e como compactá-la?
Um serviço de nuvem é criado a partir de três componentes, a definição de serviço _(.csdef)_, a configuração do serviço _(.cscfg)_e um pacote de serviço _(.cspkg)_. Arquivos de **ServiceDefinition.csdef** e **ServiceConfig.cscfg** são baseados em XML e descrevem a estrutura do serviço de nuvem e como ela está configurada; coletivamente chamados o modelo. O **ServicePackage.cspkg** é um arquivo zip que é gerado da **ServiceDefinition.csdef** e entre outras coisas, contém todas as dependências necessárias baseada em binário. Azure cria um serviço de nuvem do **ServicePackage.cspkg** e o **ServiceConfig.cscfg**.

Depois que o serviço de nuvem está em execução no Azure, você pode reconfigurá-lo através do arquivo de **ServiceConfig.cscfg** , mas você não pode alterar a definição.

## <a name="what-would-you-like-to-know-more-about"></a>O que você gostaria de saber mais sobre?

* Eu quiser saber mais sobre os arquivos [ServiceDefinition.csdef](#csdef) e [ServiceConfig.cscfg](#cscfg) .
* Eu já sabe sobre isso, dê [alguns exemplos](#next-steps) sobre o que pode configurar.
* Quero criar o [ServicePackage.cspkg](#cspkg).
* Estou usando o Visual Studio e desejo …
    * [Criar um novo serviço de nuvem][vs_create]
    * [Reconfigurar um serviço de nuvem existente][vs_reconfigure]
    * [Implantar um projeto de serviço de nuvem][vs_deploy]
    * [Área de trabalho remota em uma instância de serviço de nuvem][remotedesktop]

<a name="csdef"></a>
## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
O arquivo de **ServiceDefinition.csdef** Especifica as configurações que são usadas pelo Azure para configurar um serviço de nuvem. O [Esquema de definição de serviço do Azure (.csdef arquivo)](https://msdn.microsoft.com/library/azure/ee758711.aspx) fornece o formato permitido para um arquivo de definição de serviço. O exemplo a seguir mostra as configurações que podem ser definidas para as funções da Web e trabalhador:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Você pode se referir a [' [esquema de definição de serviço]] para uma melhor compreensão do esquema XML usado aqui, no entanto, aqui está uma explicação rápida de alguns dos elementos:

**Sites**  
Contém as definições de aplicativos web ou sites que são hospedados no IIS7.

**InputEndpoints**  
Contém as definições para pontos de extremidade que são usados para contatar o serviço de nuvem.

**InternalEndpoints**  
Contém as definições para pontos de extremidade que são usados por instâncias de função para se comunicar com os outros.

**ConfigurationSettings**  
Contém as definições de configuração para recursos de uma função específica.

**Certificados**  
Contém as definições de certificados que são necessários para uma função. Exemplo de código anterior mostra um certificado que é usado para a configuração do Azure Connect.

**LocalResources**  
Contém as definições para os recursos de armazenamento local. Um recurso de armazenamento local é um diretório reservado no sistema de arquivos da máquina virtual na qual uma instância de uma função está em execução.

**Importa**  
Contém as definições para módulos importados. Exemplo de código anterior mostra os módulos para Conexão de área de trabalho remota e Azure Connect.

**Inicialização**  
Contém tarefas que são executadas quando a função for iniciado. As tarefas são definidas em um arquivo executável ou. cmd.



<a name="cscfg"></a>
## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration
A configuração das configurações de seu serviço de nuvem é determinada pelos valores no arquivo **ServiceConfiguration** . Especifique o número de instâncias que você deseja implantar para cada função neste arquivo. Os valores para as configurações que você definiu no arquivo de definição de serviço são adicionados ao arquivo de configuração de serviço. As impressões digitais para qualquer certificados de gerenciamento que são associados com o serviço de nuvem também são adicionados ao arquivo. O [Esquema de configuração de serviço do Azure (.cscfg arquivo)](https://msdn.microsoft.com/library/azure/ee758710.aspx) fornece o formato permitido para um arquivo de configuração do serviço.

O arquivo de configuração do serviço não é fornecido com o aplicativo, mas é carregado para o Azure como um arquivo separado e é usado para configurar o serviço de nuvem. Você pode carregar um novo arquivo de configuração de serviço sem reimplantar seu serviço de nuvem. Os valores de configuração para o serviço de nuvem podem ser alterados enquanto o serviço de nuvem é executado. O exemplo a seguir mostra as configurações que podem ser definidas para as funções da Web e trabalhador:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Você pode se referir ao [Esquema de configuração do serviço](https://msdn.microsoft.com/library/azure/ee758710.aspx) para entender melhor o esquema XML usado aqui, no entanto, aqui está uma explicação rápida dos elementos:

**Instâncias**  
Configura o número de instâncias para a função em execução. Para impedir que o seu serviço de nuvem potencialmente se tornar disponível durante as atualizações, é recomendável que você implantar mais de uma instância de suas funções voltados para a web. Fazendo isso, está atendendo às diretrizes de no [Contrato de nível de serviço de computação de Azure (SLA)](http://azure.microsoft.com/support/legal/sla/), que garante 99,95% conectividade externa para funções voltado para a Internet quando duas ou mais ocorrências de função são implantadas para um serviço.

**ConfigurationSettings**  
Configura as configurações para as instâncias em execução para uma função. O nome do `<Setting>` elementos devem coincidir com as definições de configuração no arquivo de definição de serviço.

**Certificados**  
Configura os certificados usados pelo serviço. Exemplo de código anterior mostra como definir o certificado para o módulo de acesso remoto. O valor do atributo *impressão digital* deve ser definido para a impressão digital do certificado usar.

<p/>

 >[AZURE.NOTE] A impressão digital do certificado pode ser adicionada ao arquivo de configuração usando um editor de texto ou o valor pode ser adicionado na guia **certificados** na página de **Propriedades** da função no Visual Studio.



## <a name="defining-ports-for-role-instances"></a>Definir portas para instâncias de função
Azure permite somente um ponto de entrada para uma função web. Isso significa que todo o tráfego ocorre por meio de um endereço IP. Você pode configurar seus sites para compartilhar uma porta Configurando o cabeçalho do host para direcionar a solicitação para o local correto. Você também pode configurar seus aplicativos para escutar portas conhecidas no endereço IP.

O exemplo a seguir mostra a configuração para uma função da web com um aplicativo de site e web. O site está configurado como o local de entrada padrão na porta 80, e os aplicativos web são configurados para receber solicitações de um cabeçalho de host alternativo que é chamado de "mail.mysite.cloudapp.net".

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" <mark>port="80"</mark> />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" <mark>hostheader="mail.mysite.cloudapp.net"</mark> />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Alterar a configuração de uma função
Você pode atualizar a configuração do seu serviço de nuvem enquanto ele é executado no Azure, sem que o serviço permaneça offline. Para alterar as informações de configuração, você pode carregar um novo arquivo de configuração, ou editar o arquivo de configuração no lugar e aplicá-la a seu serviço em execução. As seguintes alterações podem ser feitas na configuração de um serviço:

- **Alterar os valores de configurações**  
Quando uma configuração alterações, uma instância da função pode optar por aplicar a alteração enquanto a instância está online, ou para a Lixeira da instância normalmente e aplique a alteração enquanto a instância está offline.

- **Alterar a topologia de serviço de instâncias de função**  
Alterações de topologia não afetam instâncias em execução, exceto onde uma instância está sendo removida. Todas as instâncias restantes geralmente não precisam ser reciclados; No entanto, você pode optar por Lixeira instâncias de função em resposta a uma alteração de topologia.

- **Alterando a impressão digital de certificado**  
Você só pode atualizar um certificado quando uma instância da função estiver offline. Se um certificado é adicionado, excluído ou alterado enquanto uma instância da função está online, Azure normalmente leva a instância offline para atualizar o certificado e colocá-lo online novamente após a mudança estiver concluída.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Alterações de configuração de manipulação com eventos de tempo de execução de serviço
A [Biblioteca de tempo de execução do Azure](https://msdn.microsoft.com/library/azure/mt419365.aspx) inclui o namespace [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx) , que fornece classes para interagir com o ambiente Azure do código em execução em uma instância de uma função. A classe [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) define os seguintes eventos que são gerados antes e após uma alteração na configuração:

- **Evento de [alteração](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx)**  
Isso ocorre antes que a alteração de configuração é aplicada a uma instância especificada de uma função dando a você uma chance de tomadas instâncias de função, se necessário.
- **Evento de [alterado](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx)**  
Ocorre depois que a alteração de configuração é aplicada a uma instância especificada de uma função.

> [AZURE.NOTE] Como as alterações de certificado sempre levar as instâncias de uma função offline, eles não aumente os eventos RoleEnvironment.Changing ou RoleEnvironment.Changed.

<a name="cspkg"></a>
## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Para implantar um aplicativo como um serviço de nuvem no Azure, primeiro você deve empacotar o aplicativo no formato apropriado. Você pode usar a ferramenta de linha de comando **CSPack** (instalada com o [SDK do Azure](https://azure.microsoft.com/downloads/)) para criar o arquivo de pacote como uma alternativa para Visual Studio.

**CSPack** usa o conteúdo do arquivo de definição de serviço e arquivo de configuração do serviço para definir o conteúdo do pacote. **CSPack** gera um arquivo de pacote de aplicativo (.cspkg) que você pode carregar no Azure usando o [portal do Azure](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Por padrão, o pacote é chamado `[ServiceDefinitionFileName].cspkg`, mas você pode especificar um nome diferente usando o `/out` opção de **CSPack**.

**CSPack** está geralmente localizada em  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

>[AZURE.NOTE]
CSPack.exe (no windows) está disponível executando o atalho do **Prompt de comando do Microsoft Azure** que está instalado com o SDK.  
>  
>Execute o programa CSPack.exe sozinha para ver a documentação sobre todos os comandos e opções possíveis.

<p />

>[AZURE.TIP]
Executar o serviço de nuvem localmente no **Emulador de computação do Microsoft Azure**, use a opção de **/copyonly** que esta opção copia os arquivos binários para o aplicativo a um layout de diretório do qual elas podem ser executadas no emulador computação.

### <a name="example-command-to-package-a-cloud-service"></a>Comando de exemplo para um serviço de nuvem do pacote
O exemplo a seguir cria um pacote de aplicativos que contém as informações para uma função da web. O comando Especifica o arquivo de definição de serviço para usar o diretório onde os arquivos binários podem ser encontrados, e o nome do arquivo de pacote.

    cspack [DirectoryName]\[ServiceDefinition]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /out:[OutputFileName]

Se o aplicativo contém uma função web e uma função de trabalho, o comando a seguir é usado:

    cspack [DirectoryName]\[ServiceDefinition]
           /out:[OutputFileName]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]

Onde as variáveis são definidas da seguinte maneira:

| Variável                  | Valor |
| ------------------------- | ----- |
| \[DirectoryName\]         | A subpasta no diretório raiz do projeto que contém o arquivo .csdef do projeto Azure.|
| \[ServiceDefinition\]     | O nome do arquivo de definição de serviço. Por padrão, esse arquivo é chamado ServiceDefinition.csdef.  |
| \[OutputFileName\]        | O nome para o arquivo de pacote gerado. Normalmente, isso é definido para o nome do aplicativo. Se nenhum nome de arquivo for especificado, o pacote de aplicativo é criado como \[ApplicationName\].cspkg.|
| \[Nome de função\]              | O nome da função conforme definido no arquivo de definição de serviço.|
| \[RoleBinariesDirectory] | O local dos arquivos binários para a função.|
| \[VirtualPath\]           | Os diretórios físicos para cada caminho virtual definidas na seção Sites da definição do serviço.|
| \[PhysicalPath\]          | Os diretórios físicos do conteúdo para cada caminho virtual definidos no nó site da definição do serviço.|
| \[RoleAssemblyName\]      | O nome do arquivo binário para a função.| 


## <a name="next-steps"></a>Próximas etapas

Estou criando um pacote de serviço de nuvem e desejo …

* [Configuração de área de trabalho remota para uma instância de serviço de nuvem][remotedesktop]
* [Implantar um projeto de serviço de nuvem][deploy]

Estou usando o Visual Studio e desejo …

* [Criar um novo serviço de nuvem][vs_create]
* [Reconfigurar um serviço de nuvem existente][vs_reconfigure]
* [Implantar um projeto de serviço de nuvem][vs_deploy]
* [Configuração de área de trabalho remota para uma instância de serviço de nuvem][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop.md
[vs_remote]: ../vs-azure-tools-remote-desktop-roles.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
