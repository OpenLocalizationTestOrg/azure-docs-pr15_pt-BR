<properties 
    pageTitle="Introdução ao autenticação de certificado com base no Android | Microsoft Azure" 
    description="Saiba como configurar a autenticação de certificado baseado em soluções com dispositivos Android" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/10/2016" 
    ms.author="markvi" />



# <a name="get-started-with-certificate-based-authentication-on-android---public-preview"></a>Introdução ao autenticação de certificado com base no Android - Public Preview  

> [AZURE.SELECTOR]
- [iOS](active-directory-certificate-based-authentication-ios.md)
- [Android](active-directory-certificate-based-authentication-android.md)


Este tópico mostra como configurar e utilizar a autenticação de certificado baseado (CBA) em um dispositivo Android para usuários de locatários nos planos do Office 365 Enterprise, Business e educação. 

CBA permite ser autenticada pelo Azure Active Directory com um certificado de cliente em um dispositivo Android ou iOS ao conectar sua conta do Exchange online para: 

- Aplicativos móveis do Office como o Microsoft Outlook e o Microsoft Word   
- Clientes do Exchange ActiveSync (EAS) 

Configurar esse recurso elimina a necessidade de inserir uma combinação de nome de usuário e senha em determinadas email e aplicativos do Microsoft Office no seu dispositivo móvel. 
 

## <a name="supported-scenarios-and-requirements"></a>Requisitos e cenários com suporte  



### <a name="general-requirements"></a>Requisitos gerais 


Para todos os cenários neste tópico, são necessárias as seguintes tarefas:  

- Acesso a authority(s) de certificado para emitir certificados de cliente.  

- O authority(s) de certificados deve ser configurados no Active Directory do Azure. Você pode encontrar as etapas detalhadas sobre como concluir a configuração na seção [Introdução](#getting-started) .  

- Autoridade de certificação raiz e qualquer autoridades de certificação intermediárias devem ser configuradas no Active Directory do Azure.  

- Cada autoridade de certificação deve ter uma lista de certificados revogados (CRL) que pode ser referenciada por meio de uma URL de com a Internet.  

- O certificado de cliente deve ser emitido para autenticação de cliente.  


- Exchange ActiveSync somente para clientes, o certificado de cliente deve ter o endereço do usuário email roteáveis no Exchange online no nome de capital ou o valor de nome de RFC822 do campo nome alternativo. O valor de RFC822 de mapas do Active Directory do Azure para o atributo de endereço Proxy no diretório.  



### <a name="office-mobile-applications-support"></a>Suporte de aplicativos móveis do Office 

| Aplicativos                      | Suporte      |
| ---                       | ---          |
| Word / Excel / PowerPoint | ![Seleção][1]  |
| OneNote                   | Em breve  |
| OneDrive                  | ![Seleção][1]  |
| Outlook                   | ![Seleção][1]  |
| Yammer                    | ![Seleção][1]  |
| Skype for Business        | ![Seleção][1]  |


### <a name="requirements"></a>Requisitos  

A versão de SO do dispositivo deve ser 5.0 Android (pirulito) e acima. 

Um servidor de federação deve ser configurado.  


Azure Active Directory revogar um certificado de cliente, o token ADFS deve ter as seguintes declarações:  

  - `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
(O número de série do certificado do cliente) 

  - `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
(A cadeia de caracteres para o emissor do certificado do cliente) 

Active Directory do Azure adiciona essas declarações ao token de atualização se eles estiverem disponíveis no token do ADFS (ou qualquer outro token SAML). Quando o token de atualização precisa ser validados, essas informações são usadas para verificar a revogação. 

Como prática recomendada, você deve atualizar as páginas de erro do ADFS com instruções sobre como obter um certificado de usuário. 

Para obter mais detalhes, consulte [Personalizando as AD FS entrar páginas](https://technet.microsoft.com/library/dn280950.aspx).  



### <a name="exchange-activesync-clients-support"></a>Suportam a clientes do Exchange ActiveSync 


Há suporte para certos aplicativos do Exchange ActiveSync no Android 5.0 (pirulito) ou posterior. Para determinar se seu aplicativo de email oferece suporte a esse recurso, entre em contato com o seu desenvolvedor de aplicativos. 



## <a name="getting-started"></a>Guia de Introdução 


Para começar, você precisa configurar as autoridades de certificação no Active Directory do Azure. Para cada autoridade de certificação, carregue o seguinte: 

- A parte pública do certificado, em formato *. cer* 

- Internet opostas URLs onde residem os as listas de certificados revogados (CRLs)
 

Abaixo está o esquema de uma autoridade de certificação: 

    class TrustedCAsForPasswordlessAuth 
    { 
       CertificateAuthorityInformation[] certificateAuthorities;    
    } 

    class CertificateAuthorityInformation 

    { 
        CertAuthorityType authorityType; 
        X509Certificate trustedCertificate; 
        string crlDistributionPoint; 
        string deltaCrlDistributionPoint; 
        string trustedIssuer; 
        string trustedIssuerSKI; 
    }                

    enum CertAuthorityType 
    { 
        RootAuthority = 0, 
        IntermediateAuthority = 1 
    } 


Para carregar as informações, você pode usar o módulo Microsoft Azure AD através do Windows PowerShell.  
A seguir são exemplos para adicionar, remover ou modificar uma autoridade de certificação. 



### <a name="configuring-your-azure-ad-tenant-for-certificate-based-authentication"></a>Configurar seu locatário do Azure AD para o certificado de autenticação com base em 

1. Inicie o Windows PowerShell com privilégios de administrador. 

2. Instale o módulo Azure AD. Você precisa instalar a versão [1.1.143.0](http://www.powershellgallery.com/packages/AzureADPreview/1.1.143.0) ou superior.  

        Install-Module -Name AzureADPreview –RequiredVersion 1.1.143.0 

3. Conectar-se ao seu locatário de destino: 

        Connect-AzureAD 

### <a name="adding-a-new-certificate-authority"></a>Adicionando uma nova autoridade de certificação

1. Definir várias propriedades da autoridade de certificação e adicioná-lo ao Azure Active Directory: 

        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 

5. Obter as autoridades de certificação: 

        Get-AzureADTrustedCertificateAuthority 


### <a name="retrieving-the-list-certificate-authorities"></a>Recuperando as autoridades de certificação de lista

Recupere as autoridades de certificação atualmente armazenadas no Active Directory do Azure para seu locatário: 

        Get-AzureADTrustedCertificateAuthority 


### <a name="removing-a-certificate-authority"></a>Removendo uma autoridade de certificação

1.  Recupere as autoridades de certificação: 

        $c=Get-AzureADTrustedCertificateAuthority 


2. Remova o certificado para a autoridade de certificação: 

        Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### <a name="modfiying-a-certificate-authority"></a>Modfiying uma autoridade de certificação 

1.  Recupere as autoridades de certificação: 

        $c=Get-AzureADTrustedCertificateAuthority 


2. Modificar as propriedades na autoridade de certificação: 

        $c[0].AuthorityType=1 

3. Defina a **autoridade de certificação**: 

        Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 




## <a name="testing-office-mobile-applications"></a>Teste de aplicativos móveis do Office  

Para testar a autenticação de certificado em seu aplicativo móvel do Office: 

1.  No seu dispositivo de teste, instale um aplicativo móvel do Office (por exemplo, OneDrive) da Google Play Store.

2.  Verifique se que o certificado de usuário tiver sido configurado para o seu dispositivo de teste. 

3.  Inicie o aplicativo. 

4.  Insira seu nome de usuário e, em seguida, escolha o certificado de usuário que você deseja usar. 

Você deve estar conectado com êxito. 





## <a name="testing-exchange-activesync-client-applications"></a>Testar aplicativos de cliente do Exchange ActiveSync

Para acessar o Exchange ActiveSync via autenticação de certificado baseado, um perfil do EAS que contém o certificado de cliente deve estar disponível para o aplicativo. O perfil do EAS deve conter as seguintes informações:

- Certificado de usuário a ser usado para autenticação 

- O ponto de extremidade do EAS deve ser office365 (como esse recurso é suportado atualmente apenas no ambiente de vários locatário online do Exchange)

Um perfil EAS pode ser configurado e colocado no dispositivo por meio da utilização de um MDM como o Intune ou colocando manualmente o certificado no perfil do EAS no dispositivo.  


### <a name="testing-eas-client-applications-on-android"></a>Testar aplicativos de cliente do EAS no Android 

Para testar a autenticação de certificado com um aplicativo no Android 5.0 (pirulito) ou posterior, execute as etapas abaixo:  

1. Configure um perfil EAS no aplicativo que atende aos requisitos acima.  


2. Depois que o perfil está configurado corretamente, abra o aplicativo e verificar que o email está sincronizando. 




## <a name="revocation"></a>Revogação

Para revogar um certificado de cliente, o Active Directory do Azure busca na lista de certificados revogados (CRL) de URLs carregados como parte das informações de autoridade de certificação e armazena. Publicar a última carimbo de hora (propriedade de**Data de efetivação** ) na CRL é usado para garantir a CRL ainda é válida. A CRL é referenciada periodicamente para revogar o acesso para certificados que fazem parte da lista.

Se uma revogação mais instantânea for necessária (por exemplo, se um usuário perder um dispositivo), o token de autorização do usuário pode ser invalidado. Para invalidar o token de autorização, defina o campo **StsRefreshTokenValidFrom** para determinado usuário usando o Windows PowerShell. Você deve atualizar o campo **StsRefreshTokenValidFrom** para cada usuário que você deseja revogar o acesso para.
 
Para garantir que a revogação persiste, você deve definir a **Data de efetivação** da CRL a uma data após o valor definido por **StsRefreshTokenValidFrom** e garantir que o certificado em questão está na CRL.
 
As etapas a seguir descrevem o processo de atualização e invalidem o token de autorização definindo o campo **StsRefreshTokenValidFrom** . 

1. Conecte com credenciais de administrador para o serviço de MSOL: 

        $msolcred = get-credential 
        connect-msolservice -credential $msolcred 

1.  Recupere o valor de StsRefreshTokensValidFrom atual de um usuário: 

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com` 
        $user.StsRefreshTokensValidFrom 


1.  Configure um novo valor de StsRefreshTokensValidFrom para a igual de usuário para o carimbo de hora atual: 

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")


A data que você definir deve ser no futuro. Se a data não estiver no futuro, a propriedade **StsRefreshTokensValidFrom** não está definida. Se a data for no futuro, **StsRefreshTokensValidFrom** está definido para a hora atual (não a data indicada pelo comando Set-MsolUser). 


<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png