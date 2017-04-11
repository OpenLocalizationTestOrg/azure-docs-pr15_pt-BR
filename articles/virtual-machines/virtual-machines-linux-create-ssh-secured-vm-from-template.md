<properties
    pageTitle="Criar uma VM Linux usando um modelo Azure | Microsoft Azure"
    description="Crie uma VM Linux no Azure usando um modelo do Gerenciador de recursos do Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/24/2016"
    ms.author="v-livech"/>

# <a name="create-a-linux-vm-using-an-azure-template"></a>Criar uma VM Linux usando um modelo do Azure

Este artigo mostra como implantar rapidamente uma máquina Virtual de Linux no Azure usando um modelo do Azure.  O artigo requer:

- uma conta do Microsoft Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- o [Azure CLI](../xplat-cli-install.md) conectado com `azure login`.

- o modo de Gerenciador de recursos do Azure _deve estar no_ Azure CLI `azure config mode arm`.

Você pode implantar um modelo de Linux VM também rapidamente usando o [portal do Azure](virtual-machines-linux-quick-create-portal.md).

## <a name="quick-command-summary"></a>Resumo de comando rápido

```bash
azure group create \
-n myResourceGroup \
-l westus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Explicação passo a passo detalhada

Modelos permitem criar VMs no Azure com configurações que você deseja personalizar durante a inicialização, configurações, como nomes de usuário e nomes de host. Neste artigo, nós são iniciar um modelo do Azure utilizando uma VM Ubuntu juntamente com um grupo de segurança de rede (NSG) com porta 22 aberto para SSH.

Modelos de Gerenciador de recursos Azure são arquivos JSON que podem ser usados para tarefas unitárias simples como iniciar uma VM Ubuntu como concluído neste artigo.  Modelos do Azure também podem ser usados para construir configurações Azure complexas de ambientes inteiros como uma pilha de implantação de teste, desenvolvimento ou produção.

## <a name="create-the-linux-vm"></a>Criar a máquina virtual Linux

O exemplo de código a seguir mostra como chamar `azure group create` para criar um grupo de recursos e implantar um protegido SSH Linux VM ao mesmo tempo usando [Este modelo de Gerenciador de recursos do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Lembre-se de que seu exemplo que você precisa usar nomes que são exclusivos para seu ambiente. Este exemplo usa `myResourceGroup` como o nome do grupo de recursos, e `myVM` como o nome da máquina virtual.

```bash
azure group create \
--name myResourceGroup \
--location westus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

A saída deve parecer com o seguinte bloco de saída:

```bash
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Esse exemplo implantado uma máquina virtual usando o `--template-uri` parâmetro.  Você também pode baixar ou criar um modelo localmente e passar o modelo usando o `--template-file` parâmetro com um caminho para o arquivo de modelo como um argumento. A CLI do Azure solicita parâmetros necessários pelo modelo.

## <a name="next-steps"></a>Próximas etapas

Pesquise na [Galeria de modelos](https://azure.microsoft.com/documentation/templates/) para descobrir quais estruturas de aplicativo para implantar o próximo.
