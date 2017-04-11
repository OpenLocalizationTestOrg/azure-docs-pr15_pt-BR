
<properties
    pageTitle="Obtendo recomendações em lotes: API de recomendações de aprendizado de máquina | Microsoft Azure"
    description="Recomendações – obtendo recomendações em lotes de aprendizado de máquina Azure"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="luisca"/>

# <a name="get-recommendations-in-batches"></a>Obter recomendações em lotes

>[AZURE.NOTE] Obter recomendações em lotes é mais complicada que obtendo recomendações um por vez. Verificar as APIs para obter informações sobre como obter recomendações para uma única solicitação:

> [Recomendações de item ao Item](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4)<br>
> [Recomendações de usuário-a-Item](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)
>
> Pontuação de lote funciona somente para compilações criadas após 21 de julho de 2016.


Há situações em que você precisa obter recomendações para mais de um item por vez. Por exemplo, você pode estar interessado na criação de um cache de recomendações ou até mesmo analisar os tipos de recomendações que você está obtendo.

Operações de pontuação em lotes, como chamamos-los, são operações assíncronas. Você precisa enviar a solicitação, aguarde a operação concluir e, em seguida, coletar seus resultados.  

Para ser mais precisas, estas são as etapas a seguir:

1.  Crie um contêiner de armazenamento do Azure se você não tenha um.
2.  Carregar um arquivo de entrada que descreve cada uma das suas solicitações de recomendação ao armazenamento de Blob do Azure.
3.  Inicie o trabalho em lotes pontuação.
4.  Aguarde a operação assíncrona concluir.
5.  Quando a operação tiver terminado, colete os resultados do armazenamento de Blob.

Vamos examinar cada uma dessas etapas.

## <a name="create-a-storage-container-if-you-dont-have-one-already"></a>Criar um contêiner de armazenamento se você não tenha um

Acesse o [portal do Azure](https://portal.azure.com) e criar uma nova conta de armazenamento, caso você não tenha um. Para fazer isso, navegue até o **novo** > **dados** + **armazenamento** > **Conta de armazenamento**.

Depois que você tiver uma conta de armazenamento, você precisa criar os contêineres de blob onde você armazenará a entrada e saída da execução do lote.

Carregar um arquivo de entrada que descreve cada um dos seu recomendação solicitações ao armazenamento de Blob – vamos chamar o input.json de arquivo aqui.
Depois que um contêiner, você precisa fazer o upload de um arquivo que descreve cada uma das solicitações que você precisará executar desse serviço recomendações.

Um lote pode realizar apenas um tipo de solicitação de uma compilação específica. Explicaremos como definir essas informações na próxima seção. Por agora, vamos supor que estamos executarão recomendações de item fora uma compilação específica. O arquivo de entrada, em seguida, contém as informações de entrada (nesse caso, os itens de propagação) para cada uma das solicitações.

Este é um exemplo da aparência do arquivo input.json:

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }

Como você pode ver, o arquivo é um arquivo JSON, onde cada uma das solicitações tem as informações necessárias para enviar uma solicitação de recomendações. Crie um arquivo JSON semelhante para as solicitações de que você precisa atender e copiá-lo para o recipiente que você acabou de criar no armazenamento de Blob.

## <a name="kick-start-the-batch-job"></a>Inicie o trabalho em lotes

A próxima etapa é enviar uma nova tarefa de lote. Para saber mais, verifique a [referência à API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/).

O corpo da solicitação da API precisa definir os locais onde a entrada, saída e arquivos de erro precisam ser armazenado. Ele também precisa definir as credenciais necessárias para acessar esses locais. Além disso, você precisará especificar alguns parâmetros que se aplicam ao lote de inteiros (o tipo de recomendações para solicitar, modelo/criar o número de resultados por chamada, e assim por diante).

Este é um exemplo de como o corpo da solicitação deve parecer:

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

Veja algumas informações importantes:

-   Atualmente, **authenticationType** sempre deve ser definida como **PublicOrSas**.

-   Você precisa obter um token de assinatura de acesso compartilhado (SAS) para permitir que a API de recomendações para leitura e gravação de/para sua conta de armazenamento de Blob. Obter mais informações sobre como gerar tokens SAS podem ser encontradas na [página API de recomendações](../storage/storage-dotnet-shared-access-signature-part-1.md).

-   Somente **apiName** que é suportado atualmente é **ItemRecommend**, que é usado para recomendações de Item ao Item. Processamento em lotes atualmente não dá suporte recomendações de usuário-a-Item.

## <a name="wait-for-the-asynchronous-operation-to-finish"></a>Aguarde a operação assíncrona ao fim

Quando você começa a operação em lote, a resposta retorna o cabeçalho de operação local que fornece as informações necessárias controlar a operação.
Você pode controlar a operação usando a [API de Status de operação recuperar]( https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da), exatamente como você para controlar a operação de uma operação de compilação.

## <a name="get-the-results"></a>Obter os resultados

Após a operação, supondo que havia sem erros, você pode coletar os resultados da sua saída armazenamento de Blob.

O exemplo a seguir mostram como a saída pode parecer. Neste exemplo, vamos mostrar resultados para um lote com apenas dois solicitações (para resumir).

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## <a name="learn-about-the-limitations"></a>Saiba mais sobre as limitações

-   Trabalho em lotes apenas uma pode ser chamado por assinatura por vez.
-   Um arquivo de entrada de trabalho em lotes não pode ser mais de 2 MB.
