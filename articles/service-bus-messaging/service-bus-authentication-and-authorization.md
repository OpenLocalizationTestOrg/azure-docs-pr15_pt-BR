<properties 
    pageTitle="Autenticação de barramento e a autorização de serviço | Microsoft Azure"
    description="Visão geral de autenticação de assinatura de acesso compartilhado (SAS)."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="service-bus-authentication-and-authorization"></a>Autorização e autenticação de barramento de serviço

Aplicativos podem autenticar barramento de serviço do Azure usando qualquer autenticação de assinatura de acesso compartilhado (SAS) ou por meio do Azure Active Directory controle de acesso (também conhecido como o serviço de controle de acesso ou ACS). Compartilhado acesso assinatura autenticação permite que os aplicativos autenticar barramento de serviço usando uma tecla de acesso configurada no namespace ou na entidade com a qual direitos específicos estão associados. Você pode usar essa chave para gerar um token de assinatura de acesso compartilhado que os clientes podem usar para autenticar barramento de serviço.

>AZURE. SAS importante é recomendável ACS, pois fornece um esquema de autenticação simples, flexível e fácil de usar para barramento de serviço. Aplicativos podem usar em cenários em que eles não precisa gerenciar a noção de um "usuário" autorizado.

## <a name="shared-access-signature-authentication"></a>Autenticação de acesso assinatura compartilhada

[Autenticação de SAS](service-bus-sas-overview.md) permite conceder a um usuário acesso a recursos de barramento de serviço com direitos específicos. Autenticação de SAS no serviço carregador envolve a configuração de uma chave de criptografia com direitos associados em um recurso de barramento de serviço. Clientes podem obter acesso a esse recurso apresentando um token SAS que consiste o URI sendo acessado do recurso e uma expiração assinados com a chave configurada.

Você pode configurar as chaves Associações de em um namespace barramento de serviço. A chave se aplica a todas as mensagens entidades no namespace. Você também pode configurar chaves em tópicos e filas de barramento de serviço. Também há suporte para SAS no barramento de serviço retransmite.

Para usar, você pode configurar um objeto de [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) em um namespace, fila ou tópico que consiste no seguinte:

- *Nome chave* que identifica a regra.

- *PrimaryKey* é uma chave de criptografia usada para entrada/validar tokens SAS.

- *Chave secundária* é uma chave de criptografia usada para entrada/validar tokens SAS.

- *Direitos* que representa a coleção de ouvir, enviar ou gerenciar direitos.

Regras de autorização configuradas no nível do namespace podem conceder acesso a todas as entidades em um namespace para clientes com tokens assinados usando a chave correspondente. Até 12 tal autorização regras podem ser configuradas em um namespace barramento de serviço, fila ou tópico. Por padrão, um [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) com todos os direitos é configurada para cada namespace quando ela é provisionada primeiro.

Para acessar uma entidade, o cliente requer um token SAS gerado usando um específico [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). O token SAS é gerado usando o HMAC-SHA256 de uma cadeia de caracteres do recurso que consiste o URI do recurso ao qual o acesso seja solicitado e uma expiração com uma chave de criptografia associada à regra de autorização.

Suporte de autenticação de SAS para barramento de serviço está incluído nas versões Azure .NET SDK 2.0 e versões posteriores. SAS inclui suporte para um [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). Todas as APIs que aceitam uma cadeia de conexão como um parâmetro incluem suporte para cadeias de caracteres de conexão SAS.

## <a name="acs-authentication"></a>Autenticação ACS

Autenticação de barramento de serviço por meio do ACS é gerenciada por meio de um complemento "-sb" namespace do ACS. Se você quiser um namespace do ACS companion seja criado por um namespace barramento de serviço, não é possível criar seu namespace barramento de serviço no portal Azure clássico; Você deve criar o namespace usando o cmdlet do PowerShell [AzureSBNamespace de novo](https://msdn.microsoft.com/library/azure/dn495165.aspx) . Por exemplo:

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

Para evitar a criação de um namespace ACS, execute o seguinte comando:

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

Por exemplo, se você criar um namespace de barramento de serviço chamado **contoso.servicebus.windows.net**, um namespace do ACS companion chamado **contoso-sb.accesscontrol.windows.net** é provisionado automaticamente. Para todos os namespaces que foram criados antes de agosto de 2014, um namespace ACS acompanha também foi criado.

Uma identidade de serviço padrão "proprietário", com todos os direitos, está provisionada por padrão neste namespace do ACS companion. Você pode obter controle refinado para qualquer entidade de barramento de serviço por meio do ACS Configurando as relações de confiança apropriado. Você pode configurar identidades de serviço adicional para gerenciar o acesso a entidades de barramento de serviço.

Para acessar uma entidade, o cliente solicita um token SWT ACS com as declarações apropriadas apresentando suas credenciais. O token SWT, em seguida, deve ser enviado como parte da solicitação de serviço barramento para habilitar a autorização do cliente para acessar a entidade.

Suporte para barramento de serviço de autenticação de ACS é incluída nas versões Azure .NET SDK 2.0 e versões posteriores. Essa autenticação inclui suporte para um [SharedSecretTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedsecrettokenprovider.aspx). Todas as APIs que aceitam uma cadeia de conexão como um parâmetro incluem suporte para cadeias de caracteres de conexão de ACS.

## <a name="next-steps"></a>Próximas etapas

Continue a leitura de [autenticação de assinatura de acesso compartilhado com barramento de serviço](service-bus-shared-access-signature-authentication.md) para obter mais detalhes sobre SAS.

Para obter uma visão geral de Associações no carregador de serviço de, consulte [Assinaturas de acesso compartilhado](service-bus-sas-overview.md).

Você pode encontrar mais informações sobre tokens de ACS no [como: solicitar um Token do ACS através do protocolo de QUEBRAR OAuth](https://msdn.microsoft.com/library/hh674475.aspx).



