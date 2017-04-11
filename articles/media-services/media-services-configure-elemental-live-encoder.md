<properties 
    pageTitle="Configurar o codificador Live primários para enviar um fluxo ao vivo de taxa de bits única | Microsoft Azure" 
    description="Este tópico mostra como configurar o codificador Live primários para enviar um fluxo de taxa de bits única para canais de AMS habilitados para codificação ao vivo." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="cenkdin;anilmur;juliako"/>

#<a name="use-the-elemental-live-encoder-to-send-a-single-bitrate-live-stream"></a>Use o codificador Live primários para enviar um fluxo de taxa de bits única ao vivo

> [AZURE.SELECTOR]
- [Primários ao vivo](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

Este tópico mostra como configurar o codificador [Live primários](http://www.elementaltechnologies.com/products/elemental-live) para enviar um fluxo de taxa de bits única para canais de AMS habilitados para codificação ao vivo.  Para obter mais informações, consulte [Trabalhando com canais que estão habilitados para executar Live codificação com serviços de mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerenciar serviços de mídia do Azure (AMS) com a ferramenta de Azure Media Services Explorer (AMSE). Esta ferramenta só é executado no PC com Windows. Se você estiver em Mac ou Linux, use o portal do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).

##<a name="prerequisites"></a>Pré-requisitos

- Deve ter um conhecimento funcional do usando o Live primários interface da web para criar eventos ao vivo.
- [Criar uma conta de serviços de mídia do Azure](media-services-portal-create-account.md)
- Certifique-se de que não há um ponto de extremidade Streaming executando com pelo menos uma unidade de streaming alocada. Para obter mais informações, consulte [Gerenciar Streaming pontos de extremidade de uma conta de serviços de mídia](media-services-portal-manage-streaming-endpoints.md).
- Instale a versão mais recente da ferramenta [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
- Inicie a ferramenta e conectar à sua conta de AMS.

##<a name="tips"></a>Dicas

- Sempre que possível, use uma conexão de internet conectada.
- É uma boa de regra ao determinar os requisitos de largura de banda duas vezes o streaming bitrates. Enquanto isso não é uma exigência obrigatória, ela ajuda a reduzir o impacto do congestionamento da rede.
- Quando usar o software baseado codificadores, feche qualquer programa desnecessário.

## <a name="elemental-live-with-rtp-ingest"></a>Inclusão de primários Live com RTP

Esta seção mostra como configurar o codificador Live primários que envia um fluxo de taxa de bits única ao vivo através de RTP.  Para obter mais informações, consulte [fluxo MPEG-TS sobre RTP](media-services-manage-live-encoder-enabled-channels.md#channel).

### <a name="create-a-channel"></a>Criar um canal

1.  Na ferramenta de AMSE, navegue até a guia **Live** e clique com o botão direito dentro da área de canal. Selecione **criar canal …** no menu.

![Primários](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Especifique um nome de canal, o campo Descrição é opcional. Em configurações de canal, selecione **padrão** para a opção Live codificação, com o protocolo de entrada definida como **RTP (MPEG-TS)**. Você pode deixar todas as outras configurações como ela está.


Certifique-se de **Iniciar o novo canal agora** está selecionada.

3. Clique em **criar canal**.
![Primários](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

>[AZURE.NOTE] O canal pode levar até 20 minutos para iniciar.

Enquanto estiver iniciando o canal, você pode [Configurar o codificador](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

>[AZURE.IMPORTANT] Observe que a cobrança começa assim que o canal vai em um estado pronto. Para obter mais informações, consulte [Estados do canal](media-services-manage-live-encoder-enabled-channels.md#states).

###<a id=configure_elemental_rtp></a>Configurar o codificador Live primários 

Neste tutorial são usadas as seguintes configurações de saída. O restante desta seção descreve as etapas de configuração mais detalhadamente. 

**Vídeo**:
 
- Codec: h. 264 
- Perfil: Alto (nível 4.0) 
- Taxa de bits: 5000 kbps 
- Quadro chave: 2 segundos (60 segundos) 
- Taxa de quadro: 30
 
**Áudio**:

- Codec: AAC (LC) 
- Taxa de bits: 192 kbps 
- Taxa de amostragem: 44,1 kHz


####<a name="configuration-steps"></a>Etapas de configuração

1. Navegue para a interface de web **Live primários** e configurar o codificador do **UDP/TS** streaming. 

2. Após criar um novo evento, role para baixo até os grupos de saída e adicione o grupo de saída **UDP/TS** . 

3. Crie uma nova saída selecionando **novo fluxo** e, em seguida, clicando em **Adicionar saída**.  
    
    ![Primários](./media/media-services-elemental-live-encoder/media-services-elemental13.png)
    
    >[AZURE.NOTE] É recomendável que o evento primários tem o código de tempo definido como "Relógio do sistema" para ajudar o codificador se reconectar no caso de uma falha do fluxo.

4. Agora que a saída tiver sido criada, clique em **Adicionar fluxo**. As configurações de saída agora podem ser configuradas. 
5. Role para baixo até o "fluxo 1" que acabou de ser criado, clique na guia **vídeo** à esquerda e expanda a seção de configurações **avançadas** . 

    ![Primários](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    Enquanto Live primários tem uma ampla variedade de personalização disponíveis, as configurações a seguir são recomendadas para introdução ao fluxo para AMS. 
    
    - Resolução: 1280 x 720 
    - Taxa de quadros: 30 
    - Tamanho de GOP: 60 quadros 
    - Modo de entrelaçamento: progressivo 
    - Taxa de bits: 5000000 bit/s (Isso pode ser ajustado com base nas limitações de rede) 
    

    ![Primários](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

6. Obter a URL de entrada do canal.
    
    Navegue de volta para a ferramenta AMSE e verificar o status de conclusão de canal. Depois que o estado mudou de **Iniciando** **em execução**, você pode obter a URL de entrada.
      
    Quando o canal está em execução, clique com botão direito no nome do canal, navegue para baixo até hover sobre **URL de entrada de copiar para área de transferência** e selecione **URL de entrada principal**.  
    
    ![Primários](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
    
1. Cole estas informações no campo de **Destino principal** a primários. Todas as outras configurações podem permanecer padrão.
    
    ![Primários](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    Para redundância extra, repita essas etapas com a URL de entrada secundário criando uma guia de "Saída" separada para UDP/TS Streaming.
    
7. Clique em **criar** (se um novo evento foi criado) ou **Atualizar** (se editando um evento já existente) e vá para iniciar o codificador. 

>[AZURE.IMPORTANT]Antes de clicar em **Iniciar** em Live primários da interface da web, você **deve** garantir que o canal está pronto. 
>Além disso, certifique-se para não deixar o canal em um estado pronto sem um evento por mais de > 15 minutos.

Após o fluxo de execução por 30 segundos, navegar de volta para a reprodução de ferramenta e teste AMSE.  

###<a name="test-playback"></a>Reprodução de teste
  
1. Navegue até a ferramenta AMSE e clique com botão direito do canal a ser testado. No menu, passe o mouse sobre **a visualização de reprodução** e selecione **com o Azure Media Player**.  

    ![Primários](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Se o fluxo de aparecer no player, em seguida, o codificador foi corretamente configurado para se conectar ao AMS. 

Se um erro for recebido, o canal precisará ser redefinidas e configurações de codificador ajustadas. Consulte o tópico de [solução de problemas](media-services-troubleshooting-live-streaming.md) para obter orientações.   

###<a name="create-a-program"></a>Criar um programa

1. Depois de reprodução do canal é confirmada, crie um programa. Na guia **Live** na ferramenta de AMSE, clique com o botão direito dentro da área de programa e selecione **Criar novo programa**.  

    ![Primários](./media/media-services-elemental-live-encoder/media-services-elemental9.png)

2. Nome do programa e, se necessário, ajuste o **Comprimento da janela de arquivamento** (que usa como padrão 4 horas). Você também pode especificar um local de armazenamento ou deixe como padrão.  
3. Marque a caixa **Iniciar o programa agora** .
4. Clique em **Criar programa**.  
  
    Observação: A criação do programa leva menos tempo do que a criação de canal.    
 
5. Após a execução do programa, confirme reprodução direita clicando no programa e navegando para **reprodução os programas** e selecionando **com o Azure Media Player**.  
6. Depois de confirmar, clique com botão direito do programa novamente e selecione **copiar a URL de saída para área de transferência** (ou recuperar essas informações da opção no menu **configurações e informações de programa** ). 

O fluxo está pronto para ser inserido em um player ou distribuído para uma audiência para visualização ao vivo.  

## <a name="troubleshooting"></a>Solução de problemas

Consulte o tópico de [solução de problemas](media-services-troubleshooting-live-streaming.md) para obter orientações. 


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
