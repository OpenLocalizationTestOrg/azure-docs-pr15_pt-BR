<properties
    pageTitle="Códigos de erro de serviços de mídia Azure | Microsoft Azure"
    description="O tópico fornece uma visão geral dos códigos de erro de serviços de mídia do Azure."
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016" 
    ms.author="juliako"/>

# <a name="azure-media-services-error-codes"></a>Códigos de erro de serviços de mídia Azure

Ao usar os serviços de mídia do Microsoft Azure, você pode receber códigos de erro HTTP do serviço dependendo problemas como tokens de autenticação expirando às ações que não têm suporte nos serviços de mídia. A seguir está uma lista de **códigos de erro HTTP** que podem ser retornados pelos serviços de mídia e as possíveis causas para eles.  
  
## <a name="400-bad-request"></a>400 Solicitação inválida

A solicitação contém informações inválidas e for rejeitada devido a um dos seguintes motivos:

- Uma versão sem suporte do API é especificada. Para a versão mais atual, consulte [instalação para desenvolvimento de API do resto de serviços de mídia](media-services-rest-how-to-use.md).
- A versão da API dos serviços de mídia não está especificada. Para obter informações sobre como especificar a versão da API, consulte [Conectando-se aos serviços de mídia com a API de REST de serviços de mídia](media-services-rest-connect-programmatically.md). 
   
    >[AZURE.NOTE] Se você estiver usando o .NET ou Java SDKs para se conectar aos serviços de mídia, a versão da API é especificada por você sempre que você experimente e realizar alguma ação contra serviços de mídia.
- Uma propriedade indefinida foi especificada. É o nome da propriedade na mensagem de erro. Somente as propriedades que são membros de uma determinada entidade podem ser especificadas. Consulte a [Referência API do restante dos serviços de mídia do Azure](http://msdn.microsoft.com/library/azure/hh973617.aspx) para obter uma lista de entidades e suas propriedades.
- Um valor de propriedade inválido foi especificado. É o nome da propriedade na mensagem de erro. Consulte o link anterior para tipos de propriedade válida e seus valores.
- Um valor da propriedade está ausente e é necessário.
- Parte da URL especificada contém um valor incorreto.
- Houve uma tentativa de atualizar uma propriedade de WriteOnce.
- Houve uma tentativa para criar um trabalho que tem um entrada ativo com uma AssetFile primária que não foi especificada ou não pôde ser determinado.
- Houve uma tentativa de atualizar um localizador de SAS. Locators SAS só podem ser criados ou excluídos. Streaming locators pode ser atualizado. Para obter mais informações, consulte [Locators](http://msdn.microsoft.com/library/azure/hh974308.aspx).
- Uma operação sem suporte ou consulta foi enviada. 

## <a name="401-unauthorized"></a>401 não autorizado

A solicitação não pôde ser autenticada (antes que possam ser autorizado) devido a um dos seguintes motivos:

- Cabeçalho de autenticação ausente.
- Valor de cabeçalho de autenticação inválida.
    - O token expirou. Se usando as APIs REST diretamente, consulte [Conectando-se aos serviços de mídia com a API de REST de serviços de mídia](media-services-rest-connect_programmatically.md) para aprender a gerar um novo token de autenticação. Se você estiver usando o .NET ou Java SDKs, crie um objeto CloudMediaContext ou MediaContract para gerar o token. Para obter mais informações sobre como fazer isso, consulte [Conectando-se aos serviços de mídia com o SDK de serviços de mídia para .NET](media-services-dotnet-connect-programmatically.md).
    - O token contém uma assinatura inválida.</li></ul></li></ul>

## <a name="403-forbidden"></a>403 Proibido

A solicitação não é permitida devido a um dos seguintes motivos:

- A conta de serviços de mídia não pode ser encontrada ou foi excluída.
- A conta de serviços de mídia está desabilitada e o tipo de solicitação não GET HTTP. Operações de serviço retornará uma resposta 403 também.
- O símbolo de autenticação não contém informações de credenciais do usuário: AccountName e/ou SubscriptionId. Você pode encontrar essas informações na extensão de interface de usuário de serviços de mídia para a sua conta de serviços de mídia no Portal de gerenciamento do Azure.
- O recurso não pode ser acessado.
    - Houve uma tentativa para usar um MediaProcessor que não está disponível para sua conta de serviços de mídia.
    - Houve uma tentativa para atualizar um modelo de trabalho definido pelo serviços de mídia.
    - Houve uma tentativa para substituir o localizador de alguns outros serviços de mídia da conta.
    - Houve uma tentativa para substituir ContentKey de alguns outros serviços de mídia da conta.

- O recurso não pôde ser criado devido a uma cota de serviço que foi atingida para a conta de serviços de mídia. Para obter mais informações sobre as cotas de serviço, consulte [cotas e limitações](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 não encontrado

A solicitação não é permitida em um recurso devido a um dos seguintes motivos:

- Houve uma tentativa de atualizar uma entidade que não existe.
- Houve uma tentativa para excluir uma entidade que não existe.
- Houve uma tentativa para criar uma entidade que está vinculado a uma entidade que não existe.
- Houve uma tentativa para obter uma entidade que não existe.
- Houve uma tentativa para especificar uma conta de armazenamento que não está associada com a conta de serviços de mídia.  

## <a name="409-conflict"></a>409 Conflito

A solicitação não é permitida devido a um dos seguintes motivos:

- Mais de uma AssetFile com o nome especificado dentro do ativo.
- Houve uma tentativa para criar uma segunda AssetFile primária dentro do ativo.
- Houve uma tentativa para criar um ContentKey com a Id especificada já usada.
- Houve uma tentativa para criar um localizador com a Id especificada já usada.
- Mais de uma IngestManifestFile com o nome especificado dentro do IngestManifest.
- Houve uma tentativa de vincular uma segunda criptografia de armazenamento ContentKey ao ativo criptografados armazenamento.
- Houve uma tentativa de vincular a mesma ContentKey ao ativo.
- Houve uma tentativa para criar um localizador de um ativo cuja contêiner de armazenamento em falta ou não está mais associadas com o ativo.
- Houve uma tentativa para criar um localizador de um ativo que já possui 5 locators em uso. (O armazenamento do azure impõe o limite de cinco diretivas de acesso compartilhado em um contêiner de armazenamento.)
- Vinculando a conta de armazenamento de um ativo para um IngestManifestAsset não é a mesma que a conta de armazenamento usada pelo pai IngestManifest.  

## <a name="500-internal-server-error"></a>Erro de servidor interno 500

Durante o processamento da solicitação, serviços de mídia encontra algum erro que impede o processamento de continuar. Isso pode ser devido a um dos seguintes motivos:

- Criar uma ativo ou trabalho falha porque informações de cota de serviço da conta dos serviços de mídia estão temporariamente indisponíveis.
- Criar um contêiner de armazenamento de blob ativo ou IngestManifest falha porque informações de conta de armazenamento da conta estão temporariamente indisponíveis.
- Outro erro inesperado. 

## <a name="503-service-unavailable"></a>503 Serviço indisponível

O servidor está atualmente não conseguir receber solicitações. Este erro pode ser causado por excessivas solicitações de serviço. Otimização do mecanismo de serviços de mídia restringe o uso de recursos para aplicativos que fazer excessiva solicitação para o serviço.

>[AZURE.NOTE]Verifique a mensagem de erro e a cadeia de caracteres de código de erro para obter informações mais detalhadas sobre o motivo pelo qual você adquiriu o erro 503. Esse erro não significa sempre a otimização.

Descrições de status possíveis são:

- "O servidor está ocupado. Execuções anteriores desse tipo de solicitação demorou mais do que {0} segundos."
- "O servidor está ocupado. Mais de {0} solicitações por segundo podem ser limitada."
- "O servidor está ocupado. Mais de solicitações de {0} em {1} segundos podem ser limitada."

Para lidar com esse erro, é recomendável usar a lógica de repetição de retirada exponencial. Isso significa que usando vez mais aguarda entre as tentativas de respostas de erro consecutivas.  Para obter mais informações, consulte o [Bloco de aplicativo de tratamento de falhas temporárias](https://msdn.microsoft.com/library/hh680905.aspx). 

>[AZURE.NOTE]Se você estiver usando o [SDK de serviços de mídia do Azure para .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), a lógica de repetição para o erro 503 foi implementada pelo SDK.  
  
## <a name="see-also"></a>Consulte também  

[Códigos de erro de gerenciamento de serviços de mídia](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
