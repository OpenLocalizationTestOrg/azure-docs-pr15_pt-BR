<properties
   pageTitle="Monitorar operações, eventos e contadores de Balanceador de carga | Microsoft Azure"
   description="Saiba como habilitar eventos de alerta e teste o log de status de integridade de Balanceador de carga do Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="log-analytics-for-azure-load-balancer-preview"></a>Análise de log de Balanceador de carga do Azure (visualização)

Você pode usar tipos diferentes de logs no Azure para gerenciar e solucionar problemas balanceadores de carga. Alguns desses logs podem ser acessadas por meio do portal. Todos os logs podem ser extraídos de um armazenamento de blob do Microsoft Azure e visualizou no ferramentas diferentes, como o Excel e PowerBI. Você pode saber mais sobre os diferentes tipos de logs na lista abaixo.

- **Logs de auditoria:** Você pode usar [Logs de auditoria do Azure](../../articles/monitoring-and-diagnostics/insights-debugging-with-events.md) (conhecida anteriormente como Logs operacionais) para exibir todas as operações sendo enviadas a seu inscrições Azure e seus status. Logs de auditoria são ativados por padrão e podem ser visualizados no portal do Azure.
- **Alertar logs de eventos:** Você pode usar esse log para ver quais alertas para balanceador de carga são geradas. O status de Balanceador de carga é coletado a cada cinco minutos. Esse registro só é gravado se um evento de alerta de Balanceador de carga é gerado.
- **Logs de teste de integridade:** Você pode usar esse registro para verificar status de verificação de integridade de teste, quantas instâncias estão online no balanceador de carga back-end e porcentagem de máquinas virtuais recebendo tráfego de rede do balanceador de carga. Esse log é gravado no alteração de evento de status de teste.

>[AZURE.IMPORTANT] Faça logon analytics atualmente só funciona para Internet opostas balanceadores de carga. Logs estão disponíveis apenas para os recursos implantados no modelo de implantação do Gerenciador de recursos. Você não pode usar logs para recursos do modelo de implantação clássico. Para obter mais informações sobre os modelos de implantação, consulte [Gerenciador de recursos de Noções básicas sobre implantação e implantação clássica](../../articles/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Habilitar o log

Log de auditoria é automaticamente habilitado para cada recurso Gerenciador de recursos. Você precisa habilitar eventos e registro em log do teste de integridade para iniciar a coleta de dados disponíveis por meio desses logs. Use as etapas a seguir para habilitar o log.

Entrar no [portal do Azure](http://portal.azure.com). Se você ainda não tiver um balanceador de carga, [Crie um balanceador de carga](load-balancer-get-started-internet-arm-ps.md) antes de continuar.

1. No portal do, clique em **Procurar**.
2. Selecione **balanceadores de carga**.

    ![Portal - balanceador de carga](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Selecione um balanceador de carga existente >> **Todas as configurações**.
4. No lado direito da caixa de diálogo sob o nome do balanceador de carga, role para **Monitoring**, clique em **Diagnóstico**.

    ![Portal - configurações de Balanceador de carga](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. No painel de **Diagnóstico** , em **Status**, selecione **sobre**.
6. Clique na **conta de armazenamento**.
7. Em **LOGS**, selecione uma conta de armazenamento existente ou crie um novo. Use o controle deslizante para determinar quantas dat de evento dias serão mantidos nos logs de eventos. 8. Clique em **Salvar**.

    ![Portal - logs de diagnóstico](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

>[AZURE.INFORMATION] logs de auditoria não exigem uma conta de armazenamento separada. O uso de armazenamento para o evento e a integridade do log de teste provocará encargos de serviço.

## <a name="audit-log"></a>Log de auditoria

O log de auditoria é gerado por padrão. Os logs são preservados por 90 dias no repositório de Logs de eventos do Azure. Saiba mais sobre esses logs lendo o artigo [Exibir eventos e logs de auditoria](../../articles/monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="alert-event-log"></a>Log de eventos de alerta

Esse log é gerado apenas se você habilitou-a em um por base de Balanceador de carga. Os eventos são registrados no formato JSON e armazenados na conta de armazenamento especificado quando você ativou o registro em log. A seguir é um exemplo de um evento.

    {
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }

A saída JSON mostra a propriedade *eventname* que descreverá o motivo balanceador de carga criado um alerta. Nesse caso, o alerta gerado foi devido a TCP porta esgotamento causado pela fonte IP NAT limites (SNAT).

## <a name="health-probe-log"></a>Log de teste de integridade

Esse log é gerado apenas se você habilitou-a em um por carga balanceador base conforme detalhado acima. Os dados são armazenados na conta de armazenamento especificado quando você ativou o registro em log. Um contêiner chamado 'ideias-logs-loadbalancerprobehealthstatus' é criado e os dados a seguintes são registrados:

    {
        "records":
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 1,
                "healthPercentage": 50.000000
            }
        },
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 0,
                "healthPercentage": 100.000000
            }
        }
    }

A saída JSON mostra no campo propriedades as informações básicas para o status de integridade de teste. A propriedade *dipDownCount* mostra o número total de instâncias no back-end que não estão recebendo o tráfego de rede devido a respostas de falha de teste.

## <a name="view-and-analyze-the-audit-log"></a>Exibir e analisar o log de auditoria

Você pode exibir e analisar dados de log de auditoria usando qualquer um dos seguintes métodos:

- **Ferramentas azure:** Recupere informações de logs de auditoria por meio do PowerShell do Azure, a Interface de linha de comando (CLI) do Azure, a API REST Azure ou o portal de visualização Azure. Instruções passo a passo para cada método são detalhadas no artigo [operações de auditoria com o Gerenciador de recursos](../../articles/resource-group-audit.md) .
- **Power BI:** Se você não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing) , você pode tentá-lo gratuitamente. Usando os [Logs de auditoria do Azure pack tribunal de conteúdo](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), você pode analisar seus dados com painéis pré-configurado ou você pode personalizar modos de exibição para atender às suas necessidades.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Exibir e analisar o teste de saúde e o log de eventos

Você precisa se conectar à sua conta de armazenamento e recuperar as entradas de log JSON para logs de teste de eventos e integridade. Após baixar os arquivos JSON, você pode convertê-los em CSV e modo de exibição no Excel, PowerBI ou qualquer outra ferramenta de visualização de dados.

>[AZURE.TIP] Se você estiver familiarizado com Visual Studio e conceitos básicos da alteração de valores para constantes e variáveis em c#, você pode usar as [Ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis do Github.

## <a name="additional-resources"></a>Recursos adicionais

- Postagem de blog de [visualizar os Logs de auditoria Azure com o Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
- [Exibir e analisar os Logs de auditoria do Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) postagem de blog.

## <a name="next-steps"></a>Próximas etapas

[Compreender testes de Balanceador de carga](load-balancer-custom-probe-overview.md)
