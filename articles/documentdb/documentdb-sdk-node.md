<properties
    pageTitle="API DocumentDB Node & SDK | Microsoft Azure"
    description="Saiba tudo sobre o Node API e SDK incluindo datas de lançamento, datas de aposentadoria e as alterações feitas entre cada versão do SDK DocumentDB Node."
    services="documentdb"
    documentationCenter="nodejs"
    authors="rnagpal"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB APIs e SDKs

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTANTE](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

##<a name="documentdb-nodejs-api-and-sdk"></a>SDK e API DocumentDB Node

<table>
<tr><td>**Baixe o SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**Documentação da API**</td><td>[Documentação de referência de API Node](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>
<tr><td>**Instruções de instalação do SDK**</td><td>[Instruções de instalação](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**Contribuem para SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**Amostras**</td><td>[Exemplos de código Node](documentdb-nodejs-samples.md)</td></tr>
<tr><td>**Obter tutorial de Introdução**</td><td>[Começar a usar o SDK do Node](documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**Tutorial do aplicativo da Web**</td><td>[Criar um aplicativo web do Node usando DocumentDB](documentdb-nodejs-application.md)</td></tr>
<tr><td>**Plataformas suportadas atual**</td><td>[V0.10 Node](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[V0.12 Node](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[V4.2.0 Node](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##<a name="release-notes"></a>Notas de versão

###<a name="1.10.0"/>1.10.0</a>

- Adicionado suporte para cruzada partição consultas em paralelo.
- Adicionado suporte para consultas de início/ORDER BY para conjuntos de particionada.

###<a name="1.9.0"/>1.9.0</a>

- Suporte a política repetir adicionado solicitações limitadas. (Limitadas solicitações recebem uma exceção de muito grande solicitação taxa, código de erro 429.) Por padrão, DocumentDB tentativas nove vezes para cada solicitação quando o código de erro 429 é encontrado, aceitar a hora de retryAfter no cabeçalho de resposta. Um intervalo de tempo de repetição fixo agora pode ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy se você queira ignorar a hora de retryAfter retornada pelo servidor entre as tentativas. DocumentDB agora aguarda um máximo de 30 segundos para cada solicitação que está sendo limitado (independentemente contagem de repetição) e retorna a resposta com código de erro 429. Desta vez também pode ser substituído na propriedade RetryOptions no objeto ConnectionPolicy.

- DocumentDB agora retorna x-ms-aceleração--contagem de repetição e x-ms-throttle-retry-wait-time-ms como os cabeçalhos de resposta em cada solicitação para indicar a aceleração repetir contagem e a hora de cummulative a solicitação esperava entre as tentativas.

- A classe de RetryOptions foi adicionada, expor a propriedade RetryOptions na classe ConnectionPolicy que pode ser usada para substituir alguns das opções padrão de repetição.

###<a name="1.8.0"/>1.8.0</a>

 - Adicionado o suporte para contas de banco de dados de várias regiões.

###<a name="1.7.0"/>1.7.0</a>

- Adicionado o suporte para o recurso de tempo para Live(TTL) para documentos.

###<a name="1.6.0"/>1.6.0</a>

- Implementado [coleções particionadas](documentdb-partition-data.md) e [níveis de desempenho definidos pelo usuário](documentdb-performance-levels.md).

###<a name="1.5.6"/>1.5.6</a>

- Fixo bug RangePartitionResolver.resolveForRead onde ele não estava retornando links devido a um mau concat dos resultados.

###<a name="1.5.5"/>1.5.5</a>

- Fixo hashParitionResolver resolveForRead(): quando nenhuma chave de partição fornecido foi lançar exceção, em vez de retornar uma lista de todos os links registrados.

###<a name="1.5.4"/>1.5.4</a>

- Correções dedicada HTTPS agente de [100 #](https://github.com/Azure/azure-documentdb-node/issues/100) - problema: evitar modificar o agente global para fins de DocumentDB. Use um agente dedicado para todas as solicitações de biblioteca.

###<a name="1.5.3"/>1.5.3</a>

- Correções de problemas [81 #](https://github.com/Azure/azure-documentdb-node/issues/81) - corretamente alça traços em ids de mídia.

###<a name="1.5.2"/>1.5.2</a>

- Correções de problemas [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter ouvinte perda de aviso.

###<a name="1.5.1"/>1.5.1</a>

- Correções de problemas [#92](https://github.com/Azure/azure-documentdb-node/issues/90) - Renomear pasta Hash ao hash para sistemas de maiusculas e minúsculas.

### <a name="1.5.0"/>1.5.0</a>

- Implementar o suporte de fragmentação adicionando resolvedores de partição hash & intervalo.

### <a name="1.4.0"/>1.4.0</a>

- Implemente Upsert. Novos métodos de upsertXXX em documentClient.

### <a name="1.3.0"/>1.3.0</a>

- Ignorada para colocar números de versão em alinhamento com outros SDKs.

### <a name="1.2.2"/>1.2.2</a>

- Dividir p promete embalagem para novo repositório.
- Atualize para o arquivo de pacote para npm registro.

### <a name="1.2.1"/>1.2.1</a>

- Implementa ID roteamento com base.
- Corrige o problema [49](https://github.com/Azure/azure-documentdb-node/issues/49) - atual propriedade está em conflito com o método Current ().

### <a name="1.2.0"/>1.2.0</a>

- Adicionado suporte para geoespaciais índice.
- Valida propriedade id para todos os recursos. IDs de recursos não podem conter?, /, #, #47; & #47; caracteres ou terminar com um espaço.
- Adiciona o novo cabeçalho "índice transformação progresso" ao ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>

- Implementa V2 diretiva de indexação.

### <a name="1.0.3"/>1.0.3</a>

- Problema [#40] (https://github.com/Azure/azure-documentdb-node/issues/40) - implementado eslint e pesado configurações no núcleo e promessa SDK.

### <a name="1.0.2"/>1.0.2</a>

- Problema [45](https://github.com/Azure/azure-documentdb-node/issues/45) - embalagem promessas não inclui o cabeçalho com erro.

### <a name="1.0.1"/>1.0.1</a>

- Implementado capacidade de consulta de conflitos adicionando readConflicts, readConflictAsync e queryConflicts.
- Documentação de API atualizada.
- Execute [41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync erro.

### <a name="1.0.0"/>1.0.0</a>

- GA SDK.

## <a name="release--retirement-dates"></a>Versão & datas de aposentadoria
A Microsoft fornecerá notificação pelo menos **12 meses** antes Cancelando um SDK para simplificar a transição para uma versão mais recente/suportada.

Novos recursos e funcionalidades e otimizações somente sejam adicionadas a SDK do atual, assim é recomendável que você sempre atualiza para a versão mais recente SDK mais cedo possível.

Qualquer solicitação para DocumentDB usando um SDK descartado será rejeitada pelo serviço.

> [AZURE.WARNING]
Todas as versões do Azure DocumentDB SDK para Node antes da versão **1.0.0** serão desativadas em **29 de fevereiro de 2016**.

<br/>

| Versão | Data do lançamento | Data de aposentadoria
| ---     | ---          | ---
| [1.10.0](#1.10.0) | 03 de outubro de 2016 |---
| [1.9.0](#1.9.0) | 07 de julho de 2016 |---
| [1.8.0](#1.8.0) | 14 de junho de 2016 |---
| [1.7.0](#1.7.0) | 26 de abril de 2016 |---
| [1.6.0](#1.6.0) | 29 de março de 2016 |---
| [1.5.6](#1.5.6) | 08 de março de 2016 |---
| [1.5.5](#1.5.5) | 02 de fevereiro de 2016 |---
| [1.5.4](#1.5.4) | 01 de fevereiro de 2016 |---
| [1.5.2](#1.5.2) | 26 de janeiro de 2016 |---
| [1.5.2](#1.5.2) | 22 de janeiro de 2016 |---
| [1.5.1](#1.5.1) | 4 de janeiro de 2016 |---
| [1.5.0](#1.5.0) | 31 de dezembro de 2015 |---
| [1.4.0](#1.4.0) | 06 de outubro de 2015 |---
| [1.3.0](#1.3.0) | 06 de outubro de 2015 |---
| [1.2.2](#1.2.2) | 10 de setembro de 2015 |---
| [1.2.1](#1.2.1) | 15 de agosto de 2015 |---
| [1.2.0](#1.2.0) | 05 de agosto de 2015 |---
| [1.1.0](#1.1.0) | 09 de julho de 2015 |---
| [1.0.3](#1.0.3) | 04 de junho de 2015 |---
| [1.0.2](#1.0.2) | 23 de maio de 2015 |---
| [1.0.1](#1.0.1) | 15 de maio de 2015 |---
| [1.0.0](#1.0.0) | 08 de abril de 2015 |---
| 0.9.4-prerelease | 06 de abril de 2015 | 29 de fevereiro de 2016
| 0.9.3-prerelease | 14 de janeiro de 2015 | 29 de fevereiro de 2016
| 0.9.2-prerelease | 18 de dezembro de 2014 | 29 de fevereiro de 2016
| 0.9.1-prerelease | 22 de agosto de 2014 | 29 de fevereiro de 2016
| 0.9.0-prerelease | 21 de agosto de 2014 | 29 de fevereiro de 2016


## <a name="faq"></a>Perguntas Freqüentes
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Consulte também

Para saber mais sobre DocumentDB, consulte a página de serviço do [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) .
