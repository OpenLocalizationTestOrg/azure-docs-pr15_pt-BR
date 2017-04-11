<properties 
    pageTitle="Como criar um ASE ILB usando modelos de Gerenciador de recursos Azure | Microsoft Azure" 
    description="Aprenda a criar um ASE de Balanceador de carga interno usando modelos do Gerenciador de recursos do Azure." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="nirma" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="stefsch"/>   

# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Como criar um ASE ILB usando modelos de Gerenciador de recursos do Azure

## <a name="overview"></a>Visão geral ##
Serviço de aplicativo ambientes pode ser criados com um endereço interno de rede virtual em vez de um VIP público.  Este endereço interno é fornecido por um componente de Azure chamado balanceador de carga interno (ILB).  Um ASE ILB podem ser criado usando o portal do Azure.  Ele também pode ser criado usando automação por meio de modelos do Gerenciador de recursos do Azure.  Este artigo explica as etapas e sintaxe necessárias para criar um ASE ILB com modelos do Gerenciador de recursos do Azure.

Há três etapas envolvidas no automatizar a criação de um ASE ILB:
1. Primeiro a base ASE é criada em uma rede virtual usando um endereço de Balanceador de carga interno, em vez de um VIP público.  Como parte desta etapa, um nome de domínio raiz é atribuído a do ASE ILB.
2. Depois de criado o ASE ILB, um certificado SSL é carregado.  
3. O certificado SSL carregado explicitamente é atribuído do ASE ILB como seu certificado SSL de "padrão".  Este certificado SSL será usado para o tráfego SSL para aplicativos em do ASE ILB quando os aplicativos endereçados usando o domínio raiz comum atribuído do ASE (por exemplo, https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Criando a Base ILB ASE ##
Um modelo de exemplo Gerenciador de recursos do Azure e seu arquivo de parâmetros associados, estão disponíveis no GitHub [aqui][quickstartilbasecreate].

A maioria dos parâmetros no arquivo *azuredeploy.parameters.json* são comuns para a criação de tanto ILB ASEs, como ASEs vinculados a um VIP público.  Na lista abaixo chamadas parâmetros da nota especial de saída, ou que são exclusivos, ao criar um ASE ILB:


- *interalLoadBalancingMode*: na maioria dos casos definir essa opção para 3, o que significa o tráfego HTTP/HTTPS em portas 80/443, tanto os dados de controle/portas de canal ouviu pelo serviço FTP do ASE, será acoplado a um ILB alocado endereço interno de rede virtual.  Se essa propriedade é definida para 2, somente o serviço FTP relacionados portas (canais de controle e de dados) serão associadas a um endereço ILB, enquanto o tráfego HTTP/HTTPS permanecerá no VIP público.
-  *dnsSuffix*: este parâmetro define o domínio raiz padrão que será atribuído do ASE.  Na variação pública do serviço de aplicativo do Azure, o domínio de raiz padrão para todos os aplicativos web é *azurewebsites.net*.  No entanto como um ASE ILB é interno a rede virtual de um cliente, não faz sentido usar o domínio de raiz de padrão do serviço público.  Em vez disso, um ASE ILB deve ter um domínio raiz padrão que faz sentido para uso em uma rede virtual interno da empresa.  Por exemplo, uma corporação Contoso hipotética pode usar um domínio raiz *interno contoso.com* padrão para aplicativos que são destinados a só ser resolvido e acessíveis dentro de uma rede virtual da Contoso. 
-  *ipSslAddressCount*: este parâmetro é automaticamente padronizadas de acordo com um valor de 0 no arquivo *azuredeploy.json* porque ILB ASEs ter apenas um único endereço ILB.  Há sem endereços de IP SSL explícitos para um ASE ILB e, portanto, o pool de endereços IP SSL para um ASE ILB deve ser definido como zero, caso contrário, ocorrerá um erro de provisionamento. 

Depois que o arquivo de *azuredeploy.parameters.json* tiver sido preenchido para um ASE ILB, o ASE ILB podem ser criado usando o seguinte trecho de código do Powershell.  Altere o arquivo caminhos para corresponder ao onde os arquivos de modelo do Gerenciador de recursos do Azure estão localizados no seu computador.  Lembre-se também fornecer seus próprios valores para o nome de implantação do Gerenciador de recursos do Azure e o nome do grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Após o Gerenciador de recursos do Azure modelo é enviado levará algumas horas para do ASE ILB a ser criado.  Uma vez concluída a criação, a ASE ILB aparecerá no portal do UX na lista de ambientes de serviço de aplicativo para a assinatura que disparou a implantação.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Carregando e configurando o certificado SSL de "Padrão" ##

Depois de criado o ASE ILB, um certificado SSL deve ser associado do ASE como "padrão" certificado SSL usados para estabelecer conexões SSL para aplicativos.  Continuar com o exemplo de Contoso Corporation hipotético, se o ASE padrão DNS sufixo é *interno contoso.com*, em seguida, uma conexão para *https://some-random-app.internal-contoso.com* requer um certificado SSL válido para **.internal contoso.com*. 

Há várias maneiras para obter um certificado SSL válido, incluindo autoridades de certificação interna, adquirir um certificado de um emissor externo e usando um certificado auto-assinado.  Independentemente da fonte do certificado SSL, os seguintes atributos de certificado precisam estar configurado corretamente:

- *Assunto*: esse atributo deve ser definido como **sua raiz-domínio here.com*
- *Nome alternativo*: esse atributo deve incluir ambos * *sua-raiz-domínio-here.com*, e * *.scm.your-raiz-domínio-here.com*.  O motivo da segunda entrada é que conexões SSL para o site SCM/Kudu associado a cada aplicativo serão feitas usando um endereço do formulário *your-app-name.scm.your-root-domain-here.com*.

Com um certificado SSL válido em mãos, são necessárias duas etapas preparatórias adicionais.  O certificado SSL deve ser convertidas/salva como um arquivo. pfx.  Lembre-se de que o arquivo. pfx precisa incluir todos os intermediários e raiz de certificados e também precisa ser protegido com uma senha.

Em seguida, o arquivo. pfx resultante deve ser convertido em uma cadeia de caracteres na Base 64 porque o certificado SSL será carregado usando um modelo do Gerenciador de recursos do Azure.  Como gerente de recursos do Azure modelos são arquivos de texto, o arquivo. pfx deve ser convertido em uma cadeia de caracteres na Base 64 que ela possa ser incluída como um parâmetro do modelo.

O trecho de código do Powershell abaixo mostra um exemplo de gerar um certificado autoassinado, exportando o certificado como um arquivo. pfx, converter o arquivo. pfx na Base 64 cadeia de caracteres codificada e salvando-na Base 64 codificada como a cadeia de caracteres em um arquivo separado.  O código do Powershell para codificação na Base 64 foi adaptado do [Blog de Scripts do Powershell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
    
    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")
    
Depois que o certificado SSL foi com êxito gerado e convertido em uma codificação base64 de cadeia de caracteres, o modelo do Gerenciador de recursos do Azure de exemplo no GitHub para [Configurar o certificado SSL padrão] [ configuringDefaultSSLCertificate] pode ser usado.

Os parâmetros no arquivo *azuredeploy.parameters.json* estão listados abaixo:

- *appServiceEnvironmentName*: O nome do ASE ILB está sendo configurado.
- *existingAseLocation*: cadeia de caracteres de texto contendo o Azure região onde o ASE ILB foi implantado.  Por exemplo: "Centro Sul dos EUA".
- *pfxBlobString*: O based64 codificado representação de cadeia de caracteres do arquivo. pfx.  Usando o trecho de código mostrado anteriormente, copie a cadeia de caracteres contida em "exportedcert.pfx.b64" e cole-o como o valor do atributo *pfxBlobString* .
- *senha*: A senha usada para proteger o arquivo. pfx.
- *certificateThumbprint*: impressão digital do certificado.  Se você recuperar esse valor do Powershell (por exemplo, *$certificate. Impressão digital* do trecho de código anterior), você pode usar o valor como-é.  No entanto se você copiar o valor da caixa de diálogo de certificado do Windows, lembre-se retirar os espaços estranhos.  O *certificateThumbprint* deve parecer com: AF3143EB61D43F6727842115BB7F17BBCECAECAE
- *certificateName*: um identificador de cadeia de caracteres amigável de sua escolha usadas para identificar o certificado.  O nome será usado como parte do identificador exclusivo do Gerenciador de recursos do Azure para a entidade de *Microsoft.Web/certificates* que representa o certificado SSL.  O nome **deve** termine com o sufixo a seguir: \_yourASENameHere_InternalLoadBalancingASE.  Esse sufixo é usado pelo portal como um indicador de que o certificado é usado para proteger um ASE habilitados para ILB.


Um exemplo abreviado de *azuredeploy.parameters.json* é mostrado abaixo:


    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Depois que o arquivo de *azuredeploy.parameters.json* tiver sido preenchido, o certificado SSL padrão pode ser configurado usando o seguinte trecho de código do Powershell.  Altere o arquivo caminhos para corresponder ao onde os arquivos de modelo do Gerenciador de recursos do Azure estão localizados no seu computador.  Lembre-se também fornecer seus próprios valores para o nome de implantação do Gerenciador de recursos do Azure e o nome do grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Após o Gerenciador de recursos do Azure modelo é enviado levará aproximadamente quarenta minutos minutos por ASE front-end para aplicar a alteração.  Por exemplo, com um ASE padrão dimensionado usando dois front-ends, o modelo levará aproximadamente uma hora e vinte minutos para ser concluída.  Enquanto o modelo está em execução a ASE não poderão dimensionada.  

Quando o modelo for concluída, aplicativos em do ASE ILB podem ser acessados por HTTPS e as conexões serão protegidas usando o certificado SSL padrão.  O certificado SSL padrão será usado quando aplicativos em do ASE ILB endereçados usando uma combinação do nome do aplicativo plus o nome de host padrão.  Por exemplo, *https://mycustomapp.internal-contoso.com* usaria o certificado SSL padrão **.internal contoso.com*.

No entanto, assim como aplicativos executando o serviço público de vários locatário, desenvolvedores podem também configurar nomes de host personalizados para aplicativos individuais e configure exclusivas ligações de certificados SSL SNI para aplicativos individuais.  


## <a name="getting-started"></a>Guia de Introdução

Para começar a usar o aplicativo de serviço ambientes, consulte [Introdução ao ambiente de serviço de aplicativo](app-service-app-service-environment-intro.md)

Todos os artigos e como-para para ambientes de serviço de aplicativo estão disponível no [Leiame para ambientes de serviço de aplicativos](../app-service/app-service-app-service-environments-readme.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 
 
