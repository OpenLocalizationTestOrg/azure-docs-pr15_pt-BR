<properties
    pageTitle="Criação de escala de máquina Virtual define para escala | Microsoft Azure"
    description="Saiba mais sobre como criar seus conjuntos de escala de máquina Virtual de escala"
    keywords="computador de virtual Linux, escala de máquina virtual define" 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="gatneil"/>

# <a name="designing-vm-scale-sets-for-scale"></a>Criação de escala de máquina virtual define de escala

Este tópico aborda considerações de design para conjuntos de escala de máquina Virtual. Para obter informações sobre o que são conjuntos de escala de máquina Virtual, consulte [Visão geral de conjuntos de escala de máquina Virtual](virtual-machine-scale-sets-overview.md).


## <a name="storage"></a>Armazenamento

Um conjunto de escala usa contas de armazenamento para armazenar unidades de sistema operacional dos VMs no conjunto. Recomendamos uma proporção de 20 VMs por conta de armazenamento ou menos. Também é recomendável que você difundir através do alfabeto os caracteres iniciais dos nomes de conta de armazenamento. Isso ajuda a distribuir a carga em diferentes sistemas internos. Por exemplo, o modelo a seguir, podemos usar a função de modelo do Gerenciador de recursos de uniqueString para gerar o hash de prefixo que é anexados a nomes de contas de armazenamento: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).


## <a name="overprovisioning"></a>Excesso de provisionamento

Começando com a versão da API "2016-03-30", conjuntos de escala de máquina virtual padrão é excesso de "provisionamento" VMs. Com excesso de provisionamento ativado, a escala definir realmente gira o VMs mais do que você solicitado para, em seguida, exclui as VMs extras que girados o último. Excesso de provisionamento melhora provisionamento taxas de sucesso. Você não será cobrado por essas VMs extras, e eles não são contadas seus limites de cota.

Embora excesso de provisionamento melhorar taxas de êxito de provisionamento, ele pode causar comportamento confuso para um aplicativo que não foi projetado para lidar com VMs desaparecer sem aviso prévio. Para ativar o excesso de provisionamento desativado, verifique se você tem a seguinte sequência no seu modelo: "overprovision": "false". Mais detalhes podem ser encontradas na [documentação de API REST do conjunto de escala do máquina virtual](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Se você desativar excesso de provisionamento, você pode obter ausente com uma taxa maior de VMs por conta de armazenamento, mas não recomendamos indo acima 40.


## <a name="limits"></a>Limites
Um conjunto de escala criado em uma imagem personalizada (um criados por você) deve criar todos os VHDs de disco do sistema operacional dentro de uma conta de armazenamento. Como resultado, o máximo recomendado número de VMs em um conjunto de escala criado em uma imagem personalizada é 20. Se você desativar excesso de provisionamento, você pode ir até 40.

Um conjunto de escala criado em uma imagem de plataforma está atualmente limitado a 100 VMs (recomendamos 5 contas de armazenamento para essa escala).

Para mais VMs que esses limites permitem, você precisa implantar vários conjuntos de escala, conforme mostrado [neste](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)modelo.