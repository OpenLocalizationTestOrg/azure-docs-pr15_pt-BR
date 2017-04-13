<properties
   pageTitle="Publicar-WebApplicationVM | Microsoft Azure"
   description="Saiba como implantar um aplicativo web para uma máquina virtual. Este script cria os recursos necessários em sua assinatura do Azure se eles não existem."
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

# <a name="publish-webapplicationvm-windows-powershell-script"></a>Publicar-WebApplicationVM (script do Windows PowerShell)

Implantar um aplicativo web para uma máquina virtual. O script cria os recursos necessários em sua assinatura do Azure se eles não existem.

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>Configuração

O caminho para o arquivo de configuração de JSON que descreve os detalhes da implantação.

|Aliases|Nenhum|
|---|---|
|Necessário?|verdadeiro|
|Posição|nomeados|
|Valor padrão|Nenhum|
|Aceitar a entrada do pipeline?|FALSO|
|Aceitar caracteres curinga?|FALSO|

### <a name="subscriptionname"></a>Nome de inscrição

O nome da assinatura do Azure na qual você deseja criar a máquina virtual.

|Aliases|Nenhum|
|---|---|
|Necessário?|FALSO|
|Posição|nomeados|
|Valor padrão|Usa a primeira assinatura do arquivo de assinatura|
|Aceitar a entrada do pipeline?|FALSO|
|Aceitar caracteres curinga?|FALSO|

### <a name="webdeploypackage"></a>WebDeployPackage

O caminho para o pacote de implantação da web para publicar na máquina virtual. Você pode criar esse pacote usando o Assistente de publicar Web no Visual Studio. Consulte [como: criar um pacote de implantação da Web no Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx).

|Aliases|Nenhum|
|---|---|
|Necessário?|FALSO|
|Posição|nomeados|
|Valor padrão|Nenhum|
|Aceitar a entrada do pipeline?|FALSO|
|Aceitar caracteres curinga?|FALSO|

### <a name="allowuntrusted"></a>AllowUntrusted

Se verdadeiro, permite o uso de certificados que não estão assinados por uma autoridade raiz confiável.

|Aliases|Nenhum|
|---|---|
|Necessário?|FALSO|
|Posição|nomeados|
|Valor padrão|FALSO|
|Aceitar a entrada do pipeline?|FALSO|
|Aceitar caracteres curinga?|FALSO|

### <a name="vmpassword"></a>VMPassword

As credenciais da conta de máquina virtual. Exemplo: - VMPassword @{Name = "admin"; Senha = "senha"}

|Aliases|Nenhum|
|---|---|
|Necessário?|FALSO|
|Posição|nomeados|
|Valor padrão|Nenhum|
|Aceitar a entrada do pipeline?|FALSO|
|Aceitar caracteres curinga?|FALSO|

### <a name="databaseserverpassword"></a>DatabaseServerPassword

As credenciais para o banco de dados SQL Azure. Exemplo: - DatabaseServerPassword @{Name = "admin"; Senha = "senha"}

|Aliases|Nenhum|
|---|---|
|Necessário?|FALSO|
|Posição|nomeados|
|Valor padrão|Nenhum|
|Aceitar a entrada do pipeline?|FALSO|
|Aceitar caracteres curinga?|FALSO|

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

Se verdadeiro, imprimir mensagens do script para o fluxo de saída.

|Aliases|Nenhum|
|---|---|
|Necessário?|FALSO|
|Posição|nomeados|
|Valor padrão|FALSO|
|Aceitar a entrada do pipeline?|FALSO|
|Aceitar caracteres curinga?|FALSO|

## <a name="remarks"></a>Comentários

Para obter uma explicação completa de como usar o script para criar ambientes de desenvolvimento e teste, consulte [Usando Scripts do Windows PowerShell para publicar para desenvolvimento e ambientes de teste](vs-azure-tools-publishing-using-powershell-scripts.md).

O arquivo de configuração de JSON Especifica os detalhes do que existe para ser implantado. Ele inclui as informações que você especificou quando você criou o projeto, como o nome, grupo de afinidade, imagem VHD e tamanho da máquina virtual. Também inclui os pontos de extremidade na máquina virtual, os bancos de dados para provisionar, se houver e parâmetros de implantação da web. O código a seguir mostra um exemplo de arquivo de configuração de JSON:

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Você pode editar o arquivo de configuração de JSON para alterar o que está provisionado. Uma máquina virtual e um serviço de nuvem são necessárias, mas a seção de banco de dados é opcional.
