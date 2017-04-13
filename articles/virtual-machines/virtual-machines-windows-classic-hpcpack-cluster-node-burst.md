<properties
 pageTitle="Adicionar intermitente nós a um cluster de HPC Pack | Microsoft Azure"
 description="Saiba como expandir um cluster de HPC Pack no Azure sob demanda adicionando instâncias de função de trabalho em execução em um serviço de nuvem"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Adicionar nós sob demanda "intermitente" a um cluster de HPC Pack no Azure



Se você configurar um cluster [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) no Azure, convém uma maneira de dimensionar rapidamente a capacidade de cluster para cima ou para baixo, sem manter um conjunto de nó de computação pré-configurado VMs. Este artigo mostra como adicionar nós sob demanda "intermitente" (instâncias de função trabalhador execução em um serviço de nuvem) como recursos de computação para um nó principal no Azure. 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

![Nós intermitente][burst]

As etapas neste artigo ajudar você a adicionar nós Azure rapidamente a um baseado em nuvem HPC Pack nó principal máquina virtual para um teste ou uma prova de conceito implantação. As etapas de alto nível são a mesma que as etapas para "intermitente no Azure" para adicionar capacidade a um cluster de HPC Pack do local de computação de nuvem. Para um tutorial, consulte [Configurar um híbrido calcular cluster com Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Para obter orientações detalhadas e considerações para implantações de produção, consulte [intermitente no Azure com Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).


## <a name="prerequisites"></a>Pré-requisitos

* **Nó principal HPC Pack implantado em uma máquina virtual do Azure** - você pode usar um nó principal autônomo máquina virtual ou aquele que faz parte de um cluster maior. Para criar um nó principal autônomo, consulte [implantar um nó de cabeça HPC Pack em uma máquina virtual do Azure](virtual-machines-windows-hpcpack-cluster-headnode.md). Para opções de implantação de cluster de HPC Pack automatizado, consulte [Opções para criar e gerenciar um cluster de HPC do Windows Azure com Microsoft HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

    >[AZURE.TIP] Se você usar o [script de implantação de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para criar o cluster no Azure, você pode incluir nós do Azure intermitente em sua implantação automatizada. Consulte os exemplos nesse artigo.

* **Assinatura do azure** - adicionar nós Azure, você pode escolher a mesma assinatura usado para implantar o nó principal máquina virtual, ou uma assinatura diferente (ou assinaturas).

* **Cota de cores** - talvez você precise aumentar a cota de cores, especialmente se você optar por implantar vários nós Azure com tamanhos de multinucleados. Aumentar uma cota, [Abra uma solicitação de suporte online do cliente](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sem nenhum custo.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Etapa 1: Criar um serviço de nuvem e uma conta de armazenamento para nós do Azure

Use o Azure portal clássico ou ferramentas equivalentes para configurar os seguintes recursos que são necessárias para implantar os nós Azure:

* Um novo serviço de nuvem do Azure
* Uma nova conta de armazenamento do Azure

>[AZURE.NOTE] Não reutilize um serviço de nuvem existente em sua assinatura. 

**Considerações**

* Configure um serviço de nuvem separada para cada modelo de nó Azure que você planeja criar. No entanto, você pode usar a mesma conta de armazenamento para vários modelos de nó.

* Recomendamos que você localizar o serviço de nuvem e a conta de armazenamento para a implantação na mesma região Azure.




## <a name="step-2-configure-an-azure-management-certificate"></a>Etapa 2: Configurar um certificado de gerenciamento do Azure

Para adicionar nós Azure como recursos de computação, é necessário um certificado de gerenciamento no nó principal e carregar um correspondente de certificado para a assinatura Azure usada para a implantação.

Para esse cenário, você pode escolher o **Certificado do gerenciamento de Azure HPC padrão** que HPC Pack instala e configura automaticamente no nó principal. Este certificado é útil para implantações de prova de conceito e fins de teste. Para usar este certificado, carregue o C:\Program Files\Microsoft HPC Pack de arquivo 2012\Bin\hpccert.cer do nó cabeça máquina virtual para a assinatura. Para carregar o certificado no [Azure portal clássico](https://manage.windowsazure.com), clique em **configurações** > **Gerenciamento de certificados**.

Para obter opções adicionais configurar o certificado de gerenciamento, consulte [cenários para configurar o certificado de gerenciamento do Azure para implantações intermitente do Azure](http://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Etapa 3: Implantar Azure nós ao cluster



As etapas para adicionar e iniciar nós Azure neste cenário geralmente são a mesma que as etapas com um nó de cabeça local. Para obter mais informações, consulte as seções a seguir nas [etapas para implantar o Azure nós com Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Criar um modelo de nó Azure

* Adicionar nós Azure ao cluster HPC do Windows

* Iniciar nós (Configurar) o Azure

Depois de adicionar e iniciar os nós, está prontos para ser usado para executar trabalhos de cluster.

Se você tiver problemas ao implantar nós Azure, consulte [Solucionar problemas de implantações do Azure nós com Microsoft HPC Pack](http://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>Próximas etapas

* Para usar um tamanho de instância pesados os nós intermitente, consulte as considerações [sobre H série e pesados VMs A série](virtual-machines-windows-a8-a9-a10-a11-specs.md).

* Se você quiser aumentar ou reduzir os recursos de computação Azure acordo com a carga de trabalho de cluster automaticamente, consulte [automaticamente ampliar ou reduzir recursos de computação Azure em um cluster de HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-cluster-node-burst/burst.png
