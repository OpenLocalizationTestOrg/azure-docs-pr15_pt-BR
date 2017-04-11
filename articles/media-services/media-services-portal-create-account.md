<properties
    pageTitle=" Crie uma conta de serviços de mídia do Azure com o portal do Azure | Microsoft Azure"
    description="Este tutorial orienta você pelas etapas de criação de uma conta de serviços de mídia do Azure com o portal do Azure."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/24/2016"
    ms.author="juliako"/>


# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Crie uma conta de serviços de mídia do Azure usando o portal do Azure

> [AZURE.SELECTOR]
- [Portal](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [RESTANTE](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

O portal Azure fornece uma maneira de criar rapidamente uma conta de serviços de mídia do Azure (AMS). Você pode usar sua conta para acessar os serviços de mídia que permitem armazenar, criptografar, codificar, gerenciar e transmitir conteúdo de mídia no Azure. No momento em que você cria uma conta de serviços de mídia, você também criar uma conta de armazenamento associado (ou use um existente) na mesma região geográfica como a conta de serviços de mídia.

Este artigo explica alguns conceitos comuns e mostra como criar uma conta de serviços de mídia com o portal do Azure.

## <a name="concepts"></a>Conceitos

Acessar serviços de mídia requer duas contas associadas:

- Uma conta de serviços de mídia. Sua conta lhe dá acesso a um conjunto de serviços de mídia baseado em nuvem que estão disponíveis no Azure. Uma conta de serviços de mídia não armazenar conteúdo de mídia real. Em vez disso, ele armazena metadados sobre o conteúdo de mídia e trabalhos de processamento de mídia em sua conta. No momento em que você cria a conta, selecione uma região de serviços de mídia disponível. A região que você seleciona é uma central de dados que armazena os registros de metadados para a sua conta.

    Regiões de serviços de mídia (AMS) disponíveis incluem o seguinte: norte da Europa, Europa Ocidental, oeste EUA, Leste EUA, Sudeste Asiático, da Ásia Oriental, oeste Japão Japão Leste. Serviços de mídia não usar grupos de afinidade.
    
    AMS agora também está disponível nos seguintes data centers: Sul Brasil, Índia Oeste, Índia Sul e Índia Central. Agora você pode usar o portal do Azure para criar contas de serviço de mídia e realizar várias tarefas descritas aqui. No entanto, o Live codificação não está habilitado nesses centros de dados. Além disso, nem todos os tipos de codificação reservadas unidades estão disponíveis nesses centros de dados.
    
    - Brasil Sul: Apenas padrão e unidades reservadas codificação básicas estão disponíveis.
    - Índia Oeste Índia Sul: 

- Uma conta de armazenamento do Azure. Contas de armazenamento devem estar localizadas na mesma região geográfica como a conta de serviços de mídia. Quando você cria uma conta de serviços de mídia, você pode escolher uma conta existente do armazenamento na mesma região, ou você pode criar uma nova conta de armazenamento na mesma região. Se você excluir uma conta de serviços de mídia, os blobs em sua conta de armazenamento relacionados não são excluídos.

## <a name="create-an-ams-account"></a>Criar uma conta de AMS

As etapas desta seção mostram como criar uma conta de AMS.

1. Faça logon no [portal do Azure](https://portal.azure.com/).
2. Clique em **+ novo** > **Web + Mobile** > **Serviços de mídia**.

    ![Criam serviços de mídia](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Em **Criar conta de serviços de mídia** insira valores necessários.

    ![Criam serviços de mídia](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Em **Nome da conta**, insira o nome da nova conta AMS. Um nome de conta de serviços de mídia é todos os números em minúsculas ou letras sem espaços e caracteres de 3 a 24 de comprimento.
    2. Na assinatura, selecione entre as diferentes assinaturas do Azure que você tem acesso a.
    
    2. No **Grupo de recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é um conjunto de recursos que compartilham o ciclo de vida, permissões e políticas. Saiba mais [aqui](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. Em **local**, selecione a região geográfica que será usada para armazenar os registros de mídia e metadados de sua conta de serviços de mídia. Essa região será usado para processar e transmitir sua mídia. Apenas as regiões de serviços de mídia disponíveis aparecem na caixa de listagem suspensa. 
    
    3. Na **Conta de armazenamento**, selecione uma conta de armazenamento para fornecer armazenamento de blob do conteúdo mídia de sua conta de serviços de mídia. Você pode selecionar uma conta existente do armazenamento na mesma região geográfica como sua conta de serviços de mídia, ou você pode criar uma conta de armazenamento. Uma nova conta de armazenamento é criada na mesma região. As regras para nomes de contas de armazenamento são as mesmas contas de serviços de mídia.

        Saiba mais sobre armazenamento [aqui](storage-introduction.md).

    4. Selecione **Fixar no painel de controle** para ver o progresso da implantação conta.
    
7. Clique em **criar** na parte inferior do formulário.

    Depois que a conta é criada com êxito, o status é alterado para **execução**. 

    ![Configurações de serviços de mídia](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Para gerenciar sua conta de AMS (por exemplo, carregar vídeos, codificar ativos, monitorar o progresso de tarefa) use a janela **configurações** .

## <a name="manage-keys"></a>Gerenciar chaves

É necessário o nome da conta e as informações da chave primárias para programaticamente acessar a conta de serviços de mídia.

1. No portal do Azure, selecione sua conta. 

    A janela **configurações** é exibida à direita. 

2. Na janela **configurações** , selecione **chaves**. 

    As janelas de **Gerenciar chaves** mostra o nome da conta e as chaves primárias e secundárias é exibida. 
3. Pressione o botão Copiar para copiar os valores.
    
    ![Teclas de serviços de mídia](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="next-steps"></a>Próximas etapas

Agora você pode carregar arquivos para sua conta de AMS. Para obter mais informações, consulte [carregar arquivos](media-services-portal-upload-files.md).

## <a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


