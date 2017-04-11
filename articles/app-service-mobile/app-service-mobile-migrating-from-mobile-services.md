<properties
    pageTitle="Migrar de serviços móveis para um aplicativo de serviço de aplicativo móvel"
    description="Saiba como migrar facilmente seu aplicativo de serviços de celular para um aplicativo do aplicativo de serviço móvel"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="adrianha"/>

# <a name="article-top"></a>Migrar seu serviço de celular existente do Azure para o serviço de aplicativo do Azure

Com a [disponibilidade geral do serviço de aplicativo do Azure], sites de serviços de celular do Azure podem ser facilmente migrados in-loco para tirar proveito de todos os recursos do serviço de aplicativo do Azure.  Este documento explica o que esperar quando migrar seu site do Azure Mobile Services para o serviço de aplicativo do Azure.

## <a name="what-does-migration-do"></a>O que migração faz ao seu site

Migração de seu serviço móvel do Azure transforma o serviço Mobile em um aplicativo de [Serviço de aplicativo do Azure] sem afetar o código.  Seu Hubs de notificação, conexão de dados SQL, configurações de autenticação, trabalhos agendados e nome de domínio permanecem inalterados.  Os clientes móveis usando seu serviço móvel do Azure continuam operando normalmente.  Migração reinicia seu serviço quando ele é transferido para o serviço de aplicativo do Azure.

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Por que você deve migrar seu site

Microsoft recomenda migrar seu serviço móvel do Azure para tirar proveito dos recursos do Azure serviço de aplicativo, incluindo:

  *  Novos recursos de host, incluindo [WebJobs] e [nomes de domínio personalizado].
  *  Conectividade com seus recursos no local usando [VNet] , além de [Conexões de híbrido].
  *  Monitoramento e solução de problemas com o novo Relíquia ou [Ideias de aplicativo].
  *  Ferramentas do DevOps internas, incluindo [slots de teste], reversão e em produção testes.
  *  [Dimensionar automaticamente], balanceamento de carga e [monitoramento do desempenho].

Para obter mais informações sobre as vantagens do serviço de aplicativo do Azure, consulte o tópico [Mobile versus aplicativo serviços] .

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar qualquer trabalho principal no seu site, você deve [fazer backup de seu serviço móvel] scripts e banco de dados SQL.

## <a name="migrating-site"></a>Migrando seus sites

O processo de migração migra todos os sites dentro de uma única região do Azure.

Para migrar seu site:

  1.  Faça logon [Portal clássico Azure].
  2.  Selecione um serviço de celular na região que você deseja migrar.
  3.  Clique no botão de **migrar para o serviço de aplicativo** .

    ![O botão migrar][0]

  4.  Leia a migrar a caixa de diálogo do serviço de aplicativo.
  5.  Insira o nome do seu serviço móvel na caixa fornecida.  Por exemplo, se seu nome de domínio for contoso.azure-mobile.net, insira _contoso_ na caixa fornecida.
  6.  Clique no botão de escala.

Monitore o status da migração no monitor de atividades. Seu site está listado como *migrar* no Portal de clássico do Azure.

  ![Monitor de atividades de migração][1]

Cada migração pode levar de 3 a 15 minutos por serviço móvel sendo migrado.  Seu site permanece disponível durante a migração.
Seu site é reiniciado no final do processo de migração.  O site não está disponível durante o processo de reinicialização, que pode durar alguns segundos.

## <a name="finalizing-migration"></a>Finalizando a migração

Planeje testar seu site de um cliente móvel após a conclusão do processo de migração.  Certifique-se de que você pode executar todas as ações comuns do cliente sem alterações no cliente móvel.  

### <a name="update-app-service-tier"></a>Selecione um serviço de aplicativo apropriado nível de preço

Você tem mais flexibilidade nos preços após você migrar para o serviço de aplicativo do Azure.

  1.  Faça logon no [portal do Azure].
  2.  Selecione **todos os recursos** ou **Os serviços de aplicativo** e clique no nome do seu serviço móvel migradas.
  3.  A lâmina configurações abre por padrão.
  4.  No menu Configurações, clique em **Plano de serviço de aplicativo** .
  5.  Clique no bloco de **Nível de preços** .
  6.  Clique no bloco apropriado aos seus requisitos, clique em **Selecionar**.  Talvez você precise clicar camadas de **Exibir tudo** para ver os preços disponíveis.

Como ponto de partida, recomendamos as seguintes camadas:

| Nível de preço de serviço móvel | Nível de preços de serviço de aplicativo |
| :-------------------------- | :----------------------- |
| Livre                        | F1 livre                  |
| Básico                       | B1 Basic                 |
| Padrão                    | S1 padrão              |

Não há considerável flexibilidade na escolha direita preços nível do aplicativo.  Consulte [Preços de serviço de aplicativo] para obter detalhes completos sobre o preço do seu novo serviço de aplicativo.

> [AZURE.TIP]O nível padrão de serviço do aplicativo contém acesso a vários recursos que talvez você queira usar, inclusive [slots de teste], backups automáticos e a escala automática.  Confira os novos recursos enquanto você estiver lá!

### <a name="review-migration-scheduler-jobs"></a>Examine os trabalhos do Agendador migrada

Trabalhos do Agendador não ficará visíveis até aproximadamente 30 minutos após a migração.  Tarefas agendadas continuam a executar em segundo plano.
Para exibir os trabalhos agendados depois que elas fiquem visíveis novamente:

  1.  Faça logon no [portal do Azure].
  2.  Selecione **Procurar >**, insira o **cronograma** na caixa _filtro_ e selecione **Conjuntos de Agendador**.

Há um número limitado de Agendador gratuito trabalhos disponíveis após a migração.  Examine os [Planos de Agendador do Azure]e seu uso.

### <a name="configure-cors"></a>Configurar CORS se necessário

Compartilhamento de recursos entre origens é uma técnica para permitir que um site acessar um API da Web em um domínio diferente.  Se você estiver usando os serviços do Azure Mobile com um site associado, você precisa configurar CORS como parte da migração.  Se você estiver acessando o Azure Mobile Services exclusivamente a partir de dispositivos móveis, em seguida, CORS não precisa estar configurado, exceto em casos raros.

Suas configurações de CORS migradas estão disponíveis como a configuração de aplicativo **MS_CrossDomainWhitelist** .  Para migrar seu site para o recurso de CORS de serviço de aplicativo:

  1.  Faça logon no [portal do Azure].
  2.  Selecione **todos os recursos** ou **Os serviços de aplicativo** e clique no nome do seu serviço móvel migradas.
  3.  A lâmina configurações abre por padrão.
  4.  Clique em **CORS** no menu API.
  5.  Insira qualquer origens permitidos na caixa fornecida, pressionando Enter após cada um deles.
  6.  Depois que sua lista de origens de permitidos estiver correta, clique no botão Salvar.

> [AZURE.TIP]Uma das vantagens de usar um serviço de aplicativo do Azure é que você pode executar seu site e o serviço móvel no mesmo site.  Para obter mais informações, consulte a seção [próximas etapas](#next-steps) .

### <a name="download-publish-profile"></a>Baixar um novo perfil de publicação

O perfil de publicação do seu site é alterado ao migrar para o serviço de aplicativo do Azure.  Se você pretende publicar o seu site de dentro do Visual Studio, é necessário um novo perfil de publicação.  Para baixar o novo perfil de publicação:

  1.  Faça logon no [portal do Azure].
  2.  Selecione **todos os recursos** ou **Os serviços de aplicativo** e clique no nome do seu serviço móvel migradas.
  3.  Clique em **obter publicar perfil**.

O arquivo PublishSettings é baixado para seu computador.  Normalmente é chamado de _nome do site_. PublishSettings.  Importe configurações de publicação para seu projeto existente:

  1.  Abra o Visual Studio e seu projeto do serviço móvel do Azure.
  2.  Clique com botão direito seu projeto no **Solution Explorer** e selecione **Publicar...**
  3.  Clique em **Importar**
  4.  Clique em **Procurar** e selecione seu baixado Publicar arquivo de configurações.  Clique em **Okey**
  5.  Clique em **Validar Conexão** para garantir que o trabalho de configurações de publicação.
  6.  Clique em **Publicar** para publicar seu site.


## <a name="working-with-your-site"></a>Trabalhando com seu pós-migração de site

Começa a trabalhar com seu novo serviço de aplicativo na pós-migração [portal do Azure] .  A seguir estão algumas observações em operações específicas que você usou para executar no [Portal de clássico do Azure], juntamente com seus equivalentes de serviço de aplicativo.

### <a name="publishing-your-site"></a>Download e a publicação de seu site migrado

Seu site está disponível via gito ou ftp e pode ser publicado novamente com vários mecanismos diferentes, incluindo WebDeploy, TFS, Mercurial, GitHub e FTP.  As credenciais de implantação são migradas com o restante do seu site.  Se você não definir suas credenciais de implantação ou se você não se lembrá-las, você poderá redefini-las:

  1. Faça logon no [portal do Azure].
  2. Selecione **todos os recursos** ou **Os serviços de aplicativo** e clique no nome do seu serviço móvel migradas.
  3. A lâmina configurações abre por padrão.
  4. Clique em **credenciais de implantação** na publicação menu.
  5. Digite as novas credenciais de implantação nas caixas fornecidas, clique no botão Salvar.

Você pode usar essas credenciais para clonar o site com gito ou configurar implantações automatizadas GitHub, TFS ou Mercurial.  Para obter mais informações, consulte a [documentação de implantação do serviço de aplicativo do Azure].

### <a name="appsettings"></a>Configurações do aplicativo

A maioria das configurações de um serviço móvel migrado estão disponíveis por meio de configurações do aplicativo.  Você pode obter uma lista das configurações de aplicativo a partir do [portal do Azure].
Para exibir ou alterar suas configurações de aplicativo:

  1. Faça logon no [portal do Azure].
  2. Selecione **todos os recursos** ou **Os serviços de aplicativo** e clique no nome do seu serviço móvel migradas.
  3. A lâmina configurações abre por padrão.
  4. Clique em **configurações do aplicativo** no menu geral.
  5. Role até a seção configurações de aplicativo e localize a configuração do aplicativo.
  6. Clique no valor da configuração do aplicativo para editar o valor.  Clique em **Salvar** para salvar o valor.

Você pode atualizar várias configurações de aplicativo ao mesmo tempo.

> [AZURE.TIP]Há duas configurações de aplicativo com o mesmo valor.  Por exemplo, você poderá ver _ApplicationKey_ e _MS\_ApplicationKey_.  Atualize ambas as configurações de aplicativo ao mesmo tempo.

### <a name="authentication"></a>Autenticação

Todas as configurações de autenticação estão disponíveis como configurações do aplicativo em seu site migrado.  Para atualizar suas configurações de autenticação, você deve alterar as configurações de aplicativo apropriado.  A tabela a seguir mostra as configurações de aplicativo apropriadas para seu provedor de autenticação:

| Provedor          | ID do cliente                 | Secreta do cliente                | Outras configurações             |
| :---------------- | :------------------------ | :--------------------------- | :------------------------- |
| Conta da Microsoft | **MS\_MicrosoftClientID**  | **MS\_MicrosoftClientSecret** | **MS\_MicrosoftPackageSID** |
| Facebook          | **MS\_FacebookAppID**      | **MS\_FacebookAppSecret**     |                            |
| Twitter           | **MS\_TwitterConsumerKey** | **MS\_TwitterConsumerSecret** |                            |
| Google            | **MS\_GoogleClientID**     | **MS\_GoogleClientSecret**    |                            |
| Azure AD          | **MS\_AadClientID**        |                              | **MS\_AadTenants**          |

Observação: **MS\_AadTenants** armazenado como uma lista separada por vírgulas de domínios de locatário (os campos "Locatários permitido" no portal de serviços de celular).

> [AZURE.WARNING] **Não use os mecanismos de autenticação no menu Configurações**
>
> Serviço de aplicativo do Azure fornece um separada "sem código" autenticação e a autorização sistema sob o _autenticação / autorização_ menu Configurações e a opção de _Autenticação do celular_ (substituída) no menu Configurações.  Essas opções são incompatíveis com um serviço de celular Azure migradas.  Você pode [atualizar seu site](app-service-mobile-net-upgrading-from-mobile-services.md) para tirar proveito da autenticação do serviço de aplicativo do Azure.

### <a name="easytables"></a>Dados

Na guia _dados_ nos serviços do Mobile foi substituída por _Tabelas fácil_ dentro do portal Azure.  Para acessar tabelas fácil:

  1. Faça logon no [portal do Azure].
  2. Selecione **todos os recursos** ou **Os serviços de aplicativo** e clique no nome do seu serviço móvel migradas.
  3. A lâmina configurações abre por padrão.
  4. Clique em **tabelas fácil** no menu móvel.

Você pode adicionar uma tabela clicando no botão **Adicionar** ou acessar suas tabelas existentes clicando em um nome de tabela.  Há várias operações que você pode fazer a partir este blade, incluindo:

* Alterando as permissões de tabela
* Editando scripts operacionais
* Gerenciando o esquema de tabela
* Excluir a tabela
* Limpar os conteúdos da tabela
* Excluir linhas específicas da tabela

### <a name="easyapis"></a>API

Na guia da _API_ nos serviços do Mobile foi substituída por _APIs fácil_ dentro do portal Azure.  Para acessar APIs fácil:

  1. Faça logon no [portal do Azure].
  2. Selecione **todos os recursos** ou **Os serviços de aplicativo** e clique no nome do seu serviço móvel migradas.
  3. A lâmina configurações abre por padrão.
  4. Clique em **APIs fácil** no menu móvel.

Suas APIs migrados já estão listados na lâmina.  Você também pode adicionar uma API dessa lâmina.  Para gerenciar uma API específica, clique em API.
Da lâmina do novo, você pode ajustar as permissões e editar os scripts para a API.

### <a name="on-demand-jobs"></a>Trabalhos do Agendador

Todos os trabalhos do Agendador estão disponíveis por meio da seção de conjuntos de trabalho do Agendador.  Para acessar seus trabalhos do Agendador:

  1. Faça logon no [portal do Azure].
  2. Selecione **Procurar >**, insira o **cronograma** na caixa _filtro_ e selecione **Conjuntos de Agendador**.
  3. Selecione o conjunto de trabalho de seu site.  Ele é chamado _NomeDoSite_-trabalhos.
  4. Clique em **configurações**.
  5. Clique em **trabalhos do Agendador** em gerenciar.

Tarefas agendadas são listadas com a frequência especificada antes da migração.  Sob demanda trabalhos estão desabilitados.  Para executar um trabalho sob demanda:

  1. Selecione o trabalho que você deseja executar.
  2. Se necessário, clique em **Ativar** para habilitar o trabalho.
  3. Clique em **configurações**, em seguida, **cronograma**.
  4. Selecione uma recorrência de **uma vez**, clique em **Salvar**

Seus trabalhos sob demanda estão localizados em `App_Data/config/scripts/scheduler post-migration`.  Recomendamos que você converta todos os trabalhos de sob demanda para [WebJobs] ou [funções].  Grave nova trabalhos do Agendador como [WebJobs] ou [funções].

### <a name="notification-hubs"></a>Hubs de notificação

Serviços de celular usa Hubs de notificação para notificações por push.  As seguintes configurações de aplicativo são usadas para vincular o Hub de notificação para o serviço Mobile após a migração:

| Configuração do aplicativo                    | Descrição                              |
| :------------------------------------- | :--------------------------------------- |
| **MS\_PushEntityNamespace**             | O Namespace do Hub de notificação           |
| **MS\_NotificationHubName**             | O nome do Hub de notificação                |
| **MS\_NotificationHubConnectionString** | A cadeia de Conexão do Hub de notificação   |
| **MS\_NamespaceName**                   | Um alias para MS_PushEntityNamespace      |

Seu Hub de notificação é gerenciado por meio do [portal do Azure].  Observe o nome do Hub de notificação (você pode encontrar isso usando as configurações de aplicativo):

  1. Faça logon no [portal do Azure].
  2. Selecione **Procurar**>, selecione **Hubs de notificação**
  3. Clique no nome do Hub de notificação associado ao serviço móvel.

> [AZURE.NOTE]Se o seu HUb de notificação é um tipo de "Mista", ele não está visível.  "Misto" digite hubs utilizam recursos Hubs de notificação e herdados barramento de serviço de notificação.  [Converter seu namespaces misto] antes de continuar.  Quando a conversão estiver concluída, o seu hub de notificação aparece no [portal do Azure].

Para obter mais informações, leia a documentação de [Hubs de notificação] .

> [AZURE.TIP]Recursos de gerenciamento de Hubs de notificação no [portal do Azure] ainda estão em visualização.  O [Portal de clássico do Azure] permanecerão disponíveis para o gerenciamento de todos os seus Hubs de notificação.

### <a name="legacy-push"></a>Configurações de envio herdado

Se você configurou Push seu serviço móvel antes da introdução em Hubs de notificação, você está usando _push herdado_.  Se você estiver usando o envio e você não vir um Hub de notificação listado em sua configuração, é provável que você estiver usando o _envio herdado_.  Esse recurso é migrado com todos os outros recursos.  No entanto, recomendamos que você atualize para Hubs de notificação logo após a migração é concluída.

Enquanto isso, todas as configurações de envio herdados (com a exceção notável do certificado APNS) estão disponíveis em configurações do aplicativo.  Atualize o certificado APNS, substituindo o arquivo apropriado no sistema de arquivos.

### <a name="app-settings"></a>Outras configurações de aplicativo

As seguintes configurações de aplicativo adicionais são migrados do seu serviço de celular e estão disponíveis em *configurações* > *Configurações do aplicativo*:

| Configuração do aplicativo              | Descrição                             |
| :------------------------------- | :-------------------------------------- |
| **MS\_MobileServiceName**         | O nome do aplicativo                    |
| **MS\_MobileServiceDomainSuffix** | O prefixo de domínio. ou seja mobile.net do Azure |
| **MS\_ApplicationKey**            | Sua chave de aplicativo                    |
| **MS\_MasterKey**                 | Sua chave de mestre do aplicativo                     |

A tecla de aplicativo e chave mestre são idênticos as teclas do aplicativo do seu serviço móvel original.  Em particular, a tecla de aplicativo é enviada pelo clientes móveis para validar o uso da API do celular.

### <a name="cliequivalents"></a>Equivalentes de linha de comando

Mais você pode usar o comando _azure móvel_ para gerenciar seu site de serviços de celular do Azure.  Em vez disso, muitas funções foram substituídas com o comando _site azure_ .  Use a tabela a seguir para localizar equivalentes de comandos comuns:

| _Mobile Azure_ Comando                     | Comando equivalente do _Site do Azure_            |
| :----------------------------------------- | :----------------------------------------- |
| locais móveis                           | lista de locais de sites                         |
| lista móvel                                | lista de sites                                  |
| Mostrar móvel _nome_                         | Mostrar _nome_ do site                           |
| reiniciar móvel _nome_                      | site reiniciar _nome_                        |
| Reimplantação móvel _nome_                     | Reimplantação _commitId_ _nome_ do site implantação |
| chave móvel definir _nome_ de _tipo de_ _valor_       | Excluir _chave_ _nome_ do site appsetting <br/> site appsetting adicionar _chave_=de_valor de_ _nome_ |
| _nome_ da lista de configuração móvel                  | de lista do site appsetting _nome_                |
| configuração móvel obter _nome_ _chave_             | site appsetting Mostrar _chave_ _nome_          |
| configuração móvel define _nome_ de _chave_             | Excluir _chave_ _nome_ do site appsetting <br/> site appsetting adicionar _chave_=de_valor de_ _nome_ |
| _nome_ da lista de domínio móvel                  | lista de domínio site _nome_                    |
| domínio móvel adicionar de _nome de_ _domínio_          | domínio de site adicionar _nome_ de _domínio_            |
| domínio móvel excluir _nome_                | Excluir de domínio do site de _domínio_ _nome_         |
| escala móvel Mostrar _nome_                   | Mostrar _nome_ do site                           |
| alterar _nome_ de escala móvel                 | site escala modo _modo_ _nome_ <br /> instâncias _instâncias_ _nome_ do site escala |
| _nome_ da lista appsetting móvel              | de lista do site appsetting _nome_                |
| appsetting móvel adicionar _nome_ _chave_ _valor_ | site appsetting adicionar _chave_=de_valor de_ _nome_   |
| _nome_ da delete appsetting móvel _chave_      | Excluir _chave_ _nome_ do site appsetting        |
| appsetting móvel Mostrar _nome_ _chave_        | Excluir _chave_ _nome_ do site appsetting        |

Atualize a autenticação ou as configurações de notificação de envio atualizando a configuração do aplicativo apropriado.
Editar arquivos e publicar seu site via ftp ou gito.

### <a name="diagnostics"></a>Diagnóstico e log

Log de diagnóstico normalmente está desabilitado em um serviço de aplicativo do Azure.  Para habilitar o log de diagnóstico:

  1. Faça logon no [portal do Azure].
  2. Selecione **todos os recursos** ou **Os serviços de aplicativo** e clique no nome do seu serviço móvel migradas.
  3. A lâmina configurações abre por padrão.
  4. Selecione **Os Logs de diagnóstico** no menu de recursos.
  5. Clique **em** para os seguintes logs: **Aplicativo log (sistema de arquivos)**, **mensagens de erro detalhadas**e **rastreamento de solicitação falha**
  6. Clique em **Sistema de arquivos** de log do servidor Web
  7. Clique em **Salvar**

Para exibir os logs:

  1. Faça logon no [portal do Azure].
  2. Selecione **todos os recursos** ou **Os serviços de aplicativo** e clique no nome do seu serviço móvel migradas.
  3. Clique no botão **Ferramentas**
  4. Selecione o **fluxo de Log** no menu OBSERVE.

Logs são exibidos na janela como eles são gerados.  Você também pode baixar os logs para análise posterior usando suas credenciais de implantação. Para obter mais informações, consulte a documentação de [log] .

## <a name="known-issues"></a>Problemas conhecidos

### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Excluir um clonar de aplicativo móvel migrados faz com que uma interrupção de site

Se você clonar seu serviço móvel migrado usando o PowerShell do Azure e exclua a clonar, a entrada DNS para o serviço de produção será removida.  Seu site é não estar mais acessíveis a partir da Internet.  

Resolução: Se você quiser clonar seu site, fazê-lo através do portal.

### <a name="changing-webconfig-does-not-work"></a>Alterar o Web. config não funciona

Se você tiver um site ASP.NET, mude para o `Web.config` arquivo não são aplicadas.  O serviço de aplicativo do Azure cria um adequado `Web.config` arquivo durante a inicialização para o tempo de execução do Mobile serviços de suporte.  Você pode substituir certas configurações (como cabeçalhos personalizados) usando um arquivo de transformação XML.  Criar um arquivo em chamado `applicationHost.xdt` -esse arquivo deve terminar na `D:\home\site` diretório o serviço do Azure.  Carregar o `applicationHost.xdt` arquivo através de um script de implantação personalizada ou diretamente usando Kudu.  A seguir mostra um exemplo de documento:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Para obter mais informações, consulte a documentação de [Amostras de transformar XDT] no GitHub.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Migrados Mobile Services não pode ser adicionada ao Gerenciador de tráfego

Quando você cria um perfil do Gerenciador de tráfego, você não pode escolher diretamente um serviço móvel migradas para o perfil.  Use uma "empresa externa".  O ponto de extremidade externo só pode ser adicionado através do PowerShell.  Para obter mais informações, consulte o [tutorial do Gerenciador de tráfego](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Próximas etapas

Agora que seu aplicativo é migrado para o serviço de aplicativo, há ainda mais recursos que você pode usar:

  * Implantação de [teste slots] permitem que você alterações ao seu site do estágio e executar A / B teste.
  * [WebJobs] fornecem uma substituição para trabalhos agendado sob demanda.
  * Você pode [implantar continuamente] seu site vinculando seu site GitHub, TFS ou Mercurial.
  * Você pode usar o [Aplicativo ideias] para monitorar o seu site.
  * Servir um site e uma API de celular do mesmo código.

### <a name="upgrading-your-site"></a>Atualizar seu site de serviços de dispositivo móvel para o SDK de aplicativos do Azure Mobile

  * Para projetos de servidor baseado em Node, o novo [Mobile aplicativos Node SDK] oferece vários recursos novos. Por exemplo, você pode agora fazer desenvolvimento local e depuração, usar qualquer versão Node acima 0,10 e personalizar com qualquer middleware Express.js.

  * Para. Projetos de servidor baseado em líquido, os novos [pacotes do Mobile aplicativos SDK NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) ter mais flexibilidade em dependências de NuGet.  Esses pacotes a nova autenticação de aplicativo de serviço de suporte e redigir com qualquer projeto ASP.NET. Para saber mais sobre como atualizar, consulte [atualizar seu serviço de celular .NET existentes ao aplicativo de serviço](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Preço de serviços de aplicativo]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Obtenção de informações de aplicativo]: ../application-insights/app-insights-overview.md
[Dimensionar automaticamente]: ../app-service-web/web-sites-scale.md
[Serviço de aplicativo do Azure]: ../app-service/app-service-value-prop-what-is.md
[Azure documentação de implantação do serviço de aplicativo]: ../app-service-web/web-sites-deploy.md
[Azure Portal clássico]: https://manage.windowsazure.com
[Portal do Azure]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/en-us/regions/
[Planos do Agendador do Azure]: ../scheduler/scheduler-plans-billing.md
[implantar continuamente]: ../app-service-web/app-service-continuous-deployment.md
[Converter seu namespaces misto]: https://azure.microsoft.com/en-us/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[nomes de domínio personalizado]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[disponibilidade geral do serviço de aplicativo do Azure]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Conexões de híbrido]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[Registro em log]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Aplicativos móveis Node SDK]: https://github.com/azure/azure-mobile-apps-node
[Serviços versus aplicativo de serviço móvel]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Hubs de notificação]: ../notification-hubs/notification-hubs-push-notification-overview.md
[monitoramento do desempenho]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[Fazer backup de seu serviço de celular]: ../mobile-services/mobile-services-disaster-recovery.md
[preparação slots]: ../app-service-web/web-sites-staged-publishing.md
[VNet]: ../app-service-web/web-sites-integrate-with-vnet.md
[WebJobs]: ../app-service-web/websites-webjobs-resources.md
[Exemplos de transformação XDT]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[Funções]: ../azure-functions/functions-overview.md
