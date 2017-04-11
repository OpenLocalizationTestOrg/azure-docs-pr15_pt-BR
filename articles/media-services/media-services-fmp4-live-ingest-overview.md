<properties 
    pageTitle="Especificação de inclusão de serviços de mídia do Azure MP4 fragmentados live | Microsoft Azure" 
    description="Esta especificação descreve o protocolo e o formato MP4 fragmentado com base inclusão de streaming ao vivo para serviços de mídia do Microsoft Azure. Serviços de mídia do Microsoft Azure fornece serviço de streaming live que permite aos clientes transmitir eventos ao vivo e transmitir conteúdo em tempo real usando o Microsoft Azure como a plataforma de nuvem. Este documento também discute as melhores práticas em criar altamente redundantes e robustos ao vivo inclusão mecanismos." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"     
    ms.author="cenkdin;juliako"/>

#<a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Especificação de inclusão de serviços de mídia do Azure MP4 fragmentados live

Esta especificação descreve o protocolo e o formato MP4 fragmentado com base inclusão de streaming ao vivo para serviços de mídia do Microsoft Azure. Serviços de mídia do Microsoft Azure fornece serviço de streaming live que permite aos clientes transmitir eventos ao vivo e transmitir conteúdo em tempo real usando o Microsoft Azure como a plataforma de nuvem. Este documento também discute as melhores práticas em criar altamente redundantes e robustos ao vivo inclusão mecanismos.


##<a name="1-conformance-notation"></a>1. notação de conformidade

As palavras-chave "deve", "Não deve", "Obrigatória", "Deverá", "Não deverá", "Deveria", "Não deve", "Recomendado", "Pode" e "Opcional" neste documento devem ser interpretadas como descrito na RFC 2119.

##<a name="2-service-diagram"></a>2. diagrama de serviço de 

O diagrama a seguir mostra a arquitetura de alto nível do serviço de streaming do live em serviços de mídia do Microsoft Azure:

1.  Codificador Live envia feeds ao vivo em canais que são criadas e provisionados via o SDK do Microsoft Azure Media Services.
2.  Canais, programas e Streaming ponto de extremidade na alça de serviços de mídia do Microsoft Azure todas as funcionalidades de streaming live incluindo recebimento, formatação, nuvem DVR, segurança, escalabilidade e redundância.
3.  Opcionalmente os clientes podem escolher implantar uma camada CDN entre o ponto de extremidade de Streaming e os pontos de extremidade do cliente.
4.  Fluxo de extremidade de cliente do ponto de extremidade Streaming usando protocolos HTTP adaptativa Streaming (por exemplo, Streaming suave, traço, HDS ou HLS).

![Image1][image1]


##<a name="3-bit-stream-format--iso-14496-12-fragmented-mp4"></a>3. formato de fluxo de bits – ISO 14496-12 fragmentado MP4

Formato de conexão para streaming live inclusão discutido neste documento se baseia [ISO-14496-12]. Consulte [[MS-SSTR]](http://msdn.microsoft.com/library/ff469518.aspx) para obter explicação detalhada de formato MP4 fragmentado e extensões para ambos os arquivos de vídeo sob demanda e live streaming inclusão.

###<a name="live-ingest-format-definitions"></a>Ao vivo inclusão definições de formato 

A seguir é uma lista de formato especial definições que se aplicam para dinâmicos inclusão em serviços de mídia do Microsoft Azure:

1. A caixa 'moov', 'ftyp' e LiveServerManifestBox devem ser enviadas com cada solicitação (HTTP POST).  Ele deve ser enviado no começo do fluxo e sempre que o codificador deve reconectar para retomar fluxo inclusão.  Consulte a seção 6 em [1] para obter mais detalhes.
2. Seção 3.3.2 em [1] define uma caixa opcional chamada StreamManifestBox para live inclusão. Devido a lógica de roteamento de Balanceador de carga do Microsoft Azure, uso dessa caixa está obsoleta e não devem estar presentes quando a inclusão no serviço de mídia do Microsoft Azure. Se essa caixa estiver presente, serviços de mídia do Azure silenciosamente ignorará.
3. O TrackFragmentExtendedHeaderBox definido no 3.2.3.2 em [1] deve estar presente para cada fragmento.
4. Versão 2 do TrackFragmentExtendedHeaderBox ser usadas para gerar segmentos de mídia com URLs idênticos em vários data centers. O campo de índice de fragmento é necessária para formatos de data center cruzado failover de fluxo de baseado no índice como Apple HTTP Live Streaming (HLS) e MPEG-traço baseada no índice.  Para habilitar o failover do data center cruzado, o índice de fragmento deve ser sincronizado entre vários codificadores e aumente por 1 para cada fragmento de mídia sucessivas, mesmo em codificador reiniciar ou falhas.
5. Seção 3.3.6 em [1] define caixa denominada MovieFragmentRandomAccessBox ('mfra') que pode ser enviada no final da inclusão ao vivo para indicar EOS (fim do fluxo) para o canal. Devido a lógica de recebimento de serviços de mídia do Azure, uso da EOS (fim do fluxo) está obsoleta e a caixa 'mfra' para inclusão ao vivo não devem ser enviadas. Se enviado, serviços de mídia do Azure silenciosamente ignorará. É recomendável usar o [Canal redefinir](https://msdn.microsoft.com/library/azure/dn783458.aspx#reset_channels) para redefinir o estado do ponto de recebimento e também é recomendável usar o [Programa parar](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) para encerrar uma apresentação e fluxo.
6. A duração de fragmento MP4 deve ser constante, para reduzir o tamanho dos manifestos cliente e aprimorar heurística de download do cliente por meio do uso de marcas de repetição.  A duração pode flutuar para compensar taxas de quadro não inteiro.
7. A duração de fragmento MP4 deve estar entre aproximadamente 2 e 6 segundos.
8. Carimbos de fragmento MP4 e índices (TrackFragmentExtendedHeaderBox fragment_ absolute_ tempo e fragment_index) devem chegar em ordem crescente.  Embora os serviços de mídia do Azure é flexível a fragmentos duplicados, ele tem muito limitado a capacidade de reordenar fragmentos de acordo com o cronograma de mídia.

##<a name="4-protocol-format--http"></a>4. formato de protocol – HTTP

ISO fragmentado MP4 com base em tempo real inclusão para solicitar um padrão de execução prolongadas HTTP POST para transmitir dados de mídia codificado fornecidos em formato MP4 fragmentado para o serviço de usos de serviços de mídia do Microsoft Azure. Cada POSTAGEM HTTP envia uma completa fragmentado MP4 fluxo de bits ("fluxo") a partir começando com caixas de cabeçalho (caixa 'moov', 'ftyp' e "Live manifesto caixa servidor") e continuar com uma sequência de fragmentos (caixas 'moof' e 'mdat'). Consulte a seção em 9.2 [1] sintaxe de URL de solicitação de HTTP POST. Um exemplo da URL POSTAGEM é: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

###<a name="requirements"></a>Requisitos

Aqui estão os requisitos detalhados:

1. Codificador deve começar a transmissão enviando uma solicitação de HTTP POST com um vazio "corpo" (conteúdo comprimento zero) usando a mesma URL de inclusão. Isso pode ajudar a detectar rapidamente se o ponto de extremidade de inclusão ao vivo é válido e se houver qualquer autenticação ou outras condições necessárias. Por protocolo HTTP, o servidor não será capaz de enviar resposta HTTP voltar até que a solicitação inteira, incluindo o corpo da POSTAGEM for recebida. Considerando a natureza longa duração do evento ao vivo, sem essa etapa, o codificador talvez não consiga detectar qualquer erro até que ele termine enviar todos os dados.
2. Codificador deve tratar erros ou desafios de autenticação como resultado de (1). Se (1) é bem sucedida com uma resposta 200, continuar.
3. Codificador deve começar uma nova solicitação de HTTP POST com o fluxo de MP4 fragmentado.  A carga deve começar com as caixas de cabeçalho seguidas por fragmentos.  Observe que a caixa 'moov', 'ftyp' e "Live manifesto caixa servidor" (na ordem) deve ser enviada com cada solicitação, mesmo se o codificador deve reconectar porque a solicitação anterior foi encerrada antes do fim do fluxo. 
4. Codificador deve usar transferir codificação em partes para carregamento desde que é impossível prever o comprimento de conteúdo inteiro do evento ao vivo.
5. Quando o evento termina, após enviar o último fragmento, o codificador normalmente deve terminar a sequência de mensagem transferir codificação em partes (a maioria das pilhas de cliente HTTP lidar com ela automaticamente). Codificador deve esperar no serviço retornar o código de resposta final e, em seguida, encerrar a conexão. 
6. Codificador não devem usar o substantivo Events() conforme descrito em 9.2 [1] para a inclusão ao vivo em serviços de mídia do Microsoft Azure.
7. Se a solicitação de HTTP POST termina ou atinge o tempo limite antes do final do fluxo com um erro TCP, o codificador deve emitir uma nova solicitação de POSTAGEM usando uma conexão de novo e siga os requisitos acima com o requisito adicional que o codificador deve reenviar os dois fragmentos de MP4 anterior para cada faixa no fluxo e retomar sem introduzir descontinuidades na linha do tempo de mídia.  Reenviar os dois últimos fragmentos MP4 para cada faixa garante que não há nenhuma perda de dados.  Em outras palavras, se um fluxo contém um controle de áudio e vídeo e a solicitação POST atual falha, o codificador deve se reconectar e reenviar os dois últimos fragmentos para a faixa de áudio, que anteriormente foram enviadas com êxito, e os dois últimos fragmentos para a faixa de vídeo, que foram anteriormente com êxito enviados, para garantir que haja sem perda de dados.  O codificador deve manter um buffer "encaminhar" de fragmentos de mídia que ele reenvie quando se reconectar.

##<a name="5-timescale"></a>5. escala de tempo 

[[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) descreve o uso da "Escala de tempo" para SmoothStreamingMedia (seção 2.2.2.1), StreamElement (seção 2.2.2.3), StreamFragmentElement(2.2.2.6) e LiveSMIL (seção 2.2.7.3.1). Se o valor de escala de tempo não estiver presente, o valor padrão usado é 10.000.000 (MHz 10). Embora a especificação de formato de Streaming suave não bloquear o uso de outros valores de escala de tempo, a maioria dos usos implementações codificador esse valor padrão (10 MHz) para gerar Streaming suave inclusão dados. Devido a recurso de [Embalagem dinâmico de mídia do Azure](media-services-dynamic-packaging-overview.md) , é recomendável usar kHz 90 a escala de tempo de fluxos de vídeo e kHz 44,1 ou 48.1 para fluxos de áudio. Se os valores de escala de tempo diferentes são usados para fluxos de diferentes, a escala de tempo de nível de fluxo deve ser enviada. Consulte [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

##<a name="6-definition-of-stream"></a>6. definição de "Fluxo"  

"Fluxo" é a unidade básica de operação inclusão ao vivo para redigir apresentação ao vivo, manipulando failover streaming e cenários de redundância. "Fluxo" é definido como um exclusivo fragmentado MP4 fluxo de bits que pode conter uma única faixa ou várias faixas. Uma apresentação ao vivo completo pode conter um ou mais fluxos dependendo da configuração do encoder(s) ao vivo. Os exemplos a seguir ilustram várias opções de como usar stream(s) para compor uma apresentação ao vivo completo.

**Exemplo:** 

Cliente deseja criar uma apresentação ao vivo de streaming que inclui as seguintes bitrates de áudio/vídeo:

Vídeo – 3000kbps, 1500kbps, 750kbps

Áudio – 128kbps

###<a name="option-1-all-tracks-in-one-stream"></a>Opção 1: Todas as faixas em um fluxo

Nessa opção, um único codificador gera todas as faixas de áudio/vídeo e agrupá-los em um fragmentado MP4 fluxo de bits que obtém enviado por meio de uma única conexão de HTTP POST. Neste exemplo, há apenas um fluxo para esta apresentação ao vivo:

![image2][image2]

###<a name="option-2-each-track-in-a-separate-stream"></a>Opção 2: Cada faixa em um fluxo separado

Nessa opção, o encoder(s) só colocar um controlar para cada fluxo de bits fragmento MP4 e postar todos os fluxos em várias conexões HTTP separadas. Isso pode ser feito com codificadores uma ou várias codificadores. Do ponto de vista da inclusão ao vivo, esta apresentação ao vivo é composta de quatro fluxos.

![image3][image3]

###<a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Opção 3: Agrupar faixa de áudio com a faixa de vídeo de taxa de bits mais baixa em um fluxo

Essa opção, o cliente escolhe para reunir a faixa de áudio com a faixa de vídeo de taxa de bits mais baixa para o fluxo de bits de fragmento MP4 um e deixar outras duas faixas de vídeo cada sendo seu próprio fluxo. 


![image4][image4]


###<a name="summary"></a>Resumo

O que é mostrado acima não é uma lista completa de todas as opções de inclusão possíveis para este exemplo. Na verdade, qualquer agrupamento de faixas em fluxos é compatível com a inclusão ao vivo. Clientes e fornecedores de codificador podem escolher suas próprias implementações baseadas em complexidade engenharia, capacidade de codificador e redundância e considerações de failover. No entanto deve observar que na maioria dos casos há apenas uma trilha de áudio de toda a apresentação ao vivo portanto é importante garantir que o healthiness do fluxo de recebimento que contém a faixa de áudio. Essa consideração muitas vezes resulta em colocar a faixa de áudio em seu próprio fluxo (como em opção 2) ou agrupando-lo com a faixa de vídeo de taxa de bits mais baixa (como em opção 3). Também para melhor redundância e tolerância, enviando a controlar o áudio mesmo em dois fluxos de diferentes (opção 2 com faixas de áudio redundantes) ou agrupando a faixa de áudio pelo menos duas as faixas de vídeo de taxa de bits mais baixos (opção 3 com áudio agrupado em pelo menos dois fluxos de vídeo) é altamente recomendável para ao vivo inclusão em serviços de mídia do Microsoft Azure.

##<a name="7-service-failover"></a>7. Failover de serviço 

Fornecido natureza do streaming ao vivo, suporte a failover boa é essencial para garantir a disponibilidade do serviço. Serviços de mídia do Microsoft Azure destina-se para lidar com vários tipos de falhas, incluindo erros de rede, erros de servidor, problemas de armazenamento, etc. Quando usado em conjunto com lógica de failover apropriado do lado codificador ao vivo, o cliente pode obter um serviço de streaming live altamente confiável da nuvem.

Nesta seção, abordaremos cenários de failover do serviço. Nesse caso, a falha manifestos próprio como um erro de rede e acontece em algum lugar dentro do serviço. Aqui estão algumas recomendações para a implementação de codificador para lidar com failover de serviço:


1. Use um limite de segunda 10 para estabelecer a conexão de TCP.  Se uma tentativa de estabelecer a conexão levar mais de 10 segundos, anular a operação e tente novamente. 
2. Use um tempo limite curto para enviar a solicitação HTTP blocos de mensagem.  Se a duração de fragmento de destino MP4 N segundos, use um tempo limite de envio entre N e 2N segundos; Por exemplo, use um tempo limite de 6 a 12 segundos se a duração de fragmento MP4 é 6 segundos.  Se ocorrer um tempo limite, redefina a conexão, abra uma nova conexão e retomar fluxo inclusão sobre a nova conexão. 
3. Manter um buffer sucessivas que contém os dois últimos fragmentos, para cada faixa, com êxito e completamente enviadas para o serviço.  Se a solicitação de HTTP POST para um fluxo é encerrada ou atinge o tempo limite antes do final do fluxo abrir uma nova conexão e comece a outra solicitação de HTTP POST, reenviar os cabeçalhos de fluxo, reenviar os dois últimos fragmentos para cada faixa e retomar o fluxo sem introduzir uma descontinuidade na linha do tempo de mídia.  Isso reduz a chance de perda de dados.
4. É recomendável que o codificador não limitar o número de tentativas para estabelecer uma conexão ou retomar streaming depois que ocorre um erro TCP.
5. Após um erro de TCP:
    1. A conexão atual deve ser fechado e uma nova conexão deve ser criada para uma nova solicitação de HTTP POST.
    2. O novo HTTP POST URL deve ser a mesma que a URL de POSTAGEM inicial.
    3. Nova POSTAGEM HTTP deve incluir cabeçalhos fluxo (caixa 'moov', 'ftyp' e "Live manifesto caixa servidor") idênticos aos cabeçalhos de fluxo em da POSTAGEM inicial.
    4. Os dois últimos fragmentos enviados para cada faixa devem ser enviada novamente e streaming retomado sem introduzir uma descontinuidade na linha do tempo de mídia.  Os carimbos de hora do fragmento MP4 devem aumentar continuamente, mesmo em solicitações HTTP POST.
6. O codificador deve terminar a solicitação HTTP POST se dados não está sendo enviados a uma taxa proporcional com a duração de fragmento MP4.  Uma solicitação de HTTP POST que não envia dados pode impedir que os serviços de mídia do Azure rapidamente desconectar do codificador em caso de uma atualização do serviço.  Por esse motivo, a POSTAGEM HTTP para esparso tracks (sinal de ad) devem ser curtos vivia, encerrando assim que o fragmento esparso é enviado.

##<a name="8-encoder-failover"></a>8. Failover de codificador

Failover de codificador é o segundo tipo de cenário de failover que precisa ser solucionado para entrega de streaming ao vivo de ponta a ponta. Neste cenário, a condição de erro aconteceu no lado codificador. 

![image5][image5]


Estas são as expectativas do ponto de extremidade inclusão ao vivo quando acontece codificador failover:

1. Uma nova instância de codificador deve ser criada para continuar streaming, conforme ilustrado no diagrama acima (fluxo para 3000 k vídeo com linha tracejada).
2. O novo codificador deve usar a mesma URL para solicitações HTTP POST como a instância falhou.
3. Solicitação POST do codificador novo deve incluir as caixas de cabeçalho de MP4 fragmentadas mesmas como a instância falhou.
4. O codificador novo deve estar sincronizado corretamente com todos os outros codificadores em execução para a mesma apresentação ao vivo para gerar amostras de áudio/vídeo sincronizadas com os limites de fragmento alinhado.
5. O novo fluxo deve ser semanticamente equivalente com o fluxo anterior e intercambiáveis no nível de cabeçalho e o fragmento.
6. O codificador novo deve tentar minimizar a perda de dados.  O fragment_absolute_time e fragment_index de mídia fragmentos devem aumentar do ponto onde o codificador última interrompido.  A fragment_absolute_time e fragment_index devem aumentar de forma contínua, mas é permitido para apresentar uma descontinuidade, se necessário.  Serviços de mídia do Azure ignorará fragmentos que ele já recebida e processada, portanto, é melhor para erro na lateral reenviar fragmentos que ao introduzir descontinuidades na linha do tempo de mídia. 

##<a name="9-encoder-redundancy"></a>9. redundância de codificador 

Crítico para determinados eventos ao vivo que disponibilidade ainda maior demanda e qualidade da experiência, recomenda-se para adotar ativa codificadores redundantes conseguir failover perfeito sem perda de dados.

![image6][image6]

Conforme ilustrado no diagrama acima, há dois grupo de codificadores insistem duas cópias de cada fluxo simultaneamente no serviço ao vivo. Esta configuração é suportada porque serviços de mídia do Microsoft Azure tem a capacidade de filtrar fragmentos duplicados com base em fluxo ID e fragmento carimbo de hora. O fluxo ao vivo resultante e arquivo morto será uma única cópia de todos os fluxos que é a melhor agregação possível de duas fontes. Por exemplo, em um caso extremo hipotético, contanto que não há um codificador (não precisa ser a mesma) executando em qualquer ponto no tempo para cada fluxo, o fluxo ao vivo resultante do serviço será contínuo sem perda de dados. 

O requisito para esse cenário é quase a mesma que os requisitos em caso de codificador Failover com exceção de que o segundo conjunto de codificadores estiver executando ao mesmo tempo como os codificadores principais.

##<a name="10-service-redundancy"></a>10. redundância de serviço  

Distribuição global altamente redundantes, às vezes, é necessário fazer backup de região de cruz para lidar com desastres regionais. Clientes expandindo a topologia de "Codificador redundância", podem optar por ter uma implantação service redundantes em uma região diferente que está conectada com o conjunto 2a de codificadores. Os clientes também podem trabalhar com um provedor CDN para implantar um mercado (Gerenciador de tráfego Global) na frente as implantações de dois service perfeitamente rotear o tráfego de cliente. Os requisitos para os codificadores são iguais a ocorrência de "Codificador redundância" com a exceção apenas que o segundo conjunto de codificadores precisam ser apontada uma diferente ao vivo inclusão ponto final. O diagrama a seguir mostra essa configuração:

![image7][image7]

##<a name="11-special-types-of-ingestion-formats"></a>11. tipos de formatos de inclusão especiais 

Esta seção descreve alguns tipo especial de formatos de inclusão ao vivo que foram projetados para lidar com alguns cenários específicos.

###<a name="sparse-track"></a>Controlar esparso

Quando uma apresentação ao vivo streaming com experiência de cliente avançado, ele geralmente é necessário para transmitir eventos sincronizadas ou sinaliza em banda com os dados de mídia principal. Um exemplo disto é dinâmica inserção de anúncios ao vivo. Esse tipo de sinalização de evento é diferente do áudio/vídeo regular streaming devido a sua natureza esparsa. Em outras palavras, os dados de sinalização geralmente não ocorrer continuamente e o intervalo pode ser difícil prever. O conceito de controlar esparso foi projetado especificamente para criar e transmitir dados sinalização em banda.

A seguir é uma implementação recomendada para controlar esparso a inclusão:

1. Crie um separada fragmentado MP4 bit-fluxo que contém apenas esparso track(s) sem faixas de áudio/vídeo.
2. Na "Live manifesto caixa servidor" conforme definido na seção 6 em [1], use o parâmetro de "parentTrackName" para especificar o nome da faixa pai. Consulte a seção 4.2.1.2.1.2 em [1] para obter mais detalhes.
3. Na "Live manifesto caixa servidor", manifestOutput deve ser definida como "true".
4. Devido à natureza esparsa do evento sinalização, é recomendável que:
    1. No início do evento ao vivo, o codificador envia as caixas de cabeçalho inicial para o serviço que permite que o serviço registrar o controle esparso no manifesto do cliente.
    2. O codificador deve terminar a solicitação de HTTP POST quando dados não está sendo enviados.  Uma POSTAGEM HTTP longa duração que não enviar dados pode impedir que os serviços de mídia do Azure rapidamente desconectar do codificador em caso de uma reinicialização de atualização ou servidor de serviço, como o servidor de mídia será bloqueado temporariamente em uma operação de recebimento no soquete. 
    3. Durante o tempo quando dados sinalização não estiverem disponíveis, o codificador deveria fechar a POSTAGEM HTTP solicitar.  Enquanto a solicitação POST estiver ativa, o codificador deve enviar dados 
    4. Ao enviar fragmentos esparsos, codificador pode definir explícito cabeçalho Content-Length se ele estiver disponível.
    5. Ao enviar fragmento esparso com uma nova conexão, codificador deve começar a enviar de caixas de cabeçalho da seguido os fragmentos de novo. Isso é para lidar com casos em failover aconteceu entre e a nova conexão esparso está sendo estabelecida para um novo servidor que não viu a faixa esparsa antes.
    6. O fragmento de controlar esparso estarão disponível para o cliente quando o pai correspondente controlar fragmento que tem igual ou maior valor de carimbo de hora é disponibilizada para o cliente. Por exemplo, se o fragmento esparso tem um carimbo de hora de t = 1000, ele é esperado após o cliente vê carimbo de hora 1000 de fragmento de vídeo (presumindo que o nome do controle pai é vídeo) ou além, ele poderá baixar o fragmento esparso t = 1000. Observe que o sinal real poderia ser muito bem usado para outra posição na linha do tempo de apresentação para sua finalidade designada. No exemplo acima, é possível que o fragmento esparso de t = 1000 tem uma carga XML que é para inserir um anúncio em uma posição que está alguns segundos mais tarde.
    7. A carga de fragmento de controlar esparso pode estar em vários formatos diferentes (por exemplo, XML ou texto ou binário, etc.) dependendo cenários diferentes. 


###<a name="redundant-audio-track"></a>Faixa de áudio redundante

Em um típico Streaming adaptativa HTTP cenário (por exemplo, Streaming suave ou traço), geralmente há apenas uma faixa de áudio de toda a apresentação. Ao contrário de faixas de vídeo que têm vários níveis de qualidade para o cliente à sua escolha em condições de erro, a faixa de áudio pode ser um único ponto de falha se a inclusão do fluxo que contém a faixa de áudio foi desfeito. 

Para resolver esse problema, serviços de mídia do Microsoft Azure dá suporte à inclusão ao vivo redundantes de faixas de áudio. A ideia é que a mesma faixa de áudio pode ser enviada várias vezes em fluxos de diferentes. Embora o serviço apenas registrará a faixa de áudio uma vez no manifesto do cliente, é capaz de usar faixas de áudio redundantes como backups para recuperar áudio fragmentos se a faixa de áudio principal está com problemas. Para inclusão redundantes faixas de áudio, o codificador precisa:

1. Crie a faixa de áudio mesma em vários fragmento MP4-fluxos de bit. As faixas de áudio redundantes devem ser semanticamente equivalente com exatamente os mesmo fragmento carimbos de hora e intercambiáveis no nível de cabeçalho e o fragmento.
2. Certifique-se de que o "áudio" entrada no Live Server manifesto do (seção 6 em [1]) ser o mesmo para todas as faixas de áudio redundantes.

Abaixo está uma implementação recomendada para redundantes faixas de áudio:

1. Envie cada faixa de áudio exclusiva em um fluxo por si só.  Também enviar um fluxo redundante para cada um desses fluxos de faixa de áudio, onde o fluxo de 2a difere do 1o apenas pelo identificador na URL HTTP POST: {protocolo} :// {endereço do servidor} / {path}/Streams({identifier}) de ponto de publicação.
2. Use fluxos separados para enviar os dois bitrates de vídeo mais baixos. Cada um desses fluxos também deve conter uma cópia de cada faixa de áudio exclusiva.  Por exemplo, quando vários idiomas forem compatíveis, desses fluxos devem conter faixas de áudio para cada idioma.
3. Use instâncias de servidor separado (codificador) para codificar e enviar os fluxos redundantes mencionados (1) e (2). 



##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png

 