<properties
   pageTitle="Criar FQDN para uma máquina virtual no portal do Azure | Microsoft Azure"
   description="Aprenda a criar um nome de domínio totalmente qualificado ou FQDN para um gerente de recursos com base em máquina virtual no portal do Azure."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="iainfou"/>

# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>Criar um nome de domínio totalmente qualificado no portal do Azure
Quando você cria uma máquina virtual (VM) no [portal do Azure](https://portal.azure.com) usando o modelo de implantação do Gerenciador de recursos, um recurso IP público para a máquina virtual é criado automaticamente. Você pode usar este endereço IP acesse remotamente a máquina virtual. Embora o portal não cria um [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ou FQDN, por padrão, você pode adicionar um depois que a máquina virtual é criada. Este artigo demonstra as etapas para criar um nome DNS ou FQDN.

[AZURE.INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Agora você pode conectar remotamente para a máquina virtual usando esse nome DNS como com `ssh adminuser@testdnslabel.centralus.cloudapp.azure.com`.

## <a name="next-steps"></a>Próximas etapas
Agora que sua máquina virtual tem um nome IP e DNS público, você pode implantar comuns estruturas de aplicativo ou serviços como nginx, MongoDB, Docker, etc.

Você também pode ler mais sobre como [usar o Gerenciador de recursos](../azure-resource-manager/resource-group-overview.md) para obter dicas sobre a criação de implantações do Azure.