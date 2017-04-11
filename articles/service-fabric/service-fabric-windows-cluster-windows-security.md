<properties
   pageTitle="Proteger um cluster executando o Windows usando a segurança do Windows | Microsoft Azure"
   description="Saiba como configurar a segurança para nós e cliente para nó em um cluster de autônoma em execução no Windows usando a segurança do Windows."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="secure-a-standalone-cluster-on-windows-using-windows-security"></a>Proteger um cluster de autônomo no Windows usando a segurança do Windows

Para impedir o acesso não autorizado a um cluster de tecidos de serviço, que você deve protegê-lo, especialmente quando ela tem cargas de trabalho de produção em execução. Este artigo descreve como configurar segurança para nós e nó do cliente usando a segurança do Windows no arquivo *ClusterConfig.JSON* e corresponde ao configurar segurança passo de [criar um cluster de autônoma em execução no Windows](service-fabric-cluster-creation-for-windows-server.md). Para obter mais informações sobre como o serviço tecidos usa segurança do Windows, consulte [cenários de segurança de Cluster](service-fabric-cluster-security.md).

>[AZURE.NOTE]
Você deve considerar sua seleção de segurança para segurança para nós cuidadosamente, pois há nenhuma atualização de cluster da opção de segurança para outro. Alterar a seleção de segurança exijam uma recriação cluster completo.

## <a name="configure-windows-security"></a>Configurar a segurança do Windows
O arquivo de configuração de *ClusterConfig.Windows.JSON* de exemplo baixados com a [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. ZIP](http://go.microsoft.com/fwlink/?LinkId=730690) pacote de cluster standalone contém um modelo de configuração de segurança do Windows.  Segurança do Windows é configurada na seção **Propriedades** :

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

|**Configuração**|**Descrição**|
|-----------------------|--------------------------|
|ClusterCredentialType|Segurança do Windows está habilitada, definindo o parâmetro **ClusterCredentialType** para *Windows*.|
|ServerCredentialType|Segurança do Windows para clientes está habilitada, definindo o parâmetro **ServerCredentialType** para *Windows*. Isso indica que os clientes do cluster e o cluster, são executados dentro de um domínio do Active Directory.|
|WindowsIdentities|Contém as identidades cluster e do cliente.|
|ClusterIdentity|Configura a segurança para nós. Uma lista separada por vírgulas de grupo gerenciadas contas de serviço ou nomes de máquina.|
|ClientIdentities|Configura a segurança do cliente para nó. Uma matriz de contas de usuário do cliente.|
|Identidade|A identidade do cliente, um usuário do domínio.|
|IsAdmin|True Especifica que o usuário do domínio tem acesso de cliente do administrador, FALSO para acesso de cliente do usuário.|

[Nó à segurança de nó](service-fabric-cluster-security.md#node-to-node-security) está configurado definindo usando **ClusterIdentity**. Para criar relações de confiança entre nós, eles devem ser feitos conhecimento uns dos outros. Isso pode ser feito de duas maneiras diferentes: especifique o grupo de conta de serviço gerenciada que inclui todos os nós no cluster ou as identidades de nó de domínio de todos os nós no cluster. É altamente recomendável usando a abordagem de [Conta de serviço gerenciada do grupo (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) , especialmente para clusters maiores (mais de 10 nós) ou para clusters que provavelmente aumente ou diminua.
Essa abordagem permite que nós ser adicionado ou removido de gMSA, sem a necessidade de alterações para o manifesto de cluster. Essa abordagem não exige a criação de um grupo de domínio para que os administradores de cluster possui direitos de acesso para adicionar e remover membros. Para obter mais informações, consulte [Introdução ao grupo de contas de serviço gerenciadas](http://technet.microsoft.com/library/jj128431.aspx).

[Cliente para segurança de nó](service-fabric-cluster-security.md#client-to-node-security) é configurada usando **ClientIdentities**. Para estabelecer confiança entre um cliente e o cluster, você deve configurar o cluster para saber qual cliente identidades que ele pode confiar. Isso pode ser feito de duas maneiras diferentes: especifique os usuários do grupo de domínio que podem se conectar ou especificar os usuários de nó do domínio que podem se conectar. Estrutura de serviço oferece suporte a dois tipos de controle de acesso diferente para clientes que estão conectados a um cluster de estrutura de serviço: administrador e usuário. Controle de acesso fornece a capacidade do administrador de cluster limitar o acesso a certos tipos de operações de cluster para diferentes grupos de usuários, tornando o cluster mais segura.  Os administradores têm acesso total aos recursos de gerenciamento (incluindo os recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver aplicativos e serviços.

A seção de **segurança** de exemplo a seguir configura a segurança do Windows e especifica que as máquinas em *ServiceFabric/clusterA.contoso.com* fazem parte do cluster e que *CONTOSO\usera* tenha acesso de cliente do administrador:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
        "IsAdmin": true
        }]
    }
},
```

## <a name="next-steps"></a>Próximas etapas

Depois de configurar a segurança do Windows no arquivo *ClusterConfig.JSON* , retome o processo de criação de cluster em [criar um cluster de autônoma em execução no Windows](service-fabric-cluster-creation-for-windows-server.md).

Para obter mais informações sobre como nós para segurança, segurança de nó do cliente e controle de acesso baseado em função, consulte [cenários de segurança de Cluster](service-fabric-cluster-security.md).

Consulte [conectar a um cluster de seguro](service-fabric-connect-to-secure-cluster.md) para obter exemplos de conectando através do PowerShell ou FabricClient.
