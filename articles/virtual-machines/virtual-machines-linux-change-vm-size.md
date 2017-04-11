<properties
   pageTitle="Como redimensionar uma VM Linux | Microsoft Azure"
   description="Como dimensionar ou dimensionar para baixo uma máquina virtual Linux, alterando o tamanho de máquina virtual."
   services="virtual-machines-linux"
   documentationCenter="na"
   authors="mikewasson"
   manager="timlt"
   editor=""
   tags=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="mikewasson"/>


# <a name="how-to-resize-a-linux-vm"></a>Como redimensionar uma VM Linux

## <a name="overview"></a>Visão geral 

Após você provisionar uma máquina virtual (VM), você pode expandir ou reduzir a máquina virtual, alterando o [tamanho da máquina virtual][vm-sizes]. Em alguns casos, você deve desalocar a máquina virtual primeiro. Isso pode acontecer se o novo tamanho não está disponível no cluster hardware que hospeda a máquina virtual.

Este artigo mostra como redimensionar uma VM Linux usando o [Azure CLI][azure-cli].

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.


## <a name="resize-a-linux-vm"></a>Redimensionar uma máquina virtual Linux 

Para redimensionar uma máquina virtual, execute as seguintes etapas.

1. Execute o seguinte comando CLI. Esse comando lista os tamanhos de máquina virtual que estão disponíveis no cluster hardware onde a máquina virtual está hospedada.

    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```

2. Se o tamanho desejado estiver listado, execute o seguinte comando para redimensionar a máquina virtual.

    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```

    A máquina virtual será reiniciado durante este processo. Após a reinicialização, o sistema operacional existente e o disco de dados será remapeado. Nada no disco temporário serão perdido.

    Use o `--enable-boot-diagnostics` opção habilita o [Diagnóstico de inicialização][boot-diagnostics], para registrar quaisquer erros relacionados a inicialização.

3. Caso contrário, se o tamanho desejado não estiver listado, execute os seguintes comandos desaloque a máquina virtual, redimensioná-la e reinicie a máquina virtual.

    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```

   > [AZURE.WARNING] Também desalocando a máquina virtual libera quaisquer endereços IP dinâmicos atribuídos para a máquina virtual. O sistema operacional e dados discos não são afetados.
   
## <a name="next-steps"></a>Próximas etapas

Para escalabilidade adicional, executar várias instâncias de máquina virtual e dimensionar. Para obter mais informações, consulte [Dimensionar automaticamente máquinas Linux em um conjunto de escala de máquina Virtual][scale-set]. 

<!-- links -->
   
[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md