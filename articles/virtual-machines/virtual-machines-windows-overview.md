<properties
    pageTitle="Visão geral de máquinas virtuais do Windows | Microsoft Azure"
    description="Saiba mais sobre a criação e gerenciamento de máquinas virtuais do Windows no Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="davidmu"/>

# <a name="overview-of-windows-virtual-machines-in-azure"></a>Visão geral de máquinas virtuais do Windows no Azure

Azure máquinas virtuais (VM) é um dos vários tipos de [sob demanda, recursos de computação scalable](../app-service-web/choose-web-site-cloud-service-vm.md) que Azure oferece. Normalmente, você escolher uma máquina virtual quando precisar de mais controle sobre o ambiente de computação do que as outras opções oferecem. Este artigo fornece informações sobre o que você deve considerar antes de criar uma máquina virtual, como criá-lo e como você gerenciá-lo.

Uma máquina virtual do Azure fornece a flexibilidade da virtualização sem precisar comprar e manter o hardware físico que executa. No entanto, ainda é necessário manter a máquina virtual executando tarefas, como configurar, patches e instalar o software executado nele.

Azure máquinas virtuais podem ser usadas de várias maneiras. Alguns exemplos são:

- **Desenvolvimento e teste** – VMs Azure oferecem uma rápida e fácil para criar um computador com configurações específicas necessárias para código e testar um aplicativo.
- **Aplicativos na nuvem** – porque a demanda por seu aplicativo pode flutuar, ele pode fazer sentido econômico executá-lo em uma máquina virtual no Azure. Você paga VMs extras quando precisá-los e fechá-los quando não estiver.
- **Data Center estendido** – máquinas virtuais em uma rede virtual Azure facilmente pode estar conectado à rede da sua organização.

O número de VMs que o aplicativo usa pode dimensionar para cima e para fora para tudo o que é necessário para atender às suas necessidades.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>O que eu preciso pensar antes de criar uma máquina virtual?

Sempre há uma infinidade de [Considerações de design](virtual-machines-windows-infrastructure-virtual-machine-guidelines.md) quando você cria uma infraestrutura de aplicativo no Azure. Esses aspectos de uma máquina virtual são importantes pensar antes de começar:

- Os nomes dos seus recursos de aplicativo
- O local onde os recursos são armazenados
- O tamanho da máquina virtual
- O número máximo de VMs que podem ser criados
- O sistema operacional que a máquina virtual é executado
- A configuração da máquina virtual depois que ele for iniciado
- Os recursos relacionados que precisa ser a máquina virtual

### <a name="naming"></a>Nomeando

Uma máquina virtual tem um [nome](virtual-machines-windows-infrastructure-naming-guidelines.md) atribuído a ela e tem um nome de computador configurado como parte do sistema operacional. O nome de uma máquina virtual pode ter até 15 caracteres.

Se você usa o Azure para criar o disco de sistema operacional, o nome do computador e o nome de máquina virtual são os mesmos. Se você [carregar e usar sua própria imagem](virtual-machines-windows-upload-image.md) que contém um sistema operacional configurado anteriormente e usá-lo para criar uma máquina virtual, os nomes podem ser diferentes. Recomendamos que, quando você carrega seu próprio arquivo de imagem, que você faça o nome do computador no sistema operacional e o nome de máquina virtual a mesma.

### <a name="locations"></a>Locais

Todos os recursos criados no Azure são distribuídos entre várias [regiões geográficas](https://azure.microsoft.com/regions/) em todo o mundo. Geralmente, a região é chamada **local** quando você cria uma máquina virtual. Para uma máquina virtual, o local Especifica onde os discos virtuais estão armazenados.

Esta tabela mostra algumas das maneiras que você pode obter uma lista de locais disponíveis.

| Método | Descrição |
| ------ | ----------- |
| Portal do Azure | Selecione um local na lista quando você cria uma máquina virtual. |
| PowerShell Azure | Use o comando [Get-AzureRmLocation](https://msdn.microsoft.com/library/mt619449.aspx) . |
| API REST | Use a operação de [locais da lista](https://msdn.microsoft.com/library/dn790540.aspx) . |

### <a name="vm-size"></a>Tamanho de máquina virtual

O [tamanho](virtual-machines-windows-sizes.md) da máquina virtual que você usa é determinado pela carga de trabalho que você deseja executar. O tamanho que você escolher, em seguida, determina fatores como capacidade de energia, memória e armazenamento de processamento. Azure oferece uma ampla variedade de tamanhos para dar suporte a muitos tipos de usos.

Azure cobra um [preço por hora](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) baseado em tamanho e o sistema operacional a máquina virtual. Para horas parciais, encargos Azure apenas para os minutos usados. Armazenamento é o preço e cobrado separadamente.

### <a name="vm-limits"></a>Limites de máquina virtual

Sua assinatura tem padrão [limites de cota](../azure-subscription-service-limits.md) no local que poderia afetar a implantação de várias VMs para seu projeto. O limite atual em uma base por inscrição é 20 VMs por região. Limites podem ser elevados arquivando um tíquete solicitando um aumento.

### <a name="operating-system-disks-and-images"></a>Imagens e discos do sistema operacional

Máquinas virtuais use [discos virtuais (VHDs)](virtual-machines-windows-about-disks-vhds.md) para armazenar seus dados e o sistema operacional (OS). VHDs também são usadas para as imagens que você pode escolher entre instalar um sistema operacional. 

Azure fornece muitas [imagens do marketplace](https://azure.microsoft.com/marketplace/virtual-machines/) para usar com várias versões e tipos de sistemas operacionais Windows Server. Imagens do Marketplace são identificadas por fornecedor de imagem, oferta, sku e versão (normalmente versão for especificada como mais recente). 

Esta tabela mostra algumas maneiras que você pode encontrar as informações para uma imagem.

| Método | Descrição |
| ------ | ----------- |
| Portal do Azure | Os valores são especificados automaticamente para você quando você selecionar uma imagem para usar. |
| PowerShell Azure | [Get-AzureRMVMImagePublisher](https://msdn.microsoft.com/library/mt603484.aspx) -local "local"<BR>[Get-AzureRMVMImageOffer](https://msdn.microsoft.com/library/mt603824.aspx) -local "local"-"publisherName" do Publisher<BR>[Get-AzureRMVMImageSku](https://msdn.microsoft.com/library/mt619458.aspx) -local "local"-Publisher "publisherName"-oferecer "offerName" |
| APIs REST | [Fornecedores de imagem de lista](https://msdn.microsoft.com/library/mt743702.aspx)<BR>[Imagem da lista oferece](https://msdn.microsoft.com/library/mt743700.aspx)<BR>[Lista skus de imagem](https://msdn.microsoft.com/library/mt743701.aspx) |

Você pode optar por [carregar e usar sua própria imagem](virtual-machines-windows-upload-image.md) e quando você fizer, o nome do fornecedor, oferta e sku não são usados.

### <a name="extensions"></a>Extensões

Máquina virtual [extensões](virtual-machines-windows-extensions-features.md) dê seus recursos adicionais de máquina virtual por meio de configuração de implantação de postagem e tarefas automatizadas.

Essas tarefas comuns podem ser realizadas usando extensões:

- **Executar scripts personalizados** – a [Extensão de Script personalizado](virtual-machines-windows-extensions-customscript.md) ajuda você a configurar cargas de trabalho na máquina virtual executando o script quando a máquina virtual está provisionada.
- **Implantar e gerenciar configurações de** – a [extensão de configuração de estado de desejado (DSC) do PowerShell](virtual-machines-windows-extensions-dsc-overview.md) ajuda você a configurar DSC em uma máquina virtual para gerenciar configurações e ambientes.
- **Coletar dados de diagnóstico** – a [Extensão de diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) ajuda você a configurar a máquina virtual para coletar dados de diagnóstico que podem ser usados para monitorar a integridade do seu aplicativo.

### <a name="related-resources"></a>Recursos relacionados

Os recursos nesta tabela são usados pela máquina virtual e precisam existir ou ser criada quando a máquina virtual é criada.

| Recurso | Necessário | Descrição |
| -------- | -------- | ----------- |
| [Grupo de recursos](../azure-resource-manager/resource-group-overview.md) | Sim | A máquina virtual deve estar contida em um grupo de recursos. |
| [Conta de armazenamento](../storage/storage-create-storage-account.md) | Sim | A máquina virtual precisa a conta de armazenamento para armazenar seus discos virtuais. |
| [Rede virtual](../virtual-network/virtual-networks-overview.md) | Sim | A máquina virtual deve ser um membro de uma rede virtual. |
| [Endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) | Não | A máquina virtual pode ter um endereço IP público atribuído remotamente acessá-lo. |
| [Interface de rede](../virtual-network/virtual-network-network-interface-overview.md) | Sim | A máquina virtual precisa a interface de rede para se comunicar na rede. |
| [Discos de dados](virtual-machines-windows-attach-disk-portal.md) | Não | A máquina virtual pode incluir discos de dados para expandir os recursos de armazenamento. |

## <a name="how-do-i-create-my-first-vm"></a>Como posso criar minha máquina virtual primeiro?

Você tem várias opções para a criação de sua máquina virtual. A opção que você escolher depende do ambiente que você estiver usando. 

Esta tabela fornece informações para começar a criar sua máquina virtual.

| Método | Artigo |
| ------ | ------- |
| Portal do Azure | [Criar uma máquina virtual executando o Windows usando o portal](virtual-machines-windows-hero-tutorial.md) |
| Modelos | [Criar uma máquina virtual Windows com um modelo do Gerenciador de recursos](virtual-machines-windows-ps-template.md) |
| PowerShell Azure | [Criar uma máquina de virtual do Windows usando o PowerShell](virtual-machines-windows-ps-create.md) |
| SDKs de cliente | [Implantar recursos Azure usando c#](virtual-machines-windows-csharp.md) |
| APIs REST | [Criar ou atualizar uma máquina virtual](https://msdn.microsoft.com/library/mt163591.aspx) |

Você espero que ele acontece nunca, mas ocasionalmente algo errado. Se essa situação acontecer para você, examine as informações no [Gerenciador de recursos de solucionar problemas de implantação com a criação de uma máquina virtual Windows Azure](virtual-machines-windows-troubleshoot-deployment-new-vm.md).

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Como para gerenciar a máquina virtual que criei?

VMs podem ser gerenciadas usando um portal baseado em navegador, ferramentas de linha de comando com suporte para execução de scripts, ou diretamente por meio de APIs. Algumas tarefas de gerenciamento típicas que você pode realizar estão obtendo informações sobre uma máquina virtual, fazer logon em uma máquina virtual, gerenciar a disponibilidade e fazendo backups.

### <a name="get-information-about-a-vm"></a>Obter informações sobre uma máquina virtual

Esta tabela mostra algumas das maneiras que você pode obter informações sobre uma máquina virtual.

| Método | Descrição |
| ------ | ----------- |
| Portal do Azure | No menu hub, clique em **máquinas virtuais** e, em seguida, selecione a máquina virtual na lista. Na lâmina para a máquina virtual, você tem acesso a informações de visão geral, definindo valores e monitorando métricas. |
| PowerShell Azure | Para obter informações sobre como usar o PowerShell para gerenciar VMs, consulte [máquinas virtuais do Azure gerenciar usando o Gerenciador de recursos e PowerShell](virtual-machines-windows-ps-manage.md). |
| API REST | Use a operação de [máquina virtual obtenha informações](https://msdn.microsoft.com/library/mt163682.aspx) para obter informações sobre uma máquina virtual. |
| SDKs de cliente | Para obter informações sobre como usar c# para gerenciar VMs, consulte [máquinas virtuais do Azure gerenciar usando o Gerenciador de recursos do Azure e c#](virtual-machines-windows-csharp-manage.md). |

### <a name="log-on-to-the-vm"></a>Faça logon na máquina virtual

Use o botão conectar no portal do Azure para [Iniciar uma sessão de área de trabalho remota (RDP)](virtual-machines-windows-connect-logon.md). Coisas às vezes, podem dar errado ao tentar usar uma conexão remota. Se essa situação ocorre para você, consulte as informações de Ajuda em [conexões de solucionar problemas de área de trabalho remota para uma máquina virtual Azure executando o Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

### <a name="manage-availability"></a>Gerenciar a disponibilidade

É importante entender como [garantir alta disponibilidade](virtual-machines-windows-manage-availability.md) para seu aplicativo. Essa configuração envolve a criação de várias VMs para garantir que pelo menos uma está sendo executado.

Em ordem para a sua implantação qualificar para nosso 99.95 contrato de nível de serviço de máquina virtual, você precisa implantar dois ou mais VMs executando sua carga de trabalho dentro de um [conjunto de disponibilidade](virtual-machines-windows-infrastructure-availability-sets-guidelines.md). Essa configuração garante suas VMs são distribuídos em vários domínios de falhas e são implantadas em hosts com janelas de manutenção diferentes. O total [Do Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) explica a disponibilidade de garantia do Azure como um todo.
 
### <a name="back-up-the-vm"></a>Fazer backup a máquina virtual
 
Um [cofre de serviços de recuperação](../backup/backup-introduction-to-azure-backup.md) é usado para proteger dados e ativos nos serviços do Azure Backup e recuperação de Site do Azure. Você pode usar um cofre de serviços de recuperação para [implantar e gerenciar backups para VMs implantado Gerenciador de recursos usando o PowerShell](../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Próximas etapas

- Se sua intenção for trabalhar com Linux VMs, examine [Azure e Linux](virtual-machines-linux-azure-overview.md).
- Saiba mais sobre as diretrizes ao redor de configuração de sua infraestrutura na [explicação passo a passo de infraestrutura do Azure de exemplo](virtual-machines-windows-infrastructure-example.md).
- Certifique-se de que você segue as [Práticas recomendadas para execução de uma máquina de virtual do Windows no Azure](virtual-machines-windows-guidance-compute-single-vm.md).


