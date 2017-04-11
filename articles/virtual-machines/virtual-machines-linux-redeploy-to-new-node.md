<properties 
    pageTitle="Reimplantar máquinas virtuais Linux | Microsoft Azure" 
    description="Descreve como reimplantar máquinas virtuais de Linux para atenuar problemas de conexão SSH." 
    services="virtual-machines-linux" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-linux" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>

# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Reimplantar máquina virtual para o novo nó Azure

Se você tiver sido enfrentando dificuldades SSH solução de problemas ou aplicativo acesso a um Azure virtuais de máquina, reimplantar a máquina virtual pode ajudar. Quando você reimplanta uma máquina virtual, ele move a máquina virtual para um novo nó dentro de infraestrutura do Azure e, em seguida, liga-lo novamente, reter todas as suas opções de configuração e recursos associados. Este artigo mostra como reimplantar uma máquina virtual usando o Azure CLI ou o portal do Azure.

> [AZURE.NOTE] Depois de você reimplanta uma máquina virtual, o disco temporário for perdido e endereços IP dinâmicos associados a interface de rede virtual são atualizados. 


## <a name="using-azure-cli"></a>Usando CLI Azure

Verifique se você tem a [Mais recente CLI Azure instalado](../xplat-cli-install.md) no seu computador e você está no modo do Gerenciador de recursos (`azure config mode arm`).

Use o seguinte comando CLI Azure reimplantar sua máquina virtual:

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

Você pode ver o status da alteração máquina virtual enquanto percorre o processo de reimplantação. O `PowerState` da máquina virtual vai 'Execução' para 'Atualizando', 'Iniciar' e finalmente 'Running' enquanto percorre o processo de reimplantar para um novo host. Verificar o status das VMs dentro de um grupo de recursos com:

```bash
azure vm list -g <resourcegroup>
```


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>Próximas etapas
Se você estiver tendo problemas de conexão com sua máquina virtual, você pode encontrar ajuda específica sobre [solução de problemas de conexões SSH](virtual-machines-linux-troubleshoot-ssh-connection.md) ou [SSH detalhada etapas de solução de problemas](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md). Se você não consegue acessar um aplicativo executado em sua máquina virtual, você também pode ler [Solucionando problemas de aplicativo](virtual-machines-linux-troubleshoot-app-connection.md).