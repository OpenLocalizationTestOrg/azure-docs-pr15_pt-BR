<properties
   pageTitle="Exibir operações de implantação com o portal | Microsoft Azure"
   description="Descreve como usar o portal do Azure para detectar erros de implantação do Gerenciador de recursos."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-portal"></a>Operações de implantação do modo de exibição com o Portal do Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Você pode exibir as operações para uma implantação por meio do portal Azure. Talvez seja mais interessado exibindo as operações quando você recebeu um erro durante a implantação para que este artigo aborda exibindo operações com falha. O portal fornece uma interface que permite que você encontrar os erros e determinar possíveis correções facilmente.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## <a name="use-deployment-operations-to-troubleshoot"></a>Use operações de implantação para solucionar problemas

Para ver as operações de implantação, use as seguintes etapas:

1. Para o grupo de recursos envolvido na implantação, observe o status da implantação do último. Você pode selecionar esse status para obter mais detalhes.

    ![status da implantação](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. Você verá o histórico de implantação recentes. Selecione a implantação que falhou.

    ![status da implantação](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. Selecione **falha. Clique aqui para obter detalhes** para ver uma descrição de por que a implantação falhou. Na imagem abaixo, o registro de DNS não é exclusivo.  

    ![Exibir implantação falha](./media/resource-manager-troubleshoot-deployments-portal/view-error.png)

    Essa mensagem de erro deve ser suficiente para você começar a solução de problemas. No entanto, se você precisar de mais detalhes sobre quais tarefas foram concluídas, você pode exibir as operações, conforme mostrado nas etapas a seguir.

4. Você pode exibir todas as operações de implantação na lâmina **implantação** . Selecione qualquer operação para ver mais detalhes.

    ![Exibir operações](./media/resource-manager-troubleshoot-deployments-portal/view-operations.png)

    Nesse caso, verá que a conta de armazenamento, rede virtual e conjunto de disponibilidade foram criados com êxito. O endereço IP público falhou e outros recursos não tentaram.

5. Você pode exibir eventos para a implantação selecionando **eventos**.

    ![Exibir eventos](./media/resource-manager-troubleshoot-deployments-portal/view-events.png)

6. Você vê todos os eventos para a implantação e selecione qualquer um para obter mais detalhes.

    ![Consulte eventos](./media/resource-manager-troubleshoot-deployments-portal/see-all-events.png)

## <a name="use-audit-logs-to-troubleshoot"></a>Usar logs de auditoria para solucionar problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver erros para uma implantação, use as seguintes etapas:

1. Exiba os logs de auditoria para um grupo de recursos selecionando **Logs de auditoria**.

    ![Selecione logs de auditoria](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. A lâmina de **Logs de auditoria** , você verá um resumo das operações recentes para todos os grupos de recursos em sua assinatura. Ele inclui uma representação gráfica do tempo e o status das operações, bem como uma lista das operações.

    ![Mostrar ações](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. Você pode filtrar seu modo de exibição de logs de auditoria concentrar-se em determinadas condições. Selecione **filtro** na parte superior da lâmina **logs de auditoria** .

    ![logs de filtro](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

4. Da lâmina do **filtro** , selecione as condições para restringir a exibição de logs de auditoria a apenas essas operações que você deseja ver. Por exemplo, você pode filtrar operações para exibir apenas erros do grupo de recursos.

    ![Definir opções de filtro](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

5. Você pode filtrar ainda mais operações definindo um intervalo de tempo. A imagem a seguir filtra o modo de exibição para um determinado período de tempo de 20 minutos.

    ![Definir hora](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

6. Você pode selecionar qualquer uma das operações na lista. Escolha a operação que contém o erro que você deseja pesquisar.

    ![Selecione operação](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
7. Você verá que todos os eventos para essa operação. Observe as **IDS de correlação** no resumo. Essa identificação é usada para controlar eventos relacionados. Ele pode ser útil ao trabalhar com o suporte técnico para solucionar um problema. Você pode selecionar qualquer evento para ver detalhes sobre o evento.

    ![Selecione o evento](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

8. Você verá detalhes sobre o evento. Em particular, observe as **Propriedades** para obter informações sobre o erro.

    ![Mostrar detalhes de log de auditoria](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

O filtro aplicado no log de auditoria é mantido na próxima vez que você exibi-la, para que você talvez precise alterar esses valores para ampliar o modo de exibição das operações.

## <a name="next-steps"></a>Próximas etapas

- Para obter ajuda com resolução de erros de implantação específica, consulte [resolver erros comuns ao implantar recursos para Azure com o Gerenciador de recursos do Azure](resource-manager-common-deployment-errors.md).
- Para saber sobre como usar os logs de auditoria para monitorar a outros tipos de ações, consulte [operações com o Gerenciador de recursos de auditoria](resource-group-audit.md).
- Para validar sua implantação antes de executá-lo, consulte [implantar um grupo de recursos com o modelo do Gerenciador de recursos do Azure](resource-group-template-deploy.md).
