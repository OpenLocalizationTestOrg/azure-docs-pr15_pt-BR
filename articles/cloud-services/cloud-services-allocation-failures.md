<properties
    pageTitle="Solução de problemas de falha de alocação de serviço de nuvem | Microsoft Azure"
    description="Falha de alocação de solução de problemas quando você implantar os serviços de nuvem no Azure"
    services="azure-service-management, cloud-services"
    documentationCenter=""
    authors="simonxjx"
    manager="felixwu"
    editor=""
    tags="top-support-issue"/>

<tags
    ms.service="cloud-services"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="v-six"/>



# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Falha de alocação de solução de problemas quando você implantar os serviços de nuvem no Azure

## <a name="summary"></a>Resumo
Quando você implantar instâncias em um serviço de nuvem ou adiciona novos web ou instâncias de função de trabalho, o Microsoft Azure aloca recursos de computação. Ocasionalmente, você poderá receber erros ao executar essas operações mesmo antes de você atingir os limites de assinatura Azure. Este artigo explica as causas de algumas das falhas de alocação comuns e sugere possíveis correções. As informações também podem ser útil quando você planejar a implantação dos seus serviços.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Plano de fundo – como funciona a alocação
Os servidores dos data centers do Azure são particionados em clusters. Uma nova solicitação de alocação de serviço de nuvem será tentada em vários clusters. Quando a primeira instância for implantada em um serviço de nuvem (na preparação ou produção), que serviço em nuvem obtém fixada um cluster. Qualquer implantações adicionais para o serviço de nuvem acontecerá no mesmo cluster. Neste artigo, vamos nos referir a isso como "fixos para um cluster". Diagrama 1 abaixo ilustra o caso de uma alocação normal que será tentado em vários clusters; Diagrama 2 ilustra o caso de uma alocação que tem fixados Cluster 2 porque é onde o CS_1 de serviço de nuvem existente está hospedado.

![Diagrama de alocação](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Por que acontece falha de alocação
Quando uma solicitação de alocação é fixada a um cluster, há uma chance maior de falhando de encontrar recursos gratuitos, pois o pool de recursos disponíveis está limitado a um cluster. Além disso, se sua solicitação de alocação é fixada um cluster, mas não há suporte para o tipo de recurso solicitado por esse cluster, sua solicitação falhará mesmo que o cluster tem recurso gratuito. Diagrama 3 abaixo ilustra o caso onde uma alocação fixada falha porque o cluster candidato somente não tem recursos gratuitos. Diagrama 4 ilustra o caso onde uma alocação fixada falha porque o cluster candidato somente não suporta o tamanho de máquina virtual solicitado, mesmo que o cluster tem recursos gratuitos.

![Falha de alocação fixada](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Falha de alocação de solução de problemas para serviços de nuvem
### <a name="error-message"></a>Mensagem de erro
Você pode ver a seguinte mensagem de erro:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Problemas comuns
Aqui estão os cenários comuns de alocação que causam uma solicitação de alocação a ser fixado com um único cluster.

- Implantando Slot de teste - se um serviço de nuvem tem uma implantação em nenhum slot, em seguida, é fixado o serviço de nuvem todo um cluster específico.  Isso significa que se uma implantação já existir no slot produção, em seguida, uma nova implantação de preparação pode apenas ser alocada no mesmo cluster como o slot de produção. Se o cluster está se aproximando a capacidade, a solicitação poderá falhar.

- Dimensionamento - adicionando novas instâncias a um serviço de nuvem existente deve alocar no mesmo cluster.  Dimensionamento pequeno solicitações geralmente pode ser alocado, mas nem sempre. Se o cluster está se aproximando a capacidade, a solicitação poderá falhar.

- Grupo de afinidade - uma nova implantação em um serviço de nuvem vazia pode ser alocada pelo estrutura em qualquer cluster naquela região, a menos que o serviço de nuvem é fixado a um grupo de afinidade. Implantações ao mesmo grupo afinidade serão tentadas no mesmo cluster. Se o cluster está se aproximando a capacidade, a solicitação poderá falhar.

- Grupo de afinidade vNet - redes virtuais mais antigos foram vinculados a grupos de afinidade em vez de regiões e os serviços de nuvem nessas redes virtuais seriam ser fixados cluster afinidade grupo. Implantações a esse tipo de rede virtual serão tentadas no cluster fixado. Se o cluster está se aproximando a capacidade, a solicitação poderá falhar.

## <a name="solutions"></a>Soluções

1. Reimplantar a um novo serviço de nuvem - esta solução é provavelmente será mais bem-sucedidas, pois permite a plataforma à sua escolha todos os clusters nessa região.

    - Implantar a carga de trabalho em um novo serviço de nuvem  

    - Atualize o CNAME ou um registro para apontar o tráfego para o novo serviço de nuvem

    - Após zero tráfego vai para o site antigo, você poderá excluir o serviço de nuvem antigo. Essa solução deve provoca o tempo de inatividade zero.

2. Exclusão de produção e teste slots - esta solução preservará seu nome DNS existente, mas fará com que o tempo de inatividade ao seu aplicativo.

    - Excluir a produção e teste slots de um serviço de nuvem existente para que o serviço de nuvem está vazio e então

    - Crie uma nova implantação no serviço de nuvem existente. Isso tentará novamente alocação em todos os clusters na região. Certifique-se de que o serviço de nuvem não está vinculado a um grupo de afinidade.

3. IP reservado - esta solução preservará seu IP existente de endereço, mas fará com que o tempo de inatividade ao seu aplicativo.  

    - Criar um IP reservado para a sua implantação existente usando o Powershell

    ```
    New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
    ```

    - Siga #2 acima, lembrando-se especificar o novo ReservedIP em CSCFG do serviço.

4. Remova o grupo de afinidade para novas implantações - grupos de afinidade já não são recomendados. Siga as etapas para #1 acima para implantar um novo serviço de nuvem. Certifique-se de serviço de nuvem não está em um grupo de afinidade.

5. Converta em uma rede Virtual regionais - consulte [como migrar de grupos de afinidade com uma rede Virtual regionais (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
