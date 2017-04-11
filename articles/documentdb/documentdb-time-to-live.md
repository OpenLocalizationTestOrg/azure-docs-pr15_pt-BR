<properties
   pageTitle="Dados em DocumentDB com o tempo de vida de expirar | Microsoft Azure"
   description="Com TTL, Microsoft Azure DocumentDB fornece a capacidade de ter documentos automaticamente limpos do sistema após um período de tempo."
   services="documentdb"
   documentationCenter=""
   keywords="tempo de vida"
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2016"
   ms.author="kipandya"/>

# <a name="expire-data-in-documentdb-collections-automatically-with-time-to-live"></a>Dados em conjuntos de DocumentDB automaticamente com o tempo de vida de expirar

Aplicativos podem produzir e armazenar grandes quantidades de dados. Alguns dos dados, como produtos de sessão de usuário, logs e dados de eventos máquina gerado informações só são útil para um determinado período de tempo. Depois que os dados se tornar excedente com as necessidades do aplicativo é seguro limpar dados e reduzir as necessidades de armazenamento de um aplicativo.

Com "hora Live" ou TTL, Microsoft Azure DocumentDB fornece a capacidade de ter documentos automaticamente removidos do banco de dados após um período de tempo. O horário padrão do Live pode ser definido no nível do conjunto e substituído em uma base por documento. Quando TTL estiver definida, como um padrão de conjunto ou em um nível de documento, DocumentDB removerá automaticamente documentos que existem após esse período de tempo, em segundos, desde que ele foram modificado pela última vez.

Tempo de vida em DocumentDB usa um deslocamento em relação ao documento da última modificação. Para fazer isso, ele usa o campo de _ts que existe em cada documento. O campo _ts é um carimbo de hora do estilo unix época que representa a data e hora. O campo _ts é atualizado sempre que um documento é modificado. 

## <a name="ttl-behavior"></a>Comportamento TTL

O recurso TTL é controlado por propriedades TTL em dois níveis - o nível do conjunto e o nível de documento. Os valores definidos em segundos e são tratados como um delta da _ts documento última modificação em.

 1.  DefaultTTL para o conjunto
  * Se faltando (ou definido como nulo), documentos não são excluídos automaticamente.
  
  * Se presente e o valor é "-1" = infinito – documentos não expirem por padrão
  
  * Se presente e o valor é algum número ("n") – documentos expiram "n" segundos após a última modificação

 2.  TTL para os documentos: 
  * Propriedade é aplicável somente se DefaultTTL estiver presente para o conjunto pai.
  
  * Substitui o valor de DefaultTTL para o conjunto pai.

Assim que o documento expirou (ttl + _ts > = hora atual do servidor), o documento está marcado como "expirado". Nenhuma operação será permitida esses documentos após esse horário e eles serão excluídos dos resultados de qualquer consulta executada. Os documentos são excluídos física no sistema e são excluídos no plano de fundo oportunamente posteriormente. Isso não consome qualquer [Solicitação unidades (RUs)](documentdb-request-units.md) do orçamento conjunto.

A lógica acima pode ser mostrada na matriz a seguir:

|       | DefaultTTL ausente/não definir na coleção | DefaultTTL = -1 em conjunto | DefaultTTL = "n" em conjunto|
| ------------- |:-------------|:-------------|:-------------|
| TTL ausente no documento| Nada substituir no nível de documento, desde que o documento e a coleção não tem nenhum conceito de TTL. | Não há documentos nessa coleção irá expirar. | Os documentos nessa coleção expirará decorrida n de intervalo. |
| TTL = -1 no documento | Nada substituir no nível do documento, desde que o conjunto não define a propriedade DefaultTTL que um documento pode substituir. TTL em um documento é interpretada cancelamento pelo sistema. | Não há documentos nessa coleção irá expirar. | O documento com TTL = 1 nessa coleção nunca expirará. Todos os outros documentos expirará após intervalo "n". |
|  TTL = n no documento | Nada substituir no nível do documento. TTL em um documento no cancelamento interpretada pelo sistema. | O documento com TTL = n expirará após n de intervalo, em segundos. Outros documentos serão herdam intervalo de -1 e nunca expirar. | O documento com TTL = n expirará após n de intervalo, em segundos. Outros documentos herdarão intervalo "n" da coleção. |


## <a name="configuring-ttl"></a>Configurando TTL

Por padrão, o tempo de vida é desabilitado por padrão em todas as coleções de DocumentDB e em todos os documentos.

## <a name="enabling-ttl"></a>Habilitando TTL

Para ativar o TTL em um conjunto, ou os documentos em um conjunto, você precisa definir a propriedade DefaultTTL de um conjunto de -1 ou um número positivo diferente de zero. Definindo o DefaultTTL como-1 significa que padrão que todos os documentos na coleção serão live indefinidamente, mas o DocumentDB serviço deve monitorar este conjunto de documentos que substituídos esse padrão.

## <a name="configuring-default-ttl-on-a-collection"></a>Configurando o TTL padrão em um conjunto

Você é capaz de configurar um horário padrão do Live em um nível de conjunto. 

Para definir o TTL em um conjunto, você precisa fornecer um número positivo diferente de zero que indica o período, em segundos, para expirar todos os documentos no conjunto de após o último carimbo de hora modificado do documento (_ts).

Ou, você pode definir o padrão para -1, que indica que todos os documentos inseridos no conjunto de serão live indefinidamente por padrão.

## <a name="setting-ttl-on-a-document"></a>Configuração de TTL em um documento

Além de definir um padrão TTL em um conjunto, você pode definir TTL específico em um nível de documento. Isso substituirá o padrão da coleção.

Para definir o TTL em um documento, você precisa fornecer um número positivo diferente de zero, que indica o período, em segundos, para expirar o documento após o último carimbo de hora modificado do documento (_ts).

Para definir esse deslocamento de expiração, defina o campo TTL no documento.

Se um documento não tiver nenhum campo TTL, o padrão do conjunto de aplicará.

Se TTL estiver desabilitada no nível do conjunto, o campo TTL no documento será ignorado até TTL é ativado novamente o conjunto.


## <a name="extending-ttl-on-an-existing-document"></a>Estendendo TTL em um documento existente

Você pode redefinir o TTL em um documento seguindo qualquer operação de gravação no documento. Fazer isso definirá o _ts para a hora atual, e a contagem regressiva para a expiração de documento, conforme definido pelo ttl, começará novamente.

Se desejar alterar o ttl de um documento, você pode atualizar o campo como você pode fazer com qualquer outro campo configurável.


## <a name="removing-ttl-from-a-document"></a>Removendo TTL de um documento

Se TTL foi definido em um documento e você não quiser mais esse documento expire, você pode recuperar o documento, remova o campo TTL e substituir o documento no servidor.

Quando o campo TTL é removido do documento, o padrão da coleção será aplicado.

Para interromper a um documento de expiração e não herdam a coleção, em seguida, você precisa definir o valor TTL como -1.


## <a name="disabling-ttl"></a>Desabilitando TTL

Para desabilitar o TTL inteiramente em um conjunto e interromper o processo de plano de fundo de procurando documentos expirados que a propriedade DefaultTTL na coleção deve ser excluída.

A exclusão desta propriedade é diferente da configuração-para -1. Configuração para novos documentos de-1 significa adicionado à coleção será live indefinidamente, mas você pode substituí-lo em documentos específicos no conjunto de.

Remover esta propriedade inteiramente da coleção significa que nenhum documento expira, mesmo se houver documentos que explicitamente substituídos padrão anterior.


## <a name="faq"></a>Perguntas Freqüentes

**O que TTL custará mim?**

Não há nenhum custo adicional para configurar um TTL em um documento.

**Quanto tempo levará para excluir meu documento depois que o TTL está funcionando?**

Os documentos são marcados como não disponíveis assim que o documento expirou (ttl + _ts > = hora atual do servidor). Nenhuma operação será permitida esses documentos após esse horário e eles serão excluídos dos resultados de qualquer consulta executada. Os documentos são excluídos física pelo sistema no plano de fundo. Isso não irá consumir qualquer RUs de orçamento da coleção.

**Se demorar um período de tempo para excluir os documentos, eles contará para minha cota (e fatura) até que ele seja excluídos?**

Não, quando o documento expirou não será cobrado para o armazenamento desses documentos e o tamanho dos documentos não contará para a cota de armazenamento para um conjunto.

**TTL em um documento terá qualquer impacto em encargos RU?**

Não haverá nenhum impacto em encargos RU para operações executadas em qualquer documento dentro DocumentDB.

**Será a exclusão de impacto a produtividade de que eu tiver provisionado no meu conjunto documentos?**

Não, servindo solicitações contra seu conjunto receberá prioridade a execução do processo de plano de fundo para excluir seus documentos. Adicionando TTL a qualquer documento não afetará a isso.

**Quando um documento expira, quanto tempo ela permanecerá no meu conjunto até que seja excluída?**

Assim que o documento expira ele não será mais acessível. O tempo exato um documento permanecerá em seu conjunto antes de realmente excluído é não determinante e se baseará quando o processo de plano de fundo é capaz de excluir o documento.

**São documentos expirados excluídos em todos os nós ou é "eventualmente consistent?"**

O documento ficará disponível ao mesmo tempo em todos os nós e em todas as regiões.

**Existe um custo RU para monitoramento TTL tarefas em segundo plano?**

Não, não há nenhum custo de RU para isso.

**Com que frequência vencimentos TTL estão marcados?**

Verificação de vencimentos TTL não ocorre como um processo de plano de fundo. Ao responder a uma solicitação do serviço de back-end fazer o embutido verificações e excluir documentos expirados. A exclusão do documento físico é o único processo que assíncrona é executado em segundo plano. A frequência desse processo é determinada pelo RUs disponível na coleção.

**Se o recurso TTL apenas aplica documentos inteiros ou posso podem expirar valores de propriedade de documento individual?**

TTL se aplica a todo o documento. Se você gostaria de expirar apenas uma parte de um documento, em seguida, é recomendável que você extrair a parte do documento principal no documento "vinculadas" separado e depois use TTL no documento extraído.

**O recurso TTL tem os requisitos de indexação específicos?**

Sim. O conjunto deve ter a [indexação política definida](documentdb-indexing-policies.md) como Lazy ou consistente. Tentando configurar DefaultTTL em uma coleção com indexação conjunto nenhum resultará em um erro, que também tentando desativar indexação em um conjunto que possui um DefaultTTL já definido.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre DocumentDB do Azure, consulte a página de [*documentação*](https://azure.microsoft.com/documentation/services/documentdb/) do serviço.




