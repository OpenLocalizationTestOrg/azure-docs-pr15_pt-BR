<properties
    pageTitle="Ambiente de serviço de aplicativo e AutoScaling | Microsoft Azure"
    description="Ambiente de serviço de aplicativo e AutoScaling"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="byvinyal"
/>

# <a name="autoscaling-and-app-service-environment"></a>Ambiente de serviço de aplicativo e AutoScaling

Azure ambientes de serviço de aplicativo suportam *auto-escala*. Você pode pools de trabalhador individual de escala automática com base em métricas ou cronograma.

![Opções de escala automática para um pool de trabalho.][intro]

AutoScaling otimiza a utilização do recurso automaticamente aumentando e diminuindo um ambiente de serviço de aplicativo de acordo com seu orçamento e ou carregar perfil.

## <a name="configure-worker-pool-autoscale"></a>Configurar a escala automática de pool de trabalho

Você pode acessar a funcionalidade de escala automática na guia **configurações** do pool de trabalho.

![Guia de configurações do pool de trabalho.][settings-scale]

A partir daí, a interface deve ser bastante familiar desde que é a mesma experiência que você vê quando você dimensiona um plano de serviço de aplicativo. 

![Configurações de escala manual.][scale-manual]

Você também pode configurar um perfil de escala automática.

![Configurações de escala automática.][scale-profile]

Perfis de escala automática são úteis para definir limites em sua escala. Dessa forma, você pode ter um desempenho consistente experiência definindo um valor de escala de limite inferior (1) e um limite de gastos previsíveis definindo um limite superior (2).

![Configurações de escala no perfil.][scale-profile2]

Após definir um perfil, você pode adicionar regras de escala automática para expandir ou reduzir o número de instâncias do pool de funcionário dentro dos limites definidos pelo perfil. Regras de escala automática são baseadas em métricas.

![Regra de escala.][scale-rule]

 Qualquer pool de trabalho ou métricas de front-end podem ser usadas para definir regras de escala automática. Essas métricas são as mesmas medições que você pode monitorar nos gráficos de blade de recursos ou definir alertas.

## <a name="autoscale-example"></a>Exemplo de escala automática

Examinando um cenário melhor pode ser ilustrada na escala automática de um ambiente de serviço de aplicativo.

Este artigo explica todas as considerações necessárias quando você configura escala automática. O artigo o orientará interações que entram quando você fator no autoscaling ambientes de serviço de aplicativo que é hospedado em ambiente de serviço de aplicativo.

### <a name="scenario-introduction"></a>Introdução ao cenário

Fernando é um administrador do sistema para uma empresa que tem migradas uma parte das cargas de trabalho que ele gerencia para um ambiente de serviço de aplicativo.

Ambiente de serviços de aplicativo é configurado para dimensionar manual da seguinte maneira:

* **Front-ends:** 3
* **Pool de trabalhador 1**: 10
* **Pool de trabalhador 2**: 5
* **Pool de trabalhador 3**: 5

Grupo de trabalho 1 é usado para cargas de trabalho de produção, enquanto pool de trabalhador 2 e 3 de pool de trabalho são usados para cargas de trabalho de desenvolvimento e controle de qualidade (p & r).

Os planos de serviço de aplicativo para o controle de qualidade e desenvolvimento são configurados para escala manual. O plano de serviço de aplicativo de produção está definida como escala automática de lidar com variações de tráfego e carregar.

Fernando é muito familiarizado com o aplicativo. Ele sabe que as horas de pico de carga são entre 9:00 AM e 6:00 PM, porque este é um aplicativo de (LOB) de linha de negócios que os funcionários usam enquanto estiverem no escritório. Uso descarta depois disso, quando os usuários são feitos para esse dia. Fora do horário de pico, ainda há alguns carga porque os usuários podem acessar o aplicativo remotamente, usando seus dispositivos móveis ou PCs domésticos. O plano de serviço de aplicativo de produção já estiver configurada como escala automática com base em uso de CPU com as seguintes regras:

![Configurações específicas de aplicativo LOB.][asp-scale]

|   **Perfil de escala automática – dias úteis – plano de serviço de aplicativo**     |   **Perfil de escala automática – fins de semana – plano de serviço de aplicativo**     |
|   ----------------------------------------------------    |   ----------------------------------------------------    |
|   **Nome:** Perfil de dia da semana                               |   **Nome:** Perfil de fim de semana                               |
|   **Escala por:** Regras de cronograma e desempenho            |   **Escala por:** Regras de cronograma e desempenho            |
|   **Perfil:** Dias úteis                                   |   **Perfil:** Fim de semana                                    |
|   **Tipo:** Recorrência                                    |   **Tipo:** Recorrência                                    |
|   **Intervalo de destino:** instâncias de 5 a 20                     |   **Intervalo de destino:** instâncias de 3 a 10                     |
|   **Dias:** Segunda-feira, terça-feira, quarta-feira, quinta-feira, sexta-feira  |   **Dias:** Sábado, domingo                              |
|   **Hora de início:** 9:00 AM                                 |   **Hora de início:** 9:00 AM                                 |
|   **Fuso horário:** UTC-08                                   |   **Fuso horário:** UTC-08                                   |
|                                                           |                                                           |
|   **Regra de escala automática (escala para cima)**                           |   **Regra de escala automática (escala para cima)**                           |
|   **Recurso:** Produção (ambiente de serviço de aplicativo)      |   **Recurso:** Produção (ambiente de serviço de aplicativo)      |
|   **Métrica:** % DA CPU                                       |   **Métrica:** % DA CPU                                       |
|   **Operação:** Mais de 60%                         |   **Operação:** Superior a 80%                         |
|   **Duração:** 5 minutos                                 |   **Duração:** 10 minutos                                |
|   **Tempo agregação:** Média                           |   **Tempo agregação:** Média                           |
|   **Ação:** Aumentar a contagem por 2                         |   **Ação:** Aumentar a contagem por 1                         |
|   **Interessantes para baixo (minutos):** 15                             |   **Interessantes para baixo (minutos):** 20                             |
|                                                           |                                                           |
  	|   **Regra de escala automática (escala para baixo)**                     |   **Regra de escala automática (escala para baixo)**                         |
|   **Recurso:** Produção (ambiente de serviço de aplicativo)      |   **Recurso:** Produção (ambiente de serviço de aplicativo)      |
|   **Métrica:** % DA CPU                                       |   **Métrica:** % DA CPU                                       |
|   **Operação:** Menos de 30%                            |   **Operação:** Menos de 20%                            |
|   **Duração:** 10 minutos                                |   **Duração:** 15 minutos                                |
|   **Tempo agregação:** Média                           |   **Tempo agregação:** Média                           |
|   **Ação:** Diminuir contagem em 1                         |   **Ação:** Diminuir contagem em 1                         |
|   **Interessantes para baixo (minutos):** 20                             |   **Interessantes para baixo (minutos):** 10                             |

### <a name="app-service-plan-inflation-rate"></a>Taxa de inflação de plano de serviço de aplicativo

Planos de serviço de aplicativo que estão configurados para escala automática fazê-lo em um ritmo máximo por hora. Essa taxa pode ser calculada com base nos valores fornecidos na regra escala automática.

Compreendendo e calcular a *taxa de inflação de plano de serviço de aplicativo* são importante para escala automática de ambiente de serviço de aplicativo porque alterações de escala para um grupo de trabalho não são instantâneas.

A taxa de inflação de plano de serviço de aplicativo é calculada da seguinte maneira:

![Cálculo de taxa de inflação de plano de serviço de aplicativo.][ASP-Inflation]

Com base em escala automática – regra escala para cima para o perfil do dia da semana da produção plano de serviço de aplicativo:

![Taxa de inflação de plano de serviço de aplicativo para dias da semana, com base em escala automática – regra de escala para cima.][Equation1]

No caso de escala automática – regra escala para cima para o perfil de fim de semana da produção plano de serviço de aplicativo, a fórmula resolveria para:

![Taxa de inflação de plano de serviço de aplicativo para fins de semana com base em escala automática – regra de escala para cima.][Equation2]

Esse valor também pode ser calculado para operações de escala para baixo.

Com base em escala automática – regra escala para baixo para o perfil do dia da semana da produção plano de serviço de aplicativo, isso teria da seguinte maneira:

![Taxa de inflação de plano de serviço de aplicativo para dias da semana, com base em escala automática – regra de escala para baixo.][Equation3]

No caso de escala automática – regra escala para baixo para o perfil de fim de semana da produção plano de serviço de aplicativo, a fórmula resolveria para:  

![Taxa de inflação de plano de serviço de aplicativo para fins de semana com base em escala automática – regra de escala para baixo.][Equation4]

O plano de serviço de aplicativo de produção pode crescer em uma taxa máxima de oito instâncias/hora durante a semana e quatro instâncias/hora durante a fim de semana. Ele pode liberar instâncias a uma taxa máxima de quatro instâncias/hora durante a semana e seis instâncias/hora durante fins de semana.

Se vários planos de serviço de aplicativo estão sendo hospedados em um pool de trabalho, você precisa calcular a *taxa de inflação total* como a soma da taxa de inflação de todos os planos de serviço de aplicativos que estão sendo hospedagem nesse pool trabalhador.

![Cálculo de taxa de inflação total para vários planos de serviços de aplicativo hospedado em um pool de trabalho.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Use a taxa de inflação de plano de serviço de aplicativo para definir regras de escala automática de pool de trabalho

Trabalhador pools que hospedam planos de serviço de aplicativo que estão configurados para escala automática precisam ser alocado a um buffer de capacidade. O buffer permite que as operações de escala automática para ampliar ou reduzir o plano de serviço de aplicativo, conforme necessário. O buffer mínimo seria o Total aplicativo serviço planejar inflação taxa calculada.

Como operações de escala de ambiente de serviço de aplicativo levar algum tempo para aplicar, qualquer alteração deve conta para outras alterações de demanda que podem ocorrer enquanto uma operação de escala está em andamento. Para acomodar a latência, recomendamos que você use o Total aplicativo serviço planejar inflação taxa calculada como o número mínimo de instâncias que foram adicionados para cada operação de escala automática.

Com essas informações, Fernando pode definir a seguinte perfil de escala automática e regras:

![Regras de perfil de escala automática por exemplo LOB.][Worker-Pool-Scale]

|   **Perfil de escala automática – dias úteis**                        |   **Perfil de escala automática – fins de semana**                |
|   ----------------------------------------------------    |   --------------------------------------------    |
|   **Nome:** Perfil de dia da semana                               |   **Nome:** Perfil de fim de semana                       |
|   **Escala por:** Regras de cronograma e desempenho            |   **Escala por:** Regras de cronograma e desempenho    |
|   **Perfil:** Dias úteis                                   |   **Perfil:** Fim de semana                            |
|   **Tipo:** Recorrência                                    |   **Tipo:** Recorrência                            |
|   **Intervalo de destino:** instâncias de 13 a 25                    |   **Intervalo de destino:** instâncias de 6 a 15             |
|   **Dias:** Segunda-feira, terça-feira, quarta-feira, quinta-feira, sexta-feira  |   **Dias:** Sábado, domingo                      |
|   **Hora de início:** 7:00 AM                                 |   **Hora de início:** 9:00 AM                         |
|   **Fuso horário:** UTC-08                                   |   **Fuso horário:** UTC-08                           |
|                                                           |                                                   |
|   **Regra de escala automática (escala para cima)**                           |   **Regra de escala automática (escala para cima)**                   |
|   **Recurso:** Pool de trabalho 1                             |   **Recurso:** Pool de trabalho 1                     |
|   **Métrica:** WorkersAvailable                            |   **Métrica:** WorkersAvailable                    |
|   **Operação:** Menor que 8                              |   **Operação:** Menos de 3                      |
|   **Duração:** 20 minutos                                |   **Duração:** 30 minutos                        |
|   **Tempo agregação:** Média                           |   **Tempo agregação:** Média                   |
|   **Ação:** Aumentar a contagem em 8                         |   **Ação:** Aumentar a contagem por 3                 |
|   **Interessantes para baixo (minutos):** 180                            |   **Interessantes para baixo (minutos):** 180                    |
|                                                           |                                                   |
|   **Regra de escala automática (escala para baixo)**                         |   **Regra de escala automática (escala para baixo)**                 |
|   **Recurso:** Pool de trabalho 1                             |   **Recurso:** Pool de trabalho 1                     |
|   **Métrica:** WorkersAvailable                            |   **Métrica:** WorkersAvailable                    |
|   **Operação:** Maiores que 8                           |   **Operação:** Maior que 3                   |
|   **Duração:** 20 minutos                                |   **Duração:** 15 minutos                        |
|   **Tempo agregação:** Média                           |   **Tempo agregação:** Média                   |
|   **Ação:** Diminuir contagem por 2                         |   **Ação:** Diminuir contagem por 3                 |
|   **Interessantes para baixo (minutos):** 120                            |   **Interessantes para baixo (minutos):** 120                    |

O intervalo de destino definido no perfil é calculado pelas instâncias mínimas definidas no perfil do plano de serviço de aplicativo + buffer.

O intervalo máximo seria a soma de todos os intervalos máximos para todos os planos de serviço de aplicativo hospedados no pool de trabalho.

A contagem de aumento de escala de regras deve ser configurada pelo menos 1 X a taxa de inflação de plano de serviço do aplicativo de escala.

Contagem de diminuição pode ser ajustada para algo entre 1/2 X ou 1 X a taxa de inflação de plano de serviço do aplicativo de escala para baixo.

### <a name="autoscale-for-front-end-pool"></a>Escala automática para o pool de front-end

Regras para escala automática front-end são mais simples para pools de trabalho. Essencialmente, você deve  
Certifique-se de que a duração de medição e os cronômetros de cooldown considerar que operações de escala em um plano de serviço de aplicativo não são instantâneas.

Para esse cenário, Fernando sabe que a taxa de erro aumenta depois front-ends atingir 80% da CPU e define a regra de escala automática para aumentar instâncias da seguinte maneira:

![Configurações de escala automática para o pool de front-end.][Front-End-Scale]

|   **Perfil de escala automática – Front-ends**              |
|   --------------------------------------------    |
|   **Nome:** Escala automática – Front-ends                |
|   **Escala por:** Regras de cronograma e desempenho    |
|   **Perfil:** Todos os dias                           |
|   **Tipo:** Recorrência                            |
|   **Intervalo de destino:** instâncias de 3 a 10             |
|   **Dias:** Todos os dias                              |
|   **Hora de início:** 9:00 AM                         |
|   **Fuso horário:** UTC-08                           |
|                                                   |
|   **Regra de escala automática (escala para cima)**                   |
|   **Recurso:** Pool de front-end                    |
|   **Métrica:** % DA CPU                               |
|   **Operação:** Mais de 60%                 |
|   **Duração:** 20 minutos                        |
|   **Tempo agregação:** Média                   |
|   **Ação:** Aumentar a contagem por 3                 |
|   **Interessantes para baixo (minutos):** 120                    |
|                                                   |
|   **Regra de escala automática (escala para baixo)**                 |
|   **Recurso:** Pool de trabalho 1                     |
|   **Métrica:** % DA CPU                               |
|   **Operação:** Menos de 30%                    |
|   **Duração:** 20 minutos                        |
|   **Tempo agregação:** Média                   |
|   **Ação:** Diminuir contagem por 3                 |
|   **Interessantes para baixo (minutos):** 120                    |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
