<properties
   pageTitle="Publicar-WebApplicationWebSite (script do Windows PowerShell) | Microsoft Azure"
   description="Saiba como publicar um projeto da web em um site do Azure. Este script cria os recursos necessários em sua assinatura do Azure se eles não existem."
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

# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Publicar-WebApplicationWebSite (script do Windows PowerShell)

##<a name="syntax"></a>Sintaxe

Publica um projeto da web para um site Azure. O script cria os recursos necessários em sua assinatura do Azure se eles não existem.

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Configuração

O caminho para o arquivo de configuração de JSON que descreve os detalhes da implantação.

|Parâmetro|Valor padrão|
|---|---|
|Aliases|Nenhum|
|Necessário?|verdadeiro|
|Posição|nomeados|
|Valor padrão|Nenhum|
|Aceitar a entrada do pipeline?|FALSO|
|Aceitar caracteres curinga?|FALSO|

## <a name="subscriptionname"></a>Nome de inscrição

O nome da assinatura do Azure que você deseja criar o site no.

|Parâmetro|Valor padrão|
|---|---|
|Aliases|Nenhum|
|Necessário?|FALSO|
|Posição|nomeados|
|Valor padrão|Nenhum|
|Aceitar a entrada do pipeline?|FALSO|
|Aceitar caracteres curinga?|FALSO|

## <a name="webdeploypackage"></a>WebDeployPackage

O caminho para o pacote de implantação da web para publicar o site. Você pode criar esse pacote usando o Assistente de publicar Web no Visual Studio. Para obter mais informações, consulte [Introdução aos serviços de nuvem do Azure e ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

|Parâmetro|Valor padrão|
|---|---|
|Aliases|Nenhum|
|Necessário?|FALSO|
|Posição|nomeados|
|Valor padrão|Nenhum|
|Aceitar a entrada do pipeline?|FALSO|
|Aceitar caracteres curinga?|FALSO|

## <a name="databaseserverpassword"></a>DatabaseServerPassword

O nome de usuário e senha do banco de dados do SQL no Azure.

|Parâmetro|Valor padrão|
|---|---|
|Aliases|Nenhum|
|Necessário?|FALSO|
|Posição|nomeados|
|Valor padrão|Nenhum|
|Aceitar a entrada do pipeline?|FALSO|
|Aceitar caracteres curinga?|FALSO|

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

Se verdadeiro, imprimir mensagens do script para o fluxo de saída.

|Parâmetro|Valor padrão|
|---|---|
|Aliases|Nenhum|
|Necessário?|FALSO|
|Posição|nomeados|
|Valor padrão|FALSO|
|Aceitar a entrada do pipeline?|FALSO|
|Aceitar caracteres curinga?|FALSO|

## <a name="remarks"></a>Comentários

Para obter uma explicação completa de como usar o script para criar ambientes de desenvolvimento e teste, consulte [Usando Scripts do Windows PowerShell para publicar para desenvolvimento e ambientes de teste](vs-azure-tools-publishing-using-powershell-scripts.md).

O arquivo de configuração de JSON Especifica os detalhes do que existe para ser implantado. Ele inclui as informações que você especificou quando você criou o projeto, como o nome e o nome de usuário para o site. Ele também inclui o banco de dados para provisionar, se houver. O código a seguir mostra um exemplo de arquivo de configuração de JSON:

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

Você pode editar o arquivo de configuração de JSON para alterar o que é implantado. Uma seção do site é necessária, mas a seção de banco de dados é opcional.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [Publicar-WebApplicationVM (script do Windows PowerShell)](vs-azure-tools-publish-webapplicationvm.md)
