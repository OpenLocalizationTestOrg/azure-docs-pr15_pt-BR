<properties
   pageTitle="Proteger um cluster de serviço tecidos | Microsoft Azure"
   description="Descreve os cenários de segurança para um cluster de estrutura de serviço e as tecnologias diferentes usadas para implementar esses cenários."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/19/2016"
   ms.author="chackdan"/>

# <a name="service-fabric-cluster-security-scenarios"></a>Cenários de segurança de cluster tecidos de serviço

Um cluster de estrutura de serviço é um recurso que você possui. Clusters sempre devem ser protegidos para impedir que usuários não autorizados se conectando ao seu cluster, especialmente quando ela tem cargas de trabalho de produção em execução. Embora seja possível criar um cluster desprotegido, fazendo permitirá que qualquer usuário anônimo conectar-se a ele se ele expõe pontos de extremidade de gerenciamento à Internet pública. 

Este artigo fornece uma visão geral dos cenários de segurança para clusters em execução no Azure ou autônomo e das diversas tecnologias usadas para implementar esses cenários. Os cenários de segurança de cluster são:

- Segurança para nós
- Segurança de nó do cliente
- Controle de acesso baseado em função (RBAC)

## <a name="node-to-node-security"></a>Segurança para nós
Protege a comunicação entre VMs ou máquinas no cluster. Isso garante que somente os computadores que estão autorizados a ingressar no cluster podem participar hospedar aplicativos e serviços no cluster.

![Diagrama de comunicação para nós][Node-to-Node]

Clusters em execução no Azure ou autônomo clusters em execução no Windows podem usar o [Certificado de segurança](https://msdn.microsoft.com/library/ff649801.aspx) ou [Segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx) para máquinas do Windows Server.
### <a name="node-to-node-certificate-security"></a>Segurança de certificado para nós
Estrutura de serviço usa certificados de servidor x. 509 especificado como parte das configurações de tipo de nó quando você cria um cluster. Uma rápida visão geral do que esses certificados são e como você pode adquirir ou criá-los é fornecida no final deste artigo.

Certificado de segurança é configurada durante a criação do cluster por meio do portal do Azure, modelos do Gerenciador de recursos do Azure ou um modelo JSON autônomo. Você pode especificar um certificado principal e um certificado secundário opcional que é usado para sobreposições de certificado. Os certificados de primárias e secundários que você especificar devem ser diferentes do que o cliente de administração e certificados de cliente somente leitura especificado para [segurança de nó do cliente](#client-to-node-security).

Para Azure leia [Configurar um cluster usando um modelo do Gerenciador de recursos do Azure](service-fabric-cluster-creation-via-arm.md) para aprender a configurar segurança de certificado em um cluster.

Para autônomo Windows Server leia [proteger um cluster de autônomo no Windows usando certificados x. 509](service-fabric-windows-cluster-x509-security.md)

### <a name="node-to-node-windows-security"></a>Segurança do windows para nós
Para autônomo Windows Server leia [proteger um cluster de autônomo no Windows usando a segurança do Windows](service-fabric-windows-cluster-windows-security.md)

## <a name="client-to-node-security"></a>Segurança de nó do cliente
Autentica clientes e protege a comunicação entre um cliente e nós individuais no cluster. Esse tipo de segurança autentica e protege comunicações de cliente, que garante que somente os usuários autorizados possam acessar o cluster e os aplicativos implantados no cluster. Os clientes são identificados exclusivamente por meio de suas credenciais de segurança do Windows ou suas credenciais de segurança de certificado.

![Diagrama de comunicação de cliente para nó][Client-to-Node]

Clusters em execução no Azure ou autônomo clusters em execução no Windows podem usar o [Certificado de segurança](https://msdn.microsoft.com/library/ff649801.aspx) ou [Segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Segurança de certificado de cliente para nó
 Segurança de certificado de cliente para nó está configurada durante a criação do cluster por meio do portal do Azure, modelos do Gerenciador de recursos ou um modelo JSON autônomo especificando um certificado de cliente do administrador e/ou um certificado de cliente do usuário.  Administração cliente e usuário certificados de cliente que você especifica devem ser diferentes do que os certificados primárias e secundários especificado para [nós para segurança](#node-to-node-security).

Os clientes se conectam ao cluster usando o certificado de administrador têm acesso completo aos recursos de gerenciamento.  Os clientes se conectam ao cluster usando o certificado de cliente do usuário somente leitura têm acesso de leitura aos recursos de gerenciamento. Em outras palavras, esses certificados são usados para o controle de acesso de bases do função (RBAC) descrito mais adiante neste artigo.

Para Azure leia [Configurar um cluster usando um modelo do Gerenciador de recursos do Azure](service-fabric-cluster-creation-via-arm.md) para aprender a configurar segurança de certificado em um cluster.

Para autônomo Windows Server leia [proteger um cluster de autônomo no Windows usando certificados x. 509](service-fabric-windows-cluster-x509-security.md)

### <a name="client-to-node-azure-active-directory-aad-security-on-azure"></a>Segurança do Azure Active Directory (AAD) de nó do cliente no Azure
Clusters em execução no Azure também podem proteger o acesso a pontos de extremidade de gerenciamento usando o Azure Active Directory (AAD). Consulte [Configurar um cluster por usando um modelo do Gerenciador de recursos do Azure](service-fabric-cluster-creation-via-arm.md) para obter informações sobre como criar os artefatos AAD necessários, como preenchê-los durante a criação de cluster e como se conectar a esses grupos posteriormente.

## <a name="security-recommendations"></a>Recomendações de segurança
Para clusters Azure, é recomendável que você use segurança AAD para autenticar clientes e certificados para nós para segurança.

Independente do Windows Server clusters, é recomendável que você usa a segurança do Windows com grupo gerenciado contas (GMA) se você tiver o Windows Server 2012 R2 e do Active Directory. Caso contrário, ainda use a segurança do Windows com contas do Windows.

## <a name="role-based-access-control-rbac"></a>Controle de acesso baseado em função (RBAC)
Controle de acesso permite que o administrador de cluster para limitar o acesso a determinadas operações de cluster para diferentes grupos de usuários, tornando o cluster mais seguro. Dois tipos de controle de acesso diferentes têm suporte para clientes que se conectam a um cluster: funções de administrador e usuário.

Os administradores têm acesso total aos recursos de gerenciamento (incluindo os recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver aplicativos e serviços.

Especificar as funções de administrador e de usuário do cliente no momento da criação do cluster, fornecendo identidades separadas (certificados, AAD etc) para cada. Para obter mais informações sobre as configurações de controle de acesso padrão e como alterar as configurações padrão, consulte [controle de acesso para clientes de serviço tecidos baseado em função](service-fabric-cluster-security-roles.md).


## <a name="x509-certificates-and-service-fabric"></a>Certificados x. 509 e estrutura de serviço
Certificados digitais x. 509 são usados para autenticar clientes e servidores e para criptografar e assinar digitalmente mensagens. Para obter mais detalhes sobre esses certificados, vá para [trabalhar com certificados](http://msdn.microsoft.com/library/ms731899.aspx).

Algumas coisas importantes a considerar:

- Certificados usados em clusters executando cargas de trabalho de produção devem ser criados usando um serviço de certificado do Windows Server configurado corretamente ou obtidos de uma [Autoridade de certificação (CA)](https://en.wikipedia.org/wiki/Certificate_authority)de aprovado.
- Nunca use qualquer temporário ou certificados de teste em produção que são criados com ferramentas como MakeCert.exe.
- Você pode usar um certificado autoassinado, mas só deve fazer isso para clusters de teste e não em produção.

### <a name="server-x509-certificates"></a>Certificados x. 509 de servidor

Certificados de servidor têm a tarefa principal de um servidor (nó) para clientes de autenticação ou autenticar um servidor (nó) a um servidor (nó). Uma das verificações iniciais quando um cliente ou um nó autentica um nó é verificar o valor do nome comum no campo assunto. Esse nome comum ou um dos nomes alternativos de assunto dos certificados deve estar presente na lista de permitidos nomes comuns.

O artigo a seguir descreve como gerar certificados com nomes alternativos de assunto (SAN): [como adicionar um nome alternativo de assunto para um certificado LDAP seguro](http://support.microsoft.com/kb/931351).

O campo assunto pode conter vários valores, cada prefixo com uma inicialização para indicar o tipo de valor. Normalmente, a inicialização é "CN" para o nome comum; Por exemplo, "CN = www.contoso.com". Também é possível para o campo assunto em branco. Se o campo de nome de entidade alternativo opcional é preenchido, ele deve conter o nome comum do certificado e uma entrada por nome alternativo de assunto. Estes são inseridos como valores de nome de DNS.

O valor do campo finalidades do certificado deve incluir um valor apropriado, como "Servidor autenticação" ou "cliente".

### <a name="client-x509-certificates"></a>Certificados x. 509 de cliente

Certificados de cliente geralmente não são emitidos por uma autoridade de certificação de terceiros. Em vez disso, o armazenamento pessoal do local atual usuário geralmente contém certificados de cliente incluídos por uma autoridade raiz, com a finalidade "De autenticação de cliente". O cliente pode usar esses certificados quando autenticação comum é necessária.

>[AZURE.NOTE] Todas as operações de gerenciamento em um cluster de serviço tecidos exigem certificados de servidor. Certificados de cliente não podem ser usados para gerenciamento.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## <a name="next-steps"></a>Próximas etapas

Este artigo fornece informações conceituais sobre segurança do cluster. Próximo, [criar um cluster no Azure usando um modelo do Gerenciador de recursos](service-fabric-cluster-creation-via-arm.md) ou por meio do [portal do Azure](service-fabric-cluster-creation-via-portal.md).

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png
