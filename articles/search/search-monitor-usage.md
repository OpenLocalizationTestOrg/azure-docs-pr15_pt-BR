<properties 
   pageTitle="Monitorar o uso e estatísticas em um serviço de pesquisa do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado" 
   description="Controlar o recurso consumo e tamanho de índice para pesquisa do Azure, um serviço de pesquisa de nuvem hospedado no Microsoft Azure." 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="jhubbard" 
   editor=""
   tags="azure-portal"/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required" 
   ms.date="05/17/2016"
   ms.author="heidist"/>

# <a name="monitor-usage-and-statistics-in-an-azure-search-service"></a>Monitore o uso e estatísticas em um serviço de pesquisa do Azure

Acompanhando o crescimento de índices e o tamanho do documento pode ajudá-lo a ajustar proativamente capacidade antes de atingir o limite superior que você estabeleceu para seu serviço. 

Para monitorar o uso do recurso, contagens e as estatísticas de seu serviço são facilmente visualizadas no [Portal do Azure](https://portal.azure.com), mas você também pode obter as informações por programação se você estiver criando uma ferramenta de administração do serviço personalizado. Este artigo aborda as etapas para ambas as técnicas.

Você também pode usar o novo recurso de análise de tráfego de pesquisa para aprofundar atividade no nível do índice. Visite a [Análise de tráfego de pesquisa de pesquisa do Azure](search-traffic-analytics.md) para começar.

##<a name="view-counts-and-metrics-in-the-portal"></a>Exibir contagens e métricas no portal 

1. Entre [Portal do Azure](https://portal.azure.com). 

2. Abra o painel de serviço do seu serviço de pesquisa do Azure. Blocos para o serviço podem ser encontrados na Home page, ou você pode navegar para o serviço de procurar na JumpBar. Consulte [criar um serviço](search-create-service-portal.md) para obter instruções passo a passo.

A seção de uso inclui um medidor que informa que parte dos recursos disponíveis estão em uso no momento.

  ![][1]

Lembre-se de que o serviço compartilhado tem um máximo de uma réplica e partição cada. Além disso, ele só oferece suporte para 10.000 documentos em total ou 50 MB de dados, o que vier primeiro.

##<a name="get-index-statistics-using-the-rest-api"></a>Obter estatísticas de índice usando a API REST

A API de REST de pesquisa do Azure e o SDK do .NET fornecem acesso de programação ao métricas de serviço.  Se você estiver usando [indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx) para carregar um índice de banco de dados do SQL Azure ou DocumentDB, mais uma API está disponível para obter os números que você precisar. 

  + [Obter estatísticas de índice](https://msdn.microsoft.com/library/azure/dn798942.aspx)
  + [Contagem de documentos](https://msdn.microsoft.com/library/azure/dn798924.aspx)
  + [Obter o Status do indexador](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>Próximas etapas

Analise os [limites e a capacidade](search-limits-quotas-capacity.md) para determinar a combinação de partições e réplicas que será necessário se capacidade existente for insuficiente. 

Visite [Gerenciar o serviço de pesquisa no Microsoft Azure](search-manage.md) para obter mais informações sobre a administração do serviço.

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG




 
