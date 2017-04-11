<properties 
    pageTitle="API do .NET DocumentDB & SDK | Microsoft Azure" 
    description="Saiba tudo sobre a API .NET e o SDK incluindo datas de lançamento, datas de aposentadoria e as alterações feitas entre cada versão do SDK do .NET DocumentDB." 
    services="documentdb" 
    documentationCenter=".net" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB APIs e SDKs 

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTANTE](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-net-api-and-sdk"></a>SDK e API do .NET DocumentDB

<table>
<tr><td>**Download do SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>
<tr><td>**Documentação da API**</td><td>[Documentação de referência de API .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>
<tr><td>**Amostras**</td><td>[Exemplos de código do .NET](documentdb-dotnet-samples.md)</td></tr>
<tr><td>**Introdução**</td><td>[Começar a usar o SDK do .NET DocumentDB](documentdb-get-started.md)</td></tr>
<tr><td>**Tutorial do aplicativo da Web**</td><td>[Desenvolvimento de aplicativos Web com DocumentDB](documentdb-dotnet-application.md)</td></tr>
<tr><td>**Estrutura compatível atual**</td><td>[Microsoft .NET Framework 4,5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de versão

> [AZURE.IMPORTANT] Começando com a versão 1.9.2, você pode receber System.NotSupportedException ao consultar coleções particionadas. Para evitar esse erro, certifique-se de que o processo de host é 64 bits. Para projetos executáveis, isso pode ser feito desmarcando a opção "Preferir 32 bits" na janela de propriedades do projeto, na guia de compilação.

### <a name="a-name11001100httpswwwnugetorgpackagesmicrosoftazuredocumentdb1100"></a><a name="1.10.0"/>[1.10.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.10.0)

  - Suporte a conectividade direta adicionado coleções particionadas.
  - Melhor desempenho para o nível de consistência envelhecimento limitada.
  - Polígono e LineString DataTypes ao especificar o conjunto de política para consultas espaciais geográfica-instalação da cerca de indexação adicionados.
  - Adicionado LINQ suporte para StringEnumConverter, IsoDateTimeConverter e UnixDateTimeConverter ao traduzir predicados.
  - Várias correções de bugs SDK.

### <a name="a-name195195httpswwwnugetorgpackagesmicrosoftazuredocumentdb195"></a><a name="1.9.5"/>[1.9.5](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.5)

  - Corrigido um problema que causou a seguir NotFoundException: A sessão de leitura não está disponível para o token de sessão de entrada. Essa exceção em alguns casos ao consultar a região de leitura de uma conta de distribuído de localização geográfica.
  - Exposto a propriedade ResponseStream na classe ResourceResponse, que permite o acesso direto para o fluxo subjacente de uma resposta.

### <a name="a-name194194httpswwwnugetorgpackagesmicrosoftazuredocumentdb194"></a><a name="1.9.4"/>[1.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)

  - Modificado as classes ResourceResponse, FeedResponse, StoredProcedureResponse e MediaResponse para implementar a interface pública correspondente para que eles podem fictícios para teste controlado pelo implantação (TDD).
  - Corrigido um problema que causou a um cabeçalho de chave de partição mal formado ao usar um objeto JsonSerializerSettings personalizado para os dados de serialização.

### <a name="a-name193193httpswwwnugetorgpackagesmicrosoftazuredocumentdb193"></a><a name="1.9.3"/>[1.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)

  - Corrigido um problema que causou consultas de execução demorada falha com erro: token de autorização não é válido no momento.
  - Corrigido um problema que removido o SqlParameterCollection original do cruzada partição superior/Ordenar por consultas.

### <a name="a-name192192httpswwwnugetorgpackagesmicrosoftazuredocumentdb192"></a><a name="1.9.2"/>[1.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)

  - Adicionado suporte para consultas em paralelo para conjuntos particionadas.
  - Adicionado suporte para entre consultas de partição ORDER BY e superior para conjuntos de particionada.
  - Fixo as referências ausentes DocumentDB.Spatial.Sql.dll e Microsoft.Azure.Documents.ServiceInterop.dll que são necessárias ao fazer referência a um projeto de DocumentDB com uma referência para o pacote DocumentDB Nuget.
  - Fixo a capacidade de usar parâmetros de tipos diferentes ao usar funções definidas pelo usuário em LINQ. 
  - Corrigido um bug para contas globalmente replicados onde Upsert chamadas foram sendo direcionadas para ler locais em vez de locais de gravação.
  - Adicionado métodos para a interface de IDocumentClient que estavam ausentes: 
      - Método de UpsertAttachmentAsync que leva opções como parâmetros e mediaStream
      - Método de CreateAttachmentAsync que leva opções como um parâmetro
      - Método de CreateOfferQuery que leva querySpec como um parâmetro.
  - Classes públicas sem lacre que estão expostas na interface do IDocumentClient.

### <a name="a-name180180httpswwwnugetorgpackagesmicrosoftazuredocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
  - Adicionado o suporte para contas de banco de dados de várias regiões.
  - Adicionado suporte para repetir em solicitações limitadas.  Usuário pode personalizar o número de tentativas e o tempo de espera máximo Configurando a propriedade ConnectionPolicy.RetryOptions.
  - Adicionar uma nova interface IDocumentClient que define as assinaturas de todos os métodos e propriedades de DocumenClient.  Como parte dessa alteração, também alterado métodos de extensão que criar IQueryable e IOrderedQueryable para métodos na classe DocumentClient em si.
  - Opção de configuração para definir o ServicePoint.ConnectionLimit para um determinado ponto de extremidade do DocumentDB Uri adicionada.  Use ConnectionPolicy.MaxConnectionLimit para alterar o valor padrão, que é definido para 50.
  - IPartitionResolver preterida e sua implementação.  Suporte para IPartitionResolver agora é obsoleto. É recomendável que você use coleções particionado para produtividade e armazenamento maior.


### <a name="a-name171171httpswwwnugetorgpackagesmicrosoftazuredocumentdb171"></a><a name="1.7.1"/>[1.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
  - Adicionado a uma sobrecarga para Uri com base em método de ExecuteStoredProcedureAsync que leva RequestOptions como um parâmetro.
  
### <a name="a-name170170httpswwwnugetorgpackagesmicrosoftazuredocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
  - Tempo adicionado ao suporte do live (TTL) para documentos.

### <a name="a-name163163httpswwwnugetorgpackagesmicrosoftazuredocumentdb163"></a><a name="1.6.3"/>[1.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
  - Fixo um bug na embalagem Nuget do SDK .NET para empacotamento-lo como parte de uma solução de serviço de nuvem do Azure.
  
### <a name="a-name162162httpswwwnugetorgpackagesmicrosoftazuredocumentdb162"></a><a name="1.6.2"/>[1.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
  - Implementado [coleções particionadas](documentdb-partition-data.md) e [níveis de desempenho definidos pelo usuário](documentdb-performance-levels.md). 

### <a name="a-name153153httpswwwnugetorgpackagesmicrosoftazuredocumentdb153"></a><a name="1.5.3"/>[1.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[Fixo]** Consultando gera de ponto de extremidade de DocumentDB: ' System.Net.Http.HttpRequestException: erro ao copiar o conteúdo para um fluxo.

### <a name="a-name152152httpswwwnugetorgpackagesmicrosoftazuredocumentdb152"></a><a name="1.5.2"/>[1.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - LINQ expandido incluindo novos operadores para expressões de paginação, condicionais de suporte e comparação de intervalo.
    - Levar o operador para ativar o comportamento de SELECT TOP em LINQ
    - Operador de CompareTo habilitar comparações de sequências de intervalo
    - Condicional (?) e operadores (?) de união
  - **[Fixo]** ArgumentOutOfRangeException ao combinar projeção de modelo com onde-In na consulta linq.  [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151httpswwwnugetorgpackagesmicrosoftazuredocumentdb151"></a><a name="1.5.1"/>[1.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[Fixo]** Se selecionar não estiver na última expressão o provedor LINQ pressupõe que nenhuma projeção e produzidos SELECT * incorretamente.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150httpswwwnugetorgpackagesmicrosoftazuredocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Upsert implementado, métodos de UpsertXXXAsync adicionado
 - Melhorias de desempenho para todas as solicitações
 - Suporte a provedor LINQ condicional, união e métodos CompareTo para cadeias de caracteres
 - **[Fixo]** Provedor LINQ--> implementar contém método na lista para gerar o mesmo SQL como em IEnumerable e matriz
 - **[Fixo]** BackoffRetryUtility usa a mesma HttpRequestMessage novamente em vez de criar um novo em Repetir
 - **[Obsoleto]** UriFactory.CreateCollection--> agora deve usar UriFactory.CreateDocumentCollection
 
### <a name="a-name141141httpswwwnugetorgpackagesmicrosoftazuredocumentdb141"></a><a name="1.4.1"/>[1.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[Fixo]** Problemas de localização ao usar informações de cultura en não como nl-NL etc. 
 
### <a name="a-name140140httpswwwnugetorgpackagesmicrosoftazuredocumentdb140"></a><a name="1.4.0"/>[1.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - ID baseado roteamento
    - Novo auxiliar UriFactory para ajudá-lo com a ID construindo com base em links de recursos
    - Novas sobrecargas em DocumentClient tirar no URI
  - IsValid() e IsValidDetailed() em LINQ para geoespaciais adicionados
  - Suporte de provedor LINQ aprimorado
    - Truncar **matemática** - Abs, Acos, ASEN, Atan, teto, Cos, Tan Exp, plantas, Log, Log10, Pow, ARRED, entrada, sen, raiz,
    - **Cadeia de caracteres** - Concat, contém, EndsWith, IndexOf, contagem, ToLower, TrimStart, substituir, inverter, TrimEnd, StartsWith, subcadeia, ToUpper
    - **Matriz** - Concat, contém, contagem
    - Operador **IN**

### <a name="a-name130130httpswwwnugetorgpackagesmicrosoftazuredocumentdb130"></a><a name="1.3.0"/>[1.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - Suporte adicionado para modificar políticas de indexação
    - Novo método de ReplaceDocumentCollectionAsync no DocumentClient
    - Nova propriedade de IndexTransformationProgress no ResourceResponse<T> para acompanhar o progresso de porcentagem de alterações de políticas de índice
    - DocumentCollection.IndexingPolicy agora é mutável
  - Suporte adicionado para consulta e indexação espacial
    - Novo namespace Microsoft.Azure.Documents.Spatial para serializar/desserializar tipos espaciais como ponto e polígono
    - Nova classe SpatialIndex para indexação GeoJSON dados armazenados em DocumentDB
  - **[Fixo]** : consulta SQL incorretos gerada a partir de expressão linq [38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="a-name120120httpswwwnugetorgpackagesmicrosoftazuredocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Dependência em Newtonsoft.Json v5.0.7 
- Alterações para suportar Order By
  - Suporte de provedor LINQ para OrderBy() ou OrderByDescending()
  - IndexingPolicy para dar suporte a Order By 
  
        **NB: Possible breaking change** 
  
        If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- Suporte para partições dados usando as novas classes HashPartitionResolver e RangePartitionResolver e o IPartitionResolver
- Serialização DataContract
- Suporte de GUID no provedor LINQ
- Suporte UDF em LINQ

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- GA SDK

> [AZURE.NOTE]
Houve uma alteração de nome do pacote de NuGet entre visualização e GA. Podemos movido do **Microsoft.Azure.Documents.Client** para **Microsoft.Azure.DocumentDB**
<br/>


### <a name="a-name09x-preview09x-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentsclient"></a><a name="0.9.x-preview"/>[0.9.x-Preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- Visualização SDKs [obsoleto]

## <a name="release--retirement-dates"></a>Versão & datas de aposentadoria
A Microsoft fornecerá notificação pelo menos **12 meses** antes Cancelando um SDK para simplificar a transição para uma versão mais recente/suportada.

Novos recursos e funcionalidades e otimizações somente sejam adicionadas a SDK do atual, como tal, é recomendável que você sempre atualiza para a versão mais recente SDK mais cedo possível. 

Qualquer solicitação para DocumentDB usando um SDK descartado será rejeitada pelo serviço.

> [AZURE.WARNING]
Todas as versões do Azure DocumentDB SDK para .NET antes de versão **1.0.0** serão desativadas em **29 de fevereiro de 2016**. 
 
<br/>
 
| Versão | Data do lançamento | Data de aposentadoria 
| ---     | ---          | ---
| [1.10.0](#1.10.0) | 27 de setembro de 2016 |---
| [1.9.5](#1.9.5) | 01 de setembro de 2016 |---
| [1.9.4](#1.9.4) | 24 de agosto de 2016 |---
| [1.9.3](#1.9.3) | 15 de agosto de 2016 |---
| [1.9.2](#1.9.2) | 23 de julho de 2016 |---
| 1.9.1 | Obsoleta |---
| 1.9.0 | Obsoleta |---
| [1.8.0](#1.8.0) | 14 de junho de 2016 |---
| [1.7.1](#1.7.1) | 06 de maio de 2016 |---
| [1.7.0](#1.7.0) | 26 de abril de 2016 |---
| [1.6.3](#1.6.3) | 08 de abril de 2016 |---
| [1.6.2](#1.6.2) | 29 de março de 2016 |---
| [1.5.3](#1.5.3) | 19 de fevereiro de 2016 |---
| [1.5.2](#1.5.2) | 14 de dezembro de 2015 |---
| [1.5.1](#1.5.1) | 23 de novembro de 2015 |---
| [1.5.0](#1.5.0) | 05 de outubro de 2015 |---
| [1.4.1](#1.4.1) | 25 de agosto de 2015 |---
| [1.4.0](#1.4.0) | 13 de agosto de 2015 |---
| [1.3.0](#1.3.0) | 05 de agosto de 2015 |---
| [1.2.0](#1.2.0) | 06 de julho de 2015 |---
| [1.1.0](#1.1.0) | 30 de abril de 2015 |---
| [1.0.0](#1.0.0) | 08 de abril de 2015 |---
| [0.9.3-prelease](#0.9.x-preview) | 12 de março de 2015 | 29 de fevereiro de 2016
| [0.9.2-prelease](#0.9.x-preview) | Janeiro de 2015 | 29 de fevereiro de 2016
| [.9.1-prelease](#0.9.x-preview) | 13 de outubro de 2014 | 29 de fevereiro de 2016
| [0.9.0-prelease](#0.9.x-preview) | 21 de agosto de 2014 | 29 de fevereiro de 2016

## <a name="faq"></a>Perguntas Freqüentes
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Consulte também

Para saber mais sobre DocumentDB, consulte a página de serviço do [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 
