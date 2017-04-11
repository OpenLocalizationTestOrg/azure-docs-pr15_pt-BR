<properties
   pageTitle="Solucionar problemas de falhas de extensão Linux VM | Microsoft Azure"
   description="Saiba mais sobre falhas de extensão de máquina virtual do Azure Linux de solução de problemas"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="troubleshooting-azure-linux-vm-extension-failures"></a>Solucionar problemas de falhas de extensão de máquina virtual do Azure Linux

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Exibindo o status de extensão
Modelos do Azure gerente de recursos podem ser executados da CLI Azure. Depois que o modelo é executado, o status de extensão pode ser visualizado do Azure Resource Explorer ou as ferramentas de linha de comando.

Aqui está um exemplo:

Azure CLI:

      azure vm get-instance-view


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

## <a name="troubleshooting-extenson-failures"></a>Extenson falhas de solução de problemas:

### <a name="re-running-the-extension-on-the-vm"></a>Executar novamente a extensão na VM

Se você estiver executando scripts na máquina virtual usando a extensão de Script personalizado, que às vezes, podem ocorrer um erro onde máquina virtual foi criado com êxito, mas o script falhou. Nestas conditons, a maneira recomendada para recuperar desse erro é remover a extensão e execute o modelo novamente.
Observação: no futuro, essa funcionalidade seria melhor para remover a necessidade de desinstalar a extensão.

#### <a name="remove-the-extension-from-azure-cli"></a>Remover a extensão do Azure CLI

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

Onde "nome do publsher" corresponde ao tipo de extensão da saída de "máquina virtual azure get-instância-view" e nome é o nome do recurso da extensão do modelo

Depois que a extensão tenha sido removida, o modelo pode ser executado novamente para executar os scripts na máquina virtual.
