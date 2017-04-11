<properties
   pageTitle="Instalar a proteção de ponto de extremidade no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação de Central de segurança do Azure **Instalar proteção de ponto de extremidade**."
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
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="install-endpoint-protection-in-azure-security-center"></a>Instalar a proteção de ponto de extremidade no Centro de segurança do Azure

O Centro de segurança do Azure será recomendável que você provisionar um programa antimalware para seu Azure VMs (máquinas virtuais) se antimalware já não estiver habilitado. Essa recomendação se aplica apenas ao Windows VMs.

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo.  Isso não é um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na lâmina **recomendações** , selecione **Instalar proteção de ponto de extremidade**.
![Selecione Instalar proteção de ponto de extremidade][1]

2. A lâmina **Instalar proteção de ponto de extremidade** é aberta exibindo uma lista de VMs sem antimalware habilitado. Selecione na lista as VMs que você deseja instalar o antimalware em e clique em **instalar em VMs**.
![Selecione VMs instalar antimalware em][2]

3. Abre a lâmina **Selecione proteção de ponto de extremidade** para permitir que você selecione a solução de antimalware que você deseja usar. Neste exemplo, vamos selecionar **Antimalware da Microsoft**.
![Selecione proteção de ponto de extremidade][3]

4. Informações adicionais sobre a solução de antimalware são exibidas. Selecione **criar**.
![Criar solução antimalware][4]

5. Insira as configurações necessárias na lâmina **Adicionar extensão** e selecione **Okey**. Para saber mais sobre as definições de configuração, consulte [padrão e configuração de Antimalware personalizada](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

[Microsoft Antimalware](../azure-security-antimalware.md) agora está ativo nas VMs selecionadas.

## <a name="see-also"></a>Consulte também

Este artigo de mostrar como implementar a recomendação de Central de segurança "Instalar proteção de ponto de extremidade". Para saber mais sobre como ativar um programa de antimalware no Azure, consulte o seguinte:

- [Antimalware da Microsoft para serviços de nuvem e máquinas virtuais](../azure-security-antimalware.md) – Saiba como implantar antimalware da Microsoft.

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança.
- [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger seus recursos Azure.
- [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md) , Aprenda a monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Soluções de parceiros de monitoramento com o Centro de segurança do Azure](security-center-partner-solutions.md) , Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
- Postagens no [blog de segurança do azure](http://blogs.msdn.com/b/azuresecurity/) – localizar blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]:./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]:./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/create-antimalware-solution.png
