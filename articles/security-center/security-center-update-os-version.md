<properties
   pageTitle="Versão do sistema operacional de atualização na Central de segurança do Azure | Microsoft Azure"
   description="Este artigo mostra como implementar a **versão do sistema operacional de atualização**de recomendação de Central de segurança do Azure."
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

# <a name="update-os-version-in-azure-security-center"></a>Atualizar a versão do sistema operacional na Central de segurança do Azure

Máquinas virtuais (VMs) nos serviços de nuvem, o Centro de segurança do Azure será recomendável para que o sistema operacional (OS) ser atualizado se houver uma versão mais recente disponível.  Funções da web e trabalhador executando em produção slots são monitorados dos serviços de nuvem somente.

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo.  Isso não é um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na lâmina **recomendações** , selecione a **versão do sistema operacional de atualização**.
![Atualizar a versão do sistema operacional][1]

2. Isso abre a **versão do sistema operacional de atualização**de lâmina. Siga as etapas descritas neste blade para atualizar a versão do sistema operacional.

## <a name="see-also"></a>Consulte também

Este artigo de mostrar como implementar a recomendação de Central de segurança "Versão do sistema operacional de atualização". Para saber mais sobre os serviços de nuvem e atualizando a versão do sistema operacional para um serviço de nuvem, consulte:

- [Visão geral de serviços de nuvem](../cloud-services/cloud-services-choose-me.md)
- [Como atualizar um serviço na nuvem](../cloud-services/cloud-services-update-azure-service.md)
- [Como configurar os serviços de nuvem](../cloud-services/cloud-services-how-to-configure-portal.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger seus recursos Azure.
- [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md) , Aprenda a monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Soluções de parceiros de monitoramento com o Centro de segurança do Azure](security-center-partner-solutions.md) , Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do azure](http://blogs.msdn.com/b/azuresecurity/) - Obtenha as últimas notícias de segurança do Azure e informações.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
