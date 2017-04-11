<properties 
    pageTitle="Configurar o codificador Telestream Wirecast para enviar um fluxo ao vivo de taxa de bits única | Microsoft Azure" 
    description="Este tópico mostra como configurar o codificador live Wirecast para enviar um fluxo de taxa de bits única para canais de AMS habilitados para codificação ao vivo. " 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="juliako;cenkdin;anilmur"/>

#<a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Use o codificador Wirecast para enviar um fluxo de taxa de bits única ao vivo

> [AZURE.SELECTOR]
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [Primários ao vivo](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

Este tópico mostra como configurar o codificador live [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) para enviar um fluxo de taxa de bits única para canais de AMS habilitados para codificação ao vivo.  Para obter mais informações, consulte [Trabalhando com canais que estão habilitados para executar Live codificação com serviços de mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerenciar serviços de mídia do Azure (AMS) com a ferramenta de Azure Media Services Explorer (AMSE). Esta ferramenta só é executado no PC com Windows. Se você estiver em Mac ou Linux, use o portal do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).


##<a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de serviços de mídia do Azure](media-services-portal-create-account.md)
- Certifique-se de que não há um ponto de extremidade Streaming executando com pelo menos uma unidade de streaming alocada. Para obter mais informações, consulte [Gerenciar Streaming pontos de extremidade de uma conta de serviços de mídia](media-services-portal-manage-streaming-endpoints.md)
- Instale a versão mais recente da ferramenta [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
- Inicie a ferramenta e conectar à sua conta de AMS.

##<a name="tips"></a>Dicas

- Sempre que possível, use uma conexão de internet conectada.
- É uma boa de regra ao determinar os requisitos de largura de banda duas vezes o streaming bitrates. Enquanto isso não é uma exigência obrigatória, ela ajuda a reduzir o impacto do congestionamento da rede.
- Quando usar o software baseado codificadores, feche qualquer programa desnecessário.


## <a name="create-a-channel"></a>Criar um canal

1.  Na ferramenta de AMSE, navegue até a guia **Live** e clique com o botão direito dentro da área de canal. Selecione **criar canal …** no menu.

![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Especifique um nome de canal, o campo Descrição é opcional. Em configurações de canal, selecione **padrão** para a opção Live codificação, com o protocolo de entrada definida como **RTMP**. Você pode deixar todas as outras configurações como ela está.


Certifique-se de **Iniciar o novo canal agora** está selecionada.

3. Clique em **criar canal**.
![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

>[AZURE.NOTE] O canal pode levar até 20 minutos para iniciar.

Enquanto estiver iniciando o canal, você pode [Configurar o codificador](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

>[AZURE.IMPORTANT] Observe que a cobrança começa assim que o canal vai em um estado pronto. Para obter mais informações, consulte [Estados do canal](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_wirecast_rtmp></a>Configurar o codificador Telestream Wirecast

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


###<a name="configuration-steps"></a>Etapas de configuração

1. Abra o aplicativo de Telestream Wirecast sobre o computador que está sendo usado e configurar para RTMP streaming.
2. Configure a saída navegando até a guia **saída** e selecionando **Configurações de saída...**.
    
    Verifique se que o **Destino de saída** está definido como **Servidor RTP**.
3. Clique em **Okey**.
4. Na página Configurações, defina o campo de **destino** seja **Serviços de mídia do Azure**.
 
    O perfil de codificação é previamente selecionado para **H. 264 Azure 720 p 16:9 (1280 x 720)**. Para personalizar essas configurações, selecione o ícone de engrenagem à direita do menu suspenso para baixo e escolha **Nova predefinidos**.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)

5. Configure codificador predefinições.

    Nomeie o predefinir e, em seguida, verifique as seguintes configurações recomendadas:

    **Vídeo**
    
    - Codificador: MainConcept h. 264
    - Quadros por segundo: 30
    - Taxa de bits média: 5000 kbits/seg (pode ser ajustado com base nas limitações de rede)
    - Perfil: principal
    - Quadro chave cada: 60 quadros

    **Áudio**

    - Taxa de bits de destino: 192 kbits/seg
    - Taxa de amostragem: 44,100 kHz
     
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)

6. Pressione **Salvar**.

    O campo de codificação agora tem o perfil recém-criado disponível para seleção. 

    Verifique se que o novo perfil está selecionado.

7. Obter a URL de entrada do canal para atribuí-la a Wirecast **Ponto de extremidade de RTMP**.
    
    Navegue de volta para a ferramenta AMSE e verificar o status de conclusão de canal. Depois que o estado mudou de **Iniciando** **em execução**, você pode obter a URL de entrada.
      
    Quando o canal está em execução, clique com botão direito no nome do canal, navegue para baixo até hover sobre **URL de entrada de copiar para área de transferência** e selecione **URL de entrada principal**.  
    
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)

8. Na janela de Wirecast **Configurações de saída** , cole essas informações no campo **endereço** da seção de saída e atribuir um nome de fluxo. 


    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

9. Selecione **Okey**.

10. Na tela principal do **Wirecast** , confirme fontes de entrada de vídeo e áudio estão prontos e em seguida, aperte **fluxo** no canto superior esquerdo.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

>[AZURE.IMPORTANT]Antes de clicar em **fluxo**, você **deve** garantir que o canal está pronto. 
>Além disso, certifique-se para não deixar o canal em um estado pronto sem uma entrada contribuição feed por mais de > 15 minutos.

##<a name="test-playback"></a>Reprodução de teste
  
1. Navegue até a ferramenta AMSE e clique com botão direito do canal a ser testado. No menu, passe o mouse sobre **a visualização de reprodução** e selecione **com o Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Se o fluxo de aparecer no player, em seguida, o codificador foi corretamente configurado para se conectar ao AMS. 

Se um erro for recebido, o canal precisará ser redefinidas e configurações de codificador ajustadas. Consulte o tópico de [solução de problemas](media-services-troubleshooting-live-streaming.md) para obter orientações.  

##<a name="create-a-program"></a>Criar um programa

1. Depois de reprodução do canal é confirmada, crie um programa. Na guia **Live** na ferramenta de AMSE, clique com o botão direito dentro da área de programa e selecione **Criar novo programa**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)

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
