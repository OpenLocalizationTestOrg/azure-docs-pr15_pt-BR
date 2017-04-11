
<properties
   pageTitle="Criar um cluster de serviço tecidos seguro usando o Gerenciador de recursos do Azure | Microsoft Azure"
   description="Este artigo descreve como configurar um cluster de estrutura de serviço de seguro no Azure usando o Gerenciador de recursos do Azure, Cofre de chave do Azure e o Azure Active Directory (AAD) para autenticação de cliente."
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="create-a-service-fabric-cluster-in-azure-using-azure-resource-manager"></a>Criar um cluster de serviço tecidos no Azure usando o Gerenciador de recursos do Azure

> [AZURE.SELECTOR]
- [Gerenciador de recursos do Azure](service-fabric-cluster-creation-via-arm.md)
- [Portal do Azure](service-fabric-cluster-creation-via-portal.md)

Este é um guia passo a passo que orienta você pelas etapas de configuração de um cluster de estrutura de serviço do Azure seguro no Azure usando o Gerenciador de recursos do Azure. Este guia orienta você pelas seguintes etapas:

 - Configure chave cofre para gerenciar as chaves de segurança para cluster e aplicativo.
 - Crie um cluster seguro no Azure com o Gerenciador de recursos do Azure.
 - Autentica os usuários com o Azure Active Directory (AAD) para gerenciamento de cluster.

Um seguro é um cluster que impede o acesso não autorizado a operações de gerenciamento, que inclui implantando, atualizar e excluir aplicativos, serviços e os dados que elas contêm. Um insegura é um cluster que qualquer pessoa pode se conectar a qualquer momento e realizar operações de gerenciamento. Embora seja possível criar um cluster insegura, é **altamente recomendável para criar um cluster seguro**. Um cluster insegura **não podem ser protegidos posteriormente** - um novo cluster deve ser criado.

Os conceitos são os mesmos para a criação de clusters seguros, sejam clusters clusters Linux ou clusters do Windows. Para mais informações auxiliar os scripts e para a criação de seguros clusters Linux, consulte [criando clusters seguros no Linux](#secure-linux-clusters)

## <a name="log-in-to-azure"></a>Faça logon no Azure
Este guia usa [Azure PowerShell][azure-powershell]. Ao iniciar uma nova sessão do PowerShell, faça logon em sua conta do Azure e selecione sua assinatura antes de executar comandos Azure.

Faça logon em sua conta do azure:

```powershell
Login-AzureRmAccount
```

Selecione sua assinatura:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>Configurar compartimentos de chave

Esta seção percorre criando um cofre de tecla para um cluster de estrutura de serviço no Azure e para aplicativos de serviço tecidos. Para um guia completo sobre Cofre de chave, consulte o [cofre de chave do guia de Introdução][key-vault-get-started].

Estrutura de serviço usa certificados x. 509 para proteger um cluster e fornecer os recursos de segurança do aplicativo. Azure Cofre de chave é usado para gerenciar certificados para clusters de serviço tecidos no Azure. Quando um cluster é implantado no Azure, o provedor de recursos Azure responsável pela criação de serviço tecidos clusters extrai os certificados de chave do compartimento e instala-los no cluster VMs.

O diagrama a seguir ilustra a relação entre o provedor de recursos Azure que usa certificados armazenados na chave cofre quando cria um cluster, um cluster de estrutura de serviço e Cofre de chave:

![Instalação de certificado][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

A primeira etapa é criar um grupo de recursos especificamente para chave cofre. É recomendável colocar Cofre de chave em seu próprio grupo de recursos. Isso permite que você remova os grupos de recursos de computação e armazenamento, incluindo o grupo de recursos que tem o seu cluster de estrutura de serviço sem perder suas chaves e segredos. O grupo de recursos que tenha seu Cofre de chave deve estar na mesma região como cluster que está usando.

```powershell

    New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    
    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>Criar chave cofre 

Crie um cofre de chave no novo grupo de recursos. A chave cofre **deve estar habilitada para implantação** para permitir que o provedor de recursos de serviço tecidos obter certificados de-lo e instalar em nós de cluster:

```powershell

    New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment
    
    
    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all
    
    
    Tags                             :
```

Se você tiver um cofre de chave existente, você pode habilitá-lo para implantação usando Azure CLI:

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```

<a id="add-certificate-to-key-vault"></a>
## <a name="add-certificates-to-key-vault"></a>Adicionar certificados ao Cofre de chave

Certificados são usados na estrutura de serviço para fornecer autenticação e criptografia para proteger vários aspectos de um cluster e seus aplicativos. Para obter mais informações sobre como os certificados são usados em estrutura de serviço, consulte [cenários de segurança do serviço tecidos cluster][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Certificado de servidor e cluster (obrigatório) 

Este certificado é necessário para proteger um cluster e impedir o acesso não autorizado a ele. Ele fornece segurança de cluster de algumas maneiras:
 
 - **Autenticação de cluster:** Autentica comunicação de nó para nó para a federação de cluster. Somente os nós que podem provar sua identidade com este certificado podem ingressar no cluster.
 - **Autenticação do servidor:** Autentica os pontos de extremidade de gerenciamento de cluster para um cliente de gerenciamento, para que o cliente management sabe que ele está falando ao cluster real. Este certificado também fornece SSL para a API de gerenciamento de HTTPS e serviço tecidos Explorer por HTTPS.

Para atender a esses fins, o certificado deve atender aos seguintes requisitos:

 - O certificado deve conter uma chave privada.
 - O certificado deve ser criado para troca de chaves, podem ser exportada para um arquivo de troca de informações pessoais (. pfx).
 - Nome de assunto do certificado deve corresponder o domínio usado para acessar o cluster tecidos de serviço. Este matchng é necessária para fornecer SSL para pontos de extremidade de gerenciamento de HTTPS e o Explorador de estrutura de serviço do cluster. Você não pode obter um certificado SSL de uma autoridade de certificação (CA) para o `.cloudapp.azure.com` domínio. Você deve adquirir um nome de domínio personalizado para seu cluster. Quando você solicita um certificado de uma autoridade de certificação, nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para o seu cluster.

### <a name="application-certificates-optional"></a>Certificados de aplicativo (opcionais)

Qualquer número de certificados adicionais pode ser instalado em um cluster para fins de segurança do aplicativo. Antes de criar seu cluster, considere os cenários de segurança de aplicativo que exigem um certificado para ser instalados em nós, tais como:

 - Criptografia e descriptografia de valores de configuração do aplicativo
 - Criptografia de dados em nós durante a replicação 

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Certificados para uso do recurso Azure provedor de formatação

Arquivos de chave privados (. pfx) podem ser adicionados e usados diretamente por meio de chave cofre. No entanto, o provedor de recursos Azure requer chaves sejam armazenados em um formato especial JSON que inclui a. pfx como uma base 64 codificadas cadeia de caracteres e a senha da chave privada. Para acomodar esses requisitos, chaves devem ser colocadas em uma cadeia de JSON e então armazenar como *segredos* em chave cofre.

Para facilitar esse processo, um módulo do PowerShell está [disponível no GitHub][service-fabric-rp-helpers]. Siga estas etapas para usar o módulo:

  1. Baixe o conteúdo inteiro do repo em um diretório local. 
  2. Importe o módulo na janela do PowerShell:

  ```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
  ```
     
O `Invoke-AddCertToKeyVault` comando neste módulo do PowerShell automaticamente formata uma chave privada do certificado em uma cadeia de caracteres JSON e carrega chave cofre. Usá-lo para adicionar o certificado de cluster e quaisquer certificados de aplicativo adicional a chave do cofre. Repita esta etapa para qualquer certificados adicionais que você deseja instalar no seu cluster.

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"
    
    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault
    
    
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

As cadeias de caracteres anteriores são todos os pré-requisitos de chave cofre para configurar um modelo de Gerenciador de recursos de cluster de serviço tecidos que instala certificados para autenticação de nó, a segurança do ponto de extremidade de gerenciamento e a autenticação e qualquer recursos de segurança de aplicativo adicional que usam certificados x. 509. Neste ponto, agora você deve ter a seguinte configuração no Azure:

 - Grupo de recursos do Cofre de chave
   - Chave cofre
     - Certificado de autenticação de servidor de cluster
     - Certificados de aplicativo

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurar o Active Directory do Azure para autenticação de cliente

AAD permite que organizações (conhecidas como locatários) para gerenciar o acesso do usuário aos aplicativos, que são divididos em aplicativos com um logon baseado na web da interface do usuário e uma experiência de cliente nativo. Neste documento, vamos supor que você já tiver criado um locatário. Caso contrário, comece lendo [como obter um locatário do Active Directory do Azure][active-directory-howto-tenant].

Um cluster de estrutura de serviço oferece vários pontos de entrada para sua funcionalidade de gerenciamento, incluindo o baseado na web [Serviço tecidos Explorer] [ service-fabric-visualizing-your-cluster] e [Visual Studio][service-fabric-manage-application-in-visual-studio]. Como resultado, você criar dois aplicativos AAD para controlar o acesso ao cluster, um aplicativo web e um aplicativo nativo.

Para simplificar algumas das etapas envolvidas na configuração AAD com um cluster de serviço tecidos, criamos um conjunto de scripts do Windows PowerShell.

>[AZURE.NOTE] Você deve executar estas etapas *antes de* criar o cluster isso em casos em que os scripts esperam nomes cluster e pontos de extremidade, esses devem ser os valores planejados, não aquelas que você já criou.

1. [Baixar os scripts] [ sf-aad-ps-script-download] com seu computador.

2. O arquivo zip de atalho, escolha **Propriedades**, em seguida, marque a caixa de seleção **Desbloquear** e aplicar.

3. Extraia o arquivo zip.

4. Executar `SetupApplications.ps1`, fornecendo a TenantId, ClusterName e WebApplicationReplyUrl como parâmetros. Por exemplo:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Você pode encontrar seu **TenantId** executando o comando do PowerShell 'Get-AzureSubscription' '. Isso exibirá o **TenantId** para cada assinatura.

    O **ClusterName** é usada para prefixar os aplicativos AAD criados pelo script. Ele não precisa corresponder ao nome real do cluster exatamente como destina-se somente para tornar mais fácil para mapear artefatos AAD para cluster tecidos de serviço que eles estão sendo usados com.

    O **WebApplicationReplyUrl** é o ponto de extremidade padrão que AAD retorna aos usuários depois de concluir o processo de entrada. Você deve configurar isso para o ponto de extremidade do serviço tecidos Explorer para seu cluster, que, por padrão, é:

    https://&lt;cluster_domain&gt;: 19080/Explorer

    Você será solicitado a entrar em uma conta que tenha privilégios administrativos do locatário AAD. Depois de fazer, o script prossegue para criar a web e aplicativos nativos para representar seu cluster de estrutura de serviço. Se você olhar aplicativos do locatário no [portal clássico Azure][azure-classic-portal], você verá duas novas entradas:

    - *ClusterName*\_Cluster
    - *ClusterName*\_cliente

    As impressões de script o Json exigido pelo modelo de Gerenciador de recursos do Azure, quando você cria o cluster na próxima seção, portanto, mantenha o PowerShell janela aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Criar um modelo do Gerenciador de recursos de cluster tecidos de serviço

Nesta seção, a saída dos comandos do PowerShell anteriores são usados em um modelo do Gerenciador de recursos de cluster tecidos de serviço.

Modelos do Gerenciador de recursos de amostra estão disponíveis na [Galeria de modelos de início rápido Azure no GitHub][azure-quickstart-templates]. Esses modelos podem ser usados como ponto de partida para o seu modelo de cluster. 

### <a name="create-the-resource-manager-template"></a>Criar o modelo do Gerenciador de recursos

Este guia usa o [cluster de 5 nós seguro] [ service-fabric-secure-cluster-5-node-1-nodetype-wad] parâmetros de modelo e modelo de exemplo. Baixar `azuredeploy.json` e `azuredeploy.parameters.json` no seu computador e abra ambos os arquivos em seu editor de texto favorito.

### <a name="add-certificates"></a>Adicionar certificados

Certificados são adicionados a um modelo do Gerenciador de recursos de cluster referenciando no cofre de chave que contém as chaves de certificado. É recomendável que esses valores de chave cofre são colocados em um arquivo de parâmetros de modelo do Gerenciador de recursos para manter o Gerenciador de recursos reutilizável de arquivo de modelo e isenta de valores específicos para uma implantação.

#### <a name="add-all-certificates-to-the-vmss-osprofile"></a>Adicionar todos os certificados para o osProfile VMSS

Todos os certificados que precisam estar instalado no cluster devem ser configurados na seção osProfile do recurso VMSS (Microsoft.Compute/virtualMachineScaleSets). Isso instrui o provedor de recursos para instalar o certificado nas VMs. Isso inclui o certificado de cluster bem como qualquer certificados de segurança de aplicativo que você planeja usar para seus aplicativos:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-service-fabric-cluster-certificate"></a>Configurar um certificado cluster tecidos de serviço

O certificado de autenticação de cluster também deve ser configurado no recurso de cluster de estrutura de serviço (Microsoft.ServiceFabric/clusters) e a extensão de serviço tecidos para VMSS no recurso VMSS. Isso permite que o provedor de recursos de serviço tecidos configurá-lo para uso para autenticação de servidor para pontos de extremidade de gerenciamento e autenticação de cluster.

##### <a name="vmss-resource"></a>Recurso VMSS:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>Recurso de estrutura de serviço:

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-aad-config"></a>Inserir AAD config

A configuração de AAD criada anteriormente pode ser inserida diretamente em seu modelo de Gerenciador de recursos, no entanto recomenda-se para extrair os valores em parâmetros primeiro em um arquivo de parâmetros para manter o modelo do Gerenciador de recursos reutilizáveis e gratuito de valores específicos para uma implantação.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### < um "Configurar braço" ></a>parâmetros de modelo de configurar o Gerenciador de recursos

Por fim, use os valores de saída dos comandos chave cofre e AAD PowerShell para preencher o arquivo de parâmetros:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
Neste ponto, agora você deve ter o seguinte:

 - Grupo de recursos do Cofre de chave
    - Chave cofre
    - Certificado de autenticação de servidor de cluster
    - Certificado de codificação de dados
 - Locatário do Azure Active Directory 
    - Aplicativo de AAD para gerenciamento baseado na web e serviço tecidos Explorer
    - Aplicativo de AAD para gerenciamento de cliente nativo
    - Usuários com funções atribuídas 
 - Modelo do serviço tecidos cluster Gerenciador de recursos
    - Certificados configurados por meio de chave cofre
    - Azure Active Directory configurado 

O diagrama a seguir ilustra onde configuração chave cofre e AAD se encaixa no seu modelo de Gerenciador de recursos.

![Mapa de dependência do Gerenciador de recursos][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Criar o cluster

Agora você está pronto para criar o cluster usando a [implantação do BRAÇO][resource-group-template-deploy].

#### <a name="test-it"></a>Teste-

Use o seguinte comando do PowerShell para testar seu modelo do Gerenciador de recursos com um arquivo de parâmetros:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Implantá-lo

Se o teste de modelo do Gerenciador de recursos passar, use o seguinte comando do PowerShell para implantar o modelo do Gerenciador de recursos com um arquivo de parâmetros:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>
## <a name="assign-users-to-roles"></a>Atribuir usuários a funções

Quando você tiver criado os aplicativos para representar seu cluster, você precisa atribuir usuários às funções suportadas pelo serviço tecidos: somente leitura e administrador. Você pode fazer isso usando o [portal de clássico Azure][azure-classic-portal].

1. Navegue até seu locatário e escolha aplicativos.
2. Escolha o aplicativo web, que possui um nome como `myTestCluster_Cluster`.
3. Clique na guia usuários.
4. Escolha um usuário para atribuir e clique no botão **atribuir** na parte inferior da tela.

    ![Atribuir aos usuários para o botão de funções][assign-users-to-roles-button]

5. Selecione a função para atribuir ao usuário.

    ![Atribuir usuários a funções][assign-users-to-roles-dialog]

>[AZURE.NOTE] Para obter mais informações sobre funções em estrutura de serviço, consulte [controle de acesso baseado em função para clientes de serviço tecidos](service-fabric-cluster-security-roles.md).

 <a name="secure-linux-cluster"></a> 
##  <a name="create-secure-clusters-on-linux"></a>Criar clusters seguros no Linux

Para facilitar o processo, um script de auxiliar foi fornecido [aqui](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Para usar esse script auxiliar, será considerado que você já tiver CLI Azure instalado e é no seu caminho. Certifique-se de que o script tem permissões para executar executando `chmod +x cert_helper.py` após fazer o download. A primeira etapa é fazer logon para sua conta do Azure usando CLI com o `azure login` comando. Depois de log para sua conta do Azure, use o auxiliar com seu certificado auto-assinado da CA, como mostra o comando a seguir:

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]

The -ifile parameter can take a .pfx or a .pem file as input, with the certificate type (pfx or pem, or ss if it is a self-signed cert).
The parameter -h prints out the help text.
```

Este comando retorna as três cadeias de caracteres a seguintes como a saída: 

1. Um SourceVaultID, que é a identificação para o novo KeyVault ResourceGroup-criado para você. 

2. Um CertificateUrl para acessar o certificado.

3. Um CertificateThumbprint, que é usado para autenticação.


O exemplo a seguir mostra como usar o comando:

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
Executar o comando anterior fornece as três cadeias de caracteres da seguinte maneira:

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

 Nome de assunto do certificado deve corresponder o domínio usado para acessar o cluster tecidos de serviço. Isso é necessário para fornecer SSL para pontos de extremidade de gerenciamento de HTTPS e o Explorador de estrutura de serviço do cluster. Você não pode obter um certificado SSL de uma autoridade de certificação (CA) para o `.cloudapp.azure.com` domínio. Você deve adquirir um nome de domínio personalizado para seu cluster. Quando você solicita um certificado de uma autoridade de certificação nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para o seu cluster.

Estas são as entradas necessárias para criar um cluster de estrutura de serviço seguro (sem AAD), conforme descrito em [Gerenciador de recursos de configurar parâmetros de modelo](#configure-arm). Você pode se conectar ao cluster seguro por meio de instruções em [autenticar o acesso do cliente para um cluster](service-fabric-connect-to-secure-cluster.md). Clusters de visualização de Linux não suportam autenticação AAD. Você pode atribuir funções de administrador e cliente conforme descrito na seção [atribuir funções aos usuários](#assign-roles). Ao especificar funções de administrador e de cliente para um cluster de visualização do Linux, você precisa fornecer impressões digitais de certificado para autenticação (em vez de nome de assunto, desde que nenhuma validação de corrente ou revogação está sendo executada nesta versão de visualização).


Se você quiser usar um certificado autoassinado para teste, você pode usar o mesmo script para gerar um certificado auto-assinado e carregue-o para KeyVault, fornecendo o sinalizador `ss` em vez de fornecer o caminho do certificado e o nome de certificado. Por exemplo, consulte o seguinte comando para criar e carregar um certificado auto-assinado:

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net" 
```

Este comando retorna as mesmas três cadeias de caracteres, SourceVault, CertificateUrl e CertificateThumbprint, que é usada para criar um cluster de Linux seguro, juntamente com o local onde o certificado auto-assinado foi colocado. Você precisará o certificado auto-assinado para se conectar ao cluster.  Você pode se conectar ao cluster seguro por meio de instruções em [autenticar o acesso do cliente para um cluster](service-fabric-connect-to-secure-cluster.md). Nome de assunto do certificado deve corresponder o domínio usado para acessar o cluster tecidos de serviço. Isso é necessário para fornecer SSL para pontos de extremidade de gerenciamento de HTTPS e o Explorador de estrutura de serviço do cluster. Você não pode obter um certificado SSL de uma autoridade de certificação (CA) para o `.cloudapp.azure.com` domínio. Você deve adquirir um nome de domínio personalizado para seu cluster. Quando você solicita um certificado de uma autoridade de certificação nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para o seu cluster.

Os parâmetros fornecidos pelo script auxiliar podem ser preenchidos no portal do, conforme descrito na seção [criar um cluster no portal do Azure](service-fabric-cluster-creation-via-portal.md#create-cluster-portal).

## <a name="next-steps"></a>Próximas etapas

Neste ponto, você tem um cluster seguro com autenticação do Active Directory do Azure fornecendo gerenciamento. Próximo, [conectar-se ao cluster](service-fabric-connect-to-secure-cluster.md) e saiba como [Gerenciar segredos do aplicativo](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Solucionar problemas de configuração do Azure Active Directory para autenticação de cliente

Se você enfrentar um problema ao configurar o Active Directory do Azure para autenticação de cliente, examine as seguintes suggestings para obter soluções possíveis.

### <a name="service-fabric-explorer-prompts-for-selecting-certificate"></a>Solicitações de serviço tecidos Explorer para selecionar o certificado

#### <a name="problem"></a>Problema

Após o login com êxito na página de login AAD no serviço tecidos Explorer, o navegador retorna à home page, mas solicita uma caixa de diálogo para selecionar um certificado.

![Caixa de diálogo de selecionar certificado SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motivo

O usuário não foi atribuído a uma função no aplicativo de cluster AAD. Portanto, a autenticação de AAD falhará em cluster de estrutura de serviço. Serviço tecidos Explorer volta para a autenticação de certificado.

#### <a name="solution"></a>Solução

Siga as instruções de configuração de AAD e atribuir funções de usuário. Além disso, "Usuário ATRIBUIÇÃO necessários ao aplicativo do ACCESS" é recomendado para ser ativado como `SetupApplications.ps1` faz.

### <a name="connect-with-powershell-fails-with-error-the-specified-credentials-are-invalid"></a>Conectar-se com o PowerShell falhar com o erro: as credenciais especificadas são inválidas

#### <a name="problem"></a>Problema

Quando usar o PowerShell para se conectar ao cluster usando o modo de segurança de "AzureActiveDirectory", após o login com êxito na página de login AAD, falha na conexão com erro: as credenciais especificadas são inválidas mostrado.

#### <a name="solution"></a>Solução

Mesmo que acima.

### <a name="service-fabric-explorer-signing-in-return-failure-aadsts50011"></a>Serviço tecidos Explorer entrando em retorno falha: AADSTS50011

#### <a name="problem"></a>Problema

Após o login na entrada AAD página no serviço tecidos Explorer, página retorna o sinal em falha - AADSTS50011: O endereço de resposta &lt;url&gt; não coincidir com os endereços de resposta configurados para o aplicativo: &lt;guid&gt;. 

![Endereço de resposta SFX não correspondem][sfx-reply-address-not-match]

#### <a name="reason"></a>Motivo

O aplicativo de cluster(web) que representa o serviço tecidos Explorer tenta autenticar AAD, como parte da solicitação ele fornece a URL de retorno de redirecionamento. Mas ele não está listado na lista AAD aplicativo 'URL de resposta'.

#### <a name="solution"></a>Solução

Adicionar a url do serviço tecidos Explorer 'URL de resposta' na guia Configurar do aplicativo de cluster(web) ou substituir um dos itens na lista. Salve.

![Url de resposta do aplicativo Web][web-application-reply-url]

### <a name="can-i-reuse-the-same-aad-tenant-for-multiple-clusters"></a>Posso reutilizar o mesmo locatário AAD para vários clusters?

#### <a name="answer"></a>Resposta

Sim. Mas lembre-se de adicionar a URL do serviço tecidos Explorer ao seu aplicativo de cluster(web) que Explorer de tecidos serviço caso contrário, não funcionará.

### <a name="why-do-i-still-need-server-certificate-while-aad-enabled"></a>Por que eu ainda preciso certificado servidor enquanto AAD habilitada?

#### <a name="answer"></a>Resposta

FabricClient e FabricGateway executar autenticação comum. No caso de autenticação de AAD, a integração de AAD fornece a identidade do cliente ao servidor e o certificado de servidor é usada para verificar a identidade do servidor. Para obter mais informações sobre o funcionamento do certificado em estrutura de serviço, verificar [certificados x. 509 e estrutura de serviço][x509-certificates-and-service-fabric]

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png