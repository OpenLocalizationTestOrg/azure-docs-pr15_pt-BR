<properties
   pageTitle="Solucionar problemas de falhas de extensão de máquina virtual do Windows | Microsoft Azure"
   description="Saiba mais sobre a solução de problemas de falhas de extensão de máquina virtual do Windows Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Solucionar problemas de falhas de extensão de máquina virtual do Windows Azure

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Exibindo o status de extensão
Modelos do Azure gerente de recursos podem ser executados do PowerShell do Azure. Depois que o modelo é executado, o status de extensão pode ser visualizado do Azure Resource Explorer ou as ferramentas de linha de comando.

Aqui está um exemplo:

Azure PowerShell:

      Get-AzureRmVM -ResourceGroupName $RGName -Name $vmName -Status

Aqui está o exemplo de saída:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Solucionar problemas de falhas de extensão

### <a name="re-running-the-extension-on-the-vm"></a>Executar novamente a extensão na VM

Se você estiver executando scripts na máquina virtual usando a extensão de Script personalizado, que às vezes, podem ocorrer um erro onde máquina virtual foi criado com êxito, mas o script falhou. Nestas conditons, a maneira recomendada para recuperar desse erro é remover a extensão e execute o modelo novamente.
Observação: no futuro, essa funcionalidade seria melhor para remover a necessidade de desinstalar a extensão.


#### <a name="remove-the-extension-from-azure-powershell"></a>Remover a extensão do PowerShell do Azure

    Remove-AzureRmVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Depois que a extensão tenha sido removida, o modelo pode ser executado novamente para executar os scripts na máquina virtual.
