<properties
    pageTitle="Como solucionar problemas de portal DocumentDB | Microsoft Azure"
    description="Descubra para resolver problemas no portal do DocumentDB Azure." 
    services="documentdb"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="mimig"/>

# <a name="azure-documentdb-portal-troubleshooting-tips"></a>Azure portal DocumentDB dicas de solução de problemas

Este artigo descreve como resolver problemas de DocumentDB no portal do Azure. 

## <a name="resources-are-missing"></a>Recursos estão faltando

**Sintoma**: bancos de dados ou coleções estão falta no seu portal lâminas.

**Solução**: reduzir o uso do aplicativo para operar sob a cota de transferência máxima para o conjunto. 

**Explicação**: O portal é um aplicativo como qualquer outro, fazer chamadas para seu banco de dados de DocumentDB e o conjunto. Se suas solicitações atualmente estão sendo limitadas devido a chamadas sejam feitas a partir de um aplicativo separado, o portal também pode ser limitado, causando recursos não apareça no portal. Para resolver o problema, a causa do uso da taxa de transferência alta de endereço e depois atualize a lâmina portal. Obter informações sobre como medir e o uso de produtividade inferior podem ser encontradas na seção de [produtividade](documentdb-performance-tips.md#throughput) do artigo [dicas de desempenho](documentdb-performance-tips.md) .
 
## <a name="pages-or-blades-wont-load"></a>Páginas ou blades não carrega

**Sintoma**: páginas e lâminas no portal não são exibidos.

**Solução**: reduzir o uso do aplicativo para operar sob a cota de transferência máxima para o conjunto. 

**Explicação**: O portal é um aplicativo como qualquer outro, fazer chamadas para seu banco de dados de DocumentDB e o conjunto. Se suas solicitações atualmente estão sendo limitadas devido a chamadas sejam feitas a partir de um aplicativo separado, o portal também pode ser limitado, causando recursos não apareça no portal. Para resolver o problema, a causa do uso da taxa de transferência alta de endereço e depois atualize a lâmina portal. Obter informações sobre como medir e o uso de produtividade inferior podem ser encontradas na seção de [produtividade](documentdb-performance-tips.md#throughput) do artigo [dicas de desempenho](documentdb-performance-tips.md) .

## <a name="add-collection-button-is-disabled"></a>Adicionar botão coleção está desabilitado

**Sintoma**: na lâmina banco de dados, o botão **Adicionar coleção** está desativado.

**Explicação**: se sua assinatura do Azure está associada a créditos de benefícios, como créditos gratuitos oferecida de uma assinatura do MSDN, e você usou todas as suas créditos para o mês, você não consegue criar as coleções adicionais no DocumentDB.

**Solução**: remover o limite de gastos da sua conta.

1. No portal do Azure, em Jumpbar, clique em **assinaturas**, clique na assinatura associada ao banco de dados DocumentDB e na lâmina **assinatura** , clique em **Gerenciar**. 
    ![DocumentDB oferece vários bem definidas modelos de consistência (reduzida) à sua escolha](./media/documentdb-portal-troubleshooting/documentdb-change-billing.png)

2. Nova janela do navegador, você verá que você tenha não há créditos restantes. Clique no botão **Remover o limite de gastos** para remover o gastos somente o período de faturamento atual ou indefinidamente. Conclua o Assistente para adicionar ou confirmar suas informações de cartão de crédito. 
    ![DocumentDB oferece vários bem definidas modelos de consistência (reduzida) à sua escolha](./media/documentdb-portal-troubleshooting/documentdb-remove-spending-limit.png)

 
## <a name="query-explorer-completes-with-errors"></a>Explorador de consulta for concluído com erros

Consulte [Solucionar problemas de consulta Explorer](documentdb-query-collections-query-explorer.md#troubleshoot).

## <a name="no-data-available-in-monitoring-tiles"></a>Não há dados disponíveis no monitoramento blocos

Consulte [Solucionar problemas de monitoramento blocos](documentdb-monitor-accounts.md#troubleshooting).

## <a name="no-documents-returned-in-document-explorer"></a>Não há documentos retornados no Explorador de documento

Consulte [Document Explorer de solução de problemas](documentdb-view-json-document-explorer.md#troubleshoot).

## <a name="next-steps"></a>Próximas etapas

Se você ainda estiver tendo problemas no portal do, envie um email [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) para obter assistência ou arquivo uma solicitação de suporte no portal clicando em **Procurar**, **Ajuda + suporte**, e clicando em **Criar solicitação de suporte**.
