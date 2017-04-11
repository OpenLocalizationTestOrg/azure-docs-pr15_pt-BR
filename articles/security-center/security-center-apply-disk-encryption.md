<properties
   pageTitle="Aplicar criptografia de disco na Central de segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação de Central de segurança do Azure **aplicar criptografia de disco**."
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

# <a name="apply-disk-encryption-in-azure-security-center"></a>Aplicar criptografia de disco na Central de segurança do Azure

O Centro de segurança do Azure será recomendável que você aplique criptografia de disco se você tiver o Windows ou Linux VM discos que não são criptografados usando criptografia de disco do Azure. Criptografia de disco permite criptografar discos do Windows e máquina virtual do IaaS Linux.  Criptografia é recomendada para volumes o sistema operacional e os dados em sua máquina virtual.


Criptografia de disco utiliza o recurso [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão do setor do Windows e o recurso de [DM Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer o sistema operacional e criptografia de dados para ajudar a proteger e proteger seus dados e atender aos seus compromissos de segurança e conformidade organizacionais. Criptografia de disco é integrada ao [Azure chave cofre](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerenciar as chaves de criptografia de disco e segredos em sua assinatura de chave cofre, garantindo que todos os dados na discos máquina virtual são criptografados inativos em seu [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/).

> [AZURE.NOTE] Criptografia de disco Azure há suporte para os seguintes sistemas operacionais Windows server - Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Criptografia de disco é suportada nos seguintes Linux server sistemas operacionais - Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES).

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na lâmina **recomendações** , selecione **aplicar criptografia de disco**.
2. Na lâmina **aplicar criptografia de disco** , você verá uma lista das VMs para os quais recomenda-se criptografia de disco.
3. Siga as instruções para aplicar a criptografia a essas VMs.

![][1]

Para criptografar máquinas virtuais do Azure que foram identificados pela Central de segurança como precisar de criptografia, recomendamos as seguintes etapas:

- Instalar e configurar o PowerShell do Azure. Isso permitirá que você execute os comandos do PowerShell necessários para configurar os pré-requisitos necessários para criptografar máquinas virtuais do Azure.
- Obter e execute o script do PowerShell do Azure disco criptografia pré-requisitos Azure.
- Criptografe suas máquinas virtuais.

[Criptografar uma máquina Virtual de Azure](security-center-disk-encryption.md) apresentará estas etapas.  Este tópico pressupõe que você está usando o Windows 10 como máquina do cliente do qual você configurará criptografia de disco.

Há muitas abordagens que podem ser usadas para configurar os pré-requisitos e configurar a criptografia para máquinas virtuais do Azure. Se você já estiver bem versados na Azure PowerShell ou CLI do Azure, em seguida, pode preferir usar abordagens alternativas. Para saber mais sobre essas outras abordagens consulte [criptografia de disco Azure](../security/azure-security-disk-encryption.md).



## <a name="see-also"></a>Consulte também

Este documento mostrado como implementar a recomendação de Central de segurança "Aplicar criptografia de disco". Para saber mais sobre criptografia de disco, consulte o seguinte:

- [Criptografia e gerenciamento de chaves com Cofre de chave do Azure](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (vídeo, 36 min 39 sec) – Saiba como usar o gerenciamento de criptografia de disco para IaaS VMs e Azure chave cofre para ajudar a proteger e proteger seus dados.
- [Criptografar uma máquina Virtual Azure](security-center-disk-encryption.md) (documento) – Saiba como criptografar máquinas virtuais do Azure.
- [Criptografia de disco Azure](../security/azure-security-disk-encryption.md) (documento) – Saiba como habilitar a criptografia de disco para Windows e Linux VMs.

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança.
- [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md) , Aprenda a monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger seus recursos Azure.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
- Postagens no [blog de segurança do azure](http://blogs.msdn.com/b/azuresecurity/) – localizar blog sobre conformidade e segurança do Azure.



<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
