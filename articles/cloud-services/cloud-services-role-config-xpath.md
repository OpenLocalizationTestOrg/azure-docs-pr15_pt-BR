<properties 
pageTitle="Folha de enganar nuvem função Serviços config XPath | Microsoft Azure" 
description="As diversas configurações de XPath pode usar na configuração de função de serviço de nuvem para expor as configurações como uma variável de ambiente." 
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
ms.date="08/10/2016" 
ms.author="adegeo"/>

# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Expor definições de configuração de função como uma variável de ambiente com XPath

No arquivo de definição de serviço de função da web ou trabalhador de serviço de nuvem, você pode expor os valores de configuração de tempo de execução como variáveis de ambiente. Os seguintes valores de XPath são suportados (que correspondem aos valores de API).

Esses valores XPath também estão disponíveis por meio da biblioteca de [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) . 

## <a name="app-running-in-emulator"></a>Aplicativo em execução no emulador

Indica que o aplicativo é executado no emulador.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@emulated" |
| Código  | var x = RoleEnvironment.IsEmulated; |


## <a name="deployment-id"></a>ID de implantação

Recupera a ID de implantação para a instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@id" |
| Código  | var deploymentId = RoleEnvironment.DeploymentId; |


## <a name="role-id"></a>ID de função 

Recupera a ID de função atual para a instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@id" |
| Código  | var id = RoleEnvironment.CurrentRoleInstance.Id; |


## <a name="update-domain"></a>Domínio de atualização

Recupera o domínio de atualização da instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Código  | var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |


## <a name="fault-domain"></a>Domínio de falhas

Recupera o domínio da falha da instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Código  | var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |


## <a name="role-name"></a>Nome de função

Recupera o nome de função das instâncias.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Código  | var rname = RoleEnvironment.CurrentRoleInstance.Role.Name;  |


## <a name="config-setting"></a>Configuração

Recupera o valor da configuração especificada.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Código  | configuração de var = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |
 
## <a name="local-storage-path"></a>Caminho de armazenamento local

Recupera o caminho de armazenamento local para a instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Código  | var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). RootPath; |


## <a name="local-storage-size"></a>Tamanho de armazenamento local

Recupera o tamanho do armazenamento local para a instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Código  | var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protocolo de ponto de extremidade 

Recupera o protocolo de ponto de extremidade para a instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Código  | var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. Protocolo; |

## <a name="endpoint-ip"></a>Ponto de extremidade IP

Obtém o endereço IP do ponto de extremidade especificado.

| Tipo | Exemplo |
| ----- | ---- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Código  | var endereço = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Address |

## <a name="endpoint-port"></a>Porta de ponto de extremidade 

Recupera a porta de ponto de extremidade para a instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Código  | porta de var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |





## <a name="example"></a>Exemplo

Aqui está um exemplo de uma função de trabalho que cria uma tarefa de inicialização com uma variável de ambiente denominada `TestIsEmulated` definido como o [ @emulated valor xpath](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o arquivo de [ServiceConfiguration](cloud-services-model-and-package.md#serviceconfigurationcscfg) .

Crie um pacote de [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) .

Habilite a [área de trabalho remota](cloud-services-role-enable-remote-desktop.md) para uma função.
