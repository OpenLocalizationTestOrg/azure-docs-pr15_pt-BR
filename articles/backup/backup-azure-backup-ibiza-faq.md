<properties
   pageTitle="Serviços de recuperação cofre perguntas Frequentes | Microsoft Azure"
   description="Esta versão das perguntas Frequentes dá suporte à versão de visualização público do serviço do Azure Backup. Respostas para perguntas frequentes sobre o agente de backup, backup e retenção, recuperação, segurança e outras perguntas comuns sobre a solução de Backup do Azure."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="solução de backup; serviço de backup"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="10/21/2016"
     ms.author="trinadhk; markgal; jimpark;"/>

# <a name="recovery-services-vault---faq"></a>Compartimento de serviços de recuperação - perguntas Frequentes


Este artigo fornece informações específicas ao Cofre de serviços de recuperação e ele suplementa a [Perguntas Frequentes de Backup do Azure](backup-azure-backup-faq.md). Perguntas frequentes sobre o Azure Backup fornece o conjunto completo de perguntas e respostas sobre o serviço de Backup do Azure.  

Você pode fazer perguntas sobre o Backup do Azure na seção Disqus neste artigo ou um artigo relacionado. Você também pode postar perguntas sobre o serviço de Backup do Azure no [Fórum de discussão](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Compartimentos de serviços de recuperação são Gerenciador de recursos com base. Compartimentos de Backup (modo clássico) ainda são suportados? <br/>
Sim, compartimentos de Backup ainda são suportados. Crie compartimentos de Backup no [portal de clássico](https://manage.windowsazure.com). Crie compartimentos de serviços de recuperação no [portal do Azure](https://portal.azure.com). No entanto é altamente recomendável que você crie Cofre de serviços de recuperação como todos os futuros aprimoramentos estarão disponível somente em serviços de recuperação cofre.

## <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Pode migrar um cofre de Backup para um cofre de serviços de recuperação? <br/>
Infelizmente não, neste momento você não pode migrar o conteúdo de um cofre de Backup para um cofre de serviços de recuperação. Estamos trabalhando em Adicionando essa funcionalidade, mas ele não está disponível como parte do Public Preview.

## <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Dão suporte a serviços de recuperação compartimentos VMs clássicas ou Gerenciador de recursos com base em VMs? <br/>
Ambos os modelos de suporte de compartimentos de serviços de recuperação.  Você pode fazer backup de uma máquina virtual criada no portal do clássico (que são VMs de modo clássico) ou uma máquina virtual criada no portal do Azure (que são Gerenciador de recursos com base) para um cofre de serviços de recuperação.

## <a name="i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode--how-can-i-backup-them-in-recovery-services-vault"></a>Eu tiver feito backup meu VMs clássicas em backup cofre. Agora quero migrar meu VMs do modo clássico para o modo do Gerenciador de recursos.  Como posso fazer backup-los em Cofre de serviços de recuperação?
Backups das VMs clássicos no cofre backup não pode ser migrado automaticamente ao Cofre de serviços de recuperação quando você migra VMs do clássico para o modo do Gerenciador de recursos. Siga estas etapas para a migração de backups de máquina virtual:

1. Em backup cofre, acesse a guia **Itens protegidos** e selecione a máquina virtual. Clique em [Parar proteção](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Deixe *Excluir dados de backup associados* opção **desmarcada**.
2. Migre a máquina virtual do modo clássico para o modo do Gerenciador de recursos. Certifique-se de armazenamento e rede correspondente à máquina virtual também são migrados para o modo do Gerenciador de recursos.
3. Criar um cofre de serviços de recuperação e configurar o backup na máquina virtual migrada usando a ação de **Backup** na parte superior do painel de controle do cofre. Saiba mais sobre como [habilitar backup em Cofre de serviços de recuperação](backup-azure-vms-first-look-arm.md)
