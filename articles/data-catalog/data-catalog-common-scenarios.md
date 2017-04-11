<properties
   pageTitle="Cenários comuns de catálogo de dados Azure | Microsoft Azure"
   description="Uma visão geral dos cenários comuns para Azure catálogo de dados, incluindo o registro e descoberta de fontes de dados de alto valor, habilitando intelligence de autoatendimento de business e capturar conhecimento tribal existente sobre fontes de dados e processos."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/03/2016"
   ms.author="maroche"/>


# <a name="azure-data-catalog-common-scenarios"></a>Cenários comuns do Azure catálogo de dados

Este artigo apresenta cenários comuns onde o catálogo de dados do Azure pode ajudar as organizações a obter mais valor de suas fontes de dados existentes.

## <a name="scenario-1---registration-of-central-data-sources"></a>Cenário 1 # - registro de fontes de dados central

Organizações geralmente têm um número de fontes de dados de alto valor. Essas fontes de dados incluem linha de sistemas OLTP de negócios, depósitos de dados e business intelligence / bancos de dados de análise. Muitas vezes aumenta o número de sistemas e a sobreposição entre sistemas, ao longo do tempo conforme as necessidades dos negócios evoluem e como os negócios em si evolui por meio de fusões e aquisições.

Muitas vezes é difícil para os usuários saber onde localizar os dados dentro essas fontes de dados. Perguntas como essas são todas muito comuns:

- Os sistemas de três HR usada dentro da empresa, que devo usar para criar esse tipo de relatório?
- Onde devem ir para obter os números de vendas certificados para o ano fiscal que acabou de encerrar?
- Quem eu pedir ou qual é o processo que eu devo usar para obter acesso ao data warehouse?
- Não sei se esses números são direita – quem pode posso pedir ideias em como esses dados deve ser usado antes para compartilhar esse painel com minha equipe?

Neste cenário, pode ajudar a catálogo de dados do Azure. As fontes de dados central, alto valor gerenciado por TI que são usadas em toda a organização são geralmente o ponto de partida lógico para preencher o catálogo. Embora qualquer usuário pode registrar uma fonte de dados, ter o catálogo kick-started com as fontes de dados que provavelmente fornecer valor para o maior número de usuários ajudará conduzir a adoção e o uso do sistema. Para clientes do guia de Introdução ao catálogo de dados do Azure, identificando e registrar as fontes de dados de chave usadas pelo muitas equipes diferentes de consumidores de dados podem ser a primeira etapa para o sucesso.

Este cenário também apresenta uma oportunidade para anotar as fontes de dados de alto valor para torná-los mais fáceis de entender e acessar. Um aspecto-chave desse esforço é incluir informações sobre como os usuários podem solicitar acesso à fonte de dados. Catálogo de dados do Azure permite aos usuários fornecer o endereço de email do usuário ou da equipe responsável para controlar o acesso de fonte de dados, links para ferramentas existentes ou documentação ou texto livre que descreve o processo de solicitação de acesso. Com essas informações incluídas no catálogo, os usuários que descobrir fontes de dados registrados, mas que ainda não tem permissões para acessar os dados facilmente podem solicitar acesso usando os processos definidos e controlado pelos proprietários de fonte de dados.

## <a name="scenario-2---self-service-business-intelligence"></a>Cenário #2 - intelligence de autoatendimento de business

Embora as soluções de inteligência de negócios corporativos tradicional continuem a ser uma parte muito útil dos cenários de dados de muitas organizações, a alteração ritmo dos negócios fez BI de autoatendimento mais importante. BI de autoatendimento permite que os operadores de informações e analistas para criar seus próprios relatórios, pastas de trabalho e painéis sem depender de uma equipe de TI central – ou sendo restrito a agenda e disponibilidade dessa equipe de TI.

Em cenários de BI de autoatendimento, é comum para os usuários combinar dados de várias fontes, muitos dos quais talvez não anteriormente foram usados para análise e BI. Embora algumas dessas fontes de dados já deve ser conhecidas, frequentemente há um processo de descobrir o que deve ocorrer para localizar e avaliar possíveis fontes de dados para uma determinada tarefa.

Normalmente, esse processo de detecção é um manual: analistas usará suas conexões de rede de ponto para identificar outras pessoas que trabalham com os dados que está sendo procurados. Quando uma fonte de dados está localizada pode ser usado, mas o processo se repete novamente para cada subsequentes autoatendimento BI esforço, com vários usuários executando o mesmo processo manual redundante de descoberta.

Com o catálogo de dados do Azure, os usuários podem interromper esse ciclo de esforço redundante. Depois que uma fonte de dados tenha sido descoberta através de meios tradicionais, um analista pode registrar a fonte de dados para torná-la mais facilmente descobertos no futuro. Embora o usuário pode adicionar mais valor anotando os ativos de dados registrados, isso não precisa ser feito ao mesmo tempo como registro. Os usuários podem contribuir ao longo do tempo, como permitir suas agendas, gradualmente adicionando valor para as fontes de dados registradas no catálogo.

Esse crescimento orgânico do conteúdo do catálogo é um complemento natural ao registro prévio de fontes de dados central. Preencher previamente o catálogo com os dados que muitos usuários precisarão pode ser um motivador para uso inicial e descoberta. Habilitando usuários para registrar e anotar fontes adicionais pode ser uma maneira de mantê-los e seus colegas, envolvidas.

Também vale a pena observar que embora este cenário enfoque especificamente BI de autoatendimento, o mesmo padrões e desafios aplicam a grande escala corporativos projetos de BI também. Qualquer esforço que envolve um processo manual de descoberta de fonte de dados é uma iniciativa que pode adicionar valor à organização por meio do uso catálogo de dados do Azure.

## <a name="scenario-3---capturing-tribal-knowledge"></a>Cenário #3 - captura conhecimento tribal

Como posso saber quais dados que você precisa fazer seu trabalho e onde encontrar esses dados?

Se você já estiver no seu trabalho por um tempo, você provavelmente só sabe. Você esteve por meio de um processo de aprendizado de gradual e ao longo do tempo aprendeu sobre fontes de dados que são fundamentais para o seu trabalho do dia a dia.

Quando um novo funcionário ingressa sua equipe, como ele reconhece os dados que ele precisa fazer seu trabalho e onde encontrá-los?

Chances são, ele solicita que você.

Essa transferência contínua de conhecimento tribal é parte do processo de descoberta de fonte de dados em organizações grandes e pequenos. Membros da equipe mais experientes e sênior criaram o conhecimento passar dos anos, e os membros da equipe mais recentes aprendeu a peça quando tiverem dúvidas. As informações mais importantes com frequência existem somente no chefe de algumas pessoas importantes, e quando as pessoas estiverem de férias ou deixe a equipe, a organização é afetada.

Às vezes, esses especialistas de dados tornará o esforço para documentar seus conhecimentos, compartilhá-lo por email ou em documentos do Word em um site de equipe do SharePoint. Embora isso pode ser importante, que ele apresenta um novo problema de descoberta – como pessoas saber quais documentação existe e onde encontrá-los...

Catálogo de dados do Azure fornece um local para compartilhar esse conhecimento tribal e tornando mais facilmente descobertos. Especialistas em dados diretamente podem anotar ativos de dados e também podem incluir links para documentação existente. Não apenas isso captura o conhecimento em si, mas ele também coloca o conhecimento na mesma experiência que é usada para descoberta de fonte de dados. Quando alguém usa o catálogo de descobrir um fonte de dados não apenas ele localize a fonte em si, ele também encontrará conhecimento do especialista previamente existentes apenas em mente do especialista em si.
