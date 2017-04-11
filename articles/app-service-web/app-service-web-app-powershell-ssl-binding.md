<properties
    pageTitle="Vinculação de certificados SSL usando o PowerShell"
    description="Saiba como vincular certificados SSL a seu aplicativo web usando o PowerShell."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/13/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-ssl-certificate-binding-using-powershell"></a>Azure ligação de certificado SSL de aplicativo de serviço, usando o PowerShell #

Com o lançamento do Microsoft Azure PowerShell versão 1.1.0 um cmdlet novo foi adicionado que faria permite que o usuário associa certificados SSL de novo ou existentes a um aplicativo Web existente.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

Para saber como usar o Gerenciador de recursos do Azure com base Azure PowerShell cmdlets para gerenciar seus aplicativos Web verificar [Gerenciador de recursos do Azure com base em comandos do PowerShell do Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="uploading-and-binding-a-new-ssl-certificate"></a>Carregando e associando um novo certificado SSL ##

Cenário: O usuário gostaria de vincular um certificado SSL a um dos seus aplicativos web.

Se souber o nome do grupo de recursos que contém o aplicativo da web, o nome do aplicativo da web, o caminho do arquivo. pfx certificado na máquina do usuário, a senha para o certificado e o nome de host personalizado, podemos usar o seguinte comando do PowerShell para criar esse vinculação SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

Observe que, antes de adicionar uma ligação SSL para um aplicativo web, você deve ter um nome de host (domínio personalizado) já configurado. Se o nome do host não estiver configurado, você receberá um erro 'hostname' não existir enquanto executa o novo AzureRmWebAppSSLBinding. Você pode adicionar um nome de host diretamente a partir do portal ou usando o PowerShell do Azure. O seguinte trecho do PowerShell pode ser para configurar o nome do host antes de executar AzureRmWebAppSSLBinding de novo.   
  
    $webApp = Get-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup  
    $hostNames = $webApp.HostNames  
    $HostNames.Add("www.contoso.com")  
    Set-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup -HostNames $HostNames   
  
É importante entender o que o cmdlet Set-AzureRmWebApp substitui o nome de host para o aplicativo web. Portanto, o trecho acima do PowerShell é acrescentar à lista existente dos nomes de host para o aplicativo web.  

## <a name="uploading-and-binding-an-existing-ssl-certificate"></a>Carregando e associando um certificado SSL existente ##

Cenário: O usuário gostaria de vincular um certificado SSL carregado anteriormente a um dos seus aplicativos web.

Podemos obter a lista de certificados já carregou em um grupo de recursos específicos usando o comando a seguir

    Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

Observe que os certificados são locais para um local específico e um grupo de recursos, o usuário precisará carregar novamente o certificado, se o aplicativo web configurado estiver em um local diferente e recurso grupo outros que do certificado necessário 

Se souber o nome do grupo de recursos que contém o aplicativo da web, o nome do aplicativo da web, a impressão digital do certificado e o nome de host personalizado, podemos usar o seguinte comando do PowerShell para criar esse vinculação SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## <a name="deleting-an-existing-ssl-binding"></a>Excluindo uma vinculação SSL existente  ##

Cenário: O usuário gostaria de excluir uma ligação SSL existente.

Se souber o nome do grupo de recursos que contém o aplicativo da web, o nome do aplicativo web e o nome de host personalizado, podemos usar o seguinte comando do PowerShell para remover essa ligação SSL:

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

Observe que, se a ligação SSL removida foi a última vinculação usando esse certificado nesse local, por padrão o certificado será excluída, se o usuário deseja manter o certificado que ele pode usar a opção DeleteCertificate para manter o certificado

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### <a name="references"></a>Referências ###
- [Gerenciador de recursos de Azure com base em comandos do PowerShell do Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)
- [Introdução ao ambiente de serviço de aplicativo](app-service-app-service-environment-intro.md)
- [Usando o PowerShell Azure com o Azure Resource Manager](../powershell-azure-resource-manager.md)
