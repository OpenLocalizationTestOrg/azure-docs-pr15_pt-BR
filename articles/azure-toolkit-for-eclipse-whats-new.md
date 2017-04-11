<properties
    pageTitle="Novidades no Azure Kit de ferramentas para Eclipse"
    description="Saiba mais sobre os recursos mais recentes no Kit de ferramentas do Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/26/2016" 
    ms.author="robmcm;asirveda;martinsawicki"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh694270.aspx -->

# <a name="whats-new-in-the-azure-toolkit-for-eclipse"></a>Novidades no Azure Kit de ferramentas para Eclipse

## <a name="azure-toolkit-for-eclipse-releases"></a>Kit de ferramentas de Azure para lançamentos Eclipse

Este artigo contém informações sobre as diversas versões e atualizações mais recentes para o Kit de ferramentas do Azure para Eclipse.

> [AZURE.NOTE] Há também um kit de ferramentas do Azure para o IDE IntelliJ. Para obter mais informações, consulte [Kit de ferramentas do Azure para IntelliJ].

### <a name="august-26-2016"></a>26 de agosto de 2016

O Kit de ferramentas do Azure para Eclipse - lançamento de agosto de 2016 inclui as seguintes melhorias:

* **Distribuições JDK personalizado**. O Kit de ferramentas do Azure para Eclipse agora suporta especificando e implantar uma versão JDK aleatório para seu contêiner Web App Azure:
  - Além dos JDKs fornecidos pelo Azure, você também pode escolher entre uma ampla seleção de versões de Zulu OpenJDK disponibilizados no Azure por sistemas de Azul.
  - Você também pode especificar sua próprias distribuição JDK se você carregá-lo como um arquivo ZIP à sua conta de armazenamento.
* **Aprimoramentos para o modo de exibição do Azure Explorer**:
  - Suporte para o gerenciamento de máquinas virtuais usando o novo modelo de Gerenciador de recursos do Azure: você pode listar, criar e excluir máquinas virtuais baseados em Gerenciador de recursos sem sair do IDE.
  - Suporte para gerenciamento de contas de armazenamento de blob usando o Gerenciador de recursos do Azure, que completa a funcionalidade existente para gerenciar contas de armazenamento "clássico".
* **O Driver de Microsoft JDBC 6.0 para o SQL Server**. Essa atualização inclui o driver JDBC mais recente para o Microsoft SQL Server (v 6.0), que é agora incluído como uma biblioteca que você pode adicionar facilmente seus projetos Java, substituindo, portanto, a versão mais antiga.

### <a name="june-29-2016"></a>29 de junho de 2016

O Kit de ferramentas do Azure para Eclipse - lançamento de junho de 2016 inclui as seguintes melhorias:

* **Requisito de Java 8**. O Kit de ferramentas do Azure para Eclipse agora requer Java 8, embora esse requisito é somente para o Kit de ferramentas - seus aplicativos podem continuar a usar todas as versões do Java que são suportadas pelo Azure.
* **Suporte para o mais recente JDKs Java**. As versões mais recentes do JDKs Java agora são suportadas pelo Kit de ferramentas do Azure para Eclipse.
* **Suporte para o SDK Azure v2.9.1**. A versão mais recente do SDK do Azure agora é o pré-requisito mínimo para o Kit de ferramentas do Azure para Eclipse.
* **Exemplos de integrados**. O Kit de ferramentas do Azure para Eclipse agora apresenta vários aplicativos de exemplo para ajudar os desenvolvedores a começar.
* **Integração de ferramenta de HDInsight**. Ferramentas de HDInsight do Azure agora estão incluídas com o Kit de ferramentas do Azure para Eclipse. Para obter mais informações, consulte [HDInsight ferramentas plug-in para Eclipse].
* **Depuração de aplicativos da Web de Java remota**. O Kit de ferramentas do Azure para Eclipse agora dá suporte a depuração de Java web apps em serviço de aplicativo do Azure remoto.
* **Suporte a versão do Eclipse Luna.** A nova versão mínima necessária Eclipse IDE é Luna.

### <a name="april-12-2016"></a>12 de abril de 2016

O Kit de ferramentas do Azure para Eclipse - lançamento de abril de 2016 inclui as seguintes melhorias:

* **Suporte para o SDK Azure v2.9.0**. A versão mais recente do SDK do Azure agora é o pré-requisito mínimo para o Kit de ferramentas do Azure para Eclipse.
* **Usabilidade diversas, melhorias de desempenho e capacidade de resposta relacionadas ao suporte do Azure Web App**. Um número de otimização de desempenho em como o Kit de ferramentas se comunica com resultado Azure em uma interface de usuário mais responde.
* **Capacidade de excluir um contêiner de aplicativo da Web existente no Azure dentro Eclipse**. O Kit de ferramentas do Azure para Eclipse agora permite que você exclua um contêiner existente do Azure Web sem sair do Eclipse.

### <a name="march-7-2016"></a>7 de março de 2016

O Kit de ferramentas do Azure para Eclipse - lançamento de março de 2016 inclui as seguintes melhorias:

* **Suporte para implantação rápida de aplicativos Java leves**. O Kit de ferramentas do Azure para Eclipse agora dá suporte a implantação rápida de aplicativos Java leves em contêineres de aplicativo do Azure Web, portanto implantar aplicativos Java agora leva segundos em vez de minutos.
* **Suporte para gerenciamento de aplicativo Web usando o modo de exibição do Explorer do Azure**. O modo de exibição do Explorer do Azure no Kit de ferramentas agora oferece suporte para a listagem, iniciar e parar o Azure Web Apps.
* **Distribuições Tomcat atualizado, Jetty e Zulu OpenJDK**. O Kit de ferramentas do Azure para Eclipse fornece suporte para versões atualizadas do Tomcat, Jetty e Zulu OpenJDK para implantações de Java em serviços de nuvem Azure.

### <a name="january-4-2016"></a>4 de janeiro de 2016

O Kit de ferramentas do Azure para Eclipse - lançamento de janeiro de 2016 inclui as seguintes melhorias:

* **Suporte para as atualizações de Zulu OpenJDK**. Para obter mais informações, consulte a [página da web de sistemas de Azul para o OpenJDK Zulu].
* **Tomcat atualizados e distribuições de Jetty**. As distribuições Jetty e Tomcat que estão disponíveis na Microsoft Azure para uso com o Kit de ferramentas do Azure para Eclipse foram atualizadas.
* **Paridade de recurso entre Eclipse e IntelliJ Toolkits Azure**. O Kit de ferramentas do Azure para Eclipse e o [Kit de ferramentas do Azure para IntelliJ] agora suportam o mesmo conjunto de recursos.

### <a name="september-1-2015"></a>1º de setembro de 2015

O Kit de ferramentas do Azure para Eclipse - lançamento de setembro de 2015 inclui as seguintes melhorias:

* **Suporte para as atualizações de Zulu OpenJDK**. Para obter mais informações, consulte a [página da web de sistemas de Azul para o OpenJDK Zulu].
* **Tomcat atualizados e distribuições de Jetty**. As distribuições Jetty e Tomcat que estão disponíveis na Microsoft Azure para uso com o Kit de ferramentas do Azure para Eclipse foram atualizadas. (Essas distribuições permite que os desenvolvedores criem desenvolvimento rápido e testar projetos com o Kit de ferramentas do Azure para Eclipse.
* **Suporte a referências Tomcat e Jetty atualizadas automaticamente**. Além de versões específicas do Tomcat e Jetty que estão disponíveis no Azure, os desenvolvedores agora podem referenciar uma distribuição conhecida como a "mais recente (atualizado automaticamente)", que será atualizado automaticamente para a distribuição mais recente de cada versão principal de Jetty ou Tomcat na próxima vez que suas instâncias de função são reciclados. (Reciclagem ocorre automaticamente, mas os desenvolvedores podem acionar manualmente uma reciclagem por meio do portal Azure.) Esse novo recurso significa que os desenvolvedores não têm reimplantar seu aplicativo possam ter seu software de servidor atualizado. (
*  Essa funcionalidade atualmente destina somente para fins de desenvolvimento e teste ou não-aplicativos essenciais e não é recomendada para produção.)
* **Modo de exibição do Explorer do azure para blobs, filas e tabelas no armazenamento do Azure**. Isso permite aos desenvolvedores realizar um conjunto de tarefas comuns com seus artefatos de armazenamento diretamente do IDE Eclipse. Por exemplo: excluindo, carregando ou baixando blobs.

### <a name="august-1-2015"></a>1º de agosto de 2015

O Kit de ferramentas do Azure para Eclipse - lançamento de agosto de 2015 inclui as seguintes melhorias:

* **Gerenciamento de chaves do aplicativo ideias instrumentação**. Essa atualização permite que você adquirir, criar e gerenciar suas chaves de instrumentação de obtenção de informações do aplicativo diretamente do IDE Eclipse.
* **O Driver de Microsoft JDBC 4.1 para o SQL Server**. Essa atualização inclui suporte para o driver JDBC mais recente para o Microsoft SQL Server.
* **Versão 2.7 do SDK do Azure**. Essa atualização mais recente para o SDK do Azure é pré-requisito novo para o Kit de ferramentas quando instalado no Windows. (Observe que isso não é necessário em sistemas operacionais não-Windows.)
* **Suporte para v7 Zulu OpenJDK atualizar**. Para obter mais informações, consulte a [página da web de sistemas de Azul para o OpenJDK Zulu].

### <a name="may-1-2015"></a>1º de maio de 2015

O Kit de ferramentas do Azure para Eclipse - lançamento de maio de 2015 inclui as seguintes melhorias:

* **Aprimorado a seleção de servidor de interface do usuário**. Esta versão simplifica o uso do Kit de ferramentas em sistemas operacionais não-Windows.
* **Suporte para projetos Maven**. Esta versão oferece suporte a projetos Maven como aplicativos, que pode implantar o Kit de ferramentas para o Azure e configurar a obtenção de informações do aplicativo.
* **Versão 2.6 do SDK do Azure**. Essa atualização mais recente para o SDK do Azure é pré-requisito novo para o Kit de ferramentas quando instalado no Windows. (Observe que isso não é necessário em sistemas operacionais não-Windows.)
* **Atualização de implantação em vez de republicar**. Se você estiver republicar um projeto de implantação quando a versão anterior já está ao vivo, o Kit de ferramentas agora usa funcionalidade de atualização de implantação do Azure em vez de desligar a implantação anterior e republicar do zero, como fazia no passado. Isso permite que o seu serviço de nuvem para execução sem interrupção sempre que possível, ajudando a alcançar alta disponibilidade mesmo durante uma atualização e acelera o processo de publicação novamente.
* **Suporte para o mais recente v8 Zulu OpenJDK - atualização 40**. Para obter mais informações, consulte a [página da web de sistemas de Azul para o OpenJDK Zulu].

### <a name="march-9-2015"></a>9 de março de 2015

O Kit de ferramentas do Azure para Eclipse - lançamento de março de 2015 inclui as seguintes melhorias:

* **Suporte para Mac, Ubuntu e tipos adicionais de Linux**. Esta versão do Kit de ferramentas do Azure para Eclipse adiciona suporte para o Mac OS e várias plataformas Unix, para que os desenvolvedores podem instalar o Kit de ferramentas para criar, configurar e publicar projetos Java para serviços de nuvem do Azure (PaaS) do Eclipse executados em sistemas operacionais além do Windows.

>[AZURE.NOTE] Esse recurso é na visualização e não é recomendado para uso em ambientes de produção. Não há suporte ao cliente contrato de nível de serviço (SLA), mas todos os comentários é muito bem-vindo e recomenda-se.

* **Plug-in novas percepções do aplicativo**. Os desenvolvedores agora são capazes de configurar telemetria servidor automático usando ideias de aplicativo no Azure.
* **Automação de implantação de linha de comando baseado em ant**. Este recurso permite aos desenvolvedores automatizar a publicação de versões mais recentes de suas implantações usando Ant fora do Eclipse. Um script previamente gerado automaticamente está configurado para um projeto após a primeira vez que ele é implantado do Eclipse e implantações subsequentes podem usar o script para automatizar totalmente implantações via somente a linha de comando.
* **Disponibilidade de tomcat e Jetty no Azure para implantação mais simples e mais rápido**. Desenvolvedores agora podem fazer referência a várias versões de Tomcat e Jetty que estão disponíveis no Azure diretamente em vez de ter que carregar um servidor Java para suas contas (ou por meio do Kit de ferramentas), portanto não é necessário para carregar um servidor Java para cenários rápidos, guia de Introdução.
* **Método de atalho para publicação Java web apps para serviços de nuvem Azure**. Para reduzir a curva de aprendizado para cenários de desenvolvimento e teste simples, os desenvolvedores agora podem publicar aplicativos Java mais diretamente para o Azure. Em vez de ter que percorrer o processo de criação e configuração de um projeto de implantação do Azure, aplicativos serão implantados com uma instância padrão do v8 Tomcat e JVM Zulu (OpenJDK).

### <a name="january-30-2015"></a>30 de janeiro de 2015

O Kit de ferramentas do Azure para Eclipse - lançamento de janeiro de 2015 inclui as seguintes melhorias:

* **Suporte para núcleo de liberdade do servidor de aplicativo IBM® WebSphere®**. Esta versão adiciona o IBM WebSphere Application Server liberdade Core à lista de servidores de aplicativos com suporte do qual o Kit de ferramentas é capaz de implantar para o Azure. Essa adição mais recente expande a lista atual de servidores de aplicativos que são compatíveis com &quot;-de-prontos&quot; pelo Kit de ferramentas, que já incluídas várias versões do Tomcat, Jetty, JBoss e GlassFish.
* **Inclusão de obtenção de informações do aplicativo SDK**. Essa biblioteca de API de cliente lançada recentemente (v0.9.0) agora é parte do pacote para bibliotecas do Azure para Java.
* **Atualizado pacote para bibliotecas Azure para Java**. Essa atualização inclui bibliotecas do Azure para Java v0.7.0 e v2.0.0 da API do cliente de armazenamento, bem como a v0.9.0 de aplicativo ideias SDK lançados recentemente.

### <a name="november-12-2014"></a>12 de novembro de 2014

O Kit de ferramentas do Azure para Eclipse - lançamento de novembro de 2014 inclui as seguintes melhorias:

* **Suporte para o SDK do Azure 2.5**. Essa atualização mais recente para o SDK do Azure é pré-requisito novo para o Kit de ferramentas.
* **Suporte para uma versão atualizada do v 1.8 Zulu OpenJDK, v 1.7 e v 1.6 pacotes**. Para obter mais informações, consulte a [página da web de sistemas de Azul para o OpenJDK Zulu].
* **Suporte para os novos tamanhos de D padrão para serviços de nuvem**, o qual oferta aumentou o desempenho e os recursos de memória adicional. Para obter mais informações, consulte [Máquina Virtual e tamanhos de serviço de nuvem do Azure].

### <a name="october-17-2014"></a>17 de outubro de 2014

O Kit de ferramentas do Azure para Eclipse - lançamento de outubro de 2014 inclui as seguintes melhorias:

* **Melhorias de desempenho na Publicar cenários de nuvem**. Carregamento de informações de assinatura é muito mais rápido quando os usuários têm várias assinaturas e contas de armazenamento.
* **Suporte para uma versão atualizada do pacote Zulu OpenJDK v 1.8**. Para obter mais informações, consulte a [página da web de sistemas de Azul para o OpenJDK Zulu].
* **Suporte para substituição de versões mais antigas do 3º JDKs de terceiros**. Pacotes JDK preteridos já não aparecerá no menu suspenso para novos projetos de implantação. Referência pacotes JDK preteridos continuará sendo capaz de fazê-lo para o horário sendo, mas é recomendado atualizar tais projetos para contar com as últimas de projetos existentes.
* **Versão atualizada do pacote para as bibliotecas do Azure de biblioteca de API de cliente Java**. Para obter mais informações, consulte a [API do cliente do Microsoft Azure].
* **Correções de bugs.** Esta versão contém um número de diversas correções de bugs que foram com base em relatórios do usuário e testes.

### <a name="august-5-2014"></a>5 de agosto de 2014

O Kit de ferramentas do Azure para Eclipse - lançamento de agosto de 2014 inclui os seguintes aperfeiçoamentos

* **Suporte para o SDK do Azure 2,4.** Versões mais antigas do Kit de ferramentas Eclipse não funcionará com o SDK lançado recentemente.
* **As versões atualizadas do Zulu OpenJDK v 1.6, 1.7 e v 1.8 pacotes.** Para obter mais informações, consulte a [página da web de sistemas de Azul para o OpenJDK Zulu].
* **Versão atualizada do pacote para bibliotecas do Azure para a biblioteca de API de cliente Java.** Para obter mais informações, consulte a [API do cliente do Microsoft Azure].
* **Suporte para mais recente publicar o formato de arquivo de configurações.** Foi adicionado suporte para a versão 2.0 do formato de arquivo de configurações de publicação.
* **Alterações de arquitetura atrás publicar para o recurso de nuvem.** O Kit de ferramentas agora está usando a API do Microsoft Azure cliente lançados recentemente para Java para o suporte de publicar na nuvem.
* **Correções de bugs.** Esta versão contém um número de correções de bugs solicitada pelo usuário.

### <a name="june-12-2014"></a>12 de junho de 2014

O Kit de ferramentas do Azure para Eclipse - lançamento de junho de 2014 é uma pequena atualização do serviço que fornece as seguintes melhorias:

* **Suporte para o pacote de Zulu OpenJDK v 1.8.** Para obter mais informações, consulte a [página da web de sistemas de Azul para o OpenJDK Zulu].
* **Versões atualizadas a v 1.6 Zulu OpenJDK e 1,7 pacotes.** Para obter mais informações, consulte a [página da web de sistemas de Azul para o OpenJDK Zulu].
* **Versão atualizada do pacote para bibliotecas do Azure para a biblioteca de API de cliente Java.** Para obter mais informações, consulte a [API do cliente do Microsoft Azure].
* **Correções de bugs.** Esta versão contém um número de correções de bugs solicitada pelo usuário.

### <a name="april-4-2014"></a>4 de abril de 2014

O plug-in do Azure para Eclipse - lançamento de abril de 2014 foi lançado. Esta é uma atualização que acompanha o lançamento do Azure SDK 2,3, que é um pré-requisito e será baixado automaticamente quando você instala o plug-in. Esta atualização inclui novos recursos, correções de bugs e algumas melhorias de usabilidade orientado por comentários como visualizar de fevereiro de 2014:

* **Suporte a versão do Azure SDK 2.3.** O plug-in do Azure para Eclipse - lançamento de abril de 2014 requer Azure SDK 2.3. Ao usar o novo plug-in, se você ainda não tiver Azure SDK 2.3, você será solicitado a permitir a sua instalação. Não use Azure SDK 2.3 com versões anteriores do plug-in.
* **Atualização de aplicativos sem implantação de pacote completo.** Ao implantar aplicativos Java que fazem parte de seu projeto, o plug-in agora carrega automaticamente-los para sua conta de armazenamento selecionado para que você possa atualizá-lo e Lixeira instâncias de função para implantar os bits mais recentes do aplicativo sem precisar recriar e reimplantar todo o pacote.
* **Tomcat 8 agora é um servidor de aplicativo reconhecido.** Se você selecionar uma pasta de instalação Tomcat 8 no seu computador na guia **servidor** da caixa de diálogo **Projeto de implantação do Azure** , o plug-in agora automaticamente o detectar e poder implantar Tomcat 8 de forma automática, semelhante a versões mais antigas do Tomcat já na lista.
* **Atualizações de pacote de azul Zulu OpenJDK: 51 de atualização de v 1.7 e v 1.6 47 de atualização.** Eficaz com esta versão, atualização de pacote do sistema Azul JDK abrir Zulu v7 51 está disponível. Além disso, pacotes de v6 JDK abrir Zulu iniciar sendo disponível, começando com atualização 47. Essas atualizações são além do pacote de v7 Zulu abrir JDK disponível anteriormente atualizar 45, atualize 40 e atualizar 25.
* **Suporte para o tamanho de A8 e A9 Microsoft Azure Virtual Machine.** Agora você pode implantar um serviço de nuvem para a memória alta A8 e tamanhos de A9 Virtual Machine. Para obter mais informações sobre esses tamanhos de máquina virtual, consulte [Máquina Virtual e tamanhos de serviço de nuvem do Azure].
* **Redirecionamento automático de HTTP para HTTPS para funções habilitados para SSL.** Quando seu serviço de nuvem contém funções HTTPS somente, se a solicitação de usuário especificar HTTP, ele redirecionará automaticamente para HTTPS. Não é necessário criar uma função separada para manipular as solicitações HTTP.
* **Expresse emulador usado para emulação local.** O Azure Express emulador agora é usado como o emulador ao depurar seus aplicativos localmente.
* **Azure agora é chamado como Microsoft Azure.** Telas de interface do usuário agora refletem que Azure foi agora é chamado e não mais chamado Azure.

### <a name="february-6-2014"></a>6 de fevereiro de 2014

O plug-in do Azure para Eclipse - fevereiro de 2014 visualização foi lançado. Esta atualização inclui novos recursos, correções de bugs e algumas melhorias de usabilidade orientado por comentários como visualizar de outubro de 2013:

* **Suporte para SSL transferindo.** Transferindo segura de Sockets Layer (SSL) foi adicionado como um recurso, permitindo que você facilmente ativar o suporte de HTTPS Hypertext Transfer Protocol Secure () na sua implantação Java no Azure, sem a necessidade de configurar SSL em seu servidor de aplicativos Java. Isso é particularmente relevante na sessão afinidade e/ou autenticados cenários de comunicação. Por exemplo, ao usar o filtro de serviço de controle de acesso (ACS), que já é suportado pelo Kit de ferramentas. Para obter mais informações, consulte [Transferindo SSL] e [como usar SSL transferindo].
* **GlassFish 4 agora é um servidor de aplicativo reconhecido.** Se você selecionar um diretório de instalação do GlassFish 4 em sua máquina na guia **servidor** da caixa de diálogo **Projeto de implantação do Azure** , o plug-in agora automaticamente o detectar e poder implantar GlassFish extensões 4 de forma automática, semelhante à versão GlassFish extensões 3 já está na lista.
* **Atualização de pacote de azul Zulu OpenJDK 45.** Eficiente com esta versão Zulu (pacote de v7 JDK aberta) atualização do sistema de Azul 45 agora está disponível; Isso é além a atualização disponível anteriormente 40 e atualização de 25.
* **Suporte para 'auto' para particular portas de ponto de extremidade.** Você pode definir uma porta particular como automático para os pontos de extremidade de entrada e pontos de extremidade internos para permitir que o Azure atribuir uma porta automaticamente a esse ponto de extremidade. Anteriormente você só pode atribuir um número de porta específico.
* **Suporte para personalizar o nome do certificado (CN) na criação certificado auto-assinado da interface do usuário.** Anteriormente, o mesmo nome embutido foi usado para todos os novos certificados; Agora você pode especificar seu próprio nome de certificado para ajudar a distinguir entre vários certificados no portal do Azure usados para fins diferentes.
* **Ferramentas azure:** Barra de ferramentas Azure tem um atualizado com as seguintes alterações: 
    * ![][ic710876]Este ícone foi adicionado para o **Novo projeto de implantação do Azure**.
    * ![][ic710877]Este ícone foi adicionado como um atalho para a caixa de diálogo de criação de certificado auto-assinado.
* **Suporte para o tamanho da máquina de Virtual do Azure A5.** Agora você pode implantar um serviço de nuvem para a memória alta tamanho da máquina Virtual de A5. Para obter mais informações sobre esse tamanho de máquina virtual, consulte [Máquina Virtual e tamanhos de serviço de nuvem do Azure].
* **Suporte para Microsoft Windows Server 2012 R2.** Agora você pode selecionar o Windows Server 2012 R2 como o sistema operacional na nuvem.

### <a name="october-22-2013"></a>22 de outubro de 2013

O plug-in do Azure para Eclipse - outubro de 2013 Preview foi lançado. Essa atualização inclui novos recursos, correções de bugs e algumas melhorias de usabilidade orientado por comentários, desde que a setembro de 2013 Preview:

* **Suporte a versão do Azure SDK 2.2.** O plug-in do Azure para Eclipse - outubro de 2013 visualizar suporta Azure SDK 2.2. O plug-in ainda funcionarão com o Azure SDK 2.1 e será instalado automaticamente Azure SDK 2.2 se você ainda não tiver pelo menos Azure SDK 2.1 instalado.
* **Atualização de pacote de azul Zulu OpenJDK 40.** Como anunciado para setembro de 2013 visualizar, agora o plug-in permite que o usando um JDK fornecido terceiros diretamente no Azure, sem a necessidade de carregar seu próprio JDK. Na versão de outubro de 2013, atualização de Zulu (pacote de v7 JDK aberta) do sistema de Azul 40 está disponível; Isso é além da atualização originalmente publicada 25.
* **Link de implantação de nuvem no registro de atividade.** Dentro do Log de atividade do Azure, quando sua implantação tem um status de **publicado**, você pode clicar em **publicado** já que agora é um link para sua implantação; sua implantação, em seguida, será aberta no navegador. (O status de **publicado** foi anteriormente rotulada como **executando**.)
* **Seleção de sistema operacional de destino disponíveis em Publicar tempo.** A caixa de diálogo **Publicar Azure** contém um novo campo, o **Sistema operacional de destino**, que fornece uma maneira mais detectável para você definir seu sistema operacional de destino.
* **Autosobrescrever implantação anterior.** A caixa de diálogo **Publicar Azure** contém uma nova caixa de seleção, **implantação anterior substituir**. Se essa opção estiver marcada, quando nova implantação é publicada automaticamente, ele substituirá a implantação anterior; Você não seria enfrentar &quot;409 Conflito&quot; problemas ao publicar no mesmo local sem primeiro cancelar publicação a implantação anterior.
* **Jetty 9 agora é um servidor de aplicativo reconhecido.** Se você selecionar um diretório de instalação do Jetty 9 em sua máquina na guia **servidor** da caixa de diálogo **Projeto de implantação do Azure** , o plug-in agora automaticamente o detectar e poder implantar Jetty 9 de forma automática, semelhante às versões mais antigas do Jetty já está na lista.
* **Adicione uma função a partir do menu de contexto do Project.** Menu de contexto de projeto do **Azure** agora contém um novo item de menu, **Adicionar função**, que fornece uma rápida e mais detectável maneira de adicionar uma nova função ao seu projeto Azure.
* **Uma atualização para o pacote para as bibliotecas do Azure para biblioteca Java.** Isso se baseia na versão 0.4.6 da [API do cliente do Microsoft Azure].

### <a name="september-25-2013"></a>25 de setembro de 2013

O plug-in do Azure para Eclipse - setembro de 2013 Preview foi lançado. Esta atualização inclui novos recursos, correções de bugs e algumas melhorias de usabilidade orientado por comentários, desde que a agosto de 2013 Preview:

* **Capacidade de implantar o pacote de Azul Zulu OpenJDK disponível no Azure.** Uma nova opção foi adicionada ao especificar o JDK para usar com sua implantação do Azure. Usando essa opção, você pode implantar um pacote JDK de terceiros diretamente na nuvem Azure, sem precisar carregar seus próprios. Sistemas de azul está fornecendo a primeira tal pacote chamado Zulu, com base em OpenJDK, que agora pode ser implantado usando essa opção.
* **Uma atualização para o pacote para as bibliotecas do Azure para biblioteca Java.** Isso se baseia na versão 0.4.5 da [API do cliente do Microsoft Azure].

### <a name="august-1-2013"></a>1º de agosto de 2013

O plug-in do Azure para Eclipse - agosto de 2013 Preview foi lançado. Esta é uma atualização que acompanha o lançamento do Azure SDK 2.1, que é um pré-requisito e será baixado automaticamente quando você instala o plug-in. Esta atualização inclui novos recursos, correções de bugs e algumas melhorias de usabilidade orientado por comentários como visualizar de julho de 2013:

* **Remoção de opções para incluir o JDK local e o servidor de aplicativo local como parte do pacote de implantação.** Baixar o JDK e servidor de aplicativo de armazenamento em nuvem durante a implantação é preferível incorporação esses componentes no pacote, desde baixando os resultados de itens no tamanho menor de pacote de implantação, tempos de implantação e facilitar a manutenção. Como resultado, as opções para incluir o JDK e servidor de aplicativos no pacote de implantação foram removidos. Projetos existentes que foram configurados para incluir o JDK local e o servidor de aplicativo local como parte do pacote de implantação será convertido automaticamente para carregamento automático o JDK e servidor de aplicativo para armazenamento em nuvem.
* **Suporte a versão 2.1 do Azure SDK.** O plug-in do Azure para Eclipse - agosto de 2013 Preview requer Azure SDK 2.1. Não use o agosto de 2013 preview com versões anteriores do SDK do Azure e não use Azure SDK 2.1 com versões anteriores do plug-in do Azure para Eclipse.
* **Suporte para a versão de Kepler Eclipse.** Relacionados a isso, a nova versão mínima necessária Eclipse IDE é Indigo. O plug-in do Azure para Eclipse já não oficialmente seja testado em Helios.

### <a name="july-3-2013"></a>3 de julho de 2013

O plug-in do Azure para Eclipse - julho de 2013 Preview foi lançado. Esta atualização inclui novos recursos, correções de bugs e algumas melhorias de usabilidade orientado por comentários como visualizar de maio de 2013:

* **Capacidade de criar uma nova conta de armazenamento.** Um botão **New** foi adicionado à caixa de diálogo **Adicionar conta de armazenamento** . Isso permite que você crie uma conta de armazenamento dentro do plug-in Eclipse, sem exigir que você faça logon no Portal de gerenciamento do Azure. (Você já deve ter uma assinatura do Azure para usar este recurso.) Para obter mais informações sobre como criar uma nova conta de armazenamento, consulte [criar uma nova conta de armazenamento].
* **Novo &quot;(auto)&quot; opção conta de armazenamento usada para implantação automática do JDK e servidor e armazenamento em cache.** Ao usar a opção **carregar automaticamente** para o JDK e servidor de aplicativos, agora você pode especificar **(automático)** para a conta de armazenamento e a URL para usar ao carregar o JDK e servidor de aplicativos, ou quando estiver usando o cache do Azure. Em seguida, esses recursos automaticamente usará a mesma conta de armazenamento como o que você selecionar na caixa de diálogo **Publicar Azure** . O tutorial [Criando um aplicativo Hello World Azure no Eclipse] foi atualizado para usar a nova opção de **(automático)** .
* **Capacidade de definir seus pontos de extremidade do serviço Azure.** Especifique os pontos de extremidade do serviço que determinam que se o seu aplicativo é implantado e gerenciado pela plataforma Windows Azure global, Azure operado pela 21Vianet na China, ou um particular plataforma Windows Azure. Para obter mais informações, consulte [Azure pontos de extremidade do serviço].
* **Grandes implantações podem especificar um recurso de armazenamento local.** Se sua implantação é muito grande para ser contido na pasta padrão approot, agora você pode especificar um recurso de armazenamento local como o destino de implantação para o seu JDK e servidor de aplicativos. Para obter mais informações, consulte [Implantando grandes implantações].
* **Suporte para tamanhos A6 e A7 Azure Virtual Machine.** Agora você pode implantar um serviço de nuvem para a memória alta A6 e A7 Virtual Machine tamanhos. Para obter mais informações sobre esses tamanhos, consulte [Máquina Virtual e tamanhos de serviço de nuvem do Azure].
* **Uma atualização para o pacote para as bibliotecas do Azure para biblioteca Java.** Isso se baseia na versão 0.4.4 da [API do cliente do Microsoft Azure].

### <a name="may-1-2013"></a>1 de maio de 2013

O plug-in do Azure para Eclipse - talvez 2013 Preview foi lançado. Esta é uma atualização principal que acompanha o lançamento do Azure SDK 2.0, que é um pré-requisito e será baixado automaticamente quando você instala o plug-in. Esta versão inclui novos recursos, correções de bugs e algumas melhorias de usabilidade orientado por comentários como visualizar de fevereiro de 2013:

* **Carregamento automático do JDK e servidor de aplicativo e implantação de armazenamento do Azure.** Uma nova opção que carrega o JDK selecionado e o servidor do aplicativo, quando necessário, para uma conta de armazenamento do Azure especificado automaticamente e implanta esses componentes a partir daí, em vez de incorporação no pacote de implantação ou tendo o upload de usuário, em seguida, manualmente. Este recurso comumente solicitado para melhorar a facilidade de implantar os componentes de servidor e JDK, especialmente para usuários iniciantes. Para uma passo a passo que usa essas opções, consulte [Criando um aplicativo Hello World Azure no Eclipse].
* **Centralizado o controle de conta de armazenamento e a capacidade de referência de contas de armazenamento mais facilmente (através de um controle de lista suspensa).** Isso se aplica a vários recursos que dependem de armazenamento, como o JDK e implantação do componente de servidor e armazenamento em cache. Para obter mais informações, consulte a [Lista de contas de armazenamento do Azure].
* **Configuração de acesso remoto simplificada na publicar os Assistente de nuvem.** Tudo o que você precisa fazer é digite um nome de usuário e senha para habilitar o acesso remoto ou deixe em branco para manter o acesso remoto desativado.
* **Uma atualização para o pacote para as bibliotecas do Azure para biblioteca Java.** Isso se baseia na versão 0.4.2 da [API do cliente do Microsoft Azure].
* **Suporte para sessões conjunta no Windows Server 2012.** Anteriormente, sessões conjunta trabalharam apenas no Windows Server 2008 R2, agora ambos nuvem afinidade de sessão de suporte do sistema operacional destinos.
* **Melhorias de desempenho de carregamento de pacote.** Mesmo quando o JDK e servidor de aplicativo são incorporados no pacote de implantação, a carregar parte do processo de implantação pode ser aproximadamente duas vezes mais rápida em comparação com versões anteriores.

### <a name="february-8-2013"></a>8 de fevereiro de 2013

O plug-in do Azure para Eclipse - fevereiro de 2013 Preview foi lançado. Esta é uma atualização secundária que inclui correções de bugs, melhorias de usabilidade orientado por comentários e alguns recursos novos como visualizar de novembro de 2012:

* Suporte para implantação JDKs, servidores de aplicativos e aleatório outros componentes do público ou privado Azure blob downloads de armazenamento em vez de incluí-los no pacote de implantação ao implantar na nuvem.
* Capacidade de alterar a ordem na qual componentes definidos pelo usuário de uma função são processadas, por meio da adição de botões **Mover para cima** e **Mover para baixo** na seção **componentes** das **Propriedades de função do Azure**.
* Atualização para a biblioteca de **pacote para as bibliotecas do Azure para Java** , com base na versão 0.4.0 da [API do cliente do Microsoft Azure].

### <a name="november-5-2012"></a>5 de novembro de 2012

O plug-in do Azure para Eclipse - novembro de 2012 visualização foi lançado. Esta é uma atualização importante que inclui um número de novos recursos, bem como outras correções de bugs e melhorias de usabilidade orientado por comentários como visualizar o setembro de 2012:

* Suporte para o Microsoft Windows Server 2012 como o sistema operacional na nuvem.
* Suporte para o Azure localizado cache suporte a memcached clientes.
* Inclusão das bibliotecas de cliente do Apache Qpid JMS para tirar proveito de mensagens baseadas em AMQP do Azure.
* Um aprimorado Assistente para **Novo projeto** , com uma nova página no final que fornece aos usuários a capacidade de habilitar rapidamente vários recursos principais comuns do seu projeto: sessões conjunta, cache e depuração remota.
* Redução automática de instâncias de função como 1 quando em execução no emulador computação, para evitar conflitos de vinculação de porta entre as instâncias do servidor.

### <a name="september-28-2012"></a>28 de setembro de 2012

O plug-in do Azure para Eclipse - setembro de 2012 visualização foi lançado. Esta atualização de serviço inclui um número adicionais correções de bugs como visualizar de agosto de 2012, bem como algumas melhorias de usabilidade orientado por comentários nos recursos existentes:

* Suporte para o Microsoft Windows 8 e o Microsoft Windows Server 2012 como o sistema operacional de desenvolvimento, resolvendo problemas que anteriormente impede que o plug-in funcionando corretamente nesses sistemas operacionais.
* Suporte aprimorado para especificação de intervalos de porta de ponto de extremidade.
* Correções de bugs relacionadas a caminhos de arquivo que contém espaços.
* Melhorias de menu de contexto de função para obter acesso rápido a configurações específicas de função.
* Aprimoramentos secundárias no Assistente de **publicação para nuvem** e um número adicionais correções de bugs.

### <a name="august-28-2012"></a>28 de agosto de 2012

O plug-in do Azure para Eclipse - agosto de 2012 visualização foi lançado. Esta atualização de serviço inclui correções adicionais como visualizar de julho de 2012, bem como várias melhorias de usabilidade orientado por comentários para recursos existentes:

* Na caixa de diálogo Filtro de serviços de controle de acesso do Azure:
    * **Opção para incorporar o certificado de assinatura** no arquivo de guerra do seu aplicativo, para simplificar a implantação de nuvem.
    * **Opção para criar um certificado autoassinado** no filtro ACS interface do usuário. Para obter informações adicionais sobre o filtro de serviços de controle de acesso do Azure, veja [como autenticar usuários da Web com o Azure acesso controle serviço usando Eclipse].
* No Assistente de projeto de implantação do Azure (também se aplica a página de propriedades de configuração do servidor da função):
    * **Descoberta automática do local JDK** no seu computador (que você pode substituir se desejar).
    * **Detecção automática do tipo de servidor** quando você seleciona o diretório de instalação do servidor de aplicativo.

### <a name="july-15-2012"></a>15 de julho de 2012

O plug-in do Azure para Eclipse - julho de 2012 visualização, os endereços de um número de bugs prioridade mais altos encontrado e/ou relatado por usuários após o lançamento de junho de 2012, que foi lançado. Isso é apenas uma atualização de serviço, não há novos recursos estão contidos.

### <a name="june-7-2012"></a>7 de junho de 2012

Plug-in Azure para Eclipse - CTP de junho de 2012 foi lançado. Novos recursos incluem:

* **Assistente para novo projeto de implantação do Azure:** Permite que você selecione seu JDK, servidor de aplicativos Java e aplicativos Java diretamente no Assistente aprimorado da interface do usuário. Estão incluídas na lista de configurações de servidor de-de-prontos para escolher Tomcat 6, 7 Tomcat, GlassFish extensões 3, Jetty 7, Jetty 8, JBoss 6 e 7 JBoss (autônomo). Além disso, você pode personalizar a lista de configurações de servidor. Este aperfeiçoamento de interface do usuário é uma alternativa para arrastar e soltar arquivos compactados e copiando sobre scripts de inicialização, que estava anteriormente a abordagem principal. Esse método ainda funciona bem, mas provavelmente será usado somente para cenários mais avançados.
* **Página de propriedades de função de configuração do servidor:** Permite que você alternar facilmente JDKs, servidores de aplicativo Java e aplicativos associados a sua implantação após ter criado o projeto. Para obter mais informações, consulte [Propriedades de configuração do servidor].
* **&quot;Publicar em nuvem&quot; Assistente:** fornece uma maneira fácil de implantar o seu projeto no Azure diretamente do Eclipse, automatizar a remoção de pesado anteriormente manual de busca de credenciais, entrar no Portal de gerenciamento do Azure, ao carregar um pacote, etc. Para obter um exemplo de como implantar o seu projeto diretamente no Azure, consulte [Criando um aplicativo Hello World Azure no Eclipse].
* **Ferramentas azure:** Uma barra de ferramentas Azure agora está disponível em Eclipse contém botões que chamam os seguintes recursos:
    * ![][ic710879]**Executar no emulador Azure**: executa seu projeto no emulador.
    * ![][ic710880]**Redefinir Azure emulador**: redefine o emulador.
    * ![][ic710881]**Criar pacote de nuvem para Azure**: compila seu pacote para implantação.
    * ![][ic710876]**Novo projeto de implantação do Azure**: cria um novo projeto de implantação do Azure.
    * ![][ic710882]**Publicar Azure nuvem**: publica seu projeto no Azure.
    * ![][ic710883]**Cancelar publicação**: exclui sua implantação.
    * Muitas desses botões da barra de ferramentas Azure são usadas na [criação de um aplicativo Hello World Azure no Eclipse].
* **Azure bibliotecas para Java:** Agora disponível como parte do pacote único para bibliotecas do Azure para biblioteca Java no Eclipse, que acompanha a instalação do plug-in e contendo todas as dependências necessárias também. Basta adicionar uma referência à biblioteca em seu projeto Java e você não precisa baixar nada separadamente. Para obter mais informações, consulte [instalar o Kit de ferramentas do Azure para Eclipse].
* **Microsoft JDBC Driver 4.0 para SQL Server disponíveis durante a instalação do plug-in:** Durante a instalação do novo plug-in, a versão mais recente do Driver de JDBC da Microsoft para o SQL Server pode ser instalada.
* **Filtro controle de serviço do Access azure disponíveis durante a instalação do plug-in:** Este novo componente, incluído como uma biblioteca de Eclipse no Kit de ferramentas, permite que o seu aplicativo da web de Java perfeitamente tire proveito de autenticação de serviço de controle de acesso (ACS) Azure usando vários provedores de identidade, como o Google, Live.com e Yahoo!. Você não precisa escrever lógica de autenticação por conta própria, basta configurar algumas opções e deixe o filtro a fazer o trabalho pesado de permitindo que os usuários entrar usando o ACS. Você pode focalizar apenas escrever o código que fornece aos usuários acesso aos recursos com base em sua identidade, como retornado ao seu aplicativo pelo filtro de dentro do objeto de solicitação. Para um tutorial sobre como usar o filtro de ACS, veja [como autenticar usuários da Web com o Azure acesso controle serviço usando Eclipse].
* **Detecção automática de pré-requisito o Azure SDK 1.7:** Quando você cria um novo projeto de implantação do Azure, Azure SDK 1.7 serão baixadas automaticamente se ela já não estiver instalada.
* **Pontos de extremidade da instância:** Permite o acesso de ponto de extremidade de direta de porta para comunicação com instâncias de função de balanceamento de carga. Pontos de extremidade de instância podem ser adicionados por meio de pontos de extremidade da interface do usuário, disponível na página de [Propriedades de pontos de extremidade] . Isso ajuda a ativar depuração remota e diagnósticos JMX específico calcular instâncias em execução na nuvem em cenários com vários-implantações de instância. 
* **Componentes de interface do usuário:** Torna mais fácil para os usuários avançados configurar dependências de projeto entre funções Azure individuais do projeto e outros recursos externos como projetos de aplicativo Java; também torna mais fácil descrever sua lógica de implantação. Para obter mais informações, consulte [Propriedades de componentes].
* **Atualização automática de versões anteriores de projetos:** Quando você abre um espaço de trabalho que tem o Azure projeto criado com uma versão anterior do plug-in, os projetos antigos serão mostrados no Eclipse como fechada, porque versões anteriores de projetos não são compatíveis com a nova versão. Se você tentar abrir um desses projetos antigos, um Assistente de atualização será iniciado. Se você concordar com a atualização, um novo projeto, com **_Upgraded** acrescentado ao nome, será criado e atualizado automaticamente para trabalhar com a nova versão. Você pode renomear o novo projeto conforme necessário. Como parte da atualização, seu projeto original não será modificado (e permanecerão fechado).

### <a name="december-10-2011"></a>10 de dezembro de 2011

Plug-in Azure para Eclipse - dezembro de 2011 CTP foi lançado. Novos recursos incluem:

* **Afinidade de sessão (&quot;sessões conjunta&quot;) de suporte:** Ajudando a habilitar com informações de estado, agrupadas aplicativos Java com apenas uma única caixa de seleção. Para obter mais informações, consulte [Afinidade de sessão].
* **Previamente feitas exemplos de script de inicialização:** Para os mais populares Java servidores (Tomcat, Jetty, JBoss, GlassFish), que você pode apenas copiar/colar do diretório de exemplos do seu projeto no seu script de inicialização.
* **Saída de inicialização de emulador em tempo real:** Agora você pode assistir a execução de todas as etapas do seu script de inicialização em uma janela de console dedicado, mostrando o andamento e falhas no script como ele é executado pelo Azure.
* **Java.exe automático, simplificada monitoramento:** Que forçar uma reciclagem de função quando java.exe parar a execução, usando um script leve, preexistente incluído automaticamente na sua implantação.
* **Aplicativo Java remoto depuração de configuração da interface do usuário:** Permite que você facilmente ative a depurador remoto do Eclipse acessar seu aplicativo Java em execução no emulador ou na nuvem Azure, para que possa percorrer e depurar seu código Java em tempo real. Para obter mais informações, consulte [Depuração de aplicativos do Azure no Eclipse].
* **Configuração do recurso de armazenamento local da interface do usuário:** Assim você não precisa configurar recursos locais manipulando XML diretamente. Este recurso também permite que você acesse ao caminho do arquivo eficaz do recurso local depois de ser implantado por meio de uma variável de ambiente que você pode fazer referência diretamente do seu script de inicialização. Para obter mais informações, consulte [Propriedades de armazenamento Local].
* **Configuração de variável de ambiente da interface do usuário:** Portanto você já não precisa definir variáveis de ambiente por meio de edição manual da configuração do XML. Para obter mais informações, consulte [Propriedades de variáveis de ambiente].
* **Driver JDBC do SQL Azure:** Obtém instalado via o plug-in como uma biblioteca Eclipse totalmente integrada, permitindo mais fácil programar SQL Azure. 
* **Menu de contexto de rápido acesso a configuração de função de interface do usuário**: basta com o botão direito na pasta de função e clique em **Propriedades**.
* **Ícones de pasta de projeto e função do Azure personalizada:** Para melhor visibilidade e facilitar a navegação em seu espaço de trabalho e o projeto.

## <a name="see-also"></a>Consulte também ##

Para obter mais informações sobre os kits de ferramentas do Azure para IDEs Java, consulte os links a seguir:

- [Kit de ferramentas de Azure para Eclipse]
  - [Instalando o Kit de ferramentas do Azure para Eclipse]
  - [Criar um aplicativo de Web Olá mundo para Azure no Eclipse]
  - *Novidades no Azure Kit de ferramentas para Eclipse (neste artigo)*
- [Kit de ferramentas Azure para IntelliJ]
  - [Instalando o Kit de ferramentas do Azure para IntelliJ]
  - [Criar um aplicativo de Web Olá mundo para Azure no IntelliJ]
  - [Novidades no Azure Kit de ferramentas para IntelliJ]

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center].

<!-- URL List -->

[Kit de ferramentas de Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de ferramentas Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Criar um aplicativo de Web Olá mundo para Azure no Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Criar um aplicativo de Web Olá mundo para Azure no IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalando o Kit de ferramentas do Azure para Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalando o Kit de ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[What's New in the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novidades no Azure Kit de ferramentas para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Central de desenvolvedores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547

[Página da web de sistemas de azul para o OpenJDK Zulu]: http://go.microsoft.com/fwlink/?LinkId=402457
[Pontos de extremidade do serviço Azure]: http://go.microsoft.com/fwlink/?LinkID=699526
[Lista de contas de armazenamento do Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[Propriedades de componentes]: http://go.microsoft.com/fwlink/?LinkID=699525#components_properties
[Criando um aplicativo Hello World para Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Depuração de aplicativos do Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Implantando grandes implantações]: http://go.microsoft.com/fwlink/?LinkID=699536
[Propriedades de pontos de extremidade]: http://go.microsoft.com/fwlink/?LinkID=699525#endpoints_properties
[Propriedades de variáveis de ambiente]: http://go.microsoft.com/fwlink/?LinkID=699525#environment_variables_properties
[Plug-in HDInsight de ferramentas para Eclipse]: ./hdinsight/hdinsight-apache-spark-eclipse-tool-plugin.md
[Como autenticar usuários da Web com o serviço de controle de acesso Azure usando Eclipse]: http://go.microsoft.com/fwlink/?LinkID=264703
[Como usar SSL transferindo]: http://go.microsoft.com/fwlink/?LinkID=699545
[Instalando o Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propriedades de armazenamento local]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties
[API do cliente do Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=280397
[Propriedades de configuração do servidor]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Afinidade de sessão]: http://go.microsoft.com/fwlink/?LinkID=699548
[Transferindo SSL]: http://go.microsoft.com/fwlink/?LinkID=699549
[Para criar uma nova conta de armazenamento]: http://go.microsoft.com/fwlink/?LinkID=699528#create_new
[Máquina virtual e tamanhos de serviço de nuvem do Azure]: http://go.microsoft.com/fwlink/?LinkId=466520

<!-- IMG List -->

[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710877]: ./media/azure-toolkit-for-eclipse-whats-new/ic710877.png
[ic710879]: ./media/azure-toolkit-for-eclipse-whats-new/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-whats-new/ic710880.png
[ic710881]: ./media/azure-toolkit-for-eclipse-whats-new/ic710881.png
[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710882]: ./media/azure-toolkit-for-eclipse-whats-new/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-whats-new/ic710883.png
