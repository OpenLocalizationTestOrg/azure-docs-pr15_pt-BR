<properties
    pageTitle="Conectar-se a pilha Azure com CLI | Microsoft Azure"
    description="Saiba como usar a interface de linha entre plataformas (CLI) para gerenciar e implantar recursos na pilha do Azure"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="helaw"/>

# <a name="install-and-configure-azure-stack-cli"></a>Instalar e configurar o Azure pilha CLI

Neste documento, vamos orientá-lo durante o processo de usar Azure Interface de linha de comando (CLI) para gerenciar recursos do Azure pilha plataformas Linux e Mac cliente.  

## <a name="install-azure-stack-cli"></a>Instalar o Azure pilha CLI

Se você estiver em Mac ou Linux, você pode obter a CLI, usando o seguinte comando:
  
    `npm install -g azure-cli@0.10.4`.


## <a name="connect-to-azure-stack"></a>Conectar-se a pilha Azure
Nas etapas a seguir, você configurar CLI do Azure para se conectar ao Azure pilha. Depois que você entrar e recuperar informações de assinatura.

1.  Recupere o valor do active directory-recurso-id executando este PowerShell:
        
          (Invoke-RestMethod -Uri https://api.azurestack.local/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]

2.  Use o seguinte comando CLI para adicionar o ambiente do Azure pilha, lembrando-se de atualizar *-active-directory-recurso-id* com os dados de URL recuperados na etapa anterior:

           azure account env add AzureStack --resource-manager-endpoint-url "https://api.azurestack.local" --management-endpoint-url "https://api.azurestack.local" --active-directory-endpoint-url  "https://login.windows.net" --portal-url "https://portal.azurestack.local" --gallery-endpoint-url "https://portal.azurestack.local" --active-directory-resource-id "https://azurestack.local-api/" --active-directory-graph-resource-id "https://graph.windows.net/"

3.  Entrar usando o seguinte comando (substituir *username* com seu nome de usuário):

        azure login -e AzureStack -u “<username>”

    >[AZURE.NOTE]Se você estiver recebendo problemas de validação de certificado, desativar a validação de certificado executando o comando `set        NODE_TLS_REJECT_UNAUTHORIZED=0`.

4.  Defina o modo de configuração Azure ao Gerenciador de recursos do Azure usando o seguinte comando:

        azure config mode arm

5.  Recupere uma lista de assinaturas.

        azure account list     

## <a name="next-steps"></a>Próximas etapas

[Implantar modelos com CLI do Azure](azure-stack-deploy-template-command-line.md)

[Conectar-se com o PowerShell](azure-stack-connect-powershell.md)

[Gerenciar permissões de usuário](azure-stack-manage-permissions.md)
