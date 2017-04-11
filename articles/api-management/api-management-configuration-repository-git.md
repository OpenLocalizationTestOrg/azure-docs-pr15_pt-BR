<properties 
    pageTitle="Como salvar e configurar a configuração do serviço de gerenciamento de API usando gito" 
    description="Saiba como salvar e configurar a configuração do serviço de gerenciamento de API usando gito." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Como salvar e configurar a configuração do serviço de gerenciamento de API usando gito

>[AZURE.IMPORTANT] Configuração de gito para gerenciamento de API está no modo de visualização. Ele é funcionalidade completo, mas é na visualização porque podemos ativamente buscam comentários sobre esse recurso. É possível que estamos podem fazer uma quebra de alterar em resposta a comentários do cliente, portanto, é recomendável não dependendo o recurso para uso em ambientes de produção. Se você tiver dúvidas ou comentários, informe-em `apimgmt@microsoft.com`.

Cada instância do serviço de gerenciamento de API mantém um banco de dados de configuração que contém informações sobre a configuração e metadados para a instância do serviço. Podem ser feitas alterações para a instância do serviço alterar uma configuração no portal do publisher, usando um cmdlet do PowerShell ou fazer uma chamada de API REST. Além desses métodos, você também pode gerenciar a configuração de instância do serviço usando gito, a habilitação dos cenários de gerenciamento de serviço, como:

-   Controle de versão de configuração - baixar e armazenar versões diferentes da configuração do serviço
-   Em massa alterações na configuração: fazer alterações em várias partes da configuração do serviço no seu repositório local e integrar as alterações de volta para o servidor com uma única operação
-   Familiar toolchain gito e fluxo de trabalho - usar as ferramentas gito e fluxos de trabalho que você já estiver familiarizado com

O diagrama a seguir mostra uma visão geral das diferentes maneiras de configurar sua instância de serviço de gerenciamento de API.

![Configurar gito][api-management-git-configure]

Quando você faz alterações ao seu serviço usando o portal do publisher, cmdlets do PowerShell ou a API REST, você estiver gerenciando seu banco de dados de configuração de serviço usando o `https://{name}.management.azure-api.net` ponto de extremidade, conforme mostrado no lado direito do diagrama. O lado esquerdo do diagrama ilustra como você pode gerenciar a configuração de serviço usando gito e gito repositório de seu serviço localizado em `https://{name}.scm.azure-api.net`.

As etapas a seguir fornecem uma visão geral do gerenciamento de sua instância de serviço de gerenciamento de API usando gito.

1.  Habilitar o acesso de gito no seu serviço
2.  Salvar seu banco de dados de configuração do serviço no seu repositório gito
3.  Clonar o repo gito em sua máquina local
4.  Puxe o repo mais recente para baixo até sua máquina local e alterações de confirmação e envio de volta para seu repo
5.  Implantar as alterações de sua repo em seu banco de dados de configuração do serviço

Este artigo descreve como ativar e usar gito para gerenciar a configuração do serviço e fornece uma referência para os arquivos e pastas no repositório gito.

## <a name="to-enable-git-access"></a>Para habilitar o acesso de gito

Você pode exibir rapidamente o status da sua configuração gito exibindo o ícone gito no canto superior direito do portal do publisher. Neste exemplo, gito acesso não ainda foi habilitado.

![Status de gito][api-management-git-icon-enable]

Para exibir e configurar as definições de configuração de gito, você pode clicar no ícone de gito, ou clique no menu de **segurança** e navegue até a guia de **repositório de configuração** .

![Habilitar GITO][api-management-enable-git]

Para habilitar o acesso de gito, marque a caixa de seleção **Habilitar gito acesso** .

Após alguns instantes a alteração é salva e uma mensagem de confirmação será exibida. Observe que o ícone de gito foi alterado para cor para indicar que o acesso de gito está ativado e agora, a mensagem de status indica que há alterações não salvas no repositório. Isso ocorre porque o banco de dados de configuração do serviço de gerenciamento de API ainda não foi salvo no repositório.

![Gito habilitado][api-management-git-enabled]

>[AZURE.IMPORTANT] Quaisquer senhas que não são definidas como propriedades serão armazenadas no repositório e permanecerão no seu histórico até você desabilitar e reabilitar gito acesso. Propriedades fornecem um local seguro para gerenciar os valores de constante de cadeia de caracteres, incluindo segredos, em toda a configuração de API e políticas, assim você não tem armazená-las diretamente em suas instruções de política. Para obter mais informações, consulte [como usar propriedades em políticas de gerenciamento de API do Azure](api-management-howto-properties.md).

Para obter informações sobre como habilitar ou desabilitar o acesso de gito usando a API REST, consulte [Habilitar ou desabilitar o acesso de gito usando a API REST](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Para salvar a configuração do serviço no repositório gito

A primeira etapa antes de clonagem repositório é salvar o estado atual da configuração do serviço no repositório. Clique em **Salvar configuração repositório**.

![Salvar configuração][api-management-save-configuration]

Faça as alterações desejadas na tela de confirmação e clique em **Okey** para salvar.

![Salvar configuração][api-management-save-configuration-confirm]

Após alguns instantes a configuração é salva, e o status de configuração do repositório é exibido, incluindo a data e hora da última alteração de configuração e a última sincronização entre a configuração do serviço e o repositório.

![Status da configuração][api-management-configuration-status]

Após a configuração é salva no repositório, ele pode ser clonar.

Para obter informações sobre como executar essa operação usando a API REST, consulte [Confirmar instantâneo de configuração usando a API REST](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Clonar repositório para sua máquina local

Para clonar um repositório, é necessário o URL seu repositório, um nome de usuário e uma senha. O nome de usuário e a URL são exibidos na parte superior da guia **repositório de configuração** .

![Clonar gito][api-management-configuration-git-clone]

A senha é gerada na parte inferior da guia **repositório de configuração** .

![Gerar senha][api-management-generate-password]

Para gerar uma senha, primeiro, certifique-se de que o **vencimento** é definida como a data de validade desejada e a hora e clique em **Gerar Token**.

![Senha][api-management-password]

>[AZURE.IMPORTANT] Anote esta senha. Depois que você sair desta página a senha não será exibida novamente.

Os exemplos a seguir usam a ferramenta de gito Bash do [Gito para Windows](http://www.git-scm.com/downloads) , mas você pode usar qualquer ferramenta gito que você está familiarizado.

Abra sua ferramenta gito na pasta desejada e execute o seguinte comando para clonar repositório gito ao seu computador local, usando o comando fornecido pelo portal do publisher.

    git clone https://bugbashdev4.scm.azure-api.net/ 

Fornece o nome de usuário e senha quando solicitado.

Se você receber erros, tente modificar seu `git clone` comando para incluir o nome de usuário e senha, conforme mostrado no exemplo a seguir.

    git clone https://username:password@bugbashdev4.scm.azure-api.net/

Se isso fornece um erro, tente a parte de senha do comando de codificação de URL. Uma maneira rápida de fazer isso é abrir o Visual Studio e execute o seguinte comando na **Janela imediata**. Para abrir a **Janela imediata**, abra qualquer solução ou o projeto no Visual Studio (ou criar um novo aplicativo de console vazio) e escolha **Windows**, **imediata** no menu **Depurar** .

    ?System.NetWebUtility.UrlEncode("password from publisher portal")

Use a senha codificada junto com seu usuário nome e local do repositório para construir o comando gito.

    git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/

Depois que o repositório é duplicado pode visualizar e trabalhar com ele no seu sistema de arquivos local. Para obter mais informações, consulte [referência de estrutura de arquivo e pasta de repositório de gito local](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Para atualizar seu repositório local com a configuração de instância mais atual do serviço

Se você fizer alterações em sua instância de serviço de gerenciamento de API no portal do publisher ou usando a API REST, você deve salvar essas alterações no repositório antes de atualizar seu repositório local com as alterações mais recentes. Para fazer isso, clique em **Salvar configuração repositório** na guia **repositório de configuração** no portal do publisher e, em seguida, execute o seguinte comando no seu repositório local.

    git pull

Antes de executar `git pull` garantir que está na pasta para seu repositório local. Se você concluiu o `git clone` comando, e em seguida, você deve alterar o diretório para sua repo executando um comando semelhante ao seguinte.

    cd bugbashdev4.scm.azure-api.net/

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Por push as alterações de seu local repo para o repo de servidor

Para enviar as alterações do seu repositório local para o repositório de servidor, você deve confirmar suas alterações e então push-los para o repositório do servidor. Para confirmar suas alterações, abra a ferramenta de comando gito, alterne para o diretório do seu repositório local e emita os seguintes comandos.

    git add --all
    git commit -m "Description of your changes"

Para enviar todas as confirmações no servidor, execute o seguinte comando.

    git push

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Para implantar as alterações de configuração do serviço para a instância do serviço de gerenciamento de API

Depois que as alterações locais são confirmadas e enviadas para o repositório de servidor, você pode implantá-las à sua instância de serviço de gerenciamento de API.

![Implantar][api-management-configuration-deploy]

Para obter informações sobre como executar essa operação usando a API REST, consulte [alterações de implantar gito no banco de dados de configuração usando a API REST](https://msdn.microsoft.com/library/dn781420.aspx#DeployChanges).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Referência de estrutura de arquivo e pasta de repositório de gito local

Os arquivos e pastas no repositório local gito contêm as informações de configuração sobre a instância do serviço.

| Item                       | Descrição                                                                                |
|-------------------------   |--------------------------------------------------------------------------------------------|
| pasta de api de gerenciamento de raiz | Contém a configuração de nível superior para a instância do serviço                                  |
| pasta de APIs                | Contém a configuração para as apis da instância de serviço                            |
| pasta de grupos              | Contém a configuração para os grupos da instância de serviço                          |
| pasta de políticas            | Contém as diretivas da instância de serviço                                              |
| pasta de portalStyles        | Contém a configuração para as personalizações de portal do desenvolvedor da instância de serviço |
| pasta produtos            | Contém a configuração para os produtos da instância de serviço                        |
| pasta de modelos           | Contém a configuração para os modelos de email da instância de serviço                 |

Cada pasta pode conter um ou mais arquivos e em alguns casos, uma ou mais pastas, por exemplo, uma pasta para cada API, produto ou grupo. Os arquivos dentro de cada pasta são específicos para o tipo de entidade descrito pelo nome da pasta.

| Tipo de arquivo | Finalidade                                                                |
|-----------|------------------------------------------------------------------------|
| JSON      | Informações de configuração sobre a respectiva entidade                  |
| HTML      | Descrições sobre a entidade, geralmente são exibidos no portal do desenvolvedor |
| XML       | Instruções de política                                                      |
| CSS       | Folhas de estilo para personalização de portal do desenvolvedor                        |

Esses arquivos podem ser criados, excluídos, editados e gerenciados no seu sistema de arquivos local e as alterações implantadas de volta para a sua instância de serviço de gerenciamento de API.

>[AZURE.NOTE] As seguintes entidades não estão contidas no repositório gito e não podem ser configuradas usando gito.
>
>-    Usuários
>-    Assinaturas
>-    Propriedades
>-    Entidades portal do desenvolvedor diferente de estilos

### <a name="root-api-management-folder"></a>Pasta de api de gerenciamento de raiz

Raiz `api-management` pasta contém um `configuration.json` arquivo que contém informações de alto nível sobre a instância de serviço no seguinte formato.

    {
      "settings": {
        "RegistrationEnabled": "True",
        "UserRegistrationTerms": null,
        "UserRegistrationTermsEnabled": "False",
        "UserRegistrationTermsConsentRequired": "False",
        "DelegationEnabled": "False",
        "DelegationUrl": "",
        "DelegatedSubscriptionEnabled": "False",
        "DelegationValidationKey": ""
      },
      "$ref-policy": "api-management/policies/global.xml"
    }

As quatro primeiras configurações (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, e `UserRegistrationTermsConsentRequired`) mapa para as seguintes configurações na guia **identidades** na seção **segurança** .

| Configuração de identidade                     | Mapas para                                               |
|--------------------------------------|-------------------------------------------------------|
| RegistrationEnabled                  | Caixa de seleção **redirecionar usuários anônimos à página de entrada** |
| UserRegistrationTerms                | **Termos de uso na inscrição de usuário da** caixa de texto               |
| UserRegistrationTermsEnabled         | Caixa de seleção **Mostrar termos de uso na página de inscrição**         |
| UserRegistrationTermsConsentRequired | Caixa de seleção **exigir consentimento**                          |

![Configurações de identidade][api-management-identity-settings]

As próximas quatro configurações (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, e `DelegationValidationKey`) mapa para as seguintes configurações na guia **delegação** na seção **segurança** .

| Configuração de delegação           | Mapas para                                    |
|------------------------------|--------------------------------------------|
| DelegationEnabled            | Caixa de seleção **representante entrar & inscrição**    |
| DelegationUrl                | Caixa de texto **URL de ponto de extremidade de delegação**        |
| DelegatedSubscriptionEnabled | Caixa de seleção de **assinatura do produto de representante** |
| DelegationValidationKey      | Caixa de texto de **Chave de validação de representante**        |

![Configurações de delegação][api-management-delegation-settings]

A configuração final, `$ref-policy`, mapeia o arquivo de declarações de política global para a instância do serviço.

### <a name="apis-folder"></a>pasta de APIs

O `apis` pasta contém uma pasta para cada API da instância de serviço que contém os itens a seguir.

-   `apis\<api name>\configuration.json`-Esta é a configuração para a API e contém informações sobre a URL do serviço de back-end e as operações. São as mesmas informações retornada seriam se você tivesse que chame [obter uma API específica](https://msdn.microsoft.com/library/azure/dn781423.aspx#GetAPI) com `export=true` na `application/json` formato.
-   `apis\<api name>\api.description.html`-Esta é a descrição da API e corresponde ao `description` propriedade da [entidade de API](https://msdn.microsoft.com/library/azure/dn781423.aspx#EntityProperties).
-   `apis\<api name>\operations\`-Esta pasta contém `<operation name>.description.html` arquivos que são mapeados para as operações na API. Cada arquivo contém a descrição de uma única operação na API que mapeia para o `description` propriedade da [entidade de operação](https://msdn.microsoft.com/library/azure/dn781423.aspx#OperationProperties) a API REST.

### <a name="groups-folder"></a>pasta de grupos

O `groups` pasta contém uma pasta para cada grupo definido na instância do serviço.

-   `groups\<group name>\configuration.json`-Essa é a configuração do grupo. Esta é a mesma informação que poderia ser retornada se você tivesse que a operação de [obter um grupo específico](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup) de chamadas.
-   `groups\<group name>\description.html`-Esta é a descrição do grupo e corresponde ao `description` propriedade da [entidade de grupo](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties).

### <a name="policies-folder"></a>pasta de políticas

O `policies` pasta contém as instruções de políticas para a sua instância de serviço.

-   `policies\global.xml`-contém diretivas definidas no escopo global para a sua instância de serviço.
-   `policies\apis\<api name>\`-Se você tiver qualquer políticas definidas no escopo de API, elas estão contidas nesta pasta.
-   `policies\apis\<api name>\<operation name>\`pasta - se você tiver qualquer políticas definidas no escopo de operação, elas estão contidas nessa pasta na `<operation name>.xml` arquivos que são mapeados para as instruções de políticas para cada operação.
-   `policies\products\`-Se você tiver quaisquer políticas definidas no escopo do produto, elas estão contidas nesta pasta, que contém `<product name>.xml` arquivos que são mapeados para as instruções de políticas para cada produto.

### <a name="portalstyles-folder"></a>pasta de portalStyles

O `portalStyles` pasta contém configuração e folhas de estilo para personalizações portal do desenvolvedor para a instância do serviço.

-   `portalStyles\configuration.json`-contém os nomes das folhas de estilo usadas pelo portal do desenvolvedor
-   `portalStyles\<style name>.css`-cada `<style name>.css` arquivo contém estilos para o portal do desenvolvedor (`Preview.css` e `Production.css` por padrão).

### <a name="products-folder"></a>pasta produtos

O `products` pasta contém uma pasta para cada produto definido na instância do serviço.

-   `products\<product name>\configuration.json`-Essa é a configuração do produto. Esta é a mesma informação que poderia ser retornada se você tivesse chamar a operação de [obter um produto específico](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct) .
-   `products\<product name>\product.description.html`-Esta é a descrição do produto e corresponde ao `description` propriedade da [entidade product](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product) a API REST.

### <a name="templates"></a>modelos

O `templates` pasta contém configuração para os [modelos de email](api-management-howto-configure-notifications.md) da instância do serviço.

-   `<template name>\configuration.json`-Essa é a configuração para o modelo de email.
-   `<template name>\body.html`-Este é o corpo do modelo de email.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre outras maneiras de gerenciar sua instância de serviço, consulte:

-   Gerenciar sua instância de serviço usando os seguintes cmdlets do PowerShell
    -   [Implantação do serviço de referência de cmdlets do PowerShell](https://msdn.microsoft.com/library/azure/mt619282.aspx)
    -   [Gerenciamento de serviço de referência de cmdlets do PowerShell](https://msdn.microsoft.com/library/azure/mt613507.aspx)
-   Gerenciar sua instância de serviço no portal do publisher
    -   [Gerenciar sua primeira API](api-management-get-started.md)
-   Gerenciar sua instância de serviço usando a API REST
    -   [Referência de gerenciamento de API REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx)

## <a name="watch-a-video-overview"></a>Assista a uma visão geral em vídeo

> [AZURE.VIDEO configuration-over-git]

[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




