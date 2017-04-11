<properties 
    pageTitle="Implantando o portal de usuário para o servidor de autenticação multifator do Azure"
    description="Esta é a página de autenticação multifator do Azure que descreve como começar com o Azure MFA e o portal de usuário."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>

# <a name="deploying-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Implantando o portal de usuário para o servidor de autenticação multifator do Azure

O Portal de usuário permite ao administrador instalar e configurar o Portal do Azure multifator autenticação do usuário. O Portal de usuário é um site do IIS que permite aos usuários para se inscrever no Azure multi-Factor Authentication e manter suas contas. Um usuário pode alterar o número de telefone, alterar seu PIN ou ignorar a autenticação multifator Azure durante sua próxima entrada na.

Os usuários serão logon no portal do usuário usando seu nome de usuário normal e a senha e irá concluir uma chamada de autenticação multifator de Azure ou responder perguntas de segurança para concluir sua autenticação. Se o registro do usuário for permitido, um usuário irá configurar seu número de telefone e o PIN na primeira vez que fizerem logon no portal do usuário.

Administradores do Portal de usuário pode ser configurar e permissão para adicionar novos usuários e atualizar usuários existentes.

<center>![Configuração](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploying-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Implantando o portal de usuário no mesmo servidor como o servidor de autenticação multifator do Azure

Os seguintes pré-requisitos são necessários para instalar o Portal de usuários no mesmo servidor como o servidor de autenticação multifator Azure:

- IIS precisa estar instalado incluindo asp.net e IIS 6 meta a compatibilidade de base (para IIS 7 ou superior)
- Conectado usuário deve ter direitos de administrador para o computador e o domínio, se aplicável.  Isso ocorre porque a conta precisa ter permissões para criar grupos de segurança do Active Directory.

### <a name="to-deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Implante o portal de usuário para o servidor de autenticação multifator do Azure

1. Dentro do servidor de autenticação multifator Azure: Clique ícone de Portal de usuário no menu à esquerda, clique em botão de instalar o Portal de usuário.
1. Clique em Avançar.
1. Clique em Avançar.
1. Se o computador está associado a um domínio e configuração do Active Directory para proteger a comunicação entre o Portal de usuário e o serviço de autenticação multifator de Azure está incompleta, será exibida na etapa do Active Directory. Clique no botão Avançar para completar automaticamente essa configuração.
1. Clique em Avançar.
1. Clique em Avançar.
1. Clique em Fechar.
1. Abra um navegador da web de qualquer computador e navegue até a URL onde o usuário Portal foi instalado (por exemplo, https://www.publicwebsite.com/MultiFactorAuth). Certifique-se de que não há erros ou avisos de certificado são exibidos.

<center>![Configuração](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploying-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>Implantando o Portal de usuário do servidor do Azure autenticação multifator em um servidor separado

Para usar o aplicativo de autenticação multifator Azure, estes são necessários para que o aplicativo pode se comunicar com êxito com o Portal de usuário:

Consulte requisitos de Hardware e Software para requisitos de hardware e software:

- Você deve estar usando v 6.0 ou superior do Azure multifator autenticação de servidor.
- Portal de usuário deve ser instalado em um servidor de web voltado para a Internet executando o Microsoft® Internet Information Services (IIS) 6. x, IIS 7. x ou superior.
- Ao usar o IIS 6. x, certifique-se de v2.0.50727 ASP.NET é instalado, registrado e definido para permitido.
- Serviços de função necessários ao usar o IIS 7. x ou superior incluem ASP.NET e compatibilidade de Metabase do IIS 6.
- Portal de usuário deve ser protegido com um certificado SSL.
- O SDK de serviço Web do Azure multifator autenticação deve ser instalado no IIS 6. x, IIS 7. x ou superior no servidor que o servidor de autenticação multifator Azure está instalado.
- O SDK de serviço Web do Azure multifator autenticação deve ser protegido com um certificado SSL.
- Portal de usuário deve ser capaz de se conectar o SDK de serviço Web do Azure multifator autenticação SSL.
- Portal de usuário deve ser capaz de autenticar SDK do Azure multifator autenticação Web serviço usando as credenciais de uma conta de serviço que é um membro de um grupo de segurança chamado "PhoneFactor administradores". Essa conta de serviço e o grupo existirem no Active Directory se o servidor de autenticação multifator Azure está em execução em um servidor de domínio. Essa conta de serviço e o grupo existirem localmente no servidor de autenticação multifator do Azure se ele não está associado a um domínio.

Instalando o portal de usuário em um servidor diferente do servidor de autenticação multifator Azure requer três etapas a seguir:

1. Instalar o SDK do serviço web
2. Instalar o portal de usuário
3. Configurar definições de Portal do usuário no servidor do Azure autenticação multifator


### <a name="install-the-web-service-sdk"></a>Instalar o SDK do serviço web

Se o SDK de serviço Web autenticação do Azure multifator já não estiver instalado no servidor de autenticação multifator do Azure, vá para o servidor e abra o servidor de autenticação multifator Azure. Clique no ícone de SDK de serviço Web, clique no SDK do serviço Web instalar … botão e siga as instruções apresentadas. O SDK do serviço Web deve ser protegido com um certificado SSL. Um certificado auto-assinado é okey para essa finalidade, mas ele deve ser importado para o armazenamento de "Confiáveis autoridades de certificação raiz" da conta do computador Local no servidor web Portal de usuário para que ele confie certificado ao iniciar a conexão SSL.

<center>![Configuração](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="install-the-user-portal"></a>Instalar o portal de usuário

Antes de instalar o portal de usuário em um servidor separado, lembre-se dos seguintes procedimentos:

- É útil abrir um navegador da web no servidor web voltado para a Internet e navegue até a URL do SDK do serviço da Web que foi inserido no arquivo Web. config. Se o navegador pode acessar o serviço da web com êxito, ele deve solicitar credenciais. Insira o nome de usuário e senha que foram inseridas no arquivo Web. config exatamente como ele aparece no arquivo. Certifique-se de que não há erros ou avisos de certificado são exibidos.
- Se um firewall ou proxy reverso é situados na frente do servidor de web do Portal de usuário e executar SSL transferindo, você pode editar o arquivo de Web. config do Portal de usuário e adicionar a seguinte chave para o <appSettings> seção para que o Portal de usuário possa usar http em vez de https. <add key="SSL_REQUIRED" value="false"/>

#### <a name="to-install-the-user-portal"></a>Para instalar o portal de usuário

1. Abra o Windows Explorer no servidor do servidor de autenticação multifator do Azure e navegue até a pasta onde o servidor de autenticação multifator Azure está instalado (por exemplo, C:\Program Files\Multi fator autenticação de servidor). Escolha a versão de 32 bits ou 64 bits do arquivo de instalação MultiFactorAuthenticationUserPortalSetup conforme apropriado para o servidor que será instalado no Portal de usuário. Copie o arquivo de instalação no servidor voltado para a Internet.
2. No servidor web voltado para a Internet, o arquivo de configuração deve ser executado com direitos de administrador. A maneira mais fácil de fazer isso é abrir um prompt de comando como administrador e navegue até o local onde o arquivo de instalação foi copiado.
3. Execute o arquivo de instalação MultiFactorAuthenticationUserPortalSetup64, altere o nome de Site e diretório Virtual se desejado.
4. Após concluir a instalação do Portal do usuário, navegue até C:\inetpub\wwwroot\MultiFactorAuth (ou diretório apropriado com base no nome do diretório virtual) e edite o arquivo Web. config.
5. Localize a chave USE_WEB_SERVICE_SDK e altere o valor de false para true. Localize as chaves WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD e defina os valores para o nome de usuário e a senha da conta de serviço que é um membro da segurança PhoneFactor Admins agrupar (consulte a seção requisitos acima). Certifique-se de digitar o nome de usuário e senha entre as aspas no final da linha, (valor = "" / >). É recomendável usar um nome de usuário qualificado (por exemplo, domínio \ nomedeusuário ou computador)
6. Localize a configuração de pfup_pfwssdk_PfWsSdk e altere o valor de "http://localhost:4898/PfWsSdk.asmx" para a URL do SDK do serviço da Web que está executando o servidor de autenticação multifator do Azure (por exemplo, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Como SSL é usado para esta conexão, você deve fazer referência o SDK do serviço Web pelo nome do servidor e não endereço IP desde o certificado SSL será tiver sido emitido para o nome do servidor e a URL usada deve corresponder ao nome no certificado. Se o nome do servidor não resolver para um endereço IP do servidor voltado para a Internet, adicione uma entrada para o arquivo de hosts nesse servidor para mapear o nome do servidor que autenticação multifator Azure para seu endereço IP. Salve o arquivo Web. config após alterações foram feitas.
7. Se o site que o usuário Portal foi instalado em (por exemplo, Site padrão) ainda não tenha sido vinculado com um certificado assinado publicamente, instalar o certificado no servidor se não estiver instalado, abra o Gerenciador de IIS e vincular o certificado para o site.
8. Abra um navegador da web de qualquer computador e navegue até a URL onde o usuário Portal foi instalado (por exemplo, https://www.publicwebsite.com/MultiFactorAuth). Certifique-se de que não há erros ou avisos de certificado são exibidos.



## <a name="configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Configurar definições de portal do usuário no servidor de autenticação multifator do Azure
Agora que o portal estiver instalado, você precisa configurar o servidor de autenticação multifator Azure para trabalhar com o portal.

Servidor do Azure autenticação multifator fornece várias opções para o portal de usuário.  A tabela a seguir fornece uma lista dessas opções e obter uma explicação do que elas são usadas.

Configurações do Portal de usuário|Descrição|
:------------- | :------------- |
URL do Portal de usuário| Permite que você digite a URL de onde o portal está sendo hospedado.
Autenticação primária| Permite que você especifique o tipo de autenticação para usar ao entrar no portal.  Autenticação do Windows, Radius ou LDAP.
Permitir que os usuários fazer logon no|Permite que os usuários insiram um nome de usuário e senha na página de entrada para o portal de usuário.  Se isso não estiver selecionado, as caixas serão esmaecidas.
Permitir o registro de usuário|Permite que o usuário se inscrever no autenticação multifator fazendo-los a uma tela de instalação que solicitará informações adicionais, como número de telefone.  Prompt para telefone backup permite aos usuários especificar um número de telefone secundário.  Solicitar terceiro token JURAMENTO permite aos usuários especificar um token de JURAMENTO festa 3º.
Permite que usuários iniciem Bypass de uso único| Isso permite que os usuários iniciem uma única ignorar.  Se um conjuntos de usuário, ele será levado afetam na próxima vez que o usuário entra no.  Solicitar bypass segundos fornece aos usuários uma caixa para que eles possam alterar o padrão de 300 segundos.  Caso contrário, o bypass avulsa só é bom para 300 segundos.
Permitir que os usuários possam selecionar método| Permite aos usuários especificar o método de contato principal.  Isso pode ser chamada telefônica, mensagem de texto, aplicativo móvel ou token de JURAMENTO.
Permitir que os usuários possam selecionar idioma|  Permite ao usuário alterar o idioma usado para a chamada telefônica, mensagem de texto, aplicativo móvel ou token de JURAMENTO.
Permitir que os usuários ativar o aplicativo móvel| Permite que os usuários gerar um código de ativação para concluir o processo de ativação do aplicativo móvel que é usado com o servidor.  Você também pode definir o número de dispositivos que eles podem ativar isso.  Entre 1 e 10.
Usar perguntas de segurança de fallback|Permite que você use perguntas de segurança em caso de falha de autenticação multifator.  Você pode especificar o número de perguntas de segurança que devem ser respondidas com êxito.
Permitir que os usuários associar token de JURAMENTO de terceiros| Permite que os usuários especifiquem um token de JURAMENTO de terceiros.
Usar token de JURAMENTO para fallback|Permite o uso de um token de JURAMENTO no caso que a autenticação multifator não for bem-sucedido.  Você também pode especificar o tempo limite de sessão em minutos.
Habilitar o log|Habilita o log no portal do usuário.  Os arquivos de log estão localizados em: C:\Program Files\Multi fator autenticação Server\Logs.

A maioria dessas configurações são visíveis para o usuário quando estiverem habilitados e os sinais de usuário no portal do usuário.

![Configurações do portal de usuário](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Para configurar as definições de portal de usuário no servidor de autenticação multifator do Azure




1. No servidor de autenticação multifator Azure, clique no ícone de Portal de usuário. Na guia Configurações, insira a URL para o Portal de usuário na caixa de texto URL do Portal de usuário. Essa URL será inserida em emails que são enviadas aos usuários quando eles são importados para o servidor de autenticação multifator Azure se a funcionalidade de email foi habilitada.
2. Escolha as configurações que você deseja usar no Portal do usuário. Por exemplo, se os usuários podem controlar seus métodos de autenticação, certifique-se de que a opção Permitir que usuários para selecionar o método está marcada junto com os métodos pode escolher entre.
3. Clique no link Ajuda no canto superior direito para obter ajuda Noções básicas sobre qualquer uma das configurações exibidas.

<center>![Configuração](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>Guia de administradores
Essa guia simplesmente permite que você adicione usuários com privilégios administrativos.  Ao adicionar um administrador, você pode ajustar as permissões que recebem.  Dessa forma, você pode ter certeza conceder apenas as permissões necessárias para o administrador.  Basta clicar no botão Adicionar e selecione e usuário e suas permissões e clique em Adicionar.

![Administradores de portal de usuário](./media/multi-factor-authentication-get-started-portal/admin.png)


## <a name="security-questions"></a>Perguntas sobre segurança
Essa guia permite especificar as perguntas de segurança que os usuários precisarão fornecem respostas para se as perguntas de segurança de uso para fallback opção está selecionada.  Servidor de autenticação multifator Azure vem com perguntas padrão que você pode usar.  Você também pode alterar a ordem ou adicionar suas próprias perguntas.  Ao adicionar suas próprias perguntas, você pode especificar o idioma que você gostaria que esses pergunta apareça no também.

![Perguntas de portal de segurança do usuário](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## <a name="passed-sessions"></a>Sessões passadas

## <a name="saml"></a>SAML
Permite que você configurar o portal do usuário para aceitar declarações de um provedor de identidade usando SAML.  Você pode especificar a tempo limite sessão, especifique o certificado de verificação e o Log de redirecionamento de URL.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>IPs confiáveis
Essa guia permite especificar endereços IP simples ou intervalos de endereços IP que podem ser adicionados para que se um usuário está entrando em um desses endereços IP, autenticação multifator é ignorada.

![Portal de usuário IPs confiáveis](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>Inscrição de autoatendimento de usuário
Se quiser que seus usuários entrar e registrar você deve selecionar a permitir que usuários para efetuar login no e permitir opções de inscrição de usuário. Lembre-se de que as configurações que você seleciona afetará a experiência de entrada do usuário.

Por exemplo, quando um usuário fizer logon Portal do usuário e clica no botão Log In, que são tiradas em seguida, para a página de configuração de usuário de autenticação do Azure multifator.  Dependendo de como você configurou a autenticação multifator Azure, o usuário poderá selecionar o método de autenticação.  

Se selecionar o método de autenticação de chamadas de voz ou ter sido pré-configurada para usar esse método, a página solicitará que o usuário insira seu número de telefone principal e extensão se aplicável.  Eles também podem ter permissão para inserir um número de telefone de backup.  

![Portal de usuário IPs confiáveis](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Se o usuário é necessária para usar um PIN quando autenticam, a página também solicitará que ele insira um PIN.  Depois de inserir seus números de telefone e o PIN (se aplicável), o usuário clica a chamar-Me agora para autenticar botão.  Azure autenticação multifator executará uma autenticação de telefonema ao número de telefone principal do usuário.  O usuário deve atender a chamada de telefone e insira seu PIN (se aplicável) e pressione # para prosseguir para a próxima etapa do processo de inscrição automática.   

Se o usuário seleciona o método de autenticação de texto SMS ou foi pré-configurada para usar esse método, a página solicitará que o usuário para o seu número de telefone celular.  Se o usuário é necessária para usar um PIN quando autenticam, a página também solicitará que ele insira um PIN.  Depois de inserir seu número de telefone e o PIN (se aplicável), o usuário clica o texto Me agora para autenticar botão.  Azure autenticação multifator executará uma autenticação de SMS para celular do usuário.  O usuário deve receber o SMS que contém uma única-tempo-senha (OTP) e responder à mensagem com esse OTP plus seu PIN, se aplicável) para prosseguir para a próxima etapa do processo de inscrição automática.

![Portal de usuário SMS](./media/multi-factor-authentication-get-started-portal/text.png)   

Se o usuário seleciona o método de autenticação do aplicativo móvel ou foi pré-configurada para usar esse método, a página solicitará que o usuário para instalar o aplicativo de autenticação multifator de Azure em seu dispositivo e gerar um código de ativação.  Depois de instalar o aplicativo de autenticação multifator de Azure, o usuário clica no botão Gerar código de ativação.    

>[AZURE.NOTE]Para usar o aplicativo de autenticação multifator de Azure, o usuário deve habilitar notificações por push para seu dispositivo.

A página exibe um código de ativação e uma URL junto com uma imagem de código de barras.  Se o usuário é necessária para usar um PIN quando autenticam, a página também solicitará que ele insira um PIN.  O usuário insere o código de ativação e a URL do aplicativo de autenticação multifator de Azure ou usa o scanner de código de barras para digitalizar a imagem de código de barras e clica no botão Ativar.    

Após concluir a ativação, o usuário clica no botão autenticar agora.  Azure autenticação multifator executará uma autenticação aplicativo móvel do usuário.  O usuário deve inserir seu PIN (se aplicável) e pressione o botão de autenticar em seu aplicativo móvel para prosseguir para a próxima etapa do processo de inscrição automática.  


Se os administradores configurou o servidor de autenticação multifator Azure para coletar segurança perguntas e respostas, o usuário depois será levado para a página de perguntas de segurança.  O usuário deve selecionar quatro perguntas de segurança e fornecem respostas para suas perguntas selecionadas.    

![Perguntas de portal de segurança do usuário](./media/multi-factor-authentication-get-started-portal/secq.png)  

A inscrição automática de usuário agora está concluída e o usuário estiver conectado portal do usuário.  Os usuários podem efetuar novamente o Portal de usuário a qualquer momento no futuro para alterar seus números de telefone pinos, métodos de autenticação e perguntas de segurança caso seja permitido por seus administradores.
