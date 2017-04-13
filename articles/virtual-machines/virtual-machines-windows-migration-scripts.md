<properties
    pageTitle="Ferramentas da comunidade do Azure para gerenciamento de serviço de migração do Gerenciador de recursos do Azure"
    description="Este artigo catálogos as ferramentas que foram fornecidas pela comunidade para ajudar com a migração de recursos de IaaS do gerenciamento de serviço do Azure para a pilha de Gerenciador de recursos do Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="singhkay"/>

# <a name="community-tools-for-azure-service-management-to-azure-resource-manager-migration"></a>Ferramentas da comunidade do Azure para gerenciamento de serviço de migração do Gerenciador de recursos do Azure

Este artigo catálogos as ferramentas que foram fornecidas pela comunidade para ajudar com a migração de recursos de IaaS do gerenciamento de serviço do Azure para a pilha de Gerenciador de recursos do Azure.

>[AZURE.NOTE]Essas ferramentas não são suportadas oficialmente pela Microsoft Support. Portanto estão abertas origem na Github e estamos felizes aceito PRs para correções ou cenários adicionais. Para reportar um problema, use o recurso de problemas Github.
>
> Migrando com essas ferramentas fará com que o tempo de inatividade para sua máquina Virtual clássico. Se você estiver procurando migração plataformas com suporte, visite 
>
>- [Plataformas com suporte a migração de recursos de IaaS do clássico para pilha de Gerenciador de recursos do Azure](./virtual-machines-windows-migration-classic-resource-manager.md)
>- [Migração de clássico ao Gerenciador de recursos do Azure com suporte técnico detalhamento da plataforma](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
>- [Migrar IaaS recursos do clássico ao Azure Resource Manager usando o PowerShell do Azure](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## <a name="asm2arm"></a>ASM2ARM

Este é um módulo de script do PowerShell para migrar sua **única** Máquina Virtual (VM) da pilha de gerenciamento de serviço do Azure para o Gerenciador de recursos do Azure pilha. 

[Link para a documentação de ferramenta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz

migAz é uma opção adicional para migrar um conjunto completo de recursos de IaaS de gerenciamento de serviço do Azure para recursos de IaaS de Gerenciador de recursos do Azure. A migração pode ocorrer dentro da mesma assinatura ou entre diferentes assinaturas e tipos de assinatura (ex: assinaturas CSP).

[Link para a documentação de ferramenta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)