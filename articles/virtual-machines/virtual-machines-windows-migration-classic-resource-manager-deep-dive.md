<properties
    pageTitle="Técnico detalhamento da plataforma suportada migração de clássico ao Gerenciador de recursos do Azure | Microsoft Azure"
    description="Este artigo significa técnico detalhamento da plataforma suportada migração de recursos do clássico ao Gerenciador de recursos do Azure"
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
    ms.date="08/22/2016"
    ms.author="kasing"/>

# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Técnico detalhamento da plataforma suportada migração de clássico ao Gerenciador de recursos do Azure
Vamos dar uma análise aprofundada sobre migração de modelo de implantação clássico Azure para o modelo de implantação do Gerenciador de recursos do Azure. Vamos examinar recursos em um nível de recurso e de recursos para ajudá-lo a entender como a plataforma Windows Azure migra recursos entre os modelos de implantação de duas. Para obter mais informações, leia o artigo de lançamento do serviço: [plataforma suportada migração de recursos de IaaS do clássico ao Gerenciador de recursos do Azure](virtual-machines-windows-migration-classic-resource-manager.md).

## <a name="detailed-guidance-on-migration"></a>Orientações detalhadas sobre migração

Você pode encontrar o clássico e representações de gerente de recursos dos recursos da tabela a seguir. Outros recursos e recursos não são suportados no momento.

| Representação clássica                                 | Representação do Gerenciador de recursos                 | Anotações detalhadas                                                                                                                                                                                                                                                                                                                                                                                                                                                      
|--------------------------------------------------------|-------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome do serviço de nuvem                                     | Nome DNS                                        | Durante a migração, um novo grupo de recursos é criado para cada serviço de nuvem com o padrão de nomenclatura `<cloudservicename>-migrated`. Este grupo de recursos contém todos os seus recursos. O nome do serviço de nuvem torna-se um nome DNS que está associado com o endereço IP público.                                                                                                                                                                                                    |   
| Máquina virtual                                        | Máquina virtual                                 | Propriedades específicas de máquina virtual são migradas inalterado. Determinadas informações de osProfile, como o nome do computador, não são armazenadas no modelo clássico de implantação e permanecem vazias após a migração.                                                                                                                                                                                                                                                                |   
| Recursos de disco anexados para máquina virtual                          | Implícitos discos anexados para máquina virtual                   | Discos não são modelados como recursos de nível superior no modelo de implantação do Gerenciador de recursos. Eles são migrados como discos implícitos sob a máquina virtual. Somente os volumes que estejam anexados a uma máquina virtual são aceitos atualmente. Gerenciador de recursos VMs agora pode usar contas de armazenamento clássico, que permite que os discos sejam facilmente migrados sem todas as atualizações. |   
| Extensões de máquina virtual                                          | Extensões de máquina virtual                                   | Todas as extensões de recurso, exceto extensões XML, são migradas do modelo clássico de implantação.                                                                                                                                                                                                                                                                                                                                                                        |   
| Certificados de máquina virtual                           | Certificados no Azure cofre chave                 | Se um serviço de nuvem contém certificados de serviço, um novo cofre chave Azure por serviço de nuvem e move os certificados para o cofre chave. VMs são atualizadas para fazer referência os certificados de chave cofre.                                                                                                                                                                                                                               |  
| Configuração de WinRM                                    | Configuração de WinRM em osProfile             | Windows configuração do gerenciamento remoto é movida inalterada, como parte da migração.                                                                                                                                                                                                                                                                                                                                                                                            |   
| Propriedade do conjunto de disponibilidade                              | Recurso de conjunto de disponibilidade                       | Especificação de conjunto de disponibilidade era uma propriedade na VM no modelo clássico de implantação. Conjuntos de disponibilidade tornam-se um recurso de nível superior como parte da migração. Não há suporte para as seguintes configurações: vários conjuntos de disponibilidade por serviço de nuvem, ou um ou mais disponibilidade juntamente com VMs que não estão em nenhuma disponibilidade conjunto em um serviço na nuvem.                                                                                |   
| Configuração de rede em uma máquina virtual                          | Interface de rede principal                       | Configuração de rede em uma máquina virtual é representada como o recurso de interface de rede principal após a migração. Para VMs que não estão em uma rede virtual, o endereço IP interno altera durante a migração.                                                                                                                                                                                                                                                            |   
| Várias interfaces de rede em uma máquina virtual                    | Interfaces de rede                              | Se uma máquina virtual tem várias interfaces de rede associadas a ele, cada interface de rede torna-se um recurso de nível superior como parte da migração no modelo de implantação do Gerenciador de recursos, juntamente com todas as propriedades.                                                                                                                                                                                                                                                            |  
| Conjunto de ponto de extremidade de balanceamento de carga                             | Balanceador de carga                                   | No modelo clássico de implantação, a plataforma atribuído um balanceador de carga implícitos para cada serviço de nuvem. Durante a migração, um novo recurso de Balanceador de carga é criado e o conjunto de ponto de extremidade de balanceamento de carga se torna regras de Balanceador de carga.                                                                                                                                                                                                                                     |   
| Regras de NAT de entrada                                      | Regras de NAT de entrada                               | Pontos de extremidade de entrada definidos na máquina virtual são convertidos em regras de conversão de endereço de rede de entrada em balanceador de carga durante a migração.                                                                                                                                                                                                                                                                                                                                           |  
| Endereço de VIP                              | Endereço IP público com nome DNS                 | O endereço IP virtual se torna um endereço IP público e está associado com o balanceador de carga.                                                                                                                                                                                                                                                                                                                                                        |   
| Rede virtual                                        | Rede virtual                                 | A rede virtual é migrada, com todas as propriedades, para o modelo de implantação do Gerenciador de recursos. Um novo grupo de recursos é criado com o nome `-migrated`. Há [configurações sem suporte](virtual-machines-windows-migration-classic-resource-manager.md).                                                                                                                                                                                                  |   
| IPs reservadas                                           | Endereço IP público com o método de alocação estática  | IPs reservadas associada ao balanceador de carga são migradas, juntamente com a migração de serviço de nuvem ou a máquina virtual. Atualmente não há suporte para a migração de IP reservada não associada.                                                                                                                                                                                                                                                           |   
| Endereço IP público por máquina virtual                               | Endereço IP público com o método de alocação dinâmica | O endereço IP público associado com a máquina virtual é convertido como um recurso de endereço IP público, com o método de alocação definido como estático.                                                                                                                                                                                                                                                                                                                                   |   
| NSGs                                | NSGs                         | Grupos de segurança de rede associados a uma sub-rede são clonar como parte da migração para o modelo de implantação do Gerenciador de recursos. O NSG no modelo clássico de implantação não é removido durante a migração. No entanto, as operações de plano de gerenciamento para o NSG são bloqueadas quando a migração está em andamento.                                                                                                                                                                             |  
| Servidores DNS                                            | Servidores DNS                                     | Servidores DNS associados a uma rede virtual ou a máquina virtual são migrados como parte da migração recurso correspondente, juntamente com todas as propriedades.                                                                                                                                                                                                                                                                                                                    |   
| UDRs                                    | UDRs                             | Rotas definidas pelo usuário associadas a uma sub-rede são clonar como parte da migração para o modelo de implantação do Gerenciador de recursos. O UDR no modelo clássico de implantação não é removido durante a migração. As operações de plano de gerenciamento para o UDR são bloqueadas quando a migração está em andamento.                                                                                                                                                                             |   
| Encaminhamento de propriedade na configuração de rede de uma máquina virtual IP | Propriedade no NIC de encaminhamento de IP               | A propriedade em uma máquina virtual de encaminhamento de IP é convertida em uma propriedade na interface de rede durante a migração.                                                                                                                                                                                                                                                                                                                                                           |   
| Balanceador de carga com vários IPs                        | Balanceador de carga com vários recursos IP públicos | Cada IP público associado ao balanceador de carga é convertido em um recurso IP público e associado ao balanceador de carga após a migração.                                                                                                                                                                                                                                                                                                       |   
| Nomes DNS internos na VM                           | Nomes DNS internos no NIC                    | Durante a migração, os sufixos DNS internos para as VMs são migrados para uma propriedade de somente leitura chamada "InternalDomainNameSuffix" no NIC. O sufixo permanece inalterado após a migração e resolução de máquina virtual deve continuar a trabalhar como anteriormente.                                                                                                                                                                                                           |   

## <a name="illustration-of-a-simple-migration-walkthrough"></a>Ilustração de um passo a passo de migração simples

A captura de tela a seguir mostra um serviço de nuvem existente com uma máquina virtual (não em uma rede virtual) após a fase de preparação:

![Representação clássica Após preparar](./media/virtual-machines-windows-migration-classic-resource-manager/classic-migration-prepare-portal.png)

Depois de concluir o processo de migração, as capturas de tela a seguintes mostram que os novos recursos foram criados em um novo grupo de recursos: ![representação do Gerenciador de recursos após preparar](./media/virtual-machines-windows-migration-classic-resource-manager/resourcemanager-migration-prepare-portal.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você entende a migração de recursos de IaaS clássicos para o Gerenciador de recursos, você pode iniciar a migração de recursos.

- [Usar o PowerShell para migrar IaaS recursos do clássico ao Gerenciador de recursos do Azure](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Use CLI para migrar IaaS recursos do clássico ao Gerenciador de recursos do Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Suporte a plataforma migração de recursos de IaaS do clássico para o Gerenciador de recursos do Azure](virtual-machines-windows-migration-classic-resource-manager.md)
- [Clonar uma máquina virtual clássica ao Gerenciador de recursos do Azure usando scripts do PowerShell de comunidade](virtual-machines-windows-migration-scripts.md)