<properties 
   pageTitle="Introdução ao armazenamento de Lucerne de dados usando a API REST | Microsoft Azure" 
   description="Use WebHDFS restante APIs para executar operações no repositório de Lucerne de dados" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-rest-apis"></a>Introdução ao armazenamento de Lucerne de dados do Azure usando APIs REST

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node](data-lake-store-manage-use-nodejs.md)

Neste artigo, você aprenderá como usar WebHDFS restante APIs e dados Lucerne armazenamento restante APIs para realizar gerenciamento de conta, bem como as operações de sistema de arquivos no repositório de Lucerne de dados do Azure. Armazenamento de Lucerne de dados do Azure expõe sua própria APIs REST para operações de gerenciamento de conta. No entanto, como dados Lucerne Store é compatível com o ecossistema HDFS e Hadoop, ele oferece suporte usando WebHDFS restante APIs para operações de sistema de arquivos.

>[AZURE.NOTE] Para obter informações detalhadas sobre a API REST suporte para armazenamento de Lucerne de dados, consulte [Referência de API do Azure dados Lucerne armazenamento restante](https://msdn.microsoft.com/library/mt693424.aspx).

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).

- **Criar um aplicativo do Active Directory do Azure**. Use o aplicativo do Azure AD para autenticar o aplicativo de armazenamento de Lucerne de dados com o Azure AD. Há diferentes abordagens para autenticar com Azure AD, que são a **autenticação do usuário final** ou **ao serviço**. Para obter instruções e mais informações sobre como se autenticar, consulte [autenticar com armazenamento de Lucerne de dados usando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

- [cURL](http://curl.haxx.se/). Este artigo usa ondulação para demonstrar como fazer chamadas de API REST em relação a uma conta de armazenamento de Lucerne de dados.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como autenticam usando o Azure Active Directory?

Você pode usar duas abordagens para autenticar usando o Active Directory do Azure.

### <a name="end-user-authentication-interactive"></a>Autenticação de usuário final (interativa)

Neste cenário, o aplicativo solicita o usuário para efetuar login e todas as operações são executadas no contexto do usuário. Execute as seguintes etapas para autenticação interativa.

1. Por meio do aplicativo, redirecione o usuário para a URL a seguir:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<REDIRECIONAR-URI > precisam ser codificados para uso em uma URL. Portanto, para https://localhost, use `https%3A%2F%2Flocalhost`)

    Efeitos neste tutorial, você pode substituir os valores de espaço reservado na URL acima e colá-lo na barra de endereços do navegador da web. Você será redirecionado para autenticar usando seu logon Azure. Depois que você fizer logon, a resposta é exibida na barra de endereços do navegador. A resposta será no seguinte formato:
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Capture o código de autorização da resposta. Para este tutorial, você pode copiar o código de autorização na barra de endereços do navegador da web e passar na solicitação de POSTAGEM no ponto de extremidade token, conforme mostrado abaixo:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] Nesse caso, o \<URI de REDIRECIONAMENTO > não precisa ser codificada.

3. A resposta é um objeto JSON que contém um token de acesso (por exemplo, `"access_token": "<ACCESS_TOKEN>"`) e um token de atualização (por exemplo, `"refresh_token": "<REFRESH_TOKEN>"`). Seu aplicativo usa o token de acesso ao acessar o repositório de Lucerne de dados do Azure e o token de atualização para obter outro token de acesso quando um token de acesso expira.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  Quando o token de acesso expira, você pode solicitar um novo token de acesso usando o token de atualização, conforme mostrado abaixo:

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
Para obter mais informações sobre autenticação de usuário interativo, consulte o [código de autorização conceder fluxo](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Serviço de autenticação (não-interativo)

Neste cenário, o aplicativo fornece suas própria credenciais para realizar as operações. Para isso, você deve emitir uma solicitação de POSTAGEM como a mostrada abaixo. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

A saída dessa solicitação incluirá um símbolo de autorização (indicado por `access-token` na saída abaixo) que você subsequentemente passará com suas chamadas de API REST. Salvar este token de autenticação em um arquivo de texto; Você precisará isso neste artigo.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Este artigo usa o **não interativo** abordagem. Para obter mais informações sobre não interativo (chamadas de serviço para), consulte [serviço para chamadas de serviço usando credenciais](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="create-a-data-lake-store-account"></a>Criar uma conta de armazenamento de Lucerne de dados

Essa operação se baseia a API REST chamada definida [aqui](https://msdn.microsoft.com/library/mt694078.aspx).

Use o seguinte comando de rotação. Substituir ** \<yourstorename >** com seu nome de armazenamento de Lucerne de dados.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

No comando acima, substitua \< `REDACTED` \> com o token de autorização que você recuperou anteriormente. A carga de solicitação para este comando está contida no arquivo **input.json** que é fornecido para a `-d` parâmetro acima. O conteúdo do arquivo input.json semelhante ao seguinte:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }   

## <a name="create-folders-in-a-data-lake-store-account"></a>Criar pastas em uma conta de armazenamento de Lucerne de dados

Essa operação se baseia a API REST WebHDFS chamada definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Use o seguinte comando de rotação. Substituir ** \<yourstorename >** com seu nome de armazenamento de Lucerne de dados.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

No comando acima, substitua \< `REDACTED` \> com o token de autorização que você recuperou anteriormente. Esse comando cria um diretório chamado **mytempdir** sob a pasta raiz da sua conta de armazenamento de Lucerne de dados.

Se a operação for concluída com êxito, você verá uma resposta assim:

    {"boolean":true}

## <a name="list-folders-in-a-data-lake-store-account"></a>Lista de pastas em uma conta de armazenamento de Lucerne de dados

Essa operação se baseia a API REST WebHDFS chamada definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Use o seguinte comando de rotação. Substituir ** \<yourstorename >** com seu nome de armazenamento de Lucerne de dados.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

No comando acima, substitua \< `REDACTED` \> com o token de autorização que você recuperou anteriormente.

Se a operação for concluída com êxito, você verá uma resposta assim:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## <a name="upload-data-into-a-data-lake-store-account"></a>Carregar dados para uma conta de armazenamento de Lucerne de dados

Essa operação se baseia a API REST WebHDFS chamada definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Carregar dados usando a API REST WebHDFS é um processo de duas etapas, conforme explicado abaixo.

1. Envie uma solicitação de HTTP colocar sem enviar os dados de arquivo a ser carregado. No comando a seguir, substitua ** \<yourstorename >** com seu nome de armazenamento de Lucerne de dados.

        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

    A saída para este comando ser conterá uma URL de redirecionamento temporário, como mostrada abaixo.

        HTTP/1.1 100 Continue

        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...

2. Agora você deve enviar outra solicitação HTTP colocar em relação a URL listada para a propriedade de **localização** na resposta. Substituir ** \<yourstorename >** com seu nome de armazenamento de Lucerne de dados.

        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

    O resultado será semelhante ao seguinte:

        HTTP/1.1 100 Continue

        HTTP/1.1 201 Created
        ...
        ...

## <a name="read-data-from-a-data-lake-store-account"></a>Ler dados de uma conta de armazenamento de Lucerne de dados

Essa operação se baseia a API REST WebHDFS chamada definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Lendo dados de um armazenamento de dados Lucerne conta é um processo de duas etapas.

* Enviar uma solicitação GET contra o ponto de extremidade primeiro `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Isso retornará um local para enviar a solicitação GET próxima à.
* Em seguida, enviar a solicitação GET contra o ponto de extremidade `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Isso exibirá o conteúdo do arquivo.

Entretanto, porque não há nenhuma diferença nos parâmetros de entrada entre a primeira e a segunda etapa, você pode usar o `-L` parâmetro para enviar a solicitação primeira. `-L`opção essencialmente combina duas solicitações em um e fará ondulação refazer a solicitação no novo local. Por fim, a saída de todas as chamadas de solicitação é exibida, como mostrado abaixo. Substituir ** \<yourstorename >** com seu nome de armazenamento de Lucerne de dados.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Você deve ver uma saída semelhante à seguinte:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...
    
    HTTP/1.1 200 OK
    ...
    
    Hello, Data Lake Store user!

## <a name="rename-a-file-in-a-data-lake-store-account"></a>Renomear um arquivo em uma conta de armazenamento de Lucerne de dados

Essa operação se baseia a API REST WebHDFS chamada definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Use o seguinte comando ondulação para renomear um arquivo. Substituir ** \<yourstorename >** com seu nome de armazenamento de Lucerne de dados.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Você deve ver uma saída semelhante à seguinte:

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-file-from-a-data-lake-store-account"></a>Excluir um arquivo de uma conta de armazenamento de Lucerne de dados

Essa operação se baseia a API REST WebHDFS chamada definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Use o seguinte comando ondulação para excluir um arquivo. Substituir ** \<yourstorename >** com seu nome de armazenamento de Lucerne de dados.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Você deve ver uma saída semelhante ao seguinte:

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-data-lake-store-account"></a>Excluir uma conta de armazenamento de Lucerne de dados

Essa operação se baseia a API REST chamada definida [aqui](https://msdn.microsoft.com/library/mt694075.aspx).

Use o seguinte comando ondulação para excluir uma conta de armazenamento de Lucerne de dados. Substituir ** \<yourstorename >** com seu nome de armazenamento de Lucerne de dados.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Você deve ver uma saída semelhante ao seguinte:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="see-also"></a>Consulte também

- [Abrir os aplicativos grandes de fonte de dados compatíveis com o armazenamento de Lucerne de dados do Azure](data-lake-store-compatible-oss-other-applications.md)
 
