<properties 
    pageTitle="Implementação de contrato Mobile Azure para aplicativo de jogos"
    description="Cenário de aplicativo de jogos para implementar o contrato de celular do Azure" 
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

#<a name="implement-mobile-engagement-with-gaming-app"></a>Implementar o contrato móvel com o aplicativo de jogos

## <a name="overview"></a>Visão geral

Uma inicialização de jogos tenha iniciado um novo aplicativo jogo de role-play/estratégia de fichim com base. O jogo foi configurar e utilizar para 6 meses. Este jogo é um enorme sucesso e ela tem milhões de downloads e a retenção é muito alta em comparação com outros aplicativos de jogo de inicialização. A reunião de revisão trimestral, os participantes concordam que precisam para aumentar a receita média por usuário (ARPU). Pacotes de jogo Premium estão disponíveis como ofertas especiais. Esses pacotes de jogos permitem aos usuários atualizar a aparência e o desempenho de suas linhas de fichim e iscas ou soluciona no jogo. No entanto, as vendas de pacote são muito baixo. Assim que eles decidirem primeiro analisar a experiência do cliente com uma ferramenta de análise e, em seguida, para desenvolver um contrato de programa para aumentar a usando vendas avançadas segmentação.

Com base no [Azure Mobile contrato - guia de Introdução com as práticas recomendadas](mobile-engagement-getting-started-best-practices.md) que eles criam uma estratégia de contrato.

##<a name="objectives-and-kpis"></a>Objetivos e KPIs

Atender às principais responsáveis para o jogo. Todos concordar sobre um objetivo principal - aumentar as vendas de pacote premium por 15%. Criam Business indicadores chave de desempenho (KPIs) medida e unidade este objetivo

* Em que nível do jogo esses pacotes adquiridos?
* Qual é a receita por usuário, por sessão, por semana e mês?
* Quais são os tipos de compra favoritas?

Parte 1 do [Guia de Introdução](mobile-engagement-getting-started-best-practices.md) explica como definir os objetivos e KPIs. 

Com os KPIs de negócios agora definidos, o gerente de produto Mobile cria KPIs de contrato para determinar retenção e novas tendências de usuário.

* Monitorar retenção e usar entre intervalos a seguir: diariamente, cada 2 dias, semanal, mensal e cada 3 meses
* Contagens de usuário ativo
* A classificação de aplicativo no repositório

Os seguintes KPIs técnicos com base nas recomendações da equipe de TI, foram adicionados ao responder às seguintes perguntas:

* O que é o meu caminho de usuário (qual página é visitada, quanto tempo os usuários gastam nele)
* Número de falhas ou bugs encontrados por sessão
* Quais versões do sistema operacional estão executando meus usuários?
* O que é o tamanho médio da tela para meus usuários?
* Que tipo de conectividade com a internet que tenho que meus usuários?

Para cada KPI o gerente de produto do celular especifica os dados que ela precisa e onde ele está localizado no seu manual.

## <a name="engagement-program-and-integration"></a>Programa de contrato e integração

Antes de criar um programa de contrato avançado, o Diretor de projeto Mobile responsável pelo projeto deve ter uma profunda compreensão de como e quando produtos são consumidos pelos usuários.

Após 3 meses, o Diretor de projeto Mobile tenha coletado dados suficientes para aprimorar suas vendas de notificação de envio do aplicativo. Ele aprende que:

* A primeira compra geralmente acontece no nível de 14. Para 90% dos casos, a compra é novo armas lendária para $3.
* 80% dos casos, os usuários que tem feito uma compra, continue com o produto e fazer mais compras.
* Os usuários que passaram o nível de 20, comece a gastar mais de US $10/ semana.
* Os usuários tendem a comprar pacotes de premium no nível 16, 24 e 32.

Graças a essa análise o Director de projeto Mobile decide criar push específico sequências de notificação para aumentar em vendas de aplicativo. Ele cria três sequências de envio que ele chama: Bem-vindo do programa, o programa de vendas e programa inativa. Para obter mais informações, consulte a [Guias estratégicos](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)
    ![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->
