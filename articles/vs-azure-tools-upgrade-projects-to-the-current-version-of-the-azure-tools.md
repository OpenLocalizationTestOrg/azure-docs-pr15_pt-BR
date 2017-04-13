<properties
   pageTitle="Como atualizar projetos para a versão atual das ferramentas do Azure | Microsoft Azure"
   description="Saiba como atualizar um projeto Azure no Visual Studio para a versão atual das ferramentas do Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>Como atualizar projetos para a versão atual das ferramentas do Azure para Visual Studio

## <a name="overview"></a>Visão geral

Depois de instalar a versão atual das ferramentas do Azure (ou uma versão anterior do mais recente que 1,6), quaisquer projetos que foram criados usando uma ferramentas do Azure solte antes de 1,6 (novembro de 2011) serão atualizadas automaticamente assim que você abri-los. Se você criou projetos usando a versão (novembro de 2011) 1,6 dessas ferramentas e você ainda tem essa versão instalada, você pode abrir esses projetos na versão mais antiga e mais tarde decidir se deseja atualizá-los.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Como o seu projeto muda quando atualizá-lo

Se um projeto é atualizado automaticamente ou especificar que você deseja atualizá-lo, seu projeto é modificado para funcionar com versões atuais de determinados conjuntos e algumas propriedades também são alteradas como esta seção descreve. Se seu projeto exigir outras alterações para ser compatível com a versão mais recente das ferramentas, você deve fazer essas alterações manualmente.

- O arquivo Web. config para funções da web e o arquivo de App para funções de trabalho são atualizados para fazer referência a versão mais recente do Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll.

- Os conjuntos de Microsoft.WindowsAzure.StorageClient.dll, Microsoft.WindowsAzure.Diagnostics.dll e Microsoft.WindowsAzure.ServiceRuntime.dll são atualizados para as novas versões.

- Perfis de publicação que foram armazenados no arquivo de projeto Azure (.ccproj) são movidos para um arquivo separado, com o .azurePubXml de extensão, na subpasta **Publicar** .

- Algumas propriedades no perfil publicar são atualizadas para oferecer suporte a recursos novos e alterados. **AllowUpgrade** é substituído pelo **DeploymentReplacementMethod** porque você pode atualizar um serviço de nuvem implantado simultaneamente ou de forma incremental.

- A propriedade **UseIISExpressByDefault** é adicionada e definida como false para que o servidor da web que é usado para depuração não alterar automaticamente de serviços de informações da Internet (IIS) para IIS Express. IIS Express é o servidor de web padrão para projetos que são criados com as versões mais recentes das ferramentas.

- Se o cache do Azure estiver hospedado em um ou mais das funções do seu projeto, algumas propriedades na configuração de serviço (arquivo .cscfg) e a definição de serviço (arquivo .csdef) são alteradas quando um projeto é atualizado. Se o projeto usa o pacote NuGet cache do Azure, o projeto é atualizado para a versão mais recente do pacote. Você deve abrir o arquivo Web. config e verifique se que a configuração do cliente foi mantida corretamente durante o processo de atualização. Se você adicionou as referências para cache Azure montagens de cliente sem usar o pacote do NuGet, esses conjuntos não serão atualizados; Você deve atualizar manualmente essas referências para as novas versões.

>[AZURE.IMPORTANT] Para F # projetos, você deve atualizar manualmente referências aos conjuntos Azure para que eles fazem referência as versões mais recentes desses módulos.

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>Como atualizar um projeto do Azure para a versão atual

1. Instalar a versão atual das ferramentas do Azure para a instalação do Visual Studio que você deseja usar para o projeto atualizado e, em seguida, abra o projeto que você deseja atualizar. Se o projeto foi criado com uma ferramentas Azure solte antes de 1,6 (novembro de 2011), o projeto é atualizado automaticamente para a versão atual. Se o projeto foi criado com o novembro de 2011 lançamento e versão ainda está instalada, o projeto será aberto nessa versão.

1. No Solution Explorer, abra o menu de atalho para o nó do projeto, escolha **Propriedades**e, em seguida, escolha a guia de **aplicativo** da caixa de diálogo que aparece.

    Na guia **Application** mostra a versão de ferramentas associado ao projeto. Se a versão atual do Azure ferramentas for exibida, o projeto já foi atualizado. Se você instalou uma versão mais recente das ferramentas de que a guia mostra, um botão de **atualização** aparecerá.

1. Escolha o botão **Atualizar** para atualizar um projeto para a versão atual das ferramentas.

1. Criar o projeto e endereçar quaisquer erros que resultam de alterações de API. Para obter informações sobre como modificar seu código para a nova versão, consulte a documentação da API específico.
