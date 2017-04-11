<properties 
    pageTitle="Implementação de contrato Mobile Azure para o aplicativo de mídia"
    description="Cenário de aplicativo de mídia para implementar o contrato de celular do Azure" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="08/19/2016"
    ms.author="piyushjo"/>

#<a name="implement-mobile-engagement-with-media-app"></a>Implementar o contrato móvel com o aplicativo de mídia

## <a name="overview"></a>Visão geral

John é um gerente de projeto móvel de uma empresa de grande de mídia. Ele iniciado recentemente um novo aplicativo que tem uma contagem de download muito alto. Ele atingiu suas metas para download, mas ainda seu retorno em Investment(ROI) por usuário não atender seus requisitos. 

John já tenha identificado por seu ROI está muito baixo. Os usuários frequentemente pararem de usar seu aplicativo após somente 2 semanas e maioria deles nunca voltar. Deseja aumentar a retenção de seu aplicativo.

Após alguns inicial testes, ele aprendeu quando ele envolve seus usuários com as notificações por push, eles tendem a continuar a usar seu aplicativo. Também os usuários que eram inativos muitas vezes retornará ao aplicativo dependendo notificações, que ele envia. José decide investir em algum tipo de programa de contrato para seu aplicativo que usa o direcionamento avançadas com notificações por push.

John recentemente leu o [Contrato de celular do Azure - guia de Introdução com as práticas recomendadas](mobile-engagement-getting-started-best-practices.md) e decidiu implementar as recomendações do guia.

##<a name="objectives-and-kpis"></a>Objetivos e KPIs

Principais responsáveis para aplicativo de João atendem. Business é gerado de anúncios, os usuários consumam sua mídia. Aumentando o conteúdo consumido por usuário, John aumenta sua receita. Todos concordar sobre um objetivo principal: para aumentar as vendas de anúncios por 25%. Criam Business indicadores chave de desempenho (KPIs) medida e unidade este objetivo

* Número de anúncios clicado por usuário
* Quantas páginas de artigo visitaram (por usuário / por sessão / por semana / mês...)
* O que são categorias favoritas

Com base na reunião de João com principais responsáveis por ele definiu sua KPIs de negócios. Ele segue parte 1 do [Azure Mobile contrato - guia de Introdução com as práticas recomendadas](mobile-engagement-getting-started-best-practices.md). 

Em seguida, ele cria os seguintes KPIs de contrato para garantir que os objetivos são atingidos:

* Monitorar retenção em intervalos a seguir: diário, semanal, quinzenal e mensal.
* Contagens de usuários ativos
* A classificação de aplicativo no aplicativo armazena

Os seguintes KPIs técnico com base nas recomendações da equipe de TI, foram adicionados ao responder às seguintes perguntas:

* O que é o meu caminho de usuário (qual página é visitada, quantos usuários de tempo gastam em-lo)
* Número de falhas ou bugs encontrados por sessão?
* Quais versões do sistema operacional estão executando meus usuários?
* O que é o tamanho médio da tela para meus usuários?
* Que tipo de conexões com a internet que tenho que meus usuários?

Para cada KPI, ele classifica os dados necessários e ele registra-la no local apropriado do seu manual.

## <a name="engagement-program-and-integration"></a>Programa de contrato e integração

Agora que John terminou definindo seus KPIs, ele começa sua fase de estratégia de contrato definindo 4 programas de contrato e seus objetivos:    ![][1]

Em seguida, John vai mais profundo, detalha notificações por push para cada programa. Notificação de envio são definidos pelas cinco elementos:

1. Objetivo: o que é o objetivo da notificação
2. Como o objetivo seja atingido
3. Destino: quem receberá a notificação?
4. Conteúdo: O que é o texto e o formato da notificação (no App/Out do aplicativo)
5. Quando: o que é o melhor momento para enviar essa notificação de envio

    ![][2]

Para obter mais informações consulte [Guias estratégicos](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

De acordo com a parte 2 do white paper John usa a seção de destino para definir quais dados que ele tenha a coletar e grava seu plano de marca em conjunto com a equipe de TI para implementar a solução. Após 1 semana de implementação e testes de aceitação do usuário, John finalmente pode iniciar seus programas.

##<a name="program-results"></a>Resultados do programa

4 meses mais tarde, John revisa o desempenho de programas. O programa de boas-vindas e o programa semanais são reunião suas metas. O número de usuário com apenas uma sessão diminui, mais recursos do aplicativo estão sendo usados e o número de conexões por semana tem duplicado.

O **Programa inativa** está ajudando John entender tendências de usuário. Parece que 15% dos usuários inativos voltar ao aplicativo. No entanto maioria deles não permanecer ativa mais de 1 mês. John prevê uma otimização de possível dessa sequência com notificações adicionais e expandindo suas escolhas de conteúdo.

**Descubra o programa** não funciona bem. Ela aumenta cruzada selling mas não o suficiente para alcançar seus objetivos. John identifica que ele não tem dados suficientes para tornar relevantes direcionamento e propor conteúdo apropriado. Ele interrompe este programa e se concentra em "notificações por push editorial" com Azure Mobile contrato de envio. Seus jornalistas já tem uma solução CMS para enviar notificações por push e não deseja alterar.

José decide usar a API atinja que é uma API REST HTTP que permite Gerenciando campanhas de alcance sem precisar usar interface AZME Web. Com essa abordagem João pode coletar os dados que ele precisa e permitir que os seus autores manter usando a solução CMS.

Para garantir que esse recurso funcione corretamente, John pede a equipe de TI esteja atento nos seguintes pontos:

1. **Sistemas operacionais** : todas elas têm suas próprias regras para administrar as notificações por push, portanto John decide listar todos os casos e verifica se as APIs lidar com ele.
Por exemplo: O sistema de envio Android permite panorama que não é o caso com iOS.

2. **Período**: João quer uma API, que defina o período de tempo e um fim como campanhas. Ele deseja preservar os usuários de qualquer limite de notificação interrupções.

3. **Categorias**: equipe de Marketing prepara modelo para cada tipo de alerta. John perguntará equipe de TI definir categorias dentro da API.

Após alguns testes John está satisfeito. Graças a essa API, jornalistas ainda podem enviar notificações de envio com seus CMS e Azure Mobile contrato coleta todos os dados de comportamentos para eles

Após esses 4 primeiro meses resultados refletem uma boa confiança geral de desempenho e fornece para John e seu quadro, ROI por aumentos de usuário por 15% e vendas móvel representam 17,5% do total de vendas, um aumento de 7,5% em apenas quatro meses.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
