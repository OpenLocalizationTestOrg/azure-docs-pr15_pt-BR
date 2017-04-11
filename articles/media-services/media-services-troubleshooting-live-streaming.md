<properties 
    pageTitle="Guia de solução de problemas do fluxo ao vivo | Microsoft Azure" 
    description="Este tópico fornece sugestões sobre como solucionar problemas de streaming ao vivo." 
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
    ms.topic="article" 
    ms.date="10/12/2016"  
    ms.author="juliako"/>

#<a name="troubleshooting-guide-for-live-streaming"></a>Guia de solução de problemas do streaming ao vivo

Este tópico fornece sugestões sobre como solucionar alguns problemas de streaming ao vivo.

## <a name="issues-related-to-on-premises-encoders"></a>Problemas relacionados ao codificadores de local 

Esta seção fornece sugestões sobre como solucionar problemas relacionados à codificadores locais que estão configurados para enviar um fluxo de taxa de bits única para canais de AMS habilitados para codificação ao vivo.

###<a name="problem-would-like-to-see-logs"></a>Problema: Gostaria de ver logs 

- **Possível problema**: não é possível localizar os logs de codificador que podem ajudar na depuração de problemas.
    
    - **Telestream Wirecast**: normalmente, você encontra logs em C:\Users\{username} \AppData\Roaming\Wirecast\ 
    - **Live primários**: você pode encontrar tem links para logs no portal de gerenciamento. Clique em **estatísticas**e **Logs**. Na página **Arquivos de Log** , você verá uma lista de registros para todos os itens de LiveEvent; Selecione a sua sessão atual de correspondência. 
    - **Flash Media Live codificador**: você pode encontrar o **Diretório de Log...** , navegando até a guia **Codificação de Log** .
    
###<a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problema: não há nenhuma opção para a saída de um fluxo de progressivo

- **Possível problema**: O codificador sendo usado não Desentrelaçar automaticamente. 

    **Etapas de solução de problemas**: procure por uma opção de desentrelaçamento dentro da interface de codificador. Quando eliminação entrelaçamento estiver ativado, verifique novamente as configurações de saída progressivo. 
 
###<a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problema: Tentado várias configurações de saída de codificador e ainda não conseguir se conectar. 

- **Possível problema**: canal de codificação Azure não foi redefinida adequadamente. 

    **Etapas de solução de problemas**: Verifique se o codificador já não está fazendo para AMS, parar e redefinir o canal. Após a execução novamente, tente conectar seu codificador com as novas configurações. Se isso ainda não corrigir o problema, tente criar um novo canal inteiramente, às vezes, canais podem se tornar corrompidas após várias tentativas.  

- **Possível problema**: GOP o tamanho ou configurações de quadro chave não forem ideais. 

    **Etapas de solução de problemas**: intervalo de tamanho ou quadro-chave de GOP recomendados é 2 segundos. Alguns codificadores calcular essa configuração no número de quadros, enquanto outros usam segundos. Por exemplo: quando saindo 30 quadros/s, o tamanho de GOP seria 60 quadros, que equivale a 2 segundos.  
     
- **Possível problema**: portas fechadas estão bloqueando o fluxo. 

    **Etapas de solução de problemas**: quando streaming via RTMP, verifique as configurações de firewall e/ou proxy para confirmar que as portas de saída 1935 e 1936 estão abertas. Ao usar o streaming RTP, confirme se a porta de saída 2010 é aberta. 


###<a name="problem-when-configuring-the-encoder-to-stream-with-the-rtp-protocol-there-is-no-place-to-enter-a-host-name"></a>Problema: Ao configurar o codificador fluxo com o protocolo RTP, há um local para digitar um nome de host. 

- **Possível problema**: codificadores RTP muitos não permitem nomes de host e um endereço IP precisam ser adquiridos.  

    **Etapas de solução de problemas**: para localizar o endereço IP, abra um prompt de comando em qualquer computador. Para fazer isso no Windows, abra o inicializador de execução (WIN + R) e digite "cmd" para abrir.  

    Após abrir o prompt de comando, digite "Ping [nome do Host de AMS]". 

    O nome do host pode ser derivado omitindo o número da porta da URL inclusão Azure, como destacado no exemplo a seguir: 

    RTP://Test2-amstest009.RTP.Channel.mediaservices.Windows.NET:2010 / 

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

###<a name="problem-unable-to-playback-the-published-stream"></a>Problema: Impossível a reprodução do fluxo publicado.
 
- **Possível problema**: não há nenhum ponto de extremidade Streaming executando ou não há nenhuma streaming unidades (escala) alocadas. 

    **Etapas de solução de problemas**: Navegue até a guia "Ponto de extremidade de Streaming" na ferramenta AMSE e confirme há um ponto de extremidade Streaming executando com uma unidade de streaming. 
    


>[AZURE.NOTE] Se após seguir as etapas de solução de problemas que você ainda não consegue com êxito de fluxo, envie um tíquete usando o portal do Azure.

##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
