<properties
    pageTitle="Glossário do Azure - dicionário Azure | Microsoft Azure"
    description="Use o Glossário do Azure para entender a terminologia de nuvem na plataforma Windows Azure. Esse dicionário Azure curto fornece definições para termos de nuvem comuns do Azure."
    keywords="Dicionário Azure, terminologia de nuvem, Glossário Azure, definições de terminologia, termos de nuvem"
    services="na"
    documentationCenter="na"
    authors="MonicaRush"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="monicar"/>


# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glossário do Microsoft Azure: um dicionário de terminologia de nuvem na plataforma Windows Azure

Glossário do Microsoft Azure é um dicionário curto da terminologia de nuvem para a plataforma Windows Azure.

## <a name="find-service-definitions-and-other-cloud-terms"></a>Localizar definições de serviço e outros termos de nuvem

* Para obter definições de serviços do Azure e seus AWS correspondentes consulte [Microsoft Azure e Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

* Para gerais do setor termos de nuvem consulte [termos computação em nuvem](https://azure.microsoft.com/overview/cloud-computing-dictionary/).

Glossário do Azure com as duas referências acima fornece uma taxonomia de ponta a ponta do Azure e o setor de nuvem.  

## <a name="azure-glossary-list"></a>Lista de glossário Azure


### <a name="account"></a>conta  
Trabalho ou escola ou conta pessoal da Microsoft que é usado para acessar e gerenciar uma assinatura do Azure.  
Veja também [como Azure assinaturas são associadas com o Active Directory do Azure](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="availability-set"></a>conjunto de disponibilidade  
Uma coleção de máquinas virtuais que são gerenciados em conjunto para fornecer a confiabilidade e redundância de aplicativos. O uso de um conjunto de disponibilidade garante que durante qualquer um evento de manutenção planejadas ou pelo menos uma máquina virtual está disponível.  
Consulte também [Gerenciar a disponibilidade de máquinas virtuais Linux](./virtual-machines/virtual-machines-linux-manage-availability.md) ou [Gerenciar a disponibilidade de máquinas virtuais do Windows](./virtual-machines/virtual-machines-windows-manage-availability.md)


### <a name="classic-model"></a>Modelo de implantação clássico Azure  
Um dos dois [modelos de implantação](resource-manager-deployment-model.md) usadas para implantar recursos no Azure (o novo modelo é o gerente de recursos do Azure). Alguns recursos Azure podem ser implantados em um modelo ou outro, enquanto outras pessoas podem ser implantadas em ambos os modelos. Orientação para detalhes de recursos Azure individuais que modelos de agrupamentos de um recurso pode ser implantada com.


### <a name="cli"></a>Azure interface de linha (comando)  
Uma [interface de linha](xplat-cli-install.md) que podem ser usados para gerenciar os serviços do Azure do Windows, OSX e PCs Linux.


### <a name="powershell"></a>PowerShell Azure  
Uma [interface de linha de comando](powershell-install-configure.md) para gerenciar serviços Azure por meio de uma linha de comando de PCs Windows. Alguns serviços ou recursos de serviço podem ser gerenciados apenas por meio de PowerShell ou CLI. Orientação para cada detalhes de recurso Azure individual que modelos de agrupamentos de um recurso pode ser implantada com.   
Veja também [como instalar e configurar o PowerShell do Azure](powershell-install-configure.md)


### <a name="arm-model"></a>Modelo de implantação do Azure Gerenciador de recursos  
Um dos dois [modelos de implantação](resource-manager-deployment-model.md) usadas para implantar recursos no Microsoft Azure (a outra é o modelo de implantação clássico). Alguns recursos Azure podem ser implantados em um modelo ou outro, enquanto outras pessoas podem ser implantadas em ambos os modelos. Orientação para detalhes de recursos Azure individuais que modelos de agrupamentos de um recurso pode ser implantada com.


### <a name="fault-domain"></a>domínio de falhas  
A coleção de máquinas virtuais em um conjunto de disponibilidade que possivelmente pode falhar ao mesmo tempo. Um exemplo é um grupo de máquinas em um rack que compartilham uma opção de origem e de rede de energia comuns. No Azure, as máquinas virtuais em um conjunto de disponibilidade são automaticamente separadas em vários domínios de falhas.  
Consulte também [Gerenciar a disponibilidade de máquinas virtuais Linux](./virtual-machines/virtual-machines-linux-manage-availability.md) ou [Gerenciar a disponibilidade de máquinas virtuais do Windows](./virtual-machines/virtual-machines-windows-manage-availability.md)  


### <a name="geo"></a>localização geográfica  
Um limite definido para residência de dados que geralmente contém dois ou mais regiões. Os limites podem estar dentro ou além fronteiras nacionais e são influenciados pela regulamentação do imposto. Cada geográfica tem pelo menos uma região. Exemplos de geos são Pacífico e Japão. Também chamado de *geografia*.  
Consulte também [regiões Azure](best-practices-availability-paired-regions.md)


### <a name="geo-replication"></a>replicação geográfica  
O processo de automaticamente replicar conteúdo como blobs, tabelas e filas dentro de um par regional.  
Consulte também [Replicação geográfica ativa do banco de dados do SQL Azure](./sql-database/sql-database-geo-replication-overview.md)


### <a name="image"></a>imagem  
Um arquivo que contém o sistema operacional e configuração do aplicativo que pode ser usada para criar qualquer número de máquinas virtuais. Há dois tipos de imagens no Azure: máquina virtual imagem e imagem de sistema operacional. Uma imagem de máquina virtual inclui um sistema operacional e todos os discos anexados a uma máquina virtual quando a imagem é criada. Uma imagem de SO contém apenas um sistema operacional generalizado com configurações de disco sem dados.  
Consulte também [navegar e selecionar imagens de máquina virtual do Windows no Azure com PowerShell ou CLI](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md)


### <a name="limits"></a>limites  
O número de recursos que podem ser criados ou o parâmetro de comparação de desempenho que pode ser obtido. Limites são geralmente associados com ofertas, serviços e assinaturas.  
Consulte também [assinatura do Azure e limites de serviço, cotas e restrições](azure-subscription-service-limits.md)


### <a name="load-balancer"></a>Balanceador de carga  
Um recurso que distribui o tráfego entre computadores em uma rede. No Azure, um balanceador de carga distribui o tráfego para máquinas virtuais definido em um conjunto de Balanceador de carga. Um [balanceador de carga](./load-balancer/load-balancer-overview.md) pode ser voltado para a internet, ou pode ser interno.  


### <a name="offer"></a>oferta  
Os preços, créditos e termos relacionados aplicáveis uma assinatura do Azure.  
Consulte o [Azure oferecem a página de detalhes](https://azure.microsoft.com/support/legal/offer-details/)


### <a name="portal"></a>Portal  
O portal seguro da Web usado para implantar e gerenciar serviços do Azure.  Há dois portais: O [portal do Azure](http://portal.azure.com/) e o [portal clássico](http://manage.windowsazure.com/). Alguns serviços estão disponíveis em ambos os portais, enquanto outros só estão disponíveis em um ou outro. As listas de [gráfico de disponibilidade de portal Azure](https://azure.microsoft.com/features/azure-portal/availability/) quais serviços estão disponíveis no qual portal.  


### <a name="region"></a>região  
Uma área dentro de uma localização geográfica que não bordas não cruzados nacionais e contém um ou mais dos data centers. Preços, serviços regionais e tipos de oferta são expostos no nível de região. Uma região geralmente é combinada com outra região, que pode ser até várias centenas milhas ausente, para formar um par regional. Pares regionais podem ser usados como um mecanismo de recuperação de dados e cenários de alta disponibilidade. Também denominados geralmente *local*.  
Consulte também [regiões Azure](best-practices-availability-paired-regions.md)


### <a name="resource"></a>recurso  
Um item que faz parte de sua solução Azure. Cada serviço Azure permite implantar diferentes tipos de recursos, como bancos de dados ou máquinas virtuais.   
Consulte também [Visão geral do Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md)


### <a name="resource-group"></a>grupo de recursos  
Um contêiner no Gerenciador de recursos que contém recursos relacionados para um aplicativo. O grupo de recursos pode incluir todos os recursos de um aplicativo ou apenas os recursos que estão agrupados logicamente. Você pode decidir como você deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização.  
Consulte também [Visão geral do Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md)


### <a name="arm-template"></a>Modelo do Gerenciador de recursos  
Um arquivo JSON que maneira declarativa define um ou mais recursos Azure e que define dependências entre os recursos implantados. O modelo pode ser usado para implantar os recursos de forma consistente e repetidamente.  
Consulte também [modelos de coautoria Gerenciador de recursos do Azure](resource-group-authoring-templates.md)


### <a name="resource-provider"></a>provedor de recursos  
Um serviço que fornece os recursos que você pode implantar e gerenciar através do Gerenciador de recursos. Cada provedor de recursos oferece operações para trabalhar com os recursos que são implantados. Provedores de recursos podem ser acessados através do portal do Azure, PowerShell do Azure e vários SDKs de programação.  
Consulte também [Visão geral do Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md)


### <a name="role"></a>função  
Um meio para controlar o acesso que pode ser atribuído a usuários, grupos e serviços. Funções são capazes de executar ações tais como criar, gerenciar e leia sobre os recursos do Azure.  
Consulte também [RBAC: funções internas](./active-directory/role-based-access-built-in-roles.md)


### <a name="sla"></a>contrato de nível de serviço (SLA)  
O contrato que descreve os compromissos da Microsoft para atividade e conectividade. Cada serviço Azure tem um SLA específico.  
Consulte também [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/)


### <a name="storage-account"></a>conta de armazenamento  
Uma conta de armazenamento que lhe acessem os serviços do Azure Blob, fila, tabela e arquivo no armazenamento do Azure. Sua conta de armazenamento fornece namespace exclusivo para os objetos de dados de armazenamento do Azure.  
Consulte também [contas de armazenamento do Azure sobre](./storage/storage-create-storage-account.md)


### <a name="subscription"></a>assinatura  
Contrato de um cliente com a Microsoft que permita obter serviços Azure. A assinatura preços e termos relacionados são controlados pela oferta escolhida para a assinatura. Consulte o [contrato de assinatura Online da Microsoft](https://azure.microsoft.com/support/legal/subscription-agreement/).  
Veja também [como Azure assinaturas são associadas com o Active Directory do Azure](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="tag"></a>marca  
Um termo de indexação que permite que você categorizar os recursos de acordo com suas necessidades de gerenciamento ou de cobrança. Você pode usar marcas quando você tem uma coleção complexa de grupos de recursos e os recursos, e você precisa para visualizar esses ativos da maneira que faz mais sentido. Por exemplo, você pode marcar recursos que servem uma função semelhante em sua organização ou pertencem ao mesmo departamento.  
Consulte também [usando marcas para organizar seus recursos Azure](resource-group-using-tags.md)


### <a name="update-domain"></a>domínio de atualização  
A coleção de máquinas virtuais em um conjunto de disponibilidade que estão atualizados ao mesmo tempo. Máquinas virtuais no mesmo domínio atualização são reiniciadas juntas durante a manutenção planejada. Azure nunca é reiniciado a mais de um domínio de atualização de cada vez. Também conhecido como um domínio de atualização.  
Consulte também [Gerenciar a disponibilidade de máquinas virtuais Linux](./virtual-machines/virtual-machines-linux-manage-availability.md) ou [Gerenciar a disponibilidade de máquinas virtuais do Windows](./virtual-machines/virtual-machines-windows-manage-availability.md)  


### <a name="vm"></a>máquina virtual  
A implementação de software de um computador físico que executa um sistema operacional. Várias máquinas virtuais podem executar simultaneamente no mesmo hardware. No Azure, máquinas virtuais estão disponíveis em uma variedade de tamanhos.  
Consulte também a [documentação de máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)


### <a name="vm-extension"></a>extensão de máquina virtual  
Um recurso que implementa comportamentos ou recursos que seja ajudam outros programas trabalhar ou fornecer a capacidade para interagir com um computador em execução. Por exemplo, você pode usar a extensão de acesso de máquina virtual para redefinir ou modificar valores de acesso remoto em uma máquina virtual Azure.  
Consulte também [sobre extensões de máquina virtual e recursos (Windows)](./virtual-machines/virtual-machines-windows-extensions-features.md) ou [sobre recursos (Linux) e extensões de máquina virtual](./virtual-machines/virtual-machines-linux-extensions-features.md)


### <a name="vnet"></a>rede virtual  
Uma rede que fornece conectividade entre seus recursos Azure que está isolada de todos os outros locatários Azure. Ele pode ser conectado a outras redes virtuais Azure através de um [Gateway de VPN do Azure](./vpn-gateway/vpn-gateway-about-vpngateways.md) e à sua rede local usando [várias opções](./vpn-gateway/vpn-gateway-cross-premises-options.md). Você pode controlar totalmente os blocos de endereço IP, as configurações DNS, políticas de segurança e tabelas de rota dentro dessa rede.  
Consulte também [Visão geral de rede Virtual](./virtual-network/virtual-networks-overview.md)  

###<a name="see-also"></a>**Consulte também**  
- [Introdução ao Azure](https://azure.microsoft.com/get-started/)
- [Central de recursos de nuvem](https://azure.microsoft.com/resources/)  
- [Azure para seu aplicativo de negócios](https://azure.microsoft.com/overview/business-apps-on-azure/)
- [Azure no seu data center](https://azure.microsoft.com/overview/business-apps-on-azure/) 





