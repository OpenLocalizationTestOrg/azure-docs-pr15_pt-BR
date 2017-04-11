<properties
   pageTitle="Gerenciador de recursos e clássico implantação | Microsoft Azure"
   description="Descreve as diferenças entre o modelo de implantação do Gerenciador de recursos e o clássico (ou gerenciamento de serviço) modelo de implantação."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/27/2016"
   ms.author="tomfitz"/>

# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Gerenciador de recursos de Azure versus implantação clássica: Noções básicas sobre modelos de implantação e o estado de seus recursos

Neste tópico, você saiba mais sobre o Gerenciador de recursos do Azure e modelos de implantação clássico, o estado de seus recursos, e por seus recursos foram implantados com um ou outro. O Gerenciador de recursos e modelos de implantação clássico representam duas maneiras diferentes de implantação e gerenciamento de suas soluções Azure. Você trabalha com eles por meio de dois conjuntos de API diferentes, e os recursos implantados podem conter diferenças importantes. Os dois modelos não são totalmente compatíveis com os outros. Este tópico descreve as diferenças.

Para simplificar a implantação e gerenciamento de recursos, recomendamos que você use o Gerenciador de recursos para todos os novos recursos. Se possível, recomendamos que você reimplantar recursos existentes por meio do Gerenciador de recursos.

Se você é novo para o gerente de recursos, talvez você queira examinar primeiro a terminologia definida na [Visão geral do Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md).

## <a name="history-of-the-deployment-models"></a>Histórico de modelos de implantação

Azure originalmente fornecido apenas o modelo de implantação clássico. Nesse modelo, cada recurso existe independentemente; não houve nenhuma maneira de agrupar recursos relacionados. Em vez disso, você tinha para manualmente controlar quais recursos compostos de sua solução ou aplicativo e lembre-se de gerenciá-los em uma abordagem coordenada. Para implantar uma solução, você tinha criar cada recurso individualmente através do portal clássico ou criar um script que implantado todos os recursos na ordem correta. Para excluir uma solução, você tinha excluir cada recurso individualmente. Não facilmente, você pode aplicar e atualizar diretivas de controle de acesso para recursos relacionados. Por fim, você não pode aplicar marcas recursos para rotular com termos que ajudam você monitorar seus recursos e gerenciar cobranças.

Em 2014, Azure introduziu Gerenciador de recursos, que adicionou o conceito de um grupo de recursos. Um grupo de recursos é um contêiner para recursos que compartilham um ciclo de vida comuns. O modelo de implantação do Gerenciador de recursos fornece vários benefícios:

- Você pode implantar, gerenciar e monitorar todos os serviços para sua solução como um grupo, em vez de lidar com esses serviços individualmente.
- Repetidamente, você pode implantar sua solução em todo o ciclo de vida e ter confiança que seus recursos são implantados em um estado consistente.
- Você pode aplicar controle de acesso a todos os recursos no seu grupo de recursos e essas políticas são aplicadas automaticamente quando novos recursos são adicionados ao grupo de recursos.
- Você pode aplicar marcas aos recursos para organizar logicamente todos os recursos na sua assinatura.
- Você pode usar JavaScript Object Notation (JSON) para definir a infraestrutura de sua solução. O arquivo JSON é conhecido como um modelo do Gerenciador de recursos.
- Você pode definir as dependências entre recursos para que eles são implantados na ordem correta.

Quando o Gerenciador de recursos foi adicionado, todos os recursos foram adicionados retroativamente a grupos de recursos padrão. Se você criar um recurso por meio de implantação clássico agora, o recurso é criado automaticamente dentro de um grupo de recursos padrão para esse serviço, embora você não especificou desse grupo de recursos na implantação. No entanto, basta existentes em um grupo de recursos não significa que o recurso foi convertido para o modelo do Gerenciador de recursos. Vamos examinar como cada serviço lida com os modelos de dois implantação na próxima seção. 

## <a name="understand-support-for-the-models"></a>Noções básicas sobre o suporte para os modelos 

Ao decidir qual modelo implantação usar para seus recursos, há três cenários a serem consideradas:

1. O serviço suporta o Gerenciador de recursos e fornece apenas um único tipo.
2. O serviço compatível com o Gerenciador de recursos, mas fornece dois tipos - uma para Gerenciador de recursos e outra para clássico. Esse cenário só se aplica máquinas virtuais, contas de armazenamento e redes virtuais.
3. O serviço não suporta o Gerenciador de recursos.

Para descobrir se um serviço suporta Gerenciador de recursos, consulte [Gerenciador de recursos com suporte provedores](resource-manager-supported-services.md).

Se o serviço que você deseja usar não suporta o Gerenciador de recursos, você deve continuar usando implantação clássica.

Se o serviço oferece suporte a rede virtual e **não é** uma máquina virtual, conta de armazenamento ou Gerenciador de recursos, você pode usar o Gerenciador de recursos sem qualquer complicações.

Para máquinas virtuais, contas de armazenamento e redes virtuais, se o recurso foi criado por meio de implantação clássica, você deverá continuar a operar por meio de operações clássicas. Se a máquina virtual, conta de armazenamento ou rede virtual foi criada por meio da implantação do Gerenciador de recursos, você deve continuar usando o Gerenciador de recursos de operações. Essa distinção pode obter confusa quando sua assinatura contém uma mistura de recursos criada por meio do Gerenciador de recursos e implantação clássica. Essa combinação de recursos pode criar resultados inesperados porque os recursos não oferecem suporte para as mesmas operações.

Em alguns casos, um comando Gerenciador de recursos pode recuperar informações sobre um recurso criado por meio da implantação clássica ou pode executar uma tarefa administrativa como mover um recurso clássico para outro grupo de recursos. Mas, nesses casos não devem fornecer a impressão de que o tipo oferece suporte a operações do Gerenciador de recursos. Por exemplo, suponha que você tenha um grupo de recursos que contém uma máquina virtual que foi criada com implantação clássica. Se você executar o seguinte comando do PowerShell do Gerenciador de recursos:

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

Retorna a máquina virtual:
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

No entanto, o Gerenciador de recursos o cmdlet **Get-AzureRmVM** retornará somente máquinas virtuais implantadas por meio do Gerenciador de recursos. O comando a seguir não retornar a máquina virtual criada por meio de implantação clássica.

    Get-AzureRmVM -ResourceGroupName ExampleGroup

Somente recursos criados pelas marcas de suporte do Gerenciador de recursos. Você não pode aplicar marcas aos recursos clássicos.

## <a name="resource-manager-characteristics"></a>Características do Gerenciador de recursos

Para ajudá-lo a entender os dois modelos, vamos analisar as características dos tipos de Gerenciador de recursos:

- Criado por meio do [portal do Azure](https://portal.azure.com/).

     ![Portal do Azure](./media/resource-manager-deployment-model/portal.png)

     Para computação, armazenamento e recursos de rede, você tem a opção de usar o Gerenciador de recursos ou clássico implantação. Selecione **Gerenciador de recursos**.

     ![Implantação do Gerenciador de recursos](./media/resource-manager-deployment-model/select-resource-manager.png)

- Criado com a versão do Gerenciador de recursos do cmdlets do PowerShell do Azure. Esses comandos têm o formato *Verbo-AzureRmNoun*.

        New-AzureRmResourceGroupDeployment

- Criado por meio da [API do Azure Gerenciador de recursos REST](https://msdn.microsoft.com/library/azure/dn790568.aspx) para operações de restante.

- Criado por meio do Azure comandos executados no modo do **arm** .

        azure config mode arm
        azure group deployment create 

- O tipo de recurso não inclui **(clássico)** no nome. A imagem a seguir mostra o tipo como **conta de armazenamento**.

    ![aplicativo Web](./media/resource-manager-deployment-model/resource-manager-type.png)

## <a name="classic-deployment-characteristics"></a>Características de implantação clássico

Você também pode saber o modelo clássico de implantação como o modelo de gerenciamento de serviço.

Recursos criados no modelo clássico de implantação compartilham as seguintes características:

- Criado por meio do [portal clássico](https://manage.windowsazure.com)

     ![Portal clássico](./media/resource-manager-deployment-model/classic-portal.png)

     Ou, o portal do Azure e você especificar implantação **clássico** (para computação, armazenamento e redes).

     ![Implantação clássica](./media/resource-manager-deployment-model/select-classic.png)

- Criado por meio da versão de gerenciamento de serviço do cmdlets do PowerShell do Azure. Esses nomes de comando tem o formato *Verbo-AzureNoun*.

        New-AzureVM 

- Criado por meio da [API do serviço de gerenciamento de REST](https://msdn.microsoft.com/library/azure/ee460799.aspx) para operações de restante.
- Criado por meio do Azure comandos a executar no modo de **asm** .

        azure config mode asm
        azure vm create 

- O tipo de recurso inclui **(clássico)** no nome. A imagem a seguir mostra o tipo como **conta de armazenamento (clássica)**.

    ![tipo de clássico](./media/resource-manager-deployment-model/classic-type.png)

Você pode usar o portal do Azure para gerenciar recursos que foram criados por meio de implantação clássica.

## <a name="changes-for-compute-network-and-storage"></a>Alterações para computação, rede e armazenamento

O diagrama a seguir exibe os recursos de computação, rede e armazenamento implantados por meio do Gerenciador de recursos.

![Arquitetura do Gerenciador de recursos](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Observe as seguintes relações entre os recursos:

- Todos os recursos existirem dentro de um grupo de recursos.
- Na máquina virtual depende de uma conta de armazenamento específicos definida no provedor de recursos de armazenamento para armazenar seus discos no armazenamento de blob (obrigatório).
- Na máquina virtual referencia uma NIC específico definida em uma disponibilidade definir definido no provedor de recursos de computação (opcional) e o provedor de recursos de rede (obrigatório).
- NIC referências endereço IP atribuído da máquina virtual (obrigatório), a sub-rede da rede virtual para a máquina virtual (obrigatório) e a um grupo de segurança de rede (opcional).
- A sub-rede de uma rede virtual faz referência a um grupo de segurança de rede (opcional).
- A instância de Balanceador de carga referências o pool de back-end de endereços IP que incluem a NIC de uma máquina virtual (opcional) e referencia um carga balanceador público ou privado endereço IP (opcional).

Aqui estão os componentes e seus relacionamentos para implantação clássico:

![arquitetura clássica](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

A solução clássica para hospedar uma máquina virtual inclui:

- Um serviço de nuvem necessários que atua como um contêiner para hospedar máquinas virtuais (computação). Máquinas virtuais são fornecidas automaticamente com uma placa de interface de rede (NIC) e um endereço IP atribuído pelo Azure. Além disso, o serviço de nuvem contém uma instância de Balanceador de carga externo, um endereço IP público e pontos de extremidade padrão para permitir que o tráfego remoto de PowerShell de desktop e remoto para máquinas virtuais baseadas em Windows e Secure Shell (SSH) para máquinas virtuais baseadas em Linux.
- Uma conta de armazenamento necessários que armazena os VHDs para uma máquina virtual, inclusive o sistema operacional, as unidades de dados temporários e adicionais (armazenamento).
- Uma rede virtual opcional que atua como um recipiente adicional, na qual você pode criar uma estrutura de sub-redes e designar a sub-rede no qual a máquina virtual está localizada (rede).

A tabela a seguir descreve as alterações no como provedores de recursos de computação, rede e armazenamento interagem:

 Item | Clássico | Gerenciador de recursos
 ---|---|---
| Serviço de nuvem para máquinas virtuais |  Serviço de nuvem era um contêiner para manter as máquinas virtuais que obrigatório disponibilidade da plataforma e balanceamento de carga. | Serviço de nuvem já não é um objeto necessário para a criação de uma máquina Virtual usando o novo modelo. |
| Redes virtuais | Uma rede virtual é opcional para a máquina virtual. Se incluída, a rede virtual não pode ser implantada com o Gerenciador de recursos. | Máquina virtual requer uma rede virtual que tenha sido implantada com o Gerenciador de recursos. |
| Contas de armazenamento | Na máquina virtual requer uma conta de armazenamento que armazena os VHDs para o sistema operacional, discos de dados temporários e adicionais. | Na máquina virtual requer uma conta de armazenamento para armazenar seus discos no armazenamento de blob. |
| Conjuntos de disponibilidade | Disponibilidade para a plataforma foi indicada Configurando a mesma "AvailabilitySetName" nas máquinas virtuais. A contagem máxima de domínios de falhas foi 2. | Conjunto de disponibilidade é um recurso exposto pelo provedor de Microsoft.Compute. Máquinas virtuais que exigem alta disponibilidade devem ser incluídas no conjunto de disponibilidade. A contagem máxima de domínios de falhas agora é 3. |
| Grupos de afinidade | Grupos de afinidade eram necessários para a criação de redes virtuais. No entanto, com a introdução de redes virtuais Regional, que não era necessário mais. |Para simplificar, o conceito de grupos de afinidade não existe nas APIs expostas por meio do Gerenciador de recursos do Azure. |
| Balanceamento de carga    | Criação de um serviço de nuvem fornece um balanceador de carga implícito para máquinas virtuais implantadas. | O balanceador de carga é um recurso exposto pelo provedor do Microsoft.Network. A interface de rede principal das máquinas virtuais que precisa ser balanceamento de carga deve ser referenciando balanceador de carga. Balanceadores de carga podem ser interno ou externo. Uma instância de Balanceador de carga referências o pool de back-end de endereços IP que incluem a NIC de uma máquina virtual (opcional) e referencia um carga balanceador público ou privado endereço IP (opcional). [Leia mais.](../articles/resource-groups-networking.md) |
|Endereço IP virtual | Serviços de nuvem obtém um VIP padrão (endereço de IP Virtual) quando uma máquina virtual é adicionada a um serviço na nuvem. O endereço IP Virtual é o endereço associado balanceador de carga implícito.    | Endereço IP público é um recurso exposto pelo provedor do Microsoft.Network. Endereço IP público pode ser (reservado) estática ou dinâmica. Dinâmico IPs público pode ser atribuído a um balanceador de carga. Endereços IP do público pode ser protegido usando grupos de segurança. |
|Endereço IP reservado|   Você pode reservar um endereço IP no Azure e associá-la a um serviço de nuvem para garantir que o endereço IP seja conjunta.   | Endereço IP público podem ser criado no modo "Estático" e oferece o mesmo recurso como "Reservadas endereço IP". IPs público estático só podem ser atribuídos a um balanceador de carga agora. |
|Endereço IP público (PIP) por máquina virtual | Endereços IP públicos também pode ser associados a uma máquina virtual diretamente. | Endereço IP público é um recurso exposto pelo provedor do Microsoft.Network. Endereço IP público pode ser (reservado) estática ou dinâmica. No entanto, apenas IPs público dinâmico pode ser atribuído a uma Interface de rede para obter um IP público por máquina virtual agora. |
|Pontos de extremidade| Pontos de extremidade de entrada necessárias sejam configurados em uma máquina Virtual estar abertos a conectividade para determinadas portas. Um dos modos de comuns de se conectar a máquinas virtuais executadas pela configuração de pontos de extremidade de entrada. | Regras de entrada NAT pode ser configuradas na balanceadores de carga para obter o mesmo recurso de habilitar pontos de extremidade em portas específicas para conexão com VMs. |
|Nome DNS| Um serviço de nuvem teria um nome de DNS exclusivo implícito. Por exemplo: `mycoffeeshop.cloudapp.net`. | Nomes de DNS são parâmetros opcionais que podem ser especificados em um recurso de endereço IP público. O FQDN é no seguinte formato - `<domainlabel>.<region>.cloudapp.azure.com`. |
|Interfaces de rede | Primário e secundário Interface de rede e suas propriedades foram definidas como configuração de rede de uma máquina Virtual. | Interface de rede é um recurso exposto pelo provedor de Microsoft.Network. O ciclo de vida da Interface de rede não está vinculado a uma máquina Virtual. Ele referências endereço IP atribuído da máquina virtual (obrigatório), a sub-rede da rede virtual para a máquina virtual (obrigatório) e a um grupo de segurança de rede (opcional). |

Para saber sobre como conectar redes virtuais de diferentes modelos de implantação, consulte [conectar redes virtuais de diferentes modelos de implantação no portal](./vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="migrate-from-classic-to-resource-manager"></a>Migrar do clássico ao Gerenciador de recursos

Se você estiver pronto para migrar seus recursos de implantação clássica para implantação do Gerenciador de recursos, consulte:

1. [Técnico detalhamento da plataforma suportada migração de clássico ao Gerenciador de recursos do Azure](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [Migração de plataforma suportada de recursos de IaaS do clássico ao Gerenciador de recursos do Azure](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [Migrar os recursos de IaaS do clássico ao Gerenciador de recursos do Azure usando o PowerShell do Azure](./virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [Migrar os recursos de IaaS do clássico ao Gerenciador de recursos do Azure usando CLI do Azure](./virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Posso criar uma máquina virtual usando o Gerenciador de recursos do Azure para implantar em uma rede virtual ou a conta de armazenamento criada usando implantação clássica?**

Não há suporte para isso. Você não pode usar o Gerenciador de recursos do Azure para implantar uma máquina virtual em uma rede virtual que foi criada usando a implantação clássica.

**Posso criar uma máquina virtual usando o Gerenciador de recursos do Azure de uma imagem do usuário que foi criada usando as APIs de gerenciamento de serviço do Azure?**

Não há suporte para isso. No entanto, você pode copiar os arquivos VHD de uma conta de armazenamento que foi criada usando as APIs de gerenciamento de serviço e adicioná-las a uma nova conta criada por meio do Gerenciador de recursos do Azure.

**Qual é o impacto sobre a cota de minha assinatura?**

As cotas para as contas de armazenamento criadas por meio do Gerenciador de recursos do Azure, redes virtuais e máquinas virtuais são separadas dos outras cotas. Cada assinatura obtém cotas para criar os recursos usando as novas APIs. Você pode ler mais sobre as cotas adicionais [aqui](../articles/azure-subscription-service-limits.md).

**Posso continuar a usar o meu scripts automatizados para provisionamento de contas de armazenamento por meio de APIs do Gerenciador de recursos, redes virtuais e máquinas virtuais?**

Todos os scripts que você tenha criado e automação continuam a trabalhar para as máquinas virtuais existentes, redes virtuais criadas sob o modo de gerenciamento de serviço do Azure. No entanto, os scripts devem ser atualizados para usar o novo esquema para criar os mesmos recursos através de modo do Gerenciador de recursos.

**Onde posso encontrar exemplos de modelos do Gerenciador de recursos do Azure?**

Um conjunto abrangente de modelos de starter pode ser encontrado nos [Modelos de início rápido do Azure Gerenciador de recursos](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Próximas etapas

- Para percorrer a criação de modelo que define uma máquina virtual, a conta de armazenamento e rede virtual, consulte [explicação passo a passo de modelo do Gerenciador de recursos](resource-manager-template-walkthrough.md).
- Para ver os comandos para implantar um modelo, consulte [implantar um aplicativo com o Gerenciador de recursos do Azure modelo](resource-group-template-deploy.md).
