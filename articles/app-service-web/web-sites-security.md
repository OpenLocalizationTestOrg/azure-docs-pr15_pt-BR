<properties
    pageTitle="Proteger um aplicativo no serviço de aplicativo do Azure"
    description="Aprenda a proteger um aplicativo web, back-end do aplicativo móvel ou aplicativo de API do serviço de aplicativo do Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="01/12/2016"
    ms.author="cephalin"/>


#<a name="secure-an-app-in-azure-app-service"></a>Proteger um aplicativo no serviço de aplicativo do Azure

Este artigo ajuda você a começar a proteger seu aplicativo web, back-end do aplicativo móvel ou aplicativo de API do serviço de aplicativo do Azure. 

Segurança no serviço de aplicativo do Azure tem dois níveis: 

- **Segurança de infraestrutura e plataforma** - você confia Azure para que os serviços que você precisa executar realmente coisas com segurança na nuvem.
- **Segurança de aplicativo** - é necessário criar o próprio aplicativo com segurança. Isso inclui como integrar com o Azure Active Directory, como gerenciar certificados e como garantir que você pode falar com segurança com diferentes serviços. 

#### <a name="infrastructure-and-platform-security"></a>Segurança de infraestrutura e plataforma
Porque o serviço de aplicativo mantém as VMs Azure, armazenamento, conexões de rede, estruturas da web, gerenciamento e recursos de integração e muito mais, ele está ativamente protegido e protegido percorre conformidade rígida e verifica continuamente para garantir que:

- Os aplicativos de serviço de aplicativo são isolados ambas na Internet e de recursos Azure os outros clientes.
- Comunicação de segredos (por exemplo, sequências de conexão) entre o seu aplicativo de serviço de aplicativos e outros recursos Azure (por exemplo, banco de dados SQL) em um grupo de recursos permanece dentro do Azure e não cross qualquer limites de rede. Segredos sempre são criptografados.
- Toda a comunicação entre o seu aplicativo de serviço de aplicativos e recursos externos, como gerenciamento do PowerShell, interface de linha, SDKs do Azure, APIs REST e conexões híbridos, corretamente são criptografados.
- ameaças de 24 horas, gerenciamento de protege os recursos de serviço de aplicativo contra malware, distribuído negação de serviço (DDoS), em-a-interceptação (MITM) e outras ameaças. 

Para obter mais informações sobre a segurança de infraestrutura e plataforma no Azure, consulte [Central de confiabilidade do Azure](/support/trust-center/security/).

#### <a name="application-security"></a>Segurança de aplicativo

Embora o Azure é responsável por protegendo a infraestrutura e a plataforma que seu aplicativo é executado em, é sua responsabilidade proteger o seu próprio aplicativo. Em outras palavras, você precisa desenvolver, implantar e gerenciar seu código do aplicativo e o conteúdo de forma segura. Sem isso, seu código do aplicativo ou o conteúdo ainda pode estar vulnerável a ameaças tais como:

- Inclusão de SQL
- Sequestro de sessão
- Cross-site scripting
- MITM de nível de aplicativo
- DDoS de nível de aplicativo

Uma discussão completa das considerações de segurança para aplicativos baseados na web está além do escopo deste documento. Como ponto de partida para obter mais orientações sobre protegendo seus aplicativos, consulte o [Projeto de segurança de aplicativo de Web aberta (OWASP)](https://www.owasp.org/index.php/Main_Page), especificamente os [10 principais do projeto.](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), que lista as falhas de segurança de aplicativo atual de superior 10 críticos da web, conforme determinado por membros OWASP.

## <a name="perform-penetration-testing-on-your-app"></a>Executar testes em seu aplicativo de penetração

Uma das maneiras mais fáceis de ainda iniciado com testes de vulnerabilidades em seu aplicativo de serviço de aplicativo é usar a [integração com segurança Tinfoil](/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) para realizar a verificação em seu aplicativo de vulnerabilidades de um único clique. Você pode exibir os resultados de teste em um relatório fácil de entender e saiba como corrigir cada vulnerabilidade com instruções passo a passo.

Se você prefere executar seus próprios testes de penetração ou deseja usar outro provedor ou pacote de scanner, você deve seguir o [processo de aprovação de testes de penetração Azure](https://security-forms.azure.com/penetration-testing/terms) e obter aprovação prévia para realizar os testes de penetração desejado.

##<a name="https"></a>Comunicação segura com clientes

Se você usar o ** \*. azurewebsites.net** nome de domínio criado para seu aplicativo de serviço de aplicativo, você poderá usar imediatamente HTTPS, pois um certificado SSL é fornecido para todas as ** \*. azurewebsites.net** nomes de domínio. Se seu site usa um [nome de domínio personalizado](web-sites-custom-domain-name.md), você pode carregar um certificado SSL para [Permitir o HTTPS](web-sites-configure-ssl-certificate.md) para o domínio personalizado.

Habilitar [HTTPS](https://en.wikipedia.org/wiki/HTTPS) pode ajudar a proteger contra ataques MITM na comunicação entre seu aplicativo e seus usuários.

## <a name="secure-data-tier"></a>Nível de dados seguros

Serviço de aplicativo altamente se integra ao banco de dados SQL, tal que todas as cadeias de caracteres de conexão são criptografadas em todos os segmentos e só são descriptografar na VM que o aplicativo é executado em *e* somente quando o aplicativo é executado. Além disso, o Azure SQL Database inclui muitos recursos de segurança para ajudá-lo a proteger os dados de aplicativo contra ameaças, incluindo [criptografia inativos](https://msdn.microsoft.com/library/dn948096.aspx), [Sempre criptografadas](https://msdn.microsoft.com/library/mt163865.aspx), [Masking dinâmico de dados](../sql-database/sql-database-dynamic-data-masking-get-started.md)e a [Detecção de ameaças](../sql-database/sql-database-threat-detection-get-started.md). Se você tiver dados confidenciais ou requisitos de conformidade, consulte [proteger seu banco de dados SQL](../sql-database/sql-database-security.md) para obter mais informações sobre como proteger seus dados.

Se você usar um provedor de banco de dados de terceiros, como ClearDB, você deve consultar a documentação do provedor diretamente em práticas recomendadas de segurança.  

##<a name="develop"></a>Implantação e desenvolvimento seguro

### <a name="publishing-profiles-and-publish-settings"></a>Configurações de publicação e perfis de publicação

Ao desenvolver aplicativos, executar tarefas de gerenciamento, ou automatizar tarefas usando utilitários como o **Visual Studio**, **Web Matrix**, **Azure PowerShell** ou a **Interface de linha do Azure (Azure comando)**, você pode usar um arquivo de *configurações de publicação* ou um *perfil de publicação*. Os dois tipos de arquivo autenticam com o Azure e devem ser protegidos para evitar acesso não autorizado.

* Contém um arquivo de **configurações de publicação**

    * Sua ID do Azure assinatura

    * Um certificado de gerenciamento que permite executar tarefas de gerenciamento de sua assinatura *sem precisar fornecer um nome de conta ou senha*.

* Contém um arquivo de **perfil de publicação**

    * Informações da publicação para seu aplicativo

Se você usar um utilitário que usa um arquivo de configurações de publicação ou publicar perfil, importe o arquivo que contém as configurações de publicação ou perfil no utilitário e, em seguida, **Excluir** o arquivo. Se você deve manter o arquivo, compartilhar com outras pessoas trabalhando no projeto, por exemplo, armazene-o em um local seguro, como um diretório *criptografada* com permissões restritas.

Além disso, você deverá verificar se que as credenciais importadas são protegidas. Por exemplo, o **Azure PowerShell** e a **Interface de linha do Azure (Azure comando)** armazenam informações importadas no seu **diretório inicial** (*~* em sistemas Linux ou OS X e */users/yourusername* em sistemas Windows.) Para segurança extra, você talvez queira **criptografar** esses locais usando as ferramentas de criptografia disponíveis para seu sistema operacional.

### <a name="configuration-settings-and-connection-strings"></a>Definições de configuração e cadeias de caracteres de conexão
É prática comum para armazenar cadeias de caracteres de conexão, credenciais de autenticação e outras informações confidenciais em arquivos de configuração. Infelizmente, esses arquivos podem ser expostos em seu site ou marcados em um repositório público, expor essas informações. Por exemplo, uma pesquisa simples em [GitHub](https://github.com), pode descobrir inúmeros arquivos de configuração com segredos expostos nos repositórios públicos.

A prática recomendada é evitar essas informações ter arquivos de configuração do seu aplicativo. Serviço de aplicativo permite armazenar informações de configuração como parte do ambiente de tempo de execução como **configurações de aplicativo** e **cadeias de caracteres de conexão**. Os valores são expostos para o seu aplicativo no tempo de execução por meio de *variáveis de ambiente* para a maioria dos idiomas de programação. Para aplicativos .NET, esses valores são inseridos em sua configuração de .NET no tempo de execução. Além dessas situações, essas configurações permanecerão criptografadas, a menos que você exibir ou configurá-los usando o [Portal do Azure](https://portal.azure.com) ou utilitários como PowerShell ou CLI Azure. 

Armazenar informações de configuração no serviço de aplicativo possibilita administrador do aplicativo bloquear informações confidenciais para os aplicativos de produção. Os desenvolvedores podem usar um conjunto separado de configurações para desenvolvimento de aplicativos e as configurações podem ser substituídas automaticamente pelas configurações definidas no aplicativo de serviço. Nem mesmo os desenvolvedores precisam saber as senhas configuradas para o aplicativo de produção. Para obter mais informações sobre como configurar as configurações de aplicativo e cadeias de caracteres de conexão no serviço de aplicativo, consulte [Configurando web apps](web-sites-configure.md).

### <a name="ftps"></a>FTPS

Serviço de aplicativo do Azure fornece acesso seguro de FTP ao sistema de arquivos para o aplicativo por meio de **FTPS**. Isso permite que você acesse com segurança o código do aplicativo do aplicativo web, bem como diagnósticos logs. É recomendável que você sempre use FTPS em vez de FTP. 

O link FTPS para seu aplicativo pode ser encontrado com as seguintes etapas:

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Selecione **Procurar tudo**.
3. Da lâmina **Procurar** , selecione **Serviços de aplicativo**.
4. Selecione a lâmina de **Serviços de aplicativo** , o aplicativo desejado.
5. Da lâmina do aplicativo, selecione **todas as configurações**.
6. Da lâmina **configurações** , selecione **Propriedades**.
7. Os links FTP e FTPS são fornecidos na lâmina **configurações** . 

Para obter mais informações sobre FTPS, consulte [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a segurança da plataforma Windows Azure, informações sobre um **incidente de segurança ou abuse**de relatório, ou para informar Microsoft que você executarão **testes de penetração** do seu site, consulte a seção segurança da [Central de confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

Para obter mais informações sobre arquivos **Web. config** ou **applicationHost** nos aplicativos de serviço de aplicativo, consulte [Opções de configuração desbloqueados nos aplicativos web do serviço de aplicativo do Azure](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Para obter informações sobre informações de log para os aplicativos de serviço de aplicativo, que pode ser útil para detectar ataques, consulte [Habilitar o log de diagnóstico](web-sites-enable-diagnostic-log.md).

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="whats-changed"></a>O que mudou

* Para um guia para a alteração de sites para o serviço de aplicativo consulte: [o serviço de aplicativo do Azure e seu impacto sobre serviços existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
