<properties
   pageTitle="Protegendo suas máquinas virtuais na Central de segurança do Azure | Microsoft Azure"
   description="Endereços este documento recomendações na Central de segurança do Azure que ajudarão a proteger suas máquinas virtuais e permanecer em conformidade com políticas de segurança."
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
   ms.date="09/25/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>Protegendo suas máquinas virtuais na Central de segurança do Azure

O Centro de segurança do Azure analisa o estado de segurança de seus recursos Azure. Quando o Centro de segurança identifica possíveis vulnerabilidades de segurança, ele cria recomendações que orientação você durante o processo de configuração controles necessários.  Recomendações se aplicam a tipos de recursos Azure: VMs (máquinas virtuais), rede, SQL e aplicativos.

Este artigo aborda recomendações que se aplicam a VMs.  Centro de recomendações de máquina virtual em torno de coleta de dados, aplicando atualizações de sistema, provisionamento antimalware, criptografando seus discos de máquina virtual e muito mais.  Use a tabela a seguir como uma referência para ajudá-lo a compreender as recomendações de máquina virtual disponíveis e o que cada uma delas fará se aplicá-lo.

## <a name="available-vm-recommendations"></a>Recomendações de máquina virtual disponíveis

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
| [Atualizar a versão do sistema operacional](security-center-update-os-version.md) | Recomenda-se de que você atualize a versão do sistema operacional (OS) para seu serviço de nuvem para a versão mais recente disponível para sua família do sistema operacional.  Para saber mais sobre os serviços de nuvem, consulte [Visão geral dos serviços de nuvem](../cloud-services/cloud-services-choose-me.md). |
| [Avaliação de vulnerabilidade não instalada](security-center-vulnerability-assessment-recommendations.md) | Recomenda-se de que você instale uma solução de avaliação de vulnerabilidade em sua máquina virtual. |
| [Remediar vulnerabilidades](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Permite que você veja vulnerabilidades de sistema e aplicativo detectadas pela solução de avaliação de vulnerabilidade instalada em sua máquina virtual. |

## <a name="see-also"></a>Consulte também

Para saber mais sobre as recomendações que se aplicam a outros tipos de recurso Azure, consulte o seguinte:

- [Protegendo seus aplicativos na Central de segurança do Azure](security-center-application-recommendations.md)
- [Protegendo sua rede na Central de segurança do Azure](security-center-network-recommendations.md)
- [Protegendo seu serviço SQL Azure na Central de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
