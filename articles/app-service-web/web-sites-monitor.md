<properties
    pageTitle="Monitorar aplicativos em um serviço de aplicativo do Azure"
    description="Saiba como monitorar aplicativos em um serviço de aplicativo do Azure usando o Portal do Azure."
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="byvinyal"/>

# <a name="how-to-monitor-apps-in-azure-app-service"></a>Como: monitorar aplicativos em um serviço de aplicativo do Azure

[Serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) fornece funcionalidade de monitoramento interna no [Portal do Azure](https://portal.azure.com).
Isso inclui a capacidade de revisar **métricas** para um aplicativo, bem como o plano de serviço de aplicativo, configuração de **alertas** e até mesmo **dimensionamento** automaticamente com base nos seguintes métricas e **cotas** .

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Métricas e Noções básicas sobre cotas

### <a name="quotas"></a>Cotas

Aplicativos hospedados no serviço de aplicativo estão sujeitos a certos *limites* sobre os recursos que eles podem usar. Os limites são definidos pelo **plano de serviço de aplicativo** associado com o aplicativo.

Se o aplicativo estiver hospedado em um plano de **livre** ou **compartilhado** , os limites dos recursos que o aplicativo pode usar são definidos pelas **cotas**.

Se o aplicativo estiver hospedado em um **básico**, **padrão** ou **Premium** plano e depois os limites nos recursos que eles podem usar são definidos pelo **tamanho** (pequeno, médio, grande) e a **contagem de instância** (1, 2, 3,...) do **plano de serviço de aplicativo**.

**Cotas** para aplicativos **livre** ou **compartilhados** são:

* **CPU(Short)**
   * Quantidade de CPU permitida para este aplicativo em um intervalo de 3 minutos. Essa cota define novamente a cada 3 minutos.
* **CPU(Day)**
   * Quantidade total da CPU permitida para este aplicativo em um dia. Essa cota define novamente a cada 24 horas à meia-noite UTC.
* **Memória**
   * Quantidade total de memória permitida para este aplicativo.
* **Largura de banda**
   * Quantidade total de largura de banda saída permitida para este aplicativo em um dia.
   Essa cota define novamente a cada 24 horas à meia-noite UTC.
* **Sistema de arquivos**
   * Quantidade total de armazenamento permitido.

A cota de apenas aplicável aos aplicativos hospedados em **básico**, **padrão** e planos de **Premium** é o **sistema de arquivos**.

Obter mais informações sobre cotas específico, limites e os recursos disponíveis para as SKUs de serviço de aplicativo diferentes podem ser encontradas aqui: [Limites de serviço de assinatura do Azure](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>Imposição de cota

Se um aplicativo em seu uso exceder a **coluna CPU (nome abreviado)**, **CPU (dia)**ou cota de **largura de banda** , em seguida, o aplicativo será interrompido até que a cota define novamente. Durante esse período, todas as solicitações de entrada resultará em um **HTTP 403**.
![][http403]

Se a cota de **memória** do aplicativo for excedida, o aplicativo será reiniciado.

Se a cota de **sistema de arquivos** for excedida, qualquer gravação operação falhará, incluindo a gravação logs.

Cotas podem ser aumentadas ou removidas de seu aplicativo atualizando seu plano de serviço de aplicativo.

### <a name="metrics"></a>Métricas

**Métricas** fornecem informações sobre o aplicativo ou o comportamento do plano de serviço de aplicativo.

Para um **aplicativo**, as métricas disponíveis são:

* **Tempo médio de resposta**
   * O tempo médio necessário para o aplicativo atender às solicitações em ms.
* **Média memória conjunto de trabalho**
   * O valor médio de memória em MiBs usado pelo aplicativo.
* **Tempo de CPU**
   * A quantidade de CPU em segundos consumida por aplicativo. Para obter mais informações sobre essa métrica, consulte: [porcentagem de CPU de vs de tempo de CPU](#cpu-time-vs-cpu-percentage)
* **Dados em**
   * O valor de entrada banda consumida por aplicativo do MiBs.
* **Dados**
   * A quantidade de largura de banda saída consumida por aplicativo do MiBs.
* **2xx http**
   * Contagem de solicitações resulta em um código de status de http > = 200, mas < 300.
* **3xx http**
   * Contagem de solicitações resulta em um código de status de http > = 300 mas < 400.
* **HTTP 401**
   * Contagem de solicitações resulta em código de status HTTP 401.
* **HTTP 403**
   * Contagem de solicitações resulta em código de status HTTP 403.
* **HTTP 404**
   * Contagem de solicitações resulta em código de status de HTTP 404.
* **HTTP 406**
   * Contagem de solicitações resulta em código de status de HTTP 406.
* **4xx http**
   * Contagem de solicitações resulta em um código de status de http > = 400 mas < 500.
* **Erros de servidor HTTP**
   * Contagem de solicitações resulta em um código de status de http > = 500, mas < 600.
* **Conjunto de trabalho de memória**
   * Quantidade de memória usada pelo aplicativo do MiBs atual.
* **Solicitações**
   * Número total de solicitações, independentemente de seu código de status HTTP resultante.

Para um **plano de serviço de aplicativo**, as métricas disponíveis são:

>[AZURE.NOTE] Métricas de plano de serviço de aplicativo estão disponíveis apenas para planos **básicos**, **Standard** e **Premium** SKU.

* **Porcentagem de CPU**
   * A média CPU usada em todas as instâncias do plano.
* **Porcentagem de memória**
   * A memória média usada em todas as instâncias do plano.
* **Dados em**
   * A entrada largura de banda média usada em todas as instâncias do plano.
* **Dados**
   * A média de largura de banda usada em todas as instâncias do plano de saída.
* **Comprimento da fila de disco**
   * O número médio de ler e gravar solicitações que foram enfileiradas armazenamento. Um comprimento de fila de disco alta é uma indicação de um aplicativo que pode estar desacelerando devido a/s de disco.
* **Comprimento da fila de http**
   * O número médio de solicitações HTTP que tinham que ficam na fila antes de ser atendida. Um comprimento de fila de HTTP alto ou crescente é um sintoma de um plano sob carga pesado.

### <a name="cpu-time-vs-cpu-percentage"></a>Porcentagem de CPU vs de tempo de CPU
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Há 2 métricas que reflitam o uso da CPU. **Tempo de CPU** e a **porcentagem de CPU**

**Tempo de CPU** é útil para aplicativos hospedados em planos **livre** ou **compartilhado** desde que uma das suas cotas é definida em minutos de CPU usados pelo aplicativo.

**Porcentagem de CPU** , por outro lado, é útil para aplicativos hospedados em planos **básicos**, **standard** e **premium** como eles podem ser dimensionados e essa métrica é uma boa indicação do uso do geral em todas as instâncias.

##<a name="metrics-granularity-and-retention-policy"></a>Detalhamento de métricas e política de retenção

Métricas para um aplicativo e o plano de serviço do aplicativo são registradas e agregadas pelo serviço com as seguintes granularidades e políticas de retenção:

 * Métricas de detalhamento de **minuto** são mantidas para **48 horas**
 * Métricas de detalhamento de **hora** são mantidas por **30 dias**
 * Métricas de detalhamento de **dia** são mantidas por **90 dias**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitorando métricas no Portal do Azure e cotas.

Você pode examinar o status das diferentes **cotas** e **métricas** afetando um aplicativo no [Portal do Azure](https://portal.azure.com).

![][quotas]
**Cotas** podem ser encontrados em Configurações >**cotas**. A UX permite que você examine: (1) o nome de cotas, (2) seu intervalo de redefinição, (3) seu limite atual e valor (4) atual.

![][metrics]
**Métricas** pode ser acessado diretamente da lâmina do recurso. Você também pode personalizar o gráfico por: (1) **clique** nele e selecione (2) **Editar gráfico**.
A partir daqui, você pode alterar o **intervalo de tempo**de (3), (4) **tipo de gráfico**e **métricas** de (5) para exibir.  

Você pode saber mais sobre métricas aqui: [métricas de serviço do Monitor](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Alertas e escala automática
Métricas para um plano do aplicativo ou serviço de aplicativo pode ser conectado até alertas, para saber mais sobre isso, consulte [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Aplicativos de serviço de aplicativo hospedados em básico, padrão ou premium aplicativo planos de serviço de suportam a **escala automática**. Isso permite que você configure regras que monitorar as métricas de plano de serviço de aplicativo e podem aumentar ou diminuir a contagem de instância fornecendo recursos adicionais conforme necessário, ou dinheiro salvando quando o aplicativo está provisionar excedente. Você pode saber mais sobre o dimensionamento automático do aqui: [como escala](../monitoring-and-diagnostics/insights-how-to-scale.md) e [práticas recomendadas para o Monitor do Azure autoscaling](../monitoring-and-diagnostics/insights-autoscale-best-practices.md) aqui

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="whats-changed"></a>O que mudou
* Para um guia para a alteração de sites para o serviço de aplicativo consulte: [o serviço de aplicativo do Azure e seu impacto sobre serviços existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
