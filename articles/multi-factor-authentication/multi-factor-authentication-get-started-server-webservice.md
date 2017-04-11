<properties 
    pageTitle="Introdução o serviço MFA Server Mobile aplicativo Web"
    description="O aplicativo de autenticação multifator Azure oferece uma opção de autenticação adicionais de fora da banda.  Ele permite que o servidor MFA usar notificações de envio para os usuários."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="getting-started-the-mfa-server-mobile-app-web-service"></a>Introdução o serviço MFA Server Mobile aplicativo Web

O aplicativo de autenticação multifator Azure oferece uma opção de autenticação adicionais de fora da banda. Em vez de colocar um telefonema automatizada ou SMS ao usuário durante o logon, a autenticação multifator Azure envia uma notificação para o aplicativo de autenticação multifator Azure no smartphone ou tablet do usuário. O usuário simplesmente toca "Autenticar" (ou insira um PIN e toca "Autenticar") no aplicativo para efetuar login.

Para usar o aplicativo de autenticação multifator Azure, a seguir é necessária para que o aplicativo pode se comunicar com êxito com o serviço móvel do aplicativo Web:

- Consulte requisitos de Hardware e Software para requisitos de hardware e software
- Você deve estar usando v 6.0 ou superior do Azure multifator autenticação de servidor
- Serviço móvel do aplicativo Web deve ser instalado em um servidor de web voltado para a Internet executando o Microsoft® Internet Information Services (IIS) IIS 7. x ou superior.  Para obter mais informações sobre IIS consulte [IIS.NET](http://www.iis.net/).
- Certifique-se de v4.0.30319 ASP.NET é instalado, registrado e definido para permitido
- Serviços de função necessários incluem ASP.NET e compatibilidade de Metabase do IIS 6
- Serviço móvel do aplicativo Web deve ser acessado por meio de uma URL pública
- Serviço móvel do aplicativo Web deve ser protegido com um certificado SSL.
- O SDK de serviço Web do Azure multifator autenticação deve ser instalado no IIS 7. x ou superior no servidor que o servidor de autenticação multifator do Azure
- O SDK de serviço Web do Azure multifator autenticação deve ser protegido com um certificado SSL.
- Serviço móvel do aplicativo Web deve ser capaz de se conectar o SDK de serviço Web do Azure multifator autenticação SSL
- Serviço móvel do aplicativo Web deve ser capaz de autenticar SDK do Azure multifator autenticação Web serviço usando as credenciais de uma conta de serviço que é um membro de um grupo de segurança chamado "PhoneFactor administradores". Essa conta de serviço e o grupo existirem no Active Directory se o servidor de autenticação multifator Azure está em execução em um servidor de domínio. Essa conta de serviço e o grupo existirem localmente no servidor de autenticação multifator do Azure se ele não está associado a um domínio.


Instalando o portal de usuário em um servidor diferente do servidor de autenticação multifator Azure requer três etapas a seguir:

1. Instalar o SDK do serviço web
2. Instalar o serviço da web de aplicativo móvel
3. Definir as configurações de aplicativo móvel no servidor de autenticação multifator do Azure
4. Ativar o aplicativo de autenticação multifator Azure para usuários finais

## <a name="install-the-web-service-sdk"></a>Instalar o SDK do serviço web

Se o SDK de serviço Web autenticação do Azure multifator já não estiver instalado no servidor de autenticação multifator do Azure, vá para o servidor e abra o servidor de autenticação multifator Azure. Clique no ícone de SDK de serviço Web, clique no SDK do serviço Web instalar … botão e siga as instruções apresentadas. O SDK do serviço Web deve ser protegido com um certificado SSL. Um certificado auto-assinado é okey para essa finalidade, mas ele deve ser importado para o armazenamento de "Confiáveis autoridades de certificação raiz" da conta do computador Local no servidor web Portal de usuário para que ele confie certificado ao iniciar a conexão SSL.

<center>![Configuração](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## <a name="install-the-mobile-app-web-service"></a>Instalar o serviço da web de aplicativo móvel
Antes de instalar o serviço da web de aplicativo móvel, lembre-se dos seguintes procedimentos:

- Se o Portal de usuário de autenticação do Azure multifator já estiver instalado no servidor voltado para a Internet, o nome de usuário, senha e URL para o SDK do serviço Web podem ser copiados do arquivo de Web. config do Portal de usuário.
- É útil abrir um navegador da web no servidor web voltado para a Internet e navegue até a URL do SDK do serviço da Web que foi inserido no arquivo Web. config. Se o navegador pode acessar o serviço da web com êxito, ele deve solicitar credenciais. Insira o nome de usuário e senha que foram inseridas no arquivo Web. config exatamente como ele aparece no arquivo. Certifique-se de que não há erros ou avisos de certificado são exibidos.
- Se um firewall ou proxy reverso é situados na frente do servidor de web do serviço da Web App Mobile e executar SSL transferindo, você pode editar o arquivo de Web. config do aplicativo Web serviço móvel e adicione a seguinte chave para o <appSettings> seção para que o serviço da Web App Mobile possa usar http em vez de https. No entanto SSL ainda é necessário do aplicativo móvel para o proxy de firewall/reversão. <add key="SSL_REQUIRED" value="false"/>

### <a name="to-install-the-mobile-app-web-service"></a>Para instalar o serviço da web de aplicativo móvel

<ol>
<li>Abra o Windows Explorer no servidor de autenticação multifator do Azure e navegue até a pasta onde o servidor de autenticação multifator Azure está instalado (por exemplo, C:\Program Files\Azure autenticação multifator). Escolha a versão de 32 bits ou 64 bits do Azure multifator AuthenticationPhoneAppWebServiceSetup instalação arquivo conforme apropriado para o servidor que serviço móvel do Web App será instalado no. Copie o arquivo de instalação no servidor voltado para a Internet.</li>

<li>No servidor web voltado para a Internet, o arquivo de configuração deve ser executado com direitos de administrador. A maneira mais fácil de fazer isso é abrir um prompt de comando como administrador e navegue até o local onde o arquivo de instalação foi copiado.</li>  

<li>Execute o arquivo de instalação AuthenticationMobileAppWebServiceSetup multifator, altere o Site se desejado e altere o diretório Virtual para um nome curto como "PA". Um nome curto diretório virtual é recomendável desde que os usuários devem inserir a URL do serviço da Web de aplicativo Mobile no dispositivo móvel durante a ativação.</li>

<li>Após concluir a instalação do Azure multifator AuthenticationMobileAppWebServiceSetup, navegue até C:\inetpub\wwwroot\PA (ou diretório apropriado com base no nome do diretório virtual) e edite o arquivo Web. config.</li>  

<li>Localize as chaves WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD e defina os valores para o nome de usuário e a senha da conta de serviço que é um membro da segurança PhoneFactor Admins agrupar (consulte a seção requisitos acima). Isso pode ser a mesma conta que está sendo usada como a identidade do Portal de usuário de autenticação do Azure multifator se que foi instalado anteriormente. Certifique-se de digitar o nome de usuário e senha entre as aspas no final da linha, (valor = "" / >). É recomendável usar um nome de usuário qualificado (por exemplo, domínio \ nomedeusuário ou computador).</li>  

<li>Localize a configuração do aplicativo Web Service_pfwssdk_PfWsSdk pfMobile e altere o valor de "http://localhost:4898/PfWsSdk.asmx" para a URL do SDK do serviço da Web que está executando o servidor de autenticação multifator do Azure (por exemplo, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Como SSL é usado para esta conexão, você deve fazer referência o SDK do serviço Web pelo nome do servidor e não endereço IP desde o certificado SSL será tiver sido emitido para o nome do servidor e a URL usada deve corresponder ao nome no certificado. Se o nome do servidor não resolver para um endereço IP do servidor voltado para a Internet, adicione uma entrada para o arquivo de hosts nesse servidor para mapear o nome do servidor que autenticação multifator Azure para seu endereço IP. Salve o arquivo Web. config após alterações foram feitas.</li>  

<li>Se o site que o serviço móvel do aplicativo da Web foi instalado em (por exemplo, Site padrão) ainda não tenha sido vinculado com um certificado assinado publicamente, instalar o certificado no servidor se não estiver instalado, abra o Gerenciador de IIS e vincular o certificado para o site.</li>  

<li>Abra um navegador da web de qualquer computador e navegue até a URL onde o serviço da Web App Mobile foi instalado (por exemplo, https://www.publicwebsite.com/PA). Certifique-se de que não há erros ou avisos de certificado são exibidos.</li>

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Definir as configurações de aplicativo móvel no servidor de autenticação multifator do Azure
Agora que o serviço da web de aplicativo móvel estiver instalado, você precisa configurar o servidor de autenticação multifator Azure para trabalhar com o portal.

#### <a name="to-configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Para definir as configurações de aplicativo móvel no servidor de autenticação multifator do Azure

1. No servidor de autenticação multifator Azure, clique no ícone de Portal de usuário. Se os usuários podem controlar seus métodos de autenticação, na guia Configurações, em permitir que usuários selecionem método, verifique aplicativo Mobile. Sem esse recurso ativado, os usuários finais deverão entrar em contato com seu suporte técnico para concluir a ativação para o aplicativo de celular.
2. Verificar a permitir que usuários para ativar a caixa de aplicativo Mobile.
3. Marque a caixa Permitir que o registro de usuário.
4. Clique no ícone do aplicativo Mobile.
5. Insira a URL sendo usada com o diretório virtual que foi criado ao instalar o Azure multifator AuthenticationMobileAppWebServiceSetup. Um nome de conta podem ser inserido no espaço fornecido. Esse nome de empresa será exibido no aplicativo móvel. Se deixado em branco, o nome do seu provedor de autenticação multifator criado no Portal de gerenciamento do Azure será exibido.



<center>![Configuração](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>
