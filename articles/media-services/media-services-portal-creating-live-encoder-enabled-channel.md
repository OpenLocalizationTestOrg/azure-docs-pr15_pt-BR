<properties 
    pageTitle="Como realizar streaming ao vivo usando os serviços de mídia do Azure para criar fluxos de multi-taxa de bits com o portal do Azure | Microsoft Azure" 
    description="Este tutorial orienta você pelas etapas de criação de um canal que recebe um fluxo de taxa de bits única ao vivo e codifica fluxo de multi-taxa de bits usando o portal do Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
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


#<a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-the-azure-portal"></a>Como realizar streaming ao vivo usando os serviços de mídia do Azure para criar fluxos de multi-taxa de bits com o portal do Azure

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [API REST](https://msdn.microsoft.com/library/azure/dn783458.aspx)

Este tutorial orienta você pelas etapas de criação de um **canal** que recebe um fluxo de taxa de bits única ao vivo e codifica fluxo de multi-taxa de bits.

>[AZURE.NOTE]Para obter mais informações conceituais relacionadas a canais que estão habilitados para codificação ao vivo, consulte [Live streaming usando os serviços de mídia do Azure para criar fluxos de multi-taxa de bits](media-services-manage-live-encoder-enabled-channels.md).

##<a name="common-live-streaming-scenario"></a>Cenário de Streaming Live comum

A seguir estão as etapas gerais envolvidos na criação de aplicativos comuns de streaming ao vivo.

>[AZURE.NOTE] Atualmente, a duração máxima recomendada de um evento ao vivo é 8 horas. Se você precisa executar um canal para longos períodos de tempo, contate amslived no Microsoft.com.

1. Conecte uma câmera de vídeo para um computador. Inicie e configure um codificador ao vivo no local que possa um fluxo de taxa de bits única em um dos seguintes protocolos de saída: RTMP, Streaming suave ou RTP (MPEG-TS). Para obter mais informações, consulte [suporte de RTMP de serviços de mídia do Azure e Live codificadores](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    Esta etapa também poderia ser executada depois de criar seu canal.

1. Criar e iniciar um canal. 

1. Recuperar o canal inclusão URL. 

    A URL de recebimento é usada pelo codificador ao vivo para enviar o fluxo para o canal.
1. Recupere a URL de visualização de canal. 

    Use esta URL para verificar se seu canal corretamente está recebendo o fluxo ao vivo.

3. Crie um evento/programa (que também criará um ativo). 
1. Publica o evento (criará um localizador de OnDemand do ativo associado).  

    Certifique-se de ter pelo menos uma unidade reservada no ponto de extremidade streaming do qual você deseja transmitir conteúdo de streaming.
1. Inicie o evento quando estiver pronto para iniciar o streaming e arquivamento.
2. Opcionalmente, o codificador ao vivo pode ser sinalizado para iniciar uma publicidade. O anúncio é inserido no fluxo de saída.
1. Pare o evento sempre que você deseja parar de streaming e o evento de arquivamento.
1. Excluir o evento (e, opcionalmente, exclua o ativo).   

##<a name="in-this-tutorial"></a>Neste tutorial

Neste tutorial, o portal do Azure é usado para executar as seguintes tarefas: 

2.  Configure o streaming de pontos de extremidade.
3.  Crie um canal que estiver ativado para executar a codificação ao vivo.
1.  Obter a URL de inclusão para fornecer para dinâmicos codificador. O codificador ao vivo usará essa URL para o fluxo de inclusão no canal. .
1.  Criar um evento/programa (e um ativo)
1.  Publicar o ativo e obter streaming URLs  
1.  Reproduzir seu conteúdo 
2.  Limpando

##<a name="prerequisites"></a>Pré-requisitos

A seguir é necessárias para concluir o tutorial.

- Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- Uma conta de serviços de mídia. Para criar uma conta de serviços de mídia, consulte [Criar conta](media-services-portal-create-account.md).
- Uma webcam e um codificador que pode enviar um fluxo de taxa de bits única ao vivo.

##<a name="configure-streaming-endpoints"></a>Configurar pontos de extremidade streaming 

Serviços de mídia fornece embalagem dinâmica que permite que você fornecer sua MP4s multi-taxa de bits nos seguintes formatos de streaming: MPEG traço, HLS, Streaming suave ou HDS, sem precisar empacotar novamente em esses formatos de fluxo contínuo. Com embalagem dinâmica você só precisa armazenar e pagar os arquivos no formato de armazenamento único e serviços de mídia será construir e servir a resposta apropriada com base em solicitações de um cliente.

Para tirar proveito da embalagem dinâmico, você precisa obter pelo menos uma unidade de streaming para o ponto de extremidade streaming do qual você planeja entrega seu conteúdo.  

Para criar e alterar o número de unidades reservadas de streaming, faça o seguinte:

1. Logon no [portal do Azure](https://portal.azure.com/) e selecione sua conta de AMS.
1. Na janela **configurações** , clique em **pontos de extremidade de Streaming**. 

2. Clique no ponto de extremidade de streaming padrão. 

    A janela de **Detalhes de ponto de EXTREMIDADE de STREAMING de padrão** é exibida.

3. Para especificar o número de unidades streaming, deslize o controle deslizante de **unidades de Streaming** .

    ![Unidades de streaming](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-streaming-units.png)

4. Clique no botão **Salvar** para salvar as alterações.

    >[AZURE.NOTE]A alocação de qualquer novas unidades pode levar até 20 minutos para ser concluída.

##<a name="create-a-channel"></a>Criar um canal

1. No [portal do Azure](https://portal.azure.com/), selecione Serviços de mídia e clique no nome de sua conta de serviços de mídia.
2. Selecione **Live Streaming**.
3. Selecione **Criar personalizada**. Esta opção permitirá que você a criar um canal que está habilitado para codificação ao vivo.

    ![Criar um canal](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
    
4. Clique em **configurações**.
    
    1.  Escolha o tipo de canal **Live codificação** . Esse tipo Especifica que você deseja criar um canal que está habilitado para codificação ao vivo. Isso significa que a entrada fluxo de taxa de bits única é enviado para o canal e codificado em um fluxo de multi-taxa de bits usando configurações de codificador live especificado. Para obter mais informações, consulte [Live streaming usando os serviços de mídia do Azure para criar fluxos de multi-taxa de bits](media-services-manage-live-encoder-enabled-channels.md). Clique em Okey.
    2. Especifique o nome de um canal.
    3. Clique em Okey na parte inferior da tela.
    
5. Selecione a guia de **recebimento** .

    1. Nesta página, você pode selecionar um protocolo de streaming. Para o tipo de canal de **Codificação de Live** , opções de protocolo válidos são:
        
        - Taxa de bits Fragmented MP4 um único (Streaming suave)
        - Taxa de bits única RTMP
        - RTP (MPEG-TS): Fluxo de transporte MPEG-2 sobre RTP.
        
        Para obter explicações detalhadas sobre cada protocolo, consulte [Live streaming usando os serviços de mídia do Azure para criar fluxos de multi-taxa de bits](media-services-manage-live-encoder-enabled-channels.md).
    
        Você não pode alterar a opção de protocolo enquanto o canal ou seus eventos/programas associados estão em execução. Se você precisar protocolos diferentes, você deve criar canais separados para cada protocolo streaming.  

    2. Você pode aplicar restrição de IP no recebimento. 
    
        Você pode definir os endereços IP permitidos para receber um vídeo para esse canal. Permitido endereços IP podem ser especificados como um único endereço IP (por exemplo, ' 10.0.0.1'), um intervalo IP usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo IP usando um endereço IP e uma máscara de sub-rede decimal com pontos (por exemplo, ' 10.0.0.1(255.255.252.0)').

        Se nenhum endereço IP forem especificado e há uma definição de regra, em seguida, nenhum endereço IP poderão ser. Para permitir que qualquer endereço IP, crie uma regra e defina 0.0.0.0/0.

6. Na guia **Visualizar** , aplica restrição de IP na visualização.
7. Na guia **codificação** , especifique a codificação predefinir. 

    Atualmente, o único sistema valor predefinido que você pode selecionar é **padrão 720p**. Para especificar um valor de predefinido personalizado, abra um tíquete de suporte da Microsoft. Em seguida, insira o nome do predefinir criado para você. 

>[AZURE.NOTE] Atualmente, o início de canal pode levar até 30 minutos. Redefinição do canal pode levar até 5 minutos.

Depois de criado o canal, você pode clicar no canal e selecionar **configurações** onde você pode exibir suas configurações de canais. 

Para obter mais informações, consulte [Live streaming usando os serviços de mídia do Azure para criar fluxos de multi-taxa de bits](media-services-manage-live-encoder-enabled-channels.md).


##<a name="get-ingest-urls"></a>URLs de inclusão de Get

Depois que o canal é criado, você pode obter inclusão URLs que você irá fornecer com o codificador ao vivo. O codificador usa esses URLs para um fluxo ao vivo de entrada.

![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)


##<a name="create-and-manage-events"></a>Criar e gerenciar eventos

###<a name="overview"></a>Visão geral

Um canal está associado a/programas de eventos que permitem que você controlar a publicação e armazenamento de segmentos em um fluxo ao vivo. Canais gerenciar/programas de eventos. A relação de canal e programa é muito semelhante à mídia tradicional onde um canal tem um fluxo constante de conteúdo e um programa é delimitado para alguns eventos agendados naquele canal.

Você pode especificar o número de horas que você deseja manter o conteúdo registrado para o evento definindo o tamanho da **Janela de arquivamento** . Esse valor pode ser definido entre o mínimo de 5 minutos a um máximo de 25 horas. Janela de tamanho de arquivo morto também determina que a quantidade máxima de clientes de tempo pode buscar Voltar no tempo da posição atual do live. Eventos podem ser executados sobre o período de tempo especificado, mas o conteúdo que estiver atrás o comprimento da janela continuamente é descartado. Este valor desta propriedade também determina quanto tempo os manifestos do cliente podem crescer.

Cada evento está associado um ativo. Para publicar o evento, você deve criar um localizador de OnDemand do ativo associado. Ter esse localizador permitirá que você criar uma URL de streaming que você pode fornecer aos seus clientes.

Um canal suporta até três eventos em execução simultaneamente para poder criar vários arquivos mortos do mesmo fluxo de entrada. Isso permite que você publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, o requisito de negócios é arquivar 6 horas de um evento, mas a transmissão apenas últimos 10 minutos. Para fazer isso, você precisa criar dois executando simultaneamente evento. Um evento é definido para arquivar 6 horas do evento, mas o programa não é publicado. O outro evento é definido para arquivar por 10 minutos e este programa é publicado.

Você não deve reutilizar programas existentes para novos eventos. Em vez disso, criar e iniciar um novo programa para cada evento.

Inicie um programa do evento/quando estiver pronto para iniciar o streaming e arquivamento. Pare o evento sempre que você deseja parar de streaming e o evento de arquivamento. 

Para excluir o conteúdo arquivado, parar e excluir o evento e exclua o ativo associado. Um ativo não pode ser excluído se ele for usado pelo evento; o evento deve ser excluído primeiro. 

Mesmo depois de você parar e excluir o evento, os usuários poderão transmitir conteúdo arquivado como um vídeo sob demanda, para desde que você não exclua o ativo.

Se quiser manter o conteúdo arquivado, mas ele não está disponível para streaming, exclua o localizador de streaming.

###<a name="createstartstop-events"></a>Criar/iniciar/parar eventos

Depois que você tiver o fluxo fluindo para o canal, você pode começar o evento streaming pela criação de um ativo, programa e Streaming localizador. Isso arquivar o fluxo e disponibilizá-lo para visualizadores através do ponto de extremidade de Streaming. 

Há duas maneiras de iniciar o evento: 

1. Na página do **canal** , pressione **Evento Live** para adicionar um novo evento.

    Especifique: nome do evento, nome do ativo, janela de arquivamento e opção de criptografia.
    
    ![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
    
    Se você deixou **Publicar este evento ao vivo agora** marcada, o evento as URLs de publicação será obter criado.
    
    Você pode pressionar **Iniciar**, sempre que você estiver pronto para transmitir o evento.

    Depois de iniciar o evento, você pode pressionar **inspeção** para iniciar a reprodução do conteúdo.

2. Como alternativa, você pode usar um atalho e pressione o botão **Ir Live** na página de **canal** . Isso criará um padrão ativo, programa e Streaming localizador.

    O evento é nomeado **padrão** e a janela de arquivamento está definida como 8 horas.

Você pode assistir o evento publicado na página **de eventos ao vivo** . 

Se você clicar **Fora ar**, ele irá parar todos os eventos ao vivo. 


##<a name="watch-the-event"></a>Assista o evento

Para ver o evento, clique em **inspeção** no portal do Azure ou copie a URL de streaming e use um player de sua escolha. 
 
![Criado](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

Evento ao vivo converte automaticamente eventos ao conteúdo sob demanda quando interrompido.

##<a name="clean-up"></a>Limpar

Se você terminar de eventos de streaming e deseja limpar os recursos provisionados anteriormente, siga o procedimento a seguir.

- Pare de enviar o fluxo do codificador.
- Pare o canal. Depois que o canal for interrompido, ele não provocará qualquer encargos. Quando você precisar iniciá-lo novamente, ela terá a mesma URL de inclusão, então você não precisa reconfigurar seu codificador.
- Você pode parar o ponto de extremidade Streaming, a menos que você deseja continuar a fornecer arquivamento de seu evento ao vivo como um fluxo de sob demanda. Se o canal está no estado parado, ele não provocará qualquer encargos.
  
##<a name="view-archived-content"></a>Exibir conteúdo arquivado

Mesmo depois de você parar e excluir o evento, os usuários poderão transmitir conteúdo arquivado como um vídeo sob demanda, para desde que você não exclua o ativo. Um ativo não pode ser excluído se ele for usado por um evento; o evento deve ser excluído primeiro. 

Para gerenciar os ativos, selecione a **configuração** e clique em **ativos**.

![Ativos](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

##<a name="considerations"></a>Considerações

- Atualmente, a duração máxima recomendada de um evento ao vivo é 8 horas. Se você precisa executar um canal para longos períodos de tempo, contate amslived no Microsoft.com.
- Certifique-se de ter pelo menos uma unidade reservada no ponto de extremidade streaming do qual você deseja transmitir conteúdo de streaming.


##<a name="next-step"></a>Próxima etapa

Examine os serviços de mídia caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
