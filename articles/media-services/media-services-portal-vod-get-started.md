<properties
    pageTitle=" Introdução ao fornecimento de conteúdo sob demanda usando o portal de Azure | Microsoft Azure"
    description="Este tutorial orienta você pelas etapas de implementação de um serviço de entrega de conteúdo de vídeo sob demanda (VoD) básico com o aplicativo de serviços de mídia do Azure (AMS) usando o portal do Azure."
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
    ms.date="08/30/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>Introdução ao fornecimento de conteúdo sob demanda usando o portal do Azure

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Este tutorial orienta você pelas etapas de implementação de um serviço de entrega de conteúdo de vídeo sob demanda (VoD) básico com o aplicativo de serviços de mídia do Azure (AMS) usando o portal do Azure.

> [AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

Este tutorial inclui as seguintes tarefas:

1.  Crie uma conta de serviços de mídia do Azure.
2.  Configure o ponto de extremidade de streaming.
1.  Carregar um arquivo de vídeo.
1.  Codifica o arquivo de origem em um conjunto de arquivos de MP4 adaptativa taxa de bits.
1.  Publica os ativos e obter streaming e URLs de download progressivo.  
1.  Reproduzir seu conteúdo.


## <a name="create-an-azure-media-services-account"></a>Criar uma conta de serviços de mídia do Azure

As etapas desta seção mostram como criar uma conta de AMS.

1. Faça logon no [portal do Azure](https://portal.azure.com/).
2. Clique em **+ novo** > **Web + Mobile** > **Serviços de mídia**.

    ![Criam serviços de mídia](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Em **Criar conta de serviços de mídia** insira valores necessários.

    ![Criam serviços de mídia](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Em **Nome da conta**, insira o nome da nova conta AMS. Um nome de conta de serviços de mídia é todos os números em minúsculas ou letras sem espaços e caracteres de 3 a 24 de comprimento.
    2. Na assinatura, selecione entre as diferentes assinaturas do Azure que você tem acesso a.
    
    2. No **Grupo de recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é um conjunto de recursos que compartilham o ciclo de vida, permissões e políticas. Saiba mais [aqui](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. Em **local**, selecione a região geográfica é usada para armazenar os registros de mídia e metadados de sua conta de serviços de mídia. Essa região é usado para processar e transmitir sua mídia. Apenas as regiões de serviços de mídia disponíveis aparecem na caixa de listagem suspensa. 
    
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

## <a name="configure-streaming-endpoints"></a>Configurar pontos de extremidade streaming

Ao trabalhar com serviços de mídia do Azure um dos cenários mais comuns estiver entregando vídeo por meio de taxa de bits adaptativa streaming a seus clientes. Serviços de mídia compatível com a taxa de bits adaptativa seguinte streaming tecnologias: Streaming Live HTTP (HLS), Streaming suave, MPEG traço e HDS (para somente licenciados Adobe faltava/acesso).

Serviços de mídia fornece embalagem dinâmica, que permite que você fornecer sua taxa de bits adaptativa MP4 codificado conteúdo streaming formatos compatíveis com os serviços de mídia (MPEG traço, HLS, Streaming suave, HDS) just-in-time, sem precisar armazenar predefinidas versões de cada um desses streaming formatos.

Para tirar proveito da embalagem dinâmico, você precisa fazer o seguinte:

- Codifica seu arquivo mezzanine (origem) em um conjunto de arquivos de taxa de bits adaptativa MP4 (as etapas de codificação são demonstrou posteriormente neste tutorial).  
- Crie pelo menos uma unidade streaming para o *fluxo de extremidade* do qual você planeja entrega seu conteúdo. As etapas a seguir mostram como alterar o número de unidades de streaming.

Com embalagem dinâmica, você só precisa armazenar e pagar os arquivos no formato de armazenamento único e serviços de mídia cria e serve a resposta apropriada com base em solicitações de um cliente.

Para criar e alterar o número de unidades reservadas de streaming, faça o seguinte:


1. Na janela **configurações** , clique em **pontos de extremidade de Streaming**. 

2. Clique no padrão streaming ponto de extremidade. 

    A janela de **Detalhes de ponto de EXTREMIDADE de STREAMING de padrão** é exibida.

3. Para especificar o número de unidades streaming, deslize o controle deslizante de **unidades de Streaming** .

    ![Unidades de streaming](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Clique no botão **Salvar** para salvar as alterações.

    >[AZURE.NOTE]A alocação de qualquer novas unidades pode levar até 20 minutos para ser concluída.

## <a name="upload-files"></a>Carregar arquivos

Fluxo de vídeos usando os serviços de mídia do Azure, você precisa carregar os vídeos de origem, codificá-las em vários bitrates e publicar o resultado. A primeira etapa é coberta nesta seção. 

1. Na janela **configuração** , clique em **ativos**.

    ![Carregar arquivos](./media/media-services-portal-vod-get-started/media-services-upload.png)

3. Clique no botão **carregar** .

    A janela **carregar um vídeo ativo** aparece.

    >[AZURE.NOTE] Não há nenhuma limitação de tamanho de arquivo.
    
4. Navegue até o vídeo desejado em seu computador, selecione-o e acertar Okey.  

    Inicia o carregamento e você pode ver o andamento sob o nome de arquivo.  

Quando o carregamento for concluída, você verá o novo ativo listado na janela de **ativos** . 

## <a name="encode-assets"></a>Codificar ativos

Ao trabalhar com serviços de mídia do Azure um dos cenários mais comuns estiver entregando adaptativa taxa de bits streaming a seus clientes. Serviços de mídia compatível com a taxa de bits adaptativa seguinte streaming tecnologias: Streaming Live HTTP (HLS), Streaming suave, MPEG traço e HDS (para somente licenciados Adobe faltava/acesso). Para preparar vídeos para taxa de bits adaptativa streaming, você precisa codificar o vídeo de origem em arquivos de multi-taxa de bits. Você deve usar o codificador de **Mídia codificador padrão** para codificar vídeos.  

Serviços de mídia também fornece pacote dinâmico, que permite que você fornecer sua MP4s multi-taxa de bits nos seguintes formatos de streaming: MPEG traço, HLS, Streaming suave ou HDS, sem precisar empacotar novamente para esses formatos de fluxo contínuo. Com embalagem dinâmica, você só precisa armazenar e pagar os arquivos no formato de armazenamento único e serviços de mídia cria e serve a resposta apropriada com base em solicitações de um cliente.

Para tirar proveito da embalagem dinâmico, você precisa fazer o seguinte:

- Codifica seu arquivo de origem em um conjunto de arquivos de MP4 multi-taxa de bits (as etapas de codificação são demonstrou posteriormente nesta seção).
- Obtenha pelo menos uma unidade de streaming para o ponto de extremidade streaming do qual você planeja entrega seu conteúdo. Para obter mais informações, consulte [Configurando pontos de extremidade de streaming](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

### <a name="to-use-the-portal-to-encode"></a>Usar o portal para codificar

Esta seção descreve as etapas que você pode tomar para codificar seu conteúdo com o codificador de mídia padrão.

1.  Na janela **configurações** , selecione **ativos**.  
2.  Na janela de **ativos** , selecione o ativo que você gostaria de codificar.
3.  Pressione o botão de **codificar** .
4.  Na janela **codificar um ativo** , selecione o processador "Padrão de codificador de mídia" e um valor predefinido. Por exemplo, se você souber o vídeo de entrada tem uma resolução de pixels de 1920x1080, você pode usar o "H264 taxa de vários bits 1080p" predefinido. Para obter mais informações sobre as predefinições, consulte [Este](https://msdn.microsoft.com/library/azure/mt269960.aspx) artigo – é importante selecionar o valor predefinido que é mais apropriado para o vídeo de entrada. Se você tem um vídeo de baixa resolução (640 x 360), então você não deve estar usando o padrão "H264 taxa de vários bits 1080p" predefinido.
    
    Para facilitar o gerenciamento, você tem uma opção de edição no nome do ativo saída e o nome do trabalho.
        
    ![Codificar ativos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Pressione **criar**.

### <a name="monitor-encoding-job-progress"></a>Monitorar o progresso de trabalho de codificação

Monitorar o andamento do trabalho de codificação, clique em **configurações** (na parte superior da página) e, em seguida, selecione **trabalhos**.

![Trabalhos](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Publicar conteúdo

Para fornecer uma URL que pode ser usada para transmitir ou baixar o conteúdo de usuário, primeiro é necessário "publicação" seu ativo, criando um localizador. Locators fornecem acesso a arquivos contidos em ativo. Serviços de mídia compatível com dois tipos de localizadores: 

- Streaming locators (OnDemandOrigin), usados para fluxo adaptativa (por exemplo, para fluxo MPEG traço, HLS ou Streaming suave). Para criar um localizador de streaming seu ativo deve conter um arquivo de .ism. 
- Localizadores progressivos de (SAS), usados para a entrega de vídeo por meio de download progressivo.


Uma URL de streaming tem o seguinte formato e você pode usá-lo para ser reproduzido Streaming suave ativos.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para criar um HLS streaming URL, acrescentar (formato = m3u8-aapl) para a URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para criar um traço MPEG streaming URL, acrescentar (formato = mpd-tempo-csf) para a URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Uma URL de SAS tem o seguinte formato.

    {blob container name}/{asset name}/{file name}/{SAS signature}

>[AZURE.NOTE] Se você usou o portal para criar locators antes de março de 2015, locators com uma data de validade de dois anos foram criados.  

Para atualizar a data de expiração de um localizador, use o [resto](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) ou APIs do [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) . Quando você atualiza a data de expiração de um localizador SAS, altera a URL.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Usar o portal para publicar um ativo

Para usar o portal para publicar um ativo, faça o seguinte:

1. Selecione **configurações** > **ativos**.
1. Selecione o ativo que você deseja publicar.
1. Clique no botão **Publicar** .
1. Selecione o tipo de localizador.
2. Pressione a **Adicionar**.

    ![Publicar](./media/media-services-portal-vod-get-started/media-services-publish1.png)

A URL é adicionada à lista de **URLs publicado**.

## <a name="play-content-from-the-portal"></a>Reproduzir conteúdo do portal

O portal Azure fornece um player de conteúdo que você pode usar para testar o vídeo.

Clique no vídeo desejado e clique no botão **Executar** .

![Publicar](./media/media-services-portal-vod-get-started/media-services-play.png)

Algumas considerações se aplicar:

- Verifique se que o vídeo foi publicado.
- Este **Media player** reproduz do padrão streaming ponto de extremidade. Se você deseja reproduzir a partir de um ponto de extremidade de streaming não padrão, clique em para copiar a URL e usar outro jogador. Por exemplo, [O reprodutor de serviços de mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

##<a name="next-steps"></a>Próximas etapas

Examine os serviços de mídia caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


