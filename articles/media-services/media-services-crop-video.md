<properties
    pageTitle="Como cortar vídeo | Microsoft Azure"
    description="Este artigo mostra como cortar vídeos com mídia codificador padrão."
    services="media-services"
    documentationCenter=""
    authors="anilmur"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016"  
    ms.author="anilmur;juliako;"/>

# <a name="crop-videos-with-media-encoder-standard"></a>Vídeos de corte com mídia codificador padrão

Você pode usar o padrão de codificador de mídia (MES) para cortar sua entrada vídeo. Corte é o processo de seleção de uma janela retangular dentro do quadro de vídeo e codificação apenas os pixels nessa janela. O diagrama a seguir ajuda a ilustrar o processo.

![Cortar um vídeo](./media/media-services-crop-video/media-services-crop-video01.png)

Suponha que você tenha como entrada um vídeo que tem uma resolução de 1920x1080 pixels (proporção 16:9), mas tem barras pretas (pilar caixas) na parte esquerda e direita, para que apenas uma janela de 4:3 ou 1440 x 1080 pixels contém vídeo ativo. Você pode usar MES para cortar ou editar out as barras pretas e codificar a região 1440 x 1080.

Corte do MES é um estágio de pré-processamento, portanto, os parâmetros de corte no predefinir a codificação se aplicam ao vídeo de entrada original. Codificação é um estágio subsequente e as configurações de largura/altura se aplicam a *previamente processadas* vídeo e não o vídeo original. Ao criar sua predefinir você precisa fazer o seguinte: (a) selecione os parâmetros de corte com base em vídeo de entrada original e (b) seu codificar configurações com base no vídeo cortado. Se você não coincidem seu codificar as configurações para o vídeo cortada, a saída não será conforme o esperado.

O tópico a [seguir](media-services-advanced-encoding-with-mes.md#encoding_with_dotnet) mostra como criar um trabalho de codificação com MES e como especificar um personalizado predefinido para a tarefa de codificação. 

## <a name="creating-a-custom-preset"></a>Criação de predefinições personalizadas

No exemplo mostrado no diagrama:

1. Entrada original é 1920x1080
1. Ele precisa ser cortada para uma saída de 1440 x 1080, que é centralizada no quadro de entrada
1. Isso significa que um deslocamento de X de (1920 – 1440) / 2 = 240 e um Y deslocarem de zero
1. A largura e altura do retângulo de corte são 1440 e 1080, respectivamente
1. No estágio codificar, o ask é produzir três camadas, são resoluções 1440 x 1080, 960 x 720 e 480 x 360, respectivamente

###<a name="json-preset"></a>Predefinir JSON


    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


##<a name="restrictions-on-cropping"></a>Restrições de corte

O recurso de corte é destinado a ser manual. Você precisa carregar o vídeo de entrada em uma ferramenta de edição adequada que permite selecionar quadros de interesse, posicione o cursor para determinar compensações para o retângulo de corte, para determinar o valor de codificação predefinido que é ajustada para esse determinado vídeo, etc. Este recurso não serve para permitir coisas como: detecção automática e remoção das bordas de preto letterbox/pillarbox em seu vídeo de entrada.

Seguintes restrições se aplicam ao recurso de corte. Se elas não forem atendidas, a codificar tarefa pode falhar ou produzir um resultado inesperado.

1. Tem as coordenadas e o tamanho do retângulo de corte caber no vídeo de entrada
1. Conforme mencionado acima, a largura e altura nas configurações codificar precisam corresponder ao vídeo recortado
1. Corte aplica-se aos vídeos capturados no modo paisagem (isto é mantido não aplicável a vídeos gravados com um smartphone verticalmente ou no modo de retrato)
1. Funciona melhor com vídeo progressivo capturado com pixels quadrados

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-step"></a>Próxima etapa
 
Consulte serviços de mídia do Azure caminhos de aprendizado para ajudá-lo a aprender sobre ótimos recursos oferecidos pelo AMS.  

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]
