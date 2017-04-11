<properties
   pageTitle="Conectar a um cluster particular seguro | Microsoft Azure"
   description="Este artigo descreve como proteger a comunicação dentro do autônomo ou cluster particular também entre clientes e o cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/08/2016"
   ms.author="dkshir"/>

# <a name="secure-a-standalone-cluster-on-windows-using-x509-certificates"></a>Proteger um cluster de autônomo no Windows usando certificados x. 509

Este artigo descreve como proteger a comunicação entre os vários nós do cluster Windows autônomo, bem como para autenticar clientes que se conectam a esse cluster, usando o x. 509 certificados. Isso garante que somente os usuários autorizados podem acessar o cluster, os aplicativos implantados e executar tarefas de gerenciamento.  Segurança de certificado deve ser habilitada no cluster quando o cluster é criado.  

Para obter mais informações sobre segurança de cluster como nós para segurança, segurança de nó do cliente e controle de acesso baseado em função, consulte [cenários de segurança de Cluster](service-fabric-cluster-security.md).

## <a name="which-certificates-will-you-need"></a>Os certificados que você precisará?

Para começar, [Baixe o pacote de cluster standalone](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) para um de nós no seu cluster. O pacote baixado, você encontrará um arquivo de **ClusterConfig.X509.MultiMachine.json** . Abra o arquivo e examine a seção de **segurança** na seção **Propriedades** :

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
            "ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
            "HttpApplicationGatewayCertificate":{
                "Thumbprint": "[Thumbprint]",
                "X509StoreName": "My"
            }
        }
    }

Esta seção descreve os certificados que você precisa para proteger o seu cluster autônoma do Windows. Para ativar a segurança baseada em certificado defina os valores de **ClusterCredentialType** e **ServerCredentialType** como *X509*.

>[AZURE.NOTE] Uma [impressão digital](https://en.wikipedia.org/wiki/Public_key_fingerprint) é a identidade principal de um certificado. Leia [como recuperar a impressão digital de um certificado](https://msdn.microsoft.com/library/ms734695.aspx) para descobrir a impressão digital dos certificados que você criar.

A tabela a seguir lista os certificados que você precisará na sua configuração de cluster:

|**Configuração de CertificateInformation**|**Descrição**|
|-----------------------|--------------------------|
|ClusterCertificate|Este certificado é necessário para proteger a comunicação entre os nós em um cluster. Você pode usar dois certificados diferentes, um principal e um secundário para atualização. Defina a impressão digital do certificado primário na seção de **impressão digital** e que do secundário nas variáveis **ThumbprintSecondary** .|
|ServerCertificate|Este certificado é apresentado para o cliente quando ele tenta se conectar a esse cluster. Para sua conveniência, você pode optar por usar o mesmo certificado para *ClusterCertificate* e *ServerCertificate*. Você pode usar dois certificados de servidor diferente, um principal e um secundário para atualização. Defina a impressão digital do certificado primário na seção de **impressão digital** e que do secundário nas variáveis **ThumbprintSecondary** . |
|ClientCertificateThumbprints|Este é um conjunto de certificados que você deseja instalar nos clientes autenticados. Você pode ter um número diferente de certificados de cliente instalados nas máquinas que você deseja permitir acesso ao cluster. Defina a impressão digital do cada certificado na variável **CertificateThumbprint** . Se você definir o **IsAdmin** como *true*, então o cliente com este certificado instalado pode fazer administrador atividades de gerenciamento no cluster. Se o **IsAdmin** for *Falso*, o cliente com este certificado somente pode executar as ações permitidas para direitos de acesso do usuário, normalmente somente leitura. Para obter mais informações sobre funções leia [(RBAC) de controle de acesso baseado em função](service-fabric-cluster-security.md/#role-based-access-control-rbac)  |
|ClientCertificateCommonNames|Configurar o nome comum do primeiro certificado de cliente para o **CertificateCommonName**. O **CertificateIssuerThumbprint** é a impressão digital para o emissor deste certificado. Leia [Trabalhando com certificados](https://msdn.microsoft.com/library/ms731899.aspx) para saber mais sobre nomes comuns e o emissor.|
|HttpApplicationGatewayCertificate|Este é um certificado opcional que pode ser especificado se você deseja proteger seu Gateway de aplicativo Http. Certifique-se de reverseProxyEndpointPort é definida em nodeTypes se você estiver usando este certificado.|

Aqui está o exemplo de configuração de cluster onde os certificados de cliente, servidor e Cluster foram fornecidos.

 ```
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
      "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
      "iPAddress": "10.7.0.6",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        }
        "security": {
            "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ServerCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpoint": "19001",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="aquire-the-x509-certificates"></a>Adquirir o x. 509 certificados
Para proteger a comunicação dentro do cluster, você precisará primeiro obter certificados x. 509 para os nós de cluster. Além disso, para limitar a conexão a esse cluster máquinas/usuários autorizados, você precisará obter e instalar certificados para os computadores cliente.

Para clusters que executam cargas de trabalho de produção, você deve usar uma [Autoridade de certificação (CA)](https://en.wikipedia.org/wiki/Certificate_authority) assinado certificado x. 509 para proteger o cluster. Para obter detalhes sobre como obter esses certificados, vá para [como: obter um certificado](http://msdn.microsoft.com/library/aa702761.aspx).

Para clusters que você usa para fins de teste, você pode optar por usar um certificado autoassinado.

## <a name="optional-create-a-self-signed-certificate"></a>Opcional: Crie um certificado autoassinado
Uma maneira de criar um certificado auto-assinado que pode ser protegido corretamente é usar o script *CertSetup.ps1* na pasta serviço tecidos SDK no diretório *C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure*. Edite o arquivo e use essa opção para criar um certificado com um nome adequado.

Agora, exporte o certificado para um arquivo PFX com uma senha protegida. Primeiro você precisa obter a impressão digital do certificado. Execute o aplicativo certmgr.exe. Navegue até a pasta **Local\Pessoal.** e localize o certificado que você acabou de criar. Clique duas vezes no certificado para abri-lo, selecione a guia *detalhes* e role para baixo até o campo *impressão digital* . Copie o valor de impressão digital para o comando do PowerShell abaixo, removendo os espaços.  Altere o valor de *$pswd* para uma senha de segurança adequado ou protegê-la e executar o PowerShell:

```   
$pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
```

Para ver os detalhes de um certificado instalado na máquina, você pode executar o seguinte comando do PowerShell:

```
$cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
Write-Host $cert.ToString($true)
```

Como alternativa, se você tiver uma assinatura do Azure, siga a seção [Adicionar certificados chave cofre](service-fabric-cluster-creation-via-arm.md#add-certificate-to-key-vault).

## <a name="install-the-certificates"></a>Instalar os certificados
Quando você tiver o certificado, você pode instalá-los em nós do cluster. Os nós precisam ter o Windows PowerShell mais recente 3. x instalado neles. Você precisará repetir essas etapas em cada nó, para Cluster e servidor de certificados e qualquer secundário.

1. Copie os arquivos. pfx para o nó.
2. Abra uma janela do PowerShell como administrador e digite os seguintes comandos. Substitua o *$pswd* com a senha que você usou para criar este certificado. Substitua o caminho completo da. pfx copiados para este nó a *$PfxFilePath* .

    ```
    $pswd = "1234"
    $PfcFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```

3. Em seguida, você precisa configurar o controle de acesso neste certificado para que o processo de serviço tecidos, que é executado sob a conta de serviço de rede, pode usá-lo executando o script a seguir. Fornece a impressão digital do certificado e "Serviço de rede" para a conta de serviço. Você pode verificar se as ACLs no certificado estão corretas usando a ferramenta de certmgr.exe e examinando as chaves particulares gerenciar no certificado.

    ```
    param
    (
        [Parameter(Position=1, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$pfxThumbPrint,

        [Parameter(Position=2, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$serviceAccount
        )

        $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; };

        # Specify the user, the permissions and the permission type
        $permission = "$($serviceAccount)","FullControl","Allow"
        $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission;

        # Location of the machine related keys
        $keyPath = $env:ProgramData + "\Microsoft\Crypto\RSA\MachineKeys\";
        $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName;
        $keyFullPath = $keyPath + $keyName;

        # Get the current acl of the private key
        $acl = (Get-Item $keyFullPath).GetAccessControl('Access')

        # Add the new ace to the acl of the private key
        $acl.SetAccessRule($accessRule);

        # Write back the new acl
        Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop

        #Observe the access rights currently assigned to this certificate.
        get-acl $keyFullPath| fl
        ```

4. Repeat the steps above for each server certificate. You can also use these steps to install the client certificates on the machines that you want to allow access to the cluster.

## Create the secure cluster
After configuring the **security** section of the **ClusterConfig.X509.MultiMachine.json** file, you can proceed to [Create your cluster](service-fabric-cluster-creation-for-windows-server.md#createcluster) section to configure the nodes and create the standalone cluster. Remember to use the **ClusterConfig.X509.MultiMachine.json** file while creating the cluster. For example, your command might look like the following:

```
.\CreateServiceFabricCluster.ps1 - ClusterConfigFilePath.\ClusterConfig.X509.MultiMachine.json - MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab - AcceptEULA $true
```

Once you have the secure standalone Windows cluster successfully running, and have setup the authenticated clients to connect to it, follow the section [Connect to a secure cluster using PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) to connect to it. For example:

```
Conectar-ServiceFabricCluster - ConnectionEndpoint 10.7.0.4:19000 - KeepAliveIntervalInSec 10 - X509Credential - ServerCertThumbprint 057b9544a6f2733e0c8d3a60013a58948213f551 - FindType FindByThumbprint - FindValue 057b9544a6f2733e0c8d3a60013a58948213f551 - StoreLocation CurrentUser - StoreName meu
```

If you are logged on to one of the machines in the cluster, since this already has the certificate installed locally you can simply run the Powershell command to connect to the cluster and show a list of nodes:

```
Conectar-ServiceFabricCluster Get-ServiceFabricNode
```
To remove the cluster call the following command:

```
.\RemoveServiceFabricCluster.ps1 - ClusterConfigFilePath.\ClusterConfig.X509.MultiMachine.json - MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab
```
