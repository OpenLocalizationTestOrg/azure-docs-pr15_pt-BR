
<properties
   pageTitle="Orientação Azure | padrões & práticas | Microsoft Azure"
   description="Arquiteturas de referência Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="christb"/>

# <a name="azure-reference-architectures"></a>Arquiteturas de referência Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

_Esse conteúdo está em desenvolvimento ativo. Hoje, é útil para que estamos fazendo-lo disponível para visualização. Seus comentários são importantes._

Nossas arquiteturas de referência são organizadas por cenário, com várias arquiteturas relacionadas agrupadas.
Cada arquitetura individual oferece práticas recomendadas e etapas dirigidas, bem como um componente executável que incorpora as recomendações.
Muitas das arquiteturas de são progressivas; Construindo sobre arquiteturas anteriores que têm menos requisitos.

## <a name="designing-your-infrastructure-for-resiliency"></a>Projetar sua infraestrutura para resiliência

Esta série começa com práticas recomendadas de configuração de máquina virtual ideal e culmina em uma implantação de várias regiões com failover.

- [Executando um máquina virtual do Windows no Azure](guidance-compute-single-vm.md)
- [Executando uma máquina virtual Linux no Azure](guidance-compute-single-vm-linux.md)
- [Executando várias VMs para escalabilidade e disponibilidade](guidance-compute-multi-vm.md)
- [Executando o Windows VMs para uma arquitetura de N camadas](guidance-compute-n-tier-vm.md)
- [Executando Linux VMs para uma arquitetura de N camadas](guidance-compute-n-tier-vm-linux.md)
- [Executando o Windows VMs em vários regiões para alta disponibilidade](guidance-compute-multiple-datacenters.md)
- [Executando Linux VMs em várias regiões para alta disponibilidade](guidance-compute-multiple-datacenters-linux.md)

## <a name="connecting-your-on-premises-network-to-azure"></a>Conectando a sua rede local para o Azure

Esta série inicia demonstrando as maneiras de se conectar a sua rede existente para o Azure. Em seguida, ele se expande para cobrir requisitos de disponibilidade e segurança.

- [Implementar uma arquitetura de rede híbrida com o Azure e VPN local](guidance-hybrid-network-vpn.md)
- [Implementar uma arquitetura de rede híbrida com rota expressa do Azure](guidance-hybrid-network-expressroute.md)
- [Implementar uma arquitetura de rede híbrido altamente disponível](guidance-hybrid-network-expressroute-vpn-failover.md)

## <a name="securing-your-hybrid-network"></a>Protegendo sua rede híbrido

Esta série aborda práticas comprovadas na criação DMZ no Azure para conexões seguras vêm do seu centro de dados locais e na Internet.

- [Implementação de uma DMZ entre Azure e seu centro de dados local](guidance-iaas-ra-secure-vnet-hybrid.md)
- [Implementação de uma DMZ entre Azure e a Internet](guidance-iaas-ra-secure-vnet-dmz.md)

## <a name="providing-identity-services"></a>Fornecendo serviços de identidade

Esta série inicia demonstrando como usar o Active Directory do Azure para fornecer autenticação de usuário no Azure. Em seguida, ele se expande para cobrir cenários complexos estendendo sua infraestrutura de ADICIONA no Azure e usando o ADFS para delegação.

- [Implementação do Active Directory do Azure](./guidance-identity-aad.md)
- [Estendendo serviços de diretório do Active Directory (ADICIONA) para o Azure](./guidance-identity-adds-extend-domain.md)
- [Criando uma floresta de recurso de serviços de diretório do Active Directory (ADICIONA) no Azure](./guidance-identity-adds-resource-forest.md)
- [Implementando serviços de Federação do Active Directory (AD FS) no Azure](./guidance-identity-adfs.md)

## <a name="architecting-scalable-web-application-using-azure-paas"></a>Arquitetura de aplicativo web scalable usando PaaS do Azure

Esta série aborda as recomendações para a criação de aplicativos web flexível e altamente disponível. 

- [Aplicativo web básico](guidance-web-apps-basic.md)
- [Melhorando escalabilidade em um aplicativo web](guidance-web-apps-scalability.md)
- [Aplicativo Web com alta disponibilidade](guidance-web-apps-multi-region.md)
