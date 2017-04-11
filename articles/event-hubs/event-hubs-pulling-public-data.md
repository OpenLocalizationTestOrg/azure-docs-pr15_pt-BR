<properties
    pageTitle="Extraindo dados públicos em Hubs de evento do Azure | Microsoft Azure"
    description="Visão geral dos Hubs evento importar de exemplo da web"
    services="event-hubs"
    documentationCenter="na"
    authors="spyrossak"
    manager="timlt"
    editor=""/>

<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/25/2016"
    ms.author="spyros;sethm" />

# <a name="pulling-public-data-into-azure-event-hubs"></a>Extraindo dados públicos em Hubs de evento do Azure

Em cenários típicos de Internet das coisas (IoT), você tem dispositivos que você pode programar a dados de envio para Azure, um Hub de evento do Azure ou um hub IoT. Ambas essas hubs são pontos de entrada no Azure para armazenar, analisar e visualizando com uma infinidade de ferramentas disponibilizado no Microsoft Azure. No entanto, ambas exigem que você enviar dados para eles, formatado como JSON e protegidas de formas específicas. Isto traz a pergunta a seguir. O que fazer se você quiser trazer dados de fontes públicas ou privadas onde os dados são expostos como um serviço web ou um feed de algum tipo, mas você não tem a capacidade de alterar como os dados são publicados? Considere o clima ou tráfego ou cotações - você não pode dizer ao NOAA, ou WSDOT ou NASDAQ para configurar um envio para seu Hub de evento. Para resolver esse problema, nós gravados e código aberto uma amostra de nuvem pequenas que você pode modificar e implantar que irá extrair os dados de tal origem e enviados por push para o seu Hub de evento. A partir daí, você pode fazer o que quiser com ele, assunto, é claro, com os termos de licença do produtor. Você pode encontrar o aplicativo [aqui](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/).

Observe que o código neste exemplo só mostra como retirar dados de web típico feeds e como escrever a um Hub de evento do Azure. Isso não tem a intenção de ser um aplicativo de produção e nenhum tentativas foram feitas para torná-lo adequado para uso em tal ambiente - é strictfly uma DIY, voltada para desenvolvedores exemplo apenas. Além disso, a existência deste exemplo não é um uma recomendação que você deve **extrair** dados no Azure em vez de **envio** . Você deve examinar a segurança, desempenho e funcionalidade e fatores de custos antes de liquidar uma arquitetura de ponta a ponta.

## <a name="application-structure"></a>Estrutura do aplicativo

O aplicativo é escrito em c# e a [Descrição de exemplo](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) contém todas as informações que você precisa modificar, criar e publicar o aplicativo. As seções a seguir fornecem uma visão geral do que o aplicativo faz.

Vamos começar com a suposição de que você tenha acesso a um feed de dados. Por exemplo, talvez você queira busca os dados de tráfego do departamento de estado de Washington de transporte ou os dados meteorológicos do NOAA, para exibir relatórios personalizados ou para combinar dados com outros dados em seu aplicativo. Você também precisará de configurou um Hub de evento do Azure e souber a conexão sequência necessária para acessá-lo.

Quando a solução de GenericWebToEH é iniciado, ele lê um arquivo de configuração (App) para obter um número de itens:

1. A URL ou uma lista de URLs, para o site publicando os dados. Ideal seria este é um site que publica dados em JSON, como aqueles referenciada por WSDOT [aqui](http://www.wsdot.wa.gov/Traffic/api/). 
2. Credenciais para a URL, se necessário. Muitas fontes públicas não precisa de credenciais, ou você pode colocar as credenciais na sequência de URL. Outras exigem que você forneça separadamente. (Observe que você só pode especificar um conjunto de credenciais neste aplicativo, só funcionará se você especificar somente uma URL, não uma lista de URLs).
3. A cadeia de conexão e o nome do Hub evento no namespace Hubs de evento, ao qual você fará com que os dados. Você pode encontrar essas informações no portal do Azure.
4. Um suspensão intervalo, em milissegundos, para o intervalo entre o site de dados públicos de sondagem. Essa configuração requer alguns pensamento. Se você pesquisar muito raramente, poderá perder dados; Por outro lado, se você pesquisar com muita frequência, você pode receber muitos dados repetitivos ou pior ainda, você pode estar bloqueado como um bot mal-intencionado. Considere a frequência a fonte de dados é atualizada - clima ou o tráfego de dados podem ser atualizados a cada 15 minutos, mas talvez cada alguns segundos, dependendo de onde obtê-los cotações de ações. 
5. Um sinalizador para informar o aplicativo se os dados está chegando como JSON ou XML. Como você precisa enviar os dados para um Hub de evento, o aplicativo tem um módulo converter XML em JSON antes de enviar.

Após ler o arquivo de configuração, o aplicativo entra em um loop - acessando o site público, convertendo os dados, se necessário, escrevê-lo para seu Hub de evento e, em seguida, aguardando para o intervalo de suspensão antes de executá-la novamente. Especificamente:

  * Lendo o site público. Para receber dados de pronto para enviar a instância da classe RawXMLWithHeaderToJsonReader é usada do Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs. Lê fluxo de origem no método GetData e, em seguida, divide em partes menores (isto é, registros) usando GetXmlFromOriginalText. 
  Este método lerá XML bem como bem formado JSON ou JSON de matriz. Depois de processamento é iniciado usando a configuração de MergeToXML da App (padrão = vazio).
  * Os dados de envio e recebimento são implementados como um loop no método Process() na Program.cs. 
  Depois de receber os resultados de saída do GetData, coloca o método valores separados por no hub do evento.

## <a name="next-steps"></a>Próximas etapas

Para implantar a solução, clonar ou baixar o aplicativo de [GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) , editar o arquivo App, criar e finalmente publicá-lo. Depois de publicar o aplicativo, você pode vê-lo em execução no portal de clássico do Azure em serviços de nuvem e você poderá alterar algumas das configurações (como o destino do Hub de evento e o intervalo de suspensão) na guia **Configurar** .

Veja mais exemplos de evento Hubs na [Galeria de exemplos Azure](https://azure.microsoft.com/documentation/samples/?service=event-hubs) e no [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5).
