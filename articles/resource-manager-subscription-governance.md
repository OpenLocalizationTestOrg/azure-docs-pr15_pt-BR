<properties
   pageTitle="Práticas recomendadas para empresas mover para o Azure | Microsoft Azure"
   description="Descreve uma estrutura que empresas podem usar para garantir um ambiente seguro e gerenciável."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="rdendtler"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="rodend;karlku;tomfitz"/>

# <a name="azure-enterprise-scaffold---prescriptive-subscription-governance"></a>Scaffold enterprise Azure - gestão de assinatura dirigidas

Empresas cada vez mais estão adotando a nuvem pública para seu agilidade e flexibilidade. Eles estão utilizando Resistências da nuvem para gerar receita ou otimizar os recursos para os negócios. Microsoft Azure fornece inúmeros serviços que porte pode montar como blocos de construção para endereçar uma ampla variedade de cargas de trabalho e aplicativos. 

Mas, muitas vezes é difícil saber onde começar. Após decidir usar Azure, comumente surgirem a algumas perguntas:

- "Como atende nossos requisitos legais para Soberania de dados em certos países?"
- "Como garantir que alguém não mudou inadvertidamente um sistema crítico?"
- "Como saber o que cada recurso oferece suporte para que eu possa conta para ela e bola-o novamente com precisão?"

O cliente potencial de uma assinatura vazia com nenhum rails protetor é assustadora. Este espaço em branco pode dificultar a movimentação para o Azure.

Este artigo fornece um ponto de partida para profissionais técnicos a necessidade de gestão de endereços e saldo-lo com a necessidade de agilidade. Ele apresenta o conceito de um scaffold enterprise que orienta organizações em implementar e gerenciar suas assinaturas Azure. 

## <a name="need-for-governance"></a>Necessidade de governança

Ao mover para o Azure, você deve solucionar o tópico de gestão antecipado para garantir o uso bem-sucedido da nuvem dentro da empresa. Infelizmente, o tempo e burocracia de criação de um sistema de gestão abrangente significa alguns grupos de negócios ir diretamente para fornecedores sem envolver TI corporativos. Essa abordagem pode deixar a empresa aberta a vulnerabilidades se os recursos não são gerenciados corretamente. As características da nuvem pública agilidade, flexibilidade e preços baseados em consumo - são importantes para grupos de empresas que precisam rapidamente atender às exigências dos clientes (internas e externas). Mas, TI corporativos precisa garantir que dados e sistemas efetivamente protegidos.

Na vida real, estrutura é usada para criar a base da estrutura. O scaffold orienta a estrutura de tópicos geral e fornece pontos de âncora para sistemas mais permanentes ser montado. Um scaffold enterprise é a mesma: um conjunto de controles flexíveis e Azure recursos que fornecem uma estrutura para o ambiente e âncoras para serviços baseado em nuvem pública. Ele fornece os desenvolvedores (IT e grupos de negócios) uma base para criar e anexar novos serviços.

O scaffold baseia-se em práticas coletadas de muitos contratos com clientes de vários tamanhos. Esses variam de clientes de pequenas empresas desenvolvendo soluções na nuvem para empresas da Fortune 500 e fornecedores de software independentes que estão migrando e desenvolver soluções na nuvem. O scaffold enterprise foi "desenvolvido" para ser flexível para dar suporte a cargas de trabalho tradicionais IT e ágil cargas de trabalho; como os desenvolvedores criando aplicativos de software-como um serviço (SaaS) com base no Azure recursos.

O scaffold enterprise destina-se a ser a fundação de cada nova assinatura dentro do Azure. Ele permite que administradores para garantir a cargas de trabalho atendem aos requisitos de governança mínima de uma organização sem impedindo grupos comerciais e desenvolvedores de suas próprias metas rapidamente.

> [AZURE.IMPORTANT] Governança é fundamental para o sucesso do Azure. Este artigo destina-se a implementação técnica de um scaffold enterprise, mas apenas toca no processo e relações entre os componentes mais ampla. Gestão de política flui de cima para baixo e é determinada pelo qual os negócios deseja atingir. Naturalmente, a criação de um modelo de governança para Azure inclui representantes de IT, mas é mais importante, ele deve ter representação forte de gerenciamento de segurança e risco e líderes de grupo de negócios. No final, um scaffold enterprise é sobre reduzindo riscos de negócios para facilitar a missão e objetivos da organização.

A imagem a seguir descreve os componentes do scaffold. A fundação depende de um plano sólido para departamentos, contas e assinaturas. Os pilares consistem em políticas do gerente de recursos e padrões de nomenclatura fortes. O restante dos scaffold proveniente principais recursos Azure e recursos que permitem que um ambiente seguro e gerenciável.

![componentes de cadafalso](./media/resource-manager-subscription-governance/components.png)

> [AZURE.NOTE] Azure tiver crescido rapidamente desde sua introdução em 2008. Esse crescimento necessário Microsoft equipes de engenharia para repensar sua abordagem para gerenciar e implantar os serviços. O modelo do Gerenciador de recursos do Azure foi introduzido em 2014 e substitui o modelo de implantação clássico. Gerenciador de recursos permite que organizações implantar, organizar e controlar recursos Azure com mais facilidade. Gerenciador de recursos inclui paralelização ao criar recursos para implantação mais rápida de soluções complexas e interdependentes. Ele também inclui controle de acesso granular e a capacidade de recursos de marca com metadados. Recomendamos que você crie todos os recursos através do modelo do Gerenciador de recursos. O scaffold enterprise explicitamente foi projetado para o modelo do Gerenciador de recursos.

## <a name="define-your-hierarchy"></a>Definir sua hierarquia

A fundação do scaffold é a inscrição do Azure Enterprise (e o Portal Empresarial). A inscrição de enterprise define a forma e usar serviços Azure dentro de uma empresa e é a estrutura de gestão de núcleo. Dentro do contrato enterprise, clientes são capazes de subdividir ainda mais o ambiente em departamentos, contas e por fim, assinaturas. Uma assinatura do Azure é a unidade básica onde todos os recursos estão contidos. Ele também define vários limites dentro do Azure, como o número de cores, recursos, etc.

![hierarquia](./media/resource-manager-subscription-governance/agreement.png)

Cada empresa é diferente e a hierarquia na imagem anterior permite flexibilidade significativa na como Azure é organizado dentro da empresa. Antes de implementar as orientações contidas neste documento, você deve sua hierarquia de modelo e entender o impacto em cobrança, acesso aos recursos e complexidade.

Os três padrões comuns para inscrições Azure são:

- O padrão **funcional**

    ![funcional](./media/resource-manager-subscription-governance/functional.png)

- O padrão de **unidade de negócios** 

    ![Business](./media/resource-manager-subscription-governance/business.png)

- O padrão **geográficos**

    ![geográficos](./media/resource-manager-subscription-governance/geographic.png)

Aplicar o scaffold no nível de assinatura para estender a gestão de requisitos da empresa na assinatura.

## <a name="naming-standards"></a>Padrões de nomenclatura

Padrões de nomenclatura é o primeiro pilar do scaffold. Padrões de nomenclatura elaborados permitem que você identifique recursos no portal do, em uma fatura e dentro de scripts. Provavelmente, você já tem padrões de nomenclatura de infraestrutura no local. Ao adicionar Azure ao seu ambiente, você deve estender esses padrões de nomenclatura para seus recursos Azure. Padrão de nomenclatura facilitar o gerenciamento mais eficiente do ambiente em todos os níveis.

> [AZURE.TIP]
>
> - Revisar e adotar onde possível as [orientações de padrões e práticas](./guidance/guidance-naming-conventions.md). Este guia o ajudará a decidir sobre um padrão de nomenclatura significativo.
> - Use camelCasing para nomes de recursos (como myResourceGroup e vnetNetworkName). Observação: Há determinados recursos, como contas de armazenamento, onde a única opção é usar letras minúsculas (e não há outros caracteres especiais).
> - Considere usar políticas do Gerenciador de recursos do Azure (descritas na próxima seção) para impor padrões de nomenclatura.
 
## <a name="policies-and-auditing"></a>Políticas e auditoria

A segunda pilar do scaffold envolve a criação de [políticas do Gerenciador de recursos do Azure](resource-manager-policy.md) e [o log de atividade de auditoria](resource-group-audit.md). Políticas do Gerenciador de recursos fornecem a capacidade de gerenciar o risco no Azure. Você pode definir políticas que garantem Soberania de dados por restringindo, impondo ou auditoria determinadas ações. 

- Política é um sistema de **Permitir** padrão. Controlar ações definindo e atribuir políticas a recursos que negar ou ações em recursos de auditoria.
- Políticas são descritas por definições de política em uma linguagem de definição de política (se-então condições).
- Criar políticas de arquivos formatados com JSON (Javascript Object Notation). Depois de definir uma política, você atribuí-la a um escopo específico: assinatura, grupo de recursos. ou recurso.

Políticas de tem várias ações que permitem uma abordagem refinada para seus cenários. As ações são:

-   **Negar**: bloqueia a solicitação de recurso
-   **Auditoria**: permite que a solicitação, mas adiciona uma linha para o log de atividade (que pode ser usado para fornecer alertas ou acionar runbooks)
-   **Acrescentar**: adiciona informações especificadas para o recurso. Por exemplo, se não houver uma marca de "CostCenter" em um recurso, adicione essa marca com um valor padrão.

### <a name="common-uses-of-resource-manager-policies"></a>Usos comuns de políticas do Gerenciador de recursos

Azure políticas do Gerenciador de recursos são uma ferramenta poderosa no Azure Kit de ferramentas. Eles permitem que você evite custos inesperados, para identificar um centro de custos para recursos por meio de marcação e para garantir que os requisitos de conformidade são atendidos. Quando as políticas são combinadas com os recursos de auditoria internos, você pode modo soluções complexas e flexíveis. Políticas permitem que as empresas fornecer controles para cargas de trabalho de "TI tradicional" e "Agile" cargas de trabalho; como desenvolver aplicativos cliente. Os padrões mais comuns, podemos ver para políticas são:

- **Dados-de conformidade de localização geográfica Soberania** - Azure fornece regiões em todo o mundo. Empresas com frequência deseja controlar onde os recursos são criados (se deseja garantir Soberania de dados ou apenas para garantir que os recursos são criados Fechar para os consumidores de término dos recursos).
- **Gerenciamento de custos** - assinatura de um Azure pode conter recursos de muitos tipos e escala. Corporações muitas vezes quiser assegurar que assinaturas padrão evitar o uso de recursos desnecessariamente grandes, que podem custar centenas de dólares um mês ou mais.
- **Padrão de gestão por meio de marcas necessárias** - exigindo marcas é um dos recursos mais comuns e altamente desejados. Usando diretivas de Gerenciador de recursos do Azure empresas são capazes de garantir que um recurso está marcado adequadamente. As marcas mais comuns são: tipo de departamento, proprietário do recurso e ambiente (por exemplo - produção, teste, desenvolvimento)

**Exemplos**

"Tradicionais IT" assinatura para aplicativos de linha de negócios

-   Impor marcas de departamento e proprietário em todos os recursos
-   Restringir a criação de recursos para a região da América do Norte
-   Restringir a capacidade de criar VMs de série G e Clusters de HDInsight

"Agile" ambiente para uma unidade de negócios criando aplicativos em nuvem

- Para atender às necessidades de Soberania de dados, permitir a criação de recursos somente em uma região específica.
- Aplica a marca de ambiente em todos os recursos. Se um recurso é criado sem uma marca, acrescentar a **ambiente: desconhecido** marca ao recurso.
- Auditoria quando recursos são criados fora da América do Norte, mas não impeça.
- Auditoria quando os recursos de alto custo são criados.

> [AZURE.TIP]
>
> O uso mais comum de políticas do Gerenciador de recursos em organizações é controle *onde* os recursos podem ser criados e *que* tipos de recursos podem ser criados. Além de fornecer controles em *onde* e *quais*, muitas empresas usam diretivas para garantir recursos têm os metadados apropriados para cobrar volta consumo. Recomendamos aplicar políticas no nível de assinatura para:
>
> - Dados-de conformidade de localização geográfica Soberania
> - Gerenciamento de custos
> - Marcas necessárias (Determined pela necessidade de negócios, como BillTo, proprietário do aplicativo)
>
> Você pode aplicar políticas adicionais em níveis mais baixos de escopo.
 
### <a name="audit---what-happened"></a>Auditoria - o que aconteceu?

Para ver como o seu ambiente está funcionando, você precisa fazer auditoria da atividade de usuário. A maioria dos tipos de recursos dentro do Azure criam logs de diagnóstico que você pode analisar por meio de uma ferramenta de log ou no pacote de gerenciamento de operações do Azure. Você pode coletar logs de atividades em várias assinaturas para fornecer um departamento ou modo de exibição de empresa. Registros de auditoria são uma ferramenta importante de diagnóstico e um mecanismo fundamental para eventos de disparador no ambiente do Azure.

Logs de atividades do Gerenciador de recursos implantações permitem determinar as **operações** que levaram coloque e quem executou-los. Logs de atividades podem ser coletados e agregados usando ferramentas como a análise de Log.

## <a name="resource-tags"></a>Marcas de recurso

Como usuários de sua organização adicionam recursos à assinatura, ele se torna cada vez mais importante associar recursos o departamento apropriado, o cliente e o ambiente. Você pode anexar metadados para recursos por meio de [marcas](resource-group-using-tags.md). Usar marcas para fornecer informações sobre o recurso ou o proprietário. Marcas permitem que você não apenas agregar e agrupar recursos de diversas maneiras, mas usar esses dados para fins de devolução. Você pode marcar recursos com até 15 pares de chave: valor. 

Marcas de recurso são flexíveis e devem ser anexadas à maioria dos recursos. Exemplos de marcas comuns de recurso são:

- BillTo
- Departamento (ou unidade de negócios)
- Ambiente (desenvolvimento de produção, estágio)
- Nível (camada Web, camada de aplicativos)
- Proprietário do aplicativo
- NomeDoProjeto

![marcas](./media/resource-manager-subscription-governance/resource-group-tagging.png)

Para obter mais exemplos de marcas, consulte [recomendado Nomeando convenções para recursos Azure](./guidance/guidance-naming-conventions.md).

> [AZURE.TIP]
>
> Crie uma estratégia de marcação que identifica em suas assinaturas quais metadados é necessário para os negócios, finanças, segurança, gerenciamento de riscos e gerenciamento geral do ambiente. É recomendável fazer uma política que exige a marcação para:
>
> - Grupos de recursos
> - Armazenamento
> - Máquinas virtuais
> - Servidores de ambientes/web do serviço de aplicativo

## <a name="resource-group"></a>Grupo de recursos 

Gerenciador de recursos permite que você colocar recursos em grupos significativos para afinidade gerenciamento, de cobrança ou natural. Conforme mencionado anteriormente, o Azure tem dois modelos de implantação. No modelo clássico anterior, a unidade básica de gerenciamento foi a assinatura. Era difícil dividir recursos dentro de uma assinatura, o que levou à criação de grandes números de assinaturas. Com o modelo do Gerenciador de recursos, podemos viu a introdução de grupos de recursos. Grupos de recursos são contêineres de recursos que têm um ciclo de vida comuns ou compartilham um atributo como "todos os servidores SQL" ou "Aplicativo R".

Grupos de recursos não podem estar contidos em si e recursos só podem pertencer a um grupo de recursos. Você pode aplicar determinadas ações em todos os recursos em um grupo de recursos. Por exemplo, a exclusão de um grupo de recursos remove todos os recursos no grupo de recursos. Normalmente, você colocar uma aplicação inteira ou um sistema relacionados no mesmo grupo de recursos. Por exemplo, um aplicativo de três camadas chamado Contoso Web Application seria contêm o servidor web, o servidor de aplicativos e o SQL server no mesmo grupo de recursos.

> [AZURE.TIP]
>
> Como organizar seus grupos de recursos pode variar de cargas de trabalho de "TI tradicional" cargas de trabalho de "TI ágil"
>
> - "Tradicionais IT" cargas de trabalho mais comumente são agrupadas por itens dentro do ciclo de vida mesmo, como um aplicativo. Agrupar por aplicativo permite gerenciamento de aplicativo individual.
> - "Agile IT" cargas de trabalho tendem a se concentrar em aplicativos externos nuvem voltados para o cliente. Os grupos de recursos devem refletir as camadas de implantação (como camada de Web, nível de aplicativo) e gerenciamento.

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

Você provavelmente está se perguntando "quem deve ter acesso aos recursos?" e "como controlar esse acesso?" Permitindo ou proibindo o acesso ao portal do Azure e controlar o acesso aos recursos no portal é fundamental. 

Quando Azure inicialmente foi lançado, controles de acesso para uma assinatura foram básicos: administrador ou colegas. Acesso a uma assinatura no acesso a todos os recursos no portal do modelo implícito clássico. Essa falta de controle refinado levou à proliferação de assinaturas para fornecer um nível de controle de acesso razoável para uma inscrição do Azure.

Essa proliferação de assinaturas não for mais necessário. Com o controle de acesso baseado em função, você pode atribuir usuários a funções padrão (como tipos comuns de "leitor" e "gravador" das funções). Você também pode definir funções personalizadas.

> [AZURE.TIP]
>  
> - Conecte seu armazenamento de identidade corporativa (mais comumente Active Directory) ao Azure Active Directory usando a ferramenta de AD Connect.
> - Controle a administração/Co-administração de uma assinatura usando uma identidade gerenciada. **Não** atribuir a administração/Co-administrador para um novo proprietário de assinatura. Em vez disso, use funções RBAC para fornecer direitos do **proprietário** para um grupo ou individuais.
> - Adicione usuários Azure a um grupo (por exemplo, o aplicativo X proprietários) no Active Directory. Use o grupo sincronizado para fornecer os direitos apropriados para gerenciar o grupo de recursos que contém o aplicativo de membros do grupo.
> - Siga o princípio do concedendo o **privilégio mínimo** necessário para realizar o trabalho esperado. Por exemplo:
>     - Grupo de implantação: Um grupo que só é capaz de implantar recursos.
>     - Gerenciamento de máquina virtual: Um grupo que poderá reiniciar VMs (para operações)

## <a name="azure-resource-locks"></a>Bloqueios de recursos do Azure

Enquanto a sua organização adiciona serviços básicos para a assinatura, ele se torna cada vez mais importante garantir que esses serviços estão disponíveis para evitar interrupções de negócios. [Bloqueios de recursos](resource-group-lock-resources.md) permitem que você restringir operações em recursos de alto valor onde modificação ou exclusão teria um impacto significativo nos seus aplicativos ou infraestrutura de nuvem. Você pode aplicar bloqueios a uma assinatura, grupo de recursos ou recurso. Normalmente, você aplicar bloqueios para recursos básicos como redes virtuais, gateways e contas de armazenamento. 

Bloqueios de recursos suportam dois valores: CanNotDelete e somente leitura. CanNotDelete significa que os usuários (com os direitos apropriados) ainda podem ler ou modificar um recurso, mas não é possível excluí-la. Somente leitura significa que os usuários autorizados não é possível excluir ou modificar um recurso.

Para criar ou excluir bloqueios de gerenciamento, você deve ter acesso ao `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*` ações.
Das funções internas, somente o proprietário e o administrador de acesso de usuário são concedidos essas ações.

> [AZURE.TIP] Opções de núcleo de rede devem ser protegidas com bloqueios. Exclusão acidental de um gateway, VPN to-site seria desastroso uma assinatura do Azure. Azure não permite a exclusão de uma rede virtual que está em uso, mas aplicar mais restrições é uma precaução úteis. Políticas também são fundamentais para a manutenção dos controles apropriados. Recomendamos que você aplicar um bloqueio de **CanNotDelete** para políticas que estão em uso.
>
> - Rede virtual: CanNotDelete
> - Grupo de segurança de rede: CanNotDelete
> - Políticas: CanNotDelete

## <a name="core-networking-resources"></a>Principais recursos de rede

Acesso aos recursos pode ser interno (dentro da rede da empresa) ou externo (através da internet). É fácil para os usuários em sua organização inadvertidamente colocar recursos no ponto errado e potencialmente abri-los ao access mal-intencionados. Assim como dispositivos no local, empresas devem adicionar controles apropriados para garantir que os usuários Azure faça as decisões corretas. Para gestão de assinatura, identificamos recursos principais que fornecem controle básico do access. Principais recursos consistem em:

- **Redes virtuais** são objetos de contêiner para sub-redes. Embora não terminantemente necessário, geralmente é usado quando Conectando aplicativos aos recursos corporativos internos.
- **Grupos de segurança de rede** são semelhantes a um firewall e fornecer regras para como um recurso pode "falar" através da rede. Eles fornecem controle granular sobre como / se uma sub-rede (ou máquina virtual) pode se conectar à Internet ou outras sub-redes na mesma rede virtual.

![sistema de rede básico](./media/resource-manager-subscription-governance/core-network.png)

> [AZURE.TIP]
>  
> - Crie redes virtuais dedicadas a cargas de trabalho de face interna e externa da face cargas de trabalho. Essa abordagem reduz a chance de colocar inadvertidamente máquinas virtuais que são destinadas a cargas de trabalho internas em um espaço de opostas externo.
> - Grupos de segurança de rede são fundamentais para essa configuração. No mínimo, bloquear o acesso à internet de redes virtuais internas e bloquear o acesso à rede corporativa de redes externas de virtuais.

### <a name="automation"></a>Automação

Gerenciar recursos individualmente é ambos demorada e potencialmente propensa para determinadas operações. Azure fornece vários recursos de automação incluindo Azure automação, aplicativos de lógica e funções do Azure. [Automação de Azure](./automation/automation-intro.md) permite que os administradores criem e definir runbooks para lidar com tarefas comuns no gerenciamento de recursos. Você pode criar runbooks usando um editor de código do PowerShell ou um editor de gráfico. Você pode produzir fluxos de trabalho de vários estágios complexos. Automação Azure geralmente é usada para lidar com tarefas comuns, como desligar recursos não utilizados ou criar recursos em resposta a um disparador específico sem precisar a intervenção.

> [AZURE.TIP]
>
> - Criar uma conta de automação do Azure e examine os disponíveis na [Galeria de Runbook](./automation/automation-runbook-gallery.md)de runbooks disponíveis (linha gráfica e de comando).
> - Importar e personalizar runbooks chave para seu próprio uso.
>
> Um cenário comum é a capacidade de máquinas virtuais de início/desligamento em um cronograma. Há runbooks de exemplo que estão disponíveis na Galeria que manipular esse cenário e ensinam como expandi-la.


## <a name="azure-security-center"></a>O Centro de segurança do Azure

Talvez um dos bloqueadores de maiores para nuvem adoção foi preocupações sobre segurança. Departamentos de segurança e gerentes de risco de TI necessário garantir que recursos no Azure estejam protegidos. 

O [Centro de segurança do Azure](./security-center/security-center-intro.md) fornece uma visão central o status de segurança de recursos nas assinaturas e fornece recomendações que ajudam a evitar recursos comprometidos. Ele pode habilitar políticas mais granulares (por exemplo, aplicar políticas para grupos de recursos específicos que permitem a empresa adaptar sua postura em relação ao risco são tratando). Finalmente, o Centro de segurança do Azure é uma plataforma aberta que permite que parceiros da Microsoft e fornecedores independentes de software para criar software que se conecta à Central de segurança do Azure para aprimorar seus recursos. 

> [AZURE.TIP]
>
> O Centro de segurança do Azure é ativado por padrão em cada assinatura. No entanto, você deve habilitar a coleta de dados de máquinas virtuais para permitir que o Centro de segurança do Azure instalar o agente e começar a coleta de dados.
>
> ![coleta de dados](./media/resource-manager-subscription-governance/data-collection.png)

## <a name="next-steps"></a>Próximas etapas

- Agora que você aprendeu sobre gestão de assinatura, é hora de ver essas recomendações na prática. Veja [exemplos de implementação de gestão de assinatura Azure](resource-manager-subscription-examples.md).

*[Karl Kuhnhausen](https://github.com/karlkuhnhausen) contribuiu neste tópico.*
