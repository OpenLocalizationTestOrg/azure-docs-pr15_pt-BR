<properties
    pageTitle="Controlando Azure CDN Premium da Verizon comportamento de solicitações com cadeias de caracteres de consulta do cache | Microsoft Azure"
    description="Sequência de consulta do Azure CDN cache controles como arquivos devem ser armazenados em cache quando eles contêm cadeias de caracteres de consulta."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

#<a name="controlling-caching-behavior-of-cdn-requests-with-query-strings---premium"></a>Controlar o comportamento de cache de solicitações CDN com cadeias de caracteres de consulta - Premium

> [AZURE.SELECTOR]
- [Padrão](cdn-query-string.md)
- [Azure CDN Premium da Verizon](cdn-query-string-premium.md)

##<a name="overview"></a>Visão geral

Cadeia de caracteres de consulta cache controles como arquivos devem ser armazenados em cache quando eles contêm cadeias de caracteres de consulta.

> [AZURE.IMPORTANT] Os produtos padrão e Premium CDN fornecem a mesma funcionalidade de cache de sequência de consulta, mas é diferente da interface do usuário.  Este documento descreve a interface do **Azure CDN Premium da Verizon**.  Para o cache de cadeia de caracteres de consulta com o ** **Azure CDN do Akamai Azure CDN padrão**e da Verizon** , consulte [Controlando comportamento de cache de solicitações CDN com cadeias de caracteres de consulta](cdn-query-string.md).

Três modos estão disponíveis:

- **cache de padrão**: Este é o modo padrão.  O nó de borda CDN passará a cadeia de caracteres de consulta do solicitante para a origem na primeira solicitação e cache ativo.  Todas as solicitações subsequentes para esse ativo servidas do nó borda irá ignorar a cadeia de caracteres de consulta até que o cache ativo expire.
- **sem cache**: nesse modo, solicitações com cadeias de caracteres de consulta não são armazenadas em cache no nó CDN borda.  O nó de borda recupera o ativo diretamente a partir de origem e passa ao solicitante com cada solicitação.
- **cache de exclusivos**: este modo trata cada solicitação com uma cadeia de caracteres de consulta como um ativo exclusivo com seu próprio cache.  Por exemplo, a resposta a partir da origem de uma solicitação de *foo.ashx?q=bar* poderia ser armazenados em cache no nó borda e retornada para caches subsequentes com essa cadeia de caracteres de consulta mesmo.  Uma solicitação para *foo.ashx?q=somethingelse* poderia ser armazenados em cache como um ativo separado com seu próprio tempo Live.

##<a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Alterando as configurações para premium CDN perfis de cache de sequência de consulta

1. Da lâmina CDN perfil, clique no botão **Gerenciar** .

    ![Botão de gerenciar blade de perfil CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)

    O portal de gerenciamento de CDN é aberta.

2. Passe o mouse sobre a guia **HTTP grandes** , em seguida, passe o mouse sobre o submenu de **Configurações de Cache** .  Clique em **cache de sequência de consulta**.

    Opções de cache de cadeia de caracteres de consulta são exibidas.

    ![Cadeia de caracteres de consulta CDN opções de cache](./media/cdn-query-string-premium/cdn-query-string.png)

3. Depois de fazer sua seleção, clique no botão de **atualização** .


> [AZURE.IMPORTANT] As alterações de configurações podem não ser imediatamente visíveis, como demora para o registro a se propagar através do CDN.  Para perfis do <b>Azure CDN da Verizon</b> , propagação geralmente concluirá dentro de 90 minutos, mas em alguns casos pode levar mais tempo.
