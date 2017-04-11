<properties
    pageTitle="Comparação de serviço de aplicativo, máquinas virtuais, estrutura de serviço e os serviços de nuvem Azure | Microsoft Azure"
    description="Saiba como escolher entre o serviço de aplicativo do Azure, máquinas virtuais, estrutura de serviço e os serviços de nuvem para hospedar aplicativos da web."
    services="app-service\web, virtual-machines, cloud-services"
    documentationCenter=""
    authors="tdykstra"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/07/2016"
    ms.author="tdykstra"/>

# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Comparação de serviço de aplicativo, máquinas virtuais, tecidos de serviço e serviços de nuvem do Azure

## <a name="overview"></a>Visão geral

Azure oferece várias maneiras para sites do host: [Serviço de aplicativo do Azure][], [máquinas virtuais][], [Estrutura de serviço][]e [Os serviços de nuvem][]. Este artigo ajuda você a entender as opções e fazer a escolha certa para o aplicativo web.

Serviço de aplicativo do Azure é a melhor opção para a maioria dos aplicativos web. Implantação e gerenciamento são integrados a plataforma, sites podem ser dimensionados rapidamente para lidar com altas cargas de tráfego e o Gerenciador de tráfego e balanceamento de carga interna fornecer alta disponibilidade. Você pode mover sites existentes para o serviço de aplicativo do Azure facilmente com uma [ferramenta de migração on-line](https://www.migratetoazure.net/), use um aplicativo de código-fonte aberto da Galeria de aplicativo Web ou criar um novo site usando as ferramentas de sua preferência e framework. O recurso de [WebJobs][] torna mais fácil adicionar processamento para seu aplicativo web do serviço de aplicativo de trabalho de plano de fundo.

Estrutura de serviço é uma boa opção se você estiver criando um novo aplicativo ou regravação em um aplicativo existente para usar uma arquitetura de microservice. Aplicativos, que são executadas em um pool compartilhado de máquinas, podem começar pequenos e crescer a escala de massa com centenas ou milhares de máquinas conforme necessário. Serviços de estado facilitam a maneira consistente e confiável armazenar o estado de aplicativo e serviço tecidos gerencia automaticamente partição de serviço, dimensionamento e disponibilidade para você.  Serviço tecidos também suporta WebAPI com Interface aberta da Web para .NET (OWIN) e núcleo do ASP.NET.  Em comparação com o serviço de aplicativo, serviço tecidos que também esteja fornece mais controle sobre ou acesso direto, a infraestrutura subjacente. Você pode remoto em seus servidores ou configurar tarefas de inicialização do servidor. Serviços de nuvem é semelhante a estrutura de serviço no grau de controle versus facilidade de uso, mas agora é um serviço herdado e tecidos do serviço é recomendado para desenvolvimento de novos.

Se você tiver um aplicativo existente que exijam modificações substanciais seja executado em serviço de aplicativo ou serviço tecidos, você poderia escolher máquinas virtuais para simplificar a migração para a nuvem. Entretanto, corretamente configurando, protegendo e manutenção VMs requer muito mais tempo e conhecimento de TI em comparação com o serviço de aplicativo do Azure e estrutura de serviço. Se você estiver considerando máquinas virtuais do Azure, verifique se que você levar em consideração o esforço de manutenção contínua necessário para patch, atualizar e gerenciar seu ambiente de máquina virtual. Azure máquinas virtuais é infraestrutura-como um serviço (IaaS), enquanto o serviço de aplicativo e serviço tecidos são plataforma-como um serviço (Paas). 

## <a name="features"></a>Comparação de recursos

A tabela a seguir compara os recursos do serviço de aplicativo, os serviços de nuvem, máquinas virtuais e estrutura de serviço para ajudar você a tomar a melhor opção. Para obter informações atuais sobre o SLA para cada opção, consulte [Contratos de nível de serviço do Azure](/support/legal/sla/).

Recurso|Serviço de aplicativo (web apps)|Serviços de nuvem (funções da web)|Máquinas virtuais|Estrutura de serviço|Anotações
---|---|---|---|---|---
Implantação de quase instantânea|X|||X|Implantando um aplicativo ou uma atualização do aplicativo a um serviço de nuvem ou criando uma máquina virtual, leva vários minutos pelo menos; implantar um aplicativo para um aplicativo web leva segundos.
Crescerão para máquinas maiores sem reimplantação|X|||X|
Instâncias de servidor de Web compartilham conteúdo e configuração, o que significa que não é necessário reimplantar ou reconfigure ao dimensionar.|X|||X|
Vários ambientes de implantação (produção e preparação)|X|X||X|Serviço tecidos permite ter vários ambientes para seus aplicativos ou para implantar versões diferentes do seu aplicativo lado a lado.
Gerenciamento de atualização de sistema operacional automático|X|X|||As atualizações automáticas do sistema operacional estão planejadas para uma estrutura futura do serviço de lançamento.
Migrando de plataforma perfeita (mover facilmente entre 32 e 64 bits)|X|X|||
Implantar código com GITO, FTP|X||X||
Implantar código com implantação da Web|X||X||Serviços de nuvem é compatível com o uso de implantação da Web para implantar atualizações em instâncias de função individuais. No entanto, você não pode usá-lo para implantação inicial de uma função, e se você usar a implantação da Web para obter uma atualização você precisa implantar separadamente para cada instância de uma função. Várias instâncias são necessárias para poder para o SLA de serviço de nuvem para ambientes de produção.
Suporte de WebMatrix|X||X||
Acesso a serviços como o barramento de serviço, armazenamento, banco de dados SQL|X|X|X|X|
Host na web ou camada de serviços da web de uma arquitetura de vários nível|X|X|X|X|
Intermediária de host de uma arquitetura de vários nível|X|X|X|X|Aplicativos do serviço de aplicativo web facilmente podem hospedar uma intermediária da API REST e o recurso de [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) pode hospedar o processamento de trabalhos em segundo plano. Você pode executar WebJobs em um site dedicado para atingir escalabilidade independente para a camada. O recurso de [API aplicativos](../app-service-api/app-service-api-apps-why-best-platform.md) de visualização fornece ainda mais recursos para hospedar serviços REST.
Suporte de MySQL-como um serviço integrado|X|X|X||Serviços de nuvem podem integrar MySQL-como um serviço por meio de ofertas do ClearDB, mas não como parte do fluxo de trabalho Portal Azure.
Suporte do ASP.NET, clássico ASP, Node, PHP, Python|X|X|X|X|Serviço tecidos suporta a criação de uma web front-end usando [ASP.NET 5](../service-fabric/service-fabric-add-a-web-frontend.md) ou você pode implantar qualquer tipo de aplicativo (Node, Java etc.) como um [Convidado executável](../service-fabric/service-fabric-deploy-existing-app.md).
Dimensionar a várias instâncias sem reimplantação|X|X|X|X|Máquinas virtuais pode ser dimensionada para várias instâncias, mas os serviços em execução neles devem ser escritos para lidar com essa escala-out. Você precisa configurar um balanceador de carga para rotear solicitações nos computadores do e criar um grupo de afinidade para impedir a reinicialização simultânea de todas as instâncias devido a falhas de hardware ou manutenção.
Suporte para SSL|X|X|X|X|Serviço de aplicativo web Apps, SSL para nomes de domínio personalizado tem suporte apenas para o modo Basic e padrão. Para obter informações sobre como usar SSL com aplicativos web, consulte [Configurar um certificado SSL para um site do Azure](../app-service-web/web-sites-configure-ssl-certificate.md).
Integração do Visual Studio|X|X|X|X|
Depuração remota|X|X|X||
Implantar código com TFS|X|X|X|X|
Isolamento de rede com [Rede Virtual do Azure](/services/virtual-network/)|X|X|X|X|Consulte também [integração de rede Virtual do Azure sites](/blog/2014/09/15/azure-websites-virtual-network-integration/)
Suporte para o [Gerenciador de tráfego Azure](/services/traffic-manager/)|X|X|X|X|
Monitoramento de ponto de extremidade integrado|X|X|X||
Acesso de desktop remoto para servidores||X|X|X|
Instalar qualquer MSI personalizado||X|X|X|Serviço tecidos permite hospedar qualquer arquivo executável como um [Convidado executável](../service-fabric/service-fabric-deploy-existing-app.md) ou você pode instalar qualquer aplicativo VMs.
Capacidade de definir/executar tarefas de inicialização||X|X|X|
Pode ouvir eventos ETW||X|X|X|

## <a name="scenarios"></a>Cenários e recomendações

Aqui estão alguns cenários comuns do aplicativo com recomendações como para quais Azure hospedagem opção pode ser mais apropriada para cada um.

- [Preciso de um web front-end com back-end de banco de dados e processamento de plano de fundo para executar aplicativos de negócios integrados com ativos de local.](#onprem)
- [Preciso de uma maneira confiável para hospedar meu site corporativo que aumenta bem e ofertas globais alcançar.](#corp)
- [Eu tenho um aplicativo de IIS6 em execução no Windows Server 2003.](#iis6)
- [Sou um proprietário de pequenas empresas e preciso de uma maneira barata hospedar meu site, mas com o futuro crescimento em mente.](#smallbusiness)
- [Sou um web ou designer gráfico e quero projetar e criar sites da web para meus clientes.](#designer)
- [Eu estou migrando meu aplicativo de vários nível com uma web front-end para a nuvem.](#multitier)
- [Meu aplicativo depende Windows altamente personalizados ou ambientes Linux e eu deseja movê-lo na nuvem.](#custom)
- [Meu site usa software de fonte aberta e quero hospedá-lo no Azure.](#oss)
- [Eu tenho um aplicativo de linha de negócios que precisa se conectar à rede corporativa.](#lob)
- [Eu quiser hospedar uma API REST ou serviço da web para clientes móveis.](#mobile)


### <a id="onprem"></a>Preciso de um web front-end com back-end de banco de dados e processamento de plano de fundo para executar aplicativos de negócios integrados com ativos de local.

Serviço de aplicativo Azure é uma ótima solução para aplicativos de negócios complexos. Ele permite que você desenvolver aplicativos que dimensionar automaticamente em uma plataforma de carga equilibrada, são protegidos com o Active Directory e se conectar aos seus recursos locais. Ele torna gerenciando esses aplicativos fácil através de um portal extraordinária e APIs e permite que você percepção como os clientes estão usando-las com ferramentas de visão de aplicativo. O recurso de [Webjobs][] permite executar processos de plano de fundo e tarefas como parte de sua camada de web, enquanto a conectividade híbrido e recursos VNET tornam mais fácil conectar-se para recursos locais. Serviço de aplicativo Azure fornece SLA do três 9 para aplicativos web e permite que você:

* Execute seus aplicativos confiavelmente em uma plataforma de nuvem AutoCorreção, correção automática.
* Dimensionar automaticamente em uma rede global de data centers.
* Fazer backup e restauração de recuperação de dados.
* Ser compatíveis com ISO, SOC2 e PCI.
* Integrar com o Active Directory

### <a id="corp"></a>Preciso de uma maneira confiável para hospedar meu site corporativo que aumenta bem e ofertas globais alcançar.

Serviço de aplicativo do Azure é uma ótima solução para hospedar sites corporativos. Ele permite que os aplicativos de web dimensionar rapidamente e facilmente para atender a demanda em uma rede global de data centers. Ele oferece alcance local, tolerância e gerenciamento de tráfego inteligente. Tudo em uma plataforma que fornece ferramentas de gerenciamento de classe internacional, permitindo que você percepção integridade do site e o tráfego de site rapidamente e facilmente. Serviço de aplicativo Azure fornece SLA do três 9 para aplicativos web e permite que você:

* Execute seus sites confiável em uma plataforma de nuvem AutoCorreção, correção automática.
* Dimensionar automaticamente em uma rede global de data centers.
* Fazer backup e restauração para recuperação de dados.
* Gerenciar logs e tráfego com ferramentas integradas.
* Ser compatíveis com ISO, SOC2 e PCI.
* Integrar com o Active Directory

### <a id="iis6"></a>Eu tenho um aplicativo de IIS6 em execução no Windows Server 2003.

Serviço de aplicativo do Azure torna mais fácil evitar os custos de infraestrutura associados Migrando aplicativos IIS6 antigos. Microsoft criou [migração fácil de usar ferramentas e orientações de migração detalhado](https://www.movemetowebsites.net/) que permitem que você verificar a compatibilidade e identificar todas as alterações que precisam ser feitas. Integração com o Visual Studio, TFS e ferramentas comuns de CMS torna mais fácil implantar aplicativos IIS6 diretamente para a nuvem. Uma vez implantado, o Portal de Azur fornece ferramentas de gerenciamento robustos que permitem que você dimensionar para baixo para gerenciar custos e atender demanda conforme necessário. Com a ferramenta de migração, você pode:

* Rapidamente e facilmente migre seu aplicativo de web do Windows Server 2003 herdado para a nuvem.
* Optar por deixar sua anexado SQL banco de dados no local para criar um aplicativo híbrido.
* Mover seu banco de dados do SQL junto com seu aplicativo herdado automaticamente.

### <a id="smallbusiness"></a>Sou um proprietário de pequenas empresas e preciso de uma maneira barata hospedar meu site, mas com o futuro crescimento em mente.

Serviço de aplicativo do Azure é uma ótima solução para esse cenário, porque você pode começar a usá-lo gratuitamente e, em seguida, adicionar mais recursos quando precisar. Cada aplicativo web gratuito vem com um domínio fornecido pelo Azure (*your_company*. azurewebsites.net), e a plataforma inclui ferramentas integradas de implantação e gerenciamento, além de uma galeria de aplicativo que tornam mais fácil começar. Há muitos outros serviços e opções de escala que permitem que o site a evoluir com demanda de aumento de usuário. Com o serviço de aplicativo do Azure, você pode:

- Começar com a camada livre e, em seguida, redimensionar conforme necessário.
- Use a Galeria de aplicativo para configurar aplicativos populares da web, como WordPress rapidamente.
- Adicione recursos e serviços Azure adicionais ao seu aplicativo conforme necessário.
- Proteger seu aplicativo web com HTTPS.

### <a id="designer"></a>Sou um web ou designer gráfico e quero projetar e construir sites para meus clientes

Para os desenvolvedores da web e designers, o serviço de aplicativo do Azure integra-se facilmente uma variedade de estruturas e ferramentas, inclui suporte de implantação para gito e FTP e oferece integração com ferramentas e serviços como o Visual Studio e o banco de dados SQL. Com o serviço de aplicativo, você pode:

- Usar ferramentas de linha de comando para [tarefas automatizadas][scripting].
- Trabalhar com idiomas populares como [.net][dotnet], [PHP][], [Node][nodejs]e [Python][].
- Selecione três níveis diferentes de dimensionamento para dimensionando até capacidades muito altas.
- Integrar com outros serviços do Azure, como o [Banco de dados SQL][sqldatabase], [Barramento de serviço] [ servicebus] e [armazenamento][]ou ofertas de parceiros do [Repositório do Azure][azurestore], como MySQL e MongoDB.
- Integre com ferramentas como o Visual Studio, gito, WebMatrix, WebDeploy, TFS e FTP.

### <a id="multitier"></a>Eu estou migrando meu aplicativo de vários nível com uma web front-end para a nuvem

Se você estiver executando um aplicativo de vários nível, como um servidor web que se conecta a um banco de dados, o serviço de aplicativo do Azure é uma boa opção que oferece a integração com o Azure SQL Database. E você pode usar o recurso de WebJobs para processos de back-end em execução.

Escolha serviço tecidos para um ou mais dos seus níveis se você precisa de mais controle sobre o ambiente de servidor, como a capacidade de remoto em seu servidor ou configurar tarefas de inicialização do servidor.

Escolha máquinas virtuais para um ou mais dos seus níveis se você quiser usar sua própria imagem de máquina ou executar software de servidor ou serviços que você não pode configurar em estrutura de serviço.

### <a id="custom"></a>Meu aplicativo depende Windows altamente personalizados ou ambientes Linux e eu deseja movê-lo na nuvem.

Se seu aplicativo requer complexa instalação ou configuração de software e o sistema operacional, máquinas virtuais é provavelmente a melhor solução. Com máquinas virtuais, você pode:

- Use a Galeria de máquina Virtual para começar com um sistema operacional, como o Windows ou Linux e personalizá-lo para seus requisitos do aplicativo.
- Criar e carregar uma imagem personalizada de um servidor local existente para executar em uma máquina virtual no Azure.

### <a id="oss"></a>Meu site usa software de fonte aberta e quero hospedá-lo no Azure

Se sua estrutura de código-fonte aberto é suportada no aplicativo de serviço, os idiomas e estruturas necessitadas pelo seu aplicativo são configuradas automaticamente para você. Serviço de aplicativo permite que você:

- Usar muitos Abrir origem idiomas populares, como o [.NET][dotnet], [PHP][], [Node][nodejs]e [Python][].
- Configure o WordPress, Drupal, Umbraco, DNN e muitos outros aplicativos da web de terceiros.
- Migrar um aplicativo existente ou crie um novo da Galeria de aplicativo.

Se não há suporte para a sua estrutura de código-fonte aberto no aplicativo de serviço, você pode executá-lo em uma das outra web Azure opções de hospedagem. Com máquinas virtuais, instalar e configurar o software na imagem da máquina, que pode ser Windows ou baseado em Linux.

### <a id="lob"></a>Eu tenho um aplicativo de linha de negócios que precisa se conectar à rede corporativa

Se você quiser criar um aplicativo de linha de negócios, seu site pode exigir acesso direto aos serviços ou dados na rede corporativa. Isso é possível no serviço de aplicativo, estrutura de serviço e máquinas virtuais usando o [serviço de rede Virtual do Azure](/services/virtual-network/). Serviço de aplicativo, você pode usar o [recurso de integração de VNET](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), que permite que seus aplicativos Azure executem como se fossem na sua rede corporativa.

### <a id="mobile"></a>Eu quiser hospedar uma API REST ou serviço da web para clientes móveis

Serviços web baseados em HTTP permitem que você dar suporte a uma ampla variedade de clientes, incluindo os clientes móveis. Estruturas como API Web do ASP.NET integram com o Visual Studio para tornar mais fácil criar e consumir serviços REST.  Esses serviços são expostos a partir de um ponto de extremidade da web, portanto, é possível usar qualquer técnica no Azure de hospedagem na web para dar suporte a esse cenário. No entanto, o serviço de aplicativo é uma excelente opção para APIs REST de hospedagem. Com o serviço de aplicativo, você pode:

- Crie rapidamente um [aplicativo móvel](../app-service-mobile/app-service-mobile-value-prop.md) ou [aplicativo de API](../app-service-api/app-service-api-apps-why-best-platform.md) para hospedar o serviço da web HTTP em um dos data centers distribuídos globalmente do Azure.
- Migrar serviços existentes ou criar novos.
- Atingir SLA para disponibilidade com uma única instância ou dimensionada para várias máquinas dedicadas.
- Use o site publicado para fornecer APIs REST para quaisquer clientes HTTP, incluindo os clientes móveis.

> [AZURE.NOTE]
> Se você deseja começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta, vá para <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, onde você pode imediatamente criar um aplicativo de curta duração starter no serviço de aplicativo do Azure gratuitamente. Nenhum cartão de crédito necessários, sem compromissos.

## <a id="nextsteps"></a>Próximas etapas

Para obter mais informações sobre os três opções de hospedagem da web, consulte [Introdução ao Azure](../fundamentals-introduction-to-azure.md).

Para começar a usar as opções que você escolhe para seu aplicativo, consulte os seguintes recursos:

* [Serviço de aplicativo do Azure](/documentation/services/app-service/)
* [Serviços de nuvem Azure](/documentation/services/cloud-services/)
* [Azure máquinas virtuais](/documentation/services/virtual-machines/)
* [Estrutura de serviço](/documentation/services/service-fabric)

<!-- URL List -->

[Serviço de aplicativo do Azure]: /services/app-service/
[Serviços de nuvem]: http://go.microsoft.com/fwlink/?LinkId=306052
[Máquinas virtuais]: http://go.microsoft.com/fwlink/?LinkID=306053
[Estrutura de serviço]: /services/service-fabric
[ClearDB]: http://www.cleardb.com/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
[azurestore]: http://www.windowsazure.com/gallery/store/
[scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
[dotnet]: http://www.windowsazure.com/develop/net/
[nodejs]: http://www.windowsazure.com/develop/nodejs/
[PHP]: http://www.windowsazure.com/develop/php/
[Python]: http://www.windowsazure.com/develop/python/
[servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
[sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
[Armazenamento]: http://www.windowsazure.com/documentation/services/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
