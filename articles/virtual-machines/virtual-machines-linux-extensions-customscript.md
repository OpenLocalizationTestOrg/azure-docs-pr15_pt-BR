<properties
   pageTitle="Scripts personalizados em Linux VMs | Microsoft Azure"
   description="Automatizar tarefas de configuração do Linux VM usando a extensão de Script personalizado"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="nepeters"/>

# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Usando a extensão de Script personalizado Azure com máquinas virtuais Linux

A extensão de Script personalizado downloads e executa scripts em Azure máquinas virtuais. Essa extensão é útil para configuração de implantação de postagem, instalação do software ou qualquer outra configuração / tarefa de gerenciamento. Scripts podem ser baixados do armazenamento do Azure ou outro local de internet acessíveis, ou fornecidos para a extensão de tempo de execução. A extensão de Script personalizado integra-se com os modelos do Gerenciador de recursos do Azure e também pode ser executada usando a CLI do Azure, PowerShell, portal Azure ou a API de REST de máquina Virtual do Azure.

Este documento fornece detalhes sobre como usar a extensão de Script personalizado do CLI do Azure e de um modelo do Gerenciador de recursos do Azure e também detalha etapas de solução de problemas nos sistemas Linux.

## <a name="extension-configuration"></a>Configuração de extensão

A configuração de extensão de Script personalizado especifica coisas como local de script e o comando para ser executado. Essa configuração pode ser armazenada em arquivos de configuração, especificada na linha de comando ou em um modelo do Gerenciador de recursos do Azure. Dados confidenciais podem ser armazenados em uma configuração protegida, que é criptografada e descriptografar apenas na máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos como uma senha.

### <a name="public-configuration"></a>Configuração pública

Esquema:

- **commandToExecute**: (obrigatório, cadeia de caracteres) o script de ponto de entrada para executar
- **fileUris**: (opcional, matriz de cadeia de caracteres) as URLs para arquivos devem ser baixadas.
- **carimbo de hora** (opcional, inteiro) use este campo somente para disparar um reexecutar do script, alterando o valor desse campo.

```none
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Configuração protegida

Esquema:

- **commandToExecute**: (opcional, string) o script de ponto de entrada para executar. Use este campo se o comando contiver segredos, como senhas.
- **storageAccountName**: (opcional, string) o nome da conta de armazenamento. Se você especificar credenciais de armazenamento, todos os fileUris devem ser URLs para Blobs do Azure.
- **storageAccountKey**: (opcional, string) a tecla de acesso da conta de armazenamento.


```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>CLI Azure

Ao usar a CLI do Azure para executar a extensão de Script personalizado, crie um arquivo de configuração ou arquivos que contém no mínimo o uri do arquivo e o comando de execução de scripts.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /scirpt-config.json
```

Opcionalmente, o comando pode ser executado usando o `--public-config` e `--private-config` opção, que permite a configuração deve ser especificado durante a execução e sem um arquivo de configuração separado.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

### <a name="azure-cli-examples"></a>Exemplos CLI Azure

**Exemplo 1** - configuração de público com o arquivo de script.

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
  "commandToExecute": "./hello.sh"
}
```

Comando CLI Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Exemplo 2** - configuração público com nenhum arquivo de script.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando CLI Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Exemplo 3** - um arquivo de configuração público é usado para especificar o arquivo de script URI e um arquivo de configuração protegida é usado para especificar o comando a ser executado.

Arquivo de configuração público:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
}
```

Arquivo de configuração protegida:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Comando CLI Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path ./public.json --private-config-path ./protected.json
```

## <a name="resource-manager-template"></a>Modelo do Gerenciador de recursos

A extensão de Script do Azure personalizada pode ser executada em tempo de implantação de máquina Virtual usando um modelo do Gerenciador de recursos. Para fazer isso, adicione JSON formatado corretamente para o modelo de implantação.

### <a name="resource-manager-examples"></a>Exemplos do Gerenciador de recursos

**Exemplo 1** - configuração de público.

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**Exemplo 2** - comando de execução na configuração protegida.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Consulte .net Core música Store Demo para um exemplo completo - [Música Store Demo](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db).

## <a name="troubleshooting"></a>Solução de problemas

Quando a extensão de Script personalizado é executado, o script é criado ou baixado em um diretório semelhante ao seguinte exemplo. A saída de comando também é salva nesse diretório em `stdout` e `stderr` arquivo.

```none
/var/lib/azure/custom-script/download/0/
```

A extensão de Script do Azure produz um log, que pode ser encontrado aqui.

```none
/var/log/azure/customscript/handler.log
```

O estado de execução da extensão de Script personalizado também pode ser recuperado com a CLI do Azure.

```none
azure vm extension get <resource-group> <vm-name>
```

A saída parece com o seguinte texto:

```none
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre outras extensões de Script de máquina virtual, consulte [Visão geral de extensão de Script do Azure para Linux](./virtual-machines-linux-extensions-features.md).