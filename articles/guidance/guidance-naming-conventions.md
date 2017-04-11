<properties
   pageTitle="Recomendado convenções de nomenclatura para recursos Azure | Orientação | Microsoft Azure"
   description="Convenções de nomenclatura recomendadas para recursos Azure. Como nomear máquinas virtuais, contas de armazenamento, redes, redes virtuais, sub-redes e outras entidades Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/27/2016"
   ms.author="christb"/>
   
# <a name="recommended-naming-conventions-for-azure-resources"></a>Convenções de nomenclatura recomendadas para recursos Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

A escolha de um nome para qualquer recurso no Microsoft Azure é importante porque:

- É difícil alterar um nome mais tarde.
- Nomes devem atender aos requisitos de seu tipo de recurso específico.

Convenções de nomenclatura consistentes tornam os recursos mais fácil de localizar. Eles também podem indicar a função de um recurso em uma solução.

Este artigo é um resumo das regras de nomeação e restrições para recursos Azure e um conjunto de linha de base de recomendações para convenções de nomenclatura.  Você pode usar essas recomendações como ponto de partida para seus próprios convenções específicos às suas necessidades.  

A chave para o sucesso com convenções de nomenclatura é estabelecendo e acompanhá-los em seus aplicativos e organizações. 

## <a name="naming-subscriptions"></a>Assinaturas de nomenclatura

Ao nomear assinaturas do Azure, nomes detalhados Verifique Compreendendo o contexto e a finalidade de cada assinatura limpar.  Ao trabalhar em um ambiente com várias assinaturas, seguindo uma convenção de nomenclatura compartilhada pode melhorar clareza.

Um padrão recomendado para assinaturas de nomenclatura é:

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- Empresa geralmente seria o mesmo para cada assinatura. Entretanto, algumas empresas podem ter empresas filho dentro da estrutura organizacional. Essas empresas podem ser gerenciadas por um grupo central de TI. Nesses casos, eles podem ser diferenciados fazendo com que o nome de empresa pai (*Contoso*) e o nome da empresa filho (*North Wind*).

- Departamento é um nome dentro da organização onde um grupo de indivíduos trabalhar. Este item dentro do namespace como opcionais.

- Linha de produtos é um nome específico de um produto ou uma função que é executada a partir dentro do departamento.
Isso é geralmente opcional para serviços e aplicativos de face interna. No entanto, é altamente recomendável usar para serviços de público que exigem separação fácil e identificação (como separação clara de registros de cobrança).

- Ambiente é o nome que descreva o ciclo de vida de implantação de aplicativos ou serviços, como desenvolvimento, p & r ou produto.

| Empresa | Departamento | Linha de produto ou serviço | Ambiente | Nome completo  |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Produção | Contoso SocialGaming AwesomeService produção |
| Contoso | SocialGaming | AwesomeService | Desenvolvimento | Contoso SocialGaming AwesomeService desenvolvimento |
| Contoso | ELE | InternalApps | Produção | Contoso IT InternalApps produção |
| Contoso | ELE | InternalApps | Desenvolvimento | Contoso IT InternalApps desenvolvimento |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc. -->

## <a name="use-affixes-to-avoid-ambiguity"></a>Use afixos para evitar ambiguidade

Ao nomear recursos no Azure, é recomendável usar comuns prefixos ou sufixos para identificar o tipo e o contexto do recurso.  Enquanto todas as informações sobre o tipo de metadados, contexto, está disponível por meio de programação, aplicar afixos comuns simplifica a identificação visual.  Quando incorporação afixos na sua convenção de nomenclatura, é importante especificar claramente se o afixo é no início do nome (prefixo) ou no final (sufixo).  

Por exemplo, aqui estão dois nomes possíveis para um serviço de hospedagem um mecanismo de cálculo:

- SvcCalculationEngine (prefixo)
- CalculationEngineSvc (sufixo)

Afixos podem se referir a diferentes aspectos que descrevem os recursos específicos. A tabela a seguir mostra alguns exemplos normalmente usados.

| Aspecto | Exemplo | Anotações |
| ------ | ------- | ----- |
| Ambiente | desenvolvimento, produção, p & r | Identifica o ambiente para o recurso |
| Local | UW (conosco Oeste), ue (conosco Leste) | Identifica a região em que o recurso é implantado |
| Instância | 01, 02 | Para os recursos que têm mais de uma instância nomeada (servidores web, etc.). |
| Produto ou serviço | serviço | Identifica o produto, aplicativo ou serviço que ofereça suporte ao recurso |
| Função | SQL, da web, de mensagens | Identifica a função do recurso associado |

Ao desenvolver uma convenção de nomenclatura específica para sua empresa ou projetos, é importante para escolher um conjunto comum de afixos e sua posição (sufixo ou prefixo).

## <a name="naming-rules-and-restrictions"></a>Regras e restrições de nomenclatura

Cada tipo de recurso ou serviço no Azure aplica um conjunto de nomenclatura restrições e escopo; qualquer padrão ou convenção de nomenclatura deve cumprir as regras de nomenclatura necessárias e escopo.  Por exemplo, enquanto o nome de uma máquina virtual mapeia para um nome DNS (e, portanto, é necessário para ser exclusivo em todos do Azure), o nome de um VNET é delimitado para o grupo de recursos que ele é criado dentro.

Em geral, evite tendo quaisquer caracteres especiais (`-` ou `_`) como o primeiro ou último caractere em qualquer nome. Esses caracteres fará com que a maioria das regras de validação falha.

| Categoria | Serviço ou entidade | Escopo | Comprimento | Maiusculas e minúsculas | Caracteres válidos | Padrão de sugerido | Exemplo |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| Grupo de recursos | Grupo de recursos | Global | 1-64 | Maiusculas e minúsculas | ALPHANUMERIC, sublinhado e hífen | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| Grupo de recursos | Conjunto de disponibilidade | Grupo de recursos | 1-80 | Maiusculas e minúsculas | ALPHANUMERIC, sublinhado e hífen | `<service-short-name>-<context>-as` | `profx-sql-as` |
| Geral | Marca | Entidade associada | 512 (nome), 256 (valor) | Maiusculas e minúsculas | Alfanuméricos | `"key" : "value"` | `"department" : "Central IT"` |
| Calcular | Máquina virtual | Grupo de recursos | 1-15 | Maiusculas e minúsculas | ALPHANUMERIC, sublinhado e hífen | `<name>-<role>-<instance>` | `profx-sql-001` |
| Armazenamento | Nome de conta de armazenamento (dados) | Global | 3-24 | Letras minúsculas | Alfanuméricos | `<service short name><type><number>` | `profxdata001` |
| Armazenamento | Nome de conta de armazenamento (discos) | Global | 3-24 | Letras minúsculas | Alfanuméricos | `<vm name without dashes>st<number>` | `profxsql001st0` |
| Armazenamento | Nome de contêiner | Conta de armazenamento | 3-63 |   Letras minúsculas | ALPHANUMERIC e traço | `<context>` | `logs` |
| Armazenamento | Nome de blob | Contêiner | 1-1024 | Maiusculas e minúsculas | Qualquer caractere de URL | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Armazenamento | Nome da fila | Conta de armazenamento | 3-63 | Letras minúsculas | ALPHANUMERIC e traço | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| Armazenamento | Nome da tabela | Conta de armazenamento | 3-63 |Maiusculas e minúsculas | Alfanuméricos | `<service short name>-<context>` | `awesomeservice-logs` |
| Armazenamento | Nome do arquivo | Conta de armazenamento | 3-63 | Letras minúsculas | Alfanuméricos | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Rede | Rede virtual (VNet) | Grupo de recursos | 2-64 | Maiusculas e minúsculas | ALPHANUMERIC, traço, sublinhado e período | `<service short name>-[section]-vnet` | `profx-vnet` |
| Rede | Sub-rede | VNet pai | 2-80 | Maiusculas e minúsculas | ALPHANUMERIC, sublinhado, traço e período | `<role>-subnet` | `gateway-subnet` |
| Rede | Interface de rede | Grupo de recursos | 1-80 | Maiusculas e minúsculas | ALPHANUMERIC, traço, sublinhado e período | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| Rede | Grupo de segurança de rede | Grupo de recursos | 1-80 | Maiusculas e minúsculas | ALPHANUMERIC, traço, sublinhado e período | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| Rede | Regra de grupo de segurança de rede | Grupo de recursos | 1-80 | Maiusculas e minúsculas | ALPHANUMERIC, traço, sublinhado e período | `<descriptive context>` | `sql-allow` |
| Rede | Endereço IP público | Grupo de recursos | 1-80 | Maiusculas e minúsculas | ALPHANUMERIC, traço, sublinhado e período | `<vm or service name>-pip` | `profx-sql1-pip` |
| Rede | Balanceador de carga | Grupo de recursos | 1-80 | Maiusculas e minúsculas | ALPHANUMERIC, traço, sublinhado e período | `<service or role>-lb` | `profx-lb` |
| Rede | Carregar a configuração de regras equilibrada | Balanceador de carga | 1-80 | Maiusculas e minúsculas | ALPHANUMERIC, traço, sublinhado e período | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | TODO | TODO
-->

## <a name="organizing-resources-with-tags"></a>Organizar recursos com marcas

O Gerenciador de recursos do Azure dá suporte à marcação entidades com cadeias de caracteres de texto aleatório para identificar o contexto e simplificar a automação.  Por exemplo, a marca `"sqlVersion: "sql2014ee"` pôde identificar VMs em uma implantação executando o SQL Server 2014 Enterprise Edition para execução de um script automatizado contra elas.  Marcas devem ser usadas para ampliar e aprimorar o contexto na lateral das convenções de nomenclatura escolhido.

> [AZURE.TIP]Uma outra vantagem de marcas é que marcas abrangem grupos de recursos, permitindo que você vincular e correlação entidades em implantações de diferentes.

Cada recurso ou grupo de recursos pode ter no máximo **15** marcas. O nome da marca está limitado aos 512 caracteres e o valor de marca está limitado a 256 caracteres.

Para obter mais informações sobre recursos de marcação, consulte [usando marcas para organizar seus recursos Azure](../resource-group-using-tags.md).

Alguns dos casos comuns de uso de marcação são:

- **Cobrança**; Agrupando recursos e associando cobrança ou encargo códigos de volta.
- **Identificação de contexto de serviço**; Identificar grupos de recursos entre grupos de recursos para operações e agrupamento comuns
- **Controle de acesso e contexto de segurança**; Identificação de função administrativa com base em portfólio, sistema, serviço, aplicativo, instância, etc.

> [AZURE.TIP]Marca antecipado - marca com frequência.  Melhor ter uma linha de base esquema no lugar de marcação e ajustar ao longo do tempo em vez de ter que fazer ajustes após o fato.  

Um exemplo de algumas abordagens de marcação comuns:

| Nome da marca | Chave | Exemplo | Comentário |
| -------- | --- | ------- | ------- |
| Fatura / interno para ID de devolução | billTo  | `IT-Chargeback-1234` | Um e/s interna ou código de cobrança |
| Operador ou diretamente responsável individuais (DRI) | managedBy | `joe@contoso.com`  | Alias ou endereço de email |
| Nome do projeto | nome do projeto | `myproject`  | Nome da linha do projeto ou do produto |
| Versão do projeto | versão do projeto | `3.4`  | Versão da linha de projeto ou de produto |
| Ambiente | ambiente | `<Production, Staging, QA >` | Identificador ambiental | 
| Camada | camada | `Front End, Back End, Data` | Identificação de camada ou/contexto de função |
| Perfil de dados | dataProfile | `Public, Confidential, Restricted, Internal` | Confidencialidade dos dados armazenados no recurso |
 
## <a name="tips-and-tricks"></a>Dicas e truques

Alguns tipos de recursos podem exigir cuidado adicional sobre nomeação e convenções.

### <a name="virtual-machines"></a>Máquinas virtuais

Especialmente em topologias maiores, máquinas virtuais de nomenclatura cuidadosamente simplifica identificando a função e a finalidade de cada máquina e habilitando os scripts mais previsíveis.

> [AZURE.WARNING]Cada máquina virtual no Azure tem um nome de recurso Azure e um nome de host do sistema operacional.  
> Se o nome do recurso e o nome de host forem diferentes, gerenciar as VMs pode ser desafiador e deve-se evitar.
> Por exemplo, se uma máquina virtual é criada a partir de um VHD que já contém um sistema operacional configurado com um nome de host.

- [Convenções de nomenclatura para VMs do Windows Server](https://support.microsoft.com/en-us/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

### <a name="storage-accounts-and-storage-entities"></a>Contas de armazenamento e entidades de armazenamento

Há dois casos de uso principal para contas de armazenamento - fazendo discos para VMs e armazenar dados em blobs, filas e tabelas.  Contas de armazenamento usadas para discos de máquina virtual devem acompanhar a convenção de nomenclatura de associá-los com o nome da máquina virtual pai (e com a necessidade potencial de várias contas de armazenamento de ponta SKUs de máquina virtual, também aplicar um sufixo de número).

> [AZURE.TIP]Contas de armazenamento - se para dados ou discos - deverão seguir uma convenção de nomenclatura que permite a várias contas de armazenamento ser utilizada (isto é sempre usando um sufixo numérico).

É possível configurar um nome de domínio personalizado para acessar os dados de blob em sua conta de armazenamento do Azure.
O ponto de extremidade padrão para o serviço de Blob é `https://mystorage.blob.core.windows.net`.

Mas se você mapear um domínio personalizado (como www.contoso.com) para o ponto de extremidade do blob para sua conta de armazenamento, você também pode acessar dados em sua conta de armazenamento de blob usando esse domínio. Por exemplo, com um nome de domínio personalizado, `http://mystorage.blob.core.windows.net/mycontainer/myblob` pode ser acessada como `http://www.contoso.com/mycontainer/myblob`.

Para obter mais informações sobre como configurar esse recurso, consulte [Configurar um nome de domínio personalizado para seu ponto de extremidade de armazenamento de Blob](../storage/storage-custom-domain-name.md).

Para obter mais informações sobre nomeação blobs, contêineres e tabelas:

- [Nomear e referenciando contêineres, Blobs e metadados](https://msdn.microsoft.com/library/dd135715.aspx)
- [Filas e metadados de nomenclatura](https://msdn.microsoft.com/library/dd179349.aspx)
- [Tabelas de nomenclatura](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Um nome de blob pode conter qualquer combinação de caracteres, mas caracteres de URL reservados devem haver escape corretamente. Evite nomes de blob que terminam com um ponto (.), uma barra (/), ou uma sequência ou uma combinação dos dois. Por convenção, a barra é o separador de diretório **virtual** . Não use uma barra invertida (\) em um nome de blob. O cliente APIs pode permitir que ele, mas depois falhar ao hash corretamente, e as assinaturas não coincidirão.

Não é possível modificar o nome de uma conta de armazenamento ou contêiner depois que ela foi criada.
Se você quiser usar um novo nome, você deve excluí-la e criar um novo.

> [AZURE.TIP] Recomendamos que você estabelecer uma convenção de nomenclatura para todas as contas de armazenamento e tipos antes de embarcar no desenvolvimento de um novo serviço ou o aplicativo.

## <a name="example---deploying-an-n-tier-service"></a>Exemplo: Implantando um serviço de n camadas

Neste exemplo, vamos definir uma configuração de serviço de fileiras, consiste em servidores front-end IIS (hospedados no Windows Server VMs), com o SQL Server (hospedado em duas VMs do Windows Server), um cluster de ElasticSearch (hospedado no 6 VMs Linux) e as contas de armazenamento associado, redes virtuais, recursos de grupo e balanceador de carga.

Vamos começar definindo as convenções contextuais para este aplicativo:

| Entidade | Convenção | Descrição  |
| ------ | ---------- | ------------ |  
| Nome do serviço | `profx` | O nome curto do aplicativo ou serviço sendo implantado |
| Ambiente | `prod` | Isso é para a implantação de produção (em vez de perguntas e respostas, teste, etc.) |

Da linha de base que estamos pode mapeie as convenções para cada um dos tipos de recursos:

| Tipo de recurso | Base de convenção | Exemplo |
| ------------- | --------------- | ------- |
| Assinatura | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| Grupo de recursos | `servicename-rg` | `profx-rg` |
| Rede virtual | `servicename-vnet` | `profx-vnet` |
| Sub-rede | `role-subnet` | `sql-vnet` |
| Balanceador de carga | `servicename-lb` | `profx-lb` |
| Máquina virtual | `servicename-role[number]` | `profx-sql0` |
| Conta de armazenamento | `<vmnamenodashes>st<num>` | `profxsql0st0` |

Como visto no seguinte diagrama:

![diagrama de topologia do aplicativo] (media/guidance-naming-conventions/guidance-naming-convention-example.png "Topologia do aplicativo de amostra")

## <a name="sample---azure-cli-script-for-deploying-the-sample-above"></a>Exemplo - script CLI Azure para implantar o exemplo acima

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

    # Create the network interface card for this VM
    azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

    # Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
    azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
        --boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
    create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```
