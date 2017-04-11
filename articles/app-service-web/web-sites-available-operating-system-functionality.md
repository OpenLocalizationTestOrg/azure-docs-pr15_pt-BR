<properties 
    pageTitle="Funcionalidade do sistema operacional no serviço de aplicativo do Azure" 
    description="Saiba mais sobre a funcionalidade do sistema operacional disponível para aplicativos web, o aplicativo móvel back-ends e aplicativos de API no serviço de aplicativo do Azure" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="mollybos"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/01/2016" 
    ms.author="cephalin"/>

# <a name="operating-system-functionality-on-azure-app-service"></a>Funcionalidade do sistema operacional no serviço de aplicativo do Azure #

Este artigo descreve a funcionalidade comum de sistema operacional de linha de base que está disponível para todos os aplicativos em execução no [Serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Essa funcionalidade inclui arquivo, rede e acesso ao registro e logs de diagnóstico e eventos. 

<a id="tiers"></a>
## <a name="app-service-plan-tiers"></a>Níveis de plano de serviço de aplicativo

Serviço de aplicativo é executado aplicativos cliente em um ambiente de hospedagem de vários locatário. Aplicativos implantados nos níveis **livre** e **compartilhado** executados em processos nas máquinas virtuais compartilhadas, enquanto aplicativos implantados no **padrão** e níveis de **Premium** executados em máquinas virtuais dedicado especificamente para os aplicativos associados a um único cliente.

Como o serviço de aplicativo oferece suporte a uma experiência perfeita dimensionamento entre níveis diferentes, a configuração de segurança imposta para os aplicativos de serviço de aplicativo permanece o mesmo. Isso garante que aplicativos não inesperadamente se comportam de maneira diferente, falhando de maneiras inesperadas, quando o plano de serviço de aplicativo alterna de um nível para outro.

<a id="developmentframeworks"></a>
## <a name="development-frameworks"></a>Estruturas de desenvolvimento

Níveis de preços de serviço de aplicativo controlam a quantidade de recursos de computação (CPU, armazenamento em disco, memória e saída de rede) disponíveis para os aplicativos. No entanto, a amplitude de funcionalidade de framework disponível para aplicativos permanece o mesmo, independentemente dos níveis de escala.

Serviço de aplicativo oferece suporte a uma variedade de estruturas de desenvolvimento, incluindo ASP.NET, ASP clássico, Node, PHP e Python - todas elas executam como extensões dentro do IIS. Para simplificar e normalizar configuração de segurança, aplicativos de serviço de aplicativo normalmente executam as diversas estruturas de desenvolvimento com suas configurações padrão. Uma abordagem Configurando aplicativos poderia ter sido personalizar a área de superfície de API e funcionalidade para cada estrutura de desenvolvimento individuais. Serviço de aplicativo em vez disso leva uma abordagem mais genérica, permitindo que uma linha de base comuns de funcionalidade do sistema operacional, independentemente de estrutura de desenvolvimento de um aplicativo.

As seções a seguir resumem os tipos gerais de funcionalidade do sistema operacional disponível para aplicativos de serviço de aplicativo.

<a id="FileAccess"></a>
##<a name="file-access"></a>Acesso a arquivos

Existem várias unidades dentro de serviço de aplicativo, incluindo unidades locais e unidades de rede.

<a id="LocalDrives"></a>
### <a name="local-drives"></a>Unidades locais

Em seu núcleo, o serviço de aplicativo é um serviço em execução na parte superior de infraestrutura do Azure PaaS (plataforma como um serviço). Como resultado, as unidades locais "anexadas" a uma máquina virtual são os mesmos tipos de unidade disponíveis para qualquer função de trabalho em execução no Azure. Isso inclui uma unidade do sistema operacional (a unidade D:\), uma unidade de aplicativo que contém Azure pacote cspkg arquivos usados exclusivamente pelo serviço de aplicativo (e inacessíveis aos clientes) e uma unidade de "usuário" (a unidade C:\), cujo tamanho varia dependendo do tamanho da máquina virtual.

<a id="NetworkDrives"></a>
### <a name="network-drives-aka-unc-shares"></a>Unidades de rede (também conhecidos como UNC compartilha)

Um dos aspectos exclusivos do serviço de aplicativo que facilita a manutenção e a implantação do aplicativo simples é que todo o conteúdo de usuário é armazenado em um conjunto de compartilhamentos UNC. Este modelo perfeitamente mapeia para o padrão comum de armazenamento de conteúdo usado pelo web local ambientes com vários servidores de balanceamento de carga de hospedagem. 

No aplicativo de serviço, há número de compartilhamentos UNC criados em cada data center. Uma porcentagem do conteúdo do usuário para todos os clientes em cada data center está alocada para cada compartilhamento UNC. Além disso, todo o conteúdo para uma única assinatura do cliente sempre será colocada na mesma UNC de arquivo de compartilhar. 

Observe que devido a como funcionam os serviços de nuvem, a máquina virtual específico responsável por hospedar um compartilhamento UNC serão alterados ao longo do tempo. Ele certamente que compartilhamentos UNC serão montados por máquinas virtuais diferentes conforme eles são inseridos para cima e para baixo durante o curso normal das operações de nuvem. Por esse motivo, aplicativos nunca devem fazer suposições embutida que as informações de máquina em um caminho de arquivo UNC permanecerá estáveis ao longo do tempo. Em vez disso, eles devem usar o conveniente *Falso* caminho absoluto **D:\home\site** que fornece o serviço de aplicativo. Esse caminho absoluto falso fornece um método portátil, independente de aplicativo e usuário para se referir ao aplicativo por conta própria. Usando **D:\home\site**, um pode transferir arquivos compartilhados de um aplicativo para o aplicativo sem configurar um novo caminho absoluto para cada transferência.

<a id="TypesOfFileAccess"></a>
### <a name="types-of-file-access-granted-to-an-app"></a>Tipos de arquivo acesso concedido a um aplicativo

Cada assinatura do cliente tem uma estrutura de diretório reservado em um compartilhamento UNC específico em um data center. Um cliente pode ter vários aplicativos criados em um data center específico, para que todos os diretórios pertencentes a uma assinatura de cliente único criados no mesmo UNC compartilhar. O compartilhamento pode incluir diretórios como aqueles para conteúdo, erro e logs de diagnóstico e versões anteriores do aplicativo criado pelo controle da fonte. Conforme esperado, diretórios de aplicativo de um cliente estão disponíveis para o acesso de leitura e gravação no tempo de execução pelo código do aplicativo do aplicativo.

Nas unidades locais anexadas à máquina virtual que executa um aplicativo, o serviço de aplicativo reserva um bloco de espaço na unidade C:\ para armazenamento local temporário do aplicativo específico. Embora um aplicativo tenha acesso completo de leitura/gravação ao seu próprio armazenamento local temporário, que armazenamento realmente não devem ser usado diretamente pelo código do aplicativo. Em vez disso, o objetivo é fornecer armazenamento de arquivos temporários para IIS e web estruturas de aplicativo. Serviço de aplicativo também limita a quantidade de armazenamento temporário local disponível para cada aplicativo para impedir que aplicativos individuais consumindo quantidades excessivas de armazenamento de arquivo local.

Dois exemplos de como o serviço de aplicativo usa o armazenamento local temporário são o diretório para os arquivos temporários do ASP.NET e o diretório para IIS arquivos compactados. O sistema de compilação do ASP.NET usa o diretório "Temporary ASP.NET Files" como um local de cache de compilação temporária. IIS usa o diretório "IIS arquivos compactados temporários" para armazenar a saída de resposta compactada. Esses dois tipos de arquivo uso (bem como outras pessoas) são remapeados no aplicativo de serviço para armazenamento de local temporário-app. Este novo mapeamento garante que funcionalidade continua conforme esperado.

Cada aplicativo no serviço de aplicativo é executado como uma identidade de processo aleatório trabalhador de baixo privilégio exclusivo chamada a "aplicativo identidade do pool", ainda mais descrita aqui: [http://www.iis.net/learn/manage/configuring-security/application-pool-identities](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Código de aplicativo usa esta identidade para obter acesso somente leitura básico a unidade do sistema operacional (a unidade D:\). Isso significa que o código do aplicativo pode listar estruturas de diretório comuns e ler arquivos comuns na unidade do sistema operacional. Embora isso pode parecer ser um nível razoavelmente amplo de acesso, as mesmas pastas e arquivos são acessíveis quando você provisiona uma função de trabalho em um Azure serviço hospedado e ler o conteúdo da unidade. 

<a name="multipleinstances"></a>
### <a name="file-access-across-multiple-instances"></a>Acesso a arquivos em várias instâncias

O diretório inicial contém conteúdo de um aplicativo e código do aplicativo pode escrever a ele. Se um aplicativo é executado em várias instâncias, o diretório inicial é compartilhado entre todas as instâncias para que todas as instâncias ver mesmo diretório. Portanto, por exemplo, se um aplicativo salvar arquivos carregados para a pasta base, esses arquivos são imediatamente disponíveis para todas as instâncias. 

<a id="NetworkAccess"></a>
## <a name="network-access"></a>Acesso à rede
Código do aplicativo pode usar TCP/IP e protocolos UDP com base para tornar a saída de rede conexões com pontos de extremidade acessíveis do Internet que expõem serviços externos. Aplicativos podem usar esses mesmos protocolos para se conectar aos serviços dentro do Azure & #151; por exemplo, estabelecendo conexões HTTPS banco de dados SQL.

Há também um recurso limitado para aplicativos estabelecer uma conexão de loopback local e ter um aplicativo escutam em que soquete loopback local. Este recurso existe principalmente para permitir que aplicativos que escutam em sockets loopback local como parte de sua funcionalidade. Observe que cada aplicativo vê uma conexão de autoretorno "particular"; aplicativo "A" não é possível ouvir um soquete loopback local estabelecido pelo aplicativo "B".

Também há suporte para pipes nomeados como um mecanismo de comunicação entre processos (IPC) entre os diferentes processos que coletivamente executar um aplicativo. Por exemplo, o módulo FastCGI do IIS depende de pipes nomeados para coordenar os processos individuais que executar PHP páginas.


<a id="Code"></a>
## <a name="code-execution-processes-and-memory"></a>Execução de código, processos e memória
Conforme mencionado anteriormente, aplicativos executados dentro de processos do operador de baixo privilégio usando uma identidade do pool de aplicativos aleatório. Código do aplicativo tem acesso ao espaço de memória associado com o processo de trabalho, bem como qualquer processos filhos que podem ser gerados por processos CGI ou outros aplicativos. No entanto, um aplicativo não consegue acessar os dados de outro aplicativo ou memória, mesmo se estiver na mesma máquina virtual.

Aplicativos podem executar scripts ou páginas escritas com estruturas de desenvolvimento de web com suporte. Serviço de aplicativo não configurar qualquer web framework para modos mais restritos. Por exemplo, aplicativos ASP.NET em execução no serviço de aplicativo executado em confiança "total" em vez de um modo de confiança mais restrito. Estruturas de Web, incluindo tanto clássico ASP e ASP.NET, podem chamar componentes COM no processo (mas não out dos componentes do processo COM) como o ADO (ActiveX Data Objects) que são registrados por padrão no sistema operacional Windows.

Aplicativos podem gerar e executar código aleatório. É permitido para um aplicativo fazer coisas como geração um shell do comando ou executar um script do PowerShell. No entanto, apesar de código aleatório e processos podem ser gerados a partir de um aplicativo, scripts e programas executáveis são ainda restritas aos privilégios concedidos ao pool de aplicativos pai. Por exemplo, um aplicativo pode gerar um executável que faça uma chamada de saída HTTP, mas que mesmo executável não pode tentar desvincular o endereço IP de uma máquina virtual do seu NIC. Fazer uma chamada de saída de rede é permitido ao código de baixo privilégio, mas tentar reconfigurar as configurações de rede em uma máquina virtual requer privilégios administrativos.


<a id="Diagnostics"></a>
## <a name="diagnostics-logs-and-events"></a>Logs de diagnóstico e eventos
Informações de log são outro conjunto de dados que alguns aplicativos tentarem acessar. Os tipos de informações de log disponíveis para código em execução no serviço de aplicativo inclui diagnósticos e log informações geradas por um aplicativo que também é facilmente acessível para o aplicativo. 

Por exemplo, logs de W3C HTTP gerados por um aplicativo ativo estão disponíveis em um diretório de log no local de compartilhamento da rede criado para o aplicativo, ou disponível no armazenamento de blob se um cliente tiver configurado log W3C ao armazenamento. A última opção permite grandes quantidades de logs devem ser coletadas sem o risco de ultrapassando os limites de armazenamento de arquivo associados a um compartilhamento de rede.

Em um sentido similar, informações de diagnóstico em tempo real de aplicativos .NET também podem ser registradas usando o rastreamento do .NET e infraestrutura de diagnóstico, com opções para gravar as informações de rastreamento para compartilhamento de rede do aplicativo, ou como alternativa para um local de armazenamento de blob.

Áreas de diagnóstico log e rastreamento que não está disponível para aplicativos são eventos do Windows ETW e comuns logs de eventos do Windows (por exemplo, sistema, aplicativo e segurança logs de eventos). Como informações de rastreamento ETW potencialmente podem ser visto todo o computador (com ACLs direita), acesso de leitura e gravação para eventos ETW são bloqueadas. Os desenvolvedores podem Observe que chamadas API para ler e gravar ETW eventos e logs de eventos do Windows comuns parecer funcionar, mas isso ocorre porque o serviço de aplicativo "falsas são" as chamadas para que elas apareçam tenha êxito. Na verdade, o código do aplicativo não tem acesso a esses dados de evento.

<a id="RegistryAccess"></a>
## <a name="registry-access"></a>Acesso ao registro
Aplicativos têm acesso somente leitura a muito (embora nem todos) do registro da máquina virtual estão sendo executados em. Na prática, isso significa que as chaves do registro que permitem acesso somente leitura ao grupo local usuários estão acessíveis por aplicativos. Uma área do registro que não é suportado atualmente para ler ou gravar acesso é o HKEY\_atual\_seção do usuário.

Acesso de gravação para o registro está bloqueado, incluindo o acesso a qualquer chaves do registro por usuário. Da perspectiva do aplicativo, acesso de gravação para o registro deve nunca ser considerado no ambiente do Azure desde que aplicativos podem (e façam) obtenha migrados máquinas virtuais diferentes. O armazenamento gravável apenas persistente que pode ser dependentes por um aplicativo é a estrutura de diretório de conteúdo de por aplicativo armazenada em compartilhamentos UNC de serviço de aplicativo. 

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 
 
