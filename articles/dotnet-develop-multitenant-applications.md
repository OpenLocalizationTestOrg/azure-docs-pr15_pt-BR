<properties
    pageTitle="Padrão de aplicativo Web do locatário vários | Microsoft Azure"
    description="Encontre overviews arquitetônicos e padrões de design que descrevem como implementar um aplicativo web de vários locatários no Azure."
    services=""
    documentationCenter=".net"
    authors="wadepickett" 
    manager="wpickett"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/05/2015"
    ms.author="wpickett"/>

# <a name="multitenant-applications-in-azure"></a>Aplicativos vários locatários no Azure

Um aplicativo de vários locatários é um recurso compartilhado que permite que usuários separados ou "locatários," Exibir o aplicativo, como se fosse deles. Um cenário típico que serve a um aplicativo de vários locatários é um no qual todos os usuários do aplicativo talvez queira personalizar a experiência do usuário, mas caso contrário tem os mesmos requisitos básicos de negócios. Exemplos de aplicativos de vários locatários grandes são Office 365, Outlook.com e visualstudio.com.

Da perspectiva de um provedor aplicativo, os benefícios de multilocação principalmente se relacionam com eficiência operacional e de custo. Uma versão do seu aplicativo pode atender às necessidades dos locatários muitos/clientes, permitindo a consolidação do sistema de tarefas de administração como monitoramento, ajuste de desempenho, manutenção de software e backups de dados.

A seguir fornece uma lista dos mais significativo metas e requisitos da perspectiva de um provedor.

- **Provisionamento**: você deve ser capaz de provisionar locatários novo para o aplicativo.  Para obter aplicativos vários locatários com um grande número de locatários, é geralmente necessário automatizar esse processo habilitando provisionamento de autoatendimento.
- **Manutenção**: você deve ser capaz de atualizar o aplicativo e executar outras tarefas de manutenção enquanto estiver usando vários locatários-lo.
- **Monitoramento**: você deve ser capaz de monitorar o aplicativo em todos os momentos para identificar problemas e solucioná-los. Isso inclui monitoramento como cada locatário está usando o aplicativo.

Um aplicativo de vários locatários implementado adequadamente fornece os seguintes benefícios aos usuários.

- **Isolamento**: as atividades de locatários individuais não afetam o uso do aplicativo por outros locatários. Inquilinos não consegue acessar dados uns dos outros. Parece locatários que tiverem uso exclusivo do aplicativo.
- **Disponibilidade**: locatários individuais quer que o aplicativo seja constantemente disponível, talvez com garantias definidas em um SLA. Novamente, as atividades de outras locatários não devem afetar a disponibilidade do aplicativo.
- **Escalabilidade**: escalas de aplicativo para atender à demanda de locatários individuais. A presença e as ações de outros locatários não devem afetar o desempenho do aplicativo.
- **Custos**: os custos são inferiores executando um aplicativo dedicado, único locatário como multilocação permite o compartilhamento de recursos.
- **Personalização**. A capacidade de personalizar o aplicativo para um locatário individual de diversas maneiras como adicionando ou removendo recursos, alterando as cores e logotipos ou até mesmo adicionar seu próprio código ou script.

Em resumo, embora existam várias considerações que você deve levar em conta para fornecer um serviço altamente escalável, há também um número das metas e os requisitos que são comuns a muitos aplicativos vários locatários. Alguns podem não ser relevantes em cenários específicos, e a importância de requisitos e metas individuais serão diferentes em cada cenário. Como um provedor do aplicativo vários locatários, você também terá requisitos, como e metas dos locatários metas e requisitos, lucratividade, cobrança, vários níveis de serviço, provisionamento, monitoramento de manutenção e automação de reunião.

Para obter mais informações sobre as considerações de design adicionais de um aplicativo de vários locatários, consulte [hospedando um aplicativo de vários locatários no Azure][]. Para obter informações sobre padrões comuns de arquitetura de dados de aplicativos de banco de dados do locatários vários software como serviço (SaaS), consulte [Os padrões de Design para vários locatários SaaS aplicativos com o Azure SQL Database](./sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure fornece vários recursos que permite que você solucione os principais problemas encontrados durante a criação de um sistema de vários locatários.

**Isolamento**

- Site de segmento locatários por cabeçalhos de Host com ou sem comunicação SSL
- Site de segmento locatários por parâmetros de consulta
- Serviços Web em funções de trabalho
    - Funções de trabalho. que normalmente processar dados no back-end de um aplicativo.
    - Funções da Web que normalmente atuam como o frontend para aplicativos.

**Armazenamento**

Gerenciamento de dados como banco de dados do SQL Azure ou armazenamento do Azure serviços como o serviço de tabela que fornece serviços para armazenamento de grandes quantidades de dados não estruturados e o serviço de Blob que fornece serviços para armazenar grandes quantidades de texto não estruturado ou dados binários como imagens, áudio e vídeo.

- Proteção de dados de Multitenant no banco de dados do SQL apropriado logon do SQL Server por locatário.
- Usando tabelas do Azure para aplicativo recursos especificando uma política de acesso no nível do contêiner, é possível a capacidade de ajustar permissões sem precisar emitir nova URL para os recursos protegidos com assinaturas de acesso compartilhado.
- Azure filas para filas do Azure de recursos do aplicativo são comumente usados para processamento de unidade em nome de locatários, mas também podem ser usadas para distribuir o trabalho necessário para o gerenciamento ou de provisionamento.
- Filas de barramento de serviço para recursos de aplicativo que envia trabalho para um compartilhado um serviço, você pode usar uma única fila onde cada remetente de locatário só tem permissões (conforme derivado de declarações emitidas do ACS) para enviar a fila, enquanto somente os receptores do serviço tem permissão para retirar da fila os dados provenientes de vários locatários.


**Serviços de segurança e de Conexão**

- Barramento de serviço Azure, uma infraestrutura de mensagens que fica entre aplicativos permitindo a troca de mensagens de uma maneira flexível para escala aprimorada e resiliência.

**Serviços de rede**

Azure fornece vários serviços de rede que oferecem suporte à autenticação e melhorar a capacidade de gerenciamento de seus aplicativos hospedados. Esses serviços incluem o seguinte:

- Azure permite de rede Virtual você provisionar e gerenciar redes virtuais privadas (VPNs) no Azure bem como com segurança vinculá-las com locais infraestrutura.
- Gerenciador de tráfego de rede virtual permite tráfego de entrada de balanço de carga entre vários serviços Azure hospedados se ele estiver executando no mesmo data center ou entre diferentes dos data centers em todo o mundo.
- Azure Active Directory (AD Azure) é um serviço moderno baseados em REST, que fornece recursos de controle de acesso e gerenciamento de identidade para seus aplicativos de nuvem. Usando o Azure AD para recursos de aplicativos Azure AD para oferece uma maneira fácil de autenticação e autorização de usuários para acessar seus aplicativos da web e serviços enquanto permite que os recursos de autenticação e autorização para ser acrescentado sair do seu código.
- Barramento de serviço do Azure fornece um sistema de mensagens seguro e distribuído com o recurso de fluxo de dados para e aplicativos híbridos, como a comunicação entre o Azure hospedado aplicativos e local aplicativos e serviços, sem a necessidade de infra-estruturas complexas de firewall e segurança. Usando retransmissão de barramento de serviço para recursos de aplicativos para os serviços que são expostos como pontos de extremidade pode pertencer a locatário (por exemplo, hospedado fora do sistema, como no local) ou podem ser serviços provisionados especificamente para o site principal (porque dados confidenciais e específicas do locatário percorrem entre eles).



**Provisionamento de recursos**

Azure fornece várias maneiras locatários novo provisionar para o aplicativo. Para obter aplicativos vários locatários com um grande número de locatários, é geralmente necessário automatizar esse processo habilitando provisionamento de autoatendimento.

- Funções de trabalho permitem provisionar e provisionar eliminação por locação recursos (como quando um novo locatário sinais-up ou cancela), coletar métricas monitoração usar e gerenciar escala seguindo um cronograma de certos no ou resposta para a interseção dos limites de indicadores chave de desempenho. Esta função mesma também pode ser usada para enviar atualizações e atualizações para a solução.
- Blobs Azure podem ser usados para provisionar computação ou previamente inicializados pacotes, imagens VHD e outros recursos de serviço de recursos de armazenamento para novos locatários fornecendo as políticas de acesso de nível de contêiner proteger a computação.
- Opções de provisionamento de recursos de banco de dados SQL para um locatário incluem:

    -   DDL em scripts ou incorporados como recursos em conjuntos
    -   SQL Server 2008 R2 DAC pacotes implantado programaticamente.
    -   Copiando um banco de dados de referência mestra
    -   Usando o banco de dados importação e exportação para provisionar novos bancos de dados de um arquivo.



<!--links-->

[Hospedar um aplicativo de vários locatário no Azure]: http://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: http://msdn.microsoft.com/library/windowsazure/hh689716
