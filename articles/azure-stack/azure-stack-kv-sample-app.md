<properties
    pageTitle="Permitir que aplicativo segredos do Azure pilha chave cofre revtrieve | Microsoft Azure"
    description="Usar um aplicativo de amostra para trabalhar com o Azure pilha chave cofre"
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

# <a name="run-the-sample-application-for-key-vault"></a>Executar o aplicativo de amostra para Cofre de chave 

Neste guia, você vai usar um aplicativo de exemplo para recuperar segredos e senhas do Cofre de chave.

## <a name="download-the-samples-and-prepare"></a>Baixe as amostras e preparar

Baixe as amostras de cliente do Azure chave cofre na [página de amostras de cliente do Azure chave cofre](https://www.microsoft.com/en-us/download/details.aspx?id=45343).

Extraia o conteúdo do arquivo. zip ao computador local.

Ler o arquivo de **README.md** (Este é um arquivo de texto) e siga as instruções.

## <a name="run-sample-1--hellokeyvault"></a>Executar amostra #1--HelloKeyVault
HelloKeyVault é um aplicativo de console que percorra os principais cenários que são suportados pelo Cofre de chave:

  1. Criar importação/uma chave (chave de software ou HSM)
  2. Criptografar um segredo usando uma chave de conteúdo
  3. Quebrar a chave de conteúdo usando uma chave de chave cofre
  4. Não quebrar a chave de conteúdo
  5. Descriptografar o segredo
  6. Definir um segredo

Esse aplicativo console deve ser executado sem alterações, exceto que as definições de configuração apropriado no App serão atualizadas acordo com as seguintes etapas:

1. Atualize as configurações do aplicativo na HelloKeyVault\App.config com sua URL do cofre, ID de aplicativo de principal e secreta. As informações, opcionalmente, podem ser geradas usando **scripts\GetAppConfigSettings.ps1**.
2. Atualize os valores das variáveis obrigatórios em GetAppConfigSettings.ps1.
3. Inicie a janela do Windows PowerShell.
4. Execute o script GetAppConfigSettings.ps1 dentro da janela do PowerShell.
5. Copie os resultados do script para o arquivo de HelloKeyVault\App.config.


## <a name="next-steps"></a>Próximas etapas

[Implantar uma máquina virtual com uma senha de Cofre de chave](azure-stack-kv-deploy-vm-with-secret.md)

[Implantar uma máquina virtual com um certificado de chave cofre](azure-stack-kv-push-secret-into-vm.md)