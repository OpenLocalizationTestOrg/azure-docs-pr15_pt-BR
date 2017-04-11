<properties 
    pageTitle="Tutoriais do fluxo de trabalho do Avanced mídia codificador Premium" 
    description="Este documento contém instruções passo a passo que mostram como realizar tarefas avançadas com mídia codificador Premium fluxo de trabalho e também como criar fluxos de trabalho complexos com o Designer de fluxo de trabalho." 
    services="media-services" 
    documentationCenter="" 
    authors="xstof" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016"  
    ms.author="xstof;xpouyat;juliako"/>

#<a name="advanced-media-encoder-premium-workflow-tutorials"></a>Tutoriais de fluxo de trabalho do Media codificador Premium avançados

##<a name="overview"></a>Visão geral 

Este documento contém instruções passo a passo que mostram como personalizar fluxos de trabalho com o **Designer de fluxo de trabalho**. Você pode encontrar os arquivos de fluxo de trabalho real [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

##<a name="toc"></a>SUMÁRIO

Os tópicos a seguir são abordados:

- [Codificação de MXF em uma taxa de bits única MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
    - [Iniciar um novo fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new) 
    - [Usando a entrada de arquivo de mídia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
    - [Inspecionar fluxos de mídia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
    - [Adicionando um codificador de vídeo para. Geração de arquivo MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
    - [Codificação o fluxo de áudio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
    - [Multiplexação fluxos de áudio e vídeo em um contêiner de MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
    - [Gravar o arquivo MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
    - [Criando um ativo de serviços de mídia do arquivo de saída](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
    - [Testar o fluxo de trabalho concluído localmente](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
- [Codificação de MXF em multibitrate MP4s - dinâmica embalagem habilitada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
    - [Adicionando um ou mais saídas MP4 adicionais](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
    - [Configurar os nomes de arquivo de saída](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
    - [Adicionando um controle de áudio separado](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
    - [Adicionando o. Arquivo SMIL ISM](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
- [Codificação de MXF em multibitrate MP4 - esquema aprimorado](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
    - [Visão geral do fluxo de trabalho para aprimorar](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_overview)
    - [Convenções de nome de arquivo](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
    - [Propriedades de componente em raiz do fluxo de trabalho de publicação](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
    - [Ter gerado o arquivo de saída nomes contam com valores de propriedade publicado](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
- [A adição de miniaturas para multibitrate MP4 saída](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
    - [Visão geral do fluxo de trabalho para adicionar miniaturas](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to_multibitrate_MP4_overview)
    - [Adição de codificação de JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
    - [Lidando com conversão de espaço de cores](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
    - [Escrever as miniaturas](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
    - [Detectar erros em um fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
    - [Fluxo de trabalho concluído](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
- [Filtragem baseada em vez de saída multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
    - [Visão geral do fluxo de trabalho para iniciar a adição de filtragem para](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
    - [Usando o filtro de fluxo](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
    - [Fluxo de trabalho concluído](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
- [Introdução ao componente de script](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
    - [Script dentro de um fluxo de trabalho: Olá](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
- [Filtragem baseada em quadro de saída multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
    - [Visão geral do esquema para iniciar a adição de filtragem para](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
    - [Usando a lista de Clip XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
    - [Modificando a lista de clipes de um componente de script](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
    - [Adicionando uma propriedade de conveniência ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

##<a id="MXF_to_MP4"></a>Codificação de MXF em uma taxa de bits única MP4
 
Nesta explicação vamos criar uma taxa de bits única. Arquivo de MP4 com HE-AAC codificado áudio de um. Arquivo de entrada MXF. 

###<a id="MXF_to_MP4_start_new"></a>Iniciar um novo fluxo de trabalho 

Abra o Designer de fluxo de trabalho e selecione "Arquivo"-"novo espaço de trabalho"-"decodificar esquema" 

O novo fluxo de trabalho mostrará 3 elementos: 

- Arquivo de origem principal
- Lista de clipes XML
- Arquivo de saída/ativo  

![Novo fluxo de trabalho de codificação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Novo fluxo de trabalho de codificação*

###<a id="MXF_to_MP4_with_file_input"></a>Usando a entrada de arquivo de mídia

Para aceitar o nosso arquivo de mídia de entrada, começa com a adição de um componente de entrada de arquivo de mídia. Para adicionar um componente ao fluxo de trabalho, procure-o na caixa de pesquisa do repositório e arraste a entrada desejada para o painel de designer. Fazer isso para a entrada de arquivo de mídia e conecte o componente de arquivo de origem principal para o pin de entrada do nome do arquivo da entrada de arquivo de mídia.

![Entrada de arquivo de mídia conectada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Entrada de arquivo de mídia conectada*

Antes que podemos fazer muito mais, podemos precisará primeiro indicar para o designer de fluxo de trabalho que arquivo de amostra podemos gostaria de usar para criar nosso fluxo de trabalho com. Para fazer isso, clique no plano de fundo do painel designer e procure a propriedade de arquivo de origem principal no painel direito de propriedade. Clique no ícone de pasta e selecione o arquivo desejado para testar o fluxo de trabalho com. Assim que isso é feito, o componente de entrada de arquivo de mídia será inspecionar o arquivo e preencher seus pinos de saída para refletir o arquivo que ele inspecionadas.

![Entrada de arquivo de mídia preenchida](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Entrada de arquivo de mídia preenchida*

Enquanto isso Especifica com qual entrada queremos trabalhar, ela não informa ainda onde a saída codificada deve ir para. Semelhante a como o arquivo de origem primária foi configurado, agora configurar a propriedade variável de pasta de saída, logo abaixo.

![Propriedades de entrada e saída configuradas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Propriedades de entrada e saída configuradas*

###<a id="MXF_to_MP4_streams"></a>Inspecionar fluxos de mídia

Muitas vezes, ele tem desejado conheça o fluxo de aparência parecido fluxos no fluxo de trabalho. Para inspecionar um fluxo em qualquer ponto no fluxo de trabalho, basta clicar em uma saída ou pino de entrada em qualquer um dos componentes. Nesse caso, tente clicar no vídeo descompactado saída pinos da nossa entrada de arquivo de mídia. Uma caixa de diálogo será aberta que permite inspecionar o vídeo de saída.

![Inspecionar o pin de saída de vídeo descompactado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Inspecionar o pin de saída de vídeo descompactado*

Em nosso caso, ele informa por exemplo que estamos lidando com uma entrada de 1920x1080 em 24 quadros por segundo na 4: amostragem 2:2 de um vídeo de quase 2 minutos.

###<a id="MXF_to_MP4_file_generation"></a>Adicionando um codificador de vídeo para. Geração de arquivo MP4

Observe que, agora, um vídeo descompactado e vários pins de saída de áudio não compactado estão disponíveis para uso em nossa entrada de arquivo de mídia. Para codificar o vídeo de entrada, precisamos de um componente de codificação - nesse caso para gerar. Arquivos MP4.

Para codificar o fluxo de vídeo para h. 264, adicione o componente de codificador de vídeo AVC para a superfície de designer. Este componente leva um fluxo de vídeo descompactar como entrada e oferece um fluxo de vídeo compactado AVC em seu pin de saída.

![Desconectados codificador AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Desconectados codificador AVC*

Suas propriedades determinam como a codificação exatamente acontece. Vamos dar uma olhada em algumas das configurações mais importantes:

- Saída largura e altura de saída: esses determinam a resolução do vídeo codificado. Em nosso caso vamos com 640 x 360
- Taxa de quadro: quando definida como passagem-apenas adotará a taxa de quadros de origem, é possível substituir isso apesar. Observe que tal conversão de taxa de quadros não é movimento-recompensado.
- Perfil e nível: eles determinam o perfil AVC e o nível. Para convenientemente obter mais informações sobre os diferentes níveis e perfis, clique no ícone de ponto de interrogação no componente AVC codificador de vídeo e a página de ajuda mostrará mais detalhes sobre cada um dos níveis. Em nosso exemplo, vamos com perfil principal no nível 3,2 (padrão).
- Avalie o modo de controle e a taxa de bits (kbps): em nosso cenário podemos optar por uma taxa de bits constante (CBR) de saída em kbps 1200
- Formato de vídeo: trata-se a VUI (informações de usabilidade de vídeo) gravados no fluxo de h. 264 (informações de lado que podem ser usados por um decodificador para aprimorar a exibição, mas não é essencial corretamente decodificar):
- NTSC (típico para EUA ou Japão, usando 30 quadros/s)
- PAL (típico para Europa, usando quadros/s 25)
- Modo de tamanho de GOP: nós vai configurar tamanho fixo de GOP para nossas finalidades com um intervalo de chave de 2 segundos com GOPs fechada. Isso garante a compatibilidade com os serviços de mídia do Azure embalagem dinâmica fornece.

Para alimentar nosso codificador AVC, conecte o pin de saída de vídeo descompactado do componente de entrada de arquivo de mídia para o pin de entrada de vídeo descompactado do codificador AVC.

![Codificador de AVC conectada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Conectada codificador AVC principal*

###<a id="MXF_to_MP4_audio"></a>Codificação o fluxo de áudio

Neste ponto, podemos ter codificado vídeo, mas o fluxo de áudio descompactado original ainda deve ser compactado. Para isso entraremos com codificação AAC pelo componente AAC codificador (Dolby). Adicioná-lo ao fluxo de trabalho.

![Desconectados codificador AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Codificador AAC desconectado*

Agora, há uma incompatibilidade: há apenas um único descompactado áudio pino de entrada do codificador AAC enquanto mais do que provável a entrada de arquivo de mídia terá duas diferentes descompactado fluxo de áudio disponíveis: uma para o canal de áudio esquerdo e outra para a direita. (Se você está lidando com som subjacente, que é 6 canais.) Portanto não é possível conectar diretamente o áudio da fonte de entrada de arquivo de mídia para o codificador de áudio AAC. O componente AAC espera um fluxo de áudio "intercalado" chamado: um único fluxo com à esquerda e direita canais AVI uns com os outros. Já sabemos do nosso arquivo de mídia de origem que faixas de áudio estão na qual posição na fonte, podemos gerar tal intercalado fluxo de áudio com as posições de alto-falante corretamente atribuído para a esquerda e direita.

Primeiro, um desejará gerado um fluxo de intercalada de canais de áudio de origem necessários. O componente de Interleaver de fluxo de áudio tratará isso para nós. Adicioná-lo ao fluxo de trabalho e se conecte as saídas de áudio da entrada de arquivo de mídia nela.

![Conectado Interleaver de fluxo de áudio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Conectado Interleaver de fluxo de áudio*

Agora que temos um fluxo de áudio intercalado, podemos não especificar onde atribuir as posições do orador à esquerda ou direita. Para especificar isso, podemos aproveitar o cedente de posição de alto-falante.

![Adicionando um cedente de posição de alto-falante](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Adicionando um cedente de posição de alto-falante*

Configure o cedente de posição do orador para uso com um fluxo de entrada estéreo por meio de um filtro predefinido codificador de "Personalizada" e o canal predefinidos chamado "2.0 (L, R)". (Isso atribuirá a posição de alto-falante esquerdo para canal 1 e a posição de alto-falante direito ao canal 2.)

Conecte a saída do cedente de posição de alto-falante à entrada do codificador AAC. Em seguida, informe o codificador AAC para trabalhar com um "2.0 (L, R)" canal predefinidos, de forma que ela saiba lidar com áudio estéreo como entrada.

###<a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexação fluxos de áudio e vídeo em um contêiner de MP4

Determinado nosso AVC codificado fluxo de vídeo e nosso AAC codificados fluxo de áudio, podemos ambos para capturar uma. Contêiner de MP4. O processo de misturar fluxos diferentes em uma única é chamado "multiplexação" (ou "muxing"). Nesse caso, estamos estiver intercalação o áudio e os fluxos de vídeo em uma única coerente. Pacote de MP4. O componente que coordenadas isso para um. Contêiner de mp4 é chamado multiplexador o ISO MPEG-4. Adicionar um para a superfície e conectar o codificador de vídeo AVC e o codificador AAC a suas entradas.

![Conectada MPEG4 multiplexador](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Conectada MPEG4 multiplexador*

###<a id="MXF_to_MP4_writing_mp4"></a>Gravar o arquivo MP4

Ao escrever um arquivo de saída, o componente de saída do arquivo é usado. Nós pode se conectar isso a saída do multiplexador ISO MPEG-4 para que sua saída é gravada em disco. Para fazer isso, conecte o pin de saída do contêiner (MPEG-4) para o pin de entrada de gravação de saída do arquivo.

![Conectado a saída de arquivo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Conectado a saída de arquivo*

O nome do arquivo que será usado é determinado pela propriedade de arquivo. Enquanto essa propriedade pode ser codificados para um determinado valor, um provavelmente desejará defini-lo através de uma expressão.

Para que o fluxo de trabalho determinar automaticamente a saída da propriedade de nome de uma expressão de arquivo, clique no botão ao lado do nome de arquivo (ao lado do ícone de pasta). No menu suspenso, em seguida, selecione "Expressão". Isso abrirá o editor de expressões. Limpe o conteúdo do editor primeiro.

![Editor de expressão vazia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Editor de expressão vazia*

O editor de expressão permite inserir qualquer valor literal, misturada com uma ou mais variáveis. Variáveis começar com um sinal de cifrão. Conforme você pressionar a tecla de $, o editor mostrará uma caixa suspensa com diversas variáveis disponíveis. Em nosso caso usaremos uma combinação da variável de diretório de saída e a variável de nome de arquivo de entrada base:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Preenchido check-out do Editor de expressão](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Preenchido check-out do Editor de expressão*

>[AZURE.NOTE]Para ver ver um arquivo de saída do seu trabalho de codificação no Azure, você deve fornecer um valor no editor de expressão. 

Quando você confirma a expressão pressionando okey, a janela de propriedade serão visualizadas à qual valor a resolve de propriedade de arquivo neste momento.

![Expressão de arquivo gerar dir de saída](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Expressão de arquivo gerar dir de saída*

###<a id="MXF_to_MP4_asset_from_output"></a>Criando um ativo de serviços de mídia do arquivo de saída

Enquanto estamos escreveu um arquivo de saída MP4, ainda precisamos indicar que este arquivo pertence ao ativo saída que os serviços de mídia irá gerar como resultado de executar este fluxo de trabalho. Para esse fim, o nó de arquivo de saída/ativos na tela de fluxo de trabalho é usado. Todos os arquivos recebidos neste nó fará parte do ativo serviços de mídia do Azure resultante.

Conecte o componente de saída de arquivo para o componente do arquivo de saída/ativo para concluir o fluxo de trabalho.

![Fluxo de trabalho concluído](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Fluxo de trabalho concluído*

###<a id="MXF_to_MP4_test"></a>Testar o fluxo de trabalho concluído localmente

Para testar o fluxo de trabalho localmente, pressione o botão de reprodução na barra de ferramentas na parte superior. Quando o fluxo de trabalho terminar a execução, inspecione a saída gerada na pasta de saída configurado. Você verá o arquivo de saída MP4 terminado codificado do arquivo de origem da entrada de MXF.

##<a id="MXF_to_MP4_with_dyn_packaging"></a>Codificação de MXF em MP4 - multibitrate embalagem dinâmica habilitado

Nesta explicação vamos criar um conjunto de vários arquivos de MP4 de taxa de bits com AAC codificado áudio de uma única. Arquivo de entrada MXF.

Quando uma saída de ativos de multi-taxa de bits for desejada para uso em combinação com os recursos de embalagem dinâmico oferecidos por serviços de mídia do Azure, vários arquivos MP4 alinhado à GOP de cada uma taxa de bits diferentes e resolução precisará ser gerado. Para fazer isso, o passo a passo de [Codificação MXF em uma taxa de bits única MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) nos fornece um bom ponto de partida.

![Iniciar fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Iniciar fluxo de trabalho*

###<a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Adicionando um ou mais saídas MP4 adicionais

Cada arquivo MP4 no nosso ativo de serviços de mídia do Azure resultante oferecerá suporte uma taxa de bits diferentes e resolução. Vamos adicionar um ou mais arquivos de saída MP4 ao fluxo de trabalho.

Para certificar-se de que temos todos os nossos codificadores vídeo criados com as mesmas configurações, é mais conveniente duplicar o codificador de vídeo AVC já existente e configurar outra combinação de resolução e taxa de bits (vamos adicionar um destes 960 x 540 25 quadros por segundo em 2,5 Mbps). Para duplicar o codificador existente, a cópia colá-lo na superfície de design.

Conecte o pino de saída de vídeo descompactado da entrada de arquivo de mídia nosso novo componente AVC.

![Segunda codificador AVC conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Segunda codificador AVC conectado*

Agora adapte a configuração para nosso novo codificador AVC de saída 960 x 540 em 2,5 Mbps. (Use suas propriedades "largura de saída", "Altura de saída" e "Taxa de bits (kbps)" para isso.)

Determinado queremos usar o ativo resultante junto com embalagem dinâmico dos serviços de mídia do Azure, o ponto de extremidade streaming precisa ser capaz de gerar desses arquivos MP4 fragmentos HLS/Fragmented MP4/traço que exatamente são alinhados entre si de forma que os clientes que são alternar entre diferentes bitrates obtenham uma experiência de áudio e vídeo de contínuo suave único. Para permitir isso, precisamos garantir que, nas propriedades de codificadores AVC a GOP ("grupo de imagens") tamanho para os dois arquivos MP4 é definido para 2 segundos, que pode ser feitos por:

- Definindo o modo de tamanho de GOP tamanho fixo GOP e
- o intervalo de quadro de chave como dois segundos.
- também definir o controle IDR GOP GOP fechado para garantir que todos os GOP estão em pé em suas próprias sem dependências

Para tornar nosso fluxo de trabalho conveniente entender, renomear o codificador AVC primeiro para "codificador de vídeo AVC 640 x 360 1200kbps" e o segundo codificador AVC "codificador de vídeo AVC 960 x 540 kbps 2500".

Agora adicione uma segunda ISO MPEG-4 multiplexador e um segundo arquivo de saída. Conecte o multiplexador com o codificador AVC novo e certificar-se de que sua saída é direcionada para a saída de arquivo. Em seguida, também se conecte entrada AAC codificador de áudio saída para o novo do multiplexador. A saída de arquivo por sua vez, em seguida, pode ser conectada para o nó de arquivo de saída/ativos para adicioná-lo para o ativo de serviços de mídia que será criado.

![Segunda Muxer e saída de arquivo conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Segunda Muxer e saída de arquivo conectado*

Para compatibilidade com embalagem dinâmica de serviços de mídia do Azure, configurar modo do Multiplexador de bloco para GOP contagem ou duração e definir os GOPs por bloco para 1. (Isso deve ser o padrão).

![Modos de bloco de Muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Modos de bloco de Muxer*

Observação: talvez você queira repetir esse processo para quaisquer outras resolução e taxa de bits combinações que você deseja ter adicionado à saída ativo.

###<a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Configurar os nomes de arquivo de saída

Temos mais de um único arquivo adicionado ao ativo saída. Isso fornece uma necessidade para certificar-se de que os nomes de arquivo para cada um dos arquivos de saída são diferentes uns dos outros e talvez ainda aplicar uma convenção de nomenclatura para que ele se torne limpar do nome do arquivo que você está lidando com.

Arquivo de nomenclatura de saída pode ser controlado por meio de expressões no designer. Abra o painel de propriedade para um dos componentes do arquivo de saída e abrir o editor de expressão para a propriedade de arquivo. Nosso primeiro arquivo de saída foi configurado através da seguinte expressão (consulte o tutorial para fizer [MXF para uma taxa de bits única saída MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Isso significa que nosso filename é determinada pelo duas variáveis: o diretório de saída para escrever no e o nome base do arquivo de origem. O primeiro é exposto como uma propriedade na raiz do fluxo de trabalho e o último é determinado pelo arquivo de entrada. Observe que o diretório de saída é o que você use para teste local; Essa propriedade será substituída pelo mecanismo de fluxo de trabalho quando o fluxo de trabalho é executado pelo processador baseado em nuvem mídia em serviços de mídia do Azure.
Para dar ambos os nossos arquivos de saída de um nomeação de saída consistente, altere o primeiro arquivo de nomenclatura expressão para:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

e o segundo para:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Execute um teste intermediário executar para certificar-se de que ambos os arquivos de saída MP4 são gerados corretamente.

###<a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Adicionando um controle de áudio separado

Como você verá mais tarde quando podemos gerar um arquivo de .ism com nossos arquivos de saída MP4, podemos também exigirá um arquivo MP4 somente de áudio como a faixa de áudio do nosso streaming adaptativa. Para criar esse arquivo, adicione um muxer adicional ao fluxo de trabalho (multiplexador ISO-MPEG-4) e conectar o pino de saída do codificador AAC com seu pin de entrada para controlar 1.

![Áudio Muxer adicionado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Áudio Muxer adicionado*

Crie um terceiro componente de saída de arquivo para o fluxo de saída da muxer de saída e configurar o arquivo de nomenclatura expressão como:
    
    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Áudio Muxer Criando arquivo de saída](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Áudio Muxer Criando arquivo de saída*

###<a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Adicionando o. Arquivo SMIL ISM

Para o pacote dinâmico trabalhar em combinação com MP4 arquivos (tanto o MP4 somente de áudio) no nosso ativo de serviços de mídia, também precisamos de um arquivo de manifesto (também chamado de arquivo de "SMIL": linguagem de integração de multimídia sincronizados). Esse arquivo indica aos serviços de mídia do Azure que arquivos MP4 estão disponíveis para embalagem dinâmica e qual da considerar para o fluxo de áudio. Um arquivo de manifesto típico de um conjunto do MP4 com um único fluxo de áudio tem esta aparência:
    
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>

O arquivo .ism contém dentro de uma instrução switch, uma referência para cada um dos arquivos de vídeo MP4 individuais e além essas referências de arquivo de áudio também um (ou mais) para um MP4 que contém apenas o áudio.

Gerar o arquivo de manifesto para nosso conjunto do MP4 pode ser feito por meio de um componente denominado o "gravador de manifesto AMS". Para usá-la, arraste-o para a superfície e conecte os pinos de saída "Escrever completa" dos três componentes de saída de arquivo à entrada AMS manifesto gravador. Verifique se conectem a saída do gravador de manifesto AMS para o ativo da arquivo de saída.

Como com nossos outros componentes de saída do arquivo, configure o nome de saída do arquivo de .ism com uma expressão:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Nosso fluxo de trabalho concluído se parece com a abaixo:

![Terminar MXF ao fluxo de trabalho do multibitrate MP4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Terminar MXF ao fluxo de trabalho do multibitrate MP4*

##<a id="MXF_to__multibitrate_MP4"></a>Codificação de MXF em multibitrate MP4 - esquema aprimorado

Na [explicação passo a passo anterior do fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) temos visto como um único ativo de entrada de MXF pode ser convertido em um ativo de saída com arquivos de MP4 multi-taxa de bits, um arquivo de MP4 somente de áudio e um arquivo de manifesto para uso em conjunto com embalagem dinâmico de serviços de mídia do Azure.

Este passo a passo mostrará como alguns dos aspectos podem ser aperfeiçoados e feitas mais conveniente.

###<a id="MXF_to_multibitrate_MP4_overview"></a>Visão geral do fluxo de trabalho para aprimorar

![Fluxo de trabalho Multibitrate MP4 para aprimorar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Fluxo de trabalho Multibitrate MP4 para aprimorar*

###<a id="MXF_to__multibitrate_MP4_file_naming"></a>Convenções de nome de arquivo

No fluxo de trabalho anterior podemos especificado uma expressão simples como base para gerar nomes de arquivo de saída. Temos algumas duplicações apesar: todos os os componentes do arquivo de saída individual especificado tal expressão.

Por exemplo, nossa componente de saída de arquivo para o primeiro arquivo de vídeo é configurada com essa expressão:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Tempo para a segunda saída vídeo, temos uma expressão como:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Não seria mais limpo, menos mais conveniente se nós pode remover algumas dessa duplicação e tornar as coisas mais configurável em vez disso e propensa a erros? Felizmente, podemos: recursos de expressão do designer em combinação com a capacidade de criar propriedades personalizadas no nosso raiz do fluxo de trabalho nos dará uma camada adicional de conveniência.

Vamos supor que estamos filename configuração será da unidade da bitrates dos arquivos MP4 individuais. Estas bitrates que será nosso objetivo é configurar em um local central (na raiz do nosso gráfico), de onde eles vai ser acessados para configurar e geração de nome de arquivo. Para fazer isso, vamos começar publicando a propriedade de taxa de bits de codificadores AVC na raiz do nosso fluxo de trabalho, para que ele fique acessível em ambos os raiz também exemplo, os codificadores AVC. (Mesmo se exibido em dois lugares diferentes, há apenas um único valor subjacente.)

###<a id="MXF_to__multibitrate_MP4_publishing"></a>Propriedades de componente em raiz do fluxo de trabalho de publicação

Abra o codificador AVC primeiro, vá para a propriedade de taxa de bits (kbps) e na lista suspensa, escolha publicar.

![A propriedade de taxa de bits de publicação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*A propriedade de taxa de bits de publicação*

Configure a caixa de diálogo Publicar para publicar na raiz do nosso gráfico de fluxo de trabalho, com o nome publicado de "video1bitrate" e um nome de exibição legíveis de "Taxa de bits de 1 de vídeo". Configurar um personalizado nome do grupo chamado "Streaming Bitrates" e pressionar publicar.

![A propriedade de taxa de bits de publicação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Caixa de diálogo publicação de propriedade de taxa de bits*

Repita o mesmo para a propriedade de taxa de bits do segundo codificador AVC e nomeie "video2bitrate" com um nome de exibição da "Taxa de 2 de vídeo," de no mesmo grupo personalizado "Streaming Bitrates".

Se agora, podemos inspecionar as propriedades do fluxo de trabalho raiz, veremos nosso grupo personalizado com as duas propriedades publicados aparecem. Ambos estão refletir o valor de seu respectivo taxa de bits de codificador de AVC.

![Propriedades de taxa de bits publicado na raiz do fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Sempre que queremos acessar essas propriedades do código ou de uma expressão, podemos fazer isso assim:

- de código embutido de um componente logo abaixo da raiz: node.getPropertyAsString('.. / video1bitrate', nulo)
- dentro de uma expressão: ${ROOT_video1bitrate}
 
Vamos concluir o grupo "Bitrates Streaming" Publicando nossa faixa de áudio taxa de bits nele também. Dentro das propriedades do codificador AAC, procure a configuração de taxa de bits e selecione publicar no menu suspenso ao lado dela. Publicar na raiz do gráfico com o nome "audio1bitrate" e exibir nome "Taxa de bits de 1 de áudio" em nosso grupo personalizado "Streaming Bitrates".

![Caixa de diálogo publicação para taxa de bits de áudio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Caixa de diálogo publicação para taxa de bits de áudio*

![Propriedades de áudio e vídeo resultantes na raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Propriedades de áudio e vídeo resultantes na raiz*

Observe que a alteração de qualquer desses três valores também configura novamente e altera os valores nos respectivos componentes são vinculados com (e onde publicada do).

###<a id="MXF_to__multibitrate_MP4_output_files"></a>Ter gerado o arquivo de saída nomes contam com valores de propriedade publicado

Em vez de codificar nossos nomes de arquivo gerado, podemos agora alterar nosso expressão filename em cada um dos componentes do arquivo de saída contar com as propriedades de taxa de bits que podemos acabou de ser publicados na raiz do gráfico. Começando com nossa primeira saída de arquivo, encontre a propriedade de arquivo e editar a expressão como esta:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Os parâmetros diferentes nesta expressão podem ser acessados e inseridos pressionando o cifrão no teclado enquanto estiver na janela de expressão. Um dos parâmetros disponíveis é nossa propriedade video1bitrate que estamos publicados anteriormente.

![Acessando parâmetros dentro de uma expressão](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Acessando parâmetros dentro de uma expressão*

Faça o mesmo para a saída de arquivo para nosso segundo vídeo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

e para a saída de arquivo somente áudio:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Se agora, podemos alterar a taxa de bits para qualquer um dos arquivos de áudio ou vídeo, será possível reconfigurar o codificador respectivo e a convenção de nome de arquivo baseado em taxa de bits será atendida automático.

##<a id="thumbnails_to__multibitrate_MP4"></a>A adição de miniaturas para multibitrate MP4 saída

Iniciando de um fluxo de trabalho que gera [uma multibitrate MP4 saída de um MXF de entrada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), podemos agora examinaremos em a adição de miniaturas à saída.

###<a id="thumbnails_to__multibitrate_MP4_overview"></a>Visão geral do fluxo de trabalho para adicionar miniaturas

![Fluxo de trabalho para iniciar a partir de Multibitrate MP4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Fluxo de trabalho para iniciar a partir de Multibitrate MP4*

###<a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Adição de codificação de JPG

O coração da nossa geração miniatura será o componente de codificador de JPG, capaz de arquivos JPG de saída.

![Codificador JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Codificador JPG*

Podemos não é possível conectar Entretanto diretamente nosso fluxo de vídeo descompactado provenientes da entrada de arquivo de mídia para o codificador JPG. Em vez disso, ele espera ser entregue quadros individuais. Isso, podemos fazer por meio do componente de Gate de quadro de vídeo.

![Conectando a uma entrada de quadro com o codificador JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Conectando a uma entrada de quadro com o codificador JPG*

A entrada de quadro após cada tantas segundos ou quadros permite que um quadro de vídeo passar. O intervalo e o tempo de deslocamento com que isso acontece é configurável nas propriedades.

![Propriedades de entrada de quadro de vídeo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Propriedades de entrada de quadro de vídeo*

Vamos criar uma miniatura de cada minuto, definindo o modo de tempo (segundos) e o intervalo de 60.

###<a id="thumbnails_to__multibitrate_MP4_color_space"></a>Lidando com conversão de espaço de cores

Embora seria parecer lógico que ambos os pinos de vídeo descompactado de gate o quadro e a entrada de arquivo de mídia podem ser conectados agora, podemos obtido um aviso se fazer isso.

![Erro de espaço de cor de entrada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Erro de espaço de cor de entrada*

Isso ocorre porque a forma na qual cor informações são representadas no nosso original bruto descompactado fluxo de vídeo, vêm do nosso MXF, é diferente do que o codificador de JPG está esperando. Mais especificamente, uma chamada "espaço de cores" de "RGB" ou "Escala de cinza" espera fluxo no. Isso significa que o fluxo de vídeo entrado do vídeo quadro Gate precisará ter uma conversão aplicada primeiro com relação ao seu espaço de cores.

Arraste o fluxo de trabalho o conversor de espaço de cor - Intel e conectá-lo à nossa gate de quadro.

![Conectando um conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Conectando um conversor de espaço de cor*

Na janela de propriedades, selecione a entrada BGR 24 na lista de predefinir.

###<a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Escrever as miniaturas

Difere do nosso vídeo MP4, o componente de codificador de JPG produzirá mais de um arquivo. Para lidar com isso, um componente de cena pesquisa JPG arquivo gravador pode ser usado: ele levar as miniaturas JPG entradas e escreva-las, cada nome de arquivo que está sendo sufixo por um número diferente. (O número normalmente indicando o número de segundos/unidades no qual a miniatura foi desenhada de fluxo.)


![Apresentando o gravador de arquivo JPG cena pesquisa](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Apresentando o gravador de arquivo JPG cena pesquisa*

Configurar a propriedade de caminho de pasta de saída com a expressão: ${ROOT_outputWriteDirectory} 

e a propriedade Filename prefixo com: 

    ${ROOT_sourceFileBaseName}_thumb_

O prefixo determinará como os arquivos em miniatura estão sendo nomeados. Eles serão ser sufixados com um número indicando a posição da miniatura no fluxo.


![Propriedades de pesquisa JPG arquivo gravador de cena](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Propriedades de pesquisa JPG arquivo gravador de cena*

Conecte o gravador de arquivo JPG cena pesquisa para o nó de arquivo de saída/ativo.

###<a id="thumbnails_to__multibitrate_MP4_errors"></a>Detectar erros em um fluxo de trabalho

Conecte a entrada do conversor de espaço cor à saída de vídeo descompactada bruta. Agora execute um teste local do fluxo de trabalho. Há uma boa chance do fluxo de trabalho inesperadamente interromperá a execução e indicar com um contorno vermelho no componente que encontrou um erro:

![Erro de conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Erro de conversor de espaço de cor*

Clique no pequeno ícone de "E" vermelho na parte superior direita de canto do componente conversor de espaço de cores para ver o que é o motivo pelo qual a tentativa de codificação falha.

![Diálogo de erro de conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Diálogo de erro de conversor de espaço de cor*

Na verdade, como você pode ver, que o espaço de cores de entrada padrão para o conversor de espaço de cor deve ser rec601 para nossa conversão solicitada do YUV para RGB. Parece nosso fluxo não indica é rec601. (Registro 601 é um padrão de codificação entrelaçados sinais de vídeo analógicos em forma de vídeo digital. Especifica uma região ativa abrangendo 720 amostras de luminosidade e 360 amostras de chrominance por linha. A cor do sistema de codificação é conhecida como YCbCr 4:2:2.)

Para corrigir isso, será indicamos nos metadados do nosso fluxo que estamos lidando com conteúdo rec601. Para fazê-lo, usaremos um componente atualizador de tipo de dados de vídeo, que colocaremos entre os nosso fonte bruto e o componente de conversão de espaço de cor. Esse atualizador de tipo de dados permite a atualização manual de determinados dados vídeo propriedades de tipo. Configurá-lo para indicar um padrão para espaço de cor de "Registro 601". Isso fará com que o atualizador de tipo de dados de vídeo marcar o fluxo com o espaço de cor "Registro 601" não se houvesse nenhum espaço de cor foi definido. (Ele não substituirá os metadados existentes, a menos que a caixa de seleção de substituição foi marcada.)

![Atualizando padrão de espaço de cor no atualizador de tipo de dados](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Atualizando padrão de espaço de cor no atualizador de tipo de dados*

###<a id="thumbnails_to__multibitrate_MP4_finish"></a>Fluxo de trabalho concluído

Agora que nosso nosso fluxo de trabalho for concluído, faça outro teste executar para vê-la a passar.

![Fluxo de trabalho terminado de saída de multi-mp4 com miniaturas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Fluxo de trabalho terminado de saída de multi-mp4 com miniaturas*

##<a id="time_based_trim"></a>Filtragem baseada em vez de saída multibitrate MP4

Iniciando de um fluxo de trabalho que gera [uma multibitrate MP4 saída de um MXF de entrada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), podemos agora examinaremos em aparar o vídeo de origem com base em carimbos de hora.

###<a id="time_based_trim_start"></a>Visão geral do fluxo de trabalho para iniciar a adição de filtragem para

![Iniciando o fluxo de trabalho para adicionar filtragem para](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Iniciando o fluxo de trabalho para adicionar filtragem para*

###<a id="time_based_trim_use_stream_trimmer"></a>Usando o filtro de fluxo

O componente de filtro de fluxo permite cortar o começo e o final de um fluxo de entrada base Cronometragem informações (segundos, minutos,...). O filtro não oferece suporte a filtragem baseada em quadro.

![Filtro de fluxo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Filtro de fluxo*

Em vez de vinculação a codificadores AVC e cedente de posição de alto-falante para a entrada de arquivo de mídia diretamente, colocaremos entre aqueles o filtro de fluxo. (Uma para o sinal de vídeo e para o sinal de áudio intercalado.)

![Colocar o filtro de fluxo entre](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Colocar o filtro de fluxo entre*

Vamos configurar o filtro para que possamos apenas processar vídeo e áudio entre 15 e 60 segundos no vídeo.

Vá para as propriedades do filtro de fluxo de vídeo e configurar propriedades de hora de término (60s) e de hora de início (15 anos). Para certificar-se de que tanto nosso filtro de áudio e vídeo sempre é configurado para os mesmos início e término valores, podemos publica aqueles na raiz do fluxo de trabalho.

![Publicar a propriedade de hora de início do filtro de fluxo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publicar a propriedade de hora de início do filtro de fluxo*

![Publicar a caixa de diálogo de propriedade para o início](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Publicar a caixa de diálogo de propriedade para o início*

![Publicar a caixa de diálogo de propriedade para hora de término](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Publicar a caixa de diálogo de propriedade para hora de término*


Se agora, vamos inspecionar raiz do nosso fluxo de trabalho, ambas as propriedades será claramente exibido e configurável de lá.

![Publicado propriedades disponíveis na raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Publicado propriedades disponíveis na raiz*

Agora, abrir as propriedades de filtragem do filtro de áudio e configurar horários de início e término com uma expressão que se refere às propriedades publicadas na raiz do nosso fluxo de trabalho.

Para a hora de início de filtragem de áudio:

    ${ROOT_TrimmingStartTime}

e para sua hora de término:

    ${ROOT_TrimmingEndTime}

###<a id="time_based_trim_finish"></a>Fluxo de trabalho concluído

![Fluxo de trabalho concluído](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Fluxo de trabalho concluído*


##<a id="scripting"></a>Introdução ao componente de script

Componentes de script podem executar scripts aleatório durante as fases de execução de nosso fluxo de trabalho. Há quatro diferentes scripts que podem ser executados, cada uma com características específicas e sua próprias local no ciclo de vida do fluxo de trabalho:

- **commandScript**
- **realizeScript**
- **processInputScript**
- **lifeCycleScript**

A documentação do componente script vai mais detalhadamente para cada uma das opções acima. [A seção a seguir](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), o componente de script **realizeScript** é usado para construir um xml cliplist dinamicamente quando o fluxo de trabalho é iniciado. Esse script é chamado durante a instalação do componente, o que acontece apenas uma vez em seu ciclo de vida.


###<a id="scripting_hello_world"></a>Script dentro de um fluxo de trabalho: Olá

Arraste um componente de script para a superfície de designer e renomeie-(por exemplo, "SetClipListXML").

![Adicionando um componente de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionando um componente de script*

Quando você inspecionar as propriedades do componente script, os quatro tipos diferentes de script será mostrado, cada configurável para um script diferente.

![Propriedades do componente de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades do componente de script*

Limpe o processInputScript e abrir o editor para o realizeScript. Agora podemos estão configurados e pronto para iniciar o script.

Scripts são gravados em Groovy, uma linguagem de script compilada dinamicamente para a plataforma de Java que mantém a compatibilidade com Java. Na verdade, a maioria dos códigos de Java é código Groovy válido.

Vamos escrever um script groovy do mundo de saudação simples no contexto de nosso realizeScript. Digite o seguinte no editor de:

    node.log("hello world");

Agora, execute uma execução de teste local. Após essa execução, inspecione a propriedade de Logs (por meio da guia do sistema no componente script).

![Olá mundo log de saída](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Olá mundo log de saída*

O objeto de nó chamamos o método de log, se refere a nossa "nó" atual ou o componente nós estiver script dentro. Assim, cada componente tem a capacidade de dados de log de saída, disponível por meio da guia do sistema. Nesse caso, estamos saída o literal de cadeia de caracteres "Olá". É importante entender aqui é que isso pode provar para ser uma ferramenta valiosa de depuração, fornecendo informações sobre o que o script está realmente fazendo.

De dentro de nosso ambiente de script, também temos acesso às propriedades em outros componentes. Tente o seguinte:


    //inspect current node: 
    def nodepath = node.getNodePath(); 
    node.log("this node path: " + nodepath);
    
    //walking up to other nodes: 
    def parentnode = node.getParentNode(); 
    def parentnodepath = parentnode.getNodePath(); 
    node.log("parent node path: " + parentnodepath);
    
    //read properties from a node: 
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null ); 
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null); 
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);

Nossa janela de log, nos mostrará o seguinte:

![Saída do log para acessar caminhos de nó](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Saída do log para acessar caminhos de nó*


##<a id="frame_based_trim"></a>Filtragem baseada em quadro de saída multibitrate MP4

Iniciando de um fluxo de trabalho que gera [uma multibitrate MP4 saída de um MXF de entrada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), podemos agora examinaremos em aparar o vídeo de origem com base nas contagens de quadro.

###<a id="frame_based_trim_start"></a>Visão geral do esquema para iniciar a adição de filtragem para

![Fluxo de trabalho para iniciar a adição de filtragem para](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Fluxo de trabalho para iniciar a adição de filtragem para*

###<a id="frame_based_trim_clip_list"></a>Usando a lista de Clip XML

Em todos os tutoriais de fluxo de trabalho anterior, usamos o componente de entrada de arquivo de mídia como nossa fonte de entrada de vídeo. Para esse cenário específico no entanto, usaremos o componente origem de lista do clipe, em vez disso. Observe que isso não deve ser a melhor maneira de trabalho; use somente a fonte de lista do clipe quando há um motivo real para fazê-lo (como na abaixo caso, onde estamos fazendo uso dos recursos de filtragem de lista de clip).

Para alternar do nosso entrada de arquivo de mídia com a fonte de lista do clipe, arraste o componente origem de lista do clipe na superfície de design e conecte o pin do clipe lista XML para o nó XML de lista do clipe de designer de fluxo de trabalho. Isso deve preencher a fonte de lista do clipe com pinos de saída, de acordo com o nosso vídeo de entrada. Agora, conectar os pinos descompactado áudio e vídeo descompactado da a origem de lista do clipe ao respectivo AVC codificadores e Interleaver de fluxo de áudio. Agora, remova a entrada de arquivo de mídia.

![Substituídas a entrada de arquivo de mídia com a fonte de lista do clipe](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Substituídos a entrada de arquivo de mídia com a fonte de lista do clipe*

O componente origem de lista do clipe toma como entrada um "Clip lista XML". Ao selecionar o arquivo de origem para testar com localmente, esse xml de lista do clipe é preenchido automaticamente para você.

![Propriedade de XML de lista do clipe preenchido automaticamente](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Propriedade de XML de lista do clipe preenchido automaticamente*

Procurando um pouco mais próximo de xml, isso é como ele se parece com:

![Editar a caixa de diálogo de lista de clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Editar a caixa de diálogo de lista de clip*

No entanto, isso não refletir os recursos do clipe lista xml. Uma opção que temos é adicionar um elemento de "Cortar" em de áudio e vídeo origem, assim:

![Adicionando um elemento arrumar a da lista de clipes](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Adicionando um elemento arrumar a da lista de clipes*

Se você modificar o xml de lista do clipe assim acima e executa um teste local, você verá o vídeo corretamente foi cortado entre 10 e 20 segundos no vídeo.

Ao contrário do que acontece quando você fizer uma execução local no entanto, esse mesmo xml cliplist não teria o mesmo efeito quando aplicados em um fluxo de trabalho é executado em serviços de mídia do Azure. Quando o Azure Premium codificador for iniciado, o xml cliplist é gerado sempre que novamente, com base no arquivo de entrada que do trabalho de codificação foi fornecido. Isso significa que qualquer alteração que fazemos no xml Infelizmente deve ser substituída.

Para combater o xml cliplist seja apagado quando um trabalho de codificação é iniciado, podemos novamente gerar-instantâneos logo após o início do nosso fluxo de trabalho. Essas ações personalizadas podem ser feitas por meio do que é chamado um "componente de script". Para obter mais informações, consulte [Introdução ao componente de script](media-services-media-encoder-premium-workflow-tutorials.md#scripting).


Arraste um componente de script para a superfície de designer e renomeá-lo para "SetClipListXML".

![Adicionando um componente de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionando um componente de script*

Quando você inspecionar as propriedades do componente script, os quatro tipos diferentes de script será mostrado, cada configurável para um script diferente.

![Propriedades do componente de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades do componente de script*


###<a id="frame_based_trim_modify_clip_list"></a>Modificando a lista de clipes de um componente de script

Antes de podemos novamente escrever xml cliplist gerado durante a inicialização de fluxo de trabalho, podemos precisará tem acesso à propriedade de xml cliplist e conteúdo. Podemos fazer isso assim:

    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);

![Lista de clipes de entrada está sendo registrada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Lista de clipes de entrada está sendo registrada*

Primeiro, precisamos uma maneira de determinar quais ponto até o ponto em que queremos cortar o vídeo. Para fazer isso conveniente para o usuário menos técnica do fluxo de trabalho, publica duas propriedades na raiz do gráfico. Para fazer isso, clique com botão direito a superfície e selecione "Adicionar propriedade":

- Primeira propriedade: "ClippingTimeStart" do tipo: "Código de tempo"
- Segunda propriedade: "ClippingTimeEnd" do tipo: "Código de tempo"

![Adicionar caixa de diálogo de propriedade para hora de início de corte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Adicionar caixa de diálogo de propriedade para hora de início de corte*

![Publicado recorte propriedades de tempo na raiz do fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publicado recorte propriedades de tempo na raiz do fluxo de trabalho*

Configure as propriedades para um valor adequado:

![Configurar o início de recorte e encerrar propriedades](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Configurar o início de recorte e encerrar propriedades*

Agora, nosso script, estamos pode acessar ambas as propriedades, como esta:

    
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);

![Janela de log mostrando o início e fim do corte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Janela de log mostrando o início e fim do corte*

Vamos analisar as cadeias de caracteres de código de tempo em um mais conveniente usar formulário, usando uma expressão regular simples:
    
    //parse the start timing: 
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1); 
    node.log("timecode start is: " + starttimecode); 
    def startframerate = startregresult.group(2); 
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend); 
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2); 
    node.log("framerate end is: " + endframerate);

![Janela de log com saída de código de tempo analisada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Janela de log com saída de código de tempo analisada*

Com essas informações à mão, agora podemos modificar o xml cliplist para refletir as horas de início e término para o recorte de quadro precisos desejado do filme.

![Código de script para adicionar elementos ARRUMAR](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Código de script para adicionar elementos ARRUMAR*

Isso foi feito por meio de operações de manipulação de cadeia de caracteres normal. O xml de lista resultante do clipe modificado é gravado a propriedade clipListXML na raiz do fluxo de trabalho através do método "setProperty". A janela de log após a execução de outro teste faria nos mostram o seguinte:

![Registro em log da lista de clipes resultante](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Registro em log da lista de clipes resultante*

Faça um teste executar para ver como os fluxos de vídeo e áudio tiveram sido recortados. Como você vai fazer mais de uma execução de teste com valores diferentes para os pontos de filtragem, você notará que aqueles não serão realizadas em consideração Entretanto! O motivo para isso é que o designer, ao contrário de tempo de execução do Azure, não substitua o xml cliplist cada execução. Isso significa que somente na primeira vez que você definiu de entrada e saída pontos, causará o xml transformar, todos os outros horários, nossa cláusula de guarda (se (clipListXML.indexOf ("<trim>") = = -1)) impedirá que o fluxo de trabalho adicionando outro elemento ARRUMAR quando já existe um presente.

Para tornar nosso fluxo de trabalho conveniente para testar localmente, melhor adicionamos alguns códigos de manutenção de casa que verifica se um elemento ARRUMAR já estava presente. Nesse caso, estamos pode removê-la antes de continuar modificando o xml com os novos valores. Em vez de usar manipulação de cadeia de caracteres simples, é provavelmente mais seguro fazer isso por meio do modelo de objeto do xml real análise.

Antes que possamos adicionar esse código embora, que precisaremos adicionar um número de instruções de importação no início do nosso script primeiro:
    
    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;

Depois disso, podemos adicionar o código de limpeza necessário:

    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already: 
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes: 
    elementsToDelete.each{ 
        e -> e.getParentNode().removeChild(e);
    }; 
    node.log("deleted any existing trim nodes");
    
    //serialize the modified clip list xml dom into a string: 
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result); 
    clipListXML = result.getWriter().toString();
    
Este código vai logo acima do ponto em que adicionamos os elementos ARRUMAR ao xml cliplist.

Neste ponto, podemos executar e modificar nosso fluxo de trabalho como quanto queremos tendo as alterações sejam aplicadas nunca tempo.    

###<a id="frame_based_trim_clippingenabled_prop"></a>Adicionando uma propriedade de conveniência ClippingEnabled

Como você talvez não sempre deseja aparar para acontecer, vamos terminar desativar nosso fluxo de trabalho, adicionando um sinalizador booliano conveniente que indique se deseja ou não podemos habilitar filtragem / recorte.

Assim como antes, publique uma nova propriedade na raiz do nosso fluxo de trabalho chamado "ClippingEnabled" do tipo "BOOLIANO".

![Publicado uma propriedade para habilitar recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publicado uma propriedade para habilitar recorte*

Com o abaixo cláusula de guarda simples, podemos verificar se filtragem for necessária e decidir se nossa lista de clipes como tal precisa ser modificada ou não.

    //check if clipping is required: 
    def clippingrequired = node.getProperty("../ClippingEnabled"); 
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML); 
        node.log("no clipping required"); 
        return; 
    }


###<a id="code"></a>Código completo

    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;
    
    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping: 
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);
    
    //for local testing: delete any pre-existing trim elements 
    //from the clip list xml by parsing the xml into a DOM:
    
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder(); 
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath(); 
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }
    
    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e -> 
        e.getParentNode().removeChild(e); 
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return; 
    }

    //add trim elements to cliplist xml 
    if ( clipListXML.indexOf("<trim>") == -1 ) 
    {
        //trim video 
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode + 
            " </outPoint>\n </trim> \n"); 
        //trim audio 
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" + 
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML ); 
        node.setProperty("../clipListXml",clipListXML); 
    }


##<a name="also-see"></a>Consulte também 

[Apresentando o Premium codificação no Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Como usar Premium codificação no Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Codificação de conteúdo sob demanda com o serviço de mídia do Microsoft Azure](media-services-encode-asset.md#media_encoder_premium_workflow)

[Codecs e formatos de fluxo de trabalho de Premium de codificador de mídia](media-services-premium-workflow-encoder-formats.md)

[Arquivos de fluxo de trabalho de exemplo](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Ferramenta de Explorador de serviços de mídia do Azure](http://aka.ms/amse)

##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
