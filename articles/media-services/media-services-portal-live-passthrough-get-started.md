<properties 
    pageTitle="Como realizar streaming ao vivo com codificadores no local usando o portal de Azure | Microsoft Azure" 
    description="Este tutorial orienta você pelas etapas de criação de um canal que está configurado para uma entrega passagem." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
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


#<a name="how-to-perform-live-streaming-with-on-premise-encoders-using-the-azure-portal"></a>Como realizar live streaming com codificadores no local usando o portal do Azure

> [AZURE.SELECTOR]
- [Portal]( media-services-portal-live-passthrough-get-started.md)
- [.NET]( media-services-dotnet-live-encode-with-onpremises-encoders.md)
- [RESTANTE]( https://msdn.microsoft.com/library/azure/dn783458.aspx)

Este tutorial orienta você pelas etapas de usando o portal do Azure para criar um **canal** que está configurado para uma entrega passagem. 

##<a name="prerequisites"></a>Pré-requisitos

A seguir é necessárias para concluir o tutorial:

- Uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta de serviços de mídia. Para criar uma conta de serviços de mídia, veja [como criar uma conta de serviços de mídia](media-services-portal-create-account.md).
- Uma webcam. Por exemplo, [Telestream Wirecast codificador](http://www.telestream.net/wirecast/overview.htm).

É altamente recomendável revisar os seguintes artigos:

- [Serviços de mídia do Microsoft Azure RTMP oferecem suporte e Live codificadores](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
- [Visão geral do Live fluxo usando os serviços de mídia do Azure](media-services-manage-channels-overview.md)
- [Live streaming com codificadores no local que criam fluxos de multi-taxa de bits](media-services-live-streaming-with-onprem-encoders.md)


##<a id="scenario"></a>Cenário de streaming live comum

As etapas a seguir descrevem tarefas envolvidas na criação live streaming aplicativos comuns que usam canais que estão configurados para entrega de passagem. Este tutorial mostra como criar e gerenciar um canal passagem e eventos ao vivo.

1. Conecte uma câmera de vídeo para um computador. Iniciar e configurar um codificador ao vivo no local que produz um fluxo RTMP ou MP4 fragmentado multi-taxa de bits. Para obter mais informações, consulte [suporte de RTMP de serviços de mídia do Azure e Live codificadores](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    Esta etapa também poderia ser executada depois de criar seu canal.

1. Criar e iniciar um canal passagem.
1. Recuperar o canal inclusão URL. 

    A URL de recebimento é usada pelo codificador ao vivo para enviar o fluxo para o canal.
1. Recupere a URL de visualização de canal. 

    Use esta URL para verificar se seu canal corretamente está recebendo o fluxo ao vivo.

3. Crie um evento ao vivo/programa. 

    Ao usar o portal do Azure, criar um evento ao vivo também cria um ativo. 
      
    >[AZURE.NOTE]Certifique-se de ter pelo menos uma unidade reservada no ponto de extremidade streaming do qual você deseja transmitir conteúdo de streaming.
1. Inicie o evento/programa quando você estiver pronto para iniciar o streaming e arquivamento.
2. Opcionalmente, o codificador ao vivo pode ser sinalizado para iniciar uma publicidade. O anúncio é inserido no fluxo de saída.
1. Pare o programa/eventos sempre que você deseja parar de streaming e o evento de arquivamento.
1. Excluir o evento/programa (e, opcionalmente, excluir o ativo).     

>[AZURE.IMPORTANT] Revise o [streaming ao vivo com codificadores no local que criam fluxos de multi-taxa de bits](media-services-live-streaming-with-onprem-encoders.md) para saber mais sobre conceitos e considerações relacionadas ao vivo streaming com codificadores no local e canais passagem.

##<a name="to-view-notifications-and-errors"></a>Para exibir erros e notificações

Se você deseja exibir erros produzidos pelo portal do Azure e notificações, clique no ícone de notificação.

![Notificações](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

##<a name="configure-streaming-endpoints"></a>Configurar pontos de extremidade streaming 

Serviços de mídia fornece pacote dinâmico, que permite que você fornecer sua MP4s multi-taxa de bits nos seguintes formatos de streaming: MPEG traço, HLS, Streaming suave ou HDS, sem precisar empacotar novamente para esses formatos de fluxo contínuo. Com embalagem dinâmica você só precisa armazenar e pagar os arquivos no formato de armazenamento único e serviços de mídia cria e serve a resposta apropriada com base em solicitações de um cliente.

Para tirar proveito da embalagem dinâmico, você precisa obter pelo menos uma unidade de streaming para o ponto de extremidade streaming do qual você planeja entrega seu conteúdo.  

Para criar e alterar o número de unidades reservadas de streaming, faça o seguinte:

1. Faça logon no [portal do Azure](https://portal.azure.com/).
1. Na janela **configurações** , clique em **pontos de extremidade de Streaming**. 

2. Clique no ponto de extremidade de streaming padrão. 

    A janela de **Detalhes de ponto de EXTREMIDADE de STREAMING de padrão** é exibida.

3. Para especificar o número de unidades streaming, deslize o controle deslizante de **unidades de Streaming** .

    ![Unidades de streaming](./media/media-services-portal-passthrough-get-started/media-services-streaming-units.png)

4. Clique no botão **Salvar** para salvar as alterações.

    >[AZURE.NOTE]A alocação de qualquer novas unidades pode levar até 20 minutos para ser concluída.
    
##<a name="create-and-start-pass-through-channels-and-events"></a>Criar e iniciar eventos e canais de passagem

Um canal está associado a/programas de eventos que permitem que você controlar a publicação e armazenamento de segmentos em um fluxo ao vivo. Canais gerenciar eventos. 
    
Você pode especificar o número de horas que você deseja manter o conteúdo registrado para o programa definindo o tamanho da **Janela de arquivamento** . Esse valor pode ser definido entre o mínimo de 5 minutos a um máximo de 25 horas. Janela de tamanho de arquivo morto também determina que a quantidade máxima de clientes de tempo pode buscar Voltar no tempo da posição atual do live. Eventos podem ser executados sobre o período de tempo especificado, mas o conteúdo que estiver atrás o comprimento da janela continuamente é descartado. Este valor desta propriedade também determina quanto tempo os manifestos do cliente podem crescer.

Cada evento está associado um ativo. Para publicar o evento, você deve criar um localizador de OnDemand do ativo associado. Ter esse localizador permite que você crie uma URL de streaming que você pode fornecer aos seus clientes.

Um canal suporta até três eventos em execução simultaneamente para poder criar vários arquivos mortos do mesmo fluxo de entrada. Isso permite que você publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, o requisito de negócios é arquivar 6 horas de um programa, mas a transmissão apenas últimos 10 minutos. Para fazer isso, você precisa criar dois programas em execução simultaneamente. Um programa está definido para arquivar 6 horas do evento, mas o programa não é publicado. Outro programa está definido como arquivamento de 10 minutos e este programa é publicado.

Você não deve reutilizar eventos ao vivo existentes. Em vez disso, criar e iniciar um novo evento para cada evento.

Inicie o evento quando estiver pronto para iniciar o streaming e arquivamento. Pare o programa sempre que você deseja parar de streaming e o evento de arquivamento. 

Para excluir o conteúdo arquivado, parar e excluir o evento e exclua o ativo associado. Um ativo não pode ser excluído se ele for usado por um evento; o evento deve ser excluído primeiro. 

Mesmo depois de você parar e excluir o evento, os usuários poderão transmitir conteúdo arquivado como um vídeo sob demanda, para desde que você não exclua o ativo.

Se quiser manter o conteúdo arquivado, mas ele não está disponível para streaming, exclua o localizador de streaming.

###<a name="to-use-the-portal-to-create-a-channel"></a>Usar o portal para criar um canal 

Esta seção mostra como usar a opção de **Criação rápida** para criar um canal passagem.

Para obter mais detalhes sobre canais passagem, consulte [Live streaming com codificadores no local que criam fluxos de multi-taxa de bits](media-services-live-streaming-with-onprem-encoders.md).

1. No [portal do Azure](https://portal.azure.com/), selecione sua conta de serviços de mídia do Azure.
2. Na janela **configurações** , clique em **Live streaming**. 

    ![Guia de Introdução](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
    
    A janela **Live streaming** aparece.

3. Clique em **Criar rápido** para criar um canal passagem com a RTMP inclusão de protocolo.

    A janela de **Criar um novo canal** é exibida.
4. Dê um nome para o novo canal e clique em **criar**. 

    Isso cria um canal passagem com a RTMP inclusão de protocolo.

##<a name="create-events"></a>Criar eventos

1. Selecione um canal ao qual você deseja adicionar um evento.
2. Pressione o botão de **Evento Live** .

![Evento](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)


##<a name="get-ingest-urls"></a>URLs de inclusão de Get

Depois que o canal é criado, você pode obter inclusão URLs que você irá fornecer com o codificador ao vivo. O codificador usa esses URLs para um fluxo ao vivo de entrada.

![Criado](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

##<a name="watch-the-event"></a>Assista o evento

Para ver o evento, clique em **inspeção** no portal do Azure ou copie a URL de streaming e use um player de sua escolha. 
 
![Criado](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

Evento ao vivo obter convertidos automaticamente ao conteúdo sob demanda quando interrompido.

##<a name="clean-up"></a>Limpar

Para obter mais detalhes sobre canais passagem, consulte [Live streaming com codificadores no local que criam fluxos de multi-taxa de bits](media-services-live-streaming-with-onprem-encoders.md).

- Um canal pode ser interrompido somente quando todos os eventos/programas no canal interromperam.  Depois que o canal for interrompido, ele não provoca todos os encargos. Quando você precisar iniciá-lo novamente, ela terá a mesma URL de inclusão, então você não precisa reconfigurar seu codificador.
- Um canal pode ser excluído somente quando todos os eventos ao vivo no canal foram excluídos.

##<a name="view-archived-content"></a>Exibir conteúdo arquivado

Mesmo depois de você parar e excluir o evento, os usuários poderão transmitir conteúdo arquivado como um vídeo sob demanda, para desde que você não exclua o ativo. Um ativo não pode ser excluído se ele for usado por um evento; o evento deve ser excluído primeiro. 

Para gerenciar os ativos, selecione a **configuração** e clique em **ativos**.

![Ativos](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

##<a name="next-step"></a>Próxima etapa

Examine os serviços de mídia caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
