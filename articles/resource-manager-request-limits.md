<properties
   pageTitle="Limites de solicitação de recurso Gerenciador Azure | Microsoft Azure"
   description="Descreve como usar a otimização com o Gerenciador de recursos do Azure solicitações quando os limites de assinatura tiveram sido alcançados."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="tomfitz"/>

# <a name="throttling-resource-manager-requests"></a>A limitação de solicitações de Gerenciador de recursos

Para cada assinatura e locatário, os limites de gerente de recursos solicitações de leitura para 15.000 por hora e solicitações de gravação para 1.200 por hora. Se seu aplicativo ou script atingir esses limites, você precisa suas solicitações de aceleração. Este tópico mostra como determinar as solicitações restantes que você tem antes de atingir o limite e como responder quando você atingiu o limite.

Quando atingir o limite, você receber o código de status HTTP **muito 429 muitas solicitações**.

O número de solicitações é delimitado para sua assinatura ou seu locatário. Se você tiver vários, aplicativos simultâneos fazendo solicitações em sua assinatura, as solicitações desses aplicativos são adicionadas juntos para determinar o número de solicitações restantes.

Solicitações de assinatura com escopo são aqueles a involve passar sua id de assinatura, como recuperar o recurso grupos em sua assinatura. Solicitações de locatário escopo não inclua seu id de assinatura, como recuperar locais Azure válidos.

## <a name="remaining-requests"></a>Solicitações restantes

Você pode determinar o número de solicitações restantes examinando cabeçalhos de resposta. Cada solicitação inclui valores para o número de leitura restante e solicitações de gravação. A tabela a seguir descreve os cabeçalhos de resposta que você pode examinar esses valores:

| Cabeçalho de resposta | Descrição |
| --------------- | ----------- |
| x-MS-ratelimit-remaining-Subscription-reads | Assinatura com escopo lê restante |
| x-MS-ratelimit-remaining-Subscription-Writes | Assinatura com escopo grava restante |
| x-MS-ratelimit-remaining-tenant-reads | Escopo de locatário lê restante |
| x-MS-ratelimit-remaining-tenant-Writes | Escopo de locatário grava restante |
| x-MS-ratelimit-remaining-Subscription-Resource-Requests | Assinatura com escopo solicitações de tipo de recurso restante.<br /><br />Esse valor de cabeçalho somente é retornado se um serviço substituiu o limite padrão. Gerenciador de recursos adiciona esse valor em vez de assinatura leituras ou gravações. |
| x-MS-ratelimit-remaining-Subscription-Resource-Entities-Read | Assinatura com escopo solicitações de conjunto de tipo de recurso restante.<br /><br />Esse valor de cabeçalho somente é retornado se um serviço substituiu o limite padrão. Esse valor fornece o número de solicitações de conjunto restantes (recursos de lista). |
| x-MS-ratelimit-remaining-tenant-Resource-Requests | Solicitações de tipo de recurso com escopo de locatário restante.<br /><br />Esse cabeçalho é adicionado somente para solicitações no nível do locatário e somente se um serviço substituiu o limite padrão. Gerenciador de recursos adiciona esse valor em vez de locatário leituras ou gravações. |
| x-MS-ratelimit-remaining-tenant-Resource-Entities-Read | Locatário escopo solicitações de conjunto de tipo de recurso restante.<br /><br />Esse cabeçalho é adicionado somente para solicitações no nível do locatário e somente se um serviço substituiu o limite padrão. |

## <a name="retrieving-the-header-values"></a>Recuperar os valores de cabeçalho

Recuperar esses valores de cabeçalho no seu código ou script não será diferente de recuperação de qualquer valor de cabeçalho. 

Por exemplo, no **c#**, recuperar o valor do cabeçalho de um objeto de **HttpWebResponse** chamado **resposta** com o seguinte código:

    response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)

No **PowerShell**, recuperar o valor do cabeçalho de uma operação de chamar WebRequest.

    $r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
    $r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
    
Ou, se quiser ver as solicitações restantes para depuração, você pode fornecer a **-Depurar** parâmetro no seu cmdlet do **PowerShell** .

    Get-AzureRmResourceGroup -Debug
    
Que retorna muitas informações, incluindo o valor de resposta a seguir:

    ...
    DEBUG: ============================ HTTP RESPONSE ============================

    Status Code:
    OK

    Headers:
    Pragma                        : no-cache
    x-ms-ratelimit-remaining-subscription-reads: 14999
    ...

No **Azure CLI**, você pode recuperar o valor de cabeçalho usando a opção mais detalhada.

    azure group list -vv --json

Que retorna muitas informações, incluindo o objeto a seguir:

    ...
    silly: returnObject
    {
      "statusCode": 200,
      "header": {
        "cache-control": "no-cache",
        "pragma": "no-cache",
        "content-type": "application/json; charset=utf-8",
        "expires": "-1",
        "x-ms-ratelimit-remaining-subscription-reads": "14998",
        ...

## <a name="waiting-before-sending-next-request"></a>Aguardando antes de enviar a próxima solicitação

Quando atingir o limite de solicitação, Gerenciador de recursos retorna o código de status **429** HTTP e um **Repetir depois** valor no cabeçalho. A **Repetir depois** valor Especifica o número de segundos que o aplicativo deve aguardar (ou suspensão) antes de enviar a próxima solicitação. Se você enviar uma solicitação antes que o valor de repetição decorrido, sua solicitação não é processada e um novo valor de repetição é retornado.
