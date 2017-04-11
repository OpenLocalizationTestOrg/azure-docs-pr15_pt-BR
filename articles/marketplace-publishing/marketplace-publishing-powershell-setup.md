<properties
   pageTitle="Configurar o PowerShell para criar uma máquina virtual do Marketplace | Microsoft Azure"
   description="Instruções para configuração do PowerShell do Azure e usá-lo como um processo opcional fluem para criar imagens de máquina virtual para implantar e vendem ativado, o Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="hascipio"/>

# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Configurar o Azure PowerShell para criar uma oferta do Azure Marketplace
Para obter informações detalhadas sobre como configurar o PowerShell no Azure, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Uma abordagem simples é usar o método de certificado, que downloads e importa um certificado necessário para autenticação. Para obter o certificado necessário, use o cmdlet **Get-AzurePublishSettingsFile** . Quando solicitado, salve o arquivo. Para importar o certificado para uma sessão do PowerShell, use o cmdlet **AzurePublishSettingsFile de importação** .

Para configurar e armazenar as configurações de assinatura do Microsoft Azure comuns para a sessão do PowerShell, use os cmdlets **Set-AzureSubscription** e **Selecione AzureSubscription** :

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

O primeiro comando associa uma conta de armazenamento padrão com a assinatura (necessária para algumas operações de provisionamento de máquina virtual).  O segundo torna a assinatura aquela atual (reconhecida por outros cmdlets).

## <a name="see-also"></a>Consulte também
- [Introdução: como publicar uma oferta do Azure Marketplace](marketplace-publishing-getting-started.md)
- [Criando uma imagem de máquina virtual do Marketplace](marketplace-publishing-vm-image-creation.md)
