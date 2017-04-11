<properties
   pageTitle="Extraindo dados do SQL em Hubs de evento do Azure | Microsoft Azure"
   description="Visão geral dos Hubs evento importar de amostra SQL"
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

# <a name="pulling-data-from-sql-into-an-azure-event-hub"></a>Extraindo dados do SQL a um Hub de evento do Azure

Uma arquitetura típica para um aplicativo para o processamento de dados em tempo real envolve primeiro estendendo-a um Hub de evento do Azure. Talvez seja um cenário de IoT, como monitorar o tráfego na expande diferente de uma estrada, ou um cenário de jogos, monitorar as ações de uma Horda de frenesi causados participantes ou um cenário de empresa, como o monitoramento ocupação de construção. Nesses casos, produtores dados são geralmente externos objetos produzir dados de série de tempo que você precisa coletar, analisar, armazenar e act após e você pode têm investido muito esforço em desenvolvimento a infraestrutura para esses processos. O que você faça se desejar trazer dados de algo como um banco de dados em vez de uma fonte de dados de fluxo e usá-lo em conjunto com seus dados de streaming? Considere o caso onde você deseja usar a análise de fluxo Azure, Explorador de dados remoto (RDX) ou alguma outra ferramenta para analisar e atuar em um sistema de gerenciamento de instalações personalizadas ou lentamente alterando dados do Microsoft Dynamics AX? Para resolver esse problema, nós gravados e código aberto um exemplo de nuvem pequenas que você pode modificar e implantar que irá extrair os dados de uma tabela do SQL e push-la a um Hub de evento do Azure para usar como entrada em seus aplicativos analíticos downstream. Perceba que este é um cenário de raro e o oposto do que você normalmente faria com um Hub de evento. No entanto, se você estiver na situação onde isso é o que você precisa fazer, você pode encontrar o código na Galeria de exemplos do Azure, [aqui](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-import-from-sql/).  

Observe que o código neste exemplo é exatamente isso, uma amostra. Ele é **não** se destina a ser um aplicativo de produção e nenhum tentativas foram feitas para torná-lo adequado para uso em tal ambiente-stricly um DIY, voltada para desenvolvedores, exemplo. Você precisa revisar todos os tipos de segurança, desempenho e funcionalidade e fatores de custos antes de liquidar uma arquitetura de ponta a ponta.

## <a name="application-structure"></a>Estrutura do aplicativo

O aplicativo é escrito em c# e o arquivo readme.md na amostra contém todas as informações que você precisa modificar, criar e publicar o aplicativo. As seções a seguir fornecem uma visão de alto nível do que o aplicativo faz.

Vamos começar com a suposição de que você tenha acesso a uma tabela do SQL Azure. Você também precisará de configurou um Hub de evento do Azure e souber a conexão sequência necessária para acessá-lo.

Quando a solução de SqlToEventHub é iniciado, ele lê um arquivo de configuração (App) para obter um número de itens, conforme descrito no arquivo readme.md. Estas são bastante auto-explicativos, como o nome de tabela de dados, etc, e não é necessário para rehash as explicações aqui. 

Depois que o aplicativo tem ler o arquivo de configuração, ele vai em um loop, lendo a tabela SQL insistem registros no hub do evento e aguardando por um intervalo de suspensão definidos pelo usuário antes de executá-la novamente. Algumas coisas são vale a pena observar:

1. O aplicativo baseia-se a suposição de que a tabela SQL está sendo atualizada por algum processo externo, e você quiser enviar todos e somente as atualizações a um Hub de evento.
2. A tabela SQL precisa ter um campo que tem um número exclusivo e crescente, por exemplo, um número de registro. Isso pode ser tão simple quanto um campo chamado "Id" ou qualquer outra coisa que é incrementado como tudo o que atualiza o banco de dados adiciona registros como "Creation_time" ou "Sequence_number". O aplicativo de anotações e armazene o valor do campo em cada iteração. Em cada subsequente passagem pelo loop, o aplicativo essencialmente consulta a tabela para todos os registros onde o valor do campo excede o valor ele viu a última vez pelo loop. Estamos ligando este último valor o deslocamento de"".
3. O aplicativo cria uma tabela "TableOffsets" quando ele for iniciado, para armazenar as compensações. A tabela é criada com a consulta "CreateOffsetTableQuery" definida no arquivo de configuração. 
4. Há várias consultas usadas para trabalhar com a tabela de deslocamento, definida no arquivo de configuração como "OffsetQuery", "UpdateOffsetQuery" e "InsertOffsetQuery". Você não deve alterar essas.
5. Por fim, a consulta "Consulta de dados" definida no arquivo de configuração é a consulta que será executada para separar os registros da sua tabela do SQL. Ele está atualmente limitado a registros de início 1.000 no cada passagem pelo loop para fins de otimização - se, por exemplo, 25.000 registros foram adicionados ao banco de dados desde a última consulta, ele pode demorar um pouco para executar a consulta. Limitando a consulta aos 1.000 registros cada vez, as consultas são muito mais rápidas. Selecionando a parte superior 1.000 simples envia sucessivas lotes de 1.000 registros para o hub de evento.    

## <a name="next-steps"></a>Próximas etapas

Para implantar a solução, clonar ou baixar o aplicativo de SqlToEventHub, editar o arquivo App, criar e finalmente publicá-lo. Depois de publicar o aplicativo, você pode vê-la em execução no portal de clássico do Azure em serviços de nuvem e monitorar os eventos chegando para o seu hub de evento. Observe que a frequência será determinada por dois fatores: a frequência das atualizações para a tabela do SQL e do intervalo de suspensão que você especificou no arquivo de configuração para o aplicativo.