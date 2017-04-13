<properties
    pageTitle="Aplicar políticas para máquinas virtuais do Azure Gerenciador de recursos | Microsoft Azure"
    description="Como aplicar uma política para uma máquina de Virtual de Windows Gerenciador de recursos do Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/13/2016"
    ms.author="singhkay"/>

# <a name="apply-policies-to-azure-resource-manager-virtual-machines"></a>Aplicar políticas para máquinas virtuais do Azure Gerenciador de recursos

Usando políticas, uma organização pode aplicar várias convenções e regras em toda a empresa. Aplicação do comportamento desejado pode ajudar a atenuar o risco enquanto contribuem para o sucesso da organização. Neste artigo, vamos descreverá como você pode usar o Gerenciador de recursos do Azure políticas para definir o comportamento desejado para máquinas virtuais de sua organização.

A estrutura de tópicos para ver as etapas fazer isso é como abaixo

1. Diretiva de Gerenciador de recursos Azure 101
2. Definir uma política para sua máquina Virtual
3. Criar a política
4. Aplicar a política

## <a name="azure-resource-manager-policy-101"></a>Diretiva de Gerenciador de recursos Azure 101

Para começando a usar o Gerenciador de recursos do Azure políticas, recomendamos ler artigo abaixo e, em seguida, continue com as etapas neste artigo. O artigo a seguir descreve a definição básica e a estrutura de uma política, como políticas obter avaliada e fornece vários exemplos de definições de política.

* [Usar diretiva para gerenciar recursos e controlar o acesso](../resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>Definir uma política para sua máquina Virtual

Um dos cenários comuns para uma empresa pode ser só permitir que seus usuários criar máquinas virtuais de sistemas operacionais específicos que foram testados para ser compatível com um aplicativo de LOB. Usando uma política do Gerenciador de recursos do Azure esta tarefa pode ser feita em algumas etapas. Neste exemplo de política, vamos para permitir que somente Server 2012 R2 data center máquinas virtuais do Windows a ser criado. A definição de política se parece com abaixo

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "MicrosoftWindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "WindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "2012-R2-Datacenter"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

A política acima facilmente pode ser modificada para um cenário em que talvez você queira permitir que qualquer imagem do Centro de dados do Windows Server a ser usado para uma implantação de máquina Virtual com o abaixo para alterar

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*Datacenter"
}
```

#### <a name="virtual-machine-property-fields"></a>Campos de propriedade de máquina virtual

A tabela a seguir descreve as propriedades de máquina Virtual que podem ser usadas como campos em sua definição de política. Para obter mais informações sobre campos de política, consulte o artigo abaixo:

* [Campos e fontes](../resource-manager-policy.md#fields-and-sources)


| Nome do campo     | Descrição                                        |
|----------------|----------------------------------------------------|
| imagePublisher | Especifica o publisher da imagem               |
| imageOffer     | Especifica a oferta para o Editor de imagem escolhido |
| imageSku       | Especifica o SKU para a oferta escolhido             |
| imageVersion   | Especifica a versão de imagem para escolhido SKU     |

## <a name="create-the-policy"></a>Criar a política

Uma política facilmente pode ser criada usando a API REST diretamente ou os cmdlets do PowerShell. Para criar a política, consulte o artigo a seguir:

* [Criar uma política](../resource-manager-policy.md#creating-a-policy)


## <a name="apply-the-policy"></a>Aplicar a política

Depois de criar a política que você precisará aplicá-lo em um escopo definido. O escopo pode ser uma assinatura, grupo de recursos ou até mesmo o recurso. Para aplicar a política, consulte o artigo a seguir:

* [Criar uma política](../resource-manager-policy.md#applying-a-policy)
