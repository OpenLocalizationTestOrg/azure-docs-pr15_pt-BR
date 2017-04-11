<properties 
    pageTitle="API de Python DocumentDB & SDK | Microsoft Azure" 
    description="Saiba tudo sobre o Python API e SDK incluindo datas de lançamento, datas de aposentadoria e as alterações feitas entre cada versão do SDK Python DocumentDB." 
    services="documentdb" 
    documentationCenter="python" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB APIs e SDKs

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTANTE](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-python-api-and-sdk"></a>SDK e API de Python DocumentDB

<table>
<tr><td>**Baixe o SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**Documentação da API**</td><td>[Documentação de referência de API de Python](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>
<tr><td>**Instruções de instalação do SDK**</td><td>[Instruções de instalação do Python SDK](http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**Contribuem para SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**Introdução**</td><td>[Começar a usar o SDK do Python](documentdb-python-application.md)</td></tr>
<tr><td>**Plataformas suportadas atual**</td><td>[Python 2.7](https://www.python.org/downloads/) e [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de versão

### <a name="a-name200200httpspypipythonorgpypipydocumentdb200"></a><a name="2.0.0"/>[2.0.0](https://pypi.python.org/pypi/pydocumentdb/2.0.0)
- Adicionado suporte para Python 3.5.
- Adicionado suporte para usando um módulo de solicitações de pool de conexão.
- Adicionado suporte para consistência de sessão.
- Adicionado suporte para consultas de início/ORDERBY para conjuntos de particionada.


### <a name="a-name190190httpspypipythonorgpypipydocumentdb190"></a><a name="1.9.0"/>[1.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
- Suporte a política repetir adicionado solicitações limitadas. (Limitadas solicitações recebem uma exceção de muito grande solicitação taxa, código de erro 429.) Por padrão, DocumentDB tentativas nove vezes para cada solicitação quando o código de erro 429 é encontrado, aceitar a hora de retryAfter no cabeçalho de resposta. Um intervalo de tempo de repetição fixo agora pode ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy se você queira ignorar a hora de retryAfter retornada pelo servidor entre as tentativas. DocumentDB agora aguarda um máximo de 30 segundos para cada solicitação que está sendo limitado (independentemente contagem de repetição) e retorna a resposta com código de erro 429. Desta vez também pode ser substituído na propriedade RetryOptions no objeto ConnectionPolicy.

- DocumentDB agora retorna x-ms-aceleração--contagem de repetição e x-ms-throttle-retry-wait-time-ms como os cabeçalhos de resposta em cada solicitação para indicar a aceleração repetir contagem e a hora de cummulative a solicitação esperava entre as tentativas.

- Removido a classe RetryPolicy e a propriedade correspondente (retry_policy) exposto na classe document_client e, em vez disso introduziu uma classe RetryOptions expor a propriedade RetryOptions na classe de ConnectionPolicy que pode ser usado para substituir alguns das opções padrão de repetição.

### <a name="a-name180180httpspypipythonorgpypipydocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
  - Adicionado o suporte para contas de banco de dados de várias regiões.

### <a name="a-name170170httpspypipythonorgpypipydocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
- Adicionado o suporte para o recurso de tempo para Live(TTL) para documentos.

### <a name="a-name161161httpspypipythonorgpypipydocumentdb161"></a><a name="1.6.1"/>[1.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- Correções de bugs relacionadas a partição do lado do servidor para permitir que os caracteres especiais no caminho partitionkey.

### <a name="a-name160160httpspypipythonorgpypipydocumentdb160"></a><a name="1.6.0"/>[1.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- Implementado [coleções particionadas](documentdb-partition-data.md) e [níveis de desempenho definidos pelo usuário](documentdb-performance-levels.md). 

### <a name="a-name150150httpspypipythonorgpypipydocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- Adicionar Hash & intervalo partição resolvedores para ajudar com aplicativos de fragmentação por várias partições.

### <a name="a-name142142httpspypipythonorgpypipydocumentdb142"></a><a name="1.4.2"/>[1.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- Implemente Upsert. Novos métodos de UpsertXXX adicionados à suporta o recurso de Upsert.
- Implemente o roteamento de ID baseado. Nenhuma alteração de API pública, todas as alterações internas.

### <a name="a-name120120httpspypipythonorgpypipydocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- Índice de geoespaciais suporta.
- Valida propriedade id para todos os recursos. IDs de recursos não podem conter ?, /, #, \, caracteres ou terminar com um espaço.
- Adiciona o novo cabeçalho "índice transformação progresso" ao ResourceResponse.

### <a name="a-name110110httpspypipythonorgpypipydocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- Implementa V2 diretiva de indexação.

### <a name="a-name101101httpspypipythonorgpypipydocumentdb101"></a><a name="1.0.1"/>[1.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- Dá suporte à conexão de proxy.

### <a name="a-name100100httpspypipythonorgpypipydocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- GA SDK.

## <a name="release--retirement-dates"></a>Datas de lançamento & aposentadoria
A Microsoft fornecerá notificação pelo menos **12 meses** antes Cancelando um SDK para simplificar a transição para uma versão mais recente/suportada.

Novos recursos e funcionalidades e otimizações somente sejam adicionadas a SDK do atual, assim é recomendável que você sempre atualiza para a versão mais recente SDK mais cedo possível. 

Qualquer solicitação para DocumentDB usando um SDK descartado será rejeitada pelo serviço.

> [AZURE.WARNING]
Todas as versões do Azure DocumentDB SDK para Python antes da versão **1.0.0** serão desativadas em **29 de fevereiro de 2016**. 

<br/>

| Versão | Data do lançamento | Data de aposentadoria 
| ---     | ---          | ---
| [2.0.0](#2.0.0) | 29 de setembro de 2016 |---
| [1.9.0](#1.9.0) | 07 de julho de 2016 |---
| [1.8.0](#1.8.0) | 14 de junho de 2016 |---
| [1.7.0](#1.7.0) | 26 de abril de 2016 |---
| [1.6.1](#1.6.1) | 08 de abril de 2016 |---
| [1.6.0](#1.6.0) | 29 de março de 2016 |---
| [1.5.0](#1.5.0) | 03 de janeiro de 2016 |---
| [1.4.2](#1.4.2) | 06 de outubro de 2015 |---
| [1.4.1](#1.4.1) | 06 de outubro de 2015 |---
| [1.2.0](#1.2.0) | 06 de agosto de 2015 |---
| [1.1.0](#1.1.0) | 09 de julho de 2015 |---
| [1.0.1](#1.0.1) | 25 de maio de 2015 |---
| [1.0.0](#1.0.0) | 07 de abril de 2015 |---
| 0.9.4-prelease | 14 de janeiro de 2015 | 29 de fevereiro de 2016
| 0.9.3-prelease | 09 de dezembro de 2014 | 29 de fevereiro de 2016
| 0.9.2-prelease | 25 de novembro de 2014 | 29 de fevereiro de 2016
| 0.9.1-prelease | 23 de setembro de 2014 | 29 de fevereiro de 2016
| 0.9.0-prelease | 21 de agosto de 2014 | 29 de fevereiro de 2016

## <a name="faq"></a>Perguntas Freqüentes
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Consulte também

Para saber mais sobre DocumentDB, consulte a página de serviço do [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 
