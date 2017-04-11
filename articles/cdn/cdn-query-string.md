<properties
    pageTitle="Controlar o comportamento de solicitações com cadeias de caracteres de consulta do cache de CDN do Azure | Microsoft Azure"
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

#<a name="controlling-caching-behavior-of-cdn-requests-with-query-strings"></a>Controlar o comportamento de cache de solicitações CDN com cadeias de caracteres de consulta

> [AZURE.SELECTOR]
- [Padrão](cdn-query-string.md)
- [Azure CDN Premium da Verizon](cdn-query-string-premium.md)

##<a name="overview"></a>Visão geral

Cadeia de caracteres de consulta cache controles como arquivos devem ser armazenados em cache quando eles contêm cadeias de caracteres de consulta.

> [AZURE.IMPORTANT] Os produtos padrão e Premium CDN fornecem a mesma funcionalidade de cache de sequência de consulta, mas é diferente da interface do usuário.  Este documento descreve a interface do ** **Azure CDN do Akamai Azure CDN padrão**e da Verizon** .  Para o cache de cadeia de caracteres de consulta com o **Azure CDN Premium da Verizon**, consulte [Controlando comportamento de cache de solicitações CDN com cadeias de caracteres de consulta - Premium](cdn-query-string-premium.md).

Três modos estão disponíveis:

- **Cadeias de caracteres de consulta de ignorar**: Este é o modo padrão.  O nó de borda CDN passará a cadeia de caracteres de consulta do solicitante para a origem na primeira solicitação e cache ativo.  Todas as solicitações subsequentes para esse ativo servidas do nó borda irá ignorar a cadeia de caracteres de consulta até que o cache ativo expire.
- **Ignorar o cache de URL com cadeias de caracteres de consulta**: nesse modo, solicitações com cadeias de caracteres de consulta não são armazenadas em cache no nó CDN borda.  O nó de borda recupera o ativo diretamente a partir de origem e passa ao solicitante com cada solicitação.
- **Cada URL exclusivo em cache**: este modo trata cada solicitação com uma cadeia de caracteres de consulta como um ativo exclusivo com seu próprio cache.  Por exemplo, a resposta a partir da origem de uma solicitação de *foo.ashx?q=bar* seria ser armazenados em cache no nó borda e retornada para caches subsequentes com essa cadeia de caracteres de consulta mesmo.  Uma solicitação para *foo.ashx?q=somethingelse* poderia ser armazenados em cache como um ativo separado com seu próprio tempo Live.

##<a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Alterando as configurações para padrão CDN perfis de cache de sequência de consulta

1. Da lâmina CDN perfil, clique na extremidade CDN que você deseja gerenciar.

    ![Pontos de extremidade do CDN perfil blade](./media/cdn-query-string/cdn-endpoints.png)

    A lâmina de ponto de extremidade CDN é aberta.

2. Clique no botão **Configurar** .

    ![Botão de gerenciar blade de perfil CDN](./media/cdn-query-string/cdn-config-btn.png)

    A lâmina de configuração de CDN é aberta.

3. Selecione uma configuração na lista suspensa de **cadeia de caracteres de consulta comportamento do cache** .

    ![Cadeia de caracteres de consulta CDN opções de cache](./media/cdn-query-string/cdn-query-string.png)

4. Depois de fazer sua seleção, clique no botão **Salvar** .

> [AZURE.IMPORTANT] As alterações de configurações podem não ser imediatamente visíveis, como demora para o registro a se propagar através do CDN.  Para os perfis do <b>Azure CDN do Akamai</b> , propagação geralmente concluirá dentro de um minuto.  Para perfis do <b>Azure CDN da Verizon</b> , propagação geralmente concluirá dentro de 90 minutos, mas em alguns casos pode levar mais tempo.
