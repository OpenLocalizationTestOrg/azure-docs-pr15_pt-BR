<properties
   pageTitle="Sincronização do Azure AD Connect: Noções básicas sobre a arquitetura | Microsoft Azure"
   description="Este tópico descreve a arquitetura de sincronização do Azure AD Connect e explica os termos usados."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/31/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Sincronização do Azure AD Connect: Noções básicas sobre a arquitetura
Este tópico aborda a arquitetura básica para sincronização do Azure AD Connect. Em vários aspectos, ele é semelhante a seus antecessores MIIS 2003, ILM 2007 e 2010 de FIM. Sincronização do Azure AD Connect é a evolução dessas tecnologias. Se você estiver familiarizado com qualquer uma dessas tecnologias anteriores, o conteúdo deste tópico será familiar também. Se você estiver começando a sincronização, este tópico é para você. No entanto não é um requisito para conhecer os detalhes deste tópico para ser bem-sucedida fazendo personalizações para sincronização do Azure AD Connect (chamado de mecanismo de sincronização neste tópico).

## <a name="architecture"></a>Arquitetura
O mecanismo de sincronização cria uma exibição integrada de objetos que estão armazenados em várias fontes de dados conectados e gerencia informações de identidade nessas fontes de dados. Este modo de exibição integrado é determinado pelas informações de identidade recuperadas de um conjunto de regras que determinam como processar essas informações e fontes de dados conectados.

### <a name="connected-data-sources-and-connectors"></a>Fontes de dados conectadas e conectores
O mecanismo de sincronização processa informações de identidade de repositórios de dados diferentes, como Active Directory ou um banco de dados do SQL Server. Cada repositório de dados que organiza seus dados em um formato de banco de dados e que fornece os métodos de acesso a dados padrão é um candidato potencial de fonte de dados para o mecanismo de sincronização. Os repositórios de dados que são sincronizados pelo mecanismo de sincronização são chamados **conectada a fontes de dados** ou **conectado diretórios** (CD).

O mecanismo de sincronização encapsula interação com uma fonte de dados conectada dentro de um módulo chamado de um **conector**. Cada tipo de fonte de dados conectada tem um conector específico. O conector converte uma operação necessária para o formato que entende de fonte de dados conectada.

Conectores fazer chamadas de API para trocar informações de identidade (de leitura e gravação) com uma fonte de dados conectados. Também é possível adicionar um conector personalizado usando a estrutura de conectividade extensível. A ilustração a seguir mostra como um conector se conecta a uma fonte de dados conectada para o mecanismo de sincronização.

![Arch1](./media/active-directory-aadconnectsync-understanding-architecture/arch1.png)

Os dados podem fluir em qualquer direção, mas não pode fluir em ambas as direções simultaneamente. Em outras palavras, um conector pode ser configurado para permitir que os dados da fonte de dados conectada ao mecanismo de sincronização ou do mecanismo de sincronização para a fonte de dados conectada de fluxo, mas somente uma dessas operações pode ocorrer em qualquer momento para um objeto e atributo. A direção pode ser diferente para diferentes objetos e atributos diferentes.

Para configurar um conector, você especifica os tipos de objeto que você deseja sincronizar. Especificar os tipos de objeto define o escopo de objetos que estão incluídos no processo de sincronização. A próxima etapa é selecionar os atributos a sincronizar, que é conhecido como uma lista de inclusão de atributo. Essas configurações podem ser alteradas a qualquer momento em resposta às alterações suas regras de negócios. Quando você usa o Assistente de instalação do Azure AD Connect, essas configurações são definidas para você.

Para exportar objetos a uma fonte de dados conectados, a lista de inclusão de atributo inclua pelo menos os atributos mínimos necessários para criar um tipo de objeto específico em uma fonte de dados conectados. Por exemplo, o atributo **sAMAccountName** deve ser incluído na lista de inclusão de atributo para exportar um objeto de usuário no Active Directory porque todos os objetos de usuário no Active Directory devem ter um atributo **sAMAccountName** definido. Novamente, o Assistente de instalação faz essa configuração para você.

Se a fonte de dados conectada usa componentes estruturais, como partições ou contêineres para organizar objetos, você pode limitar as áreas na fonte de dados conectada que são usadas para uma determinada solução.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Estrutura interna do namespace de mecanismo de sincronização
O namespace do mecanismo de sincronização inteira consiste em dois namespaces que armazenam as informações de identidade. Os dois espaços são:

- O espaço de conector (CS)
- O metaverso (MV)

O **espaço do conector** é uma área de transferência que contém representações dos objetos designados de uma fonte de dados conectada e os atributos especificados na lista de inclusão de atributo. O mecanismo de sincronização usa espaço do conector para determinar o que mudou na fonte de dados conectada e alterações de entrada do estágio. O mecanismo de sincronização também usa o espaço de conector para testar as alterações de saída para exportar para a fonte de dados conectados. O mecanismo de sincronização mantém um espaço de conector distintos como uma área de transferência para cada conector.

Usando uma área de transferência, o mecanismo de sincronização permanece independente das fontes de dados conectados e não é afetado pela sua disponibilidade e acessibilidade. Como resultado, é possível processar informações de identidade a qualquer momento usando os dados na área de transferência. O mecanismo de sincronização pode solicitar apenas as alterações feitas dentro de fonte de dados conectada desde a última sessão de comunicação terminada ou push somente as alterações às informações de identidade que ainda não recebeu a fonte de dados conectada, que reduz o tráfego de rede entre o mecanismo de sincronização e a fonte de dados conectados.

Além disso, o mecanismo de sincronização armazena informações de status sobre todos os objetos que ele estágios no espaço de conector. Quando novos dados são recebidos, o mecanismo de sincronização sempre avalia se os dados já foi sincronizados.

O **metaverso** é uma área de armazenamento que contém as informações de identidade agregados de várias fontes de dados conectados, fornecendo um modo de exibição único global e integrado de todos os objetos combinados. Objetos do metaverso são criados com base nas informações de identidade recuperados de fontes de dados conectados e um conjunto de regras que permitem que você personalize o processo de sincronização.

A ilustração a seguir mostra o namespace de espaço do conector e namespace metaverso no mecanismo de sincronização.

![Arch2](./media/active-directory-aadconnectsync-understanding-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Objetos de identidade do mecanismo de sincronização
Os objetos no mecanismo de sincronização são representações de qualquer um dos objetos na fonte de dados conectada ou o modo de exibição integrado que mecanismo de sincronização tem desses objetos. Cada objeto de mecanismo de sincronização deve ter um identificador exclusivo (GUID). GUIDs fornecem a integridade dos dados e express relações entre objetos.

### <a name="connector-space-objects"></a>Objetos de espaço do conector
Quando o mecanismo de sincronização se comunica com uma fonte de dados conectados, ele lê as informações de identidade na fonte de dados conectada e usa essas informações para criar uma representação do objeto identidade de espaço do conector. Você não pode criar ou excluir esses objetos individualmente. No entanto, você pode excluir manualmente todos os objetos em um espaço de conector.

Todos os objetos no espaço do conector tem dois atributos:

- Um identificador exclusivo (GUID)
- Um nome distinto (também conhecido como DN)

Se a fonte de dados conectada atribui um atributo exclusivo para o objeto, em seguida, objetos no espaço do conector também podem ter um atributo de âncora. O atributo de âncora identifica exclusivamente um objeto na fonte de dados conectados. O mecanismo de sincronização usa a âncora para localizar a representação correspondente deste objeto na fonte de dados conectados. Mecanismo de sincronização pressupõe que a âncora de um objeto nunca muda ao longo do tempo de vida do objeto.

Muitos dos conectores usam um identificador exclusivo conhecido para gerar uma âncora automaticamente para cada objeto quando ele é importado. Por exemplo, o Active Directory Connector usa o atributo de **objectGUID** para uma âncora. Para fontes de dados conectados que não fornecem um identificador exclusivo claramente definido, você pode especificar geração de âncora como parte da configuração do conector.

Nesse caso, a âncora baseia-se em um ou mais atributos exclusivos de um objeto digite, nem de quais alterações e que exclusivamente identifica o objeto no espaço de conector (por exemplo, um número de funcionário ou um ID de usuário).

Um objeto de espaço do conector pode ser um destes procedimentos:

- Um objeto de teste
- Um espaço reservado

### <a name="staging-objects"></a>Objetos de teste
Um objeto de preparação representa uma instância dos tipos de objeto designado da fonte de dados conectada. Além dos GUID e o nome distinto, um objeto de preparação sempre tem um valor que indica o tipo de objeto.

Objetos de teste que foram importados sempre têm um valor para o atributo de âncora. Objetos de teste que foram provisionados recentemente pelo mecanismo de sincronização e estiver no processo de criação da fonte de dados conectada não tem um valor para o atributo de âncora.

Objetos de teste também conter valores atuais dos atributos de negócios e informações operacionais necessários pelo mecanismo de sincronização para executar o processo de sincronização. Informações operacionais incluem sinalizadores que indicam o tipo de atualizações que são transferidos no objeto preparação. Se um objeto de preparação recebeu novas informações de identidade da fonte de dados conectada que ainda não foi processada, o objeto está sinalizado como **importação pendente**. Se um objeto de teste tem novas informações de identidade que ainda não tem sido exportadas à fonte de dados conectada, ele será sinalizado como **Exportar pendente**.

Um objeto de preparação pode ser um objeto de importação ou exportação. O mecanismo de sincronização cria um objeto de importação usando informações de objeto recebidas de fonte de dados conectada. Quando o mecanismo de sincronização recebe informações sobre a existência de um novo objeto que corresponde a um dos tipos de objeto selecionados no conector, ele cria um objeto de importação no espaço do conector como uma representação do objeto na fonte de dados conectados.

A ilustração a seguir mostra um objeto de importação que representa um objeto na fonte de dados conectados.

![Arch3](./media/active-directory-aadconnectsync-understanding-architecture/arch3.png)

O mecanismo de sincronização cria um objeto de exportação usando informações de objeto no metaverso. Objetos de exportação são exportados para a fonte de dados conectada durante a próxima sessão de comunicação. Sob a perspectiva do mecanismo de sincronização, objetos de exportação ainda não existe na fonte de dados conectados. Portanto, o atributo de âncora para um objeto de exportação não está disponível. Depois que ele recebe o objeto do mecanismo de sincronização, a fonte de dados conectada cria um valor exclusivo para o atributo de âncora do objeto.

A ilustração a seguir mostra como um objeto de exportação é criado usando as informações de identidade no metaverso.

![Arch4](./media/active-directory-aadconnectsync-understanding-architecture/arch4.png)

O mecanismo de sincronização confirma a exportação do objeto por reimportar o objeto da fonte de dados conectada. Objetos de exportação ficam importar objetos quando o mecanismo de sincronização recebe-los durante a importação próxima fonte de dados conectada.

### <a name="placeholders"></a>Espaços reservados
O mecanismo de sincronização usa um namespace simples para armazenar objetos. Entretanto, algumas fontes de dados conectados como Active Directory usam um namespace hierárquico. Para transformar informações de um namespace hierárquico em um namespace simples, o mecanismo de sincronização usa espaços reservados para preservar a hierarquia.

Cada espaço reservado representa um componente (por exemplo, uma unidade organizacional) de um nome de objeto hierárquicos que não foi importado para o mecanismo de sincronização, mas é necessária para construir o nome hierárquico. Eles preenchem lacunas criadas por referências na fonte de dados conectados a objetos que não são objetos no espaço do conector de teste.

O mecanismo de sincronização também usa espaços reservados para armazenar objetos referenciados que ainda não foram importados. Por exemplo, se a sincronização está configurada para incluir o atributo de Gerenciador para o objeto *Abbie Silva* e o valor recebido é um objeto que não foi importado ainda, como *CN = Lee Sperry, CN = usuários, DC = fabrikam, DC = com*, as informações do Gerenciador estão armazenadas como espaços reservados no espaço do conector. Se o objeto Gerenciador posterior é importado, o objeto de espaço reservado será substituído pelo objeto de preparação que representa o gerente.

### <a name="metaverse-objects"></a>Objetos do metaverso
Um objeto metaverso contém o modo de exibição agregado tem esse mecanismo de sincronização dos objetos preparação no espaço do conector. Mecanismo de sincronização cria objetos do metaverso usando as informações em objetos de importação. Vários objetos de espaço de conector podem ser vinculados a um objeto metaverso simples, mas um objeto de espaço do conector não pode ser vinculado a mais de um objeto metaverso.

Objetos do metaverso não podem ser criados ou excluídos manualmente. O mecanismo de sincronização exclui automaticamente os objetos do metaverso que não têm um link para qualquer objeto de espaço do conector no espaço de conector.

Para mapear objetos dentro de uma fonte de dados conectada para um tipo de objeto correspondente dentro metaverso, o mecanismo de sincronização fornece um esquema extensível com um conjunto predefinido de tipos de objeto e atributos associados. Você pode criar novos tipos de objeto e atributos para objetos do metaverso. Atributos podem ser único ou vários valores, e os tipos de atributo podem ser strings, referências, números e valores booleanos.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relações entre objetos preparação e metaverso
Dentro do namespace do mecanismo de sincronização, o fluxo de dados é habilitado pela relação de vínculo entre objetos preparação e metaverso. Um objeto de teste que esteja vinculado a um objeto metaverso é chamado de **unidas objeto** (ou **o objeto de conector**). Um objeto de teste que não está vinculado a um objeto metaverso é chamado de **removido objeto** (ou **objeto desconectores**). Os termos associados e removido são preferenciais não confundir com os conectores responsáveis por importar e exportar dados de um diretório conectado.

Espaços reservados nunca são vinculados a um objeto metaverso

Um objeto Unido consiste em um objeto de preparação e sua relação vinculada a um objeto metaverso único. Objetos Unidos são usados para sincronizar valores de atributo entre um objeto de espaço do conector e um objeto do metaverso.

Quando um objeto de teste se torna um objeto Unido durante a sincronização, atributos podem fluir entre o objeto de preparação e o objeto do metaverso. Fluxo de atributo é bidirecional e é configurado usando regras de atributo de importação e exportação atributo.

Um objeto de espaço do conector único pode ser vinculado a apenas um objeto metaverso. No entanto, cada objeto metaverso pode ser vinculado a vários objetos de espaço de conector na mesma ou em espaços de conector diferente, conforme mostrado na ilustração a seguir.

![Arch5](./media/active-directory-aadconnectsync-understanding-architecture/arch5.png)

A relação vinculada entre o objeto preparação e um objeto metaverso é persistente e pode ser removida apenas por regras que você especificar.

Um objeto separado é um objeto de teste que não está vinculado a qualquer objeto metaverso. O atributo valores de um objeto separado não são processadas qualquer dentro metaverso. Os valores de atributo do objeto correspondente na fonte de dados conectada não são atualizados pelo mecanismo de sincronização.

Usando objetos separados, você pode armazenar informações de identidade no mecanismo de sincronização e processá-la mais tarde. Manter um objeto de teste como um objeto separado no espaço do conector tem muitas vantagens. Porque o sistema já testou as informações necessárias sobre esse objeto, não é necessário criar uma representação deste objeto novamente durante a importação de próxima da fonte de dados conectada. Dessa forma, o mecanismo de sincronização sempre tem um instantâneo completo da fonte de dados conectados, mesmo se não houver nenhuma conexão atual com a fonte de dados conectados. Objetos separados podem ser convertidos em objetos Unidos e vice-versa, dependendo das regras que você especificar.

Um objeto de importação é criado como um objeto separado. Um objeto de exportação deve ser um objeto Unido. A lógica de sistema aplica esta regra e exclui cada objeto de exportação que não é um objeto Unido.

## <a name="sync-engine-identity-management-process"></a>Processo de gerenciamento de identidade de mecanismo de sincronização
O processo de gerenciamento de identidade controla como as informações de identidade são atualizadas entre diferentes conectada fontes de dados. Gerenciamento de identidades ocorre em três processos:

- Importar
- Sincronização
- Exportar

Durante o processo de importação, o mecanismo de sincronização avalia as informações de identidade de entrada de uma fonte de dados conectados. Quando são detectadas alterações, ele cria novos objetos de preparação ou atualiza objetos de preparação existentes no espaço do conector para sincronização.

Durante o processo de sincronização, o mecanismo de sincronização atualiza o metaverso para refletir as alterações que ocorreram no espaço do conector e atualiza o espaço de conector para refletir as alterações que ocorreram no metaverso.

Durante o processo de exportação, o mecanismo de sincronização envia alterações que são preparados em objetos de teste e que estão sinalizados como pendentes exportação.

A ilustração a seguir mostra onde cada um dos processos ocorre como fluxos de informações de identidade de uma fonte de dados conectados a outro.

![Arch6](./media/active-directory-aadconnectsync-understanding-architecture/arch6.png)

### <a name="import-process"></a>Processo de importação
Durante o processo de importação, o mecanismo de sincronização avalia atualizações de informações de identidade. Mecanismo de sincronização compara as informações de identidade recebidas da fonte de dados conectados com as informações de identidade sobre um objeto de preparação e determina se o objeto preparação requer atualizações. Se for necessário atualizar o objeto de teste com novos dados, o objeto preparação é sinalizado como pendentes importação.

Preparar objetos no espaço do conector antes da sincronização, mecanismo de sincronização pode processar apenas as informações de identidade que foi alterado. Esse processo fornece os seguintes benefícios:

- **Sincronização eficiente**. A quantidade de dados processados durante a sincronização é minimizada.
- **Nova sincronização eficiente**. Você pode alterar como o mecanismo de sincronização processa informações de identidade sem reconectando o mecanismo de sincronização à fonte de dados.
- **Oportunidade para visualizar a sincronização**. Você pode visualizar sincronização para verificar se as suposições sobre o processo de gerenciamento de identidade estão corretas.

Para cada objeto especificado no conector, o mecanismo de sincronização primeiro tenta localizar uma representação do objeto no espaço do conector do conector. Mecanismo de sincronização examina todos os objetos de preparação no espaço do conector e tenta localizar um objeto de preparação correspondente que tem um atributo de âncora correspondente. Se nenhum objeto de preparação existente tiver um atributo de âncora correspondente, o mecanismo de sincronização tenta encontrar um objeto de preparação correspondente com o mesmo nome diferenciado.

Quando o mecanismo de sincronização de encontra um objeto de preparação que corresponde por nome distinto, mas não por âncora, ocorre o seguinte comportamento especial:

- Se o objeto localizado no espaço do conector não tem nenhum âncora, o mecanismo de sincronização remove esse objeto do espaço do conector e marca o objeto metaverso que é vinculado ao como **Repetir provisionamento na próxima sincronização executar**. Em seguida, ele cria o novo objeto de importação.
- Se o objeto localizado no espaço do conector tem uma âncora, mecanismo de sincronização supõe que esse objeto foi renomeado ou excluído no diretório conectado. Ele atribui um nome distinto temporário, novo para o objeto de espaço do conector para que ele pode testar o objeto de entrada. O objeto antigo se tornará **temporárias**, aguardando o conector importar a renomeação ou a exclusão para resolver a situação.

Se o mecanismo de sincronização localiza um objeto temporário que corresponde ao objeto especificado no conector, ele determina que tipo de aplicar alterações. Por exemplo, o mecanismo de sincronização pode renomear ou excluir o objeto da fonte de dados conectada ou ele pode atualizar somente valores de atributo do objeto.

Objetos de teste com dados atualizados são marcados como pendentes importação. Tipos diferentes de pendente importações estão disponíveis. Dependendo o resultado do processo de importação, um objeto de preparação no espaço do conector tem um dos seguintes pendentes tipos de importação:

- **Nenhum**. Nenhuma alteração para qualquer um dos atributos do objeto preparação está disponíveis. Mecanismo de sincronização não sinalizador desse tipo como pendentes importação.
- **Adicionar**. O objeto preparação é um novo objeto de importação no espaço do conector. Mecanismo de Sincronização sinalizará esse tipo como pendentes importação para processamento adicional no metaverso.
- **Atualização**. Mecanismo de sincronização localiza um objeto de preparação correspondente no espaço do conector e sinalizadores este tipo como pendentes importadas para que as atualizações para os atributos podem ser processadas no metaverso. As atualizações incluem renomeação de objeto.
- **Excluir**. Mecanismo de sincronização localiza um objeto de preparação correspondente no espaço do conector e sinalizadores este tipo como pendentes importadas para que o objeto Unido pode ser excluído.
- **Excluir/adicionar**. Mecanismo de sincronização localiza um objeto de preparação correspondente no espaço do conector, mas não correspondem a tipos de objeto. Nesse caso, um excluir-adicionar modificação é transferida. A exclusão-adicionar modificação indica para o mecanismo de sincronização que uma nova sincronização completa deste objeto deve ocorrer porque diferentes conjuntos de regras de aplicam a esse objeto quando o objeto digite alterações.

Definindo o status de importação pendente de um objeto de preparação, é possível reduzir significativamente a quantidade de dados processados durante a sincronização, pois isso permite que o sistema processar apenas os objetos que têm dados atualizados.

### <a name="synchronization-process"></a>Processo de sincronização
Sincronização consiste em dois processos relacionados:

- Entrada sincronização, quando o conteúdo do metaverso é atualizado usando os dados no espaço de conector.
- Sincronização saída, quando o conteúdo do espaço de conector é atualizado usando dados no metaverso.

Usando as informações testadas no espaço do conector, o processo de entrada de sincronização cria no metaverso o modo de exibição integrado dos dados que estão armazenados em fontes de dados conectados. Todos os objetos de preparação ou apenas aqueles com uma pendente importar informações são agregadas, dependendo de como as regras são configuradas.

As atualizações do processo de sincronização de saída exportar objetos quando alterar objetos do metaverso.

Sincronização de entrada cria o modo de exibição integrado no metaverso as informações de identidade que é recebido de fontes de dados conectados. Mecanismo de sincronização pode processar informações de identidade a qualquer momento usando as informações mais recentes de identidade que possui da fonte de dados conectada.

**Sincronização de entrada**

Sincronização de entrada inclui os seguintes processos:

- **Provisionar** (também chamado de **projeção** se for importante distinguir este processo de provisionamento de sincronização de saída). O mecanismo de sincronização cria um novo objeto metaverso com base em um objeto de preparação e links-los. Provisionar é uma operação de nível do objeto.
- **Ingressar**. O mecanismo de sincronização vincula um objeto de preparação para um objeto metaverso existente. Uma junção é uma operação de nível do objeto.
- **Fluxo de atributo de importação**. Mecanismo de sincronização atualiza os valores de atributo, chamados de fluxo de atributo, do objeto no metaverso. Fluxo de atributo de importação é uma operação de nível de atributo que requer um vínculo entre um objeto de teste e um objeto do metaverso.

Provisionar é o único processo que cria objetos no metaverso. Provisionar afeta apenas os objetos de importação que são objetos separados. Durante a provisionar, o mecanismo de sincronização cria um objeto de metaverso que corresponde ao tipo de objeto do objeto de importação e estabelece um vínculo entre os dois objetos, portanto, criando um objeto Unido.

O processo de junção também estabelece um vínculo entre um objeto metaverso e objetos de importação. A diferença entre associação e provisionar é que o processo de junção requer que o objeto de importação estão vinculados a um objeto metaverso existente, onde o processo de provisionamento cria um novo objeto metaverso.

Mecanismo de sincronização tenta ingressar em um objeto de importação para um objeto do metaverso usando critérios que é especificado na configuração da regra de sincronização.

Durante os processos de provisionar e ingressar, o mecanismo de sincronização vincula um objeto separado a um objeto metaverso, facilitando ingressou. Depois que essas operações de nível de objeto forem concluídas, o mecanismo de sincronização pode atualizar os valores de atributo do objeto metaverso associado. Esse processo é chamado de fluxo de atributo de importação.

Fluxo de atributo de importação ocorre em todos os objetos de importação que contêm dados novo e são vinculados a um objeto metaverso.

**Sincronização de saída**

Atualizações de sincronização saída exportar objetos quando um objeto metaverso alterar, mas não é excluído. O objetivo da sincronização de saída é avaliar se alterações em objetos do metaverso exigem atualizações de preparação objetos nos espaços de conector. Em alguns casos, as alterações podem exigir que preparação objetos em todos os espaços de conector será atualizado. Objetos de teste que são alterados são sinalizados como pendentes exportação, facilitando a exportar objetos. Estes objetos posteriormente são enviados para a fonte de dados conectada durante o processo de exportação de exportação.

Sincronização de saída tem três processos:

- **Provisionamento**
- **Desprovisionamento**
- **Exportar fluxo de atributo**

Provisionamento e desprovisionamento são ambas as operações de nível do objeto. Desprovisionamento depende de provisionamento como provisionamento só pode iniciá-lo. Desprovisionamento é disparada quando provisionamento remove o vínculo entre um objeto metaverso e um objeto de exportação.

Provisionamento sempre é disparado quando as alterações são aplicadas aos objetos do metaverso. Quando forem feitas alterações aos objetos do metaverso, o mecanismo de sincronização pode executar as seguintes tarefas como parte do processo de provisionamento:

- Crie objetos Unidos, onde um objeto metaverso está vinculado a um objeto de exportação recém-criado.
- Renomear um objeto Unido.
- Disjoin links entre um objeto metaverso e preparando objetos, criando um objeto separado.

Se provisionamento requer o mecanismo de sincronização para criar um novo objeto de conector, o objeto preparação aos quais o objeto do metaverso está vinculado é sempre um objeto de exportação, porque o objeto não existir ainda na fonte de dados conectados.

Se provisionamento requer o mecanismo de sincronização para disjoin um objeto Unido, criando um objeto separado, desprovisionamento é disparada. O processo de desprovisionamento exclui o objeto.

Durante desprovisionamento, excluir um objeto de exportação não física excluir o objeto. O objeto é sinalizado como **excluído**, o que significa que a operação de exclusão foi testada no objeto.

Fluxo de atributo de exportação também ocorre durante o processo de sincronização de saída, semelhante a forma que o fluxo de atributo de importação ocorre durante a sincronização de entrada. Fluxo de atributo de exportação ocorre somente entre metaverso e exportar objetos que fazem parte.

### <a name="export-process"></a>Processo de exportação
Durante o processo de exportação, o mecanismo de sincronização examina todos os objetos de exportação que estão sinalizados como pendentes exportação no espaço do conector e envia atualizações para a fonte de dados conectados.

O mecanismo de sincronização pode determinar o sucesso da exportação mas suficientemente não é possível determinar que o processo de gerenciamento de identidade é concluído. Objetos na fonte de dados conectada sempre podem ser alterados por outros processos. Porque o mecanismo de sincronização não tem uma conexão persistente com a fonte de dados conectados, não é suficiente para fazer suposições sobre as propriedades de um objeto na fonte de dados conectados com base apenas em uma notificação de exportação com êxito.

Por exemplo, um processo na fonte de dados conectada pode alterar os atributos do objeto para seus valores originais (ou seja, a fonte de dados conectada pode substituir os valores imediatamente após os dados são enviados pelo mecanismo de sincronização e aplicados com êxito na fonte de dados conectada).

Os armazenamentos de mecanismo de sincronização exportar e importar informações de status sobre cada objeto preparação. Se os valores dos atributos que são especificados na lista de inclusão de atributo foram alterados desde a última exportação, o armazenamento de importar e Exportar status permite que o mecanismo de sincronização para reagir adequadamente. Mecanismo de sincronização usa o processo de importação para confirmar os valores de atributo que foram exportados com a fonte de dados conectados. Uma comparação entre as informações importadas e exportadas, conforme mostrado na ilustração a seguir, permite que o mecanismo de sincronização determinar se a exportação foi bem-sucedida ou se ele precisa ser repetido.

![Arch7](./media/active-directory-aadconnectsync-understanding-architecture/arch7.png)

Por exemplo, se o mecanismo de sincronização exporta atributo C, que tem um valor de 5, a uma fonte de dados conectados, ele armazena C = 5 na sua memória de status de exportação. Cada exportação adicionais sobre este resultados de objeto em uma tentativa de exportar C = 5 à fonte de dados conectada novamente porque o mecanismo de sincronização supõe que esse valor não foi constantemente aplicado ao objeto (ou seja, a menos que um valor diferente foi importado recentemente da fonte de dados conectada). A memória de exportação está desmarcada quando C = 5 é recebido durante uma operação de importação do objeto.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
