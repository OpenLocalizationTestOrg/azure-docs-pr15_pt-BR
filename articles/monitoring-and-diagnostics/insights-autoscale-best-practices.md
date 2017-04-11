<properties
    pageTitle="Práticas recomendadas para autoscaling Monitor do Azure. | Microsoft Azure"
    description="Saiba princípios para usar efetivamente autoscaling no Monitor do Azure."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="ashwink"/>

# <a name="best-practices-for-azure-monitor-autoscaling"></a>Práticas recomendadas para o Monitor do Azure autoscaling

As seções a seguir neste documento ajudarão-lo a entender as práticas recomendadas para escala automática do Azure. Após examinar essas informações, você poderá melhor efetivamente usar escala automática em sua infraestrutura Azure.

## <a name="autoscale-concepts"></a>Conceitos de escala automática

- Um recurso pode ter apenas *uma* configuração de escala automática
- Uma configuração de escala automática pode ter um ou mais perfis e cada perfil podem ter uma ou mais regras de escala automática.
- Uma configuração de escala automática escalas instâncias horizontalmente, que é *check-out* , aumentando o *in* e instâncias diminuindo o número de instâncias.
 Uma configuração de escala automática tem um máximo, mínimo e valor padrão de instâncias.
- Um trabalho de escala automática sempre lê a métrica associada a escala por, verificando se ele cruzou o limite configurado para escala ou escala-in. Você pode exibir uma lista de métricas que escala automática pode escala por no [Monitor do Azure autoscaling comuns métricas](insights-autoscale-common-metrics.md).
- Todos os limites são calculados em um nível de instância. Por exemplo, "escala check-out por 1 instância média quando CPU > 80% quando a contagem de instância é 2", significa escala-out quando a CPU média em todas as instâncias é superior a 80%.
- Você sempre recebe notificações de falha via email. Especificamente, o proprietário, colaboradores e os leitores do recurso destino recebem emails. Você também sempre recebe o email de *recuperação* quando escala automática recupera uma falha e começa funcionando normalmente.
- Você pode optar por usar-in para receber uma notificação de ação de escala bem-sucedido por email e webhooks.

## <a name="autoscale-best-practices"></a>Práticas recomendadas de escala automática

Use as seguintes práticas recomendadas ao usar escala automática.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Verifique os valores mínimos e máximos são diferentes e tem uma margem adequada entre eles
Se você tiver uma configuração que tenha o mínimo = 2, máximo = 2 e atual contagem de instância é 2, nenhuma ação de escala pode ocorrer. Manter uma margem adequada entre as contagens de instância mínimo e máximo, que são inclusivo. Escala automática sempre escalas entre esses limites.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Redimensionamento manual seja redefinido pelo máximo e mínimo de escala automática
Se você atualizar manualmente a contagem de instância para um valor acima ou abaixo do máximo, o mecanismo de escala automática automaticamente escalas de volta para o mínimo (se abaixo) ou o máximo (se acima). Por exemplo, você pode definir o intervalo entre 3 e 6. Se você tiver uma instância em execução, o mecanismo de escala automática expande para 3 instâncias na sua próxima execução. Da mesma forma, ele seria escala de 8 ocorrências de volta para 6 na sua próxima execução.  Redimensionamento manual é muito temporária, a menos que você redefinir as regras de escala automática também.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Sempre usar uma combinação de escala-out e escala na regra que executa um aumentar e diminuir
Se você usar apenas uma parte da combinação, escala automática escala do único check-out ou, até o máximo ou o mínimo, é alcançado.

### <a name="do-not-switch-between-the-azure-portal-and-the-azure-classic-portal-when-managing-autoscale"></a>Não alternar entre o portal do Azure e o Azure portal clássico ao gerenciar escala automática
Para serviços de nuvem e os serviços de aplicativo (Web Apps), use o portal do Azure (portal.azure.com) para criar e gerenciar as configurações de escala automática. Para conjuntos de escala de máquina Virtual use PoSH, CLI ou API REST para criar e gerenciar as configurações de escala automática. Não alterne entre o portal de clássico Azure (manage.windowsazure.com) e o portal do Azure (portal.azure.com) quando Gerenciando configurações de escala automática. Portal clássico do Azure e seu back-end subjacente tem limitações. Mover para o portal do Azure para gerenciar escala automática usando uma interface de usuário gráfica. As opções são usar a escala automática do PowerShell, CLI ou API REST (via Azure Resource Explorer).

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Escolha a estatística apropriada para sua métrica de diagnóstico
Para métricas de diagnóstico, você pode escolher entre *média*, *mínimo*, *máximo* e *Total* como uma métrica a escala por. A estatística mais comuns é *média*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Escolha os limites cuidadosamente para todos os tipos de métrica
Recomendamos que você escolha cuidadosamente diferentes limites de escala-out e escala com base em situações práticas.

Nós *não recomendamos* configurações de escala automática, como os exemplos abaixo com os valores de limite igual ou muito semelhante para check-out e em condições:

- Aumentar instâncias por 1 contar quando contagem de segmentos < = 600
- Diminuir instâncias por 1 contar quando contagem de segmentos > = 600

Vamos examinar um exemplo do que pode levar a um comportamento que possa parecer confuso. Cosider sequência a seguir.

1. Suponha que há 2 instâncias começar com e, em seguida, o número médio de threads por instância aumenta a 625.
2. Escala automática se expande adicionando uma instância 3º.
3. Em seguida, suponha que a contagem de segmento média em instância fica 575.
4. Antes de dimensionamento para baixo, a escala automática tenta estimar que o estado final será se ele dimensionada em. Por exemplo, (contagem de instância atual) de 575 x 3 = 1,725 / 2 (final número de instâncias quando reduzida) = 862.5 threads. Isso significa que escala automática teria imediatamente fora de escala novamente até mesmo depois de ela dimensionada, se a contagem de segmento média permanece o mesmo ou até mesmo cai apenas uma pequena quantidade. No entanto, se ele aumentadas novamente, todo o processo repetir, levam a um loop infinito.
5. Para evitar essa situação (chamada de "falta de sincronismo"), escala automática não redimensionados para baixo em todos os. Em vez disso, ele ignora e revê a condição novamente na próxima vez que executa de trabalho do serviço. Isso poderia confundir muitas pessoas porque escala automática não aparecem para trabalhar quando a contagem de segmento média foi 575.

Estimativa durante uma escala de destina-se a evitar situações "flappy". Você deve manter esse comportamento em mente quando você escolhe os mesmos limites de escala e em.

Recomendamos que você escolha uma margem adequada entre o escala-out e em limites. Como exemplo, considere a seguinte combinação de regra melhor.

- Aumentar instâncias por 1 contar quando CPU % > = 80
- Diminuir instâncias por 1 contar quando CPU % < = 60

Nesse caso  

1. Suponha que há 2 instâncias começar.
2. Se o % de CPU médio nas instâncias vai para 80, escala automática expande adicionando uma terceira ocorrência.
3. Agora, suponha que ao longo do tempo, a CPU % cai para 60.
4. Regra de escala do escala automática estima o estado final se fosse a escala-in. Por exemplo, (contagem de instância atual) de 60 x 3 = 180 / 2 (final número de instâncias quando reduzida) = 90. Portanto escala automática não escala-in porque ele teriam escala-out novamente imediatamente. Em vez disso, ele ignora dimensionamento para baixo.
5. A próxima escala de tempo automática verificações, a CPU continua a se situar para 50. Estima novamente - instância 50 x 3 = 150 / 2 instâncias = 75, que está abaixo do limite de dimensionamento de 80, para que ele comporta com êxito a 2 instâncias.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Considerações para valores de limite para métricas especiais de escala
 Para métricas especiais como armazenamento ou fila de barramento de serviço métrica de comprimento, o limite é o número médio de mensagens disponíveis por número atual de instâncias. Escolha cuidadosamente escolher o valor de limite para essa métrica.

Vamos ilustrá-la com um exemplo para garantir que você compreende o comportamento melhor.

- Aumentar instâncias por 1 contagem quando a contagem de mensagens de fila do armazenamento > = 50
- Diminuir instâncias por 1 contagem quando a contagem de mensagens de fila do armazenamento < = 10

Considere a seguinte sequência:

1. Há 2 instâncias de fila do armazenamento.
2. Mensagens continuam chegando e ao revisar a fila do armazenamento, a contagem total lê 50. Você pode assumir que escala automática deve iniciar uma ação de escala-out. No entanto, observe que ainda é 50/2 = 25 mensagens por instância. Portanto, escala-out não ocorrer. Para a primeira escala a acontecer, a contagem total de mensagens na fila do armazenamento deve ser 100.
3. Em seguida, suponha que a contagem total da mensagem chegar a 100.
4. Uma instância de fila do armazenamento 3º é adicionada devido a uma ação de escala-out.  A próxima ação de escala-out não acontecerá até que a contagem total de mensagens na fila atinja 150 porque 150/3 = 50.
5. Agora o número de mensagens na fila obtém menor. Com 3 instâncias, a primeira ação de escala acontece quando o total de mensagens em todas as filas adiciona até 30 porque 30/3 = 10 mensagens por instância, que é o limite de escala.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Considerações para dimensionamento quando vários perfis são configurados em uma configuração de escala automática

Em uma configuração de escala automática, você pode escolher um perfil padrão, que é sempre aplicado sem qualquer dependência em cronograma ou a hora, ou você pode escolher um perfil recorrente ou um perfil para um período fixo com um intervalo de data e hora.

Quando o serviço de escala automática processa-los, sempre verifica na seguinte ordem:

1. Perfil de data fixa
2. Perfil recorrente
3. ("Sempre") perfil padrão

Se uma condição de perfil for atendida, escala automática não verifica a seguinte condição de perfil abaixo dela. Escala automática processa apenas um perfil por vez. Isso significa que se você quiser também incluir uma condição de processamento de um perfil de nível inferior, você deve incluir essas regras também no perfil atual.

Vamos analisar isso usando um exemplo:

A imagem abaixo mostra uma configuração de escala automática com um perfil padrão de instâncias mínimos = 2 e máximos instâncias = 10. Neste exemplo, as regras são configuradas para escala-out quando a contagem de mensagem na fila for maior que 10 escala-in em quando a contagem de mensagem na fila for menor que 3. Agora o recurso pode dimensionar entre as instâncias de 2 e 10.

Além disso, há um perfil recorrente definido para segunda-feira. Ela é definida para instâncias mínimas = 2 e máximos instâncias = 12. Isso significa na segunda-feira, a primeira escala de tempo automática procura por esta condição, se a contagem de instância for 2, ele pode ser expandido para o novo mínimo de 3. Desde que continua escala automática encontrar essa condição de perfil correspondido (segunda-feira), ele processa somente as CPU regras baseadas em escala-out/in configuradas para este perfil. No momento, ele não verifica o comprimento da fila. No entanto, se você também quiser a condição de comprimento de fila a ser verificado, você deve incluir essas regras do perfil padrão também no seu perfil de segunda-feira.

Da mesma forma, quando escala automática alterna de volta para o perfil padrão, ele primeiro verifica se as condições mínimas e máximos forem atendidas. Se o número de instâncias no momento é 12, ele comporta para 10, o máximo permitido para o perfil padrão.

![configurações de escala automática](./media/insights-autoscale-best-practices/insights-autoscale-best-practices.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Considerações para dimensionamento quando várias regras são configuradas em um perfil
Há casos em que talvez você precise definir várias regras em um perfil. O seguinte conjunto de regras de escala automática são usadas pelo uso de serviços quando várias regras são definidas.

Em *escala*, escala automática é executado se qualquer regra é atendida.
Em *escala de*escala automática exigem todas as regras sejam atendidas.

Para ilustrar, vamos supor que você tem as seguintes regras de escala automática 4:

- Se CPU < 30%, escala-in por 1
- Se memória < 50%, escala-in por 1
- Se CPU > 75%, escala-out por 1
- Se memória > 75%, escala-out por 1

Ocorre o seguir:

- Se 76% é de CPU e memória é 50%, podemos escala-out.
- Se CPU é 50% e memória é 76% podemos escala-out.

Por outro lado, se CPU é 25% e memória é escala 51% automática não **não** escala-in. Para dimensionar-in, CPU deve ser 29% e memória 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Selecione sempre uma contagem de instância de segurança padrão
A contagem de instância padrão é importante escala automática escalas seu serviço para essa contagem quando métricas não estão disponíveis. Portanto, selecione uma contagem de instância padrão que é segura para suas cargas de trabalho.

### <a name="configure-autoscale-notifications"></a>Configurar notificações de escala automática
Escala automática notifica os administradores e colaboradores do recurso por email se qualquer uma das seguintes condições ocorrer:

- serviço de escala automática falha ao executar uma ação.
- Métricas não estão disponíveis para o serviço de escala automática para tomar uma decisão de escala.
- Métricas estão disponível (recuperação) novamente a tomar uma decisão de escala.
Além de condições acima, você pode configurar as notificações de email ou webhook para obter notificações de ações de escala bem-sucedido.
