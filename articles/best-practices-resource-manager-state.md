<properties
    pageTitle="Estado de tratamento no Gerenciador de recursos de modelos | Microsoft Azure"
    description="Mostra recomendados abordagens para usar objetos complexos para compartilhar dados de estado com modelos de Gerenciador de recursos do Azure e vinculado"
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="tomfitz"/>

# <a name="sharing-state-in-azure-resource-manager-templates"></a>Compartilhar estado em modelos do Gerenciador de recursos do Azure

Este tópico mostra as práticas recomendadas para gerenciar e compartilhar estado dentro de modelos. Os parâmetros e variáveis mostrados neste tópico são exemplos dos tipos de objetos que você pode definir para organizar convenientemente as suas necessidades de implantação. Esses exemplos, você pode implementar seus próprios objetos com valores de propriedade que faz sentido para seu ambiente.

Este tópico é parte de um white paper maior. Para ler o papel completo, baixe [Considerações de modelos de Gerenciador de recursos do mundo classe e práticas comprovadas](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).


## <a name="provide-standard-configuration-settings"></a>Fornecer configurações padrão

Em vez de oferecer um modelo que fornece flexibilidade total e inúmeros variações, um padrão comum é fornecem uma seleção de configurações conhecidas. Na verdade, os usuários possam selecionar tamanhos de camiseta padrão como área restrita, pequena, média e grande. Outros exemplos de tamanhos de camiseta são ofertas de produtos, como comunidade edition ou enterprise edition. Em outros casos, pode ser configurações de carga de trabalho específica de uma tecnologia – como reduzir o mapa ou nenhum sql.

Com objetos complexos, você pode criar variáveis que contêm coleções de dados, às vezes conhecidos como "pacotes de propriedade" e usar esses dados para orientar a declaração de recurso no seu modelo. Essa abordagem fornece boas, conhecidas configurações de tamanhos variados pré-configuradas para clientes. Sem configurações conhecidas, os usuários do modelo devem determinar cluster dimensionamento por conta própria, fator em restrições de recursos de plataforma e fazer cálculos para identificar as partições resultante de contas de armazenamento e outros recursos (devido a restrições de recursos e de tamanho de cluster). Além de fazer uma melhor experiência do cliente, algumas configurações conhecidas são mais fáceis de suporte e pode ajudá-lo a oferecer um nível mais alto de densidade.

O exemplo a seguir mostra como definir variáveis que contêm objetos complexos para representar conjuntos de dados. Os conjuntos de definem valores que são usados para o tamanho da máquina virtual, configurações de rede, configurações do sistema operacional e disponibilidade.

    "variables": {
      "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      },
      "tshirtSizeMedium": {
        "vmSize": "Standard_A3",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1],
          "jumpbox": 0
        }
      },
      "tshirtSizeLarge": {
        "vmSize": "Standard_A4",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
        "vmCount": 3,
        "slaveCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1,1],
          "jumpbox": 0
        }
      },
      "osSettings": {
        "scripts": [
          "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
          "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
        ],
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "14.04.2-LTS",
          "version": "latest"
        }
      },
      "networkSettings": {
        "vnetName": "[parameters('virtualNetworkName')]",
        "addressPrefix": "10.0.0.0/16",
        "subnets": {
          "dmz": {
            "name": "dmz",
            "prefix": "10.0.0.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          },
          "data": {
            "name": "data",
            "prefix": "10.0.1.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          }
        }
      },
      "availabilitySetSettings": {
        "name": "pgsqlAvailabilitySet",
        "fdCount": 3,
        "udCount": 5
      }
    }

Observe que a variável **tshirtSize** concatena o tamanho de camiseta fornecido por meio de um parâmetro (**pequeno**, **Médio**, **grande**) para o texto **tshirtSize**. Use essa variável para recuperar a variável de objeto complexo associado nesse tamanho de camiseta.

Você pode fazer referência a essas variáveis posteriormente no modelo. A capacidade de referência variáveis nomeados e suas propriedades simplifica a sintaxe de modelo e torna mais fácil compreender o contexto. O exemplo a seguir define um recurso para implantar usando os objetos mostrados anteriormente para definir valores. Por exemplo, o tamanho de máquina virtual é definido recuperando o valor para `variables('tshirtSize').vmSize` enquanto o valor para o tamanho do disco é recuperado do `variables('tshirtSize').diskSize`. Além disso, o URI para um modelo vinculado é definido com o valor para `variables('tshirtSize').vmTemplate`.

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
            "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## <a name="pass-state-to-a-template"></a>Passar o estado a um modelo

Compartilhar o estado em um modelo por meio de parâmetros que você fornecer diretamente durante a implantação.

A tabela a seguir lista parâmetros comumente usados em modelos.

Nome | Valor | Descrição
---- | ----- | -----------
local    | Cadeia de caracteres de uma lista restrita de regiões Azure   | O local onde os recursos são implantados.
storageAccountNamePrefix    | Cadeia de caracteres    | Nome DNS exclusivo para a conta de armazenamento onde discos da VM são colocados
nome_do_domínio  | Cadeia de caracteres    | Nome de domínio na máquina virtual jumpbox publicamente acessível no formato: **{nome_do_domínio}. { local}.cloudapp.com** por exemplo: **mydomainname.westus.cloudapp.azure.com**
adminUsername   | Cadeia de caracteres    | Nome de usuário para as VMs
adminPassword   | Cadeia de caracteres    | Senha para VMs
tshirtSize  | Cadeia de caracteres de uma lista restrita de oferecidas tamanhos de camiseta   | O tamanho da unidade de escala nomeado provisionar. Por exemplo, "Pequeno", "Médio", "Grande"
virtualNetworkName  | Cadeia de caracteres    | Nome da rede virtual que o consumidor deseja usar.
enableJumpbox   | Cadeia de caracteres de uma lista restrita (ativado/desativado) | Parâmetro que identifica se deseja habilitar uma jumpbox para o ambiente. Valores: "habilitado", "desativado"

O parâmetro de **tshirtSize** usado na seção anterior é definido como:

    "parameters": {
      "tshirtSize": {
        "type": "string",
        "defaultValue": "Small",
        "allowedValues": [
          "Small",
          "Medium",
          "Large"
        ],
        "metadata": {
          "Description": "T-shirt size of the MongoDB deployment"
        }
      }
    }


## <a name="pass-state-to-linked-templates"></a>Passar o estado a modelos vinculados

Ao conectar a modelos vinculados, você geralmente usa uma mistura de estático e gerado variáveis.

### <a name="static-variables"></a>Variáveis estáticas

Variáveis estáticas muitas vezes são usados para fornecer valores base, como URLs, que são usados em um modelo.

No seguinte trecho modelo, `templateBaseUrl` Especifica o local raiz para o modelo no GitHub. A próxima linha cria uma nova variável `sharedTemplateUrl` que concatena a URL base com o nome conhecido do modelo de recursos compartilhados. Abaixo dessa linha, uma variável de objeto complexo é usada para armazenar um tamanho de camiseta, onde a URL base é concatenada para o local do modelo de configuração conhecida e armazenada na `vmTemplate` propriedade.

A vantagem dessa abordagem é que se o local do modelo for alterado, você só precisa alterar a variável estática em um lugar, que passa em todo os modelos vinculados.

    "variables": {
      "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
      "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "slaveCount": 1,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      }
    }

### <a name="generated-variables"></a>Variáveis gerados

Além de variáveis estáticas, diversas variáveis são gerados dinamicamente. Esta seção identifica alguns dos tipos comuns de variáveis gerados.

#### <a name="tshirtsize"></a>tshirtSize

Você está familiarizado com essa variável gerado dos exemplos acima.

#### <a name="networksettings"></a>networkSettings

Em uma capacidade, recurso ou modelo de solução de escopo de ponta a ponta, os modelos vinculados normalmente criam recursos existentes em uma rede. Uma abordagem simples é usar um objeto complexo para armazenar as configurações de rede e passá-los para modelos vinculados.

Um exemplo de comunicação de configurações de rede pode ser visto abaixo.

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

#### <a name="availabilitysettings"></a>availabilitySettings

Recursos criados em modelos vinculados geralmente são colocados em um conjunto de disponibilidade. No exemplo a seguir, o nome do conjunto de disponibilidade é especificado e também os domínios de falhas e atualização contam usar.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Se precisar de vários conjuntos de disponibilidade (por exemplo, uma para nós mestres) e outra para nós de dados, você pode usar um nome como um prefixo, especificar vários conjuntos de disponibilidade, ou siga o modelo mostrado anteriormente para a criação de uma variável para um determinado tamanho de camiseta.

#### <a name="storagesettings"></a>storageSettings

Detalhes de armazenamento geralmente são compartilhados com modelos vinculados. No exemplo abaixo, um objeto *storageSettings* fornece detalhes sobre os nomes de conta e o contêiner de armazenamento.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

#### <a name="ossettings"></a>osSettings

Com modelos vinculados, você talvez precise passar configurações do sistema operacional para vários tipos de nós em tipos diferentes de configuração conhecida. Um objeto complexo é uma maneira fácil de armazenar e compartilhar informações do sistema operacional e também torna mais fácil dar suporte a várias opções de sistema operacional para implantação.

O exemplo a seguir mostra um objeto para *osSettings*:

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

#### <a name="machinesettings"></a>machineSettings

Uma variável gerada, *machineSettings* é um objeto complexo contendo uma mistura de variáveis de núcleo para a criação de uma máquina virtual. As variáveis incluem o nome de usuário de administrador e senha, um prefixo para os nomes de máquina virtual e uma referência de imagem do sistema operacional.

    "machineSettings": {
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "machineNamePrefix": "mongodb-",
        "osImageReference": {
            "publisher": "[variables('osFamilySpec').imagePublisher]",
            "offer": "[variables('osFamilySpec').imageOffer]",
            "sku": "[variables('osFamilySpec').imageSKU]",
            "version": "latest"
        }
    },

Observe que *osImageReference* recupera os valores da variável *osSettings* definida no modelo principal. Isso significa que você pode alterar facilmente o sistema operacional para uma máquina virtual — inteiramente ou com base na preferência de um consumidor de modelo.

#### <a name="vmscripts"></a>vmScripts

O objeto *vmScripts* contém detalhes sobre os scripts para baixar e executar em uma instância de máquina virtual, incluindo externas e internas referências. Fora de referências incluem a infraestrutura. Referências de interior incluem o software instalado instalado e a configuração.

Use a propriedade *scriptsToDownload* para listar os scripts para baixar para a máquina virtual. Esse objeto também contém referências a argumentos de linha de diferentes tipos de ações. Essas ações incluem executar a instalação padrão para cada nó individual, uma instalação executada depois que todos os nós são implantados e quaisquer scripts adicionais que podem ser específicos a um determinado modelo.

Este exemplo é de um modelo usado para implantar MongoDB, que requer um arbitrador oferecer alta disponibilidade. O *arbiterNodeInstallCommand* foi adicionada ao *vmScripts* para instalar o arbitrador.

A seção de variáveis é onde você encontrará as variáveis que definem o texto específico para executar o script com os valores adequados.

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## <a name="return-state-from-a-template"></a>Retornar o estado de um modelo

Não apenas você pode passar dados em um modelo, você também pode compartilhar dados para o modelo de chamada. Na seção **saídas** de um modelo vinculado, você pode fornecer pares de chave/valor que podem ser consumidas pelo modelo de origem.

O exemplo a seguir mostra como passar o endereço IP privado gerado em um modelo vinculado.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

Dentro do modelo principal, você pode usar esses dados com a seguinte sintaxe:

    "[reference('master-node').outputs.masterip.value]"

Você pode usar essa expressão na seção saídas ou a seção de recursos do modelo principal. Você não pode usar a expressão na seção variáveis porque ela depende o estado de tempo de execução. Para retornar esse valor do modelo principal, use:

    "outputs": { 
      "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]",
        "type": "string"
      }
     
Para obter um exemplo de como usar a seção de saídas de um modelo vinculado para retornar discos de dados para uma máquina virtual, consulte [Criando vários discos de dados para uma máquina Virtual](resource-group-create-multiple.md#creating-multiple-data-disks-for-a-virtual-machine).

## <a name="define-authentication-settings-for-virtual-machine"></a>Definir configurações de autenticação para máquina virtual

Você pode usar o mesmo padrão mostrado anteriormente para definições de configuração para especificar as configurações de autenticação para uma máquina virtual. Você cria um parâmetro para passagem no tipo de autenticação.

    "parameters": {
      "authenticationType": {
        "allowedValues": [
          "password",
          "sshPublicKey"
        ],
        "defaultValue": "password",
        "metadata": {
          "description": "Authentication type"
        },
        "type": "string"
      }
    }

Adicionar variáveis para os diferentes tipos de autenticação e uma variável para armazenar qual tipo é usada para essa implantação baseada no valor do parâmetro.

    "variables": {
      "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
      "osProfilepassword": {
        "adminPassword": "[parameters('adminPassword')]",
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]"
      },
      "osProfilesshPublicKey": {
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]",
        "linuxConfiguration": {
          "disablePasswordAuthentication": "true",
          "ssh": {
            "publicKeys": [
              {
                "keyData": "[parameters('sshPublicKey')]",
                "path": "/home/notused/.ssh/authorized_keys"
              }
            ]
          }
        }
      }
    }

Ao definir a máquina virtual, você define o **osProfile** para a variável que você criou.

    {
      "type": "Microsoft.Compute/virtualMachines",
      ...
      "osProfile": "[variables('osProfile')]"
    }


## <a name="next-steps"></a>Próximas etapas
- Para saber sobre as seções do modelo, consulte [Criação de modelos do Azure Gerenciador de recursos](resource-group-authoring-templates.md)
- Para ver as funções disponíveis em um modelo, consulte [Funções de modelo de Gerenciador de recursos do Azure](resource-group-template-functions.md)

