<properties 
    pageTitle="Configurar aplicativos da web no serviço de aplicativo do Azure" 
    description="Como configurar um aplicativo web nos serviços de aplicativo do Azure" 
    services="app-service\web" 
    documentationCenter="" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="configure-web-apps-in-azure-app-service"></a>Configurar aplicativos da web no serviço de aplicativo do Azure #

Este tópico explica como configurar um aplicativo web usando o [Portal do Azure].

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="application-settings"></a>Configurações do aplicativo

1. No [Portal do Azure], abra a lâmina para o aplicativo web.
2. Clique em **todas as configurações**.
3. Clique em **configurações do aplicativo**.

![Configurações do aplicativo][configure01]

A lâmina de **configurações do aplicativo** tem configurações agrupadas em diversas categorias.

### <a name="general-settings"></a>Configurações gerais

**Versões do framework**. Defina essas opções se seu aplicativo usa qualquer uma dessas estruturas: 

- **.NET Framework**: definir a versão do .NET framework. 
- **PHP**: definir o PHP versão ou **desativado** para desabilitar o PHP. 
- **Java**: selecione a versão Java ou **desativado** para desabilitar o Java. Use a opção de **Contêiner da Web** para escolher entre versões Tomcat e Jetty.
- **Python**: selecione a versão de Python ou **desativado** para desabilitar Python.

Por razões técnicas, ativando Java para seu aplicativo desativa as opções de .NET, PHP e Python.

<a name="platform"></a>
**Plataforma**. Seleciona se seu aplicativo web é executado em um ambiente de 32 bits ou 64 bits. Ambiente de 64 bits requer o modo Basic ou padrão. Libere e modos de compartilhado sempre executam em um ambiente de 32 bits.

**Web Sockets**. Definir **ativado** para ativar o protocolo WebSocket; Por exemplo, se seu aplicativo web usa [ASP.NET SignalR] ou [socket.io].

<a name="alwayson"></a>
**Sempre ligado**. Por padrão, aplicativos web serão descarregados se estiverem ociosos durante um determinado período de tempo. Isso permite que o sistema conservar recursos. No modo Basic ou padrão, você pode habilitar **Sempre** manter o aplicativo carregado o tempo todo. Se seu aplicativo executa trabalhos de web contínuo, você deve ativar **Sempre em**ou os trabalhos de web podem não ser executadas confiável.

**Gerenciado Pipeline versão**. Define o [modo de pipeline]do IIS. Deixe este valor definido como integrado (padrão), a menos que você tenha um aplicativo herdado que requer uma versão mais antiga do IIS.

**Troca automática**. Se você ativar a troca automática para um slot de implantação, serviço de aplicativo será automaticamente Troque o web app em produção quando você pressiona uma atualização para esse slot. Para obter mais informações, consulte [implantar para preparação slots web Apps em um serviço de aplicativo do Azure] (web-sites-testado-publishing.md).

### <a name="debugging"></a>Depuração

A **depuração remota**. Habilita a depuração remota. Quando habilitada, você pode usar o depurador remoto no Visual Studio para se conectar diretamente ao seu aplicativo web. Depuração remota permanecerá ativada por 48 horas. 

### <a name="app-settings"></a>Configurações do aplicativo

Esta seção contém pares nome/valor que você web app carregará em Iniciar para cima. 

- Para aplicativos .NET, essas configurações são inseridas em sua configuração de .NET `AppSettings` em tempo de execução, substituindo configurações existentes. 

- Aplicativos PHP, Python, Java e nó podem acessar essas configurações como variáveis de ambiente em tempo de execução. Para cada configuração de aplicativo, duas variáveis de ambiente são criadas; uma com o nome especificado por na entrada de configuração do aplicativo e outra com um prefixo de APPSETTING_. Conter o mesmo valor.

### <a name="connection-strings"></a>Cadeias de caracteres de Conexão

Cadeias de caracteres de Conexão para recursos vinculados. 

Para aplicativos .NET, essas cadeias de caracteres de conexão são inseridas em sua configuração de .NET `connectionStrings` configurações em tempo de execução, substituindo entradas existentes onde a tecla for igual ao nome de banco de dados vinculado. 

Para aplicativos PHP, Python, Java e nó, essas configurações estarão disponíveis como variáveis de ambiente em tempo de execução, o prefixo com o tipo de conexão. Os prefixos de variável de ambiente são da seguinte maneira: 

- SQL Server:`SQLCONNSTR_`
- MySQL:`MYSQLCONNSTR_`
- Banco de dados do SQL:`SQLAZURECONNSTR_`
- Personalizado:`CUSTOMCONNSTR_`

Por exemplo, se uma cadeia de conexão MySql foram nomeada `connectionstring1`, ela poderia ser acessada através da variável de ambiente `MYSQLCONNSTR_connectionString1`.

### <a name="default-documents"></a>Documentos padrão

O documento padrão é a página da web que é exibido na URL para um site raiz.  O primeiro arquivo correspondente na lista é usado. 

Aplicativos Web podem usar módulos que rota com base na URL, ao invés de servir conteúdo estático, nesse caso lá não é nenhum documento padrão como tal.    

### <a name="handler-mappings"></a>Mapeamentos de manipulador

Use esta área para adicionar processadores de script personalizado para lidar com solicitações de extensões de arquivo específico. 

- **Extensão**. A extensão de arquivo a ser tratado como PHP ou handler.fcgi. 
- **Caminho de processador de script**. O caminho absoluto do processador script. Solicitações de arquivos que correspondem a extensão de arquivo serão processadas pelo processador de script. Use o caminho `D:\home\site\wwwroot` para fazer referência ao diretório de raiz do seu aplicativo.
- **Argumentos adicionais**. Argumentos de linha de comando opcionais para o processador de script 


### <a name="virtual-applications-and-directories"></a>Diretórios e aplicativos virtuais 
 
Para configurar diretórios e aplicativos virtuais, especifique cada diretório virtual e o caminho físico correspondente relativo na raiz do site. Opcionalmente, você pode selecionar a caixa de seleção do **aplicativo** para marcar um diretório virtual como um aplicativo.


## <a name="enabling-diagnostic-logs"></a>Ativar logs de diagnóstico

Para ativar logs de diagnóstico:

1. Na lâmina para seu aplicativo web, clique em **todas as configurações**.
2. Clique em **logs de diagnóstico**. 

Opções para gravar logs de diagnóstico de um aplicativo web que ofereça suporte a registro em log: 

- **Log de aplicativo**. Grava logs de aplicativos para o sistema de arquivos. Registro em log dura por um período de 12 horas. 

**Nível**. Quando o log de aplicativo está habilitado, essa opção especifica a quantidade de informações que serão gravados (erro, aviso, informações ou detalhado).

**Log de servidor web**. Logs são salvos no formato de arquivo de log estendido W3C. 

**Mensagens de erro detalhadas**. Mensagens de erro detalhadas salva arquivos. htm. Os arquivos são salvos em /LogFiles/DetailedErrors. 

**Rastreamento de solicitação falha**. Logs de falha solicitações para arquivos XML. Os arquivos são salvos em /LogFiles/W3SVC*xxx*, onde xxx é um identificador exclusivo. Esta pasta contém um arquivo XSL e um ou mais arquivos XML. Verifique se a baixar o arquivo XSL, porque ele fornece funcionalidade para formatação e filtrar o conteúdo dos arquivos XML.

Para exibir os arquivos de log, você deve criar credenciais FTP, da seguinte maneira:

1. Na lâmina para seu aplicativo web, clique em **todas as configurações**.
2. Clique em **credenciais de implantação**.
3. Insira um nome de usuário e senha.
4. Clique em **Salvar**.

![Definir credenciais de implantação][configure03]

O nome de usuário completo do FTP é "app\username" onde o *aplicativo* é o nome de seu aplicativo web. O nome de usuário é listado na lâmina web app, sob **Essentials**.  

![Credenciais de implantação de FTP][configure02]

## <a name="other-configuration-tasks"></a>Outras tarefas de configuração

### <a name="ssl"></a>SSL 

No modo de Basic ou padrão, você pode carregar certificados SSL para um domínio personalizado. Para obter mais informações, consulte [habilitar HTTPS para um aplicativo web]. 

Para exibir seus certificados carregados, clique em **Todas as configurações** > **domínios personalizados e SSL**.

### <a name="domain-names"></a>Nomes de domínio

Adicione nomes de domínio personalizado para seu aplicativo web. Para obter mais informações, consulte [configurar um nome de domínio personalizado para um aplicativo web no serviço de aplicativo do Azure].

Para exibir seus nomes de domínio, clique em **Todas as configurações** > **domínios personalizados e SSL**.

### <a name="deployments"></a>Implantações

- Configure implantação contínua. Consulte [Gito usando para implantar Web Apps em um serviço de aplicativo do Azure](./web-sites-deploy.md).
- Slots de implantação. Consulte [implantar em ambientes de teste para Web Apps em um serviço de aplicativo do Azure].


Para exibir seus slots de implantação, clique em **Todas as configurações** > **slots de implantação**.

### <a name="monitoring"></a>Monitoramento

No modo de Basic ou padrão, você pode testar a disponibilidade de pontos de extremidade HTTP ou HTTPS, de até três locais distribuídos geográfica. Um teste de monitoramento falhará se o código de resposta HTTP é um erro (4xx ou 5xx) ou a resposta demora mais de 30 segundos. Um ponto de extremidade é considerado disponível se os testes de monitoramento bem-sucedida de todos os locais especificados. 

Para obter mais informações, consulte [como: monitorar o status de ponto de extremidade do web].

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo], onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="next-steps"></a>Próximas etapas

- [Configurar um nome de domínio personalizado no serviço de aplicativo do Azure]
- [Habilitar HTTPS para um aplicativo no serviço de aplicativo do Azure]
- [Dimensionar um aplicativo web no serviço de aplicativo do Azure]
- [Noções básicas de monitoramento Web Apps em um serviço de aplicativo do Azure]

<!-- URL List -->

[SignalR do ASP.NET]: http://www.asp.net/signalr
[Portal do Azure]: https://portal.azure.com/
[Configurar um nome de domínio personalizado no serviço de aplicativo do Azure]: ./web-sites-custom-domain-name.md
[Implantar em ambientes de teste para Web Apps em um serviço de aplicativo do Azure]: ./web-sites-staged-publishing.md
[Habilitar HTTPS para um aplicativo no serviço de aplicativo do Azure]: ./web-sites-configure-ssl-certificate.md
[Como: monitorar o status de ponto de extremidade da web]: http://go.microsoft.com/fwLink/?LinkID=279906
[Noções básicas de monitoramento Web Apps em um serviço de aplicativo do Azure]: ./web-sites-monitor.md
[modo de pipeline]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Dimensionar um aplicativo web no serviço de aplicativo do Azure]: ./web-sites-scale.md
[Socket.IO]: ./web-sites-nodejs-chat-app-socketio.md
[Experimente o aplicativo de serviço]: http://go.microsoft.com/fwlink/?LinkId=523751

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
