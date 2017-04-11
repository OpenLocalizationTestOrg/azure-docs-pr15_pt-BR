<properties
    pageTitle="Implantar modelos com a linha de comando na pilha do Azure | Microsoft Azure"
    description="Saiba como usar a interface de linha de comando (CLI) entre plataformas para implantar modelos de dentro do ClientVM ou depois de usar a VPN para se conectar ao Azure pilha."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Implantar modelos na pilha de Azure usando a linha de comando

Use a linha de comando para implantar modelos de Gerenciador de recursos do Azure para o Azure pilha VDC. Os modelos de Gerenciador de recursos Azure implantar e provisionar todos os recursos para seu aplicativo em uma operação única e coordenado.

## <a name="download-template"></a>Baixar modelo        
Para testar uma implantação com a CLI, baixe o azuredeploy.json de arquivos e azuredeploy.parameters.json de [criar o modelo de exemplo de conta de armazenamento](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Implantar o modelo
Navegue até a pasta onde esses arquivos foram baixados e execute o seguinte comando para implantar o modelo:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Esse comando implanta o modelo para o grupo de recursos **cliRG** no local padrão do Azure pilha POC.

## <a name="validate-template-deployment"></a>Valide implantação de modelo
Para ver essa conta de armazenamento e o grupo de recursos, use os seguintes comandos:

    azure group list

    azure storage account list

## <a name="next-steps"></a>Próximas etapas

[Gerenciar permissões de usuário](azure-stack-manage-permissions.md)
