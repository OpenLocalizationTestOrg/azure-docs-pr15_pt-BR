<properties
    pageTitle="Criar várias máquinas virtuais | Microsoft Azure"
    description="Opções para criar várias máquinas virtuais no Windows"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="guybo"/>

# <a name="create-multiple-azure-virtual-machines"></a>Criar várias máquinas virtuais Azure

Existem muitos cenários em que você precisa criar um grande número de semelhantes máquinas virtuais (VMs). Alguns exemplos incluem computação de alto desempenho (HPC), análise de dados em grande escala, flexível e muitas vezes sem estado intermediário ou back-end servidores (como servidores Web) e bancos de dados distribuídos.

Este artigo descreve as opções disponíveis para criar várias VMs no Azure. Essas opções ir além do simples casos em que você criar manualmente uma série de VMs. Para criar várias VMs, os processos que você usa normalmente não dimensionar bem quando você precisa criar mais de poucos VMs.

Uma maneira de criar várias VMs semelhantes é usar o Gerenciador de recursos do Azure construção de _loops de recurso_.

## <a name="resource-loops"></a>Loops de recurso

Loops de recurso são abreviação sintática nos modelos do Gerenciador de recursos do Azure. Recurso loops podem criar um conjunto de recursos configurados da mesma forma em um loop. Você pode usar o recurso loops para criar várias contas de armazenamento, interfaces de rede ou máquinas virtuais. Para obter mais informações sobre o recurso loops, consulte [criar VMs na disponibilidade define usando loops de recurso](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/).

## <a name="challenges-of-scale"></a>Desafios de escala

Embora o recurso loops facilitam construir uma infraestrutura de nuvem em escala e produzir modelos mais concisos, determinados desafios permanecem. Por exemplo, se você usar um loop do recurso para criar 100 máquinas virtuais, você precisa correlação controladores de interface de rede (NICs) com contas de armazenamento e VMs correspondentes. Como o número de VMs é provavelmente será diferente do número de contas de armazenamento, você terá de lidar com tamanhos de loop do recurso diferente, também. Esses são problemas resolvidos, mas a complexidade aumenta significativamente com escala.

Outro desafio ocorre quando você precisa de uma infraestrutura que se expande consumidor. Por exemplo, você pode querer uma infraestrutura de escala automática que automaticamente aumenta ou diminui o número de VMs em resposta a carga de trabalho. VMs não fornecem um mecanismo integrado para variar em número (escala de check-out e escala em). Se você dimensionar em removendo VMs, é difícil garantir alta disponibilidade, garantindo que VMs são balanceadas entre domínios e falhas de atualização.

Finalmente, quando você usa um loop de recursos, várias chamadas para criar recursos acessem a estrutura subjacente. Quando várias chamadas criar recursos semelhantes, Azure tem uma oportunidade implícita aprimoram este design e otimizar desempenho e confiabilidade de implantação. Isso é onde entram _escala de máquina virtual define_ .

## <a name="virtual-machine-scale-sets"></a>Conjuntos de escala de máquina virtual

Os conjuntos de escala de máquina virtual são um recurso de computação do Azure para implantar e gerenciar um conjunto de VMs idênticos. Com todas as VMs configurado a mesma, escala de máquina virtual conjuntos são fáceis de dimensionar no e dimensionar. Você simplesmente alterar o número de VMs no conjunto. Você também pode configurar conjuntos de escala de máquina virtual para escala automática com base nas exigências da carga de trabalho.

Para aplicativos que precisam expandir recursos de computação in e check-out, operações de escala implicitamente são equilibradas falhas e atualização de domínios.

Em vez de correlação vários recursos como placas de rede e VMs, um conjunto de escala de máquina virtual tem rede, armazenamento, máquina virtual e propriedades de extensão que podem ser configuradas centralmente.

Para uma introdução a conjuntos de escala de máquina virtual, consulte a [escala de máquina Virtual define a página do produto](https://azure.microsoft.com/services/virtual-machine-scale-sets/). Para obter informações mais detalhadas, vá para a [escala de máquinas virtuais define documentação](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/).
