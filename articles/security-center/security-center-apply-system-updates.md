<properties
   pageTitle="Aplicar as atualizações do sistema no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar as recomendações do Centro de segurança do Azure **Aplicar atualizações de sistema** e **reinicializar após atualizações de sistema**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="apply-system-updates-in-azure-security-center"></a>Aplicar as atualizações do sistema no Centro de segurança do Azure

Central de segurança do Azure diariamente monitora Windows e Linux VMs (máquinas virtuais) faltam atualizações de sistema operacional. Centro de segurança recupera uma lista de segurança disponíveis e atualizações críticas do Windows Update ou Windows Server Update Services (WSUS), dependendo de qual serviço está configurado em uma máquina virtual Windows.  Central de segurança também verifica as atualizações mais recentes em sistemas Linux. Se sua máquina virtual está faltando uma atualização do sistema, o Centro de segurança será recomendável aplicar as atualizações do sistema

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo.  Isso não é um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na lâmina **recomendações** , selecione **Aplicar atualizações do sistema**.
![Aplicar atualizações do sistema][1]

2. A lâmina de **Aplicar atualizações do sistema** é aberta exibindo uma lista de VMs atualizações de sistema ausentes. Selecione uma máquina virtual.
![Selecione uma máquina virtual][2]

3. Um blade é aberta exibindo uma lista de atualizações ausentes para essa máquina virtual. Selecione uma atualização do sistema. Neste exemplo, vamos selecionar KB3156016.
![Atualizações de segurança ausentes][3]

4. Siga as etapas na lâmina **Atualização de segurança** para aplicar a atualização está ausente.
![Atualização de segurança][4]

## <a name="reboot-after-system-updates"></a>Reinicializar após atualizações do sistema

5. Retornar para a lâmina **recomendações** . Uma nova entrada foi gerada depois que você aplicou atualizações de sistema, chamadas **reinicializar após atualizações do sistema**. Essa entrada permite que você sabe que precisa reinicializar a máquina virtual para concluir o processo de aplicação de atualizações de sistema.
![Reinicializar após atualizações do sistema][5]

6. Selecione **reinicializar após atualizações do sistema**. Isso abre **que a reinicialização está pendente para concluir as atualizações do sistema** blade exibindo uma lista de VMs que você precisa reiniciar para concluir o processo de atualizações do sistema de aplicar.
![Reiniciar pendente][6]

Reinicie a máquina virtual do Azure para concluir o processo.

## <a name="see-also"></a>Consulte também

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger seus recursos Azure.
- [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md) , Aprenda a monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Soluções de parceiros de monitoramento com o Centro de segurança do Azure](security-center-partner-solutions.md) , Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
- Postagens no [blog de segurança do azure](http://blogs.msdn.com/b/azuresecurity/) – localizar blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]:./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png
