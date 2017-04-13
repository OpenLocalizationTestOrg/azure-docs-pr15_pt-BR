<properties
   pageTitle="Monitorar operações, eventos e contadores NSGs | Microsoft Azure"
   description="Saiba como habilitar contadores, eventos e log operacional para NSGs"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/14/2016"
   ms.author="jdial" />

#<a name="log-analytics-for-network-security-groups-nsgs"></a>Análise de log de grupos de segurança de rede (NSGs)

Você pode usar tipos diferentes de logs no Azure para gerenciar e solucionar problemas NSGs. Alguns desses logs podem ser acessadas por meio do portal e todos os logs podem ser extraídos de um armazenamento de blob do Microsoft Azure e visualizou no ferramentas diferentes, como [A análise de Log](../log-analytics/log-analytics-azure-networking-analytics.md), o Excel e o PowerBI. Você pode saber mais sobre os diferentes tipos de logs na lista abaixo.

- **Logs de auditoria:** Você pode usar [Logs de auditoria do Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (conhecida anteriormente como Logs operacionais) para exibir todas as operações sendo enviadas a seu inscrições Azure e seus status. Logs de auditoria são ativados por padrão e podem ser visualizados no portal do Azure preview.
- **Logs de eventos:** Você pode usar esse log para ver quais NSG as regras são aplicadas às VMs e funções de instância com base no endereço MAC. O status dessas regras é coletado a cada 60 segundos.
- **Logs de contador:** Você pode usar esse log para exibir quantas vezes cada regra NSG foi aplicada para negar ou permitir o tráfego.

>[AZURE.WARNING] Logs estão disponíveis apenas para os recursos implantados no modelo de implantação do Gerenciador de recursos. Você não pode usar logs para recursos do modelo de implantação clássico. Para melhor compreensão dos dois modelos, consulte o artigo de [implantação do Gerenciador de recursos de compreensão e implantação clássica](../resource-manager-deployment-model.md) .

##<a name="enable-logging"></a>Habilitar o log
Log de auditoria é ativado automaticamente em todas as horas para cada recurso Gerenciador de recursos. Você precisa habilitar eventos e registro em log para iniciar a coleta de dados disponíveis por meio desses logs de contador. Para habilitar o log, siga as etapas abaixo.

1.  Entrar no [portal do Azure](https://portal.azure.com). Se você ainda não tiver um grupo de segurança de rede existente, [criar um NSG](virtual-networks-create-nsg-arm-ps.md) antes de continuar.

2.  No portal de visualização, clique em **Procurar** >> **grupos de segurança de rede**.

    ![Portal de visualização - grupos de segurança de rede](./media/virtual-network-nsg-manage-log/portal-enable1.png)

3. Selecione um grupo de segurança de rede existente.

    ![Portal de visualização - configurações de grupo de segurança de rede](./media/virtual-network-nsg-manage-log/portal-enable2.png)

4. Na lâmina **configurações** , clique em **Diagnóstico**e, em seguida, no painel de **Diagnóstico** , ao lado de **Status**, clique **em**
5. Na lâmina **configurações** , clique em **Conta de armazenamento**e selecione um armazenamento existente de conta, ou crie um novo.  

>[AZURE.INFORMATION] logs de auditoria não exigem uma conta de armazenamento separada. O uso de armazenamento para o evento e o log de regra provocará encargos de serviço.

6. Na lista suspensa apenas em **Conta de armazenamento**, selecione se deseja log de eventos, contadores ou ambos e clique em **Salvar**.

    ![Portal de visualização - logs de diagnóstico](./media/virtual-network-nsg-manage-log/portal-enable3.png)

## <a name="audit-log"></a>Log de auditoria
Esse log (anteriormente conhecido como o "log operacional") é gerada pelo Azure por padrão.  Os logs são preservados por 90 dias no repositório de Logs de eventos do Azure. Saiba mais sobre esses logs lendo o artigo [Exibir eventos e logs de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="counter-log"></a>Log de contador
Esse registro só é gerado se você habilitou-a em uma base por NSG conforme descrito acima. Os dados são armazenados na conta de armazenamento especificado quando você ativou o registro em log. Cada regra aplicada aos recursos é registrada no formato JSON, conforme mostrado abaixo.

    {
        "time": "2015-09-11T23:14:22.6940000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupRuleCounter",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupCounters",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"DenyAllOutBound",
            "direction":"Out",
            "type":"block",
            "matchedConnections":0
            }
    }

## <a name="event-log"></a>Log de eventos
Esse registro só é gerado se você habilitou-a em uma base por NSG conforme descrito acima. Os dados são armazenados na conta de armazenamento especificado quando você ativou o registro em log. Os dados a seguir são registrados:

    {
        "time": "2015-09-11T23:05:22.6860000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupEvent",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupEvents",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"AllowVnetOutBound",
            "direction":"Out",
            "priority":65000,
            "type":"allow",
            "conditions":{
                "destinationPortRange":"0-65535",
                "sourcePortRange":"0-65535",
                "destinationIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32",
                "sourceIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32"
            }
        }
    }

## <a name="view-and-analyze-the-audit-log"></a>Exibir e analisar o log de auditoria
Você pode exibir e analisar dados de log de auditoria usando qualquer um dos seguintes métodos:

- **Ferramentas azure:** Recupere informações de logs de auditoria por meio do PowerShell do Azure, a Interface de linha de comando (CLI) do Azure, a API REST Azure ou o portal de visualização Azure.  Instruções passo a passo para cada método são detalhadas no artigo [operações de auditoria com o Gerenciador de recursos](../resource-group-audit.md) .
- **Power BI:** Se você ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing) , você pode tentá-lo gratuitamente. Usando os [Logs de auditoria do Azure pack tribunal de conteúdo](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/) você pode analisar seus dados com pré-configurado painéis que você pode usar como-é ou personalizar.

## <a name="view-and-analyze-the-counter-and-event-log"></a>Exibir e analisar o contador e o log de eventos

[Análise de Log](../log-analytics/log-analytics-azure-networking-analytics.md) do Azure pode coletar o contador log de eventos arquivos e de sua conta de armazenamento de Blob e inclui recursos avançados de pesquisa para analisar os logs e visualizações.

Você também pode se conectar à sua conta de armazenamento e recuperar as entradas de log JSON para logs de eventos e contador. Após baixar os arquivos JSON, você pode convertê-los em CSV e modo de exibição no Excel, PowerBI ou qualquer outra ferramenta de visualização de dados.

>[AZURE.TIP] Se você estiver familiarizado com Visual Studio e conceitos básicos da alteração de valores para constantes e variáveis em c#, você pode usar as [Ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis do Github.

## <a name="next-steps"></a>Próximas etapas

- Visualizar contador e logs de eventos com [A análise de Log](../log-analytics/log-analytics-azure-networking-analytics.md)
- Postagem de blog de [visualizar os Logs de auditoria Azure com o Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
- [Exibir e analisar os Logs de auditoria do Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) postagem de blog.
