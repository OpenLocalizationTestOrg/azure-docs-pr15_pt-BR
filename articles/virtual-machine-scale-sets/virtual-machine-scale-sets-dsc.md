<properties
   pageTitle="Usando desejado configuração estada com conjuntos de escala de máquina Virtual | Microsoft Azure"
   description="Usando escala de máquina Virtual define com a extensão de DSC Azure"
   services="virtual-machine-scale-sets"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machine-scale-sets"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Usando escala de máquina Virtual define com a extensão de DSC Azure

[Conjuntos de escala de máquina virtual (VMSS)](virtual-machine-scale-sets-overview.md) pode ser usado com o manipulador de extensão de [Configuração de estado de desejado (DSC) do Azure](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md) . VMSS fornece uma maneira de implantar e gerenciar um grande número de máquinas virtuais e consumidor pode aumentar e reduzir em resposta ao carregar. DSC é usada para configurar as VMs como eles ficar online para que eles estão executando o software de produção.

## <a name="differences-between-deploying-to-vm-and-vmss"></a>Diferenças entre a implantação para máquina virtual e VMSS

A estrutura de modelo subjacente para VMSS é um pouco diferente de uma única VM. Especificamente, uma única VM implanta extensões sob o nó de "virtualMachines". Há uma entrada do tipo "extensões" onde DSC é adicionado ao modelo

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Um nó VMSS possui uma seção "propriedades" com o "VirtualMachineProfile", "extensionProfile" atributo. DSC é adicionada em "extensões"

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-vmss"></a>Comportamento de VMSS

O comportamento de VMSS é idêntico ao comportamento de uma única VM. Quando uma nova VM é criada, ele está provisionado automaticamente com a extensão de DSC. Se for necessária uma versão mais recente do WMF pela extensão, a máquina virtual será reiniciado antes de entrar online. Depois que ele está online, ele baixa a configuração de DSC. zip e provisioná-la na máquina virtual. Mais detalhes podem ser encontradas na [Visão geral do Azure extensão DSC](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md).

## <a name="next-steps"></a>Próximas etapas ##
Examine o [modelo do Gerenciador de recursos do Azure para a extensão de DSC](../virtual-machines/virtual-machines-windows-extensions-dsc-template.md).

Saiba como a [extensão de DSC com segurança lida com as credenciais](../virtual-machines/virtual-machines-windows-extensions-dsc-credentials.md). 

Para obter mais informações sobre o manipulador de extensão do Azure DSC, consulte [Introdução ao manipulador de extensão de configuração de estado do Azure desejado](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md). 

Para obter mais informações sobre DSC do PowerShell, [visite o Centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 


