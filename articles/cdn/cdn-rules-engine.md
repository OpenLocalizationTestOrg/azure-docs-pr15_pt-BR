<properties
    pageTitle="Substituindo o comportamento HTTP padrão no Azure CDN usando o mecanismo de regras | Microsoft Azure"
    description="O mecanismo de regras permite que você personalize como solicitações HTTP são tratadas por Azure CDN, como bloquear a entrega de determinados tipos de conteúdo, defina uma política de cache e modificar cabeçalhos HTTP."
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

# <a name="override-default-http-behavior-using-the-rules-engine"></a>Substituir comportamento HTTP padrão usando o mecanismo de regras

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Visão geral

O mecanismo de regras permite que você personalize como solicitações HTTP são tratadas como bloquear a entrega de determinados tipos de conteúdo, definindo uma política de cache e modificando cabeçalhos HTTP.  Este tutorial demonstraremos a criação de uma regra que alterará o comportamento de cache de ativos CDN.  Também há conteúdo de vídeo disponível na seção "[Consulte também](#see-also)".

## <a name="tutorial"></a>Tutorial

1. Da lâmina CDN perfil, clique no botão **Gerenciar** .

    ![Botão de gerenciar blade de perfil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)

    O portal de gerenciamento de CDN é aberta.

2. Clique na guia **HTTP grande** , seguida pelo **Mecanismo de regras**.

    Opções para uma nova regra são exibidas.

    ![Novas opções de regra CDN](./media/cdn-rules-engine/cdn-new-rule.png)

    >[AZURE.IMPORTANT] A ordem na qual várias regras são listadas afeta como eles são tratados. Uma regra subsequente pode substituir as ações especificadas por uma regra anterior.
    
3. Insira um nome no **nome / descrição** caixa de texto.

4. Identifique o tipo de solicitações que a regra será aplicada.  Por padrão, a condição de correspondência **sempre** é selecionada.  Você vai usar **sempre** para este tutorial, portanto, deixe que selecionou.

    ![Condição de correspondência CDN](./media/cdn-rules-engine/cdn-request-type.png)

    >[AZURE.TIP] Há muitos tipos de correspondência condições disponíveis na lista suspensa.  Clicando no ícone azul informativo à esquerda da condição correspondência mostrará a condição atualmente selecionada em detalhes.
    >
    >Para a lista completa de condições de correspondência em detalhes, consulte [condição de corresponder ao mecanismo de regras e detalhes do recurso](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_0).

5.  Clique na **+** botão ao lado de **recursos** para adicionar um novo recurso.  Na lista suspensa à esquerda, selecione **Forçar interno Max-idade**.  Na caixa de texto que aparece, digite **300**.  Deixe os valores padrão restantes.

    ![Recurso CDN](./media/cdn-rules-engine/cdn-new-feature.png)

    >[AZURE.NOTE] Como com condições de correspondência, clicando no ícone informativo azul à esquerda do novo recurso para exibir detalhes sobre esse recurso.  No caso de **Forçar interno-idade máxima**, podemos está substituindo cabeçalhos de **Cache-Control** e **expira** do ativo para controlar quando o nó de borda CDN irá atualizar o ativo a partir da origem.  Nosso exemplo de 300 segundos significa que o nó de borda CDN será o cache ativo por 5 minutos antes de atualizar o ativo de sua origem.
    >
    >Para a lista completa de recursos em detalhes, consulte [condição de correspondência do mecanismo de regras e detalhes do recurso](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1).

6.  Clique no botão **Adicionar** para salvar a nova regra.  A nova regra agora está aguardando aprovação. Depois que ele tiver sido aprovado, o status será alterado de **Pendente XML** para **XML ativa**.

    >[AZURE.IMPORTANT] Alterações de regras podem levar até 90 minutos para propagar através a CDN.

## <a name="see-also"></a>Consulte também
* [Sexta-feira azure: novos Premium recursos poderosos do Azure CDN](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (vídeo)
* [Condição de correspondência do mecanismo de regras e detalhes do recurso](https://msdn.microsoft.com/library/mt757336.aspx)
