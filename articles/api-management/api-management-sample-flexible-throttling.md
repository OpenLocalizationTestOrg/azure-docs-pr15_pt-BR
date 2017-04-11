<properties
    pageTitle="Solicitação avançada a otimização com gerenciamento de API do Azure"
    description="Aprenda a criar e aplicar cota flexível e taxa limitando políticas de gerenciamento de API do Azure."
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>


# <a name="advanced-request-throttling-with-azure-api-management"></a>Solicitação avançada a otimização com gerenciamento de API do Azure

Ser capaz de aceleração solicitações de entrada é uma função de chave de gerenciamento de API do Azure. Duas controlando a taxa de solicitações ou solicitações/dados totais transferidos, o gerenciamento de API permite que os provedores de API proteger suas APIs abuse e criar valor para diferentes níveis de produto de API.

## <a name="product-based-throttling"></a>A otimização de produto com base
Até o momento, a limitação de taxa de recursos foram limitados aos sendo com escopo de uma assinatura específica do produto (essencialmente uma chave), definido no portal de fornecedor de gerenciamento de API. Isso é útil para o provedor de API aplicar limites os desenvolvedores que se inscreveram para usar a API, no entanto, ela não ajudar, por exemplo, na otimização de usuários finais individuais da API. É possível que o para um único usuário do aplicativo do desenvolvedor consumir a cota inteira e impedir outros clientes do desenvolvedor ser capaz de usar o aplicativo. Além disso, vários clientes que podem gerar um grande volume de solicitações podem limitar o acesso aos usuários ocasionalmente.

## <a name="custom-key-based-throttling"></a>Chave personalizada com base a otimização
As novas diretivas de [Taxa-limite-por-chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [cota por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) fornecem uma solução significativamente mais flexível para o controle de tráfego. Essas novas diretivas permitem que você defina expressões para identificar as chaves que serão usadas para acompanhar a utilização de tráfego. A maneira como isso funciona é mais fácil ilustrado com um exemplo. 

## <a name="ip-address-throttling"></a>A otimização de endereço IP
As seguintes políticas restringem um endereço IP do cliente único somente 10 chamadas para cada minuto, com um total de 1.000.000 chamadas e 10.000 quilobytes de largura de banda por mês. 

    <rate-limit-by-key  calls="10"
              renewal-period="60"
              counter-key="@(context.Request.IpAddress)" />

    <quota-by-key calls="1000000"
              bandwidth="10000"
              renewal-period="2629800"
              counter-key="@(context.Request.IpAddress)" />

Se todos os clientes na Internet usaram um endereço IP exclusivo, isso pode ser uma maneira eficaz de limitar o uso pelo usuário. Entretanto, é bastante provável que vários usuários poderão compartilhar um único endereço IP público devido a eles acesso à Internet por meio de um dispositivo NAT. Apesar disso, para APIs que permitem o acesso não autenticado a `IpAddress` podem ser a melhor opção.

## <a name="user-identity-throttling"></a>Otimização de identidade de usuário
Se um usuário final é autenticado e uma chave de otimização pode ser gerada com base nas informações que identifica exclusivamente um que usuário.

    <rate-limit-by-key calls="10"
        renewal-period="60"
        counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />

Neste exemplo, podemos extrair o cabeçalho de autorização, convertê-lo para `JWT` objeto e use o assunto do token para identificar o usuário e usá-lo como a taxa limitando chave. Se a identidade do usuário estiver armazenada na `JWT` como uma das outras declarações depois que o valor poderia ser usada em seu lugar.

## <a name="combined-policies"></a>Políticas de combinado
Embora as novas diretivas de otimização fornecem mais controle do que as políticas de otimização existentes, ainda há valor combinando os dois recursos. A otimização de chave de assinatura do produto ([taxa de chamada de limite por assinatura](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) e [definir cota de uso por assinatura](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) é uma ótima maneira de habilitar monetizing de uma API por charging com base nos níveis de uso. O controle mais refinado refinado de poder aceleração por usuário é complementar e impede que o comportamento de um usuário afetar a experiência de outra. 

## <a name="client-driven-throttling"></a>Controlado pela limitação de cliente
Quando a tecla limitação é definida usando uma [expressão de política](https://msdn.microsoft.com/library/azure/dn910913.aspx), em seguida, é o provedor de API que está escolhendo como a otimização é delimitado. Entretanto, um desenvolvedor pode querer controlar como eles taxa limite seus próprios clientes. Isso pode ser ativado pelo provedor de API introduzindo um cabeçalho personalizado para permitir que o aplicativo de cliente do desenvolvedor comunicar a chave à API.

    <rate-limit-by-key calls="100"
              renewal-period="60"
              counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>

Isso permite que o aplicativo de cliente do desenvolvedor para escolher como eles desejam criar a taxa limitando chave. Com um pouco de criatividade um desenvolvedor cliente possível criar seus próprios níveis de taxa alocar conjuntos de chaves para usuários e girando o uso da chave.

## <a name="summary"></a>Resumo
Gerenciamento de API Azure fornece taxa e otimização para proteger e adicionar valor ao seu serviço de API de cotação. As novas diretivas limitação com regras de escopo personalizadas permitem que você tenha um controle mais refinado refinado sobre essas políticas para permitir que seus clientes criar aplicativos ainda melhores. Os exemplos deste artigo demonstram o uso dessas diretivas novo pela taxa de fabricação limitando chaves com endereços IP do cliente, identidade de usuário e valores de cliente gerado. No entanto, há muitas outras partes da mensagem que pode ser usado como agente de usuário, fragmentos de caminho de URL, tamanho da mensagem.

## <a name="next-steps"></a>Próximas etapas
Envie-nos seus comentários no thread Disqus deste tópico. Seria ótimo ouvir sobre outros possíveis valores-chave que foram uma escolha lógica nos seus cenários.

## <a name="watch-a-video-overview-of-these-policies"></a>Assista a uma visão geral em vídeo dessas diretivas
Para obter mais informações sobre as políticas de [Taxa-limite-por-chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [cota por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) descritas neste artigo, assista a este vídeo.

> [AZURE.VIDEO advanced-request-throttling-with-azure-api-management]
