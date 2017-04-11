<properties
    pageTitle="Guia de segurança do Azure armazenamento | Microsoft Azure"
    description="Detalhes dos muitos métodos de proteção de armazenamento do Azure, incluindo mas não limitado a RBAC, criptografia de serviço de armazenamento, criptografia do lado do cliente, SMB 3.0 e criptografia de disco do Azure."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="robinsh"/>

#<a name="azure-storage-security-guide"></a>Guia de segurança do Azure armazenamento

##<a name="overview"></a>Visão geral

Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança que permitem que os desenvolvedores criem aplicativos seguros juntos. A conta de armazenamento em si pode ser protegida usando o controle de acesso baseado em função e Azure Active Directory. Dados podem ser protegidos em trânsito entre um aplicativo e Azure usando [Criptografia do lado do cliente](storage-client-side-encryption.md), HTTPS ou SMB 3.0. Dados podem ser definidos automaticamente criptografados quando gravados ao armazenamento do Azure usando [Criptografia de serviço de armazenamento (SSE)](storage-service-encryption.md). Sistema operacional e dados discos usados por máquinas virtuais podem ser definidos para ser criptografados usando [Criptografia de disco do Azure](../security/azure-security-disk-encryption.md). Acesso delegado para os objetos de dados em armazenamento Azure pode ser concedido usando [Assinaturas de acesso compartilhado](storage-dotnet-shared-access-signature-part-1.md).

Este artigo fornecerá uma visão geral de cada um desses recursos de segurança que podem ser usados com o armazenamento do Azure. Links são fornecidos para artigos que lhe dará detalhes de cada recurso isso você pode facilmente fazer mais investigação em cada tópico.

Aqui estão os tópicos a serem abordados neste artigo:

-   [Segurança do plano de gerenciamento](#management-plane-security) – protegendo a sua conta de armazenamento

    O plano de gerenciamento consiste nos recursos usados para gerenciar sua conta de armazenamento. Nesta seção, falaremos sobre o modelo de implantação do Gerenciador de recursos do Azure e como usar o controle de acesso baseado em função (RBAC) para controlar o acesso às suas contas de armazenamento. Também falaremos sobre como gerenciar suas chaves de conta de armazenamento e como gerá-los novamente.

-   [Segurança de plano de dados](#data-plane-security) – protegendo o acesso aos seus dados

    Nesta seção, veremos permitir o acesso aos objetos dados reais em sua conta de armazenamento, como blobs, arquivos, filas e tabelas, usando compartilhados assinaturas de acesso e políticas de acesso armazenados. Abordaremos SAS de nível de serviço e SAS de nível de conta. Nós também verá como limitar o acesso a um endereço IP específico (ou intervalo de endereços IP), como limitar o protocolo usado para HTTPS e como revogar uma assinatura de acesso compartilhado sem aguardando para que ela expire.

-   [Criptografia em trânsito](#encryption-in-transit)

    Esta seção descreve como proteger os dados quando você transfere em ou reduzir o armazenamento do Azure. Falaremos sobre o uso recomendado de HTTPS e à criptografia usada pelo SMB 3.0 para compartilhamentos de arquivos do Azure. Nós também terá uma olhada no lado do cliente criptografia, que permite que você para criptografar os dados antes de serem transferido para o armazenamento em um aplicativo cliente e para descriptografar os dados após transferido sem armazenamento.

-   [Criptografia inativos](#encryption-at-rest)

    Falaremos sobre criptografia de serviço de armazenamento (SSE), e como você pode ativá-lo para uma conta de armazenamento, resultando em seu bloco bolhas, blobs de página e acrescentar blobs sendo criptografados automaticamente quando gravados ao armazenamento do Azure. Também veremos como você pode usar a criptografia de disco do Azure e explore os diferenças básicas e casos de criptografia de disco versus SSE versus criptografia do lado do cliente. Brevemente veremos conformidade FIPS para computadores do governo dos EUA.

-   Usando [A análise de armazenamento](#storage-analytics) para auditoria de acesso de armazenamento do Azure

    Esta seção discute como encontrar informações em logs de análise de armazenamento para uma solicitação. Vamos dar uma olhada a análise de armazenamento real dados de log e veja como distinguir se uma solicitação é feita com a chave da conta de armazenamento, com uma assinatura de acesso compartilhado, ou anonimamente e se ele foi bem-sucedida ou falha.

-   [Ativando clientes baseadas em navegador usando CORS](#Cross-Origin-Resource-Sharing-CORS)

    Esta seção fala sobre como permitir (CORS) de compartilhamento de recursos entre origens. Falaremos sobre acesso entre domínios e como lidar com-la com os recursos CORS internos do armazenamento do Azure.

##<a name="management-plane-security"></a>Segurança do plano de gerenciamento

O plano de gerenciamento consiste em operações que afetam a conta de armazenamento em si. Por exemplo, você pode criar ou excluir uma conta de armazenamento, obter uma lista das contas de armazenamento em uma assinatura, recuperar as chaves de conta de armazenamento ou gerar as chaves de conta de armazenamento.

Quando você cria uma nova conta de armazenamento, você pode selecionar um modelo de implantação do clássico ou o Gerenciador de recursos. O modelo clássico de criação de recursos no Azure só permitirá acesso tudo ou nada para a assinatura e, por sua vez, a conta de armazenamento.

Este guia se concentra no modelo, Gerenciador de recursos que é a maneira recomendada para a criação de contas de armazenamento. Com as contas de armazenamento do Gerenciador de recursos, em vez de fornecendo acesso à assinatura inteira, você pode controlar o acesso em um nível mais finito para o plano de gerenciamento usando o controle de acesso baseado em função (RBAC).

###<a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Como proteger sua conta de armazenamento com controle de acesso baseado em função (RBAC)

Vamos falar sobre qual RBAC e como você pode usá-lo. Cada assinatura Azure tem um Active Directory do Azure. Usuários, grupos e aplicativos daquele diretório podem concessão de acesso para gerenciar recursos na assinatura do Azure que usam o modelo de implantação do Gerenciador de recursos. Isso é conhecido como controle de acesso baseado em função (RBAC). Para gerenciar esse acesso, você pode usar o [portal do Azure](https://portal.azure.com/), as [Ferramentas de CLI do Azure](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)ou as [APIs REST do Azure armazenamento recurso provedor](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Com o modelo do Gerenciador de recursos, você coloca a conta de armazenamento em um recurso grupo e controlar o acesso para o plano de gerenciamento dessa conta de armazenamento específico usando o Active Directory do Azure. Por exemplo, você pode dar usuários específicos a capacidade de acessar as chaves de conta de armazenamento, enquanto outros usuários podem exibir informações sobre a conta de armazenamento, mas não podem acessar as chaves de conta de armazenamento.

####<a name="granting-access"></a>Concedendo acesso

Acesso atribuindo a função RBAC adequada para usuários, grupos e aplicativos, no escopo direita. Para conceder acesso à assinatura inteira, você atribuir uma função no nível de assinatura. Você pode conceder acesso a todos os recursos em um grupo de recursos, concedendo permissões ao grupo de recursos em si. Você também pode atribuir funções específicas para recursos específicos, como contas de armazenamento.

Aqui estão os principais pontos que você precisa saber sobre o uso de RBAC para acessar as operações de gerenciamento de uma conta de armazenamento do Azure:

-   Quando você atribui o acesso, você basicamente atribuir uma função para a conta que você deseja ter acesso. Você pode controlar o acesso às operações usadas para gerenciar essa conta de armazenamento, mas não os objetos de dados na conta. Por exemplo, você pode conceder permissão para recuperar as propriedades da conta de armazenamento (como redundância), mas não a um contêiner ou dados dentro de um contêiner de armazenamento de Blob.

-   Para que alguém tem permissão para acessar os objetos de dados na conta de armazenamento, você pode dar permissão para ler as chaves de conta de armazenamento e esse usuário, em seguida, pode usar essas chaves para acessar os blobs, filas, tabelas e arquivos.

-   Funções podem ser atribuídas a uma conta de usuário específico, um grupo de usuários, ou para um aplicativo específico.

-   Cada função tem uma lista de ações e não. Por exemplo, a função Colaborador de máquina Virtual tem uma ação de "listKeys" que permite que as teclas de conta de armazenamento para ser lido. O colaborador tem "Não ações" como atualizando o acesso de usuários no Active Directory.

-   Funções para armazenamento incluem (mas não está limitadas a) o seguinte:

    -   Proprietário – eles podem gerenciar tudo, inclusive acesso.

    -   Colaborador – ele pode fazer qualquer coisa o proprietário pode exceto atribuir acesso. Alguém com esta função pode visualizar e gerar as chaves de conta de armazenamento. Com as teclas de conta de armazenamento, eles podem acessar os objetos de dados.

    -   Leitor – eles podem exibir informações sobre a conta de armazenamento, exceto segredos. Por exemplo, se você atribuir uma função com permissões de leitor na conta de armazenamento para alguém, eles podem exibir as propriedades da conta de armazenamento, mas não podem fazer alterações nas propriedades ou exibir as teclas de conta de armazenamento.

    -   Colaborador de conta de armazenamento – eles podem gerenciar a conta de armazenamento – eles podem ler os grupos de recursos e recursos, a assinatura e criar e gerenciar implantações de grupo de recursos de assinatura. Eles também podem acessar as chaves de conta de armazenamento, que por sua vez, significa que eles possam acessar o plano de dados.

    -   Administrador de acesso do usuário – eles podem gerenciar o acesso do usuário para a conta de armazenamento. Por exemplo, ele podem conceder acesso de leitor a um usuário específico.

    -   Colaborador de máquina virtual – eles podem gerenciar máquinas virtuais, mas não a conta de armazenamento à qual eles estão conectados. Esta função pode listar as chaves de conta de armazenamento, o que significa que o usuário a quem você atribuir esta função pode atualizar o plano de dados.

        Ordem de um usuário criar uma máquina virtual, eles precisam ser capazes de criar o arquivo VHD correspondente em uma conta de armazenamento. Para fazer isso, eles devem ser capaz de recuperar a chave da conta de armazenamento e passe para a API criando a máquina virtual. Portanto, eles devem ter essa permissão para que eles podem listam as teclas de conta de armazenamento.

- A capacidade de definir funções personalizadas é um recurso que permite que você redigir um conjunto de ações de uma lista de ações disponíveis que podem ser executadas em recursos Azure.

- O usuário deve ser configurada no seu Azure Active Directory antes de atribuir uma função a eles.

- Você pode criar um relatório de quem concedido/revogado que tipo de acesso de/para quem e nos quais escopo usando o PowerShell ou da CLI do Azure.

####<a name="resources"></a>Recursos

-   [Controle de acesso baseado em função do Active Directory do Azure](../active-directory/role-based-access-control-configure.md)

    Este artigo explica o controle de acesso baseado em função do Azure Active Directory e como ele funciona.

-   [RBAC: Integrado funções](../active-directory/role-based-access-built-in-roles.md)

    Este artigo detalha todas as funções internas disponíveis no RBAC.

-   [Noções básicas sobre o Gerenciador de recursos de implantação e implantação clássica](../resource-manager-deployment-model.md)

    Este artigo explica a implantação do Gerenciador de recursos e modelos de implantação clássico e explica os benefícios de usar os grupos de recursos e Gerenciador de recursos

-   [Computação Azure, de rede e provedores de armazenamento sob o Azure Gerenciador de recursos](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

    Este artigo explica como a computação do Azure, de rede e provedores de armazenamento funcionam sob o modelo do Gerenciador de recursos.

-   [Gerenciando o controle de acesso baseado em função com a API REST](../active-directory/role-based-access-control-manage-access-rest.md)

    Este artigo mostra como usar a API REST para gerenciar RBAC.

-   [Referência de API REST do provedor de recursos de armazenamento do Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx)

    Esta é a referência para as APIs que você pode usar para gerenciar sua conta de armazenamento por programação.

-   [Guia do desenvolvedor para auth com a API do Gerenciador de recursos do Azure](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)

    Este artigo mostra como autenticar usando as APIs do Gerenciador de recursos.

-   [Controle de acesso baseado em função do Microsoft Azure do Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Este é um link para um vídeo no canal 9 da conferência Ignite MS 2015. Nesta sessão, falar sobre acessar recursos de gerenciamento e geração de relatórios no Azure e explorar práticas recomendadas sobre como proteger o acesso às assinaturas Azure usando o Active Directory do Azure.

###<a name="managing-your-storage-account-keys"></a>Gerenciando suas chaves de conta de armazenamento

Chaves de conta de armazenamento são cadeias de caracteres de 512 bits criadas por Azure que, juntamente com o nome da conta de armazenamento, pode ser usado para acessar os objetos de dados armazenados na conta de armazenamento, por exemplo, blobs, entidades dentro de uma tabela, mensagens da fila e arquivos em um compartilhamento de arquivos do Azure. Controlando o acesso ao armazenamento acesso à conta teclas controles para o plano de dados para essa conta de armazenamento.

Cada conta de armazenamento tem duas chaves chamadas de "Chave 1" e "2" no [portal do Azure](http://portal.azure.com/) e chave no cmdlets do PowerShell. Esses podem ser gerado novamente manualmente usando um dos vários métodos, incluindo, mas não limitado a usando o [portal do Azure](https://portal.azure.com/), PowerShell, CLI Azure ou programaticamente usando a biblioteca de cliente de armazenamento do .NET ou a API REST de serviços de armazenamento de Azure.

Há vários motivos para gerar novamente suas chaves de conta de armazenamento.

-   Você pode gerá-los novamente com regularidade por razões de segurança.

-   Você poderia gerar novamente suas chaves de conta de armazenamento se alguém gerenciado para acessar ilegalmente em um aplicativo e recuperar a chave que foi codificado ou salvo em um arquivo de configuração, dando-lhes acesso total a sua conta de armazenamento.

-   Outro caso para a nova geração de chave é se sua equipe estiver usando um aplicativo do Gerenciador de armazenamento que retém a chave da conta de armazenamento e um dos membros da equipe deixa. O aplicativo continuará a funcionar, dando-lhes acesso à sua conta de armazenamento depois que estiverem desaparece. Isso é realmente o principal motivo que criaram assinaturas de acesso do nível de conta compartilhadas – você pode usar um nível de conta SAS em vez de armazenar as teclas de acesso em um arquivo de configuração.

####<a name="key-regeneration-plan"></a>Plano de nova geração de chave

Você não deseja recriar apenas a chave que você está usando sem algum planejamento. Se você fizer isso, você poderia cortado todo o acesso a essa conta de armazenamento, que pode causar interrupção maior. É por isso existem duas chaves. Você deve gerar uma tecla por vez.

Antes de gerar suas chaves, certifique-se de que você tem uma lista de todos os aplicativos que dependem da conta de armazenamento, bem como quaisquer outros serviços que você está usando no Azure. Por exemplo, se você estiver usando os serviços de mídia do Azure que dependem de sua conta de armazenamento, você deve sincronize novamente as teclas de acesso com o seu serviço de mídia após você gerar novamente a chave. Se você estiver usando qualquer aplicativos como um Gerenciador de armazenamento, você precisará fornecer as novas chaves a esses aplicativos também. Observe que se você tiver VMs cujos arquivos VHD são armazenados na conta de armazenamento, elas não serão afetadas por gerar as chaves de conta de armazenamento.

Você pode gerar suas chaves no portal do Azure. Depois de teclas são gerados novamente eles podem levar até 10 minutos para ser sincronizados em serviços de armazenamento.

Quando você estiver pronto, aqui é o processo geral detalha como você deve alterar sua chave. Nesse caso, pressupõe-se que você está usando atualmente chave 1 e você vai mudar tudo para usar chave 2 em vez disso.

1.  Gerar chave 2 para garantir que ele é seguro. Você pode fazer isso no portal do Azure.

2.  Em todos os aplicativos onde a chave de armazenamento está armazenada, altere a chave de armazenamento para usar o novo valor da chave 2. Testar e publicar o aplicativo.

3.  Afinal dos aplicativos e serviços estão acima e em execução com êxito, gerar chave 1. Isso garante que qualquer pessoa para quem você não expressamente concedeu a nova chave deixarão de ter acesso à conta de armazenamento.

Se você estiver usando no momento chave 2, você pode usar o mesmo processo, mas reverter os nomes de chave.

Você pode migrar sobre alguns dias, alterando cada aplicativo para usar a nova chave e publicá-lo. Depois de terminar de todos eles, você deve voltar e gerar a chave antiga para que ele não funciona mais.

Outra opção é colocar a chave da conta de armazenamento em um [Cofre de chave do Azure](https://azure.microsoft.com/services/key-vault/) como um segredo e ter seus aplicativos recuperar a chave de lá. Em seguida, quando você gerar a tecla e atualize o Cofre de chave do Azure, os aplicativos não precisarão ser redistribuído porque eles selecionará a nova chave do Azure chave cofre automaticamente. Observe que você pode ter o aplicativo ler a chave de cada vez que você precisar, ou você pode cache na memória e se ele falhar quando usá-lo, recuperar a chave novamente do Azure chave cofre.

Usar Azure chave cofre também adiciona outro nível de segurança para as chaves de armazenamento. Se você usar esse método, você nunca terá que spsecuritytokenservicemanager.cs chave armazenamento em um arquivo de configuração, que remove essa via de alguém obtendo acesso às chaves sem permissão específica.

Outra vantagem de usar Azure chave cofre é que você também pode controlar o acesso às suas chaves usando o Active Directory do Azure. Isso significa que você pode conceder acesso a muitos aplicativos que precisam recuperar as chaves do Cofre de chave do Azure e souber que outros aplicativos não poderão acessar as chaves sem conceder-lhes permissão especificamente.

Observação: é recomendável usar apenas uma das chaves em todos os seus aplicativos ao mesmo tempo. Se você usar a chave 1 em alguns locais e chave 2 em outros, não será capaz de girar suas chaves sem algum aplicativo perder o acesso.

####<a name="resources"></a>Recursos

-   [Sobre contas de armazenamento do Azure](storage-create-storage-account.md#regenerate-storage-access-keys)

    Este artigo apresenta uma visão geral das contas de armazenamento e discute a visualização, copiando e gerar as teclas de acesso de armazenamento.

-   [Referência de API REST do provedor de recursos de armazenamento do Azure](https://msdn.microsoft.com/library/mt163683.aspx)

    Este artigo contém links para artigos específicos sobre Recuperando chaves de conta de armazenamento e gerar as chaves de conta de armazenamento para uma conta do Azure usando a API REST. Observação: Este é para as contas de armazenamento do Gerenciador de recursos.

-   [Operações em contas de armazenamento](https://msdn.microsoft.com/library/ee460790.aspx)

    Este artigo Gerenciador de serviço de armazenamento restante API Reference contém links para artigos específicos sobre recuperação e recriando as chaves de conta de armazenamento usando a API REST. Observação: Este é para as contas de armazenamento clássico.

-   [Diga adeus para gerenciamento de chaves – gerencie o acesso aos dados de armazenamento do Azure usando o Azure AD](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)

    Este artigo mostra como usar o Active Directory para controlar o acesso às suas chaves de armazenamento do Azure em Cofre de chave do Azure. Ele também mostra como usar um trabalho de automação do Azure para gerar as chaves em uma base por hora.

##<a name="data-plane-security"></a>Segurança de plano de dados

Segurança de plano de dados se refere aos métodos usados para proteger os objetos de dados armazenados no armazenamento do Azure – os blobs, filas, tabelas e arquivos. Temos visto métodos para criptografar os dados e a segurança durante o trânsito dos dados, mas como você faz sobre permissão de acesso aos objetos?

Existem basicamente dois métodos para controlar o acesso aos objetos de dados em si. O primeiro é controlando o acesso às chaves de conta de armazenamento e o segundo é usar assinaturas de acesso compartilhado para conceder acesso aos objetos de dados específicos para um período específico de tempo.

Uma exceção observar é que você pode permitir acesso público à sua blobs definindo o nível de acesso para o recipiente que contém os blobs adequadamente. Se você configurar o acesso para um contêiner de Blob ou contêiner, ele permitirá público acesso de leitura para os blobs no contêiner. Isso significa que qualquer pessoa com uma URL apontando para um blob no contêiner pode abri-lo em um navegador sem usar uma assinatura de acesso compartilhado ou ter as chaves de conta de armazenamento.

###<a name="storage-account-keys"></a>Teclas de conta de armazenamento

Chaves de conta de armazenamento são cadeias de caracteres de 512 bits criadas por Azure que, juntamente com o nome da conta de armazenamento, pode ser usado para acessar os objetos de dados armazenados na conta de armazenamento.

Por exemplo, você pode ler blobs, filas de gravação, criar tabelas e modificar arquivos. Muitas dessas ações podem ser executadas por meio do portal do Azure, ou usando um dos muitos aplicativos do Gerenciador de armazenamento. Você também pode escrever código para usar a API REST ou uma das bibliotecas de cliente do armazenamento para executar essas operações.

Conforme discutido na seção sobre a [Segurança do plano de gerenciamento](#management-plane-security), acesso às chaves armazenamento para uma conta de armazenamento clássico pode ser concedido concedendo acesso total à assinatura Azure. Acesso às chaves armazenamento para uma conta de armazenamento usando o modelo do Gerenciador de recursos do Azure pode ser controlado por meio de controle de acesso baseado em função (RBAC).

###<a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Como acesso aos objetos em sua conta usando assinaturas de acesso compartilhado e armazenados políticas de acesso de representante

Uma assinatura de acesso compartilhado é uma cadeia de caracteres que contém um token de segurança que pode ser anexado a um URI que permite acesso de representante ao objetos de armazenamento e especificar restrições como as permissões e o intervalo de data/hora de acesso.

Você pode conceder acesso a blobs, contêineres, mensagens da fila, arquivos e tabelas. Com tabelas, você pode realmente conceder permissão para acessar um intervalo de entidades na tabela especificando os intervalos de chave partição e a linha à qual você deseja que o usuário tenha acesso. Por exemplo, se você tiver dados armazenados com uma chave de partição de estado geográfica, você pode conceder a alguém acesso a apenas os dados para Califórnia.

Em outro exemplo, você pode dar um token SAS que permite que ele gravar entradas para uma fila de um aplicativo da web e dar um trabalhador aplicativo de função um token SAS para receber mensagens da fila e processá-los. Ou você pode dar um cliente um token SAS eles podem usar para carregar imagens em um contêiner no armazenamento de Blob e dar uma permissão de aplicativo web ler essas imagens. Em ambos os casos, não há uma separação de preocupações – cada aplicativo pode ser fornecido apenas o acesso que eles precisam para realizar sua tarefa. Isso é possível por meio do uso compartilhado assinaturas de acesso.

####<a name="why-you-want-to-use-shared-access-signatures"></a>Por que você deseja usar assinaturas de acesso compartilhado

Por que você deseja usar um em vez de apenas fornecer sua chave de conta de armazenamento, que é muito mais fácil? Fazendo check-out sua chave da conta de armazenamento é como as chaves do Reino seu armazenamento de compartilhamento. Ele concede acesso completo. Alguém poderia usar suas chaves e carregue suas bibliotecas de música toda a sua conta de armazenamento. Eles também poderiam substituir seus arquivos com versões infectados ou roubar seus dados. Dar acesso ilimitado à sua conta de armazenamento é algo que não devem ser seguidas levemente.

Com assinaturas de acesso compartilhado, você pode dar um cliente apenas as permissões necessárias para uma quantidade limitada de tempo. Por exemplo, se alguém está carregando um blob à sua conta, você pode conceder a eles acesso de gravação para apenas bastante tempo carregar o blob (dependendo do tamanho do blob, é claro). E se você mudar de ideia, você pode revogar o acesso.

Além disso, você pode especificar que solicitações feitas usando uma SAS são restritas a um determinado endereço IP ou intervalo de endereços IP externo ao Azure. Você também pode exigir que são feitas solicitações usando um protocolo específico (HTTPS ou HTTP/HTTPS). Isso significa que se você deseja permitir o tráfego HTTPS somente, você pode definir o protocolo necessário para HTTPS somente e o tráfego HTTP será bloqueado.

####<a name="definition-of-a-shared-access-signature"></a>Definição de uma assinatura de acesso compartilhado

Uma assinatura de acesso compartilhado é um conjunto de parâmetros de consulta acrescentados à URL apontando para o recurso

que fornece informações sobre o acesso permitido e o período de tempo para os quais o acesso é permitido. Aqui está um exemplo; esse URI fornece acesso de leitura a um blob de cinco minutos. Observe que SAS parâmetros de consulta deve ser codificada como URL, como % 3A para pontos (:) ou % 20 para um espaço.

    http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
    ?sv=2015-04-05 (storage service version)
    &st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
    &se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
    &sr=b (resource is a blob)
    &sp=r (read access)
    &sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
    &spr=https (only allow HTTPS requests)
    &sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)

####<a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>Como a assinatura de acesso compartilhado é autenticada pelo serviço de armazenamento do Azure

Quando o serviço de armazenamento recebe a solicitação, ele usa os parâmetros de consulta de entrada e cria uma assinatura usando o mesmo método como o programa de chamada. Ele compara as duas assinaturas. Se eles concordarem, o serviço de armazenamento pode verificar a versão de serviço de armazenamento para certificar-se de que é válido, verifique se a data e hora atuais estão dentro da janela especificada, verifique se o acesso solicitado corresponde à solicitação feita, etc.

Por exemplo, com nossa URL acima, se a URL foi apontando para um arquivo em vez de um blob, essa solicitação falhará porque ela especifica que a assinatura de acesso compartilhado é para um blob. Se o comando restante sendo chamado atualizar um blob, ele falhará porque a assinatura de acesso compartilhado Especifica que somente o acesso de leitura é permitido.

####<a name="types-of-shared-access-signatures"></a>Tipos de assinaturas de acesso compartilhado

-   Uma SAS de nível de serviço pode ser usada para acessar recursos específicos em uma conta de armazenamento. Alguns exemplos estão recuperando uma lista de blobs em um contêiner, baixando um blob, atualizando uma entidade em uma tabela, adicionando mensagens a uma fila ou carregar um arquivo para um compartilhamento de arquivo.

-   SAS um nível de conta pode ser usado para acessar qualquer coisa que uma SAS de nível de serviço pode ser usada para. Além disso, ele pode dar opções para os recursos que não são permitidos com uma SAS de nível de serviço, como a capacidade de criar contêineres, tabelas, filas e compartilhamentos de arquivos. Você também pode especificar acesso a vários serviços ao mesmo tempo. Por exemplo, você pode conceder a alguém acesso aos blobs e arquivos em sua conta de armazenamento.

####<a name="creating-an-sas-uri"></a>Criando um URI SAS

1.  Você pode criar um URI ad-hoc sob demanda, definindo a todos os parâmetros de consulta de cada vez.

    Isso é muito flexível, mas se você tiver um conjunto lógico de parâmetros que são semelhantes de cada vez, usando uma política de acesso armazenados é melhor.

2.  Você pode criar uma política de acesso armazenados para um recipiente inteiro, compartilhamento de arquivo, tabela ou fila. Você pode usar isso como base para os URIs de SAS que você criar. Permissões com base em políticas de acesso armazenado podem ser facilmente revogadas. Você pode ter até 5 políticas definidas em cada contêiner, fila, tabela ou compartilhamento de arquivo.

    Por exemplo, se você fosse ter muitas pessoas ler os blobs em um contêiner específico, você pode criar uma política de acesso armazenados que diz "conceder acesso de leitura" e quaisquer outras configurações que serão iguais cada vez. Você pode criar uma SAS URI usando as configurações da política de acesso armazenados e especificando a data/hora de expiração. A vantagem disso é que você não precisa especificar todos os parâmetros de consulta de cada vez.

####<a name="revocation"></a>Revogação

Digamos que seu SAS foi comprometida, ou você deseja alterá-lo devido a requisitos de conformidade regulamentar ou segurança corporativa. Como você revogar o acesso a um recurso usando esse SAS? Depende de como você criou o URI SAS.

Se você estiver usando ad-hoc URIS, você tem três opções. Você pode emitir tokens de SAS com políticas de expiração curto e simplesmente aguarde as associações de segurança expirar. Você pode renomear ou excluir o recurso (presumindo que o token foi com escopo de um único objeto). Você pode alterar as teclas de conta de armazenamento. Essa última opção pode ter um grande impacto, dependendo de quantos serviços estão usando essa conta de armazenamento e provavelmente não é algo que você deseja fazer sem algum planejamento.

Se você estiver usando uma SAS derivada de uma política de acesso armazenados, você pode remover o acesso pelo Revogando a política de acesso armazenados – você pode alterá-lo apenas para que ele já tiver expirado, ou você pode removê-lo completamente. Isso tem efeito imediato e invalida todas as associações de segurança criadas usando essa política de acesso armazenados. Atualizando ou removendo a política de acesso armazenado podem pessoas impacto acessando o contêiner específico, compartilhamento de arquivos, tabela ou fila via SAS, mas se os clientes são gravados para que eles solicitar novas associações de segurança quando antigo fica inválido, isso funciona bem.

Porque usando uma SAS derivada de uma política de acesso armazenados lhe dá a capacidade de revogar essa SAS imediatamente, é a prática recomendada sempre usar políticas de acesso armazenados quando possível.

####<a name="resources"></a>Recursos

Para obter informações mais detalhadas sobre como usar assinaturas de acesso compartilhado armazenado políticas e acesso, completa com exemplos, consulte os seguintes artigos:

-   Estes são os artigos de referência.

    -   [Serviço SAS](https://msdn.microsoft.com/library/dn140256.aspx)

        Este artigo fornece exemplos do uso de uma SAS de nível de serviço com blobs, mensagens da fila, intervalos de tabela e arquivos.

    -   [Construindo um serviço SAS](https://msdn.microsoft.com/library/dn140255.aspx)

    -   [Criando uma conta SAS](https://msdn.microsoft.com/library/mt584140.aspx)

-   Estas são tutoriais para usar a biblioteca de cliente .NET para criar assinaturas de acesso compartilhado e armazenados políticas de acesso.

    -   [Usando assinaturas de acesso compartilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md)

    -   [Compartilhado assinaturas do Access, parte 2: Criar e usar um com o serviço de Blob](storage-dotnet-shared-access-signature-part-2.md)

        Este artigo inclui uma explicação do modelo SAS, exemplos de assinaturas de acesso compartilhado, e recomendações para a prática recomendada usam Associações de. Também discutidas é a revogação da permissão concedida.

-   Limitando o acesso por endereço IP (IP ACLs)

    -   [O que é um ponto de extremidade lista de controle de acesso (ACLs)?](../virtual-network/virtual-networks-acl.md)

    -   [Construindo um serviço SAS](https://msdn.microsoft.com/library/azure/dn140255.aspx)

        Este é o artigo de referência Associações de nível de serviço; de Ele inclui um exemplo de ACLing de IP.

    -   [Criando uma conta SAS](https://msdn.microsoft.com/library/azure/mt584140.aspx)

        Este é o artigo de referência Associações de nível de conta; de Ele inclui um exemplo de ACLing de IP.

-   Autenticação

    -    [Autenticação para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)

-   Compartilhado Introdução Tutorial de assinaturas de acesso

    -   [SAS Introdução Tutorial](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

##<a name="encryption-in-transit"></a>Criptografia em trânsito

###<a name="transport-level-encryption--using-https"></a>Criptografia de nível de transporte – usando HTTPS

Outra etapa que você deve tomar para garantir a segurança dos seus dados de armazenamento do Azure é criptografar os dados entre o cliente e o armazenamento do Azure. A primeira recomendação é sempre usar o protocolo [HTTPS](https://en.wikipedia.org/wiki/HTTPS) , que garante a comunicação segura na Internet pública.

Você sempre deve usar HTTPS quando chamar as APIs REST ou acessar objetos em armazenamento. Além disso, **Assinaturas de acesso compartilhado**, que pode ser usado para acesso de representante ao objetos de armazenamento do Azure, inclua uma opção para especificar que somente o protocolo HTTPS pode ser usado ao usar assinaturas de acesso compartilhado, garantindo que qualquer pessoa enviando links com tokens SAS usará o protocolo correto.

####<a name="resources"></a>Recursos

-   [Habilitar HTTPS para um aplicativo no serviço de aplicativo do Azure](../app-service-web/web-sites-configure-ssl-certificate.md)

    Este artigo mostra como habilitar o HTTPS para um aplicativo Web do Azure.

###<a name="using-encryption-during-transit-with-azure-file-shares"></a>Usando criptografia durante a transferência com compartilhamentos de arquivos do Azure

Armazenamento de arquivos Azure suporta HTTPS ao usar a API REST, mas é mais comumente usados como um compartilhamento de arquivo SMB anexados a uma máquina virtual. SMB 2.1 não oferece suporte para criptografia, portanto conexões somente são permitidas dentro da mesma região no Azure. No entanto, SMB 3.0 oferece suporte à criptografia e pode ser usado com o Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10, permitindo entre região acessar e até mesmo acesso na área de trabalho.

Observe que enquanto Azure compartilhamentos de arquivos podem ser usados com Unix, o cliente Linux SMB ainda não dá suporte a criptografia, para que o access só é permitido dentro de uma região Azure. Suporte a criptografia para Linux é o roteiro de desenvolvedores do Linux responsáveis por funcionalidade SMB. Quando adicionam criptografia, você terá a mesma capacidade para acessar um compartilhamento de arquivo do Azure no Linux como você faz para Windows.

####<a name="resources"></a>Recursos

-   [Como usar o armazenamento de arquivos do Azure com Linux](storage-how-to-use-files-linux.md)

    Este artigo mostra como montar um compartilhamento de arquivo do Azure em um sistema Linux e carregamento/download de arquivos.

-   [Introdução ao armazenamento de arquivos do Azure no Windows](storage-dotnet-how-to-use-files.md)

    Este artigo fornece uma visão geral de compartilhamentos de arquivos do Azure e como montar e usá-los usando o PowerShell e .NET.

-   [Armazenamento de arquivos Azure Inside](https://azure.microsoft.com/blog/inside-azure-file-storage/)

    Este artigo apresenta a disponibilidade geral do armazenamento de arquivos do Azure e fornece detalhes técnicos sobre a criptografia SMB 3.0.

###<a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Usando a criptografia do lado do cliente para proteger os dados que você enviar ao armazenamento

Outra opção que ajuda a garantir que seus dados estão seguros enquanto sendo transferidos entre um aplicativo cliente e armazenamento é a criptografia do lado do cliente. Os dados são criptografados antes de serem transferidos para o armazenamento do Azure. Ao recuperar dados de armazenamento do Azure, os dados é descriptografar após for recebida no lado do cliente. Apesar dos dados são criptografados passando pela conexão, é recomendável que você também pode usar HTTPS, pois possui verificações de integridade de dados integradas que ajuda a reduzir erros de rede que afetam a integridade dos dados.

Criptografia do lado do cliente também é um método de criptografia seus dados inativos, como os dados são armazenados em sua forma criptografada. Falaremos sobre isso mais detalhadamente na seção criptografia [inativos](#encryption-at-rest).

##<a name="encryption-at-rest"></a>Criptografia inativos

Há três recursos Azure que fornecem criptografia inativos. Criptografia de disco Azure é usada para criptografar o sistema operacional e dados discos em máquinas virtuais do IaaS. Os outros dois – criptografia do lado do cliente e SSE – são usados para criptografar dados no armazenamento do Azure. Vamos observar cada uma dessas opções e, em seguida, fazer uma comparação e ver quando cada um deles pode ser usado.

Embora você possa usar a criptografia do lado do cliente para criptografar os dados em trânsito (que também é armazenado em seu formulário criptografado no armazenamento), talvez você prefira simplesmente usar HTTPS durante a transferência e ter alguma maneira para os dados sejam criptografados automaticamente quando ele está armazenado. Há duas maneiras de fazer isso - criptografia de disco do Azure e SSE. Um é usado para criptografar diretamente os dados em discos de dados e sistema operacional usados pelo VMs e a outra é usada para criptografar dados gravados para o armazenamento de Blob do Azure.

###<a name="storage-service-encryption-sse"></a>Criptografia de serviço de armazenamento (SSE)

SSE permite que você solicite que o serviço de armazenamento criptografar automaticamente os dados ao escrevê-lo ao armazenamento do Azure. Quando você lê os dados do armazenamento do Azure, ele será descriptografado pelo serviço de armazenamento antes de ser retornado. Isso permite que você a proteger seus dados sem precisar modificar código ou adicionar código a todos os aplicativos.

Esta é uma configuração que se aplica à conta de armazenamento inteiro. Você pode ativar e desativar esse recurso alterando o valor da configuração. Para fazer isso, você pode usar o portal do Azure, PowerShell, a CLI do Azure, a API REST de provedor de recursos de armazenamento ou a biblioteca de cliente de armazenamento do .NET. Por padrão, o SSE está desativado.

No momento, as chaves usadas para a criptografia são gerenciadas pelo Microsoft. Podemos gerar chaves originalmente e gerenciar o armazenamento seguro de chaves bem como a rotação regular, conforme definido pelo política interna do Microsoft. No futuro, você obtém a capacidade de gerenciar suas próprias chaves de criptografia e fornecem um caminho de migração do Microsoft gerenciados teclas para teclas gerenciado pelo cliente.

Este recurso está disponível para contas padrão e de armazenamento de Premium criadas usando o modelo de implantação do Gerenciador de recursos. SSE se aplica somente para bloquear blobs, blobs de página e acrescentar blobs. Outros tipos de dados, incluindo tabelas, filas e arquivos, não serão criptografados.

Dados são criptografados somente quando SSE está ativado e os dados são gravados ao armazenamento de Blob. Ativando ou desativando SSE não afeta os dados existentes. Em outras palavras, quando você habilita essa criptografia, ele não voltar e criptografar os dados que já existe; nem ele descriptografar os dados que já existe quando você desabilita SSE.

Se você quiser usar esse recurso com uma conta de armazenamento clássico, você pode criar uma nova conta de armazenamento do Gerenciador de recursos e usar AzCopy para copiar os dados para a nova conta. 

###<a name="client-side-encryption"></a>Criptografia do lado do cliente

Podemos mencionado criptografia do lado do cliente ao abordar a criptografia dos dados em trânsito. Esse recurso permite que você programaticamente criptografar seus dados em um aplicativo cliente antes de enviá-la pela conexão sejam gravados ao armazenamento do Azure e programaticamente descriptografar seus dados após recuperá-lo do armazenamento do Azure.

Isso fornece criptografia em trânsito, mas ele também fornece o recurso de criptografia inativos. Observe que embora os dados são criptografados em trânsito, ainda recomendamos usar HTTPS para aproveitar as verificações de integridade de dados internos que ajuda a reduzir erros de rede que afetam a integridade dos dados.

Um exemplo de onde você pode usar isso é se você tiver um aplicativo web que armazena blobs e recupera blobs e deseja que o aplicativo e os dados sejam tão seguros quanto possível. Nesse caso, você usaria criptografia do lado do cliente. O tráfego entre o cliente e o serviço de Blob do Azure contém o recurso criptografado e ninguém pode interpretar os dados em trânsito e reconstitui-lo em seu blobs particulares.

Criptografia do lado do cliente é incorporada do Java e as bibliotecas de cliente de armazenamento do .NET, que por sua vez usam as APIs do Cofre de chave do Azure, facilitando bastante implementar. O processo de criptografar e descriptografar os dados utiliza a técnica de envelope e armazena metadados usados pela criptografia em cada objeto de armazenamento. Por exemplo, para blobs, armazená-lo nos metadados blob, enquanto para filas, ele adiciona a cada mensagem de fila.

Para a própria criptografia, você pode gerar e gerenciar suas próprias chaves de criptografia. Você também pode usar teclas geradas pela biblioteca de cliente de armazenamento do Azure, ou você pode ter o Cofre de chave do Azure gerar chaves. Você pode armazenar suas chaves de criptografia em seu armazenamento de chave do local, ou você pode armazená-los em um cofre de chave do Azure. Azure Cofre de chave permite conceder acesso para as senhas no Azure chave cofre a usuários específicos usando o Active Directory do Azure. Isso significa que não apenas qualquer pessoa pode ler o Cofre de chave do Azure e recuperar as chaves que você estiver usando para criptografia do lado do cliente.

####<a name="resources"></a>Recursos

-   [Criptografar e descriptografar blobs no armazenamento do Microsoft Azure usando Cofre de chave do Azure](storage-encrypt-decrypt-blobs-key-vault.md)

    Este artigo mostra como usar a criptografia do lado do cliente com Cofre de chave do Azure, incluindo como criar o KEK e armazená-la no cofre usando o PowerShell.

-   [Compartimento chave de criptografia do lado do cliente e Azure para armazenamento do Microsoft Azure](storage-client-side-encryption.md)

    Este artigo fornece uma explicação de criptografia do lado do cliente e fornece exemplos de como usar a biblioteca de cliente de armazenamento para criptografar e descriptografar recursos dos serviços de armazenamento da quatro. Ele também fala sobre Azure chave cofre.

###<a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Usando criptografia de disco do Azure para criptografar discos usados pelo suas máquinas virtuais

Criptografia de disco Azure é um novo recurso que está no modo de visualização. Esse recurso permite que você criptografe o sistema operacional discos e dados usados por uma máquina Virtual IaaS. Para Windows, as unidades são criptografadas usando a tecnologia de criptografia de BitLocker padrão da indústria. Para Linux, os discos são criptografados usando a tecnologia de DM Crypt. Isso é integrado ao Azure chave cofre para permitir que você controle e gerencie as chaves de criptografia de disco.

A solução de criptografia de disco do Azure oferece suporte para os seguintes cenários de criptografia do três cliente:

-   Ative a criptografia em novas VMs IaaS criado a partir de arquivos VHD criptografados do cliente e chaves de criptografia fornecido pelo cliente, que são armazenadas no Azure chave cofre.

-   Ative a criptografia em novas VMs IaaS criado a partir do Azure Marketplace.

-   Ative a criptografia nas existentes VMs IaaS esteja em execução no Azure.

>[AZURE.NOTE] Para VMs Linux esteja em execução no Azure ou novas VMs Linux criado a partir de imagens do Azure Marketplace, criptografia do disco sistema operacional não é suportada atualmente. Criptografia de Volume do sistema operacional para Linux VMs é suportada apenas para VMs que foram locais criptografados e carregado Azure. Essa restrição se aplica somente para o disco de sistema operacional; criptografia de volumes de dados de uma VM Linux é suportada.

A solução oferece suporte a seguir para IaaS VMs para versão de visualização pública quando habilitado no Microsoft Azure:

-   Integração com o Azure cofre chave

-   Padrão [A, D e G série IaaS VMs](https://azure.microsoft.com/pricing/details/virtual-machines/)

-   Habilitar a criptografia em VMs IaaS criados usando o modelo do [Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md)

-   Público Azure todas as [regiões](https://azure.microsoft.com/regions/)

Esse recurso garante que todos os dados em seu disco de máquina virtual são criptografados no restante no armazenamento do Azure.

####<a name="resources"></a>Recursos

-   [Criptografia de disco Azure para Windows e máquinas virtuais de Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

    Este artigo discute a versão de visualização do Azure da criptografia do disco e fornece um link para baixar o white paper.

###<a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Comparação de criptografia de disco Azure, SSE e criptografia do lado do cliente

####<a name="iaas-vms-and-their-vhd-files"></a>IaaS VMs e seus arquivos VHD

Para discos usados pelo IaaS VMs, recomendamos o uso de criptografia de disco do Azure. Você pode ativar SSE para criptografar os arquivos VHD que são usados para trás esses discos no armazenamento do Azure, mas ele criptografa somente os dados recém-criados. Isso significa que se você criar uma máquina virtual e, em seguida, ative SSE a conta de armazenamento que contém o arquivo VHD, somente as alterações serão criptografadas, não o arquivo VHD original.

Se você criar uma máquina virtual usando uma imagem do Azure Marketplace, Azure executa uma [cópia superficial](https://en.wikipedia.org/wiki/Object_copying) da imagem para sua conta de armazenamento no armazenamento do Azure e ele não esteja criptografado, mesmo se você tiver SSE habilitado. Depois que ele cria a máquina virtual e começa a atualizar a imagem, SSE começarão a criptografia de dados. Por esse motivo, é melhor usar a criptografia de disco do Azure em VMs criadas a partir de imagens do Azure Marketplace se quiser que elas totalmente criptografado.

Se você trazer uma máquina virtual previamente criptografada para Azure do local, você poderá carregar as chaves de criptografia ao Cofre de chave do Azure e continuar a usar a criptografia para essa máquina virtual que você estava usando no local. Criptografia de disco Azure é habilitada para manipular esse cenário.

Se você tiver VHD não criptografado do local, você pode carregá-lo na galeria como uma imagem personalizada e provisionar uma máquina virtual dele. Se você fizer isso usando os modelos de Gerenciador de recursos, você pode pedir para ativar a criptografia de disco do Azure quando ele inicializa a máquina virtual.

Quando você adiciona um disco de dados e monte-la na máquina virtual, você pode ativar criptografia de disco do Azure nesse disco de dados. Ele irá criptografar disco dados localmente primeiro e, em seguida, a camada de gerenciamento de serviço fará uma gravação lenta contra armazenamento para que o conteúdo de armazenamento está criptografado.

####<a name="client-side-encryption"></a>Criptografia do lado do cliente####

Criptografia do lado do cliente é o método mais seguro de criptografar seus dados, porque ele criptografa antes de trânsito e criptografa os dados inativos. No entanto, ele requer que você adiciona código a aplicativos usando armazenamento, que você não deseja fazer. Nesses casos, você pode usar HTTPs para seus dados e em trânsito, SSE para criptografar os dados inativos.

Com a criptografia do lado do cliente, você pode criptografar entidades de tabela, mensagens da fila e blobs. Com SSE, você só pode criptografar blobs. Se precisar de dados de tabela e fila a serem criptografados, você deve usar a criptografia do lado do cliente.

Criptografia do lado do cliente é gerenciada totalmente pelo aplicativo. Isso é a abordagem mais segura, mas exigem que você faça alterações programação para o seu aplicativo e colocar processos de gerenciamento de chaves no lugar. Você usaria quando desejar a segurança extra durante o trânsito, e você quiser que seus dados armazenados sejam criptografados.

Criptografia do lado do cliente é mais carga no cliente e você precisa de conta para isso em seus planos de escalabilidade, especialmente se você estiver criptografando e transferindo muitos dados.

####<a name="storage-service-encryption-sse"></a>Criptografia de serviço de armazenamento (SSE)

SSE é gerenciado pelo armazenamento do Azure. Usar o SSE não oferece para a segurança dos dados em trânsito, mas ele criptografa os dados conforme está escrito ao armazenamento do Azure. Não há nenhum impacto sobre o desempenho ao usar esse recurso.

Você só pode criptografar blobs de bloco, acrescentar blobs e blobs usando SSE de página. Se você precisar criptografar dados de tabela ou fila, você deve considerar usando criptografia do lado do cliente.

Se você tiver um arquivo morto ou a biblioteca de arquivos VHD que você usar como base para a criação de novas máquinas virtuais, você pode criar uma nova conta de armazenamento, habilitar SSE e carregue os arquivos VHD dessa conta. Esses arquivos VHD serão criptografados pelo armazenamento do Azure.

Se você tiver habilitado para os discos em uma máquina virtual e SSE ativadas na conta do armazenamento mantém os arquivos VHD de criptografia de disco Azure, ele funcionará bem; resultará em qualquer dado escritas recentemente sendo criptografados duas vezes.

##<a name="storage-analytics"></a>Análise de armazenamento

###<a name="using-storage-analytics-to-monitor-authorization-type"></a>Usando a análise de armazenamento para monitorar o tipo de autorização

Para cada conta de armazenamento, você pode habilitar a análise de armazenamento do Azure executar um log e armazenar dados de métricas. Esta é uma ótima ferramenta para usar quando você deseja verificar as métricas de desempenho de uma conta de armazenamento ou precisar solucionar problemas de uma conta de armazenamento porque você está tendo problemas de desempenho.

Outra parte dos dados que você pode ver em logs de análise de armazenamento é o método de autenticação usado por alguém quando acessam armazenamento. Por exemplo, com o armazenamento de Blob, você pode ver se eles usados uma assinatura de acesso compartilhado ou as teclas de conta de armazenamento, ou se o blob acessado foi público.

Isso pode ser muito útil se você estiver totalmente preservando acesso ao armazenamento. Por exemplo, no armazenamento de Blob, você pode definir todos os contêineres para particular e implementar o uso de um serviço SAS ao longo de seus aplicativos. Você pode verificar os logs regularmente para ver se seu blobs são acessados usando as teclas de conta de armazenamento, que podem indicar uma violação de segurança, ou se os blobs são públicos, mas elas não devem ser.

####<a name="what-do-the-logs-look-like"></a>O que a aparência de logs?

Depois que você habilita as métricas de conta de armazenamento e log por meio do portal Azure, dados de análise serão iniciado acumular rapidamente. O registro em log e métricas para cada serviço é separado; registro em log só é gravado quando houver atividade nessa conta de armazenamento, enquanto as métricas serão registradas cada minuto, cada hora ou diariamente, dependendo de como você configurá-lo.

Os logs são armazenados em bolhas de bloco em um contêiner chamado $logs na conta de armazenamento. Este contêiner é criada automaticamente quando a análise de armazenamento está habilitada. Quando este contêiner é criada, você não pode excluí-lo, embora seja possível excluir seu conteúdo.

Em contêiner $logs, há uma pasta para cada serviço e, em seguida, existem subpastas para a ano/mês/dia/hora. Em hora, os logs simplesmente são numerados. Esta é a aparência da estrutura do diretório:

![Modo de exibição de arquivos de log](./media/storage-security-guide/image1.png)

Cada solicitação ao armazenamento do Azure está conectada. Aqui está um instantâneo de um arquivo de log, mostrando os primeira alguns campos.

![Instantâneo de um arquivo de log](./media/storage-security-guide/image2.png)

Você pode ver o que você pode usar os logs para controlar qualquer tipo de chamadas para uma conta de armazenamento.

####<a name="what-are-all-of-those-fields-for"></a>O que são todos esses campos para?

Não há um artigo listado nos recursos abaixo que fornece a lista dos campos muitos os logs e que elas são usadas. Aqui está a lista de campos na ordem:

![Instantâneo de campos em um arquivo de log](./media/storage-security-guide/image3.png)

Estamos interessados as entradas para GetBlob e como eles são autenticados, portanto, precisamos procure entradas com o tipo de operação "Get-Blob" e verificar o status de solicitação (4<sup>ésimo</sup> coluna) e o tipo de autorização (8<sup>ésimo</sup> coluna).

Por exemplo, nas primeiras linhas na lista acima, o status da solicitação é "Êxito" e o tipo de autorização é "autenticado". Isso significa que a solicitação foi validada usando a chave da conta de armazenamento.

####<a name="how-are-my-blobs-being-authenticated"></a>Como minha blobs estão sendo autenticados?

Temos três casos que estamos interessados.

1.  O blob é público e ela é acessada usando uma URL sem uma assinatura de acesso compartilhado. Nesse caso, o status da solicitação é "AnonymousSuccess" e o tipo de autorização é "anônimo".

    1.0; 2015-11-17T02:01:29.0488963Z; GetBlob; **AnonymousSuccess**200 124; 37; **anônimo**; mystorage...

2.  O blob é particular e foi usado com uma assinatura de acesso compartilhado. Nesse caso, o status da solicitação é "SASSuccess" e o tipo de autorização é "sas".

    1.0; 2015-11-16T18:30:05.6556115Z; GetBlob; **SASSuccess**200 416; 64; **sas**; mystorage...

3.  O blob é particular e a chave de armazenamento foi usada para acessá-lo. Nesse caso, o status da solicitação é "**êxito**" e o tipo de autorização é "**autenticados**".

    1.0; 2015-11-16T18:32:24.3174537Z; GetBlob; **Sucesso**206 59; 22; **autenticado**; mystorage...

Você pode usar o analisador de mensagem da Microsoft para exibir e analisar esses logs. Ele inclui os recursos de pesquisa e filtro. Por exemplo, talvez você queira pesquisar instâncias de GetBlob para ver se o uso for o que você espera, ou seja, para garantir que alguém não está acessando sua conta de armazenamento indevidamente.

####<a name="resources"></a>Recursos

-   [Análise de armazenamento](storage-analytics.md)

    Este artigo é uma visão geral de análise de armazenamento e como ativá-los.

-   [Formato de Log de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343259.aspx)

    Este artigo ilustra o formato de Log de análise de armazenamento e detalhes os campos disponíveis aí, incluindo tipo de autenticação, que indica o tipo de autenticação usado para a solicitação.

-   [Monitorar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)

    Este artigo mostra como configurar o monitoramento de métricas e registro em log para uma conta de armazenamento.

-   [Solução de problemas de ponta a ponta usando métricas de armazenamento do Azure e registro em log, AzCopy e analisador de mensagem](storage-e2e-troubleshooting.md)

    Este artigo fala sobre solução de problemas usando a análise de armazenamento e mostra como usar o analisador de mensagem da Microsoft.

-   [Guia de operando de analisador de mensagens da Microsoft](https://technet.microsoft.com/library/jj649776.aspx)

    Este artigo é a referência para o analisador de mensagem do Microsoft e inclui links para um tutorial, início rápido e resumo dos recursos.

##<a name="cross-origin-resource-sharing-cors"></a>Recurso de entre origens compartilhamento (CORS)

###<a name="cross-domain-access-of-resources"></a>Acesso de domínio cruzado de recursos

Quando um navegador da web em execução em um domínio faz uma solicitação HTTP para um recurso de um domínio diferente, isso se chama uma solicitação HTTP entre origens. Por exemplo, uma página HTML served de contoso.com faz uma solicitação para jpeg hospedado em fabrikam.blob.core.windows.net. Por razões de segurança, navegadores restringem solicitações HTTP entre origens iniciadas de dentro de scripts, como JavaScript. Isso significa que, quando algum código JavaScript em uma página da web em contoso.com solicita que jpeg em fabrikam.blob.core.windows.net, o navegador não permitirá a solicitação.

O que isso tem fazer com o armazenamento do Azure? Bem, se você estiver armazenando ativos estáticos como arquivos de dados XML ou JSON no armazenamento de Blob usando uma conta de armazenamento chamado Fabrikam, o domínio para os ativos será fabrikam.blob.core.windows.net e o aplicativo da web de contoso.com não poderão acessá-los usando JavaScript porque os domínios são diferentes. Isso também é verdadeiro se você estiver tentando chamar um dos serviços de armazenamento do Azure – como o armazenamento de tabela – que retornam dados JSON sejam processados pelo cliente JavaScript.

####<a name="possible-solutions"></a>Soluções possíveis

Uma maneira de resolver isso é atribuir um domínio personalizado como "storage.contoso.com" a fabrikam.blob.core.windows.net. O problema é que você só pode atribuir esse domínio personalizado à conta de um armazenamento. O que acontece se os ativos são armazenados em várias contas de armazenamento?

Outra maneira de resolver isso é tiver o aplicativo web atuar como um proxy para as chamadas de armazenamento. Isso significa que se você está carregando um arquivo para armazenamento de Blob, o aplicativo web seria seja escrever-localmente e copie-o para armazenamento de Blob ou ele seria ler tudo isso na memória e, em seguida, escreva-lo ao armazenamento de Blob. Como alternativa, você pode escrever um aplicativo web dedicado (como uma API Web) que carrega os arquivos localmente e grava-los ao armazenamento de Blob. De qualquer forma, você precisa quando determinando a escalabilidade precisa de conta para essa função.

####<a name="how-can-cors-help"></a>Como CORS pode ajudar?

Armazenamento do Azure permite habilitar CORS – entre o compartilhamento de recursos de origem. Para cada conta de armazenamento, você pode especificar domínios que podem acessar os recursos nessa conta de armazenamento. Por exemplo, no nosso caso descrito acima, podemos ativar CORS a conta de armazenamento fabrikam.blob.core.windows.net e configure-o para permitir acesso ao contoso.com. Em seguida, o contoso.com de aplicativo da web pode acessar diretamente os recursos no fabrikam.blob.core.windows.net.

Observação é que CORS permite o acesso, mas ele não fornece autenticação, que é necessária para todos os não-acesso público dos recursos de armazenamento. Isso significa que você só pode acessar blobs se eles são públicos ou incluir uma assinatura de acesso compartilhado, dando a você a permissão apropriada. Tabelas, filas e arquivos não têm público acesso e exigem uma SAS.

Por padrão, CORS está desabilitado em todos os serviços. Você pode habilitar CORS usando a API REST ou a biblioteca de cliente de armazenamento para chamar um dos métodos para definir as políticas de serviço. Quando você fizer isso, você pode incluir uma regra CORS, que está em XML. Aqui está um exemplo de uma regra de CORS que tenha sido definida usando a operação de definir propriedades de serviço para o serviço de Blob para uma conta de armazenamento. Você pode executar essa operação usando a biblioteca de cliente do armazenamento ou as APIs REST para o armazenamento do Azure.

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

Veja aqui o que significa que cada linha:

-   **AllowedOrigins** Isso informa que domínios não correspondente podem solicitar e receber dados do serviço de armazenamento. Isso significa que contoso.com e fabrikam.com podem solicitar dados do armazenamento de Blob para uma conta de armazenamento específico. Você também pode definir isso como um curinga (\*) para permitir que todos os domínios para solicitações de acesso.

-   **AllowedMethods** Esta é a lista dos métodos (verbos de solicitação HTTP) que podem ser usadas ao fazer a solicitação. Neste exemplo, apenas colocar e obter são permitidos. Você pode configurar isso para um caractere curinga (\*) para permitir que todos os métodos ser usado.

-   **AllowedHeaders** Esta é os cabeçalhos de solicitação que o domínio de origem pode especificar ao fazer a solicitação. Neste exemplo, todos os cabeçalhos de metadados, começando com x-ms-metadados, x-ms-meta-alvo e x-ms-meta-abc são permitidos. O caractere curinga (\*) indica que qualquer início de cabeçalho com o prefixo especificado é permitido.

-   **ExposedHeaders** Isso informa quais cabeçalhos de resposta devem ser expostos pelo navegador para o emissor de solicitação. Neste exemplo, qualquer cabeçalho começando com "x-ms - meta-" serão expostos.

-   **MaxAgeInSeconds** Esta é a quantidade máxima de tempo que um navegador irá armazenar em cache de solicitação de opções de comprovação. (Para obter mais informações sobre a solicitação de comprovação, verifique o primeiro artigo abaixo).

####<a name="resources"></a>Recursos

Para obter mais informações sobre CORS e como ativá-lo, por favor, confira esses recursos.

-   [Recurso de entre origens (CORS) suporte para os serviços de armazenamento do Azure no Azure.com de compartilhamento](storage-cors-support.md)

    Este artigo fornece uma visão geral do CORS e como definir as regras para os serviços de armazenamento diferentes.

-   [Recurso de entre origens compartilhamento (CORS) suporte para os serviços de armazenamento do Azure no MSDN](https://msdn.microsoft.com/library/azure/dn535601.aspx)

    Esta é a documentação de referência para CORS suporte para os serviços de armazenamento do Azure. Isso tem links para artigos aplicando a cada serviço de armazenamento e mostra um exemplo e explica cada elemento no arquivo CORS.

-   [Armazenamento do Microsoft Azure: Apresentando o CORS](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

    Este é um link para o artigo de blog inicial anunciando CORS e mostrando como usá-lo.

##<a name="frequently-asked-questions-about-azure-storage-security"></a>Perguntas frequentes sobre a segurança de armazenamento do Azure

1.  **Como verificar a integridade dos blobs que eu estou transferindo em ou sem armazenamento do Azure se eu não puder usar o protocolo HTTPS?**

    Se por algum motivo que você precisar usar HTTP em vez de HTTPS e você está trabalhando com blobs do bloco, você pode usar a verificação de MD5 para ajudar a verificar a integridade dos blobs sendo transferidos. Isso ajudará com proteção contra erros de camada de rede/transporte, mas não necessariamente com ataques intermediários.

    Se você pode usar HTTPS, que fornece segurança em nível de transporte, em seguida, usar a verificação de MD5 é redundantes e desnecessárias.
    
    Para obter mais informações, por favor, confira a [Visão geral do Azure Blob MD5](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).

2.  **O que são FIPS-conformidade do governo dos EUA?**

    Os Estados Unidos Federal Information Processing Standard (FIPS) define algoritmos aprovados para uso pelo sistemas de computador do Governo Federal dos EUA para a proteção de dados confidenciais. Habilitando FIPS o modo em um Windows server ou a área de trabalho informa o sistema operacional que algoritmos FIPS validada só devem ser usados. Se um aplicativo usa algoritmos incompatíveis, interromperá os aplicativos. Com o.NET Framework versões 4.5.2 ou superior, o aplicativo alterna automaticamente os algoritmos de criptografia para usar algoritmos compatíveis com FIPS quando o computador estiver no modo FIPS.

    Microsoft deixa até cada cliente decidir habilitar modo FIPS. Podemos acreditar que há nenhum motivo atraente para os clientes que não estão sujeitos a normas governamentais para habilitar o modo FIPS por padrão.

    **Recursos**

-   [Por que estamos estiver não recomendar "Modo FIPS" mais](http://blogs.technet.com/b/secguide/archive/2014/04/07/why-we-re-not-recommending-fips-mode-anymore.aspx)

    Este artigo de blog fornece uma visão geral de FIPS e explica por que eles não habilitarem modo FIPS por padrão.

-   [FIPS 140 validação](https://technet.microsoft.com/library/cc750357.aspx)

    Este artigo fornece informações sobre como os produtos da Microsoft e módulos de criptografia compatíveis com o padrão FIPS para o Governo Federal dos EUA.

-   ["Criptografia do sistema: usar FIPS compatível com algoritmos para criptografia, hash e assinatura" efeitos de configurações de segurança no Windows XP e em versões posteriores do Windows](https://support.microsoft.com/kb/811833)

    Este artigo fala sobre o uso do modo FIPS em computadores mais antigos do Windows.
