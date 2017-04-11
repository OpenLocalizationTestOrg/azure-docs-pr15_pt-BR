<properties 
    pageTitle="Integração de diretório entre autenticação multifator do Azure e o Active Directory"
    description="Esta é a página de autenticação multifator do Azure que descreve como integrar o servidor de autenticação multifator Azure com o Active Directory para que você possa sincronizar os diretórios."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="directory-integration-between-azure-mfa-server-and-active-directory"></a>Integração de diretório entre Azure MFA Server e Active Directory

A seção Integração de diretório permite que você configurar o servidor para integração com o Active Directory ou outro diretório LDAP.  Ele permite que você configure atributos para corresponder o esquema de diretório e configurar a sincronização automática de usuários.

## <a name="settings"></a>Configurações
Por padrão, o servidor de autenticação multifator Azure está configurado para importar ou sincronizar usuários do Active Directory.  A guia permite substituir o comportamento padrão e vincular a um diretório LDAP diferente, um diretório ADAM ou controlador de domínio específico do Active Directory.  Ele também fornece para o uso de autenticação LDAP para proxy LDAP ou ligação LDAP como destino RADIUS, pré-autenticação do IIS autenticação, ou principal para o Portal de usuário.  A tabela a seguir descreve as configurações individuais.

![Configurações](./media/multi-factor-authentication-get-started-server-dirint/dirint.png)

| Recurso | Descrição |
| ------- | ----------- |
| Usar o Active Directory | Selecione a opção de usar o Active Directory para usar o Active Directory para importação e sincronização.  Esta é a configuração padrão. <br>Observação: O computador deve estar associado a um domínio e você deve estar conectado com uma conta de domínio para integração do Active Directory funcione corretamente. |
| Incluir domínios confiáveis | Verificar a caixa de seleção incluir domínios confiáveis tenham o agente tentar se conectar aos domínios confiáveis, domínio atual, outro domínio na floresta ou domínios envolvido em uma relação de confiança de floresta.  Quando não importando ou sincronizar usuários de qualquer um dos domínios confiáveis, desmarque a caixa de seleção para melhorar o desempenho.  O padrão é verificado. |
| Usar a configuração de LDAP específico | Selecione a opção usar LDAP para usar as configurações de LDAP especificadas para a importação e sincronização. Observação: Quando usar LDAP é selecionado, a interface do usuário altera referências do Active Directory para LDAP. |
| Botão Editar | O botão Editar permite que as configurações atuais de LDAP modificadas. |
| Use as consultas de escopo de atributo | Indica se as consultas de escopo de atributo devem ser usadas.  Consultas de escopo de atributo permitem pesquisas de diretório eficiente qualificando registros com base nas entradas de atributo do outro registro.  O servidor de autenticação multifator Azure usa consultas de escopo de atributo para os usuários que são membros de um grupo de segurança de consulta com eficiência.   <br>Observação: Há alguns casos onde as consultas de escopo de atributo são suportadas, mas não devem ser usadas.  Por exemplo, do Active Directory pode ter problemas com consultas de escopo de atributo quando um grupo de segurança contém membros de mais de um domínio.  Nesse caso, a caixa de seleção deve ser desmarcada. |

A tabela a seguir descreve as definições de configuração de LDAP.

| Recurso | Descrição |
| ------- | ----------- |
| Servidor | Insira o nome do host ou o endereço IP do servidor que executa o diretório LDAP.  Um servidor de backup também pode ser especificado, separados por ponto e vírgula. <br>Observação: Quando ligar tipo for SSL, um nome de host totalmente qualificado é geralmente necessário. |
| Base DN | Digite o nome distinto do objeto de diretório base do qual todas as consultas de diretório serão iniciado.  Por exemplo, dc = abc, dc = com. |
| Vincular tipo - consultas | Selecione o tipo de ligação apropriado para uso ao ligar para pesquisar o diretório LDAP.  Isso é usado para importações, sincronização e resolução de nome de usuário. <br><br>  Anônimo - uma ligação anônima será executada.  DN vinculado e vincular senha não serão usado.  Isso só funcionará se o diretório LDAP permite ligação anônima e permissões permitem a consulta dos registros apropriados e atributos.  <br><br> Simples - DN vinculado e vincular senha serão passadas como texto sem formatação para vincular o diretório LDAP.  Isso só deve ser usado para fins de teste para verificar que o servidor pode ser contatado e a conta de vincular tem o acesso apropriado.  É recomendável que SSL seja usado após a instalação o certificado apropriado.  <br><br> SSL - vincular DN e vincular senha será criptografado usando SSL para vincular o diretório LDAP.  Isso requer que um certificado ser instalado localmente que o diretório LDAP confia.  <br><br> Windows - vincular nome de usuário e senha vincular será usado para conectar-se com segurança para um controlador de domínio do Active Directory ou diretório ADAM.  Se vincular nome de usuário está em branco, o logon de conta do usuário será usada para vincular. |
| Vincular tipo - autenticação | Selecione o tipo de ligação apropriado para uso ao executar a autenticação de ligação LDAP.  Consulte a vincular digite descrições em tipo de vincular - consultas.  Por exemplo, isso permite ligação anônima a ser usado para consultas enquanto vincular SSL é usado para proteger a autenticação de ligação LDAP. |
| Vincular DN ou vincular username | Insira o nome distinto do registro do usuário da conta usar ao ligar o diretório LDAP.<br><br>O nome distinto de vinculação é usado somente quando ligar tipo é simples ou SSL.  <br><br>Insira o nome de usuário da conta do Windows para usar ao ligar o diretório LDAP quando ligar tipo for Windows.  Se deixado em branco, o logon de conta do usuário será usada para vincular. |
| Senha vinculada | Insira a senha de ligar para o DN vincular ou o nome de usuário que está sendo usado para vincular o diretório LDAP.  Para configurar a senha para o serviço de AdSync do servidor de autenticação multifator, a sincronização deve ser habilitada e o serviço deve ser executado no computador local.  Sob a conta que está executando o serviço de AdSync do servidor de autenticação multifator como a senha serão salvos no Windows armazenados nomes de usuário e senhas.  A senha também serão salvos sob a conta que a interface de usuário do servidor de autenticação multifator está executando como e a conta que está executando o serviço do servidor de autenticação multifator como.  <br><br> Observação: Desde a senha somente é armazenada em nomes de usuário do Windows armazenados e senhas do servidor local, esta etapa precisará ser feito em cada servidor de autenticação multifator que precise acessar a senha. |
| Limite de tamanho de consulta | Especifique o limite de tamanho para o número máximo de usuários que uma pesquisa de diretório retornará.  Esse limite deve corresponder a configuração no diretório LDAP.  Para grandes pesquisas onde não há suporte para paginação, importação e sincronização tentará recuperar usuários em lotes.  Se o limite de tamanho especificado aqui é maior que o limite configurado no diretório LDAP, alguns usuários podem ser perdidos. |
| Botão de teste | Clique no botão de teste para testar a ligação com o servidor LDAP.  <br><br> Observação: A opção usar LDAP não precisa ser selecionada para testar a vinculação.  Isso permite que a associação seja testada antes do uso da configuração de LDAP. |

## <a name="filters"></a>Filtros
Filtros permitem definir critérios para qualificar registros ao realizar uma pesquisa de diretório.  Definindo o filtro que você pode fazer os objetos que você deseja sincronizar.  

![Filtros](./media/multi-factor-authentication-get-started-server-dirint/dirint2.png)

Azure autenticação multifator tem as seguintes opções de 3.

- **Filtro de contêiner** - especifique os critérios de filtro usados para qualificar registros de contêiner ao realizar uma pesquisa de diretório.  Para o Active Directory e ADAM (| ( objectClass=organizationalUnit)(objectClass=container)) geralmente é usado.  Para outros diretórios LDAP, os critérios de filtro que qualifica cada tipo de objeto de contêiner devem ser usados dependendo do esquema de diretório.  <br>Observação: Se deixado em branco, ((objectClass=organizationalUnit)(objectClass=container)) será usado por padrão.

- **Filtro de grupo de segurança** - especifique os critérios de filtro usados para qualificar registros de grupo de segurança ao realizar uma pesquisa de diretório.  Para o Active Directory e ADAM (&(objectCategory=group) (groupType:1.2.840.113556.1.4.804:=-2147483648)) é geralmente usado.  Outros diretórios LDAP, critérios de filtro que qualifica cada tipo de objeto de grupo de segurança serão utilizados dependendo do esquema de diretório.  <br>Observação: Se deixado em branco, (&(objectCategory=group) (groupType:1.2.840.113556.1.4.804:=-2147483648)) será usado por padrão.

- **Filtro de usuário** - especifique os critérios de filtro usados para qualificar registros de usuário ao realizar uma pesquisa de diretório.  Para o Active Directory e ADAM, (& (objectClass=user)(objectCategory=person)) geralmente é usado.  Para outros diretórios LDAP (objectClass = inetOrgPerson) ou algo semelhante deve ser usado dependendo do esquema de diretório. <br>Observação: Se deixado em branco, (& (objectCategory=person)(objectClass=user)) será usado por padrão.

## <a name="attributes"></a>Atributos
Atributos podem ser personalizados conforme necessário para um diretório específico.  Isso permite que você adicione atributos personalizados e ajustar a sincronização somente os atributos que você precisa.  O valor de cada campo de atributo deve ser o nome do atributo conforme definido no esquema de diretório.  Use a tabela abaixo para obter informações adicionais.

![Atributos](./media/multi-factor-authentication-get-started-server-dirint/dirint3.png)

| Recurso | Descrição |
| ------- | ----------- |
| Identificador exclusivo | Insira o nome do atributo do atributo que serve como o identificador exclusivo do contêiner, grupo de segurança e os registros de usuário.  No Active Directory, é geralmente objectGUID.  Em outras implementações LDAP, pode ser entryUUID ou algo semelhante.  O padrão é objectGUID. |
| ... Botões de (Selecionar atributo) | Cada campo de atributo tem um botão "..." ao lado dela que exibirá a caixa de diálogo Selecionar atributo permitindo um atributo a ser selecionado em uma lista. <br><br>Selecione a caixa de diálogo atributo.<br><br>Observação: Atributos podem ser inseridos manualmente e não são necessárias para fazer a correspondência de um atributo na lista de atributos. |
| Tipo de identificador exclusivo | Selecione o tipo do atributo identificador exclusivo.  No Active Directory, o atributo objectGUID será do tipo GUID.  Em outras implementações LDAP, talvez seja do tipo matriz de bytes ASCII ou cadeia de caracteres.  O padrão é GUID. <br><br>Observação: É importante definir essa corretamente como itens de sincronização são referenciadas por seu identificador exclusivo e o tipo de identificador exclusivo é usado para localizar diretamente o objeto no diretório.  Essa configuração a cadeia de caracteres quando o diretório realmente armazena o valor como uma matriz de bytes de caracteres ASCII impedirá sincronização funcione corretamente. |
| Nome diferenciado | Insira o nome do atributo do atributo que contém o nome distinto para cada registro.  No Active Directory, é geralmente distinguishedName.  Em outras implementações LDAP, pode ser entryDN ou algo semelhante.  O padrão é distinguishedName. <br><br>Observação: Se um atributo contendo apenas o nome distinto não existir, o atributo adspath pode ser usado.  O "LDAP: / /<server>/" parte do caminho será removido automaticamente deixar apenas o nome distinto do objeto. |
| Nome de contêiner | Insira o nome do atributo do atributo que contém o nome de um registro de contêiner.  O valor do atributo será exibido na hierarquia de contêiner ao importar do Active Directory ou adicionando itens de sincronização.  O padrão é o nome. <br><br>Observação: Se diferentes contêineres usam atributos diferentes para seus nomes, vários atributos de nome de contêiner podem ser especificados em separados por ponto e vírgula.  O primeiro atributo de nome de contêiner encontrado em um objeto de contêiner vai ser usado para exibir seu nome. |
| Nome do grupo de segurança | Insira o nome do atributo do atributo que contém o nome de um registro de grupo de segurança.  O valor do atributo será exibido na lista do grupo de segurança ao importar do Active Directory ou adicionando itens de sincronização.  O padrão é o nome. |
| Usuários | Os seguintes atributos são usados para pesquisando, exibir, importar e sincronizar informações de usuário do diretório. |
| Nome de usuário | Insira o nome do atributo do atributo que contém o nome de usuário em um registro de usuário.  O valor do atributo será usado como o nome de usuário do servidor de autenticação multifator.  Um segundo atributo pode ser especificado como um backup para o primeiro.  O segundo atributo só será usado se o primeiro atributo não contiver um valor para o usuário.  Os padrões são userPrincipalName e sAMAccountName. |
| Nome | Insira o nome do atributo do atributo que contém o nome de um registro de usuário.  O padrão é givenName. |
| Sobrenome | Insira o nome do atributo do atributo que contém o sobrenome em um registro de usuário.  O padrão é sn. |
| Endereço de email | Insira o nome do atributo do atributo que contém o endereço de email em um registro de usuário.  Endereço de email será usado para enviar boas-vindas e atualizar emails para o usuário.  O padrão é mail. |
| Grupo de usuários | Insira o nome do atributo do atributo que contém o grupo de usuário em um registro de usuário.  Grupo de usuário pode ser usado para filtrar os usuários no agente e em relatórios no Portal de gerenciamento do servidor de autenticação multifator. |
| Descrição | Insira o nome do atributo do atributo que contém a descrição de um registro de usuário.  Descrição é usada somente para pesquisa.  O padrão é descrição. |
| Idioma de chamada de voz | Insira o nome do atributo do atributo que contém o nome curto do idioma a ser usado para chamadas de voz do usuário. |
| Idioma do texto SMS | Insira o nome do atributo do atributo que contém o nome curto do idioma a ser usado para mensagens de texto SMS para o usuário. |
| Idioma do aplicativo de telefone | Insira o nome do atributo do atributo que contém o nome curto do idioma a ser usado para mensagens de texto de aplicativo de telefone do usuário. |
| Idioma de token JURAMENTO | Insira o nome do atributo do atributo que contém o nome curto do idioma a ser usado para mensagens de texto token JURAMENTO para o usuário. |
| Telefones | Os atributos a seguir são usados para importar ou sincronizar números de telefone do usuário.  Se um nome de atributo não for especificado para tipo de telefone, o tipo de telefone não estará disponível quando importar do Active Directory ou adicionando itens de sincronização. |
| Business | Insira o nome do atributo do atributo que contém o número de telefone comercial em um registro de usuário.  O padrão é telephoneNumber. |
| Casa | Insira o nome do atributo do atributo que contém o número de telefone residencial em um registro de usuário.  O padrão é TelefoneResidencial. |
| Pager | Insira o nome do atributo do atributo que contém o número de pager em um registro de usuário.  O padrão é pager. |
| Celular | Insira o nome do atributo do atributo que contém o número de telefone celular em um registro de usuário.  O padrão é móvel. |
| Fax | Insira o nome do atributo do atributo que contém o número de fax em um registro de usuário.  O padrão é facsimileTelephoneNumber. |
| Telefone IP | Insira o nome do atributo do atributo que contém o número de telefone IP em um registro de usuário.  O padrão é ipPhone. |
| Personalizado | Insira o nome do atributo do atributo que contém um número de telefone personalizados em |
|  | um registro de usuário.  O padrão é em branco. |
| Extensão | Insira o nome do atributo do atributo que contém a extensão de número de telefone em um registro de usuário.  O valor do campo extensão será utilizado como a extensão somente o número de telefone principal.  O padrão é em branco. <br><br>Observação: Se o atributo de extensão não for especificado, extensões podem ser incluídas como parte do atributo telefone.  A extensão deve ser precedida com um 'x' para que ele pode ser analisado.  Por exemplo, 555-123-4567 x890 resultaria em 555-123-4567 como o número de telefone e 890 como a extensão. |
| Restaurar padrões de botão | Clique no botão Restaurar padrões para retornar todos os atributos de volta para seu valor padrão.  Os padrões devem funcionar adequadamente com o esquema de Active Directory ou ADAM normal. |

Para editar atributos, basta clicar no botão Editar na guia atributos.  Isso abrirá um windows que permite editar os atributos.

![Editar atributos](./media/multi-factor-authentication-get-started-server-dirint/dirint4.png)

## <a name="synchronization"></a>Sincronização
Sincronização mantém o banco de dados do usuário do Azure multifator sincronizado com os usuários no Active Directory ou outro diretório LDAP Lightweight Directory Access Protocol () Lightweight Directory Access Protocol.  O processo é semelhante à Importando usuários manualmente do Active Directory, mas procura periodicamente alterações do grupo de segurança e de usuário do Active Directory para processar.  Ele também fornece para desabilitar ou remover usuários removido de um grupo de segurança ou contêiner e remover usuários excluídos do Active Directory.

O serviço de ADSync de autenticação multifator é um serviço do Windows que executa a sondagem periódica do Active Directory.  Isso não é devem ser confundidas com a sincronização do Azure AD ou Azure AD Connect.  ADSync a autenticação multifator, embora criados em uma base de código semelhante, é específico para o servidor de autenticação multifator Azure.  Ele é instalado em um estado de parado e é iniciado pelo serviço do servidor de autenticação multifator quando configurado para executar.  Se você tiver uma configuração de servidor de autenticação multifator vários servidor, a ADSync de autenticação multifator só pode ser executado em um único servidor.

O serviço de ADSync de autenticação multifator usa a extensão do servidor DirSync LDAP fornecida pela Microsoft para pesquisar alterações com eficiência.  Este chamador de controle de DirSync deve ter o "diretório" obter alterações à direita e DS-replicação-Get-Changes estendido controlar o acesso à direita.  Por padrão, esses direitos são atribuídos para as contas de administrador e sistema local em controladores de domínio.  O serviço de AdSync de autenticação multifator está configurado para executar como sistema local por padrão.  Portanto é mais simples executar o serviço em um controlador de domínio.  O serviço pode ser executado como uma conta com permissões de menor se você configurá-lo para sempre realizar uma sincronização completa.  Isso é menos eficiente, mas exige menos privilégios de conta.

Se configurado para usar LDAP e o diretório LDAP suporta o controle DirSync, em seguida, sondagem para usuário e alterações do grupo de segurança funcionam da mesma forma como faz com o Active Directory.  Se o diretório LDAP não suporta o controle DirSync, em seguida, uma sincronização completa será executada durante cada ciclo.

![Sincronização](./media/multi-factor-authentication-get-started-server-dirint/dirint5.png)

Use a tabela abaixo para obter informações adicionais sobre cada uma das configurações individuais na guia sincronização.

| Recurso | Descrição |
| ------- | ----------- |
| Habilitar a sincronização com o Active Directory | Quando selecionada, o serviço do servidor de autenticação multifator será iniciado para pesquisar periodicamente Active Directory de alterações. <br><br>Observação: deve ser adicionado pelo menos um Item de sincronização e um sincronizar agora devem ser executado antes do servidor de autenticação multifator serviço iniciará Processando alterações. |
| Sincronizar cada | Especifique o intervalo de tempo que o serviço de servidor de autenticação multifator esperará entre sondagem e processando alterações. <br><br> Observação: O intervalo especificado é o tempo entre o início de cada ciclo.  Se o tempo Processando alterações excede o intervalo, o serviço pesquisará novamente imediatamente. |
| Remover usuários não está mais no Active Directory | Quando selecionada, o serviço do servidor de autenticação multifator será Processar exclusão de usuário do Active Directory excluídos e remover o usuário do servidor de autenticação multifator relacionado. |
| Sempre executar uma sincronização completa | Quando selecionada, o serviço do servidor de autenticação multifator sempre executará uma sincronização completa.  Quando estiver desmarcada, o serviço do servidor de autenticação multifator executará uma sincronização incremental consultando somente usuários que foram alterados.  O padrão é desmarcado. <br><br> Observação: Quando está desmarcada, uma sincronização incremental só pode ser executada quando o controle DirSync compatível com o diretório e a conta que está sendo usada para vincular à pasta tem as permissões apropriadas para executar consultas incrementais DirSync.  Se a conta não tem as permissões apropriadas ou vários domínios estão envolvidos na sincronização, execute uma completa sincronização é recomendada. |
| Exigir aprovação do administrador quando mais de X usuários será desabilitada ou removida | Itens de sincronização podem ser configurados para desabilitar ou remover os usuários que não são mais um membro do grupo de segurança ou o contêiner do item.  Como proteção, aprovação do administrador pode ser necessária quando o número de usuários para desabilitar ou remover excede um limite.  Quando marcada, aprovação será necessária para o limite especificado.  O padrão é 5 e o intervalo é de 1 a 999. <br><br> Aprovação é facilitada primeiro enviando uma notificação por email para administradores. A notificação de email fornece instruções para revisão e aprovação a desabilitação e a remoção de usuários.  Quando a interface de usuário do servidor de autenticação multifator é iniciada, ele solicitará para aprovação. |

O botão **Sincronizar agora** permite que você execute uma sincronização completa para os itens de sincronização especificado.  Uma sincronização completa é necessária sempre que itens de sincronização são adicionados, modificados, removidos ou um novo pedidos.  Também é necessária antes do serviço de AdSync de autenticação multifator será operacional desde que define o ponto de partida do qual o serviço procurará alterações incrementais.  Se alterações foram feitas aos itens de sincronização e uma sincronização completa não foi realizada, você será solicitado a sincronizar agora ao navegar para outra seção ou ao fechar a interface do usuário.

O botão **Remover** permite ao administrador excluir um ou mais itens de sincronização de lista de item de sincronização do servidor de autenticação multifator.

>[AZURE.WARNING]Depois que um registro de item de sincronização tenha sido removido, não é possível recuperá-la. Você precisará adicionar o registro de item de sincronização novamente se tiver sido excluído por engano.

O item de sincronização ou itens de sincronização foram removidos do servidor de autenticação multifator.  O serviço do servidor de autenticação multifator já não processará os itens de sincronização.

Os botões Mover para cima e mover para baixo permitem que o administrador alterar a ordem dos itens de sincronização.  A ordem é importante, pois o mesmo usuário pode ser um membro de mais de um item de sincronização (por exemplo, um contêiner e um grupo de segurança).  As configurações aplicadas ao usuário durante a sincronização serão proveniente do primeiro item de sincronização na lista à qual o usuário está associado.  Portanto, os itens de sincronização devem ser colocados em ordem de prioridade.

>[AZURE.TIP]Uma sincronização completa deve ser executada após a remoção de itens de sincronização.  Uma sincronização completa deve ser executada após a ordenação itens de sincronização.  Clique no botão Sincronizar agora para executar uma sincronização completa.

## <a name="multi-factor-auth-servers"></a>Servidores de autenticação multifator
Servidores de autenticação multifator adicionais pode ser configurados para servir como um proxy RADIUS backup, proxy LDAP, ou para a autenticação do IIS. A configuração de sincronização será compartilhada entre todos os agentes. Entretanto, apenas um desses agentes pode ter a execução do serviço de servidor de autenticação multifator. Essa guia permite que você selecione o servidor de autenticação multifator que devem ser habilitados para sincronização.

![Servidores de vários multifator Auth](./media/multi-factor-authentication-get-started-server-dirint/dirint6.png)
