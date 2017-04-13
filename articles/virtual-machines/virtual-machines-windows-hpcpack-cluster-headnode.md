<properties
 pageTitle="Criar um nó de cabeça HPC Pack em uma máquina virtual do Azure | Microsoft Azure"
 description="Saiba como usar o portal do Azure e o modelo de implantação do Gerenciador de recursos para criar um nó de cabeça Microsoft HPC Pack em uma máquina virtual do Azure."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/17/2016"
 ms.author="danlep"/>

# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Criar o nó principal de um cluster de HPC Pack em uma VM Azure com uma imagem de Marketplace


Use uma [imagem de máquina virtual do Microsoft HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) a partir do Azure Marketplace e o portal do Azure para criar o nó principal de um cluster HPC. Esta imagem de máquina virtual do HPC Pack baseia-se no Centro de dados do Windows Server 2012 R2 com HPC Pack 2012 R2 atualização 3 pré-instalado. Use este nó principal para uma prova de implantação de conceito de HPC Pack no Azure. Você pode adicionar nós de computação ao cluster para executar cargas de trabalho HPC.



>[AZURE.TIP]Para implantar um cluster de HPC Pack concluído no Azure que inclui o nó principal e nós de computação, recomendamos que você use um método automatizado. Opções incluem o [script de implantação de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) e o modelo do Gerenciador de recursos de [cluster de HPC Pack para cargas de trabalho do Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/) . Consulte [Opções de cluster de HPC Pack no Azure](virtual-machines-windows-hpcpack-cluster-options.md) modelos adicionais. 


## <a name="planning-considerations"></a>Considerações de planejamento

Como mostrado na figura a seguir, você implantar o nó principal HPC Pack em um domínio do Active Directory em uma rede virtual Azure.

![Nó principal HPC Pack][headnode]

* **Domínio do active Directory** - o HPC Pack nó principal deve ser associado a um domínio do Active Directory no Azure antes de começar os serviços HPC a máquina virtual. Como mostrado neste artigo, para uma prova de implantação de conceito, você pode promover a máquina virtual que você criar para o nó principal como controlador de domínio antes de iniciar os serviços HPC. Outra opção é implantar um controlador de domínio separado e floresta no Azure ao qual você ingressar o nó principal máquina virtual.

* **Rede virtual azure** - quando você usa o modelo de implantação do Gerenciador de recursos para implantar o nó principal, que você especifica ou cria uma rede virtual Azure. Use a rede virtual se você precisará ingressar o nó principal para um domínio existente do Active Directory. Você também pode precisar dela posteriormente para adicionar o nó de computação VMs ao cluster.

    
## <a name="steps-to-create-the-head-node"></a>Etapas para criar o nó principal

A seguir está as etapas de alto nível para usar o portal do Azure para criar uma máquina virtual do Azure, o nó principal HPC Pack usando o modelo de implantação do Gerenciador de recursos. 


1. Se você quiser criar uma nova floresta do Active Directory no Azure com controlador de domínio separado VMs, uma opção é usar um [modelo do Gerenciador de recursos](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/). Para uma prova simple de implantação de conceito, ele é pequeno pular esta etapa e configurar o nó principal máquina virtual em si como controlador de domínio. Essa opção é descrita neste artigo.
    
2. Em [HPC Pack 2012 R2 na página do Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) do Azure Marketplace, clique em **Create Virtual Machine**. 

3. No portal do, na página **HPC Pack 2012 R2 no Windows Server 2012 R2** , selecione o modelo de implantação do **Gerenciador de recursos** e clique em **criar**.

    ![Imagem de HPC Pack][marketplace]

4. Use o portal para definir as configurações e criar a máquina virtual. Se você estiver começando a usar o Azure, siga o tutorial [criar uma máquina de virtual do Windows no portal do Azure](virtual-machines-windows-hero-tutorial.md). Para uma prova de implantação de conceito, você geralmente pode aceitar o padrão ou configurações recomendadas.

    >[AZURE.NOTE]Se você quiser participar o nó principal para um domínio do Active Directory existente no Azure, verifique se que você especificar a rede virtual para esse domínio ao criar a máquina virtual.
       
4. Depois de criar a máquina virtual e a máquina virtual está em execução, [Conecte-se para a máquina virtual](virtual-machines-windows-connect-logon.md) pela área de trabalho remota. 

5. Ingressar a máquina virtual a uma floresta de domínio existente ou crie uma floresta de domínio a máquina virtual em si.

    * Se você criou a máquina virtual em uma rede virtual Azure com uma floresta de domínio existente, participar a máquina virtual à floresta usando ferramentas padrão do Gerenciador do servidor ou o Windows PowerShell. Reinicie o.

    * Se você criou a máquina virtual em uma nova rede virtual (sem uma floresta de domínio existente), em seguida, promova a máquina virtual como controlador de domínio. Use as etapas padrão para instalar e configurar a função de serviços de domínio do Active Directory no nó principal. Para obter etapas detalhadas, consulte [instalar um novo Windows Server 2012 floresta do Active Directory](https://technet.microsoft.com/library/jj574166.aspx).

5. Após a máquina virtual está sendo executado e está associada a uma floresta do Active Directory, inicie os serviços de pacote de HPC da seguinte maneira:

    a. Conectar-se para o nó principal máquina virtual usando uma conta de domínio que seja um membro do grupo de administradores local. Por exemplo, use a conta de administrador que configurar quando você criou o nó principal máquina virtual.

    b. Para uma configuração de nó principal padrão, iniciar o Windows PowerShell como administrador e digite o seguinte:

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Pode levar alguns minutos para que os serviços de pacote de HPC iniciar.

    Para opções de configuração de nó principal adicionais, digite `get-help HPCHNPrepare.ps1`.


## <a name="next-steps"></a>Próximas etapas

* Agora, você pode trabalhar com o nó principal do cluster HPC Pack. Por exemplo, inicie HPC Cluster Manager e conclua a [Lista de tarefas pendentes de implantação](https://technet.microsoft.com/library/jj884141.aspx).
* Se você quiser aumentar o cluster calcular capacidade sob demanda, adicionar [nós do Azure intermitente](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md) em um serviço de nuvem. 

* Tente executar uma carga de trabalho de teste no cluster. Por exemplo, consulte o [guia de Introdução](https://technet.microsoft.com/library/jj884144)de de HPC Pack.

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png
