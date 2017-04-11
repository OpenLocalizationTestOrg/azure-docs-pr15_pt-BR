<properties
    pageTitle="Dimensionar a contagem de instância manual ou automaticamente | Microsoft Azure"
    description="Saiba como dimensionar seus serviços do Azure."
    authors="rboucher"
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
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="scale-instance-count-manually-or-automatically"></a>Dimensionar a contagem de instância manualmente ou automaticamente

No [Portal do Azure](https://portal.azure.com/), você pode definir manualmente a contagem de instâncias do seu serviço ou, você pode definir parâmetros para tê-la automaticamente com base na demanda de escala. Isso geralmente é conhecido como *escala de check-out* ou *escala em*.

Antes de dimensionamento com base na contagem de instância, você deve considerar que dimensionamento é afetado pela **camada de preços** além da contagem de instância. Níveis de preços diferentes podem ter memória e cores de um número diferente, e assim eles terá melhor desempenho para o mesmo número de instâncias (que é *escala para cima* ou *escala para baixo*). Este artigo aborda especificamente *escala em* e *check-out*.

Você pode dimensionar no portal e você também pode usar a [API REST](https://msdn.microsoft.com/library/azure/dn931953.aspx) ou [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para ajustar a escala manual ou automaticamente.

> [AZURE.NOTE] Este artigo descreve como criar uma configuração de escala automática no portal na [http://portal.azure.com](http://portal.azure.com). Configurações de escala automática criadas nesse portal não podem ser editado o portal clássico ([http://manage.windowsazure.com](http://manage.windowsazure.com)).

## <a name="scaling-manually"></a>Dimensionamento manualmente

1. No [Portal do Azure](https://portal.azure.com/), clique em **Procurar**e navegue para o recurso que você deseja dimensionar, como um **plano de serviço de aplicativo**.

2. O bloco de **escala** em **operações** informará o status de dimensionamento: **Desativar** para quando você estiver redimensionando manualmente, **em** para quando você estiver redimensionando por uma ou mais métricas de desempenho.
    ![Bloco de escala](./media/insights-how-to-scale/Insights_UsageLens.png)

3. Clicando no bloco, você será levado até a lâmina de **escala** . Na parte superior da lâmina escala você pode ver um histórico das ações de escala automática o serviço.  
    ![Blade de escala](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)

>[AZURE.NOTE] Somente ações que são executadas pelo escala automática aparecerá neste gráfico. Se você ajustar manualmente a contagem de instância, a alteração não será refletida neste gráfico.

4. Você pode ajustar manualmente o número **instâncias** com o controle deslizante.
5. Clique no comando **Salvar** e você vai ser dimensionada para esse número de instâncias de quase imediatamente.

## <a name="scaling-based-on-a-pre-set-metric"></a>Dimensionamento com base em uma métrica predefinida

Se você quiser que o número de instâncias para ajustar automaticamente com base em uma métrica, selecione a métrica desejada na lista suspensa de **escala por** . Por exemplo, para um **plano de serviço de aplicativo** pode dimensionar por **Porcentagem de CPU**.

1. Quando você seleciona uma métrica receberá um controle deslizante e/ou, caixas de texto para inserir o número de instâncias que você deseja dimensionar entre:

    ![Lâmina de escala com porcentagem de CPU](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)

    Escala automática nunca levará seu serviço abaixo ou acima dos limites que você definir, independentemente de sua carga.

2. Segundo, você pode escolher o intervalo de destino para a métrica. Por exemplo, se você escolheu a **porcentagem de CPU**, você pode definir um destino da CPU média em todas as instâncias no seu serviço. Uma escala de check-out acontecerá quando a CPU média excede o máximo que você definir, da mesma forma, uma escala em acontecerá sempre que a média CPU cai abaixo do mínimo.

3. Clique no comando **Salvar** . Escala automática verificará cada alguns minutos para certificar-se de que você está no intervalo de instância e destino para sua métrica. Quando o serviço recebe tráfego adicional, você obterá mais ocorrências sem fazer nada.

## <a name="scale-based-on-other-metrics"></a>Escala com base em outras métricas

Você pode dimensionar com base em métricas diferente de predefinições que aparecem na lista suspensa de **escala por** e podem até mesmo têm um conjunto complexo de escala e dimensionar em regras.

### <a name="adding-or-changing-a-rule"></a>Adicionar ou alterar uma regra

1. Escolha as **regras de cronograma e desempenho** na lista suspensa de **escala por** : ![regras de desempenho](./media/insights-how-to-scale/Insights_PerformanceRules.png)

2. Se você tivesse anteriormente escala automática, você verá um modo de exibição das regras exatas que você tinha em.

3. Para dimensionar com base em outro clique métrico da linha de **Adicionar regra** . Você também pode clicar em uma das linhas existentes para alterar a métrica que você tinha anteriormente à métrica que deseja dimensionar por.
![Adicionar regra](./media/insights-how-to-scale/Insights_AddRule.png)

4. Agora você precisa selecionar quais métrica que deseja dimensionar por. Ao escolher uma métrica há alguns aspectos a considerar:
    * O *recurso* a métrica é proveniente de. Normalmente, isso será o mesmo que o recurso que você estiver redimensionando. No entanto, se você deseja dimensionar pela profundidade de uma fila do armazenamento, o recurso é a fila que você deseja escala por.
    * O *nome de métrica* em si.
    * O *tempo de agregação* da métrica. Isso é como os dados são combinar ao longo da *duração*.

5. Depois de escolher sua métrica você escolher o limite para a métrica e o operador. Por exemplo, você poderia dizer **maior do que** **80%**.

6. Escolha a ação que você deseja fazer. Há alguns tipos diferentes de ações:
    * Aumentar ou diminuir em - isso irá adicionar ou remover o número de **valor** de instâncias que você definir
    * Aumentar ou diminuir porcentagem - isso alterará a contagem de instância por uma porcentagem. Por exemplo, você pode colocar 25 no campo **valor** , e se você tivesse atualmente 8 ocorrências, 2 seria adicionado.
    * Aumentar ou diminuir a - isso definirá a contagem de instância para o **valor** que você definir.

7. Por fim, você pode escolher legal para baixo - quanto tempo essa regra deve esperar após a ação anterior de escala para dimensionar novamente.

8. Depois de configurar sua regra acertar **Okey**.

9. Depois que você configurou todas as regras que você deseja, certifique-se de pressionar o comando **Salvar** .

### <a name="scaling-with-multiple-steps"></a>Dimensionamento com várias etapas

Os exemplos acima são bem básicos. No entanto, se você quiser ser mais curtas sobre dimensionamento para cima (ou para baixo), você mesmo pode adicionar várias regras de escala para a mesma métrica. Por exemplo, você pode definir duas regras de escala em porcentagem de CPU:

1. Dimensionar por 1 instância se porcentagem de CPU for acima de 60%
2. Dimensionar por 3 instâncias se porcentagem de CPU for acima 85%

![Várias regras de escala](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Com essa regra adicional, se sua carga excede 85% antes de uma ação de escala, você obterá duas instâncias adicionais em vez de um.

## <a name="scale-based-on-a-schedule"></a>Escala com base em um cronograma


Por padrão, quando você cria uma regra de escala será sempre aplicada. Você pode ver que quando você clica no cabeçalho de perfil:

![Perfil](./media/insights-how-to-scale/Insights_Profile.png)

No entanto, talvez você queira ter mais curtas expansão durante o dia ou semana, que no final de semana. Você mesmo pode desligar seu serviço completamente desativado horas de trabalho.

1. Para fazer isso, no perfil que você tem, selecione **Recorrência** em vez de **sempre** e escolha os horários em que você deseja aplicar o perfil.

2. Por exemplo, para que um perfil que se aplica durante a semana, na lista suspensa de **dias** desmarque **sábado** e **domingo**.

3. Para que um perfil que aplica durante o dia, defina a **hora de início** para a hora do dia que você deseja iniciar em.
    ![Padrão de recorrência](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)

4. Clique em **Okey**.

5. Em seguida, você precisará adicionar o perfil que você deseja aplicar outras vezes. Clique na linha de **Adicionar perfil** .
    ![Ausente do trabalho](./media/insights-how-to-scale/Insights_ProfileOffWork.png)

6. Nomeie seu novo, segundo, perfil, por exemplo, você poderia chamar- **ausente do trabalho**.

7. Em seguida, selecione **Recorrência** novamente e escolha o intervalo de contagem de instância que você deseja durante esse período.

8. Como com o perfil padrão, escolha os **dias** que você deseja este perfil para aplicar a e a **hora de início** durante o dia.

>[AZURE.NOTE] Escala automática usará as regras de poupança verão para qualquer **fuso horário** que você selecionar. No entanto, durante o horário de verão o deslocamento UTC mostrará o deslocamento de fuso horário base, não o deslocamento de poupança UTC verão.

9. Clique em **Okey**.

10. Agora, você precisará adicionar quaisquer regras que você deseja aplicar durante o segundo perfil. Clique em **Adicionar regra**e, em seguida, você poderia construir a mesma regra que durante o perfil padrão.
    ![Adicionar regra para desativá-lo trabalho](./media/insights-how-to-scale/Insights_RuleOffWork.png)

11. Certifique-se de criar os dois uma regra para escala e dimensionar no or else durante o perfil a contagem de instância apenas crescer (ou diminuir).

12. Por fim, clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

* [Métricas de serviço do monitor](insights-how-to-customize-monitoring.md) para certificar-se de que seu serviço está disponível e responde.
* [Habilitar o monitoramento e diagnóstico](insights-how-to-use-diagnostics.md) para coletar métricas de alta frequência detalhadas no seu serviço.
* [Notificações de alerta de recebimento](insights-receive-alert-notifications.md) sempre que ocorrerem eventos operacionais ou métricas cross um limite.
* [Monitorar o desempenho do aplicativo](../application-insights/app-insights-azure-web-apps.md) se você quiser entender exatamente como seu código é o desempenho na nuvem.
* [Exibir eventos e logs de auditoria](insights-debugging-with-events.md) para saber tudo o que aconteceu no seu serviço.
* [Disponibilidade do monitor e a capacidade de resposta de qualquer página da web](../application-insights/app-insights-monitor-web-app-availability.md) com ideias de aplicativo para que você pode descobrir se a página está inoperante.
