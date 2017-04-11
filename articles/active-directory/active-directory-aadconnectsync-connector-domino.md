<properties
   pageTitle="Conector do Lotus Domino | Microsoft Azure"
   description="Este artigo descreve como configurar Lotus Domino conector da Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="lotus-domino-connector-technical-reference"></a>Referência técnica do Lotus Domino conector
Este artigo descreve o conector do Lotus Domino. O artigo aplica-se aos seguintes produtos:

- Gerenciador de identidades do Microsoft 2016 (MIM2016)
- Gerenciador de identidades Forefront 2010 R2 (FIM2010R2)
    -   Deve usar hotfix 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2, o conector está disponível como um download a partir do [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-lotus-domino-connector"></a>Visão geral do conector Lotus Domino
O conector do Lotus Domino permite integrar o serviço de sincronização com o servidor do IBM Lotus Domino.

De uma perspectiva de alto nível, os seguintes recursos são suportados pela versão atual do conector:

Recurso | Suporte
--- | ---
Fonte de dados conectada | Servidor: <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9</li>Cliente:<li>9 do Lotus Notes</li>
Cenários | <li>Gerenciamento de ciclo de vida do objeto</li><li>Gerenciamento de grupo</li><li>Gerenciamento de senha</li>
Operações | <li>Completo e importação de Delta</li><li>Exportar</li><li>Definir e alterar senha em senha HTTP</li>
Esquema | <li>Pessoa (usuário móvel, contato (pessoas com nenhum certificado))</li><li>Grupo</li><li>Recurso (Reunião Online do recurso, sala)</li><li>Mail no banco de dados</li><li>Descoberta dinâmica dos atributos de objetos com suporte</li>

O conector Lotus Domino usa o cliente do Lotus Notes para se comunicar com o servidor Lotus Domino. Como resultado dessa dependência, um Lotus Notes Client com suporte deve ser instalado no servidor de sincronização. A comunicação entre o cliente e o servidor é implementada por meio da interface de interoperabilidade de .NET do Lotus Notes (Interop.domino.dll). Essa interface facilita a comunicação entre o Microsoft.NET plataforma e o cliente do Lotus Notes e oferece suporte ao acesso a documentos do Lotus Domino e modos de exibição. Para importação de delta, também é possível que a interface nativa C++ é usada (dependendo do método de importação de delta selecionada).

### <a name="prerequisites"></a>Pré-requisitos
Antes de usar o conector, verifique se que você tem o seguinte no servidor de sincronização:

- 4.5.2 do Microsoft .NET Framework ou posterior
- O cliente do Lotus Notes deve ser instalado no seu servidor de sincronização
- O conector do Lotus Domino requer Lotus Domino LDAP esquema banco de dados padrão (schema.nsf) esteja presente no servidor de diretório Domino. Se não estiver presente, você pode instalá-lo executando ou reiniciar o serviço LDAP no servidor Domino.

### <a name="connected-data-source-permissions"></a>Permissões de fonte de dados conectadas
Para executar qualquer uma das tarefas com suporte no conector Lotus Domino, você deve ser um membro dos seguintes grupos:

- Administradores de acesso completos
- Administradores
- Administradores de banco de dados

A tabela a seguir lista as permissões que são necessárias para cada operação:

Operação | Direitos de acesso
--- | ---
Importar | <li>Ler documentos públicos</li><li> Total acesso de administrador (quando você for membro do grupo de administradores de acesso total, você automaticamente necessário acesso eficaz para na ACL).</li>
Exportar e definir senha | Acesso eficaz: <li>Criar documentos</li><li>Excluir documentos</li><li>Ler documentos públicos</li><li>Escrever documentos públicos</li><li>Duplicar ou copiar documentos</li>Para operações de exportação, você também precisa funções a seguir: <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li>

### <a name="direct-operations-and-adminp"></a>Operações diretas e AdminP
Operações ou vá diretamente para o diretório Domino ou durante o processo de AdminP. As tabelas a seguir objetos de lista todos os suportados, operações e, se aplicável, o método de implementação relacionados:

**Catálogo de endereço principal**

Objeto | Criar | Atualização | Excluir
--- | --- | --- | ---
Pessoa | AdminP | Direto | AdminP
Grupo | AdminP | Direto | AdminP
MailInDB | Direto | Direto | Direto
Recurso | AdminP | Direto | AdminP

**Catálogo de endereços secundário**

Objeto | Criar | Atualização | Excluir
--- | --- | --- | ---
Pessoa | N/D | Direto | Direto
Grupo | Direto | Direto | Direto
MailInDB | Direto | Direto | Direto
Recurso | N/D | N/D | N/D

Quando um recurso é criado, um documento de anotações será criado. Da mesma forma, quando um recurso é excluído, o documento de anotações é excluído.

### <a name="ports-and-protocols"></a>Portas e protocolos
Cliente do IBM Lotus Notes e servidores Domino se comunicar usando anotações remoto procedimento chamar (NRPC) onde NRPC devem usar TCP/IP. O número da porta padrão é 1352, mas pode ser alterado pelo administrador do Domino.

### <a name="not-supported"></a>Sem suporte
As seguintes operações não são suportadas pela versão atual do conector Lotus Domino:

- Mova a caixa de correio entre servidores.

## <a name="create-a-new-connector"></a>Criar um novo conector

### <a name="client-software-installation-and-configuration"></a>Configuração e instalação de Software do cliente
Lotus Notes deve ser instalado o servidor **antes do** que conector é instalado.

Quando você instala, faça um **Único usuário instalar**. O padrão **Multiusuário instalar** não funciona.  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

Na página recursos, instale apenas os recursos necessários do Lotus Notes e o **Logon único do cliente**. Logon único é necessário para o conector sejam capazes de fazer logon no servidor Domino.  
![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Observação:** Inicie o Lotus Notes uma vez com um usuário que está localizado no mesmo servidor que a conta usada como conta de serviço do conector. Verifique também se fechar o cliente do Lotus Notes no servidor. Ele não pode estar executando ao mesmo tempo que o conector tenta se conectar ao servidor Domino.

### <a name="create-connector"></a>Criar conector
Para criar um conector Lotus Domino, no **Serviço de sincronização** , selecione **Agente de gerenciamento** e **criar**. Selecione o conector **Lotus Domino (Microsoft)** .  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Se sua versão do serviço de sincronização oferece a capacidade de configurar **arquitetura**, verifique se que o conector está definido para o valor padrão para executar o **processo**.

### <a name="connectivity"></a>Conectividade
Na página conectividade, você deve especificar o nome do servidor Lotus Domino e insira as credenciais de logon.  
![Conectividade](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

A propriedade de servidor Domino oferece suporte a dois formatos para o nome do servidor:

- Nomedoservidor
- NomeServidor/DirectoryName

O formato de **NomeServidor/DirectoryName** é o formato preferido para esse atributo porque ele fornece uma resposta mais rápida quando o conector contata o servidor Domino.

O arquivo de ID de usuário fornecido está armazenado no banco de dados de configuração do serviço de sincronização.

Para **Importação de Delta** você tem estas opções:

- **Nenhum**. O conector não importações delta.
- **Adicionar/atualizar**. A importação de delta do conector faz adicionar e atualizar operações. Para excluir uma operação de **Importação completa** é necessária. Esta operação está usando a interoperabilidade .net.
- **Adicionar/atualizar/excluir**. A importação de delta do conector faz adicionar, atualizar e excluir operações. Esta operação está usando as interfaces C++ nativas.

Nas **Opções de esquema** que você tem as seguintes opções:

- **Padrão de esquema**. O conector detecta o esquema do servidor Domino. Esta é a opção padrão.
- **Esquema de DSML**. Usado somente se o servidor Domino não expõem o esquema. Em seguida, você pode criar um arquivo DSML com o esquema e importá-las. Para obter mais informações sobre DSML, consulte [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

Quando você clicar em Avançar, os parâmetros de configuração de ID de usuário e senha são verificados.

### <a name="global-parameters"></a>Parâmetros globais
Na página de parâmetros globais, configure o fuso horário e importar e exportar a opção de operação.  
![Parâmetros globais](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

O parâmetro de **fuso horário do Domino servidor** define o local do seu servidor Domino.

Esta opção de configuração é necessária para dar suporte a operações de **importação de delta** porque ela permite a sincronização serviço determinar alterações entre as duas últimas importações.

#### <a name="import-settings-method"></a>Configurações de importação, método
A **Executar importação completa por** tem estas opções:

- Pesquisa
- Modo de exibição (recomendado)

**Pesquisa** está usando a indexação no Domino, mas é comum que os índices não são atualizados em tempo real e os dados retornados do servidor nem sempre são corretos. Para um sistema com muitas alterações, essa opção geralmente não funciona bem e fornece falso exclui em algumas situações. No entanto, **pesquisa** é mais rápido que o **modo de exibição**.

**Modo de exibição** é a opção recomendada, pois ele fornece o estado correto dos dados. É um pouco mais lento do que a pesquisa.

#### <a name="creation-of-virtual-contact-objects"></a>Criação de objetos de visita Virtual
O **habilita a criação de \_objeto contato** possui as seguintes opções:

- Nenhum
- Valores não-referência
- Valores de referência e não

No Domino, atributos de referência podem conter vários formatos diferentes para fazer referência a outros objetos. Para poder representar diferentes variações, implementa o conector \_objetos, também conhecido como **Contatos Virtual** (VC) de contato. Estes objetos são criados para que eles possam ingressar aos objetos MV existentes ou projetados como novos objetos. Dessa maneira, referências de atributo podem ser preservadas.

Habilitando essa configuração e se o conteúdo de um atributo de referência não é um formato de DN, uma \_objeto contato é criado. Por exemplo, um atributo de membro de um grupo pode conter endereços SMTP. Também é possível ter shortName e outros atributos presentes nos atributos de referência. Para esse cenário, selecione **Valores não-referência**. Esta configuração é a configuração mais comum para implementações Domino.

Quando Lotus Domino é configurado para ter catálogos de endereços separada com diferentes nomes distintos que representa o mesmo objeto, é possível também criar \_objetos de contato para todos os valores de referência que são encontrados em um catálogo de endereços. Para esse cenário, selecione a opção de **referência e valores não-referência** .

Se você tiver vários valores no atributo **FullName** Domino, em seguida, você também queira permitem a criação de contatos Virtual para que referências podem ser resolvidas. Por exemplo, esse atributo pode ter vários valores após um casamento ou Divórcio. Selecione a caixa de seleção Habilitar **... FullName tem vários valores** para esse cenário.

Unindo os atributos corretos, o \_objetos de contato seriam ser associados ao objeto MV.

Esses objetos têm VC =\_contato adicionado à sua DN.

#### <a name="import-settings-conflict-object"></a>Importar configurações, objeto em conflito
**Excluir o objeto de conflito**

Em uma implementação Domino grande, é possível que vários objetos têm o mesmo DN devido a problemas de replicação. Nesses casos, o conector verá dois objetos com UniversalIDs diferente, mas mesmo DN. Esse conflito resultaria em um objeto temporário sendo criado no espaço do conector. O conector pode ignorar os objetos que foram selecionados no Domino como vítimas de replicação. A recomendação é manter essa caixa de seleção selecionada.

#### <a name="export-settings"></a>Exportar configurações
Se a opção **Usar AdminP para atualizar referências** estiver desmarcada, exportação de atributos de referência, como membro, é uma chamada direta e não usam o processo de AdminP. Somente use essa opção quando AdminP não tiver sido configurado para manter a integridade referencial.

#### <a name="routing-information"></a>Informações de roteamento
Em Domino, é possível que um atributo de referência tenha inseridas como um sufixo para o DN de informações de roteamento. Por exemplo, o atributo de membro em um grupo pode conter **CN=example/organization@ABC**. O sufixo @ABC é as informações de roteamento. As informações de roteamento são usadas pelo Domino para enviar emails para o sistema Domino correto, o que poderia ser um sistema em uma organização diferente. No campo de informações de roteamento, você pode especificar o roteamento de sufixos usada dentro da organização no escopo do conector. Se um destes valores for encontrado como sufixo em um atributo de referência, as informações de roteamento serão removidas da referência. Se o sufixo roteamento em um valor de referência não pode ser correspondido a um desses valores especificados, um \_objeto contato é criado. Estes \_objetos de contato são criados com ** RO=@ ** inserido no DN. Para esses \_objetos de contato dos atributos a seguir também são adicionados ao permitir ingressando em um objeto real, se necessário: \_routingName, \_contactName, \_displayName e UniversalID.

#### <a name="additional-address-books"></a>Catálogos de endereços adicionais
Se você não tiver **auxílio** instalado, que fornece o nome de catálogos de endereços secundário, em seguida, você pode inserir manualmente esses catálogos de endereços.

#### <a name="multivalued-transformation"></a>Transformação de múltiplos valores
Muitos atributos Lotus Domino são múltiplos. Os atributos de metaverso correspondente são normalmente único importantes. Configurando a importação e a opção de operação de exportação, você deve ativar o conector para ajudá-lo com a tradução necessária dos atributos afetados.

**Exportar**  
A opção de operação de exportação suporta dois modos:

- Anexar item
- Substituir item

**Substituir Item** – quando você selecionar essa opção, o conector sempre remover os valores atuais do atributo no Domino e substituí-los com os valores fornecidos. Fornecida com valor pode ser um único valor ou múltiplos.

Exemplo: O atributo de Assistente do objeto de uma pessoa tem os seguintes valores:

- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Se um novo assistente chamado **David Alexander** é atribuído a esse objeto de pessoa, o resultado é:

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf

**Anexar Item** – quando você selecionar essa opção, o conector retém os valores existentes no atributo Domino e insira novos valores na parte superior da lista de dados.

Exemplo: O atributo de Assistente do objeto de uma pessoa tem os seguintes valores:

- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Se um novo assistente chamado **David Alexander** é atribuído a esse objeto de pessoa, o resultado é:

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

**Importar**  
A opção de operação de importação suporta dois modos:

- Padrão
- Múltiplos valores como valor único

**Padrão** – quando você selecionar a opção padrão, todos os valores de todos os atributos são importados.

**Multivalued único valor** – quando você selecionar essa opção, um atributo de valores múltiplos é convertido em um atributo de valor único. Se houver mais de um valor, o valor na parte superior (esse valor é normalmente também a mais recente) será usado.

Exemplo: O atributo de Assistente do objeto de uma pessoa tem os seguintes valores:

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

A atualização mais recente para esse atributo está **David Alexander**. Como a opção de operação de importação é definida como Multivalued único valor, o conector importa somente **David Alexander** no espaço do conector.

Não se aplicam a lógica para converter valores múltiplos atributos em atributos de valor único para o atributo de membro do grupo e para o atributo de fullname pessoa.

Ele também é possível configurar importar e exportar regras de transformação de valores múltiplos atributos por atributo, como uma exceção à regra global. Para configurar essa opção, digite [objecttype]. [attributename] nas caixas de texto de **lista de exclusão de atributo de importação** e **exportação lista de atributo de exclusão** . Por exemplo, se você inserir Person.Assistant e o sinalizador global está definido para importar todos os valores, somente o primeiro valor é importado para o assistente.

#### <a name="certifiers"></a>Certifiers
Todas as unidades de organização/organizacional são listadas pelo conector. Para poder exportar objetos de pessoa para o catálogo de endereço principal, é necessário um certifier com sua senha.

Se todos os certifiers tem a mesma senha, a **senha para todos os Certifers** podem ser usados. Então você poderá insira a senha aqui e especifique apenas o arquivo de certifier.

Se você importar apenas, então você não tenha especificar qualquer certifiers.

### <a name="configure-provisioning-hierarchy"></a>Configurar a hierarquia de provisionamento
Quando você configura o conector Lotus Domino, pule esta página de diálogo. O conector Lotus Domino não oferece suporte a hierarquia de provisionamento.  
![Hierarquia de provisionamento](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurar partições e hierarquias
Quando você configura partições e hierarquias, você deve selecionar o catálogo de endereços primário chamado NAB=names.nsf. Além de catálogo de endereço principal, você pode selecionar catálogos de endereços secundário se existirem.  
![Partições](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>Selecione atributos
Quando você configura seus atributos, você deve selecionar todos os atributos que são precedidos com ** \_MMS\_**. Esses atributos são necessários quando você provisionar novos objetos para Lotus Domino

![Atributos](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>Gerenciamento de ciclo de vida do objeto
Esta seção fornece uma visão geral dos objetos diferentes no Domino.

### <a name="person-objects"></a>Objetos de pessoa
O objeto de pessoa representa os usuários na organização e unidades de organização. Além dos atributos padrão, o administrador do Domino pode adicionar atributos personalizados para o objeto de uma pessoa. No mínimo, o objeto de uma pessoa deve incluir todos os atributos obrigatórios. Para obter uma lista completa de atributos obrigatórios, consulte [Propriedades do Lotus Notes](#lotus-notes-properties). Para registrar o objeto de uma pessoa, os seguintes pré-requisitos devem ser atendidos:

- O catálogo de endereços (names.nsf) deve foram definido e deve ser o catálogo de endereço principal.
- Você deve ter o certifier O/OU Id e a senha para registrar um determinado usuário na organização / unidade organizacional.
- Você deve definir um conjunto específico de propriedades do Lotus Notes para o objeto de uma pessoa. Essas propriedades são usadas para o objeto de pessoa de provisionamento. Para obter mais detalhes, consulte a seção chamada [Propriedades do Lotus Notes](#lotus-notes-properties) posteriormente neste documento.
- A senha HTTP inicial para uma pessoa é um atributo e um conjunto durante a configuração.
- O objeto de pessoa deve ser um dos seguintes três tipos suportados:
    1. Usuário normal com um arquivo de email e um arquivo de id de usuário
    2. Usuário (um usuário Normal que inclui todos os arquivos de banco de dados móvel) móvel
    3. Contatos (usuário com nenhum arquivo id)

Pessoas (exceto contatos) ainda mais podem ser agrupadas nos usuários e internacionais conforme definido pelo valor do \_MMS\_IDRegType propriedade. Estas pessoas usam o cliente Notes para acessar servidores Lotus Domino, têm um Id de anotações e um documento de pessoa. Se elas estiverem usando o email de anotações, depois que eles também têm um arquivo de email. O usuário deve ser registrado para se tornar ativa. Para obter mais informações, consulte:

- [Configuração de usuários de anotações](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
- [Registro de usuário](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
- [Gerenciamento de usuários](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
- [A renomeação de usuários](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Todas essas operações são executadas no Lotus Domino e, em seguida, importadas para o serviço de sincronização.

### <a name="resources-and-rooms"></a>Salas e recursos
Um recurso é outro tipo de um banco de dados no Lotus Domino. Recursos podem ser salas de conferência com vários tipos de equipamento, como projetores. Há subtipos de recursos com suporte pelo conector Lotus Domino definidos pelo atributo de tipo de recurso:

Tipo de recurso | Atributo de tipo de recurso
--- | ---
Sala | 1
Recurso (outros) | 2
Reunião online | 3

Para o tipo de objeto do recurso trabalhar, é necessário o seguinte:

- Banco de dados de reserva de recursos já deve existir no servidor Domino conectado
- O site já está definido para o recurso

O banco de dados de reserva de recursos contém três tipos de documentos:

- Perfil de site
- Recurso
- Reserva

Para obter mais detalhes sobre a configuração do banco de dados de reserva de recursos, consulte [Configurar o banco de dados do reservas de recursos](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html).

**Criar, atualizar e excluir recursos**  
As operações de criar, atualizar e excluir são executadas pelo conector Lotus Domino do banco de dados de reserva de recursos. Recursos são criados como documentos no Names.nsf (ou seja, o catálogo de endereço principal). Para obter mais detalhes sobre a edição e exclusão de recursos, consulte [Editar e excluir documentos de recursos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html).

**Importar e exportar operação para recursos**  
Os recursos podem ser importados e exportados do serviço de sincronização, como qualquer outro tipo de objeto. Selecione o tipo de objeto como recurso durante a configuração. Para operação de exportação com êxito, você deve ter detalhes de tipo de recurso, o banco de dados de conferência e o nome do Site.

### <a name="mail-in-databases"></a>Mail bancos de dados
Um banco de dados de email é um banco de dados que foi desenvolvido para receber emails. É uma caixa de correio do Lotus Domino que não está associada a qualquer conta de usuário específica do Lotus Domino (ou seja, ele não tem seu próprio arquivo de ID e senha). Um banco de dados de email tem um único ID de usuário ("nome curto") associada a ele e seu próprio endereço de email.

Se houver uma necessidade de uma caixa de correio separada com seu próprio endereço de email que pode ser compartilhado entre diferentes usuários (por exemplo, group@contoso.com), um banco de dados de correio é criado. O acesso a essa caixa de correio é controlado pelo seu Access Control List (ACL), que contém os nomes dos usuários anotações que são permitidos para abrir a caixa de correio.

Para obter uma lista dos atributos necessários, consulte a seção chamada [Atributos obrigatórios](#mandatory-attributes) neste artigo.

Quando um banco de dados foi projetado para receber um email, um documento de email no banco de dados é criado no Lotus Domino. Este documento deve existir no diretório de Domino de todos os servidores que armazena uma cópia do banco de dados. Para obter uma descrição detalhada sobre a criação de um documento de email no banco de dados, consulte [Criando um documento de email no banco de dados](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html).

Antes de criar um banco de dados de email, o banco de dados já deve existir (deve ter sido criado pelo administrador do Lotus) no servidor Domino.

### <a name="group-management"></a>Gerenciamento de grupo
Você pode obter uma visão geral detalhada do gerenciamento de grupo de Lotus Domino dos seguintes recursos:

- [Usando grupos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
- [Criar um grupo](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
- [Criando e modificando grupos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
- [Gerenciando grupos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
- [Renomear um grupo](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>Gerenciamento de senha
Para um usuário registrado do Lotus Domino, há dois tipos de senhas:

1. Senha do usuário (armazenado no arquivo de User.id)
2. Internet / senha HTTP

O conector Lotus Domino suporta somente operações com senha HTTP.

Para executar o gerenciamento de senha, você deve ativar o gerenciamento de senha para o conector no Designer de agente de gerenciamento. Para ativar o gerenciamento de senha, selecione **Ativar o gerenciamento de senha** na página de diálogo **Configurar extensões** .  
![Configurar extensões](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

O suporte de conector Lotus Domino seguintes operações em senha da Internet:

- Definir senha: Definir senha define uma nova senha HTTP/Internet usuário no Domino. Por padrão, a conta também é desbloqueada. O sinalizador de desbloquear é exposto na interface WMI do mecanismo de sincronização.
- Alterar senha: Neste cenário, um usuário talvez queira alterar a senha ou é solicitado a alterar a senha após um período especificado. Para essa operação levar coloque, tanto (o antigo e a nova senha) são obrigatórias. Depois de alterado, a nova senha é atualizada em Lotus Domino.

Para obter mais informações, consulte:

- [Usando o recurso de bloqueio de Internet](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
- [Gerenciar senhas de Internet](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>Informações de referência
Esta seção lista como requisitos de atributo para o conector Lotus Domino e descrições de atributo.

### <a name="lotus-notes-properties"></a>Propriedades do Lotus Notes
Quando você provisionar objetos de pessoa para seu diretório Lotus Domino, os objetos devem ter um conjunto específico de propriedades com valores específicos preenchidos. Esses valores só são necessários para criar operações.

A tabela a seguir lista essas propriedades e fornece uma descrição deles.

Propriedade | Descrição
--- | ---
\_MMS_AltFullName | O nome completo alternativo do usuário.
\_MMS_AltFullNameLanguage | O idioma a ser usado para especificar o nome completo alternativo do usuário.
\_MMS_CertDaysToExpire | O número de dias entre a data atual antes do certificado expira. Se não especificado, o padrão de data é dois anos a partir da data atual.
\_MMS_Certifier | Propriedade que contém o nome de hierarquia organizacional da certifier. Por exemplo: OU = OrganizationUnit, O = Org, C = país.
\_MMS_IDPath | Se a propriedade estiver vazia, nenhum arquivo de identificação de usuário é criado localmente no servidor de sincronização. Se a propriedade contiver um nome de arquivo, um arquivo de ID de usuário é criado na pasta madata. A propriedade também pode conter um caminho completo.
\_MMS_IDRegType | Pessoas podem ser classificadas como usuários internacionais, nos usuários e contatos. A tabela a seguir lista os valores possíveis: <li>0 - contato</li><li>1 - usuário de US</li><li>2 - usuário internacional</li>
\_MMS_IDStoreType | Propriedade obrigatório para EUA e usuários internacionais. A propriedade contém um valor inteiro que especifica se a identificação de usuário é armazenada como um anexo no catálogo de endereços de anotações ou no arquivo de email da pessoa. Se o arquivo de ID de usuário é um anexo no catálogo de endereços, ele opcionalmente pode ser criado como um arquivo com \_MMS_IDPath. <li>Esvazie - arquivo de ID do repositório no cofre de identificação, nenhum arquivo de identificação (usado para contatos).</li><li> 1 - anexo no catálogo de endereços de anotações. O \_MMS_Password propriedade deve ser definida para arquivos de identificação de usuário que são anexos</li><li>2 - armazenar ID no arquivo de email da pessoa. O \_MMS_UseAdminP deve ser definida como false para permitir que o arquivo de email ser criada durante o registro de pessoa. O \_MMS_Password propriedade deve ser definida para arquivos de identificação de usuário.</li>
\_MMS_MailQuotaSizeLimit | O número de megabytes permitidos para o banco de dados do arquivo de email.
\_MMS_MailQuotaWarningThreshold | O número de megabytes que são permitidos para o banco de dados do arquivo de email antes de um aviso é emitido.
\_MMS_MailTemplateName | O arquivo de modelo de email que é usado para criar o arquivo de email do usuário. Se um modelo for especificado, o arquivo de correio é criado usando o modelo especificado. Se nenhum modelo for especificado, o arquivo de modelo padrão é usado para criar o arquivo.
\_MMS_OU | Propriedade opcional que é o nome de unidade Organizacional sob o certifier. Essa propriedade deve estar vazia para contatos.
\_MMS_Password | Propriedade obrigatório para os usuários. A propriedade contém a senha para o arquivo de identificação do objeto.
\_MMS_UseAdminP | Propriedade deve ser definida como true se o arquivo de email deve ser criado pelo processo de AdminP no servidor Domino (assíncrono para o processo de exportação). Se a propriedade estiver definida como false, o arquivo de email é criado com o usuário Domino (síncrono no processo de exportação).

Para um usuário com um arquivo de identificação associado, o \_MMS_Password propriedade deve conter um valor. Para acessar emails por meio do cliente do Lotus Notes, as propriedades de servidor de email e MailFile de um usuário devem conter um valor.

Para acessar emails por meio de um navegador da Web, as seguintes propriedades devem conter valores:

- MailFile - propriedade obrigatório que contém o caminho no servidor Lotus Domino onde o arquivo de email está armazenado.
- Servidor de email - propriedade obrigatório que contém o nome do servidor Lotus Domino. Esse valor é o nome para usar quando você criou o arquivo de email do Lotus no servidor Domino.
- HTTPPassword - propriedade opcional que contém a senha de acesso da Web para o objeto.

Para acessar o servidor Domino sem o recurso de email, a propriedade HTTPPassword deve conter um valor. A propriedade MailFile e a propriedade de servidor de email podem estar vazios.

Com \_MMS_ IDStoreType = 2 (id de armazenamento no arquivo de email), a propriedade MailSystem de NotesRegistrationclass é definida como REG_MAILSYSTEM_INOTES (3).

### <a name="mandatory-attributes"></a>Atributos obrigatórios
O conector Lotus Domino principalmente oferece suporte a esses tipos de objetos (tipos de documento):

- Grupo
- Mail no banco de dados
- Pessoa
- Contato (pessoa com nenhum certifier)
- Recurso

Esta seção lista os atributos que são obrigatórios para cada objeto com suporte exportar para um servidor Domino.

Tipo de objeto | Atributos obrigatórios
--- | ---
Grupo | <li>ListName</li>
Principal no banco de dados | <li>FullName</li><li>MailFile</li><li>Servidor de email</li><li>MailDomain</li>
Pessoa | <li>Sobrenome</li><li>MailFile</li><li>ShortName</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>\_MMS_UseAdminP</li>
Contato (pessoa com nenhum certifier) | <li>\_MMS_IDRegType</li>
Recurso | <li>FullName</li><li>Tipo de recurso</li><li>ConfDB</li><li>CapacidadeDoRecurso</li><li>Site</li><li>DisplayName</li><li>MailFile</li><li>Servidor de email</li><li>MailDomain</li>

## <a name="common-issues-and-questions"></a>Perguntas e problemas comuns

### <a name="schema-detection-does-not-work"></a>Detecção de esquema não funciona
Para ser capaz de detectar o esquema, é necessário que o arquivo de schema.nsf está presente no servidor Domino. Este arquivo aparece apenas se LDAP estiver instalado no servidor. Se o esquema não estiver detectável, verifique o seguinte:

- Schema.nsf o arquivo está presente na pasta raiz do servidor Domino
- O usuário tem permissões para ver o arquivo de schema.nsf.
- Força a reinicialização do servidor LDAP. Abra o **Console do Lotus Domino** e use o comando de **Dizer LDAP ReloadSchema** recarregar o esquema.

### <a name="not-all-secondary-address-books-are-visible"></a>Nem todos os catálogos de endereços secundários estão visíveis
O conector de Domino depende o recurso de **Assistência de diretório** para poder localizar os catálogos de endereços secundários. Se os catálogos de endereços secundários estão faltando, verifique se [Auxílio](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) foi habilitado e configurado no servidor Domino.

### <a name="custom-attributes-in-domino"></a>Atributos personalizados no Domino
Há várias maneiras Domino para estender o esquema para que ela apareça como um atributo personalizado consumo pelo conector.

**Método 1: Estender Lotus Domino esquema**

1. Criar uma cópia do modelo de diretório Domino {PUBNAMES. NTF} por a seguir [estas etapas](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (você não deve personalizar o diretório do IBM Lotus Domino padrão modelo):
2. Abra o modelo de diretório de cópia do Domino {CONTOSO. Modelo NTF} criado no Designer de Domino e siga estas etapas:
    - Clique em elementos compartilhados e expanda subformulários
    - Clique duas vezes no subformulário de InheritableSchema ${ObjectName} (onde {ObjectName} é o nome da classe de objeto estrutural padrão, por exemplo: pessoa).
    - Nomeie o atributo que você deseja adicionar ao esquema {MyPersonAtrribute} e correspondente para esse atributo. Criar um campo, selecione Menu **criar** e, em seguida, selecione o **campo** de menu.
    - No campo adicionado, defina suas propriedades selecionando seu tipo, estilo, tamanho, fonte e outros parâmetros relacionados na janela Propriedades do campo.
    - Manter o atributo valor padrão mesmo como o nome atribuído para o atributo (por exemplo, se o nome do atributo é MyPersonAttribute, mantenha o valor padrão com o mesmo nome).
    - Salve o subformulário de InheritableSchema ${ObjectName} com valores atualizados.
3. Substituir o modelo de diretório Domino {PUBNAMES. NTF} com o novo modelo personalizado {CONTOSO. NTF} por a seguir [estas etapas](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Feche o administrador do Domino e abra Console de Domino para reiniciar o serviço LDAP e recarregar o esquema de LDAP:
    - No Console de Domino, inserir o comando sob o texto de **Comando Domino** arquivado para reiniciar o serviço LDAP - [Reiniciar LDAP de tarefa]( http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Para recarregar LDAP esquema usar comando LDAP informe - informar ReloadSchema de LDAP
5. Abrir Domino administrador e selecione a guia pessoas e grupos para ver o atributo adicionado é refletido na domino adicionam pessoa.
6. Abrir Schema.nsf da guia **arquivos** e ver o atributo adicionado é refletido na classe de objeto LDAP dominoPerson.

**Método 2: Criar um auxClass com o atributo personalizado e associar a classe de objeto**

1. Criar uma cópia do modelo de diretório Domino {PUBNAMES. NTF} por a seguir [estas etapas](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (nunca personalizar o diretório do IBM Lotus Domino padrão modelo):
2. Abra o modelo de diretório de cópia do Domino {CONTOSO. Modelo NTF} que foi criado, no Designer de Domino.
3. No painel esquerdo, selecione o código compartilhado e subformulários.
4. Clique em novo subformulário
5. Faça o seguinte para especificar as propriedades para o novo subformulário:
    - Com o novo subformulário aberta, escolha Design - propriedades de subformulário
    - Ao lado de propriedade nome, insira um nome para a classe de objeto auxiliar – por exemplo, TestSubform.
    - Manter a propriedade de opções "Incluir no subformulário inserir … diálogo" selecionada
    - Desmarque a opções propriedade "renderização passagem HTML em anotações."
    - Deixe as outras propriedades a mesma e fechar a caixa Propriedades do subformulário.
    - Salve e feche o subformulário novo.
6. Siga este procedimento para adicionar um campo para definir a classe de objeto auxiliar:
    - Abra o subformulário que você criou.
    - Escolha Criar - campo.
    - Ao lado do nome na guia Noções básicas da caixa de diálogo campo, especificar qualquer nome, por exemplo: {MyPersonTestAttribute}.
    - No campo adicionado, defina suas propriedades selecionando seu tipo, estilo, tamanho, fonte e propriedades relacionadas.
    - Manter o atributo valor padrão mesmo como o nome atribuído para o atributo (por exemplo, se o nome do atributo é MyPersonTestAttribute, mantenha o valor padrão com o mesmo nome).
    - Salve o subformulário com valores atualizados e faça o seguinte:
        - No painel esquerdo, selecione o código compartilhado e subformulários
        - Selecione o novo subformulário e escolha Design - propriedades de Design.
        - Clique na guia terceira à esquerda e selecione **Alterar propagar essa proibição do design**.
7. Abra o subformulário de ExtensibleSchema ${ObjectName}, (onde {ObjectName} é o nome da classe de objeto estrutural padrão, por exemplo, – pessoa).
8. Inserir recurso subformulário (que você criou, por exemplo, – TestSubform) e selecione Salvar o subformulário de ExtensibleSchema ${ObjectName}.
9. Substituir o modelo de diretório Domino {PUBNAMES. NTF} com o novo modelo personalizado {CONTOSO. NTF} por a seguir [estas etapas](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Feche o administrador do Domino e abra Console de Domino para reiniciar o serviço LDAP e recarregar o esquema de LDAP:
    - No Console de Domino, inserir o comando sob o texto de **Comando Domino** arquivado para reiniciar o serviço LDAP - [Reiniciar LDAP de tarefa](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Para recarregar LDAP esquema usar comando LDAP informar **Dizer LDAP ReloadSchema**.
11. Abra Domino administrador e selecione guia de pessoas e grupos para ver o atributo adicionado é refletido na domino adicionar pessoa (em outras pessoas guia).
12. Abrir Schema.nsf da guia **arquivos** e ver o atributo adicionado é refletido em TestSubform LDAP auxiliar classe de objeto.

**Abordagem 3: Adicionar o atributo personalizado à classe ExtensibleObject**

1. Abrir arquivo de {Schema.nsf} colocado na pasta raiz
2. Selecione Classes de objeto LDAP no menu à esquerda em **Todos os documentos de esquema** e clique em botão **Adicionar objeto classe** :
3. Fornece nome LDAP na forma de {zzzExtensibleSchema} (onde zzz é o nome da classe de objeto estrutural padrão, por exemplo pessoa). Por exemplo, para estender o esquema de pessoa classe de objeto, forneça o nome LDAP {PersonExtensibleSchema}.
4. Fornece o nome da classe do objeto Superior, para o qual você deseja estender o esquema. Por exemplo, para estender o esquema para classe de objeto de pessoa, forneça o nome da classe de objeto Superior {dominoPerson}:
5. Forneça uma identificação de objeto válida correspondente a classe de objeto.
6. Selecione atributos estendida/personalizados em campos obrigatórios ou tipos de atributo opcional conforme a necessidade:
7. Depois de adicionar os atributos necessários para o ExtensibleObjectClass, clique em **Salvar e fechar**.
8. Um ExtensibleObjectClass é criado para classe de objeto padrão respectivo com atributos estendidos.

## <a name="troubleshooting"></a>Solução de problemas

-   Para obter informações sobre como habilitar o registro em log solucionar o conector, consulte [como habilitar o rastreamento ETW para conectores](http://go.microsoft.com/fwlink/?LinkId=335731).
