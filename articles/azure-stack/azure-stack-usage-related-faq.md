<properties
    pageTitle="Perguntas frequentes relacionadas uso | Microsoft Azure"
    description="Lista de medidores de pilha do Azure, comparação com a API de uso Azure, o tempo de uso e o tempo relatado, códigos de erro."
    services="azure-stack"
    documentationCenter=""
    authors="AlfredoPizzirani"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="alfredop"/>

# <a name="azure-stack-usage-api-faqs"></a>Perguntas frequentes de uso API do Azure pilha
Este artigo responde algumas perguntas frequentes sobre a API de uso do Azure pilha.

## <a name="what-meter-ids-can-i-see"></a>Quais medidor IDs pode ver?

Atualmente, uso é relatado para a rede, armazenamento e provedores de recursos de computação.

| **Provedor de recursos** | **ID do medidor** |**Nome do medidor** | **Unidade** | **Informações adicionais** |
| --------------------------- | --------------------------------------- | -------------------------- | ---------------------------- | ----------------------------------------- |
| **Rede** | f114cb19-ea64-40b5-bcd7-aee474b62853 | Uso de endereço IP público | Endereço IP |                    
| **Armazenamento**  | B4438D5D-453B-4EE1-B42A-DC72E377F1E4 | TableCapacity | GB\*horas | Capacidade total consumida por tabelas |
|              | B5C15376-6C94-4FDD-B655-1A69D138ACA3 | PageBlobCapacity | GB\*horas | Capacidade total consumida por blobs de página |
|              | B03C6AE7-B080-4BFA-84A3-22C800F315C6 | QueueCapacity  | GB\*horas  | Capacidade total consumida por fila |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 | BlockBlobCapacity | GB\*horas  | Capacidade total consumida por blobs de bloco |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 | TableTransactions  | Contagem de solicitação no 10,000s   | Solicitações de serviço de tabela (em 10,000s) |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D | TableDataTransIn | Dados de ingresso em GB | Entrada de dados de serviço de tabela em GB |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 | TableDataTransOut | Outgress em GB | Saída de dados de serviço de tabela em GB |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 | BlobTransactions | Contagem de solicitações em 10,000s | Solicitações de serviço BLOB (em 10,000s) |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810   | BlobDataTransIn    | Dados de ingresso em GB          | Entrada de dados de serviço de blob em GB 
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8   | BlobDataTransOut   | Outgress em GB              | Saída de dados de serviço de blob em GB 
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA   | QueueTransactions  | Contagem de solicitações em 10,000s   | Solicitações de serviço de fila (em 10,000s) 
| | E518E809-E369-4A45-9274-2017B29FFF25   | QueueDataTransIn          | Dados de ingresso em GB         | Entrada de dados de serviço de fila em GB 
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2   | QueueDataTransOut         | Outgress em GB  | Saída de dados de serviço de fila em GB 
| **Calcular** | 6DAB500F-A4FD-49C4-956D-229BB9C8C793 | Horas de tamanho de máquina virtual | Tamanho da máquina virtual |



## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Como as APIs de uso do Azure pilha comparam à [API de uso do Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (atualmente na visualização pública)?

-   A API do uso de locatário é consistente com a API do Azure, com uma exceção: o sinalizador *showDetails* atualmente não é suportado no Azure pilha.

-   A API de uso do provedor se aplica somente a pilha do Azure.

-   Atualmente, a [API RateCard](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) que está disponível no Azure não está disponível na pilha do Azure.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Qual é a diferença entre a hora de uso e relatado?

Relatórios de uso de dados têm dois valores de tempo principal:

-   **Tempo informado**. O tempo em que o evento de uso inserido o sistema de uso

-   **Tempo de uso**. A hora quando o recurso de pilha Azure foi consumido

Talvez você veja uma inconsistência de valores de tempo de uso e o tempo relatado para um evento de uso específico. O atraso pode ser contanto que várias horas em qualquer ambiente.

Atualmente, você pode consultar *apenas por tempo relatado*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>O que significam esses códigos de erro de API de uso?

| **Código de status HTTP** | **Código de erro** | **Descrição** |
| ---------------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| Solicitação inválida/400        | *NoApiVersion*     | O parâmetro de consulta de *versão da api* está ausente.
| Solicitação inválida/400        | *InvalidProperty*  | A propriedade está ausente ou tem um valor inválido. A mensagem no código de erro no corpo da resposta identifica a propriedade ausente.
| Solicitação inválida/400        | *RequestEndTimeIsInFuture*  | O valor de *ReportedEndTime* for no futuro. Valores no futuro não são permitidos para este argumento.
| Solicitação inválida/400        | *SubscriberIdIsNotDirectTenant*    | Uma chamada de API de provedor usado uma ID da assinatura que não seja um locatário válido do chamador.
| Solicitação inválida/400        | *SubscriptionIdMissingInRequest*   | A ID da assinatura do chamador está ausente.
| Solicitação inválida/400        | *InvalidAggregationGranularity*   | Um detalhamento de agregação inválida foi solicitado. Valores válidos são diária e por hora.
| 503                    | *ServiceUnavailable*   | Ocorreu um erro de nova tentativa porque o serviço está ocupado ou a chamada está sendo limitada. |

## <a name="next-steps"></a>Próximas etapas
[Cliente de cobrança e devolução na pilha do Azure](azure-stack-billing-and-chargeback.md)

[Uso de recursos de provedor API](azure-stack-provider-resource-api.md)

[API de uso do recurso de locatário](azure-stack-tenant-resource-usage-api.md)
