
<properties
    pageTitle="API DocumentDB Java & SDK | Microsoft Azure"
    description="Saiba tudo sobre o SDK incluindo datas de lançamento, datas de aposentadoria e as alterações feitas entre cada versão do DocumentDB Java SDK e API Java."
    services="documentdb"
    documentationCenter="java"
    authors="rnagpal"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB APIs e SDKs

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTANTE](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-java-api-and-sdk"></a>SDK e API DocumentDB Java

<table>
<tr><td>**Download do SDK**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>
<tr><td>**Documentação da API**</td><td>[Documentação de referência da API Java](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**Contribuem para SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**Introdução**</td><td>[Começar a usar o SDK Java](documentdb-java-application.md)</td></tr>
<tr><td>**Tempo de execução com suporte atual**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de versão

### <a name="a-name191191httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb191"></a><a name="1.9.1"/>[1.9.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.1)

  - Adicionado suporte para o nível de consistência BoundedStaleness.
  - Adicionado suporte para conectividade direta para operações de CRUD para conjuntos de particionada.
  - Corrigido um bug no consultando um banco de dados com o SQL.
  - Corrigido um bug no cache da sessão onde o token de sessão pode ser definido incorretamente.

### <a name="a-name190190httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb190"></a><a name="1.9.0"/>[1.9.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.0)

  - Adicionado suporte para cruzada partição consultas em paralelo.
  - Adicionado suporte para consultas de início/ORDER BY para conjuntos de particionada.
  - Adicionado suporte para consistência forte.
  - Adicionado suporte para solicitações de nome baseado usando conectividade direta.
  - Fixo tornar ActivityId permanecer consistente em todas as repetições de solicitação.
  - Corrigido um bug relacionado ao cache da sessão ao recriar um conjunto com o mesmo nome.
  - Polígono e LineString DataTypes ao especificar o conjunto de política para consultas espaciais geográfica-instalação da cerca de indexação adicionados.
  - Problemas corrigidos com Doc Java para Java 1.8.

### <a name="a-name181181httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb181"></a><a name="1.8.1"/>[1.8.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.1)
  - Corrigido um bug no PartitionKeyDefinitionMap para conjuntos de única partição de cache e sem fazer a busca extra partição solicitações de chave.
  - Corrigido um bug não repetir quando um valor de chave de partição incorreto é fornecido.

### <a name="a-name180180httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb180"></a><a name="1.8.0"/>[1.8.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.0)
  - Adicionado o suporte para contas de banco de dados de várias regiões.
  - Adicionado suporte para repetir automática em solicitações limitadas com opções para personalizar o máximo de tentativas e tempo de espera de repetição máx.  Consulte RetryOptions e ConnectionPolicy.getRetryOptions().
  - IPartitionResolver preterido baseado partição de código personalizado. Use coleções particionadas para maiores de armazenamento e produtividade.

### <a name="a-name171171httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb171"></a><a name="1.7.1"/>[1.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
- Suporte a política repetir adicionado a otimização.  

### <a name="a-name170170httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb170"></a><a name="1.7.0"/>[1.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
- Tempo adicionado ao suporte do live (TTL) para documentos.

### <a name="a-name160160httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb160"></a><a name="1.6.0"/>[1.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
- Implementado [coleções particionadas](documentdb-partition-data.md) e [níveis de desempenho definidos pelo usuário](documentdb-performance-levels.md).

### <a name="a-name151151httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb151"></a><a name="1.5.1"/>[1.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
- Corrigido um bug no HashPartitionResolver para gerar valores de hash no recurso para ser consistente com outros SDKs.

### <a name="a-name150150httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb150"></a><a name="1.5.0"/>[1.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
- Adicionar Hash & intervalo partição resolvedores para ajudar com aplicativos de fragmentação por várias partições.

### <a name="a-name140140httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb140"></a><a name="1.4.0"/>[1.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- Implemente Upsert. Novos métodos de upsertXXX adicionados à suporta o recurso de Upsert.
- Implemente o roteamento de ID baseado. Nenhuma alteração de API pública, todas as alterações internas.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
- Lançamento ignorada para trazer o número da versão em alinhamento com outros SDKs

### <a name="a-name120120httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb120"></a><a name="1.2.0"/>[1.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- Suporta geoespaciais índice
- Valida propriedade id para todos os recursos. IDs de recursos não podem conter ?, /, #, \, caracteres ou terminar com um espaço.
- Adiciona o novo cabeçalho "índice transformação progresso" ao ResourceResponse.

### <a name="a-name110110httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb110"></a><a name="1.1.0"/>[1.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- Implementa diretiva de indexação V2

### <a name="a-name100100httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb100"></a><a name="1.0.0"/>[1.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- GA SDK

## <a name="release--retirement-dates"></a>Versão & datas de aposentadoria
A Microsoft fornecerá notificação pelo menos **12 meses** antes Cancelando um SDK para simplificar a transição para uma versão mais recente/suportada.

Novos recursos e funcionalidades e otimizações somente sejam adicionadas a SDK do atual, assim é recomendável que você sempre atualiza para a versão mais recente SDK mais cedo possível.

Qualquer solicitação para DocumentDB usando um SDK descartado será rejeitada pelo serviço.

> [AZURE.WARNING]
Todas as versões do Azure DocumentDB SDK para Java antes de versão **1.0.0** serão desativadas em **29 de fevereiro de 2016**.

<br/>

| Versão | Data do lançamento | Data de aposentadoria
| ---     | ---          | ---
| [1.9.1](#1.9.1) | 28 de outubro de 2016 |---
| [1.9.0](#1.9.0) | 03 de outubro de 2016 |---
| [1.8.1](#1.8.1) | 30 de junho de 2016 |---
| [1.8.0](#1.8.0) | 14 de junho de 2016 |---
| [1.7.1](#1.7.1) | 30 de abril de 2016 |---
| [1.7.0](#1.7.0) | 27 de abril de 2016 |---
| [1.6.0](#1.6.0) | 29 de março de 2016 |---
| [1.5.1](#1.5.1) | 31 de dezembro de 2015 |---
| [1.5.0](#1.5.0) | 04 de dezembro de 2015 |---
| [1.4.0](#1.4.0) | 05 de outubro de 2015 |---
| [1.3.0](#1.3.0) | 05 de outubro de 2015 |---
| [1.2.0](#1.2.0) | 05 de agosto de 2015 |---
| [1.1.0](#1.1.0) | 09 de julho de 2015 |---
| [1.0.1](#1.0.1) | 12 de maio de 2015 |---
| [1.0.0](#1.0.0) | 07 de abril de 2015 |---
| 0.9.5-prelease | 09 de março de 2015 | 29 de fevereiro de 2016
| 0.9.4-prelease | 17 de fevereiro de 2015 | 29 de fevereiro de 2016
| 0.9.3-prelease | 13 de janeiro de 2015 | 29 de fevereiro de 2016
| 0.9.2-prelease | 19 de dezembro de 2014 | 29 de fevereiro de 2016
| 0.9.1-prelease | 19 de dezembro de 2014 | 29 de fevereiro de 2016
| 0.9.0-prelease | 10 de dezembro de 2014 | 29 de fevereiro de 2016

## <a name="faq"></a>Perguntas Freqüentes
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Consulte também

Para saber mais sobre DocumentDB, consulte a página de serviço do [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) .
