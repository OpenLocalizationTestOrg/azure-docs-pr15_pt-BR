<properties
    pageTitle="Usando vários arquivos de entrada e propriedades do componente com codificador Premium | Microsoft Azure"
    description="Este tópico explica como usar setRuntimeProperties para usar vários arquivos de entrada e passar dados personalizados para o processador de mídia de fluxo de trabalho do Media codificador Premium."
    services="media-services"
    documentationCenter=""
    authors="xpouyat"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"  
    ms.author="xpouyat;anilmur;juliako"/>

# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Usando vários arquivos de entrada e propriedades do componente com codificador Premium

## <a name="overview"></a>Visão geral

Há situações em que talvez você precise para personalizar propriedades de componente, especificar o conteúdo XML de lista do clipe, ou enviar vários arquivos de entrada quando você envia uma tarefa com o processador de mídia de **Fluxo de trabalho do Media codificador Premium** . Alguns exemplos são:

- Sobrepor o texto em vídeo e configurando o valor de texto (por exemplo, a data atual) em tempo de execução para cada vídeo de entrada.
- Personalizando o XML de lista do clipe (para especificar um ou vários arquivos de origem, com ou sem filtragem, etc.).
- Sobrepondo uma imagem de logotipo na entrada vídeo enquanto o vídeo é codificado.

Para permitir que o **Fluxo de trabalho do Media codificador Premium** saibam que você está alterando algumas propriedades do fluxo de trabalho quando você cria a tarefa ou envia vários arquivos de entrada, você precisa usar uma cadeia de caracteres de configuração que contém **setRuntimeProperties** e/ou **transcodeSource**. Este tópico explica como usá-las.


## <a name="configuration-string-syntax"></a>Sintaxe de cadeia de caracteres de configuração

A cadeia de caracteres de configuração para definir a codificação tarefa usa um documento XML que tem esta aparência:

    <?xml version="1.0" encoding="utf-8"?>
    <transcodeRequest>
      <transcodeSource>
      </transcodeSource>
      <setRuntimeProperties>
        <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      </setRuntimeProperties>
    </transcodeRequest>


Este é o código c# que lê a configuração do XML de um arquivo e passa para a tarefa em um trabalho:

    XDocument configurationXml = XDocument.Load(xmlFileName);
    IJob job = _context.Jobs.CreateWithSingleTask(
                                                  "Media Encoder Premium Workflow",
                                                  configurationXml.ToString(),
                                                  myAsset,
                                                  "Output asset",
                                                  AssetCreationOptions.None);


## <a name="customizing-component-properties"></a>Personalizando as propriedades de componente  

### <a name="property-with-a-simple-value"></a>Propriedade com um valor simples
Em alguns casos, é útil personalizar uma propriedade de componente junto com o arquivo de fluxo de trabalho que está prestes a ser executado pelo fluxo de trabalho do Media codificador Premium.

Suponha que você criou um fluxo de trabalho texto sobreposições em vídeos e o texto (por exemplo, a data atual) deve ser definida no tempo de execução. Você pode fazer isso enviando o texto a ser definida como o novo valor para a propriedade text do componente sobreposição da tarefa codificação. Você pode usar esse mecanismo para alterar outras propriedades de um componente do fluxo de trabalho (como a posição ou cor da sobreposição, a taxa de bits de codificador AVC, etc.).

**setRuntimeProperties** é usada para substituir uma propriedade nos componentes do fluxo de trabalho.

Exemplo:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Optional Overlay/Overlay/filename" value="MyLogo.png"/>
          <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
      </setRuntimeProperties>
    </transcodeRequest>


### <a name="property-with-an-xml-value"></a>Propriedade com um valor XML

Para definir uma propriedade que espera um valor XML, encapsular usando `<![CDATA[ and ]]>`.

Exemplo:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

>[AZURE.NOTE]Certifique-se de não colocar um carro retorno logo após `<![CDATA[`.


### <a name="propertypath-value"></a>valor de propertyPath

Nos exemplos anteriores, a propertyPath foi "/ arquivos de mídia entrada/filename" ou "/ inactiveTimeout" ou "clipListXml".
Isto é, em geral, o nome do componente, em seguida, o nome da propriedade. O caminho pode ter mais ou menos níveis, como "/ primarySourceFile" (porque a propriedade é na raiz do fluxo de trabalho) ou "/ vídeo/opacidade de sobreposição gráficos/processamento" (porque a sobreposição em um grupo).    

Para verificar o caminho e nome da propriedade, use o botão de ação que está imediatamente ao lado de cada propriedade. Você pode clique neste botão de ação e selecione **Editar**. Isso mostrará o nome real da propriedade e imediatamente acima dela, o namespace.

![Ação/Edit](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Propriedade](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Vários arquivos de entrada

Cada tarefa que você envia para o **Fluxo de trabalho do Media codificador Premium** requer dois ativos:

- O primeiro é um *Ativo de fluxo de trabalho* que contém um arquivo de fluxo de trabalho. Você pode criar arquivos de fluxo de trabalho usando o [Designer de fluxo de trabalho](media-services-workflow-designer.md).
- O segundo é um *Ativos de mídia* que contém os arquivos de mídia que você deseja codificar.

Quando você estiver enviando vários arquivos de mídia com o codificador de **Fluxo de trabalho do Media codificador Premium** , as seguintes restrições se aplicam:

- Todos os arquivos de mídia devem estar na mesma *Ativos de mídia*. Usar vários ativos de mídia não é suportada.
- Você deve definir o arquivo primário neste ativos de mídia (o ideal, essa é o arquivo de vídeo principal que o codificador é solicitado a processar).
- É necessário passar dados de configuração que inclui o elemento **setRuntimeProperties** e/ou **transcodeSource** ao processador.
  - **setRuntimeProperties** é usada para substituir a propriedade filename ou outra propriedade nos componentes do fluxo de trabalho.
  - **transcodeSource** é usado para especificar o conteúdo XML de lista do clipe.

Conexões no fluxo de trabalho:

 - Se você usar um ou vários componentes de entrada de arquivo de mídia e planejar usar **setRuntimeProperties** para especificar o nome do arquivo, em seguida, não conecte o pin de componente do arquivo principal a elas. Certifique-se de que não há nenhuma conexão entre o objeto de arquivo principal e as entradas de arquivo de mídia.
 - Se você preferir usar Clip lista XML e um componente de origem de mídia, em seguida, você pode conectar ambos juntos.

![Nenhuma conexão de arquivo de origem principal para entrada de arquivo de mídia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Não há nenhuma conexão a partir do arquivo primário para componentes de entrada de arquivo de mídia se você usar setRuntimeProperties para definir a propriedade nome do arquivo.*

![Conexão de Clip lista XML para Clip-fonte da lista](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Você pode conectar o clipe lista XML à fonte de mídia e usar transcodeSource.*


### <a name="clip-list-xml-customization"></a>Clip-personalização XML de lista
Você pode especificar o XML de lista do clipe no fluxo de trabalho em tempo de execução usando **transcodeSource** na sequência de configuração XML. Isso requer o pin Clip lista XML estar conectado ao componente de origem de mídia no fluxo de trabalho.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <transcodeSource>
          <clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
          </clipList>
        </transcodeSource>
        <setRuntimeProperties>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Se você quiser especificar /primarySourceFile para usar essa propriedade para nomear os arquivos de saída usando 'Expressões', é recomendável passando o XML de lista do clipe como uma propriedade *após* a propriedade de /primarySourceFile, para evitar ter da lista de clipes a ser substituído pela configuração /primarySourceFile.

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Com a filtragem de quadro precisos adicional:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
               <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


## <a name="example"></a>Exemplo

Considere um exemplo na qual você deseja uma imagem de logotipo na entrada vídeo de sobreposição enquanto o vídeo é codificado. Neste exemplo, o vídeo de entrada é denominado "MyInputVideo.mp4" e o logotipo "MyLogo.png". Você deve executar as seguintes etapas:

- Criar um fluxo de trabalho ativo com o arquivo de fluxo de trabalho (consulte o exemplo a seguir).
- Criar um ativo de mídia, que contém dois arquivos: MyInputVideo.mp4 como o arquivo primário e MyLogo.png.
- Enviar uma tarefa para o processador de mídia de fluxo de trabalho do Media codificador Premium com os ativos de entrada acima e especifique a seguinte sequência de configuração.

Configuração:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="MyLogo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


No exemplo acima, o nome do arquivo de vídeo é enviado para o componente de entrada de arquivo de mídia e a propriedade de primarySourceFile. O nome do arquivo logotipo é enviado à outra entrada de arquivo de mídia que está conectada para o componente de sobreposição de gráfico.

>[AZURE.NOTE]O nome de arquivo de vídeo é enviado para a propriedade de primarySourceFile. O motivo para isso é usar essa propriedade no fluxo de trabalho para criar o nome do arquivo de saída correta usando expressões, por exemplo.


### <a name="step-by-step-workflow-creation-that-overlays-a-logo-on-top-of-the-video"></a>Criação de fluxo de trabalho passo a passo que sobrepõe um logotipo na parte superior do vídeo     

Estas são as etapas para criar um fluxo de trabalho que leva dois arquivos como entrada: um vídeo e uma imagem. Ele irá sobrepor a imagem na parte superior do vídeo.

Abra o **Designer de fluxo de trabalho** e selecione **arquivo** > **Novo espaço de trabalho** > **Decodificar esquema**.

O novo fluxo de trabalho mostra três elementos:

- Arquivo de origem principal
- Lista de clipes XML
- Arquivo de saída/ativo  

![Novo fluxo de trabalho de codificação](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Novo fluxo de trabalho de codificação*


Para aceitar o arquivo de mídia de entrada, comece com a adição de um componente de entrada de arquivo de mídia. Para adicionar um componente ao fluxo de trabalho, procure-o na caixa de pesquisa do repositório e arraste a entrada desejada para o painel de designer.

Em seguida, adicione o arquivo de vídeo a ser usado para projetar seu fluxo de trabalho. Para fazer isso, clique no painel de plano de fundo no Designer de fluxo de trabalho e procure a propriedade de arquivo de origem principal no painel direito de propriedade. Clique no ícone de pasta e selecione o arquivo de vídeo apropriado.

![Fonte de arquivo principal](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Fonte de arquivo principal*


Em seguida, especifique o arquivo de vídeo no componente de entrada de arquivo de mídia.   

![Fonte de entrada de arquivo de mídia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Fonte de entrada de arquivo de mídia*


Assim que isso é feito, o componente de entrada de arquivo de mídia será inspecionar o arquivo e preencher seus pinos de saída para refletir o arquivo que ele inspecionadas.

A próxima etapa é adicionar um "vídeo dados tipo atualizador" para especificar o espaço de cor para Rec.709. Adicionar um "vídeo formato conversor" que está definido para o tipo de Layout de dados/Layout = configurável planos. Isso converterá o fluxo de vídeo em um formato que possa ser executado como uma fonte do componente de sobreposição.

![Atualizador de tipo de dados e conversor de formato de vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Atualizador de tipo de dados de vídeo e o conversor de formato*

![Tipo de layout = configurável planos](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Tipo de layout é configurável planos*

Em seguida, adicionar um componente de sobreposição de vídeo e conecte o pin de vídeo (descompactado) para o pin de vídeo (descompactado) da entrada de arquivo de mídia.

Adicionar outra entrada de arquivo de mídia (para carregar o arquivo de logotipo), clique neste componente e renomeá-lo para "Logotipo de entrada de arquivo de mídia" e selecione uma imagem (um arquivo. png por exemplo) na propriedade de arquivo. Conecte o pin de imagem descompactado para o pin de imagem descompactado da sobreposição.

![Fonte de arquivo de imagem e o componente de sobreposição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Fonte de arquivo de imagem e o componente de sobreposição*


Se você deseja modificar a posição do logotipo no vídeo (por exemplo, talvez você queira posicioná-la em 10% de canto superior esquerdo do vídeo), desmarque a caixa de seleção "Entrada Manual". Você pode fazer isso porque você está usando uma entrada de arquivo de mídia para fornecer o arquivo de logotipo para o componente de sobreposição.

![Posição de sobreposição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Posição de sobreposição*


Para codificar o fluxo de vídeo para h. 264, adicione os componentes de codificador AVC codificador de vídeo e AAC para a superfície de designer. Conecte os pinos.
Configurar o codificador AAC e selecione conversão de formato de áudio/predefinir: 2.0 (L, R).

![Codificadores de áudio e vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Codificadores de áudio e vídeo*


Agora adicione os componentes **ISO Mpeg-4 multiplexador** e **Saída de arquivo** e conecte os pinos conforme mostrado.

![MP4 multiplexador e saída de arquivo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexador e saída de arquivo*


Você precisa definir o nome para o arquivo de saída. Clique no componente de **Saída do arquivo** e editar a expressão para o arquivo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nome do arquivo de saída](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nome do arquivo de saída*

Você pode executar o fluxo de trabalho localmente para verificar que ele esteja funcionando corretamente.

Depois de terminar, você pode executá-lo nos serviços de mídia do Azure.

Primeiro, preparar um ativo nos serviços de mídia do Azure com dois arquivos: o arquivo de vídeo e o logotipo. Você pode fazer isso usando o .NET ou API REST. Você também pode fazer isso usando o portal do Azure ou o [Explorador de serviços de mídia do Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Este tutorial mostra como gerenciar ativos com AMSE. Há duas maneiras de adicionar arquivos a um ativo:

- Criar uma pasta local, copie os dois arquivos e arraste e solte a pasta para a guia **ativo** .
- Carregar o arquivo de vídeo como um ativo, exibir as informações de ativos, vá para a guia arquivos e carregar um arquivo adicional (logotipo).

>[AZURE.NOTE]Certifique-se de definir um arquivo primário no ativo (o arquivo de vídeo principal).

![Arquivos de ativo no AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Arquivos de ativo no AMSE*


Selecione o ativo e escolha a codificação com o codificador Premium. Carregue o fluxo de trabalho e selecioná-lo.

Clique no botão para passar dados para o processador e adicione o seguinte XML para definir as propriedades de tempo de execução:

![Codificador Premium em AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Codificador Premium em AMSE*


Em seguida, cole os seguintes dados XML. Você precisa especificar o nome do arquivo de vídeo para a entrada de arquivo de mídia e primarySourceFile. Especifique o nome do nome do arquivo para o logotipo muito.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*


Se você usa o SDK do .NET para criar e executar a tarefa, esses dados XML deve ser passado como a cadeia de caracteres de configuração.

    public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);

Após concluir o trabalho, o arquivo MP4 no ativo saída exibe a sobreposição!

![Sobreposição no vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Sobreposição no vídeo*


Você pode baixar o fluxo de trabalho de exemplo do [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).


## <a name="see-also"></a>Consulte também

- [Apresentando o Premium codificação no Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

- [Como usar a codificação de Premium nos serviços de mídia do Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

- [Codificação de conteúdo sob demanda com os serviços de mídia do Azure](media-services-encode-asset.md#media_encoder_premium_workflow)

- [Codecs e formatos de mídia codificador Premium fluxo de trabalho](media-services-premium-workflow-encoder-formats.md)

- [Arquivos de fluxo de trabalho de exemplo](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)

- [Ferramenta de Explorador de serviços de mídia do Azure](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
