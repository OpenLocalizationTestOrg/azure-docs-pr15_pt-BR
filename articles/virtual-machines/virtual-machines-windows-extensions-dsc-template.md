<properties
   pageTitle="Desejado de modelo de configuração de estado de Gerenciador de recurso | Microsoft Azure"
   description="Definição de modelo de recurso Gerenciador de configuração de estado desejado no Azure com exemplos e solução de problemas"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="windows-vmss-and-desired-state-configuration-with-azure-resource-manager-templates"></a>VMSS do Windows e configuração de estado desejado com modelos do Gerenciador de recursos do Azure
Este artigo descreve o modelo do Gerenciador de recursos para o [manipulador de extensão de configuração de estado desejado](virtual-machines-windows-extensions-dsc-overview.md). 

## <a name="template-example-for-a-windows-vm"></a>Exemplo de modelo para uma máquina virtual Windows

O trecho a seguir vai para a seção de recursos do modelo.

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }

```

## <a name="template-example-for-windows-vmss"></a>Exemplo de modelo para VMSS do Windows

Um nó VMSS possui uma seção "propriedades" com o "VirtualMachineProfile", "extensionProfile" atributo. DSC é adicionada em "extensões". 

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": true,
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

## <a name="detailed-settings-information"></a>Informações detalhadas de configurações

O seguinte esquema é para a parte das configurações da extensão do Azure DSC em um modelo do Gerenciador de recursos do Azure.

```json

"settings": {
  "wmfVersion": "latest",
  "configuration": {
    "url": "http://validURLToConfigLocation",
    "script": "ConfigurationScript.ps1",
    "function": "ConfigurationFunction"
  },
  "configurationArguments": {
    "argument1": "Value1",
    "argument2": "Value2"
  },
  "configurationData": {
    "url": "https://foo.psd1"
  },
  "privacy": {
    "dataCollection": "enable"
  },
  "advancedOptions": {
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
    }
  } 
},
"protectedSettings": {
  "configurationArguments": {
    "parameterOfTypePSCredential1": {
      "userName": "UsernameValue1",
      "password": "PasswordValue1"
    },
    "parameterOfTypePSCredential2": {
      "userName": "UsernameValue2",
      "password": "PasswordValue2"
    }
  },
  "configurationUrlSasToken": "?g!bber1sht0k3n",
  "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}

```

## <a name="details"></a>Detalhes
| Nome da propriedade | Tipo | Descrição |
| --- | --- | --- |
| settings.wmfVersion | cadeia de caracteres | Especifica a versão do Windows Management Framework que deve ser instalado em sua máquina virtual. Definir esta propriedade para 'mais recente' instala a versão mais atualizada do WMF. Os valores possíveis somente atuais para essa propriedade são **'4.0', '5.0', ' 5.0PP' e 'mais recente'**. Esses valores possíveis estão sujeitos a atualizações. O valor padrão é 'mais recente'.|
| Settings.Configuration.URL | cadeia de caracteres | Especifica o local de URL do qual deseja baixar o arquivo de zip de configuração de DSC. Se a URL fornecida requer um token SAS para o access, você precisa definir a propriedade protectedSettings.configurationUrlSasToken como o valor de seu token SAS. Essa propriedade é necessária se settings.configuration.script e/ou settings.configuration.function são definidos. |
| Settings.Configuration.script | cadeia de caracteres | Especifica o nome de arquivo do script que contém a definição da configuração DSC. Este script deve ser na pasta raiz do arquivo zip baixado da URL especificada pela propriedade configuration.url. Essa propriedade é necessária se settings.configuration.url e/ou settings.configuration.script são definidos. |
| Settings.Configuration.Function | cadeia de caracteres | Especifica o nome da sua configuração de DSC. A configuração denominada deve estar contida no script definido pelo configuration.script. Essa propriedade é necessária se settings.configuration.url e/ou settings.configuration.function são definidos. |
| settings.configurationArguments | Coleção | Define os parâmetros que você gostaria de passar para a configuração de DSC. Essa propriedade não está criptografada. |
| settings.configurationData.url | cadeia de caracteres | Especifica a URL do qual deseja baixar o arquivo de dados (.pds1) de configuração para usar como entrada para a sua configuração de DSC. Se a URL fornecida requer um token SAS para o access, você precisa definir a propriedade protectedSettings.configurationDataUrlSasToken como o valor de seu token SAS.|
| settings.privacy.dataEnabled | cadeia de caracteres | Habilita ou desabilita a coleção de telemetria. Os valores possíveis somente para essa propriedade são **'Ativar', 'Desativar', ', ou $null**. Deixar essa propriedade em branco ou nula permite telemetria. O valor padrão é '. [Mais informações](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings | Coleção | Define locais alternativos do qual deseja baixar o WMF. [Mais informações](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments | Coleção | Define os parâmetros que você gostaria de passar para a configuração de DSC. Essa propriedade está criptografada. |
| protectedSettings.configurationUrlSasToken | cadeia de caracteres | Especifica o token SAS para acessar o URL definido pelo configuration.url. Essa propriedade está criptografada. |
| protectedSettings.configurationDataUrlSasToken | cadeia de caracteres | Especifica o token SAS para acessar o URL definido pelo configurationData.url. Essa propriedade está criptografada. |

## <a name="settings-vs-protectedsettings"></a>Configurações versus ProtectedSettings
Todas as configurações são salvas em um arquivo de texto de configurações na máquina virtual.
Propriedades em 'Configurações' são propriedades públicas porque eles não são criptografados no arquivo de texto de configurações.
Propriedades em 'protectedSettings' são criptografadas com um certificado e não são mostradas em texto sem formatação neste arquivo na VM.

Se a configuração precisa de credenciais, eles podem ser incluídos em protectedSettings:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        }
    }
}
```

## <a name="example"></a>Exemplo

O exemplo a seguir deriva da seção "Introdução" da [página Visão geral de manipulador de extensão de DSC](virtual-machines-windows-extensions-dsc-overview.md).
Este exemplo usa o recurso Gerenciador de modelos em vez de cmdlets para implantar a extensão. Salvar a configuração de "IisInstall.ps1", coloque-o em uma. Arquivo ZIP e carregue o arquivo em uma URL acessível. Este exemplo usa o armazenamento de blob do Microsoft Azure, mas é possível fazer o download. Arquivos de qualquer local aleatório ZIP.

No modelo do Gerenciador de recursos do Azure, o código a seguir instrui a máquina virtual para baixar o arquivo correto e executar a função apropriada do PowerShell:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
    } 
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="updating-from-the-previous-format"></a>Atualizando do formato anterior
Quaisquer configurações de formato anterior (contendo as propriedades públicas ModulesUrl, ConfigurationFunction, SasToken ou propriedades) automaticamente adaptar para o formato atual e execute exatamente como antes.

Esquema a seguir mostra quais anterior configurações esquema semelhante a:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1" 
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": { 
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Veja aqui como o formato anterior se adapta para o formato atual:

| Nome da propriedade | Equivalente do esquema anterior |
| --- | --- |
| settings.wmfVersion | Configurações. WMFVersion |
| Settings.Configuration.URL | Configurações. ModulesUrl |
| Settings.Configuration.script | Primeira parte das configurações. ConfigurationFunction (antes '\\\\') |
| Settings.Configuration.Function | Segunda parte das configurações. ConfigurationFunction (após '\\\\') |
| settings.configurationArguments | Configurações. Propriedades |
| settings.configurationData.url | protectedSettings.DataBlobUri (sem token SAS) |
| settings.privacy.dataEnabled | Configurações. Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings | Configurações. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments | protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken | Configurações. SasToken |
| protectedSettings.configurationDataUrlSasToken | Símbolo SAS de protectedSettings.DataBlobUri |


## <a name="troubleshooting---error-code-1100"></a>Solução de problemas - código de erro 1100
Código de erro 1100 indica que não há um problema com a entrada do usuário para a extensão de DSC.
O texto desses erros é variável e pode ser alteradas.
Aqui estão alguns dos erros que pode ocorrer e como você pode corrigi-los.

### <a name="invalid-values"></a>Valores inválidos
"Privacy.dataCollection é '{0}'. Os valores possíveis somente são ", 'Ativar' e 'Desabilitar'" "WmfVersion é '{0}'. Somente os valores possíveis são... e 'recentes' "

Problema: Um valor fornecido não é permitido.

Solução: Altere o valor inválido para um valor válido. Consulte a tabela na seção de detalhes.

### <a name="invalid-url"></a>URL inválida
"ConfigurationData.url é '{0}'. Isso não é uma URL válida""DataBlobUri é '{0}'. Isso não é uma URL válida""Configuration.url é '{0}'. Isso não é uma URL válida"

Problema: A fornecido a que URL não é válida.

Solução: Verifique todas as URLs fornecidos. Certificar-se de que todas as URLs resolvem para locais válidos que a extensão pode acessar no computador remoto.

### <a name="invalid-configurationargument-type"></a>Tipo de ConfigurationArgument inválido
"Digite configurationArguments inválidas" {0}

Problema: A propriedade ConfigurationArguments não pode resolver a um objeto de hash. 

Solução: Verifique sua propriedade ConfigurationArguments uma tabela de hash. Siga o formato fornecido no exemplo anterior. Atenção às cotações, vírgulas e chaves.

### <a name="duplicate-configurationarguments"></a>Duplicar ConfigurationArguments
"Encontrada argumentos duplicados '{0}' em configurationArguments protegidos e público"

Problema: Os ConfigurationArguments nas configurações de públicas e o ConfigurationArguments nas configurações protegidas contêm propriedades com o mesmo nome.

Solução: Remova uma das propriedades duplicadas.

### <a name="missing-properties"></a>Propriedades ausentes
"Configuration.function requer que configuration.url ou configuration.module é especificado"

"Configuration.url exige que configuration.script seja especificada"

"Configuration.script exige que configuration.url seja especificada"

"Configuration.url exige que configuration.function seja especificada"

"ConfigurationUrlSasToken exige que configuration.url seja especificada"

"ConfigurationDataUrlSasToken exige que configurationData.url seja especificada"

Problema: Uma propriedade definida necessidades outra propriedade que está falta.

Soluções: 
- Fornece a propriedade ausente.
- Remova a propriedade que precisa de propriedade ausente.


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre DSC e conjuntos de escala de máquina virtual no [Usando de escala de máquina Virtual conjuntos com a extensão de DSC do Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)

Encontre mais detalhes sobre [o gerenciamento de credenciais seguro do DSC](virtual-machines-windows-extensions-dsc-credentials.md). 

Para obter mais informações sobre o manipulador de extensão do Azure DSC, consulte [Introdução ao manipulador de extensão de configuração de estado do Azure desejado](virtual-machines-windows-extensions-dsc-overview.md). 

Para obter mais informações sobre DSC do PowerShell, [visite o Centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 
