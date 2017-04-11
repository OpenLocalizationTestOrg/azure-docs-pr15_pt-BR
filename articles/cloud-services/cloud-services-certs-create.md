<properties 
    pageTitle="Serviços em nuvem e certificados gerenciamento | Microsoft Azure" 
    description="Saiba como criar e usar certificados com o Microsoft Azure" 
    services="cloud-services" 
    documentationCenter=".net" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016"
    ms.author="adegeo"/>

# <a name="certificates-overview-for-azure-cloud-services"></a>Visão geral de certificados para serviços de nuvem do Azure
Certificados são usados no Azure para serviços de nuvem ([certificados de serviço](#what-are-service-certificates)) e para autenticar com o gerenciamento de API ([certificados gerenciamento](#what-are-management-certificates) usando o portal de clássico Azure e não ARM). Este tópico fornece uma visão geral dos dois tipos de certificado, como [criar](#create) e [implantá](#deploy) -los no Azure.

Certificados usados no Azure são x. 509 v3 certificados e podem ser assinados por outro certificado confiável ou podem ser auto-assinado. Um certificado auto-assinado é assinado por sua própria criador e por isso, por padrão, não são confiáveis. A maioria dos navegadores pode ignorar isso. Certificados autoassinados somente devem ser usados por si mesmo ao desenvolver e testar seus serviços de nuvem. 

Os certificados usados pelo Azure podem conter uma privada ou uma chave pública. Certificados têm uma impressão digital que fornece um meio identificá-los de uma maneira não ambígua. Essa impressão digital é usada no [arquivo de configuração](cloud-services-configure-ssl-certificate.md) do Azure para identificar quais certificado deve usar um serviço na nuvem. 

## <a name="what-are-service-certificates"></a>O que são certificados de serviço?
Certificados de serviço são anexados para serviços em nuvem e habilitar comunicação segura de e para o serviço. Por exemplo, se você implantou uma função web, convém fornecer um certificado que pode autenticar um ponto de extremidade HTTPS exposto. Certificados de serviço, definidos em sua definição de serviço, são implantados automaticamente na máquina virtual que está executando uma instância de sua função. 

Você pode carregar certificados de serviço portal Azure clássico ou usando o portal de clássico Azure ou usando a API de gerenciamento do serviço. Certificados de serviço estão associados a um serviço de nuvem específico e atribuídos a uma implantação no arquivo de definição de serviço.

Os certificados de serviço podem ser gerenciados separadamente de seus serviços e podem ser gerenciados por pessoas diferentes. Por exemplo, um desenvolvedor pode carregar um pacote de serviço que se refere a um certificado que um gerente de TI anteriormente carregado no Azure. Um gerente de TI pode gerenciar e renovar o certificado alterando a configuração do serviço sem precisar carregar um novo pacote de serviço. Isso é possível porque o nome lógico para o certificado e seu nome de armazenamento e o local são especificadas no arquivo de definição de serviço, enquanto a impressão digital do certificado for especificado no arquivo de configuração do serviço. Para atualizar o certificado, só é necessário carregar um novo certificado e altere o valor de impressão digital no arquivo de configuração do serviço.

## <a name="what-are-management-certificates"></a>O que são certificados gerenciamento?
Certificados gerenciamento permitem que você autenticar com a API de gerenciamento do serviço fornecido pelo Azure clássico. Muitos programas e ferramentas (como Visual Studio ou o SDK do Azure) usará esses certificados para automatizar o processo de configuração e implantação dos vários serviços Azure. Eles não estão realmente relacionados para serviços de nuvem. 

>[AZURE.WARNING] Tenha cuidado! Esses tipos de certificados permitir que qualquer pessoa que autentica com elas para gerenciar a assinatura que eles estão associados. 

### <a name="limitations"></a>Limitações
Não há um limite de 100 certificados de gerenciamento por assinatura. Também há um limite de 100 certificados de gerenciamento para todas as assinaturas em ID de usuário. do administrador de um serviço específico Se a ID de usuário para o administrador de conta já foi usada para adicionar 100 certificados de gerenciamento e há uma necessidade de mais certificados, você pode adicionar um administrador de colegas para adicionar os certificados adicionais. 

Antes de adicionar mais de 100 certificados, veja se você pode reutilizar um certificado existente. Usar coadministradores adiciona complexidade potencialmente desnecessário ao seu processo de gerenciamento de certificado.


<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Criar um novo certificado auto-assinado
Você pode usar qualquer ferramenta disponível para criar um certificado autoassinado, desde que eles adicional estas configurações:

* Um certificado x. 509.
* Contém uma chave privada.
* Criado para a troca de chaves (arquivo. pfx).
* Nome de assunto deve corresponder o domínio usado para acessar o serviço de nuvem. 
    > Você não é possível adquirir um domínio de certificado para o cloudapp.net (ou para qualquer Azure relacionados) SSL; nome de assunto do certificado deve corresponder ao nome de domínio personalizado utilizado para acessar seu aplicativo. Por exemplo, **contoso.net**, não **contoso.cloudapp.net**.
* Mínimo de criptografia de 2048 bits.
* **Serviço de certificado somente**: certificado de cliente deve residem no repositório de certificados *pessoais* .

Há duas maneiras fáceis de criar um certificado no Windows, com o `makecert.exe` utilitário ou IIS.

### <a name="makecertexe"></a>Makecert.exe

Este utilitário foi substituído e já não é documentado aqui. Consulte [Este artigo da MSDN](https://msdn.microsoft.com/library/windows/desktop/aa386968) para obter mais informações.

### <a name="powershell"></a>PowerShell

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

>[AZURE.NOTE] Se você quiser usar o certificado com um endereço IP em vez de um domínio, use o endereço IP no parâmetro - DnsName.


Se você quiser usar este [certificado com o portal de gerenciamento](../azure-api-management-certs.md), exportá-lo para um arquivo **. cer** :

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Serviços de informações da Internet (IIS)

Há muitas páginas na internet que cobrir como fazer isso com o IIS. [Aqui](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) é um ótimo eu encontrado que acho explica bem. 

### <a name="java"></a>Java
Você pode usar Java para [criar um certificado](../app-service-web/java-create-azure-website-using-java-sdk.md#create-a-certificate).

### <a name="linux"></a>Linux
[Este](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) artigo descreve como criar certificados com SSH.

## <a name="next-steps"></a>Próximas etapas

[Carregar seu certificado de serviço portal do Azure clássico](cloud-services-configure-ssl-certificate.md) (ou o [portal do Azure](cloud-services-configure-ssl-certificate-portal.md)).

Carregar um [certificado de API de gerenciamento](../azure-api-management-certs.md) do portal do Azure clássico.

>[AZURE.NOTE] O portal do Azure não usa certificados gerenciamento para acessar a API, mas utiliza contas de usuário.
