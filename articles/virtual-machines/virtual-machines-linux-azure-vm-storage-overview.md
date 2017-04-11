<properties
  pageTitle="Azure e armazenamento de máquina virtual Linux | Microsoft Azure"
  description="Descreve o armazenamento de Premium e padrão do Azure com máquinas virtuais Linux."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines-linux"
  authors="vlivech"
  manager="timlt"
  editor=""/>

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="NA"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure"
  ms.date="10/04/2016"
  ms.author="v-livech"/>

# <a name="azure-and-linux-vm-storage"></a>Armazenamento do Azure e Linux VM

Armazenamento do Azure é a solução de armazenamento de nuvem para aplicativos modernos que contam com durabilidade, disponibilidade e escalabilidade para atender às necessidades dos seus clientes.  Além de tornando possível para os desenvolvedores criem aplicativos de grande escala para dar suporte a novos cenários, armazenamento do Azure também fornece a base de armazenamento para máquinas virtuais do Azure.

## <a name="azure-storage-standard-and-premium"></a>Armazenamento do Azure: Standard e Premium

Azure máquina virtual pode ser construído discos de armazenamento padrão ou discos de armazenamento de premium.  Ao usar o Portal para escolher sua máquina virtual, você deve ativar uma lista suspensa na tela Noções básicas para exibir discos standard e premium.  Captura de tela abaixo realça esse menu do botão de alternância.  Quando estiver alternado para SSD, somente armazenamento premium VMs habilitadas serão mostradas, tudo feito por SSD unidades.  Quando estiver alternado para unidade de disco rígido, armazenamento padrão habilitado VMs unidades de disco de backup abordagem serão mostradas, juntamente com armazenamento premium VMs feitas por SSD.

  ![screen1](../virtual-machines/media/virtual-machines-linux-azure-vm-storage-overview/screen1.png)

Ao criar uma máquina virtual da `azure-cli` você pode escolher entre standard e premium ao escolher o tamanho de máquina virtual via a `-z` ou `--vm-size` cli sinalizador.

### <a name="create-a-vm-with-standard-storage-vm-on-the-cli"></a>Criar uma máquina virtual com armazenamento padrão máquina virtual na cli

O sinalizador de cli `-z` escolhe Standard_A1 com A1 sendo um armazenamento padrão com base em Linux VM.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_A1
```

### <a name="create-a-vm-with-premium-storage-on-the-cli"></a>Criar uma máquina virtual com armazenamento premium na cli

O sinalizador de cli `-z` escolhe Standard_DS1 com DS1 sendo um armazenamento premium com base em Linux VM.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_DS1
```

## <a name="standard-storage"></a>Armazenamento padrão

Azure armazenamento padrão é o tipo padrão de armazenamento.  Armazenamento padrão é econômico e ainda ser alto desempenho.  

## <a name="premium-storage"></a>Armazenamento de Premium

Armazenamento do Azure Premium oferece suporte de disco de alto desempenho e baixa latência para máquinas virtuais executando eu/O-que requer muita cargas de trabalho. Discos virtuais de máquina que usam o armazenamento de Premium armazenam dados em unidades de estado sólido (SSDs). Você pode migrar discos de máquina virtual do seu aplicativo para o armazenamento do Azure Premium para tirar proveito da velocidade e o desempenho desses discos.

Recursos premium do armazenamento:

- Discos de armazenamento Premium: Armazenamento do Azure Premium oferece suporte a discos de máquina virtual que podem ser anexados a série DS, DSv2 ou GS VMs do Azure.

- Blob de página Premium: Premium armazenamento oferece suporte Blobs de página do Azure, que são usadas para manter discos persistentes para máquinas virtuais do Azure (VMs).

- Armazenamento localmente redundante Premium: Uma conta de armazenamento Premium apenas suporta localmente redundantes armazenamento (LRS) como a opção de replicação e mantém três cópias dos dados dentro de uma única região.

- [Armazenamento de Premium](../storage/storage-premium-storage.md)

## <a name="premium-storage-supported-vms"></a>Armazenamento de Premium suporte VMs

Armazenamento de Premium oferece suporte a série DS, série DSv2, série GS e Fs série Azure VMs (máquinas virtuais). Você pode usar discos de armazenamento Standard e Premium com armazenamento Premium suporte de VMs. Mas você não pode usar discos de armazenamento de Premium com série de máquina virtual, que não é Premium armazenamento compatível.

A seguir estão as distribuições Linux que estamos validados com armazenamento Premium.

| Distribuição | Versão                 | Núcleo com suporte    |
|--------------|-------------------------|---------------------|
| Ubuntu       | 12.04                   | 3.2.0-75.110+       |
| Ubuntu       | 14.04                   | 3.13.0-44.73+       |
| Debian       | 7. x, 8. x                | 3.16.7-ckt4-1+      |
| SLES         | SLES 12                 | 3.12.36-38.1+       |
| SLES         | SLES 11 SP4             | 3.0.101-0.63.1+     |
| CoreOS       | 584.0.0+                | 3.18.4+             |
| CentOS       | 6.5, 6.6, 6.7, 7.0, 7.1 | 3.10.0-229.1.2.el7+ |
| RHEL         | 6,8 +, 7.2 +              |                     |


## <a name="file-storage"></a>Armazenamento de arquivos

Armazenamento de arquivos Azure oferece compartilhamentos de arquivos na nuvem usando o protocolo SMB padrão. Com arquivos do Azure, você pode migrar aplicativos corporativos que dependem de servidores de arquivos para o Azure. Aplicativos em execução no Azure facilmente podem montar compartilhamentos de arquivos do Azure máquinas virtuais executando Linux. E com a versão mais recente do armazenamento de arquivos, você também pode montar um compartilhamento de arquivo de um aplicativo local que suporta SMB 3.0.  Porque compartilhamentos de arquivo são SMB, você pode acessá-los por meio de APIs do sistema de arquivo padrão.

Armazenamento de arquivos baseia-se na mesma tecnologia como armazenamento de Blob, tabela e fila para que armazenamento de arquivos oferece a disponibilidade, durabilidade, escalabilidade e redundância geográfica incorporada a plataforma de armazenamento do Azure. Para obter detalhes sobre as metas de desempenho de armazenamento de arquivos e limites, consulte metas de desempenho e escalabilidade de armazenamento do Azure.

- [Como usar o armazenamento de arquivos do Azure com Linux](../storage/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>Armazenamento quente

O nível de armazenamento de alta Azure é otimizado para armazenar dados acessados com frequência.  Armazenamento quente é o tipo de armazenamento de blob armazena padrão.

## <a name="cool-storage"></a>Armazenamento interessante

O nível de armazenamento interessantes Azure é otimizado para armazenar dados raramente acessados e vida longa. Casos de uso de exemplo para armazenamento interessante incluem backups, conteúdo de mídia, dados científicos, conformidade e dados de arquivamento. Em geral, quaisquer dados que raramente acessados são um candidato ideal para armazenamento interessante.

|                             | Nível de armazenamento quente      | Nível de armazenamento interessantes     |
|:----------------------------|:---------------------:|:---------------------:|
| Disponibilidade                | 99,9%                 | 99%                   |
| Disponibilidade (leituras de ar GRS) | 99,99%                | 99,9%                 |
| Encargos de uso               | Custos mais altos de armazenamento  | Custos de armazenamento   |
|                             | Acesso inferior          | Acesso mais alto         |
|                             | e os custos de transação | e os custos de transação |


## <a name="redundancy"></a>Redundância

Os dados em sua conta de armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade, o SLA de armazenamento do Azure mesmo diante de falhas de hardware temporárias de reunião.

Quando você cria uma conta de armazenamento, você deve selecionar uma das opções de replicação a seguir:

- Armazenamento localmente redundante (LRS)
- Armazenamento de zona redundantes (ZRS)
- Armazenamento de localização geográfica redundantes (GRS)
- Acesso de leitura geográfica-redundantes (ar-GRS)

### <a name="locally-redundant-storage"></a>Armazenamento redundante localmente

Armazenamento localmente redundante (LRS) replica seus dados dentro da região em que você criou sua conta de armazenamento. Para maximizar a durabilidade, todas as solicitações feitas nos dados de sua conta de armazenamento é replicada três vezes. Essas três réplicas residem em domínios separados falhas e atualização.  Uma solicitação com êxito retornará somente depois que ele tiver sido escrito para todas as três réplicas.

### <a name="zone-redundant-storage"></a>Armazenamento redundante de zona

Armazenamento de zona redundantes (ZRS) replica seus dados em dois ou três instalações, dentro de uma única região ou entre duas regiões, fornecendo durabilidade maior que LRS. Se sua conta de armazenamento tem ZRS habilitado, seus dados serão duráveis mesmo no caso de falha em um das instalações.

### <a name="geo-redundant-storage"></a>Armazenamento redundante de localização geográfica

Armazenamento de localização geográfica redundantes (GRS) replica seus dados para uma região secundária que é centenas de milhas longe da região primária. Se sua conta de armazenamento tem GRS habilitado, seus dados serão duráveis mesmo no caso de falta de regionais concluída ou um desastre em que a região primária não é recuperável.

### <a name="read-access-geo-redundant-storage"></a>Armazenamento de localização geográfica redundantes de acesso de leitura

Acesso de leitura geográfica-redundantes (ar-GRS) maximiza a disponibilidade de sua conta de armazenamento, fornecendo acesso somente leitura aos dados no local secundário, além da replicação entre duas regiões fornecidos pelo GRS. Que dados ficar indisponíveis na região primária, seu aplicativo pode ler dados da região secundário.

Para detalhamento do armazenamento do Azure redundância consulte:

- [Azure replicação de armazenamento](../storage/storage-redundancy.md)

## <a name="scalability"></a>Escalabilidade

Armazenamento do Azure é amplamente escalável, portanto, você pode armazenar e processo centenas de terabytes de dados para oferecer suporte os cenários de grande volume necessários análise científica, financeiro e aplicativos de mídia. Ou você pode armazenar as pequenas quantidades de dados necessários para um site de pequenas empresas. Sempre que se enquadram às suas necessidades, você paga apenas os dados que está armazenado. Armazenamento do Azure atualmente armazena dezenas de trilhões de objetos customer exclusivos e manipula milhões de solicitações por segundo em média.

Para contas de armazenamento padrão: uma conta de armazenamento padrão tem uma taxa de solicitação total máxima de 20.000 IOPS. O IOPS total em todos os discos de máquina virtual em uma conta de armazenamento padrão não deve exceder esse limite.

Para contas de armazenamento premium: uma conta de armazenamento premium tem uma taxa de transferência total máximo de 50 Gbps. A taxa de transferência total em todos os discos de máquina virtual não deve exceder esse limite.

## <a name="availability"></a>Disponibilidade

Garantimos que pelo menos 99,99% (99,9% para interessantes camada de acesso) do tempo, podemos com êxito processe solicitações para ler dados de contas de armazenamento redundante geográfica de acesso de leitura (ar-GRS), desde que tentativas para ler os dados da região primária serão repetidas na região secundário.

Garantimos que pelo menos 99,9% (99% para interessantes camada de acesso) do tempo, podemos com êxito processe solicitações para ler dados de contas de localização geográfica redundantes armazenamento (GRS), zona redundantes armazenamento (ZRS) e armazenamento localmente redundantes (LRS).

Garantimos que pelo menos 99,9% (99% para interessantes camada de acesso) do tempo, podemos com êxito processe solicitações para gravar dados armazenamento localmente redundantes (LRS), zona redundantes armazenamento (ZRS) e contas de localização geográfica redundantes armazenamento (GRS) e armazenamento redundante geográfica de acesso de leitura (ar-GRS).

- [Azure SLA para armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)


## <a name="regions"></a>Regiões

Azure é disponibilizado em 30 regiões do mundo e tem anunciado planos para 4 regiões adicionais. Expansão geográfica é uma prioridade para Azure porque ele permite que nossos clientes atingir um melhor desempenho e oferecer suporte a seus requisitos e preferências de dados local.  Região mais recente do azures iniciar está localizado na Alemanha.

A Alemanha Microsoft Cloud fornece uma opção diferenciada aos serviços do Microsoft Cloud já disponíveis em toda a Europa, criando maiores oportunidades para inovações e crescimento econômico para altamente regulamentados parceiros e clientes de Alemanha, União Europeia (UE) e o europeu livre comércio associação (da EFTA).

Dados do cliente nesses novos data centers, no Magdeburg e Frankfurt, são gerenciados sob o controle de um objeto de confiança de dados, T-Systems internacional, uma empresa de alemão independente e subsidiária da Deutsche Telekom. Serviços de nuvem comercial da Microsoft nestas dos data centers adicional alemão dados manipulando normas e dar aos clientes opções adicionais de como e onde os dados são processados.


- [Mapa de regiões Azure](https://azure.microsoft.com/regions/)

## <a name="security"></a>Segurança

Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança que permitem que os desenvolvedores criem aplicativos seguros juntos. A conta de armazenamento em si pode ser protegida usando o controle de acesso baseado em função e Azure Active Directory. Dados podem ser protegidos em trânsito entre um aplicativo e Azure usando criptografia do lado do cliente, HTTPS ou SMB 3.0. Dados podem ser definidos automaticamente criptografados quando gravados ao armazenamento do Azure usando criptografia de serviço de armazenamento (SSE). Sistema operacional e dados discos usados por máquinas virtuais podem ser definidos para ser criptografados usando criptografia de disco do Azure. Acesso delegado para os objetos de dados em armazenamento Azure pode ser concedido usando assinaturas de acesso compartilhado.

### <a name="management-plane-security"></a>Segurança do plano de gerenciamento

O plano de gerenciamento consiste nos recursos usados para gerenciar sua conta de armazenamento. Nesta seção, falaremos sobre o modelo de implantação do Gerenciador de recursos do Azure e como usar o controle de acesso baseado em função (RBAC) para controlar o acesso às suas contas de armazenamento. Também falaremos sobre como gerenciar suas chaves de conta de armazenamento e como gerá-los novamente.

### <a name="data-plane-security"></a>Segurança de plano de dados

Nesta seção, veremos permitir o acesso aos objetos dados reais em sua conta de armazenamento, como blobs, arquivos, filas e tabelas, usando compartilhados assinaturas de acesso e políticas de acesso armazenados. Abordaremos SAS de nível de serviço e SAS de nível de conta. Nós também verá como limitar o acesso a um endereço IP específico (ou intervalo de endereços IP), como limitar o protocolo usado para HTTPS e como revogar uma assinatura de acesso compartilhado sem aguardando para que ela expire.

## <a name="encryption-in-transit"></a>Criptografia em trânsito

Esta seção descreve como proteger os dados quando você transfere em ou reduzir o armazenamento do Azure. Falaremos sobre o uso recomendado de HTTPS e à criptografia usada pelo SMB 3.0 para compartilhamentos de arquivos do Azure. Nós também terá uma olhada no lado do cliente criptografia, que permite que você para criptografar os dados antes de serem transferido para o armazenamento em um aplicativo cliente e para descriptografar os dados após transferido sem armazenamento.

## <a name="encryption-at-rest"></a>Criptografia inativos

Falaremos sobre criptografia de serviço de armazenamento (SSE), e como você pode ativá-lo para uma conta de armazenamento, resultando em seu bloco bolhas, blobs de página e acrescentar blobs sendo criptografados automaticamente quando gravados ao armazenamento do Azure. Também veremos como você pode usar a criptografia de disco do Azure e explore os diferenças básicas e casos de criptografia de disco versus SSE versus criptografia do lado do cliente. Brevemente veremos conformidade FIPS para computadores do governo dos EUA.

- [Guia de segurança do Azure armazenamento](../storage/storage-security-guide.md)

## <a name="cost-savings"></a>Redução dos custos

- [Custo de armazenamento](https://azure.microsoft.com/pricing/details/storage/)

- [Calculadora de custos de armazenamento](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>Limites de armazenamento

- [Limites de serviço de armazenamento](../azure-subscription-service-limits.md#storage-limits)
