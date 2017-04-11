<properties 
    pageTitle="Como implementar a recuperação de dados usando o serviço backup e restauração no gerenciamento de API do Azure | Microsoft Azure" 
    description="Saiba como usar o backup e restauração para executar a recuperação de dados no gerenciamento de API do Azure." 
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

# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Como implementar a recuperação de dados usando o serviço backup e restauração no gerenciamento de API do Azure

Escolhendo publicar e gerenciar suas APIs via gerenciamento de API do Azure você está aproveitando muitos tolerância e os recursos de infraestrutura que você teria caso contrário, projetar, implementar e gerenciar. Plataforma Windows Azure reduz uma grande parte da possíveis falhas por uma fração do custo.

Para recuperar disponibilidade problemas que afetam a região em que o gerenciamento de API do serviço está hospedado você devem estar prontos para reconstituir seu serviço em uma região diferente a qualquer momento. Dependendo das suas metas de disponibilidade e o objetivo de tempo de recuperação você talvez queira reservar um serviço de backup em uma ou mais regiões e tentar manter sua configuração e o conteúdo em sincronia com o serviço ativo. O backup de serviço e o recurso de restauração fornece o bloco de construção necessário para implementar sua estratégia de recuperação de desastres.

Este guia mostra como autenticar solicitações de Gerenciador de recursos do Azure e como fazer backup e restaurar suas instâncias de serviço de gerenciamento de API.

>[AZURE.NOTE] O processo de backup e restauração de uma instância de serviço de gerenciamento de API para recuperação de dados também pode ser usado para replicar instâncias de serviço de gerenciamento de API para cenários como o teste.
>
>Observe que cada backup expira após 7 dias. Se você tentar restaurar um backup após o período de expiração de 7 dias expirou, a restauração falhará com um `Cannot restore: backup expired` mensagem.

## <a name="authenticating-azure-resource-manager-requests"></a>Solicitações de autenticação Gerenciador de recursos do Azure

>[AZURE.IMPORTANT] A API REST para backup e restauração usa o Gerenciador de recursos do Azure e tem um mecanismo de autenticação que as APIs REST para gerenciar suas entidades de gerenciamento de API. As etapas desta seção descrevem como autenticar solicitações do Gerenciador de recursos do Azure. Para obter mais informações, consulte [solicitações de autenticação Gerenciador de recursos do Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Todas as tarefas que você fizer em recursos usando o Gerenciador de recursos do Azure devem ser autenticadas com o Azure Active Directory usando as seguintes etapas.

-   Adicione um aplicativo para o locatário do Active Directory do Azure.
-   Definir permissões para o aplicativo que você adicionou.
-   Obter o token de autenticação de solicitações ao Gerenciador de recursos do Azure.

A primeira etapa é criar um aplicativo do Azure Active Directory. Entrar no [Portal de clássico do Azure](http://manage.windowsazure.com/) usando a assinatura que contém sua instância de serviço de gerenciamento de API e navegue até a guia de **aplicativos** para o Active Directory do Azure padrão.

>[AZURE.NOTE] Se o diretório do Active Directory do Azure padrão não estiver visível à sua conta, contate o administrador da assinatura do Azure para conceder as permissões necessárias para sua conta. Para obter informações sobre como localizar seu diretório padrão, consulte [localizar seu diretório padrão](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-portal).

![Criar o aplicativo do Active Directory do Azure][api-management-add-aad-application]

Clique em **Adicionar**, **Adicionar um aplicativo minha organização está desenvolvendo**e escolha o **aplicativo cliente nativo**. Insira um nome descritivo e clique na seta próxima. Insira uma URL de espaço reservado, como `http://resources` para **Redirecionar URI**, como ele é um campo obrigatório, mas o valor não é usado mais tarde. Clique na caixa de seleção para salvar o aplicativo.

Quando o aplicativo for salvo, clique em **Configurar**, role para baixo até a seção **permissões para outros aplicativos** e clique em **Adicionar aplicativo**.

![Adicionar permissões][api-management-aad-permissions-add]

Selecione do **Windows** **Azure API de gerenciamento de serviço** e clique na caixa de seleção para adicionar o aplicativo.

![Adicionar permissões][api-management-aad-permissions]

Clique em **Permissões de delegada** ao lado do aplicativo do **Windows** **Azure API de gerenciamento de serviço** recém-adicionado, marque a caixa de **Gerenciamento de serviços do Access Azure (visualização)**e clique em **Salvar**.

![Adicionar permissões][api-management-aad-delegated-permissions]

Antes de chamar as APIs que gerar o backup e restaurá-lo, é necessário obter um token. O exemplo a seguir usa o pacote de nuget [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) para recuperar o token.

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace GetTokenResourceManagerRequests
    {
        class Program
        {
            static void Main(string[] args)
            {
                var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
                var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

                if (result == null) {
                    throw new InvalidOperationException("Failed to obtain the JWT token");
                }

                Console.WriteLine(result.AccessToken);

                Console.ReadLine();
            }
        }
    }

Substituir `{tentand id}`, `{application id}`, e `{redirect uri}` usando as instruções a seguir.

Substituir `{tenant id}` com a id de locatário do aplicativo Azure Active Directory que você acabou de criar. Você pode acessar a identificação clicando em **pontos de extremidade do modo de exibição**.

![Pontos de extremidade][api-management-aad-default-directory]

![Pontos de extremidade][api-management-endpoint]

Substituir `{application id}` e `{redirect uri}` com a **Id do cliente** e a URL da seção **Redirecionar Uris** do guia de **Configurar** do seu aplicativo Azure Active Directory. 

![Recursos][api-management-aad-resources]

Depois que os valores são especificados, o exemplo de código deve retornar um token semelhante ao seguinte exemplo.

![Token][api-management-arm-token]

Antes de chamar as operações de backup e restauração descritas nas seções a seguir, defina o cabeçalho de solicitação de autorização para sua chamada restante.

    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);

## <a name="step1"> </a>Um serviço de gerenciamento de API de backup
Fazer backup de uma solicitação de problema o seguir HTTP de serviço de gerenciamento de API:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

onde:

* `subscriptionId`-id da assinatura que contém o serviço de gerenciamento de API que você está tentando para backup
* `resourceGroupName`-uma cadeia de caracteres na forma de 'Api - Default-{serviço-região}' onde `service-region` identifica o Azure região onde a API do serviço de gerenciamento de que você está tentando backup estiver hospedado, por exemplo,`North-Central-US`
* `serviceName`-o nome do serviço de gerenciamento de API de você está fazendo um backup do especificado no momento de sua criação
* `api-version`-Substituir por`2014-02-14`

No corpo da solicitação, especifique o nome de conta de armazenamento do Azure de destino, tecla de acesso, nome de contêiner de blob e nome do backup:

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

Definir o valor da `Content-Type` cabeçalho de solicitação para `application/json`.

Backup é uma operação de execução demorada que pode demorar vários minutos para ser concluída.  Se a solicitação foi bem-sucedida e o processo de backup foi iniciado você receberá um `202 Accepted` código de status de resposta com um `Location` cabeçalho.  Tornar 'GET' solicitações para a URL na `Location` cabeçalho para descobrir o status da operação. Enquanto o backup está em andamento, você continuará a receber um código de status '202 aceitos'. Um código de resposta de `200 OK` indicará conclusão bem-sucedida da operação de backup.

**Observação**:

- **Contêiner** especificado no corpo de solicitação **deve existir**.
* Enquanto o backup está em andamento que você **não tente qualquer operações de gerenciamento de serviço** como o SKU atualizar ou fazer downgrade, alteração de nome de domínio, etc. 
* Restauração de um **backup certamente apenas por 7 dias** desde o momento de sua criação. 
* **Dados de uso** usados para a criação de análise de relatórios **não está incluída** no backup. Use o [Restante do Azure API de gerenciamento][] para recuperar periodicamente relatórios de análise para fins de proteção.
* A frequência com que você executar backups de serviço afetará seu objetivo de ponto de recuperação. Para minimizar aconselhamos implementar backups regulares, bem como a realização de backups sob demanda depois de fazer alterações importantes do seu serviço de gerenciamento de API.
* **As alterações** feitas na configuração de serviço (por exemplo, APIs, políticas, aparência portal do desenvolvedor) durante a operação de backup está no processo **pode não ser incluído no backup e, portanto, serão perdidas**.

## <a name="step2"> </a>Restaurar um serviço de gerenciamento de API
Para restaurar um gerenciamento de API do serviço de um backup criado anteriormente fazer a solicitação HTTP seguinte:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

onde:

* `subscriptionId`-id da assinatura que contém o serviço de gerenciamento de API que você está restaurando um backup em
* `resourceGroupName`-uma cadeia de caracteres na forma de 'Api - Default-{serviço-região}' onde `service-region` identifica o Azure região em que o serviço de gerenciamento de API que você está restaurando um backup em está hospedado, por exemplo,`North-Central-US`
* `serviceName`-o nome do gerenciamento de API do serviço que está sendo restaurado em especificado no momento da sua criação
* `api-version`-Substituir por`2014-02-14`

No corpo da solicitação, especifique o local do arquivo de backup, por exemplo, nome da conta de armazenamento do Azure, tecla de acesso, nome de contêiner de blob e nome do backup:

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

Definir o valor da `Content-Type` cabeçalho de solicitação para `application/json`.

A restauração é uma operação de execução demorada que pode levar até 30 ou mais minutos para ser concluída.  Se a solicitação foi bem-sucedida e o processo de restauração foi iniciado você receberá um `202 Accepted` código de status de resposta com um `Location` cabeçalho.  Tornar 'GET' solicitações para a URL na `Location` cabeçalho para descobrir o status da operação. Enquanto a restauração estiver em andamento, você continuará a receber '202 aceitos' código de status. Um código de resposta de `200 OK` indicará conclusão bem-sucedida da operação de restauração.

>[AZURE.IMPORTANT] **O SKU** do serviço que está sendo restaurado no **deve corresponder** a SKU do serviço backup está sendo restaurado.
>
>**As alterações** feitas na configuração de serviço (por exemplo, APIs, políticas, aparência portal do desenvolvedor) durante a operação de restauração está em andamento **pode ser substituído**.

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes blogs da Microsoft para duas orientações diferentes do processo de backup e restauração.

-   [Duplicar contas de gerenciamento de API do Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/) 
    -   Agradecemos a Gisela sua contribuição neste artigo.
-   [Gerenciamento de API Azure: Backup e restauração configuração](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    -   A abordagem detalhada por Stuart não coincidir com as orientações oficial, mas é muito interessante.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Gerenciamento de API Azure API REST]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
 
