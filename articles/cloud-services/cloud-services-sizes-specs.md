<properties
 pageTitle="Tamanhos para serviços de nuvem | Microsoft Azure"
 description="Lista os tamanhos de máquina virtual diferente (e IDs) para funções de web e trabalhador de serviço de nuvem Azure."
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="10/27/2016"
 ms.author="adegeo"/>

# <a name="sizes-for-cloud-services"></a>Tamanhos para serviços de nuvem

Este tópico descreve os tamanhos disponíveis e opções para instâncias de função de serviço de nuvem (funções da web e funções de trabalho). Ele também fornece considerações de implantação para ficar atento ao planejar usar esses recursos. Cada tamanho tem uma identificação que você colocará no seu [arquivo de definição de serviço](cloud-services-model-and-package.md#csdef).

Serviços de nuvem é um dos vários tipos de recursos de computação oferecidos pelo Azure. Clique [aqui](cloud-services-choose-me.md) para obter mais informações sobre os serviços de nuvem.

> [AZURE.NOTE]Para ver os limites de Azure relacionados, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md)

## <a name="sizes-for-web-and-worker-role-instances"></a>Tamanhos para web e trabalhador instâncias de função

Há vários tamanhos padrão para escolher no Azure. Considerações para alguns desses tamanhos incluem:

* VMs de série D foram projetadas para executar aplicativos que exigem maior capacidade de computação e desempenho de disco temporário. Série D VMs fornecem processadores mais rápidos, uma taxa de memória-to-core maior e uma unidade de estado sólido (SSD) para o disco temporário. Para obter detalhes, consulte o anúncio no blog do Azure, [Novos tamanhos de máquina Virtual série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

* Série de Dv2, uma de acompanhamento para a original série D, recursos de uma CPU mais eficazes. A CPU Dv2 série é aproximadamente 35% mais rápido que a CPU série D. Ele se baseia a última geração 2,4 GHz Intel Xeon® E5-2673 v3 processador (Haswell) e com o 2.0 de tecnologia de aumento de sensibilidade do Intel Turbo, pode ir até 3.1 GHz. A série de Dv2 tem as mesmas configurações de memória e disco como a série de D.

*   Série de G VMs oferecem a maioria da memória e executado em hosts que têm processadores família Intel Xeon E5 V3.

*   A série VMs podem ser implantadas em uma variedade de tipos de hardware e processadores. O tamanho é acelerado, com base em hardware, para oferecer o desempenho de processador consistente para a instância em execução, independentemente do hardware em que é implantado. Para determinar o hardware físico no qual esse tamanho é implantado, consulte o hardware virtual do dentro da máquina Virtual.

*   O tamanho A0 é assinado em excesso no hardware físico. Para este tamanho específico somente, outras implantações de cliente podem afetar o desempenho de sua carga de trabalho em execução. O desempenho relativo é descrito abaixo como a linha de base esperada, sujeitos a uma variabilidade aproximada de 15%.


O tamanho da máquina virtual afeta os preços. O tamanho também afeta a capacidade de processamento, memória e armazenamento da máquina virtual. Os custos de armazenamento são calculados separadamente com base nas páginas usadas na conta de armazenamento. Para obter detalhes, consulte [Detalhes de preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/) e [Preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). 


As considerações a seguir podem ajudá-lo a decidir sobre um tamanho:


* Os tamanhos A8 A11 e H série também conhecido como são *instâncias de pesados*. O hardware que executa esses tamanhos é projetado e otimizado para pesados e aplicativos de rede que requer muita, inclusive computação de alto desempenho (HPC) cluster simulações, modelagem e aplicativos. A série de A8 A11 usa Intel Xeon E5-2670 @ 2.6 GHZ e a série de H usa v3 Intel Xeon E5-2667 @ 3,2 GHz. Para obter informações detalhadas e considerações sobre como usar esses tamanhos, consulte [sobre A série VMs H série e pesados](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md). 

* Série Dv2, série D, G-série, são ideais para aplicativos que exigem CPUs mais rápidas, local melhor desempenho de disco ou tiver memória universo.  Eles oferecem uma combinação poderosa para vários aplicativos de nível empresarial.

*   Alguns dos hosts físicos em Azure data centers podem não suportar tamanhos maiores de máquina virtual, como A5 – A11. Como resultado, você poderá ver a mensagem de erro **Falha para configurar máquina virtual {nome da máquina}** ou **Falha ao criar máquina virtual {nome da máquina}** ao redimensionar uma máquina virtual existente para um novo tamanho; Criando uma nova máquina virtual em uma rede virtual criada antes de 16 de abril de 2013; ou adicionando uma nova máquina virtual a um serviço de nuvem existente. Consulte [erro: "Falha ao configurar máquina virtual"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) no Fórum de suporte para soluções alternativas para cada cenário de implantação.  

* Sua assinatura também pode limitar o número de cores que você pode implantar em certas famílias de tamanho. Para aumentar a cota, contate o suporte do Azure.


## <a name="performance-considerations"></a>Considerações sobre o desempenho

Nós criamos o conceito do Azure calcular unidade (ACU) para fornecer uma maneira de comparação de desempenho de computação (CPU) entre Azure SKUs. Isso ajudará você a identificar facilmente qual SKU é mais provável atender suas necessidades de desempenho.  ACU atualmente é padronizado em um pequeno (Standard_A1) máquina virtual sendo 100 todos os outros SKUs e, em seguida, representa aproximadamente quanto mais rápido que SKU pode executar uma referência padrão. 

>[AZURE.IMPORTANT] O ACU é apenas um guia.  Os resultados para sua carga de trabalho podem variar. 

<br>

|Família SKU |ACU/Core |
|---|---|
|[Standard_A0](#a-series)   |50 |
|[Standard_A1-4](#a-series) |100 |
|[Standard_A5-7](#a-series) |100 |
|[A8 A11](#a-series)    |225 *|
|[D1-14](#d-series) |160 |
|[15v2 D1](#dv2-series) |210 - 250 *|
|[G1-5](#g-series)  |180 - 240 *|
|[H](#h-series) |290 - 300 *|

ACUs marcados com um * da tecnologia Intel® Turbo para aumentar a frequência de CPU e fornecer um aumento de desempenho.  A quantidade do aumento de sensibilidade pode variar com base em outras cargas de trabalho em execução no mesmo host, carga de trabalho e o tamanho da máquina virtual.

## <a name="size-tables"></a>Dimensionar tabelas

As tabelas a seguir mostram os tamanhos e as capacidades que eles fornecem.

* Capacidade de armazenamento é mostrada em unidades de Chaveta ou 1024 ^ 3 bytes. Quando Comparando discos medido em GB (1000 ^ 3 bytes) para discos medidos em Chaveta (1024 ^ 3) lembre-se de que os números de capacidade fornecidos Chaveta podem parecer menores. Por exemplo, 1023 Chaveta = 1098.4 GB

* Taxa de transferência do disco é medida em operações de entrada/saída por segundo (IOPS) e MBps onde MBps = 10 ^ 6 bytes/seg.

* Discos de dados podem operar nos modos armazenados em cache ou não armazenados em cache. Operação de disco de dados armazenados em cache, o modo de cache de host está definido como **somente leitura** ou **ReadWrite**.  Para operação de disco de fora do cache de dados, o modo de cache de host é definido como **Nenhum**.

* Largura de banda de rede máxima é a agregado largura de banda máxima alocada e atribuídas por tipo de máquina virtual. A largura de banda máxima fornece orientação para selecionar o tipo certo de máquina virtual para garantir a capacidade de rede adequada está disponível. Ao mover entre baixa, moderada, alta e muito alto, a taxa de transferência aumentará de acordo. Desempenho de rede real dependerá muitos fatores incluindo rede e cargas de aplicativos e configurações de rede do aplicativo.


## <a name="a-series"></a>Uma série

| Tamanho        | Cores de CPU | Memória: Chaveta | Unidade de disco rígido local: Chaveta | Discos de dados do max | Taxa de transferência de disco de dados máx: IOPS | Max NICs / largura de banda de rede |
|-------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A0 | 1         | 0.768        | 20                    | 1              | 1 x 500              | 1 / baixa                   |
| Standard_A1 | 1         | 1,75         | 70                    | 2              | 2 x 500              | 1 / moderado              |
| Standard_A2 | 2         | 3,5 GB       | 135                   | 4              | 4 x 500              | 1 / moderado              |
| Standard_A3 | 4         | 7            | 285                   | 8              | 8 x 500              | 2 / alto                  |
| Standard_A4 | 8         | 14           | 605                   | 16             | 16 x 500             | 4 / alto                  |
| Standard_A5 | 2         | 14           | 135                   | 4              | 4 X 500              | 1 / moderado              |
| Standard_A6 | 4         | 28           | 285                   | 8              | 8 x 500              | 2 / alto                  |
| Standard_A7 | 8         | 56           | 605                   | 16             | 16 x 500             | 4 / alto                  |

## <a name="a-series---compute-intensive-instances"></a>A série-pesados instâncias

Para obter informações e as considerações sobre como usar esses tamanhos, consulte [sobre A série VMs H série e pesados](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).


| Tamanho         | Cores de CPU | Memória: Chaveta | Unidade de disco rígido local: Chaveta | Discos de dados do max | Taxa de transferência de disco de dados máx: IOPS | Max NICs / largura de banda de rede |
|--------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A8 * | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / alto                  |
| Standard_A9 * | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / muito alto             |
| Standard_A10 | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / alto                  |
| Standard_A11 | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / muito alto             |

* RDMA capaz

## <a name="d-series"></a>Série D


| Tamanho         | Cores de CPU | Memória: Chaveta | SSD local: Chaveta | Discos de dados do max | Taxa de transferência de disco de dados máx: IOPS | Max NICs / largura de banda de rede |
|--------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / moderado              |
| Standard_D2  | 2         | 7            | 100                  | 4              | 4 x 500              | 2 / alto                  |
| Standard_D3  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / alto                  |
| Standard_D4  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / alto                  |
| Standard_D11 | 2         | 14           | 100                  | 4              | 4 x 500              | 2 / alto                  |
| Standard_D12 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / alto                  |
| Standard_D13 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / alto                  |
| Standard_D14 | 16        | 112          | 800                  | 32             | 32 x 500             | 8 / muito alto             |

## <a name="dv2-series"></a>Série de Dv2

| Tamanho            | Cores de CPU | Memória: Chaveta | SSD local: Chaveta | Discos de dados do max | Taxa de transferência de disco de dados máx: IOPS | Max NICs / largura de banda de rede |
|-----------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1_v2  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / moderado              |
| Standard_D2_v2  | 2         | 7            | 100                  | 4              | 4 x 500              | 2 / alto                  |
| Standard_D3_v2  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / alto                  |
| Standard_D4_v2  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / alto                  |
| Standard_D5_v2  | 16        | 56           | 800                  | 32             | 32 x 500             | 8 / muito alta        |
| Standard_D11_v2 | 2         | 14           | 100                  | 4              | 4 x 500              | 2 / alto                  |
| Standard_D12_v2 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / alto                  |
| Standard_D13_v2 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / alto                  |
| Standard_D14_v2 | 16        | 112          | 800                  | 32             | 32 x 500             | 8 / muito alta        |
| Standard_D15_v2 | 20        | 140          | 1.000                | 40             | 40 x 500             | 8 / muito alta        |

## <a name="g-series"></a>Série de G

| Tamanho        | Cores de CPU | Memória: Chaveta  | SSD local: Chaveta  | Discos de dados do max | Taxa de transferência de disco máx: IOPS | Max NICs / largura de banda de rede |
|-------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_G1 | 2         | 28           | 384                  | 4              | 4 x 500            | 1 / alto                  |
| Standard_G2 | 4         | 56           | 768                  | 8              | 8 x 500            | 2 / alto                  |
| Standard_G3 | 8         | 112          | 1,536                | 16             | 16 x 500           | 4 / muito alto             |
| Standard_G4 | 16        | 224          | 3.072                | 32             | 32 x 500           | 8 / muito alta        |
| Standard_G5 | 32        | 448          | 6,144                | 64             | 64 x 500           | 8 / muito alta        |


## <a name="h-series"></a>Série de H

Azure máquinas virtuais de série H são o próxima geração computação de alto desempenho que VMs voltada para necessidades de computação final alta, como modelagem molecular e fluidos computacional. Esses 8 e 16 core VMs são criadas na tecnologia de processador Intel Haswell E5 2667 V3 apresentando DDR4 memória e armazenamento SSD com base local. 

Além de energia da CPU substancial, a série de H oferece diversas opções para redes de RDMA de baixa latência usando FDR InfiniBand e várias configurações de memória para dar suporte a requisitos de computação intenso de memória.


| Tamanho           | Cores de CPU | Memória: Chaveta | SSD local: Chaveta | Discos de dados do max | Taxa de transferência de disco máx: IOPS | Max NICs / largura de banda de rede |
|----------------|-----------|-------------|--------------------------|----------------|---------------------------|------------------------------|
| Standard_H8    | 8         | 56          | 1000                     | 16             | 16 x 500                    | 8 / alto                      |
| Standard_H16   | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / muito alto                  |
| Standard_H8m   | 8         | 112         | 1000                     | 16             | 16 x 500                    | 8 / alto                      |
| Standard_H16m  | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / muito alto                 |
| Standard_H16r * | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / muito alto                  |
| Standard_H16mr * | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / muito alto                  |


* RDMA capaz

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>Observações: Padrão A0 - A4 usando CLI e PowerShell 

No modelo clássico de implantação, alguns nomes de tamanho de máquina virtual são ligeiramente diferentes em CLI e PowerShell:

* Standard_A0 é ExtraSmall 
* Standard_A1 é pequeno
* Standard_A2 é médio
* Standard_A3 é grande
* Standard_A4 é ExtraLarge

## <a name="configure-sizes-for-cloud-services"></a>Configurar tamanhos para serviços de nuvem

Você pode especificar o tamanho de máquina Virtual de uma instância da função como parte do modelo de serviço descrito pelo [arquivo de definição de serviço](cloud-services-model-and-package.md#csdef). O tamanho da função determina o número de cores, a capacidade de memória e o tamanho do sistema de arquivo local que está alocado para uma instância de CPU. Escolha o tamanho de função com base em requisitos de recursos do seu aplicativo.

Aqui está um exemplo de configuração do tamanho de função sejam [Standard_D2](#general-purpose-d) para uma instância de função da Web:

```xml
<WorkerRole name="Worker1" vmsize="<mark>Standard_D2</mark>">
...
</WorkerRole>
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [assinatura do azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Saiba mais [sobre as VMs de série de uma série H e pesados](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) para cargas de trabalho como alto desempenho HPC (computação).

