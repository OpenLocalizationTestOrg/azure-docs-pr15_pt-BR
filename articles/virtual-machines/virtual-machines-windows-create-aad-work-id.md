<properties
   pageTitle="Criar uma identidade de trabalho ou da escola no AAD | Microsoft Azure"
   description="Saiba como criar uma identidade de trabalho ou da escola no Active Directory do Azure para usar com suas máquinas virtuais do Windows."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="creating-a-work-or-school-identity-in-azure-active-directory-to-use-with-windows-vms"></a>Criando uma identidade de trabalho ou escola no Azure Active Directory para usar com o Windows VMs

Se você criou uma conta pessoal do Azure ou tiver uma assinatura MSDN pessoal e criar a conta do Azure para aproveitar os créditos MSDN Azure - você usou uma identidade de *conta da Microsoft* para criá-lo. Vários recursos excelentes do Azure – [modelos de grupo de recursos](../azure-resource-manager/resource-group-overview.md) é um exemplo--exigem uma conta corporativa ou escolar (uma identidade gerenciados pelo Azure Active Directory) para trabalhar. Você pode seguir as instruções abaixo para criar uma nova conta corporativa ou escolar porque Felizmente, uma das melhores coisas sobre sua conta do Azure pessoal é que ela vem com um domínio do Active Directory do Azure padrão que você pode usar para criar uma nova conta corporativa ou escolar que você pode usar com o Azure recursos que exigem-lo.

No entanto, alterações recentes possibilitam gerenciar sua assinatura com qualquer tipo de conta do Microsoft Azure usando o `azure login` método de logon interativo descrito [aqui](../xplat-cli-connect.md). Você pode usar esse mecanismo ou você pode seguir as instruções que acompanha. Você também pode [criar um trabalho ou escola identidade do Azure Active Directory para usar com VMs Linux](virtual-machines-linux-create-aad-work-id.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-create-aad-work-id](../../includes/virtual-machines-common-create-aad-work-id.md)]
