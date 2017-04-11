<properties
   pageTitle="Central de segurança do Azure e Azure máquinas virtuais | Microsoft Azure"
   description="Este documento ajuda você a entender como o Centro de segurança do Azure pode proteger você máquinas virtuais do Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-and-azure-virtual-machines"></a>Central de segurança do Azure e Azure máquinas virtuais

[O Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) ajuda você a evitar, detectar e responder a ameaças. Ele fornece o monitoramento e a política de gerenciamento de segurança integrada em suas assinaturas Azure, ajuda a detectar ameaças que poderiam ir percebidas e funciona com uma enorme variedade de soluções de segurança.

Este artigo mostra como o Centro de segurança pode ajudar você a proteger suas máquinas virtuais do Azure (máquina virtual).

## <a name="why-use-security-center"></a>Por que usar o Centro de segurança?

Central de segurança ajuda você a proteger os dados de máquina virtual no Azure fornecendo visibilidade configurações de segurança da sua máquina virtual. Quando o Centro de segurança protege suas VMs, os seguintes recursos estarão disponíveis:

- Configurações de segurança do sistema operacional (OS) com as regras de configuração recomendada
- Segurança do sistema e atualizações críticas que estão ausentes
- Recomendações de proteção de ponto de extremidade
- Validação de criptografia de disco
- Avaliação de vulnerabilidade e correção
- Detecção de ameaças

Além de ajudar a proteger suas VMs do Azure, o Centro de segurança também fornece segurança monitoramento e gerenciamento de serviços de nuvem, os serviços de aplicativo, redes virtuais e muito mais. 

>[AZURE.NOTE] Consulte [Introdução à Central de segurança do Azure](security-center-intro.md) para saber mais sobre o Centro de segurança do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar o Centro de segurança do Azure, você precisará saber e considere o seguinte:

- Você deve ter uma assinatura do Microsoft Azure. Consulte [Preços do Centro de segurança](https://azure.microsoft.com/pricing/details/security-center/) para obter mais informações sobre níveis de gratuito e padrão do Centro de segurança.
- Planejar a adoção do Centro de segurança, consulte o [guia de planejamento do Centro de segurança do Azure e operações](security-center-planning-and-operations-guide.md) para saber mais sobre as considerações de planejamento e operações.
- Para obter informações sobre o suporte de sistema operacional, consulte [O Centro de segurança do Azure perguntas frequentes (FAQ)](security-center-faq.md). 

## <a name="set-security-policy"></a>Política de segurança do conjunto

Coleta de dados deve ser habilitado para que o Centro de segurança que Azure pode coletar as informações necessárias para fornecer recomendações e alertas geradas com base na política de segurança que você configurar. Na figura abaixo, você pode ver que a **coleta de dados** foi **ativado**.

Uma política de segurança define o conjunto de controles que são recomendados para recursos dentro da inscrição especificada ou o grupo de recursos. Antes de habilitar a política de segurança, você deve ter habilitada de coleta de dados, o Centro de segurança coleta dados de suas máquinas virtuais para avaliar seu estado de segurança, recomendações de segurança e ameaças o alerta. No Centro de segurança, você definir políticas para suas assinaturas Azure ou grupos de recursos de acordo com as necessidades de segurança da sua empresa e do tipo de aplicativos ou sigilo dos dados de cada assinatura. 

![Política de segurança](./media/security-center-virtual-machine/security-center-virtual-machine-fig1.png)

>[AZURE.NOTE] Para saber mais sobre cada **política de prevenção** disponível, consulte o artigo de [políticas de segurança do conjunto](security-center-policies.md) .

## <a name="manage-security-recommendations"></a>Gerenciar as recomendações de segurança

Centro de segurança analisa o estado de segurança de seus recursos Azure. Quando o Centro de segurança identifica possíveis vulnerabilidades de segurança, ele cria recomendações. As recomendações orientação-lo no processo de configuração controles necessários.

Depois de definir uma política de segurança, a Central de segurança analisa o estado de segurança de seus recursos para identificar possíveis vulnerabilidades. As recomendações são mostradas em um formato de tabela em que cada linha representa uma recomendação específica. A tabela a seguir fornece alguns exemplos de recomendações para VMs do Azure e o que cada uma delas fará se aplicá-lo. Quando você seleciona uma recomendação, você receberá informações que mostra como implementar a recomendação no Centro de segurança.

|Recomendação|Descrição|
|-----|-----|
|[Habilitar coleta de dados para assinaturas](security-center-enable-data-collection.md)|Recomenda-se de que você ativa a coleta de dados na diretiva de segurança para cada uma das suas assinaturas e todas as máquinas virtuais (VMs) em suas assinaturas.|
|[Remediar vulnerabilidades de sistema operacional](security-center-remediate-os-vulnerabilities.md)|Recomenda que você alinhar suas configurações de sistema operacional com as regras de configuração recomendada, por exemplo, não permitir senhas sejam salvas.|
|[Aplicar atualizações do sistema](security-center-apply-system-updates.md)|Recomenda-se de que você implanta ausentes segurança de sistema e atualizações críticas VMs.|
|[Reinicializar após atualizações do sistema](security-center-apply-system-updates.md#reboot-after-system-updates)|Recomenda-se de que você reinicializar uma máquina virtual para concluir o processo de aplicação de atualizações de sistema.|
|[Instalar a proteção de ponto de extremidade](security-center-install-endpoint-protection.md)|Recomenda-se de que você provisionar programas antimalware VMs (somente para o Windows VMs).|
|[Resolver alertas de integridade de proteção de ponto de extremidade](security-center-resolve-endpoint-protection-health-alerts.md)|Recomenda-se de que você resolver falhas de proteção de ponto de extremidade.|
|[Habilitar o agente de máquina virtual](security-center-enable-vm-agent.md)|Permite que você veja qual VMs exigem o agente de máquina virtual. O agente de máquina virtual deve ser instalado em VMs para provisionar patch varredura, varredura de linha de base e programas de antimalware. O agente de máquina virtual é instalado por padrão para VMs que são implantadas do Azure Marketplace. O artigo [agente de máquina virtual e extensões – parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o agente de máquina virtual.|
| [Aplicar criptografia de disco](security-center-apply-disk-encryption.md) |Recomenda-se de que você criptografe seus discos de máquina virtual usando criptografia de disco do Azure (Windows e Linux VMs). Criptografia é recomendada para volumes o sistema operacional e os dados em sua máquina virtual.|
| [Avaliação de vulnerabilidade não instalada](security-center-vulnerability-assessment-recommendations.md) | Recomenda-se de que você instale uma solução de avaliação de vulnerabilidade em sua máquina virtual. |
| [Remediar vulnerabilidades](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Permite que você veja vulnerabilidades de sistema e aplicativo detectadas pela solução de avaliação de vulnerabilidade instalada em sua máquina virtual. |

>[AZURE.NOTE] Para saber mais sobre recomendações, consulte o artigo de [recomendações de segurança Gerenciando](security-center-recommendations.md) .

## <a name="monitor-security-health"></a>Monitorar a saúde de segurança

Depois de habilitar [políticas de segurança](security-center-policies.md) para recursos de uma assinatura, o Centro de segurança analisará a segurança dos seus recursos para identificar possíveis vulnerabilidades.  Você pode exibir o estado de segurança de seus recursos, juntamente com quaisquer problemas na lâmina **integridade de segurança do recurso** . Quando você clicar em **máquinas virtuais** no bloco de integridade de **segurança do recurso** , a lâmina **máquinas virtuais** será aberto com recomendações para suas VMs. 

![Integridade de segurança](./media/security-center-virtual-machine/security-center-virtual-machine-fig2.png)

## <a name="manage-and-respond-to-security-alerts"></a>Gerenciar e responder a alertas de segurança

Centro de segurança automaticamente coleta, analisa e integra dados do log de seus recursos Azure, rede e soluções de parceiros conectada (como o firewall e o ponto de extremidade de soluções de proteção), para detectar ameaças reais e reduzir os falsos positivos. Utilizando uma agregação diverso dos [recursos de detecção](security-center-detection-capabilities.md), a Central de segurança é capaz de gerar alertas de segurança prioridades para ajudá-lo a investigar o problema e fornecer recomendações sobre como corrigir possíveis ataques rapidamente.

![Alertas de segurança](./media/security-center-virtual-machine/security-center-virtual-machine-fig3.png)

Selecione um alerta de segurança para saber mais sobre o evento que disparou o alerta e, se houver, as etapas necessárias para remediar um ataque. Alertas de segurança são agrupados por [tipo](security-center-alerts-type.md) e a data.


## <a name="see-also"></a>Consulte também

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
