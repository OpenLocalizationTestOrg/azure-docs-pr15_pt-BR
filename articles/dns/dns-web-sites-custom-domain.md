<properties
   pageTitle="Criar registros DNS personalizados para um aplicativo web | Microsoft Azure  "
   description="Como criar registros DNS para o aplicativo web usando DNS Azure de domínio personalizado."
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Criar registros de DNS para um aplicativo web em um domínio personalizado

Você pode usar o Azure DNS para hospedar um domínio personalizado para seus aplicativos web. Por exemplo, você está criando um aplicativo web do Azure e você deseja que os usuários para acessá-lo por qualquer um usando contoso.com ou www.contoso.com como um FQDN.

Para fazer isso, você precisa criar dois registros:

- Um registro de raiz "A" apontando para contoso.com
- Um registro "CNAME" para o nome de "www" que aponta para o registro

Lembre-se que, se você criar um registro a para um aplicativo web no Azure, o registro deve ser manualmente atualizado se o subjacente endereço IP para que as alterações de aplicativo web.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar, primeiro você deve criar uma zona DNS no DNS do Azure e delegar a zona no seu registrador de DNS do Azure.

1. Para criar uma zona DNS, siga as etapas em [criar uma zona DNS](dns-getstarted-create-dnszone.md).
2. Para delegar DNS Azure DNS, siga as etapas em [delegação de domínio DNS](dns-domain-delegation.md).

Após criar uma zona e delegando-Azure DNS, em seguida, você pode criar registros para seu domínio personalizado.


## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Crie um registro a para seu domínio personalizado

Um registro é usado para mapear um nome para seu endereço IP. O exemplo a seguir, podemos atribuirá @ como um registro para um endereço IPv4:

### <a name="step-1"></a>Etapa 1

Criar um registro e atribuir a uma variável $rs

    $rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600

### <a name="step-2"></a>Etapa 2

Adicione o valor de IPv4 ao conjunto de registros criado anteriormente "@" usando a variável de $rs atribuída. O valor de IPv4 atribuído será o endereço IP do seu aplicativo web.

Para localizar o endereço IP para um aplicativo web, siga as etapas em [Configurar um nome de domínio personalizado no serviço de aplicativo do Azure](../web-sites-custom-domain-name.md#Find-the-virtual-IP-address).

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### <a name="step-3"></a>Etapa 3

Confirme as alterações para o conjunto de registros. Use `Set-AzureRMDnsRecordSet` carregar as alterações para o conjunto de registros ao Azure DNS:

    Set-AzureRMDnsRecordSet -RecordSet $rs

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Crie um registro CNAME para seu domínio personalizado

Se seu domínio já é gerenciado pelo Azure DNS (consulte [delegação de domínio DNS](dns-domain-delegation.md), você pode usar o seguinte exemplo para criar um registro CNAME para contoso.azurewebsites.net.

### <a name="step-1"></a>Etapa 1

Abra o PowerShell e criar um novo conjunto de registros CNAME e atribuir a uma variável $rs. Este exemplo criará um tipo de conjunto de registros CNAME com um "hora Live" de 600 segundos na zona DNS chamada "contoso.com".

    $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600

    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
    RecordType        : CNAME
    Records           : {}
    Tags              : {}


### <a name="step-2"></a>Etapa 2

Assim que o conjunto de registros CNAME for criado, você precisa criar um valor de alias que irá apontar para o aplicativo web.

Usando a variável atribuída anteriormente "$rs", você pode usar o comando do PowerShell abaixo para criar o alias para o contoso.azurewebsites.net de aplicativo web.

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"

    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}

### <a name="step-3"></a>Etapa 3

Confirmar as alterações usando o `Set-AzureRMDnsRecordSet` cmdlet:

    Set-AzureRMDnsRecordSet -RecordSet $rs

Você pode validar o registro foi criado corretamente consultando a "www.contoso.com" usando nslookup, conforme mostrado abaixo:

    PS C:\> nslookup
    Default Server:  Default
    Address:  192.168.0.1

    > www.contoso.com
    Server:  default server
    Address:  192.168.0.1

    Non-authoritative answer:
    Name:    <instance of web app service>.cloudapp.net
    Address:  <ip of web app service>
    Aliases:  www.contoso.com
    contoso.azurewebsites.net
    <instance of web app service>.vip.azurewebsites.windows.net

## <a name="create-an-awverify-record-for-web-apps"></a>Crie um registro de "awverify" para aplicativos web


Se você decidir usar um registro para o aplicativo web, você deve percorrer um processo de verificação para garantir que o proprietário do domínio personalizado. Esta etapa de verificação é feita criando um registro CNAME especial denominado "awverify". Esta seção aplica-se apenas uma registros.


### <a name="step-1"></a>Etapa 1

Crie o registro de "awverify". No exemplo abaixo, vamos criar o registro de "aweverify" para contoso.com confirmar a posse do domínio personalizado.

    $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600

    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
    RecordType        : CNAME
    Records           : {}
    Tags              : {}


### <a name="step-2"></a>Etapa 2

Depois de criado o conjunto de registros "awverify", atribua o registro CNAME definir alias. No exemplo abaixo, nós atribuirá o registro CNAMe alias definido como awverify.contoso.azurewebsites.net.

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"

    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}

### <a name="step-3"></a>Etapa 3

Confirmar as alterações usando o `Set-AzureRMDnsRecordSet cmdlet`, conforme mostrado no comando abaixo.

    Set-AzureRMDnsRecordSet -RecordSet $rs



## <a name="next-steps"></a>Próximas etapas

Siga as etapas em [Configurando um nome de domínio personalizado para o serviço de aplicativo](../app-service-web/web-sites-custom-domain-name.md) para configurar seu aplicativo web para usar um domínio personalizado.








