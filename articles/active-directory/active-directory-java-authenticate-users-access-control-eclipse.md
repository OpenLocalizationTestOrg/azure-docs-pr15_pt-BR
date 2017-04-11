<properties
    pageTitle="Como usar o controle de acesso (Java) | Microsoft Azure"
    description="Aprenda a desenvolver e usar o controle de acesso com Java no Azure."
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>Como autenticar usuários da Web com o serviço de controle de acesso Azure usando Eclipse

Este guia mostrará como usar o serviço de controle de acesso (ACS) da Azure dentro do Kit de ferramentas do Azure para Eclipse. Para obter mais informações sobre ACS, consulte a seção [próximas etapas](#next_steps) .

> [AZURE.NOTE]
> O filtro de controle de serviços do Azure acesso é uma visualização de tecnologia da comunidade. Como software de pré-lançamento, ele não é anteriormente suportado pela Microsoft.

## <a name="what-is-acs"></a>O que é ACS?

A maioria dos desenvolvedores não são especialistas de identidade e geralmente não deseja gastar mecanismos de autenticação e autorização de desenvolvimento de tempo para seus aplicativos e serviços. ACS é um serviço Azure que oferece uma maneira fácil de autenticar os usuários que precisam acessar seus aplicativos da web e serviços sem precisar lógica de autenticação complexos de fator em seu código.

Os seguintes recursos estão disponíveis no ACS:

-   Integração com o Windows identidade Foundation (WIF).
-   Suporte para provedores de identidade populares da web (IPs) incluindo Windows Live ID, Google, Yahoo! e Facebook.
-   Suporte para serviços de Federação do Active Directory (AD FS) 2.0.
-   Um Open Data Protocol (OData)-com base em serviço de gerenciamento que fornece acesso através de programação às configurações do ACS.
-   Um Portal de gerenciamento que permita acesso administrativo para as configurações do ACS.

Para obter mais informações sobre o ACS, consulte [2.0 de serviço de controle de acesso][].

## <a name="concepts"></a>Conceitos

Azure ACS baseia-se nos princípios de identidade baseada em declarações - uma abordagem consistente para a criação de mecanismos de autenticação para aplicativos executados local ou na nuvem. Identidade baseada em declarações fornece uma maneira comum para aplicativos e serviços adquirir as informações de identidade necessárias sobre usuários dentro de sua organização, em outras organizações e na Internet.

Para concluir as tarefas deste guia, você deve entender os seguintes conceitos:

**Cliente** - no contexto deste guia de instruções, este é um navegador que está tentando obter acesso ao seu aplicativo web.

**Aplicativo de terceiros (RP) de Relying** - aplicativo RP um é um site ou serviço que outsources autenticação para uma autoridade externa. No jargão de identidade, podemos dizer que o RP confia autoridade. Este guia explica como configurar seu aplicativo para confiança ACS.

**Token** - um token é uma coleção de dados de segurança geralmente são emitidas durante a autenticação bem-sucedida de um usuário. Ele contém um conjunto de *declarações*, atributos do usuário autenticado. Uma declaração pode representar um nome de usuário, um identificador para uma função de um usuário pertence, idade de um usuário e assim por diante. Um símbolo é geralmente digitalmente assinado, o que significa sempre podem se originar voltar ao seu emissor e seu conteúdo não pode ser violado. Um usuário obtém acesso a um aplicativo de RP apresentando um token válido emitido por uma autoridade que o aplicativo de RP confia.

**Provedor de identidade (IP)** - um IP é uma autoridade que autentica identidades de usuário e emite tokens de segurança. O trabalho real de emitir tokens é implementado apesar de um serviço especial, chamado serviço STS (Security Token). Exemplos de IPs típicos incluem Windows Live ID, Facebook, repositórios de usuário de negócios (como o Active Directory) e assim por diante.
Quando ACS é configurado para confiar em um IP, o sistema irá aceitar e validar tokens emitidos por que IP. ACS pode confiar vários IPs de uma só vez, que significa que quando seu aplicativo confia ACS, você pode instantaneamente oferecer seu aplicativo a todos os usuários autenticados de todos os IPs que ACS confia em seu nome.

**Provedor de serviços de Federação (FP)** - IPs têm conhecimento direto de usuários e autenticá-los usando suas credenciais e emitir declarações sobre o que elas sabem sobre eles. Um provedor de serviços de Federação (FP) é um tipo diferente de autoridade: em vez de autenticar usuários diretamente, ele atua como um intermediário e os agentes autenticação entre um RP e um ou mais endereços de IP. IPs e quadros/s emitir tokens de segurança, portanto, ambas usam serviços STS (Security Token). ACS é um FP.

**Mecanismo de regras de ACS** - a lógica usada para transformar tokens de entrada de IPs confiáveis aos tokens como objetivo ser consumida por RP é codificada no formulário de regras de transformação de declarações simples. ACS apresenta um mecanismo de regra que cuida da aplicação de qualquer lógica de transformação especificado para o RP.

**Namespace do ACS** - um namespace é uma partição de nível superior do ACS que você usa para organizar suas configurações. Um namespace mantém uma lista de IPs você confia, os aplicativos de RP desejar servir, as regras que você espera que a regra engine para processar tokens de entrada com e assim por diante. Um namespace expõe vários pontos de extremidade que serão usados pelo aplicativo e o desenvolvedor para obter ACS para executar sua função.

A figura a seguir mostra como autenticação ACS funciona com um aplicativo web:

![Diagrama de fluxo de ACS][acs_flow]

1.  O cliente (neste caso um navegador) solicita uma página do RP.
2.  Como a solicitação ainda não foi autenticado, o RP redireciona o usuário para a autoridade que ele confia, que é ACS. ACS apresenta o usuário com a opção de IPs que foram especificados para esse RP. O usuário seleciona o IP apropriado.
3.  O cliente navega até a página de autenticação do IP e solicita ao usuário para fazer logon.
4.  Depois que o cliente é autenticado (por exemplo, a identidade credenciais são inseridas), o IP emite um token de segurança.
5.  Depois de emitir um token de segurança, o IP redireciona o cliente para ACS e o cliente envia o token de segurança emitido por IP para ACS.
6.  ACS valida o token de segurança emitido por IP, entradas a identidade declarações nesse símbolo para o mecanismo de regras do ACS, calcula as declarações de identidade de saída e emite um novo token de segurança que contém essas declarações de saída.
7.  ACS redireciona o cliente para o RP. O cliente envia o novo token de segurança emitido por ACS para o RP. O RP valida a assinatura no token de segurança emitido por ACS, valida as declarações nesse símbolo e retorna a página que foi originalmente solicitada.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as tarefas deste guia, você precisará do seguinte:

- Um Java Developer Kit (JDK), v 1,6 ou posterior.
- Eclipse IDE para Java EE desenvolvedores, Indigo ou posterior. Isso pode ser baixado do <http://www.eclipse.org/downloads/>. 
- Uma distribuição de um servidor de web baseados em Java ou o servidor de aplicativos, como o Apache Tomcat, GlassFish, servidor da aplicação JBoss ou Jetty.
- uma assinatura do Azure, que pode ser adquirida no <http://www.microsoft.com/windowsazure/offers/>.
- Lançamento de abril de 2014 o Kit de ferramentas do Azure para Eclipse, ou posterior. Para obter mais informações, consulte [instalar o Kit de ferramentas do Azure para Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
- Um certificado x. 509 para usar com o aplicativo. Você precisará este certificado no certificado pública (. cer) e a troca de informações pessoais (. Formato PFX). (Opções para criar este certificado serão descritas posteriormente neste tutorial).
- Familiaridade com o Azure calcular emulador e técnicas de implantação discutidas na [criação de um aplicativo Hello World Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## <a name="create-an-acs-namespace"></a>Criar um Namespace do ACS

Para começar a usar o serviço de controle de acesso (ACS) no Azure, você deve criar um namespace ACS. O namespace fornece um escopo exclusivo para endereçar recursos ACS de dentro de seu aplicativo.

1. Log para o [Portal de gerenciamento do Azure][].
2. Clique em **Active Directory**. 
3. Para criar um novo namespace de controle de acesso, clique em **novo**, clique em **Serviços de aplicativo**, clique em **Controle de acesso**e, em seguida, clique em **Criar rápida**. 
4. Insira um nome para o namespace. Azure verifica se o nome é exclusivo.
5. Selecione a região na qual o namespace é usado. Para o melhor desempenho, use a região em que você está implantando seu aplicativo.
6. Se você tiver mais de uma assinatura, selecione a assinatura que você deseja usar para o namespace do ACS.
7. Clique em **criar**.

Azure cria e ativa o namespace. Aguarde até que o status de novo namespace está **ativo** antes de continuar. 

## <a name="add-identity-providers"></a>Adicionar provedores de identidade

Nesta tarefa, você adicionar endereços IP para usar com o aplicativo de RP para autenticação. Para fins de demonstração, essa tarefa mostra como adicionar o Windows Live como um IP, mas você pode usar qualquer um dos IPs listados no Portal de gerenciamento do ACS.


1.  No [Portal de gerenciamento do Azure][], clique em **Active Directory**, selecione um namespace de controle de acesso e, em seguida, clique em **Gerenciar**. O Portal de gerenciamento do ACS é aberto.
2.  No painel de navegação esquerdo do Portal de gerenciamento do ACS, clique em **provedores de identidade**.
3.  Windows Live ID está habilitado por padrão e não podem ser excluída. Para fins deste tutorial, é usado somente o Windows Live ID. Nessa tela, entretanto, é onde você pode adicionar outras IPs, clicando no botão **Adicionar** .

Windows Live ID está ativado como um IP para o seu espaço para nome do ACS. Em seguida, especifique seu aplicativo da web de Java (a ser criado mais tarde) como uma RP.

## <a name="add-a-relying-party-application"></a>Adicionar um aplicativo de terceiros terceira

Nesta tarefa, você configure o ACS para reconhecer seu aplicativo de web Java como um aplicativo de RP válido.

1.  No Portal de gerenciamento do ACS, clique em **aplicativos de terceiros Relying**.
2.  Na página **Confiar aplicativos de terceiros** , clique em **Adicionar**.
3.  Na página **Adicionar confiar de aplicativo de terceiros** , faça o seguinte:
    1.  Em **nome**, digite o nome do RP. Para fins deste tutorial, digite **Azure Web App**.
    2.  No **modo**, selecione **Inserir configurações manualmente**.
    3.  No **território**, digite o URI ao qual o token de segurança emitido por ACS se aplica. Para essa tarefa, digite **http://localhost:8080 /**.
        ![Contar território de terceiros para uso no emulador de computação][relying_party_realm_emulator]
    4.  Na **URL de retorno,** digite a URL para o qual ACS retorna o token de segurança. Para essa tarefa, digite **http://localhost:8080/MyACSHelloWorld/index.jsp**
        ![Relying festa retornar a URL para uso no emulador de computação][relying_party_return_url_emulator]
    5.  Aceite os valores padrão o restante dos campos.

4.  Clique em **Salvar**.

Você agora configurou com êxito seu aplicativo da web de Java quando ele é executado no emulador computação Azure (em http://localhost:8080 /) seja uma RP em seu namespace do ACS. Em seguida, crie as regras que ACS usa para processar declarações para a RP.

## <a name="create-rules"></a>Criar regras

Nesta tarefa, você deve definir as regras que direcionam como declarações são passadas de IPs para seu RP. Neste guia, podemos simplesmente configurará ACS para copiar os tipos de declaração de entrada e valores diretamente no token de saída, sem filtragem ou modificá-los.

1.  Na página principal do Portal de gerenciamento do ACS, clique em **grupos de regras**.
2.  Na página **Grupos de regras** , clique em **Grupo de regra padrão do Azure Web App**.
3.  Na página **Editar regra de grupo** , clique em **Gerar**.
4.  Sobre o **gerar regras: grupo de regra padrão do Azure Web App** página, certifique-se de Windows Live ID está marcada e clique em **Gerar**.    
5.  Na página **Editar regra de grupo** , clique em **Salvar**.

## <a name="upload-a-certificate-to-your-acs-namespace"></a>Carregar um certificado para seu namespace do ACS

Nesta tarefa, você carregar uma. Certificado PFX que será usado para assinar solicitações de token criadas pelo seu namespace do ACS.

1.  Na página principal do Portal de gerenciamento do ACS, clique em **certificados e chaves**.
2.  Na página **certificados e chaves** , clique em **Adicionar** acima **Token de assinatura**.
3.  Na página do **certificado de autenticação de Token adicionar ou chave** :
    1. Na seção **usado para** , clique em **Confiar aplicativo de terceiros** e selecione **Azure Web App** (que você anteriormente definido como o nome do seu aplicativo de terceiros confiante).
    2. Na seção **tipo** , selecione o **Certificado x. 509**.
    3. Na seção de **certificado** , clique no botão Procurar e navegue até o arquivo de certificado x. 509 que você deseja usar. Este será um. Arquivo PFX. Selecione o arquivo, clique em **Abrir**e, em seguida, insira a senha de certificado na caixa de texto **senha** . Observe que, para fins de teste, você pode usar um auto-signed-certificado. Para criar um certificado autoassinado, use o botão **novo** na caixa de diálogo **Biblioteca de filtro de ACS** (descrita posteriormente) ou use o utilitário de **encutil.exe** do [site de projeto][] do Azure Starter Kit para Java.
    4. Certifique-se de que **Tornar primário** está marcada. Sua página de **certificado de autenticação de Token adicionar ou tecla** deve ser semelhante ao seguinte.
        ![Adicionar certificado de autenticação de token][add_token_signing_cert]
    5. Clique em **Salvar** para salvar suas configurações e fechar a página de **certificado de autenticação de Token adicionar ou tecla** .

Em seguida, examine as informações na página de integração de aplicativos e copie o URI que você precisará configurar seu aplicativo da web de Java usar ACS.

## <a name="review-the-application-integration-page"></a>Revise a página de integração de aplicativos

Você pode encontrar todas as informações e o código necessárias para configurar seu aplicativo web do Java (o aplicativo RP) para trabalhar com o ACS na página de integração de aplicativo do Portal de gerenciamento do ACS. Você precisará dessas informações quando configurar seu aplicativo da web de Java para autenticação federada.

1.  No Portal de gerenciamento do ACS, clique em **integração de aplicativos**.  
2.  Na página de **Integração de aplicativos** , clique em **Páginas de Login**.
3.  Na página de **Integração de página de Login** , clique em **Azure Web App**.

No **integração de página de logon: Azure Web App** página, a URL listada no **opção 1: Link para uma página de logon hospedada pelo ACS** serão usadas no aplicativo da web Java. Você precisará esse valor quando você adiciona a biblioteca de filtro de serviços de controle de acesso do Azure para seu aplicativo Java.

## <a name="create-a-java-web-application"></a>Criar um aplicativo da web de Java
1. Dentro Eclipse, no menu, clique em **arquivo**, clique em **novo**e clique em **Projeto de Web dinâmico**. (Se você não vir o **Projeto de Web dinâmico** listado como um projeto disponível após clicar em **arquivo**, **novo**, faça o seguinte: clique em **arquivo**, clique em **novo**, clique em **projeto**, expanda **Web**, clique em **Dinâmico Web Project**e clique em **Avançar**.) Para fins deste tutorial, nomeie o projeto **MyACSHelloWorld**. (Certifique-se você usar esse nome, etapas subsequentes neste tutorial esperam seu arquivo de guerra seja nomeado MyACSHelloWorld). Sua tela aparecerá similar ao seguinte:

    ![Criar um projeto de Hello World para exampple do ACS][create_acs_hello_world]

    Clique em **Concluir**.
2. No modo de exibição do Eclipse Explorador de projeto, expanda **MyACSHelloWorld**. **Conteúdo da Web**de atalho, clique em **novo**e, em seguida, clique em **Arquivo JSP**.
3. Na caixa de diálogo **Novo arquivo JSP** , nomeie o arquivo **JSP**. Manter a pasta pai como MyACSHelloWorld/conteúdo da Web, conforme mostrado a seguir:

    ![Adicionar um arquivo JSP por exemplo do ACS][add_jsp_file_acs]

    Clique em **Avançar**.

4. Na caixa de diálogo **Selecionar JSP modelo** , selecione **Novo arquivo JSP (html)** e clique em **Concluir**.
5. Quando o arquivo JSP abre no Eclipse, adicionar no texto para exibição **Hello ACS World!** dentro das existentes `<body>` elemento. Seu atualizado `<body>` conteúdo deve ser exibido como o seguinte:

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
    
    Salve JSP.
  
## <a name="add-the-acs-filter-library-to-your-application"></a>Adicionar a biblioteca de filtro de ACS ao seu aplicativo

1. No Explorador de projeto do Eclipse, **MyACSHelloWorld**de atalho, clique em **Criar caminho**e clique em **Configurar construir caminho**.
2. Na caixa de diálogo **Caminho de compilação de Java** , clique na guia **bibliotecas** .
3. Clique em **Adicionar biblioteca**.
4. Clique em **Filtro de serviços de controle de acesso de Azure (por MS Tech abrir)** e clique em **Avançar**. A caixa de diálogo **Filtro de serviços de controle de acesso do Azure** é exibida.  (Campo de **localização** pode ter um caminho diferente, dependendo de onde você instalou o Eclipse, e o número da versão pode ser diferente, dependendo das atualizações de software).

    ![Adicionar biblioteca de filtro do ACS][add_acs_filter_lib]

5. Usando um navegador aberto para a página de **Integração de página de Login** do Portal de gerenciamento, copie a URL listada na **opção 1: Link para uma página de logon hospedada pelo ACS** campo e cole-o campo de **Ponto de extremidade do ACS autenticação** da caixa de diálogo Eclipse.
6. Usando um navegador aberto para a página **Editar aplicativos de terceiros confiar** do Portal de gerenciamento, copie a URL listada no campo **território** e cole-o no campo **Contar território de festa** da caixa de diálogo Eclipse.
7. Na seção **segurança** da caixa de diálogo Eclipse, se você quiser usar um certificado existente, clique em **Procurar**, navegue até o certificado que você deseja usar, selecione-o e clique em **Abrir**. Ou, se você quiser criar um novo certificado, clique em **novo** para exibir a caixa de diálogo **Novo certificado** , especifique a senha, o nome do arquivo. cer e o nome do arquivo. pfx para o novo certificado.
8. Verificar **o certificado no arquivo guerra Embed**. Incorporar o certificado dessa maneira inclui-lo na sua implantação sem exigir que você adicioná-la manualmente como um componente. (Se, em vez disso, você deve armazenar seu certificado externamente do seu arquivo de guerra, você pode adicionar o certificado como um componente de função e desmarque a opção **Inserir o certificado no arquivo guerra**.)
9. [Opcional] Manter **conexões HTTPS exigem** marcada. Se você definir essa opção, você precisará acessar seu aplicativo usando o protocolo HTTPS. Se você não quiser requerem conexões HTTPS, desmarque essa opção.
10. Para uma implantação para o emulador de computação, as configurações de **Filtro do Azure ACS** terá aparência semelhantes à seguinte.

    ![Configurações de filtro de ACS Azure para uma implantação para o emulador de computação][add_acs_filter_lib_emulator]

11. Clique em **Concluir**.
12. Clique em **Sim** quando for apresentado com uma caixa de diálogo informando que um arquivo Web. XML será criado.
13. Clique **Okey** para fechar a caixa de diálogo **Caminho de compilação de Java** .

## <a name="deploy-to-the-compute-emulator"></a>Implantar o emulador de computação

1. No Explorador de projeto do Eclipse, **MyACSHelloWorld**de atalho, clique **Azure**e, em seguida, clique em **pacote para Azure**.
2. Para **nome do projeto**, digite **MyAzureACSProject** e clique em **Avançar**.
3. Selecione um JDK e servidor de aplicativos. (Essas etapas são descritas em detalhes o tutorial [Criando um aplicativo Hello World Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) ).
4. Clique em **Concluir**.
5. Clique no botão **executar no emulador Azure** .
6. Depois que seu aplicativo da web de Java for iniciado no emulador computação, feche todas as instâncias do seu navegador (de forma que nenhuma sessão atual do navegador não interferir com o teste de login do ACS).
7. Execute o aplicativo abrindo <> http://localhost:8080/MyACSHelloWorld/no seu navegador ( <> ou/https://localhost:8080/MyACSHelloWorld/se que você marcou **conexões exigir HTTPS**). Você deve ser solicitado para um logon do Windows Live ID, depois que deve ser seguidas para a URL de retorno especificada para seu aplicativo de terceiros confiante.
99.  Quando terminar de seu aplicativo de visualização, clique no botão **Redefinir emulador do Azure** .

## <a name="deploy-to-azure"></a>Implantar para o Azure

Para implantar Azure, você precisará alterar o terceira território de festa e retornar a URL para o seu namespace do ACS.

1. Dentro do Portal de gerenciamento do Azure, na página **Editar aplicativos de terceiros confiar** , modifique **território** para ser a URL de seu site implantado. Substitua **exemplo** com o nome DNS que você especificou para sua implantação.

    ![Contar território de terceiros para uso na produção][relying_party_realm_production]

2. Modificar o **URL de retorno** para a URL do seu aplicativo. Substitua **exemplo** com o nome DNS que você especificou para sua implantação.

    ![Terceira URL de retorno de terceiros para uso na produção][relying_party_return_url_production]

3. Clique em **Salvar** para salvar seu território de festa respondendo atualizados e retornar alterações de URL.
4. Manter a página de **Integração de página de Login** aberta no seu navegador, você precisará copiem em breve.
5. No Explorador de projeto do Eclipse, **MyACSHelloWorld**de atalho, clique em **Criar caminho**e clique em **Configurar construir caminho**.
6. Clique na guia de **bibliotecas** , clique em **Filtro de serviços de controle de acesso do Azure**e clique em **Editar**.
7. Usando um navegador aberto para a página de **Integração de página de Login** do Portal de gerenciamento, copie a URL listada na **opção 1: Link para uma página de logon hospedada pelo ACS** campo e cole-o campo de **Ponto de extremidade do ACS autenticação** da caixa de diálogo Eclipse.
8. Usando um navegador aberto para a página **Editar aplicativos de terceiros confiar** do Portal de gerenciamento, copie a URL listada no campo **território** e cole-o no campo **Contar território de festa** da caixa de diálogo Eclipse.
9. Na seção **segurança** da caixa de diálogo Eclipse, se você quiser usar um certificado existente, clique em **Procurar**, navegue até o certificado que você deseja usar, selecione-o e clique em **Abrir**. Ou, se você quiser criar um novo certificado, clique em **novo** para exibir a caixa de diálogo **Novo certificado** , especifique a senha, o nome do arquivo. cer e o nome do arquivo. pfx para o novo certificado.
10. Manter **incorporar o certificado no arquivo guerra** marcada, supondo que você deseja inserir o certificado no arquivo guerra.
11. [Opcional] Manter **conexões HTTPS exigem** marcada. Se você definir essa opção, você precisará acessar seu aplicativo usando o protocolo HTTPS. Se você não quiser requerem conexões HTTPS, desmarque essa opção.
12. Para uma implantação do Azure, as configurações de filtro do Azure ACS terá aparência semelhantes à seguinte.

    ![Configurações de filtro de ACS Azure para uma implantação de produção][add_acs_filter_lib_production]

13. Clique em **Concluir** para fechar a caixa de diálogo **Editar biblioteca** .
14. Clique **Okey** para fechar a caixa de diálogo de **Propriedades de MyACSHelloWorld** .
15. No Eclipse, clique no botão **Publicar na nuvem do Azure** . Responda às solicitações, semelhantes como concluídos na seção **para implantar seu aplicativo no Azure** do tópico [Criando um aplicativo Hello World Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) . 

Após a implantação do seu aplicativo da web, feche qualquer sessões de navegador aberto, executar o aplicativo da web e você deve ser solicitado a entrar com as credenciais do Windows Live ID, seguidas por sendo enviado para a URL de retorno do seu aplicativo de terceiros a terceira.

Quando você terminar usando seu aplicativo de ACS Hello World, lembre-se de excluir a implantação (você pode aprender como excluir uma implantação no tópico [Criando um aplicativo Hello World Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) ).


## <a name="next_steps"></a>Próximas etapas

Para uma análise da declaração SAML Security Markup Language () retornado pelo ACS a seu aplicativo, consulte [como exibir SAML retornado pelo serviço de controle de acesso do Azure][]. Para explorar melhor funcionalidade do ACS e para experimentar situações mais sofisticadas, consulte [2.0 de serviço de controle de acesso][].

Além disso, este exemplo usou a opção **incorporar o certificado no arquivo guerra** . Esta opção torna mais simples de implantar o certificado. Se, em vez disso você deseja manter seu certificado de autenticação separada do seu arquivo de guerra, você pode usar a técnica a seguir:

1. Na seção **segurança** da caixa de diálogo **Filtro de serviços de controle de acesso do Azure** , digite **${env. JAVA_HOME}/mycert.cer** e desmarque **incorporar o certificado no arquivo guerra**. (Ajuste mycert.cer se o nome do arquivo de certificado for diferente). Clique em **Concluir** para fechar a caixa de diálogo.
2. Copie o certificado como um componente em sua implantação: Explorador no Eclipse de projeto, expanda **MyAzureACSProject**, **WorkerRole1**de atalho, clique em **Propriedades**, expanda a **Função do Azure**e clique em **componentes**.
3. Clique em **Adicionar**.
4. Na caixa de diálogo **Adicionar componente** :
    1. Na seção **Importar** :
        1. Use o botão de **arquivo** para navegar até o certificado que você deseja usar. 
        2. Para **método**, selecione **Copiar**.
    2. **Como o nome**, clique em caixa de texto e aceite o nome padrão.
    3. Na seção **implantar** :
        1. Para **método**, selecione **Copiar**.
        2. **Ao diretório**, digite **% JAVA_HOME %**.
    4. Sua caixa de diálogo **Adicionar componente** deve ser semelhante ao seguinte.

        ![Adicionar componente de certificado][add_cert_component]

    5. Clique em **Okey**.

Neste ponto, seu certificado seria incluído na sua implantação. Observe que, independentemente de se você inserir o certificado no arquivo guerra ou adicioná-lo como um componente à sua implantação, você precisa carregar o certificado no seu namespace conforme descrito na seção [carregar um certificado para seu namespace do ACS][] .

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Carregar um certificado para seu namespace do ACS]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[site de projeto]: http://wastarterkit4java.codeplex.com/releases/view/61026
[Como exibir SAML retornado pelo serviço de controle de acesso do Azure]: /en-us/develop/java/how-to-guides/view-saml-returned-by-acs/
[Serviço de controle de acesso 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Portal de gerenciamento do Azure]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png
 
