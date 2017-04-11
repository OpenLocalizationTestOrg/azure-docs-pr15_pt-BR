<properties
    pageTitle="Migrar os recursos de IaaS do clássico ao Gerenciador de recursos do Azure usando o Azure CLI | Microsoft Azure"
    description="Este artigo explica a migração com suporte plataforma de recursos do clássico ao Gerenciador de recursos do Azure usando CLI do Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrar os recursos de IaaS do clássico ao Gerenciador de recursos do Azure usando CLI do Azure

Estas etapas mostram como usar os comandos do Azure interface de linha (comando) para migrar infraestrutura como um serviço (IaaS) recursos do modelo clássico de implantação para o modelo de implantação do Gerenciador de recursos do Azure. O artigo requer o [Azure CLI](../xplat-cli-install.md).

>[AZURE.NOTE] Todas as operações descritas aqui são idempotente. Se você tiver um problema diferente de um recurso sem suporte ou um erro de configuração, recomendamos que você repita a preparar, anular ou confirmar a operação. A plataforma tente a ação novamente.

## <a name="step-1-prepare-for-migration"></a>Etapa 1: Preparar para a migração

Aqui estão algumas práticas recomendadas que recomendamos como você avaliar migrando recursos de IaaS do clássico para o Gerenciador de recursos:

- Leia a [lista de configurações sem suporte ou recursos](virtual-machines-windows-migration-classic-resource-manager.md). Se você tiver máquinas virtuais que usam configurações sem suporte ou recursos, recomendamos que você aguarde o suporte de configuração do recurso a ser anunciado. Como alternativa, você pode remover esse recurso ou tira que a configuração Habilitar migração se ele atende às suas necessidades.
-   Se você tiver automatizado scripts que implantar sua infraestrutura e aplicativos hoje, tente criar uma configuração de teste semelhante usando esses scripts para a migração. Como alternativa, você pode configurar os ambientes de amostra usando o portal do Azure.

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Etapa 2: Definir sua assinatura e registrar o provedor

Cenários de migração, você precisa configurar o ambiente para ambas as clássico e Gerenciador de recursos. [Instalar o Azure CLI](../xplat-cli-install.md) e [Selecione a sua assinatura](../xplat-cli-connect.md).

Entrar em sua conta.
    
    azure login

Selecione a assinatura Azure usando o comando a seguir.

    azure account set "<azure-subscription-name>"

>[AZURE.NOTE] O registro é uma etapa de tempo, mas ele precisa ser feito uma vez antes de tentar migração. Sem registrar, você verá a seguinte mensagem de erro 

>   *BadRequest: A assinatura não está registrada para a migração.* 

Registrar-se com o provedor de recursos de migração usando o comando a seguir. Observe que em alguns casos, esse comando atinge o tempo limite. No entanto, o registro será bem sucedido.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Aguarde cinco minutos para o registro concluir. Você pode verificar o status da aprovação usando o comando a seguir. Verifique se RegistrationState está `Registered` antes de continuar.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Agora, alterne ILC para o `asm` modo.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Etapa 3: Verifique se que você tem bastante cores de máquina de Virtual do Azure Gerenciador de recursos na região Azure da sua implantação atual ou VNET

Para esta etapa será necessário alternar para `arm` modo. Fazer isso com o comando a seguir.

```
azure config mode arm
```

Você pode usar o seguinte comando CLI para verificar a quantidade de cores que você tem no Gerenciador de recursos do Azure atual. Para saber mais sobre cotas de núcleo, consulte [limites e o Gerenciador de recursos do Azure](../articles/azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Depois que terminar verificando nesta etapa, você pode mudar para o `asm` modo.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Etapa 4: Opção 1 - migrar máquinas virtuais em um serviço na nuvem 

Obtenha a lista de serviços de nuvem usando o seguinte comando e, em seguida, escolha o serviço de nuvem que você deseja migrar. Observe que se as VMs no serviço de nuvem estão em uma rede virtual ou se tiverem funções da web/trabalhador, você receberá uma mensagem de erro.

    azure service list

Execute o seguinte comando para obter o nome de implantação para o serviço de nuvem da saída detalhada. Na maioria dos casos, o nome de implantação é a mesma que o nome do serviço de nuvem.

    azure service show <serviceName> -vv

Prepare as máquinas virtuais no serviço de nuvem para a migração. Você tem duas opções à sua escolha.

Se você deseja migrar VMs para uma rede virtual criado a plataforma, use o comando a seguir.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Se você deseja migrar para uma rede virtual existente no modelo de implantação do Gerenciador de recursos, use o comando a seguir.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> subnetName <vnetName>

Depois que a operação de preparação for bem-sucedido, você poderá examiná a saída detalhada para obter o estado de migração de VMs e garantir que eles estejam no `Prepared` estado.

    azure vm show <vmName> -vv

Verifique a configuração para os recursos preparados usando CLI ou o portal do Azure. Se você não estiver pronto para a migração e quiser voltar para o estado antigo, use o comando a seguir.

    azure service deployment abort-migration <serviceName> <deploymentName>

Se a configuração preparada gostar, você pode Avançar e confirme os recursos usando o comando a seguir.

    azure service deployment commit-migration <serviceName> <deploymentName>


    
## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Etapa 4: Opção 2 - migrar máquinas virtuais em uma rede virtual

Escolha a rede virtual que você deseja migrar. Observe que, se a rede virtual contém funções de web/trabalhador ou VMs com configurações não suportadas, você obterá uma mensagem de erro de validação.

Obter todas as redes virtuais na assinatura usando o comando a seguir.

    azure network vnet list
    
A saída terá aparência semelhante a esta:

![Captura de tela da linha de comando com o nome de rede virtual inteiro realçado.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

No exemplo acima, o **virtualNetworkName** é o nome inteiro **"Grupo classicubuntu16 classicubuntu16"**.

Prepare a rede virtual de sua preferência para a migração usando o comando a seguir.

    azure network vnet prepare-migration <virtualNetworkName>

Verifique a configuração para as máquinas virtuais preparadas usando CLI ou o portal do Azure. Se você não estiver pronto para a migração e quiser voltar para o estado antigo, use o comando a seguir.

    azure network vnet abort-migration <virtualNetworkName>

Se a configuração preparada gostar, você pode Avançar e confirme os recursos usando o comando a seguir.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Etapa 5: Migrar uma conta de armazenamento

Quando terminar de migrar as máquinas virtuais, recomendamos que você migra a conta de armazenamento.

Preparar a conta de armazenamento para a migração usando o comando a seguir

    azure storage account prepare-migration <storageAccountName>

Verifique a configuração da conta de armazenamento preparado usando CLI ou o portal do Azure. Se você não estiver pronto para a migração e quiser voltar para o estado antigo, use o comando a seguir.

    azure storage account abort-migration <storageAccountName>

Se a configuração preparada gostar, você pode Avançar e confirme os recursos usando o comando a seguir.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Próximas etapas

- [Suporte a plataforma migração de recursos de IaaS do clássico para o Gerenciador de recursos](virtual-machines-windows-migration-classic-resource-manager.md)
- [Técnico detalhamento da plataforma suportada migração de clássico ao Gerenciador de recursos](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
