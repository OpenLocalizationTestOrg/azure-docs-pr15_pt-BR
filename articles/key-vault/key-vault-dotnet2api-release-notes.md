<properties
   pageTitle="Notas de versão do cofre API de 2. x do .NET de chave | Microsoft Azure"
   description="Desenvolvedores .NET usará esse API código para Cofre de chave do Azure"
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="CSharp"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/07/2016"
   ms.author="bruceper" />

# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Compartimento de chave Azure .NET 2.0 - notas de versão e guia de migração

As anotações e orientações a seguir são para os desenvolvedores trabalhando com o .NET de Cofre de chave do Azure / biblioteca c#. Na alteração da 1.0 versão para a versão 2.0, um número de atualizações foram feito que exigem o trabalho de migração no seu código em ordem para beneficiar os aperfeiçoamentos funcionais e recurso adições como suporte de certificados de chave cofre.

O suporte de certificados de chave cofre fornece para o gerenciamento de seu x509 certificados e comportamentos a seguir:  

-   Permite que um proprietário de certificado criar um certificado por meio de um processo de criação de chave cofre ou a importação de um certificado existente. Isso inclui ambos auto-assinado e autoridade de certificação gerados certificados.

- Permite que um proprietário de certificado de chave cofre implementar um armazenamento seguro e o gerenciamento de X509 certificados sem interação com material de chave particular.  

-   Permite que um proprietário de certificado criar uma política que direciona Cofre de chave para gerenciar o ciclo de vida de um certificado.  

-   Permite que os proprietários de certificado fornecer informações de contato de notificação sobre eventos de ciclo de vida de validade e renovação de certificado.  

-   Suporta a renovação automática com emissores selecionados - chave cofre parceiro X509 provedores de certificado / autoridades de certificação.
    - Observação - não parceria provedores/autoridades também são permitidas, mas, não oferecerá suporte para o recurso de renovação automática.


## <a name="net-support"></a>Suporte .NET
- **.NET 4.0** não é compatível com a versão 2.0 do .NET Azure chave cofre / biblioteca c#
- **.NET Core** é compatível com a versão 2.0 do .NET Azure chave cofre / biblioteca c#

## <a name="namespaces"></a>Namespaces
- Namespace para **modelos** é alterado de **Microsoft.Azure.KeyVault** para **Microsoft.Azure.KeyVault.Models**.
- Namespace **Microsoft.Azure.KeyVault.Internal** será ignorado.
- Namespace de dependências SDK Azure são alterados de **Hyak.Common** e **Hyak.Common.Internals** como **Microsoft.Rest** e **Microsoft.Rest.Serialization**


## <a name="type-changes"></a>Alterações de tipo
- *Secreta* é alterado para *SecretBundle*
- *Dicionário* alterado para *IDictionary*
- *Lista<T>, [] de cadeia de caracteres* alterado para *IList<T> *
- *NextList* alterado para *NextPageLink*


## <a name="return-types"></a>Tipos de retorno
- **KeyList** e **SecretList** retornará *IPage<T> * em vez de *ListKeysResponseMessage*
- O gerado **BackupKeyAsync** retornará *BackupKeyResult* que contém o *valor de* (backup blob). Antes do método foi quebrado e retornar somente o valor.

## <a name="exceptions"></a>Exceções
- *KeyVaultClientException* é alterado para *KeyVaultErrorException*
- O erro de serviço é alterado de exceção de *. Erro* a exceção de *. Body.Error.Message*.
- Removido informações adicionais da mensagem de erro para **[JsonExtensionData]**.

## <a name="constructors"></a>Construtores
- Em vez de aceitar um *HttpClient* como um argumento de construtor, o construtor só aceita *HttpClientHandler* ou *[DelegatingHandler]*.



## <a name="downloaded-packages"></a>Pacotes baixados  
Quando um cliente está processando uma dependência em Cofre de chave a seguir foram baixada
#### <a name="previous-package-list"></a>Lista de pacote anterior
- versão de pacote id="Hyak.Common" = "1.0.2" targetFramework = "net45"
- versão de pacote id="Microsoft.Azure.Common" = "2.0.4" targetFramework = "net45"
- versão de pacote id="Microsoft.Azure.Common.Dependencies" = "1.0.0" targetFramework = "net45"
- versão de pacote id="Microsoft.Azure.KeyVault" = "1.0.0" targetFramework = "net45"
- versão de pacote id="Microsoft.Bcl" = "1.1.9" targetFramework = "net45"
- versão de pacote id="Microsoft.Bcl.Async" = "1.0.168" targetFramework = "net45"
- versão de pacote id="Microsoft.Bcl.Build" = "1.0.14" targetFramework = "net45"
- versão de pacote id="Microsoft.Net.Http" = "2.2.22" targetFramework = "net45"

#### <a name="current-package-list"></a>Lista de pacote atual
- versão de pacote id="Microsoft.Azure.KeyVault" = "2.0.0-preview" targetFramework = "net45"
- versão de pacote id="Microsoft.Rest.ClientRuntime" = "2.2.0" targetFramework = "net45"
- versão de pacote id="Microsoft.Rest.ClientRuntime.Azure" = "3.2.0" targetFramework = "net45"


## <a name="class-changes"></a>Alterações de classe

- **UnixEpoch** classe foi removido
- Classe **Base64UrlConverter** é renomeado para **Base64UrlJsonConverter**

## <a name="other-changes"></a>Outras alterações

- Suporte para a configuração de política de repetição de operação KV sobre falhas temporárias foi adicionado para esta versão da API.



## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet
- Para as operações que retornou um *cofre*, o tipo de retorno foi uma classe que continha uma propriedade de cofre. O tipo de retorno agora é *cofre*.
- *PermissionsToKeys* e *PermissionsToSecrets* são agora *Permissions.Keys* e *Permissions.Secrets*
- Algumas das alterações tipos de retorno se aplicam ao plano de controle também.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet
- O pacote é quebrado **Microsoft.Azure.KeyVault.Extensions** e **Microsoft.Azure.KeyVault.Cryptography** para as operações de criptografia.
