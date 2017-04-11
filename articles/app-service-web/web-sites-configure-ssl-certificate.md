<properties
    pageTitle="Proteger a domínio personalizado do seu aplicativo com HTTPS | Microsoft Azure"
    description="Saiba como seguro o nome de domínio personalizado para seu aplicativo no serviço de aplicativo do Azure Configurando uma ligação de certificado SSL. Você também aprenderá obter um certificado SSL de várias ferramentas."
    services="app-service"
    documentationCenter=".net"
    authors="cephalin"
    manager="wpickett"
    editor="jimbe"
    tags="top-support-issue"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cephalin"/>

# <a name="secure-your-apps-custom-domain-with-https"></a>Proteger a domínio personalizado do seu aplicativo com HTTPS


> [AZURE.SELECTOR]
- [Comprar o certificado SSL no Azure](web-sites-purchase-ssl-web-site.md)
- [Usar o certificado SSL de qualquer outro lugar](web-sites-configure-ssl-certificate.md)


Este artigo mostra como habilitar HTTPS para um aplicativo web, um back-end do aplicativo móvel ou um aplicativo de API no [Serviço de aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) que usa um nome de domínio personalizado. Ele cubra autenticação somente de servidor. Se você precisar comum autenticação (incluindo a autenticação de cliente), consulte [Como para configurar TLS comum autenticação de serviço de aplicativo](app-service-web-configure-tls-mutual-auth.md).

Para proteger com HTTPS um aplicativo que tem um nome de domínio personalizado, você adiciona um certificado desse nome de domínio. Por padrão, o Azure protege a ** \*. azurewebsites.net** domínio curinga com um certificado SSL, para que seus clientes possam acessar já seu aplicativo em * *https://*&lt;appname >*. azurewebsites.net**. Mas se você quiser usar um domínio personalizado, como **www.contoso.com contoso.com**, ****, e ** \*. contoso.com**, o certificado padrão não seguro que. Além disso, como todos os [certificados curinga](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), o certificado padrão não é tão seguro quanto usando um domínio personalizado e um certificado para um domínio personalizado.   

>[AZURE.NOTE] Você pode obter ajuda de especialistas em Azure a qualquer momento nos [fóruns do Azure](https://azure.microsoft.com/support/forums/). Para obter suporte mais personalizado, vá para [Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obtenha suporte**.

<a name="bkmk_domainname"></a>
## <a name="what-you-need"></a>O que é necessário
Para proteger o seu nome de domínio personalizado com HTTPS, você pode vincular um certificado SSL personalizado para esse domínio personalizado no Azure. Antes de vincular um certificado personalizado, você precisa fazer o seguinte:

- **Configurar o domínio personalizado** - serviço de aplicativo só permite adicionar um certificado para um nome de domínio que já está configurado no seu aplicativo. Para obter instruções, consulte [mapear um nome de domínio personalizado para um aplicativo do Azure](web-sites-custom-domain-name.md). 
- **Dimensionar a camada básica ou superior** Planos de serviço de aplicativo em níveis de preços mais baixos não oferecem suporte a certificados SSL personalizados. Para obter instruções, consulte [Dimensionar um aplicativo no Azure](web-sites-scale.md). 
- **Obter um certificado SSL** - se você ainda não tiver um, você precisa obter uma de uma [autoridade de certificação](http://en.wikipedia.org/wiki/Certificate_authority) (CA) confiável. O certificado deve atender aos seguintes requisitos:

    - Ele é assinado por uma autoridade de certificação confiável (sem servidores de autoridade de certificação privadas).
    - Ele contém uma chave privada.
    - Ele é criado para troca de chaves e exportado para um. Arquivo PFX.
    - Ele usa um mínimo de criptografia de 2048 bits.
    - Seu nome de assunto corresponde o domínio personalizado, que ele precisa proteger. Para proteger vários domínios com um certificado, você precisa usar um nome de curinga (por exemplo, ** \*. contoso.com**) ou especificar valores de subjectAltName.
    - Ele é mesclado com todos os **[certificados intermediários](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** usado por sua autoridade de certificação. Caso contrário, você pode enfrentar problemas de interoperabilidade irreproducible em alguns clientes.

        >[AZURE.NOTE] A maneira mais fácil de obter um certificado SSL que atende a todos os requisitos é         [comprar um no portal do Azure diretamente](web-sites-purchase-ssl-web-site.md). Este artigo mostra como fazê-lo manualmente e, em seguida, vinculá-lo a seu domínio personalizado no aplicativo de serviço.
        >   
        > **Certificados de criptografia de curva elíptica (ECC)** pode trabalhar com o serviço de aplicativo, mas fora do escopo deste artigo. Trabalhar com sua CA nas etapas exatas para criar certificados ECC.

<a name="bkmk_getcert"></a>
## <a name="step-1-get-an-ssl-certificate"></a>Etapa 1. Obter um certificado SSL

Como autoridades de certificação fornecem os vários tipos de certificado SSL por diferentes preços, você deve começar por decidir qual tipo de certificado SSL para comprar. Para proteger um único nome de domínio (**www.contoso.com**), basta um certificado básico. Para proteger vários nomes de domínio (**contoso.com** *e* **www.contoso.com** 
*e* **mail.contoso.com**), você precisará de um [certificado curinga](http://en.wikipedia.org/wiki/Wildcard_certificate) ou um certificado com o [Nome de entidade alternativo](http://en.wikipedia.org/wiki/SubjectAltName) (`subjectAltName`).

Quando você souber qual comprar o certificado SSL, você pode enviar um certificado de assinatura solicitar CSR () para uma autoridade de certificação. Quando você voltar certificado solicitado da CA, em seguida, gerar um arquivo. pfx do certificado. Você pode executar essas etapas usando a ferramenta de sua escolha. Aqui estão as instruções para as ferramentas comuns:

- [Etapas de Certreq.exe](#bkmk_certreq) - o utilitário do Windows para a criação de solicitações de certificado. Foi parte do Windows desde o Windows XP/Windows Server 2000.
- [Etapas de Gerenciador do IIS](#bkmk_iismgr) - a ferramenta de escolha se você já estiver familiarizado com ele.
- [Etapas de OpenSSL](#bkmk_openssl) - uma [ferramenta de código-fonte aberto entre plataformas](https://www.openssl.org). Usá-lo para ajudá-lo a obter um certificado SSL de qualquer plataforma.
- Para obter as etapas de [etapas de subjectAltName usando OpenSSL](#bkmk_subjectaltname) - `subjectAltName` certificados.

Se quiser testar a instalação em um serviço de aplicativo antes de comprar um certificado, você pode gerar um [certificado auto-assinado](https://en.wikipedia.org/wiki/Self-signed_certificate). Este tutorial oferece duas maneiras de gerá-lo:

- [Certificado auto-assinado, Certreq.exe etapas](#bkmk_sscertreq)
- [Certificado auto-assinado, OpenSSL etapas](#bkmk_ssopenssl)

<a name="bkmk_certreq"></a>
### <a name="get-a-certificate-using-certreqexe"></a>Obter um certificado usando Certreq.exe

1. Criar um arquivo (por exemplo, **myrequest.txt**) e copie o seguinte texto e salve-o em uma pasta de trabalho. Substituir o `<your-domain>` espaço reservado com o nome de domínio personalizado do seu aplicativo.

        [NewRequest]
        Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
        Exportable = TRUE
        KeyLength = 2048              ; Required minimum is 2048
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = True
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12
        HashAlgorithm = SHA256

        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1         ; Server Authentication

    Para obter mais informações sobre as opções no CSR e outras opções disponíveis, consulte a [documentação de referência Certreq](https://technet.microsoft.com/library/dn296456.aspx).

4. No prompt de comando, `CD` em sua pasta de trabalho e executar o comando a seguir para criar o CSR:

        certreq -new myrequest.txt myrequest.csr

    **myrequest.CSR** agora é criado no seu diretório de trabalho atual.

5. Envie **myrequest.csr** para uma autoridade de certificação para obter um certificado SSL. Você carregar o arquivo ou copie o conteúdo de um editor de texto para um formulário da web.

    Para obter uma lista de autoridades de certificação confiável pela Microsoft, consulte [programa de certificado raiz confiável Microsoft: participantes][cas].

6. Depois que a autoridade de certificação respondeu a você com um certificado (. Arquivo CER), salve-o na sua pasta de trabalho. Em seguida, execute o seguinte comando para concluir o CSR pendente.

        certreq -accept -user <certificate-name>.cer

    Este comando armazena o certificado concluído no repositório de certificado do Windows.

6. Se a sua autoridade de certificação usa certificados intermediários, instalá-los antes de continuar. Eles normalmente vêm como um download separado da autoridade de certificação e em vários formatos para tipos de servidor de web diferente. Selecione a versão do Microsoft IIS.

    Depois que você baixou os certificados, clique com botão direito cada um no Windows Explorer e selecione  **Instalar certificado**. Use os valores padrão do **Assistente de importação de certificado**e continue selecionando **Avançar** até concluir a importação.

7. Para exportar o certificado SSL do repositório do certificado, pressione `Win` + `R` e executar **certmgr. msc** para iniciar o Gerenciador de certificados. Selecione **pessoal** > **certificados**. Na coluna **Emitido para** , você deverá ver uma entrada com seu nome de domínio personalizado e autoridade de certificação usada para gerar o certificado na coluna **Emitido por** .

    ![Inserir imagem do Gerenciador de certificação aqui][certmgr]

9. O certificado de atalho e selecione **Todas as tarefas** > **Exportar**. No **Assistente para exportação de certificado**, clique em **Avançar**e em seguida, selecione **Sim, exportar a chave privada**e clique em **Avançar** novamente.

    ![Exportar a chave privada][certwiz1]

10. Selecione **Troca de informações pessoais - PKCS #12**, **incluir todos os certificados no caminho de certificação, se possível**e **Exportar todas as propriedades estendidas**. Em seguida, clique em **Avançar**.

    ![incluir todos os certificados e as propriedades estendidas][certwiz2]

11. Selecione **senha**e, em seguida, digite e confirme a senha. Clique em **Avançar**.

    ![especificar uma senha][certwiz3]

12. Forneça um caminho e nome do arquivo para o certificado exportado, com a extensão **. pfx**. Clique em **Avançar** para concluir.

    ![fornecer um caminho de arquivo][certwiz4]

Agora você está pronto para carregar o arquivo PFX exportado no aplicativo de serviço. Consulte [etapa 2. Carregar e vincular o certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_iismgr"></a>
### <a name="get-a-certificate-using-the-iis-manager"></a>Obter um certificado usando o Gerenciador de IIS

1. Gere um CSR com Gerenciador do IIS para enviar à autoridade de certificação. Para obter mais informações sobre como gerar um CSR, consulte [solicitar um certificado de servidor de Internet (IIS 7)][iiscsr].

3. Envie sua CSR para uma autoridade de certificação para obter um certificado SSL. Para obter uma lista de autoridades de certificação confiável pela Microsoft, consulte [programa de certificado raiz confiável Microsoft: participantes][cas].


3. Conclua o CSR com o certificado que a autoridade de certificação envia para você. Para obter mais informações sobre como concluir o CSR, consulte [instalar um certificado de servidor de Internet (IIS 7)][installcertiis].

4. Se a sua autoridade de certificação usa certificados intermediários, instalá-los antes de continuar. Eles normalmente vêm como um download separado da autoridade de certificação e em vários formatos para tipos de servidor de web diferente. Selecione a versão do Microsoft IIS.

    Depois que você baixou os certificados, clique com botão direito cada um no Windows Explorer e selecione **Instalar certificado**. 
    Use os valores padrão do **Assistente de importação de certificado**e continue selecionando **Avançar** até concluir a importação.

4. Exporte o certificado SSL Gerenciador do IIS. Para obter mais informações sobre como exportar o certificado, consulte [Exportar um certificado de servidor (IIS 7)][exportcertiis]. 

    >[AZURE.IMPORTANT] No **Assistente para exportação de certificado**, certifique-se de que você selecione **Sim, exportar a chave privada**  
    >
    >![Exportar a chave privada][certwiz1]  
    >
    > e também selecione **Troca de informações pessoais - PKCS #12**, **incluir todos os certificados no caminho de certificação, se possível**e     **Exportar todas as propriedades estendidas**.
    >
    >![incluir todos os certificados e as propriedades estendidas][certwiz2]

Agora você está pronto para carregar o arquivo PFX exportado no aplicativo de serviço. Consulte [etapa 2. Carregar e vincular o certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_openssl"></a>
### <a name="get-a-certificate-using-openssl"></a>Obter um certificado usando OpenSSL

1. No terminal linha de comando, `CD` em um diretório de trabalho gerar uma chave privada e CSR executando o seguinte comando:

        openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. Quando solicitado, digite as informações apropriadas. Por exemplo:

        Country Name (2 letter code)
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

        Please enter the following 'extra' attributes to be sent with your certificate request

        A challenge password []:

    Quando tiver terminado, você deve ter dois arquivos no seu diretório de trabalho: **myserver.key** e **server.csr**. 
    O **server.csr** contém o CSR, e você precisa **myserver.key** mais tarde.

3. Envie sua CSR para uma autoridade de certificação para obter um certificado SSL. Para obter uma lista de autoridades de certificação confiável pela Microsoft, consulte [programa de certificado raiz confiável Microsoft: participantes][cas].


4. Depois que a autoridade de certificação envia o certificado solicitado, salve-o em um arquivo denominado **myserver.crt** no diretório de trabalho. Se sua CA fornece-lo em um formato de texto, basta copiar o conteúdo para **myserver.crt** em um editor de texto e salvá-lo. O arquivo deve parecer com o seguinte:

        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----

5. No terminal de linha de comando, execute o seguinte comando para exportar **myserver.pfx** do **myserver.key** e **myserver.crt**:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    Quando solicitado, defina uma senha para proteger o arquivo. pfx.

    > [AZURE.NOTE] Se a sua autoridade de certificação usa certificados intermediários, você deve incluí-los com o `-certfile` parâmetro. Eles normalmente vêm como um download separado da autoridade de certificação e em vários formatos para tipos de servidor de web diferente. Selecione a versão com o `.pem` extensão.
    >
    > Seu `openssl -export` comando deve parecer com o exemplo a seguir, que cria um arquivo. pfx que inclui os certificados intermediários do arquivo **intermediária-cets.pem** :
    >  
    > `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`

Agora você está pronto para carregar o arquivo PFX exportado no aplicativo de serviço. Consulte [etapa 2. Carregar e vincular o certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_subjectaltname"></a>
### <a name="get-a-subjectaltname-certificate-using-openssl"></a>Obter um certificado de SubjectAltName usando OpenSSL

1. Criar um arquivo denominado **sancert.cnf**, copie o seguinte texto nele e salvá-lo em um diretório de trabalho:

        # -------------- BEGIN custom sancert.cnf -----
        HOME = .
        oid_section = new_oids
        [ new_oids ]
        [ req ]
        default_days = 730
        distinguished_name = req_distinguished_name
        encrypt_key = no
        string_mask = nombstr
        req_extensions = v3_req # Extensions to add to certificate request
        [ req_distinguished_name ]
        countryName = Country Name (2 letter code)
        countryName_default =
        stateOrProvinceName = State or Province Name (full name)
        stateOrProvinceName_default =
        localityName = Locality Name (eg, city)
        localityName_default =
        organizationalUnitName  = Organizational Unit Name (eg, section)
        organizationalUnitName_default  =
        commonName              = Your common name (eg, domain name)
        commonName_default      = www.mydomain.com
        commonName_max = 64
        [ v3_req ]
        subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
        # -------------- END custom sancert.cnf -----

    Na linha que começa com `subjectAltName`, substitua o valor com todos os nomes de domínio que você deseja proteger (além  `commonName`). Por exemplo:

        subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

    Você não precisa alterar qualquer outro campo, incluindo `commonName`. Você será solicitado a especificá-los nas próximas etapas.

1. No terminal linha de comando, `CD` em sua pasta de trabalho e execute o seguinte comando:

        openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. Quando solicitado, digite as informações apropriadas. Por exemplo:

        Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com

    Quando terminar, você deve ter dois arquivos no seu diretório de trabalho: **myserver.key** e **server.csr**. 
    O **server.csr** contém o CSR, e você precisa **myserver.key** mais tarde.

3. Envie sua CSR para uma autoridade de certificação para obter um certificado SSL. Para obter uma lista de autoridades de certificação confiável pela Microsoft, consulte [programa de certificado raiz confiável Microsoft: participantes][cas].


4. Depois que a autoridade de certificação envia o certificado solicitado, salve-o em um arquivo denominado **myserver.crt**. Se sua CA fornece-lo em um formato de texto, basta copiar o conteúdo para **myserver.crt** em um editor de texto e salvá-lo. O arquivo deve parecer com o seguinte:

        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----

5. No terminal de linha de comando, execute o seguinte comando para exportar **myserver.pfx** do **myserver.key** e **myserver.crt**:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    Quando solicitado, defina uma senha para proteger o arquivo. pfx.

    > [AZURE.NOTE] Se a sua autoridade de certificação usa certificados intermediários, você deve incluí-los com o `-certfile` parâmetro. Eles normalmente vêm como um download separado da autoridade de certificação e em vários formatos para tipos de servidor de web diferente. Selecione a versão com o `.pem` extensão).
    >
    > Seu `openssl -export` comando deve parecer com o exemplo a seguir, que cria um arquivo. pfx que inclui os certificados intermediários do arquivo **intermediária-cets.pem** :
    >  
    > `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`

Agora você está pronto para carregar o arquivo PFX exportado no aplicativo de serviço. Consulte [etapa 2. Carregar e vincular o certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_sscertreq"></a>
### <a name="generate-a-self-signed-certificate-using-certreqexe"></a>Gerar um certificado auto-assinado usando Certreq.exe ###

>[AZURE.IMPORTANT] Certificados autoassinados são somente para teste. A maioria dos navegadores retornarão erros quando visitar um site que é protegido por um certificado auto-assinado. Alguns navegadores podem recusar mesmo navegar até o site. 

1. Criar um arquivo de texto (por exemplo, **mycert.txt**), copie o seguinte texto e salve o arquivo em uma pasta de trabalho. Substituir o `<your-domain>` espaço reservado com o nome de domínio personalizado do seu aplicativo.

        [NewRequest]
        Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
        Exportable = TRUE
        KeyLength = 2048              ; KeyLength can be 2048, 4096, 8192, or 16384 (required minimum is 2048)
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = True
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12
        HashAlgorithm = SHA256
        RequestType = Cert            ; Self-signed certificate
        ValidityPeriod = Years
        ValidityPeriodUnits = 1

        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1         ; Server Authentication

    O parâmetro importante é `RequestType = Cert`, que especifica um certificado auto-assinado. 
    Para obter mais informações sobre as opções no CSR e outras opções disponíveis, consulte a [documentação de referência Certreq](https://technet.microsoft.com/library/dn296456.aspx).

4. No prompt de comando, `CD` para seu diretório de trabalho e executar o seguinte comando:

        certreq -new mycert.txt mycert.crt
    
    O novo certificado auto-assinado agora está instalado no repositório de certificados.

7. Para exportar o certificado do repositório do certificado, pressione `Win` + `R` e executar **certmgr. msc** para iniciar o Gerenciador de certificados. Selecione **pessoal** > **certificados**. Na coluna **Emitido para** , você deverá ver uma entrada com seu nome de domínio personalizado e autoridade de certificação usada para gerar o certificado na coluna **Emitido por** .

    ![Inserir imagem do Gerenciador de certificação aqui][certmgr]

9. O certificado de atalho e selecione **Todas as tarefas** > **Exportar**. No **Assistente para exportação de certificado**, clique em **Avançar**e em seguida, selecione **Sim, exportar a chave privada**e clique em **Avançar** novamente.

    ![Exportar a chave privada][certwiz1]

10. Selecione **Troca de informações pessoais - PKCS #12**, **incluir todos os certificados no caminho de certificação, se possível**e **Exportar todas as propriedades estendidas**. Em seguida, clique em **Avançar**.

    ![incluir todos os certificados e as propriedades estendidas][certwiz2]

11. Selecione **senha**e, em seguida, digite e confirme a senha. Clique em **Avançar**.

    ![especificar uma senha][certwiz3]

12. Forneça um caminho e nome do arquivo para o certificado exportado, com a extensão **. pfx**. Clique em **Avançar** para concluir.

    ![fornecer um caminho de arquivo][certwiz4]

Agora você está pronto para carregar o arquivo PFX exportado no aplicativo de serviço. Consulte [etapa 2. Carregar e vincular o certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_ssopenssl"></a>
###<a name="generate-a-self-signed-certificate-using-openssl"></a>Gerar um certificado auto-assinado usando OpenSSL ###

>[AZURE.IMPORTANT] Certificados autoassinados são somente para teste. A maioria dos navegadores retornarão erros quando visitar um site que é protegido por um certificado auto-assinado. Alguns navegadores podem recusar mesmo navegar até o site. 

1. Crie um arquivo de texto chamado **serverauth.cnf**, e em seguida, copie o seguinte conteúdo nele e, em seguida, salve-o em um diretório de trabalho:

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName         = Country Name (2 letter code)
        countryName_min         = 2
        countryName_max         = 2
        stateOrProvinceName     = State or Province Name (full name)
        localityName            = Locality Name (eg, city)
        0.organizationName      = Organization Name (eg, company)
        organizationalUnitName      = Organizational Unit Name (eg, section)
        commonName          = Common Name (eg, your app's domain name)
        commonName_max          = 64
        emailAddress            = Email Address
        emailAddress_max        = 40

        [ req_attributes ]
        challengePassword       = A challenge password
        challengePassword_min       = 4
        challengePassword_max       = 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

2. No terminal linha de comando, `CD` em sua pasta de trabalho e execute o seguinte comando:

        openssl req -sha256 -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

    Esse comando cria dois arquivos: **myserver.crt** (o certificado auto-assinado) e **myserver.key** (a chave privada), com base nas configurações no **serverauth.cnf**.

3. Exporte o certificado para um arquivo. pfx executando o seguinte comando:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    Quando solicitado, defina uma senha para proteger o arquivo. pfx.

Agora você está pronto para carregar o arquivo PFX exportado no aplicativo de serviço. Consulte [etapa 2. Carregar e vincular o certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_configuressl"></a>
## <a name="step-2-upload-and-bind-the-custom-ssl-certificate"></a>Etapa 2. Carregar e vincular o certificado SSL personalizado

Antes de passar, examine a seção [o que é necessário](#bkmk_domainname) e verifique se:

- você tiver um domínio personalizado que mapeia para seu aplicativo do Azure,
- seu aplicativo é executado na camada **básica** ou superior, e
- Você tem um certificado SSL para o domínio personalizado de uma autoridade de certificação.


1. No seu navegador, abra a ** [Portal Azure.](https://portal.azure.com/)**
2.  Clique na opção de **Serviço de aplicativo** no lado esquerdo da página.
3.  Clique no nome do aplicativo ao qual você deseja atribuir este certificado. 
4.  Nas **configurações**, clique em **certificados SSL**
5.  Clique em **carregar certificado**
6.  Selecione o arquivo. pfx exportado na [etapa 1](#bkmk_getcert) e especifique a senha que você criar antes. Em seguida, clique em **carregar** para carregar o certificado. Agora você deve ver seu certificado carregado novamente na lâmina **certificado SSL** .
7. Clique em **Adicionar ligações** da seção nas **ligações ssl**
8. Na lâmina **Adicionar vinculação SSL** use menus suspensos para selecionar o nome de domínio para proteger com SSL e o certificado para usar. Você também pode selecionar se deseja usar **[Indicação de nome de servidor (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ou SSL baseada em IP.

    ![Inserir imagem de ligações SSL](./media/web-sites-configure-ssl-certificate/sslbindings.png)

       •    IP based SSL associates a certificate with a domain name by mapping the dedicated public IP address of the server to the domain name. This requires each domain name (contoso.com, fabricam.com, etc.) associated with your service to have a dedicated IP address. This is the traditional          method of associating SSL certificates with a web server.
       •    SNI based SSL is an extension to SSL and **[Transport Layer Security](http://en.wikipedia.org/wiki/Transport_Layer_Security)** (TLS) that allows multiple domains to share the same IP address, with separate security certificates for each domain. Most modern browsers (including Internet Explorer, Chrome, Firefox and Opera) support SNI, however older browsers may not support SNI. For more information on SNI, see the **[Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication)** article on Wikipedia.
     
9. Clique em **Adicionar vinculação** para salvar as alterações e habilitar SSL.

## <a name="step-3-change-your-domain-name-mapping-ip-based-ssl-only"></a>Etapa 3. Altere o mapeamento de nome de domínio (IP com base SSL somente)

Se você usar **SSL SNI** ligações somente, pule esta seção. Várias ligações **SNI SSL** podem trabalhar juntos no endereço de IP existente compartilhado atribuído ao seu aplicativo. No entanto, se você criar uma associação de **SSL baseada em IP** , o serviço de aplicativo cria um endereço IP dedicado para a vinculação porque o **SSL baseada em IP** requer um. Somente um dedicado IP endereço pode ser criado, portanto, somente uma vinculação **SSL baseada em IP** pode ser adicionada.

Devido a este endereço IP dedicado, você precisará configurar o seu aplicativo ainda mais se:

- [Usado um registro para mapear seu domínio personalizado](web-sites-custom-domain-name.md#a) para seu aplicativo do Azure e você acabou de adicionar uma ligação **SSL baseada em IP** . Neste cenário, você precisa mapear novamente o existente de um registro para apontar para o endereço IP dedicado seguindo estas etapas:

    1. Após ter configurado um IP baseadas vinculação SSL, um endereço IP dedicado é atribuído a seu aplicativo. Você pode encontrar esse endereço IP na página de **domínio personalizado** em configurações do aplicativo, logo acima da seção de **nomes de host** . Ele será listado como **Endereço IP externo**
    
        ![Endereço IP virtual](./media/web-sites-configure-ssl-certificate/virtual-ip-address.png)

    2. [Mapear novamente o registro para o seu nome de domínio personalizado para este novo endereço IP](web-sites-custom-domain-name.md#a).

- Você já tem uma ou mais ligações **SNI SSL** em seu aplicativo, e você acabou de adicionar uma ligação **SSL baseada em IP** . Assim que a vinculação é concluída, o * &lt;appname >*. azurewebsites.net pontos de nome de domínio para o novo endereço IP. Portanto, qualquer existente [mapeamento de CNAME do domínio personalizado](web-sites-custom-domain-name.md#cname) para * &lt;appname >*. azurewebsites.net, inclusive aqueles que a **SNI SSL** seguro, também recebe tráfego no novo endereço, que é criado para o **IP com base em SSL** somente. Neste cenário, você precisa enviar o tráfego de **SSL SNI** volta para o endereço IP compartilhado original seguindo estas etapas:

    1. Identifique todos os [mapeamentos CNAME de domínios personalizados](web-sites-custom-domain-name.md#cname) para o seu aplicativo que tenha uma associação **SNI SSL** .

    2. Mapear novamente cada registro CNAME para **sni.** &lt;appname >. azurewebsites.net em vez de &lt;appname >. azurewebsites.net.

## <a name="step-4-test-https-for-your-custom-domain"></a>Etapa 4. Teste HTTPS para seu domínio personalizado

Tudo o que falta para fazer agora é garantir que HTTPS funciona para seu domínio personalizado. Em vários navegadores, navegue até `https://<your.custom.domain>` para ver que ele serve o seu aplicativo.

- Se seu aplicativo gera você erros de validação de certificado, provavelmente você está usando um certificado auto-assinado.

- Se não for o caso, você talvez tenha deixado os certificados intermediários quando você exporta seu certificado. pfx. Volte para [o que você precisa](#bkmk_domainname) verificar se sua CSR atende a todos os requisitos pelo serviço de aplicativo.

<a name="bkmk_enforce"></a>
## <a name="enforce-https-on-your-app"></a>Impor HTTPS no seu aplicativo

Se você ainda quiser permitir o acesso HTTP para o seu aplicativo, pule esta etapa. Serviço de aplicativo faz *não* impor HTTPS, para que os visitantes ainda possam acessar seu aplicativo usando HTTP. Se você quiser aplicar HTTPS para seu aplicativo, você pode definir uma regra de reconfiguração no `web.config` arquivo para seu aplicativo. Cada aplicativo de serviço de aplicativo tem esse arquivo, independentemente da estrutura de idioma do seu aplicativo.

> [AZURE.NOTE] Não há redirecionamento de idiomas específicos de solicitações. ASP.NET MVC pode usar o filtro de [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) em vez da regra de reconfiguração em `web.config` (consulte [implantar um aplicativo ASP.NET MVC 5 seguro para um aplicativo web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)).

Siga estas etapas:

1. Navegue até o console de depuração de Kudu para o aplicativo. Seu endereço é `https://<appname>.scm.azurewebsites.net/DebugConsole`.

2. No console de depuração, CD para `D:\home\site\wwwroot`.

3. Abrir `web.config` clicando no botão de lápis.

    ![](./media/web-sites-configure-ssl-certificate/openwebconfig.png)

    Se você implantar seu aplicativo com o Visual Studio ou gito, serviço de aplicativo gera automaticamente o apropriado `web.config` para o aplicativo .NET, PHP, Node ou Python na raiz do aplicativo. 
    Se `web.config` não existir, execute `touch web.config` no prompt de comando baseado na web para criá-lo. Ou, você pode criá-lo em seu projeto local e reimplantar seu código.

4. Se você tinha que criar um `web.config`, copie o código a seguir para ela e salvá-lo. Se você abriu um Web. config existentes, você só precisa copiar toda a `<rule>` marca sua `web.config`do `configuration/system.webServer/rewrite/rules` elemento.

        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>
          <system.webServer>
            <rewrite>
              <rules>
                <!-- BEGIN rule TAG FOR HTTPS REDIRECT -->
                <rule name="Force HTTPS" enabled="true">
                  <match url="(.*)" ignoreCase="false" />
                  <conditions>
                    <add input="{HTTPS}" pattern="off" />
                  </conditions>
                  <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
                </rule>
                <!-- END rule TAG FOR HTTPS REDIRECT -->
              </rules>
            </rewrite>
          </system.webServer>
        </configuration>

    Essa regra retorna um HTTP 301 (redirecionamento permanente) para o protocolo HTTPS sempre que o usuário solicitar uma página usando HTTP. Ele redireciona de http://contoso.com para https://contoso.com.

    >[AZURE.IMPORTANT] Se houver já outros `<rule>` marcas no seu `web.config`, coloque o copiada `<rule>` marca antes da outra `<rule>` marcas.

4. Salve o arquivo no console de depuração de Kudu. Devo efetivadas imediatamente redirecionar todas as solicitações para HTTPS.

Para obter mais informações sobre o módulo regravação de URL do IIS, consulte a documentação de [Regravação de URL](http://www.iis.net/downloads/microsoft/url-rewrite) .

## <a name="more-resources"></a>Mais recursos ##
- [Central de confiabilidade do Microsoft Azure](/support/trust-center/security/)
- [Opções de configuração desbloqueadas em Sites do Azure](/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Habilitar o log de diagnóstico](web-sites-enable-diagnostic-log.md)
- [Configurar aplicativos da web no serviço de aplicativo do Azure](web-sites-configure.md)
- [Portal de gerenciamento do Azure](https://manage.windowsazure.com)

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

[customdomain]: web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://social.technet.microsoft.com/wiki/contents/articles/31634.microsoft-trusted-root-certificate-program-participants-v-2016-april.aspx
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/web-sites-configure-ssl-certificate/staticip.png
[website]: ./media/web-sites-configure-ssl-certificate/sslwebsite.png
[scale]: ./media/web-sites-configure-ssl-certificate/sslscale.png
[standard]: ./media/web-sites-configure-ssl-certificate/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/web-sites-configure-ssl-certificate/sslconfig.png
[uploadcert]: ./media/web-sites-configure-ssl-certificate/ssluploadcert.png
[uploadcertdlg]: ./media/web-sites-configure-ssl-certificate/ssluploaddlg.png
[sslbindings]: ./media/web-sites-configure-ssl-certificate/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/web-sites-configure-ssl-certificate/waws-certmgr.png
[certwiz1]: ./media/web-sites-configure-ssl-certificate/waws-certwiz1.png
[certwiz2]: ./media/web-sites-configure-ssl-certificate/waws-certwiz2.png
[certwiz3]: ./media/web-sites-configure-ssl-certificate/waws-certwiz3.png
[certwiz4]: ./media/web-sites-configure-ssl-certificate/waws-certwiz4.png


