<properties
   pageTitle="Dimensionar seu cluster ACS com a CLI Azure | Microsoft Azure"
   description="Como dimensionar seu cluster de serviço de contêiner do Azure usando a CLI do Azure."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contêineres, Microserviços, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/03/2016"
   ms.author="timlt"/>

# <a name="scale-an-azure-container-service"></a>Escala de um serviço de contêiner Azure

Você pode dimensionar o número de nós tem seu Azure contêiner ACS (serviço) usando a ferramenta de CLI do Azure. Quando você usa o CLI Azure dimensionar, a ferramenta retorna um novo arquivo de configuração que representa a alteração aplicada ao contêiner.

## <a name="about-the-command"></a>Sobre o comando

A CLI do Azure deve estar no modo de Gerenciador de recursos do Azure para interagir com contêineres do Azure. Você pode alternar para o modo do Gerenciador de recursos chamando `azure config mode arm`. O `acs` comando tem um comando de filho chamado `scale` que faz todas as operações de escala para um serviço de contêiner. Você pode obter ajuda sobre os vários parâmetros usados no comando escala executando `azure acs scale --help`, que produz algo semelhante a esta:

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>Use o comando Dimensionar

Para dimensionar um serviço de contêiner, primeiro é necessário saber o **grupo de recursos** e o **nome do Azure contêiner ACS (serviço)**e também especificar a nova contagem de agentes. Usando um valor maior ou menor, você pode dimensionar para baixo ou para cima respectivamente.

Talvez você queira saber qual a contagem atual de agentes de um serviço de contêiner antes de você dimensiona. Use o `azure acs show <resource group> <ACS name>` comando para retornar a configuração do ACS. Observe o resultado de <mark>contagem</mark> .

#### <a name="see-current-count"></a>Consulte contagem atual

```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>Dimensionar a nova contagem

Como provavelmente já é óbvio, você pode dimensionar o serviço de contêiner chamando `azure acs scale` e fornecendo o **grupo de recursos**, o **nome de ACS**e a **contagem de agente**. Quando você dimensionar um serviço de contêiner, CLI Azure retorna uma cadeia JSON que representa a nova configuração do serviço do contêiner, incluindo a nova contagem de agente.

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>Próximas etapas

- [Implantar um cluster](container-service-deployment.md)