<properties
    pageTitle="Implantar uma máquina virtual usando uma senha armazenada no Azure pilha chave cofre | Microsoft Azure"
    description="Saiba como implantar uma máquina virtual usando uma senha armazenada no Azure pilha chave cofre"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="deploy-a-vm-by-retrieving-the-password-stored-in-key-vault"></a>Implantar uma máquina virtual Recuperando a senha armazenada no cofre de chave

Quando você precisa passar um valor de seguro (como uma senha) como um parâmetro durante a implantação, você pode armazenar esse valor como um segredo em um cofre chave pilha do Azure e referenciar o valor em outros modelos do Gerenciador de recursos do Azure. Você pode incluir apenas uma referência para o segredo no seu modelo para que o segredo nunca é exposto. Você não precisa inserir manualmente o valor para o segredo sempre que você implantar os recursos. Você especificar quais usuários ou objetos de serviço podem acessar o segredo.

## <a name="reference-a-secret-with-static-id"></a>Referenciar um segredo com identificação estático

Referência o segredo de dentro de um arquivo de parâmetros, que passa valores para seu modelo. Você se referir o segredo passando o identificador de recurso do Cofre de chave e o nome do segredo. Neste exemplo, o segredo cofre chave já deve existir. Usar um valor estático para sua ID do recurso.

    "parameters": {
    "adminPassword": {
    "reference": {
    "keyVault": {
    "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
    },
    "secretName": "sqlAdminPassword"


>[AZURE.NOTE]O parâmetro que aceita o segredo deve ser uma *securestring*.

## <a name="next-steps"></a>Próximas etapas
[Implantar um aplicativo de exemplo com chave cofre](azure-stack-kv-sample-app.md)

[Implantar uma máquina virtual com um certificado de chave cofre](azure-stack-kv-push-secret-into-vm.md)

