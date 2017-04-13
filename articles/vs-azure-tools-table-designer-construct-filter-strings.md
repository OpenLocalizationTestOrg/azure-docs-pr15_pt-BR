<properties
   pageTitle="Construindo cadeias de caracteres de filtro para o designer de tabela | Microsoft Azure"
   description="Construindo cadeias de caracteres de filtro para o designer de tabela"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="constructing-filter-strings-for-the-table-designer"></a>Construindo cadeias de caracteres de filtro para o Designer de tabela

## <a name="overview"></a>Visão geral

Para filtrar dados em uma tabela do Azure que é exibido no Visual Studio **Designer de tabela**, você construir uma cadeia de caracteres de filtro e inseri-lo no campo de filtro. A sintaxe da cadeia de caracteres de filtro é definida pelo WCF Data Services e é semelhante a uma cláusula SQL WHERE, mas é enviada para o serviço de tabela por meio de uma solicitação HTTP. O **Designer de tabela** trata a codificação adequada para você, portanto, para filtrar um valor de propriedade desejada, você só precisa digitar o nome da propriedade, operador de comparação, valor do critério e opcionalmente, booliano operador no campo de filtro. Você não precisa incluir a opção de consulta $filter como você faria se você estava criando uma URL para consultar a tabela via a [Referência de API do restante de serviços de armazenamento](http://go.microsoft.com/fwlink/p/?LinkId=400447).

Os serviços de dados WCF são baseados no [Open Data Protocol](http://go.microsoft.com/fwlink/p/?LinkId=214805) (OData). Para obter detalhes sobre a opção de consulta de sistema do filtro (**$filter**), consulte a [especificação de convenções de URI do OData](http://go.microsoft.com/fwlink/p/?LinkId=214806).

## <a name="comparison-operators"></a>Operadores de comparação

Os seguintes operadores lógicos são suportados para todos os tipos de propriedade:

|Operador lógico|Descrição|Cadeia de caracteres de filtro de exemplo|
|---|---|---|
|EQ|Igual|Cidade eq 'Redmond'|
|gt|Maior que|Preço gt 20|
|GE|Maior que ou igual a|Preço ge 10|
|lt|Menor que|Preço lt 20|
|entidade legal|Menor ou igual|Entidade legal preço 100|
|ne|Não é igual|Cidade ne 'Londres'|
|e|E|Entidade legal preço 200 e preço gt 3.5|
|ou|Ou|Entidade legal preço 3.5 ou preço gt 200|
|não|Não|não isAvailable|

Ao construir uma cadeia de caracteres de filtro, as seguintes regras são importantes:

- Use os operadores lógicos para comparar uma propriedade para um valor. Observe que não é possível comparar uma propriedade para um valor dinâmico; um lado da expressão deve ser uma constante.

- Todas as partes da cadeia de caracteres filtro diferenciam maiusculas de minúsculas.

- O valor da constante deve ser do mesmo tipo de dados como a propriedade na ordem para o filtro para retornar resultados válidos. Para obter mais informações sobre tipos de propriedade com suporte, consulte [Noções básicas sobre o modelo de dados do serviço de tabela](http://go.microsoft.com/fwlink/p/?LinkId=400448).

## <a name="filtering-on-string-properties"></a>Filtrando as propriedades de cadeia de caracteres

Quando você filtra as propriedades de cadeia de caracteres, coloque-a constante de cadeia de caracteres entre aspas simples.

A exemplo a seguir filtra nas propriedades **PartitionKey** e **RowKey** ; propriedades não-chave adicionais também podem ser adicionadas na cadeia de caracteres de filtro:

    PartitionKey eq 'Partition1' and RowKey eq '00001'

Você pode colocar cada expressão de filtro entre parênteses, embora não seja obrigatório:

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

Observe que o serviço de tabela não dá suporte a consultas de curinga, e eles não são compatíveis com o Designer de tabela ou. No entanto, você pode executar o prefixo correspondência usando operadores de comparação no prefixo desejado. O exemplo a seguir retorna entidades com um início de propriedade sobrenome com a letra 'A':

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>Filtrando propriedades numéricas

Para filtrar em um número inteiro ou ponto flutuante, especifique o número sem aspas.

Este exemplo retorna todas as entidades com uma propriedade de idade cujo valor é maior do que 30:

    Age gt 30

Este exemplo retorna todas as entidades com uma propriedade de AmountDue cujo valor é menor ou igual a 100.25:

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>Filtrando propriedades booleanas

Para filtrar um valor booliano, especifique **true** ou **false** sem aspas.

O exemplo a seguir retorna todas as entidades onde a propriedade IsActive é definida como **true**:

    IsActive eq true

Você também pode escrever essa expressão de filtro sem o operador lógico. No exemplo a seguir, o serviço de tabela também retornará todas as entidades onde IsActive for **verdadeira**:

    IsActive

Para retornar todas as entidades onde IsActive for falsa, você pode usar o não operador:

    not IsActive

## <a name="filtering-on-datetime-properties"></a>Filtrando as propriedades de DateTime

Para filtrar um valor DateTime, especifique a palavra-chave **datetime** , seguida a constante de data/hora aspas simples. A constante de data/hora deve estar no formato de UTC combinado, conforme descrito na [Formatação valores de propriedade de DateTime](http://go.microsoft.com/fwlink/p/?LinkId=400449).

O exemplo a seguir retorna entidades onde a propriedade de CustomerSince é igual a 10 de julho de 2008:

    CustomerSince eq datetime'2008-07-10T00:00:00Z'
