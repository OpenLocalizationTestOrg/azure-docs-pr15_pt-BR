<properties 
    pageTitle="Contrato de móvel Azure guia de Introdução com as práticas recomendadas"
    description="Obter o guia de Introdução do Azure Mobile contrato e práticas recomendadas para integração" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="wesmc7777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="10/04/2016"
    ms.author="wesmc;ricksal"/>

# <a name="azure-mobile-engagement---getting-started-guide-with-best-practices"></a>Contrato de móvel Azure - guia de Introdução com melhores práticas

## <a name="overview"></a>Visão geral

**Tela móvel é um espaço muito cheio:** No 2013, um estudo encontrou que o dispositivo móvel médio tinha 27 aplicativos instalados. Os usuários normalmente gasto 30 horas por mês em seus aplicativos. Na maioria desta vez gasto na rede social e jogos (cerca de 20 horas). 2014, o Android market tinha cerca de 1,5 milhões de aplicativos para os usuários à sua escolha. Apple store contido em torno de 1,2 milhão aplicativos. Uso do aplicativo móvel ainda está aumentando à medida que os desenvolvedores competem neste mercado de crescimento. 

O usuário móvel médio irá instalar e desinstalar aplicativos com alta frequência dependendo alterando interesses e experiências do aplicativo. Para determinar o êxito de um aplicativo se torna vital para saber mais do que apenas quantos usuários instalar o aplicativo. É importante saber como é útil o aplicativo está e se essa tendência de uso está alterando. Importância as seguintes perguntas:

- Os usuários são início para localizar seu aplicativo desperdiçadas ou obsoleto? 
- Quantos usuários foram interrompidas usando seu aplicativo em todos os? 
- Compras do aplicativo são tendências para cima ou para baixo?
- Os usuários estão falhando concluir fluxos de trabalho devido a problemas com o aplicativo ou falta de interesse? 
- Pode manter seu aplicativo útil e relevantes, pressionando conteúdo novo à sua base de usuário? 
- Seria esse conteúdo novo a ser o mesmo para todos os usuários ou voltada para segmentos de usuários com base em comportamento em seu aplicativo? 
 
Respostas a perguntas semelhantes a estas podem ajudar a estender a vida e receita de seu aplicativo. Eles também podem ajudar você definir e manter sua base de usuários. 

Mídia relacionada aplicativos tendem a ter alguns da retenção maior entre os usuários. Um motivo para isso é constantemente estiverem fornecendo conteúdo novo aos usuários. Adoção antecipada útil de notificações de envio direcionado para um segmento do usuário tende a ter um alto impacto em retenção de aplicativo. 

O programa de contrato do Azure Mobile foi projetado para ajudá-lo a estender a vida e retenção de seu aplicativo, fornecendo um método para coletar e analisar informações detalhadas sobre o uso do aplicativo. Ele ajudará a classificar sua base de acordo com o comportamento de usuários e criar campanhas com foco para oferecer notificações por push e mensagens do aplicativo segmentos de usuários identificados. Indicadores chave de desempenho (KPIS) medem ativa como seus usuários estão com diferentes aspectos de seu aplicativo. Azure contrato Mobile fornece os métodos que você precisa determinar esses KPIs. Ele ajuda a aumentar o retorno sobre o investimento (ROI), fornecendo a infraestrutura que você precisa aumentar o envolvimento com seu aplicativo móvel. 

Para obter o máximo proveito do Azure Mobile contrato, você precisa iniciar com um plano de contrato bem projetada. Seu plano ajudará você a identificar os dados granulares, que você precisará ser capaz de segmento sua base de usuários. Isso pode ser baseado em comportamento e experiências do aplicativo. Em ordem para seu plano ter êxito, é uma prática recomendada definir claramente o KPI que será medir os objetivos do aplicativo. Com indicadores de desempenho limpar definidos, você pode inserir facilmente a lógica necessária em seu aplicativo para coletar dados fino refinados que você usará para analisar e avaliar KPIs. Este tópico é um guia de práticas recomendadas para definir os KPIs que você usará com seu plano de projeto. 


## <a name="step-1-define-your-kpis-to-fit-the-bet-model"></a>Etapa 1: Definir KPIs para ajustar o modelo de opção


Definir corretamente KPIs pode ser uma tarefa difícil para ser concluída. Aplicativos projetados para diferentes setores têm suas próprias especificações e objetivos. Isso pode tendem a confundir sua abordagem. Para ajudar a evitar isso, KPIs e objetivos deverão ser classificados em três categorias principais: **negócios**, **contrato**e **técnico**. Este é o que chamamos o **modelo de opção**.

Um bom plano geralmente terão objetivos com os KPIs que medem o sucesso em cada uma das categorias de modelo de opção a seguir.


#### <a name="business-kpis"></a>KPIs de negócios

KPIs de negócios devem ser a parte mais fácil de criar. Você provavelmente já definidos em alguma forma quando você planejar seu aplicativo móvel. Esses KPIs ajudam geralmente medida receita e ROI para você aplicativo. A lista a seguir fornece alguns KPIs de empresas que podem ajudá-lo ao definir seus indicadores de desempenho de exemplo:

- KPIs de negócios de mídia
    - Número de anúncios clicado
    - Número de página visitas por usuário
    - Número de inscrições atuais
- KPIs de negócios de jogos
    - Número de compras do aplicativo
    - Receita média por usuário (ARPU)
    - Tempo gasto por sessão
    - Dias reproduzidos e atuais no nível de jogo
- KPIs de negócios de comércio
    - Aplicativo de dias usado
    - Receita média por usuário (ARPU)
    - Valor médio no carrinho durante check-out
    - Categoria de produto para a maioria dos modos de exibição e compras
- KPIs de negócios bancários e seguro
    - Número de contas
    - Recursos ativados
    - Páginas de oferta visitadas
    - Alertas clicado ou ativadas      



#### <a name="engagement-kpis"></a>KPIs de contrato

Um KPI de contrato é um indicador de desempenho para medir o contrato de seus usuários. Tendências nesta área ajudam a determinar a retenção de seu aplicativo. Aqui estão alguns indicadores de desempenho de exemplo para este tipo de KPI:

- Usuários ativos nos últimos 7 dias
- Contagem de usuários inativos para os últimos 7 dias
- Contagem de usuários que não usou o aplicativo em 30 dias  

Alguns fatores externos óbvios podem influenciar indicadores nesta área. Por exemplo, você pode considerar um dispositivo móvel para ser com um usuário em todos os momentos. Isso pode ou não ser verdadeiro. Um aplicativo de jogos pode tendem a ter maior uso em torno de feriados quando um jogador pode reproduzir mais enquanto ausente do trabalho ou reduzir escola.   

Bem definidas KPIs nesta categoria deverão ajudá-lo a medir a relação entre seu aplicativo e seus clientes.



#### <a name="technical-kpis"></a>KPIs técnicos

Indicadores de desempenho nesta categoria o ajudará a determinar se seu aplicativo está funcionando corretamente, falhando ou travando. Esses indicadores podem medir o funcionamento do aplicativo e determinar as questões de usabilidade que podem impedir que os usuários usando o aplicativo. Informações coletadas para essa categoria também podem conter informações de desempenho que seriam relevantes para as equipes de marketing. Os dados também podem ser úteis para solução de problemas por IT e equipes para ajudar a identificar erros não relatados de suporte. 
 
Aqui estão alguns exemplos de KPIs técnicos:

- Contagem e informações de exceção não tratada ou manipulados 
- Carimbo de hora do último travamento
- Botão última clicado ou última página visitada
- Uso de memória do aplicativo
- Taxa de quadros de aplicativo
- Versão do sistema operacional que o aplicativo é executado em
- Versão do aplicativo

Defina esses KPIs para ajudar a medir o desempenho do aplicativo e identificar possíveis bugs. Este indicadores devem ajudar a reduzir o tempo que necessário para produzir uma correção para seus clientes. Eles também podem ajudar você definir um segmento do usuário que encontrou um problema específico. Você pode usar esse segmentação de usuário para criar campanhas para fornecer notificações sobre correções disponíveis e promoções possíveis para ajudar a recuperar a satisfação do cliente. 


#### <a name="playbook-exercise-1-create-your-kpi-dashboard"></a>Guia estratégico Exercício 1: Criar seu painel KPI

Ao definir sua estratégia de marketing, KPIs devem apresentar um modo de exibição para cada um dos seus objetivos principais. Eles devem ser pontos de dados claramente definidos que permitirá que você colete informações vitais para monitorar o seu aplicativo e o comportamento do usuário final.

Criar um painel KPI que contém as informações abaixo

1.  Quais são os KPIs para o aplicativo?
2.  Quais dados apontam vai usar para representar cada KPI?
3.  Onde os dados estão localizados para meu aplicativo (ou seja, tela, configurações, sistema …)?
4.  Pode reproduzir uma sequência de contrato para este KPI?

Você pode usar a planilha de **Construtor de KPI** em nosso [Modelo de manual de mídia] [ Media Playbook link] para exemplos e diretrizes.



## <a name="step-2-your-engagement-program"></a>Etapa 2: Seu programa de contrato


Um programa de ótimo contrato móvel deve ser considerado um componente chave do seu aplicativo. Isso absolutamente deve incluir um programa de boas-vindas excelente que é executado para um usuário durante os primeiros dias de uso de aplicativos. Isso tende a ter um efeito muito positivo no contrato e retenção de seu aplicativo. Estudos mostram que a maioria dos usuários parar de usar um aplicativo primeiros dias após a instalação. Você deseja esforce-se para atender ou exceder interesse determinante do cliente expectativas antecipado, enquanto o usuário ainda está focalizado no seu aplicativo. Certificar-se de que você apresenta o valor da chave e benefícios de seu aplicativo para seus clientes. 


![](./media/mobile-engagement-getting-started-best-practices/unsegmented-push-notifications.png)

Notificações por push são a melhor abordagem para contratos antecipados com usuários de dispositivo móvel. No entanto, excelente deve ter cuidado ao segmentação usuários para notificações por push. Porque depois que um usuário parece com eles estão recebendo spam ou desperdiçadas notificações, ele pode ter grave afeta. Em alguns cliques, um usuário pode excluir seu aplicativo nunca para retornar. O usuário deve estar recebendo altamente personalizado valor do aplicativo em vez de spam genérico.

Depois que os usuários estão envolvidos ativamente, seu programa de contrato pode ajudar unidade outros aspectos do aplicativo.

Por exemplo, você poderia configurar uma campanha que solicita seus usuários ativos para classificar o seu aplicativo. Como deste segmento do usuário é o mais ativo e tem a maioria dos experiência com você app, você esperava-los para dar a classificação mais precisa. Quando você tiver uma classificação de aplicativo alta, pode ajudar a aumentar o download orgânico de seu aplicativo também reduzindo os custos de aquisição de cliente novo.



#### <a name="engagement-sequence"></a>Sequência de contrato


Um programa de contrato global inclui sequências de contrato diferente. Cada sequência pretende atingir vários objetivos.


###### <a name="life-push-sequence"></a>Sequência de envio de vida


Os objetivos para uma sequência de envio de vida são diferentes dependendo do ciclo de vida do contrato do usuário com o aplicativo. Um usuário específico pode ser novos, muito ativo ou inativo. Em diferentes estágios de um ciclo de vida do contrato, os usuários podem se beneficiar seu conteúdo novo na forma de dicas ou links a documentação. 

Por exemplo um novo usuário pode precisa de ajuda obtendo orientada por um aplicativo ou beneficiar um novo incentivos de usuário semelhante à seguinte na primeira vez que iniciar o aplicativo...

*"Feliz tê-lo integrado! Lembre-se de fazer login para acessar seu mês 1º gratuito!"*


###### <a name="behavioral-push-sequence"></a>Sequência de comportamento de envio

A sequência de envio comportamento pretende aumentar uso com base em comportamento de usuário coletado para o aplicativo.  

Por exemplo, um usuário muito ativo de um aplicativo de futebol simulação pode se beneficiar engajamento com a seguinte notificação de envio...

*"John você é fã de futebol sério! Faça logon em nossa seção NFL e ganhar acesso gratuito para o SuperBowl!"*


###### <a name="alerting-push-sequence"></a>Sequência de envio de alerta

Os usuários se interessarão notícias relevantes estiveram centradas nos seus interesses. Uma sequência de alerta push aprimora contrato enviando alertas com base em interesses um usuário claramente ficou. Isso pode ser explícito quando um usuário seleciona seus próprios interesses no aplicativo. Ele também pôde ser determinado implicitamente com base em dados coletados durante a interação do usuário com o aplicativo.

Por exemplo, é possível que o usuário de um aplicativo de comércio regularmente comprar uma marca específica de café que capturadas com um KPI de negócios. O seguinte alerta pode aumentar o envolvimento desse usuário com o aplicativo.
 
*"Olá Wes, um dos seus favoritas marcas de café estará na venda 25% desativar a primeira semana do setembro de 2015. Agradecemos você como cliente e quisesse garantir que você estava ciente."*

###### <a name="rentention-push-sequence"></a>Sequência de envio de retenção

Esta sequência pretende reter usuários usando um campanhas de notificação de envio repetitivas para orientar o hábito normal de envolventes com o aplicativo. Isso pode ajudar a aumentar a retenção de aplicativo se o usuário gosta as interações. 

Por exemplo, o usuário de um aplicativo de esportes relacionados pode receber a seguinte notificação por push semanais com base em equipes favoritas do usuário:

*"Para a chance de ganhar 200 pontos, vá voto se Yankees Nova York win seu jogo esta semana contra Curitiba azul Jays!"*


#### <a name="the-3w-approach"></a>A abordagem 3W

Dominar as sequências de envio diferentes ajudará você a envolva com usuários finais. No entanto, você ainda precisa usar a abordagem 3W para personalizar suas notificações. A abordagem 3W deve resolver quem, o que é e quando para cada notificação. Se você atender a estas três perguntas claramente você notificações deve ser concentrado corretamente para contrato.

![](./media/mobile-engagement-getting-started-best-practices/who-what-when.png)



###### <a name="who-the-user-segment-that-will-receive-messages"></a>Quem: O usuário segmento que receberão mensagens

Insistem notificações aos seus usuários deve ser considerado um canal de comunicação muito confidenciais. Certificar-se de que as notificações de que objetivo para enviar a um segmento do usuário também têm como escopo os interesses daquele segmento do usuário. Uma notificação incorretamente roteada é muito provável ter um impacto negativo no usuário. Eles podem considerar-spam levam a seu aplicativo sendo desinstalado. 

Use uma combinação de critérios específicos de técnicas e comportamentos ao definir segmentos de usuários que receberão notificações. Um exemplo simples que define um segmento do usuário pode ser semelhante à instrução a seguir:

"Todos os usuários que iniciou o tempo 3 dias atrás de um aplicativo móvel para o primeiro e visitou a página de login duas vezes sem realmente concluir um logon".
 
Essa instrução ajuda a identificar os dados que você precisa coletar para oferecer suporte a um cenário específico.


###### <a name="what-the-message-that-you-will-send"></a>O que: A mensagem que você envia

**Tom**

No seu contratos use um tom apropriado para sua para seus usuários segmentados. Isso é definitivamente uma boa maneira de se conectar com os usuários finais e promover juros de um usuário em seu aplicativo. 

**Redirecionamento**

Uma notificação de envio pode ser usada para mais de abrir o aplicativo. Se a mensagem de notificação fornece um contexto como transmissão de notícias ou uma promoção de produto, essa notificação pode profunda link diretamente para o conteúdo certo dentro do aplicativo. Para oferecer suporte a isso, você deve criar um esquema de URL para permitir que o aplicativo gerencie o redirecionamento. Ao trabalhar em seus sequências de contrato, essa é uma etapa importante que não deve ser esquecida.

Redirecionamento também pode ser gerenciado para outros sistemas. Por exemplo, com uma URL de ação é possível redirecionar os usuários finais para muitos outros sistemas, incluindo o seguinte:

- Um site
- Uma caixa de correio com email já configurado
- Uma caixa SMS
- Um serviço de discagem
- Diretamente para o armazenamento de aplicativos para o aplicativo de classificação. 

Isso fornece muitas oportunidades de envolver os usuários finais e criar regras automáticas para melhorar o desempenho.


**Formatar para o conteúdo**

Diferentes tipos e formatos de notificação de envio:

1. **Anúncios** : permite que você envie mensagens de anúncios para usuários em diferentes momentos (sair do aplicativo, no aplicativo ou sempre).
2. **Votações** : habilitado para coletar informações dos usuários finais por perguntas-los. Essas respostas, em seguida, estão disponíveis ao criar critérios para os usuários finais de destino.
3. **Envia dados** : permite que você envie um arquivo de dados na Base 64 ou binário para atualizar o aplicativo. As informações contidas em um envio de dados são enviadas ao seu aplicativo para personalizar a experiência dos usuários em seu aplicativo. Seu aplicativo precisa ser projetado para oferecer suporte os dados em um envio de dados.
4. **Blocos (somente para o Windows Phone)** : habilitado para usar o serviço de notificação por Push do Microsoft (MPNS) para enviar notificação de envio por Push nativo que contém dados XML (com suporte desde SDK versão 0.9.0. A carga final para blocos não pode exceder 32 kilobytes.). A mensagem aparece diretamente no bloco da sua placa.
5. **Exibição da Web** : um modo de exibição de web é um conteúdo da web contendo pop-up. Este pop-up aparece quando o usuário final clicou a notificação de envio. Um modo de exibição da web permite que você tenha mais de interação com o usuário final.
 
>[AZURE.NOTE] Certifique-se de que o conteúdo que estiver enviando como notificações por push compatível com a plataforma respectiva (iOS, Android, Windows) diretrizes para desenvolvimento de aplicativos e enviar notificações por push.

 


###### <a name="when-the-timing-of-your-campaign"></a>Quando: O intervalo de sua campanha

Quando é o melhor horário para ativar uma campanha de disparo notificações por push? Ele deve ser manual ou automática? Ela deve ser recorrente? Determinar o momento certo ou a frequência é essencial para envolver usuários com os melhores resultados. Para cada sequência de contrato e o cenário, você deve especificar quando será o melhor horário para enviar notificações por push. Aqui estão alguns exemplos de possíveis:

![](./media/mobile-engagement-getting-started-best-practices/campaign-timing-examples.png)

Se você estiver enviando notificações muitos diariamente, você deve levar em consideração séria que seus usuários podem considerar suas comunicações como spam. 

Contrato de celular Azure oferece duas maneiras de ajudar a evitar as comunicações sendo consideradas como spam. Primeiro, use segmentação refinados para garantir que você não direcionar os mesmos usuários. Além disso, o contrato do Azure Mobile fornece um recurso de "cota". Este recurso pode limitar notificações enviadas para uma campanha. Por exemplo, definindo uma cota de padrão para 5 por semana garantirá que um usuário incluído como parte do segmento campanha usuário recebe notificações não mais do que 5 da semana.





#### <a name="playbook-exercise-2-create-your-engagement-program"></a>Guia estratégico Exercício 2: Criar seu programa de contrato

Dedicar algum tempo resumir seus objetivos e definindo as campanhas que você espera para ser reproduzido usando sequências específico. Verifique se que você aplicar a abordagem 3W notificações em suas campanhas. 

Usar a planilha de **Programa de contrato** no [Modelo de manual de mídia] [ Media Playbook link] para exemplos e diretrizes.


## <a name="step-3-app-integration"></a>Etapa 3: Integração de aplicativo


#### <a name="create-a-tag-plan"></a>Criar um plano de marca

Para integrar o contrato do Azure Mobile em seu aplicativo, você precisará criar um plano de marca. O plano de marca é a base do projeto. Define a relação entre Especificações de marketing, o fluxo de trabalho do aplicativo e os dados de marca real coletados no aplicativo para medir KPIs. Indica que a análise será capaz de ver no portal. Ele também ajuda a definir segmentos de usuários e enviar notificações de envio com foco contratar seus usuários finais. Uma vez você tiver o plano de marca definido, adicionando o código para integrá-lo em seu aplicativo é simples usando o SDK do Azure Mobile contrato.

Um plano de marca não deve marcar tudo em um aplicativo. Ele deve incluir somente os dados da etiqueta que faz parte de sua estratégia de contrato móvel. Isso provavelmente será diverso entre aplicativos. O [Modelo de manual de mídia] [ Media Playbook link] fornecido por Azure Mobile contrato ajuda você cria um plano de marca com um determinado método. Use a planilha do **Plano de marca** como um guia para a criação de seu plano de marca.

Ao definir uma seção de marca na planilha, seja bastante específico. Isso é muito importante para evitar confusão. Detalhes de cada cenário esperado no qual cada marca será enviado. Inclua o nome da atividade onde cada marca for inserida. Isso deve todos ser incluído na parte **Informative** da planilha. A planilha do plano de marca deve ser a referência principal para verificação de teste. 

Na seção de **dados para coletar** , sua equipe de desenvolvimento deve encontrar tipos, nomes, valores e pares de valor da chave extra-info necessários para cada marca que será incorporada no aplicativo.

É recomendável revisar o plano de marca com todas as equipes associadas ao projeto. Faça correções necessárias e confirme que tudo fica claro para as equipes de marketing e desenvolvimento.

A planilha de **declaração de trabalho** pode ser usada para ajudar a orientar que todos os envolvidos no projeto.


#### <a name="data-types"></a>Tipos de dados

Estas são tipos comuns de suporte a dados contrato de celular do Azure.

###### <a name="devices-and-users"></a>Dispositivos e usuários

Contrato de celular Azure identifica os usuários gerando um identificador exclusivo para cada dispositivo. Esse identificador é chamado de identificador de dispositivo (ou deviceid). Ele é gerado de forma que todos os aplicativos em execução no mesmo dispositivo compartilham o mesmo identificador de dispositivo.

###### <a name="sessions-and-activities"></a>Sessões e atividades

Uma sessão é uma instância do aplicativo está sendo executado por um usuário. A sessão abrange desde a hora em que o usuário iniciar o aplicativo, até parar.

Uma atividade é um agrupamento lógico de um conjunto de que o aplicativo pode fazer durante uma sessão. É geralmente uma determinada tela no aplicativo, mas pode ser que qualquer coisa definido pela lógica do aplicativo. No mínimo, você deve marca cada tela ou atividade para seu aplicativo. Isso permitirá que você a compreender o caminho do usuário.


###### <a name="events"></a>Eventos

Eventos são usados para relatar a interação do usuário com o aplicativo. Eles podem ser ações instantâneas, como o compartilhamento de conteúdo ou iniciar um vídeo. Eventos de marcação fornecerá conjuntos de dados que mostram como os usuários interagem com o aplicativo. 

###### <a name="jobs"></a>Trabalhos

Trabalhos são usados para relatar ações que têm uma duração. Alguns exemplos teria incluem:

- Execução de chamadas de API
- Tempo de exibição de anúncios
- Duração de tarefas do plano de fundo 
- Duração do processo de compra
- Exibindo um vídeo


###### <a name="errors"></a>Erros

Erros são usados para relatar problemas detectados pelo aplicativo. Por exemplo, ações do usuário incorretos ou falhas de chamada de API.

###### <a name="application-information"></a>Informações do aplicativo

Informações de aplicativo (App-Info) são usadas para marcar dados relacionados à experiência de um usuário com um aplicativo. Ele é gerado pela interação do usuário com o aplicativo. 

Para uma chave de determinado aplicativo-info, Azure Mobile contrato apenas controla os o valor mais recente (sem histórico). Informações de aplicativo revela o status de seu aplicativo ou seus usuários finais. Por exemplo o status de log ou grupo de favoritos do produto de um usuário.

###### <a name="crash-data"></a>Dados de falha

Falhe dados coletados automaticamente pelas falhas do aplicativo de relatórios Mobile contrato SDK não manipuladas pelo aplicativo. Por exemplo uma exceção não tratada que ocorre.


###### <a name="extra-data"></a>Dados extras

Eventos, erros, atividades e trabalhos podem ser aprimorados com parâmetros. Este é um desenvolvedor pode fornecer como dados específicos do aplicativo de informações de extra. Isso é importante para a definição de segmentação refinada. 

Por exemplo, o valor de uma marca de "artigo" permite aos usuários finais de segmento com base em quem visualizadas artigo específico. No entanto, que pode não ser suficiente. Talvez seja melhor se essa marca "artigo" mesmo também incluído extra-info como "news_category" dentro de uma atividade. Isso seria útil para determinar dinamicamente as categorias favoritas do usuário. 

Informações de extra é relatada como um par chave/valor. No exemplo deste aplicativo de mídia, as informações de extra para "news_category" seria o valor para essa categoria. Por exemplo, "esportes", "economia" ou "política".





#### <a name="tag-and-sdk-integration"></a>Integração de marca e SDK 

Para obter instruções passo a passo para integrar o SDK do contrato do Azure Mobile em seu aplicativo, siga a documentação de [Integração de SDK do contrato](mobile-engagement-windows-store-integrate-engagement.md) no site Azure. Escolha sua plataforma de destino os links na parte superior da página.

É recomendável criar projetos para os dois aplicativos criados na parte superior do Azure Mobile contrato. Um para desenvolvimento e preparação de teste e outra para preparação de produção. Sua equipe de TI pode promover depois de preparação de teste para produção quando o teste de aceitação de usuário for bem-sucedido.



#### <a name="user-acceptance-testing-uat"></a>Teste de aceitação do usuário

Usuário aceitação teste de envolve lembrando-se de que tudo funciona como projetado. Fluxos de trabalho podem ser concluídos e coletam dados necessários com base no seu plano de marca:
 
- Informações de marcação deve estar no lugar de acordo com documentadas conceitos AZME
- Todas as informações que necessárias são coletadas (incluindo o valor de informações de Extra, valor de informações de aplicativo)
- Correspondências de nomenclatura de acordo com o plano de marca de layout
- Não há nenhuma marca duplicada enviada

Teste completamente todos os tipos de comportamento de notificação que você tenha incorporado em seu aplicativo

- Dados de anúncios, pesquisas, envia sair do aplicativo e do aplicativo
- Modos de exibição de texto/Web
- Atualização do selo, categorias



#### <a name="setup"></a>Configuração

Configurando o Azure Mobile contrato é muito simple. Toda a documentação relacionada à interface do usuário está disponível no site do Azure Mobile contrato, [como navegar a interface do usuário](mobile-engagement-user-interface-home.md).

É recomendável que você iniciar, configurando as funções direita e associações de função para os usuários do seu projeto. Isso ajuda você a gerenciar o acesso apropriado para a plataforma para todos os usuários. As funções podem incluir:

- Administradores
- Desenvolvedores
- Visualizadores 

Posteriormente:
- Registre seu deviceID para testar seu próprio dispositivo.
- Vá para as configurações da sua conta e configurar o fuso horário para ter gráficos e tempo de entrega de notificação definido para seu fuso horário.
- Vá para as configurações do seu aplicativo e registrar o "aplicativo-info" você precisa ao alcance usuário final de destino.

Para obter mais informações sobre como executar sua primeira campanha de notificação de envio, examine [como começar a usar e gerenciar coloca para chegar aos seus usuários finais](mobile-engagement-how-tos.md).



## <a name="conclusion"></a>Conclusão


Programas de contrato são interativas e você deve melhorar sua continuamente conforme você experimentar o que funciona melhor para o aplicativo. 

Inicialmente, ao desenvolvimento de experiência com o contrato estratégias não tente criar uma estratégia de contrato global inteira. Dê uma abordagem passo a passo identificando KPIs e como aproveitá-los. Estratégia de contrato será exclusiva para cada aplicativo.

Depois que você tenha desenvolvido alguma experiência você pode considerar adicionando o seguinte a seus programas do contrato:

- Acompanhamento: Adquiridas usuários e você provavelmente definir fontes de coleta de dados. Contrato de móvel Azure pode ser vinculado a fontes de coleta de dados. Ele permite monitorar o desempenho de cada fonte. Essas informações serão interessantes para maximizar seu investimento de aquisição. 

- A / B teste: esta é uma parte essencial do programa de contrato. Cada aplicativo tem suas própria especificações. Com A / B teste, você pode melhorar seu programa de contrato.

- Localização geográfica: Este é uma grande oportunidade marcas. Graças a esse recurso, você pode entrar no lugar certo e no momento. É recomendável verificar que você reuniu suficiente dados de comportamento de usuário final antes de começar a usar localização geográfica.

- Envio de dados: envio de dados é um envio invisível. Envio de dados permite personalizar seu aplicativo com base em comportamento de usuário final. Por exemplo, se um segmento do usuário com frequência consulta produtos de alta tecnologia, o proprietário do aplicativo pode enviar um envio de dados que vai personalizar sua página inicial com o conteúdo de alta tecnologia.



## <a name="next-steps"></a>Próximas etapas

- [Criar uma conta do Azure Mobile contrato](mobile-engagement-create.md).
- Visite a [definir sua estratégia de contrato de celular](mobile-engagement-define-your-mobile-engagement-strategy.md) para saber mais sobre como definir sua estratégia de contrato de celular.



  

<!--Image references-->


<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
