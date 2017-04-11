<properties
   pageTitle="Conector LDAP genérico | Microsoft Azure"
   description="Este artigo descreve como configurar conector de LDAP genérico da Microsoft."
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

# <a name="generic-ldap-connector-technical-reference"></a>Referência técnica de conector LDAP genérica
Este artigo descreve o conector de LDAP genérico. O artigo aplica-se aos seguintes produtos:

- Gerenciador de identidades do Microsoft 2016 (MIM2016)
- Gerenciador de identidades Forefront 2010 R2 (FIM2010R2)
    -   Deve usar hotfix 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2, o conector está disponível como um download a partir do [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Ao se referir a RFCs IETF, este documento é usando o formato (RFC [número RFC] / [seção no documento RFC]), por exemplo (RFC 4512/4.3).
Você pode encontrar mais informações em http://tools.ietf.org/html/rfc4500 (você precisa substituir 4500 com o número RFC correto).

## <a name="overview-of-the-generic-ldap-connector"></a>Visão geral do conector LDAP genérico
O conector de LDAP genérico permite integrar o serviço de sincronização com um servidor de v3 LDAP.

Determinadas operações e elementos de esquema, como aqueles necessários para executar importação de delta, não estão especificados nas RFCs IETF. Para essas operações, são aceitos somente diretórios LDAP explicitamente especificados.

De uma perspectiva de alto nível, os seguintes recursos são suportados pela versão atual do conector:

Recurso | Suporte
--- | --- |
Fonte de dados conectada | O conector é compatível com todos os servidores de v3 LDAP (RFC 4510 compatíveis). Ele foi testado com o seguinte: <li>Serviços de diretório leve do Microsoft Active Directory (AD LDS)</li><li>Catálogo Global do Microsoft Active Directory (AD GC)</li><li>Servidor de diretório 389</li><li>Servidor de diretório Apache</li><li>IBM Tivoli DS</li><li>Diretório de Isode</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>Abrir DJ</li><li>Abrir DS</li><li>Abrir LDAP (openldap.org)</li><li>Oracle (anteriormente DOM) Directory Server Enterprise Edition</li><li>Servidor de diretório Virtual RadiantOne (VDS)</li><li>DOM um servidor de diretório</li>**Diretórios notáveis não têm suportados:** <li>Microsoft Active Directory Domain Services (AD DS) [usar o conector do Active Directory interno, em vez disso]</li><li>Diretório da Internet Oracle (OID)</li>
Cenários   | <li>Gerenciamento de ciclo de vida do objeto</li><li>Gerenciamento de grupo</li><li>Gerenciamento de senha</li>
Operações |As seguintes operações são suportadas em todos os diretórios LDAP: <li>Importação completa</li><li>Exportar</li>Só há suporte para as seguintes operações nos diretórios especificados:<li>Importação delta</li><li>Definir senha, alterar senha</li>
Esquema | <li>Esquema for detectada do esquema LDAP (RFC3673 e RFC4512/4.2)</li><li>Suporta classes estruturais, aux classes e classe de objeto do extensibleObject (RFC4512/4.3)</li>

### <a name="delta-import-and-password-management-support"></a>Suporte de gerenciamento de importação e a senha de delta
Diretórios com suporte para importação de Delta e gerenciamento de senha:

- Serviços de diretório leve do Microsoft Active Directory (AD LDS)
    - Suporta todas as operações de importação de delta
    - Suporta definir senha
- Catálogo Global do Microsoft Active Directory (AD GC)
    - Suporta todas as operações de importação de delta
    - Suporta definir senha
- Servidor de diretório 389
    - Suporta todas as operações de importação de delta
    - Suporta definir senha e alterar senha
- Servidor de diretório Apache
    - Não suporta importação delta como este diretório não tem um log de alteração persistente
    - Suporta definir senha
- IBM Tivoli DS
    - Suporta todas as operações de importação de delta
    - Suporta definir senha e alterar senha
- Diretório de Isode
    - Suporta todas as operações de importação de delta
    - Suporta definir senha e alterar senha
- Novell eDirectory e NetIQ eDirectory
    - Suporta operações de adicionar, atualizar e renomear para importação de delta
    - Não oferece suporte a operações de exclusão para importação de delta
    - Suporta definir senha e alterar senha
- Abrir DJ
    - Suporta todas as operações de importação de delta
    - Suporta definir senha e alterar senha
- Abrir DS
    - Suporta todas as operações de importação de delta
    - Suporta definir senha e alterar senha
- Abrir LDAP (openldap.org)
    - Suporta todas as operações de importação de delta
    - Suporta definir senha
    - Não oferece suporte para alterar senha
- Oracle (anteriormente DOM) Directory Server Enterprise Edition
    - Suporta todas as operações de importação de delta
    - Suporta definir senha e alterar senha
- Servidor de diretório Virtual RadiantOne (VDS)
    - Deve estar usando a versão 7.1.1 ou superior
    - Suporta todas as operações de importação de delta
    - Suporta definir senha e alterar senha
-  DOM um servidor de diretório
    - Suporta todas as operações de importação de delta
    - Suporta definir senha e alterar senha

### <a name="prerequisites"></a>Pré-requisitos
Antes de usar o conector, verifique se que você tem o seguinte no servidor de sincronização:

- 4.5.2 do Microsoft .NET Framework ou posterior

### <a name="detecting-the-ldap-server"></a>Detectar o servidor LDAP
O conector depende de várias técnicas para detectar e identificar o servidor LDAP. O conector usa o DSE raiz, o nome do fornecedor/versão e ele analisa o esquema para localizar objetos exclusivos e atributos conhecidos determinados servidores LDAP. Esses dados, se encontrados, é usado para preencher previamente as opções de configuração no conector.

### <a name="connected-data-source-permissions"></a>Permissões de fonte de dados conectadas
Para executar importação e exportação operações nos objetos no diretório conectado, a conta de conector deve ter permissões suficientes. O conector deve escrever permissões sejam capazes de exportar e permissões de leitura para ser capaz de importar. Configuração de permissão é executada dentro das experiências de gerenciamento da pasta de destino em si.

### <a name="ports-and-protocols"></a>Portas e protocolos
O conector usa o número da porta especificado na configuração, que por padrão é 389 para LDAP e 636 para LDAP.

Para LDAPS, você deve usar SSL 3.0 ou TLS. SSL 2.0 não é suportado e não pode ser ativado.

### <a name="required-controls-and-features"></a>Recursos e controles necessários
Os seguintes controles LDAP/recursos devem estar disponíveis no servidor LDAP para o conector funcione corretamente:  
`1.3.6.1.4.1.4203.1.5.3`Filtros de verdadeiro/falso

O filtro de verdadeiro/falso frequentemente não for relatado como suportada por diretórios LDAP e pode aparecer na **Página Global** em **Obrigatório recursos não encontrado**. Ele é usado para criar filtros **ou** em consultas LDAP, por exemplo, ao importar vários tipos de objeto. Se você pode importar mais de um tipo de objeto, o servidor LDAP suporta este recurso.

Se você usar um diretório onde um identificador exclusivo é a âncora a seguir também deve ser disponível (consulte a seção [Configurar âncoras](#configure-anchors) posteriormente neste artigo para obter mais informações):  
`1.3.6.1.4.1.4203.1.5.1`Todos os atributos operacionais

Se o diretório tiver mais objetos do que o que pode ajustá-la em uma chamada para o diretório, em seguida, é recomendável usar paginação. Paginação trabalhar, você precisará de uma das seguintes opções:

**Opção 1:**  
`1.2.840.113556.1.4.319`pagedResultsControl

**Opção 2:**  
`2.16.840.1.113730.3.4.9`VLVControl  
`1.2.840.113556.1.4.473`SortControl

Se ambas as opções estiverem habilitadas na configuração do conector, pagedResultsControl é usado.

`1.2.840.113556.1.4.417`ShowDeletedControl

ShowDeletedControl só é usado com o método de importação de delta USNChanged sejam capazes de ver objetos excluídos.

O conector tenta detectar as opções presentes no servidor. Se as opções não podem ser detectadas, um aviso está presente na página Global nas propriedades do conector. Nem todos os servidores LDAP controles/recursos de presente todos eles oferecem suporte e mesmo se este aviso estiver presente, o conector pode funcionar sem problemas.

### <a name="delta-import"></a>Importação delta
Importação delta só está disponível quando um diretório de suporte foi detectado. Os seguintes métodos atualmente são usados:

- LDAP Accesslog. Consulte [o log de http://www.openldap.org/doc/admin24/overlays.html#Access](http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
- Log de alteração LDAP. Consulte [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
- Carimbo de hora. Novell/NetIQ eDirectory, o conector usa última data/hora para obter criado e atualizado objetos. Novell/NetIQ eDirectory não oferece que um equivalente significa recuperar objetos excluídos. Essa opção também pode ser usada se nenhum outro método de importação de delta estiver ativo no servidor LDAP. Esta opção não é capaz de importar excluído objetos.
- USNChanged. Consulte: [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### <a name="not-supported"></a>Sem suporte
Os seguintes recursos LDAP não são suportados:

- Referências de LDAP entre servidores (RFC 4511/4.1.10)

## <a name="create-a-new-connector"></a>Criar um novo conector
Para criar um conector de LDAP genérico, no **Serviço de sincronização** , selecione **Agente de gerenciamento** e **criar**. Selecione o conector **LDAP genérico (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### <a name="connectivity"></a>Conectividade
Na página conectividade, você deve especificar as informações de Host, porta e vinculação. Dependendo de qual vinculação é selecionado, adicionais informações podem ser fornecidas nas seções a seguir.

![Conectividade](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

- A configuração de tempo limite de Conexão só é usada para a primeira conexão ao servidor quando detectar o esquema.
- Se a vinculação é anônimo, em seguida, nenhum nome de usuário / senha nem certificado são usadas.
- Para outras ligações, insira informações ambos em nome de usuário / senha ou selecione um certificado.
- Se você estiver usando o Kerberos para autenticar, também fornecem o território/domínio do usuário.

Na caixa de texto de **aliases de atributo** é usada para atributos definidos no esquema com sintaxe RFC4522. Esses atributos não podem ser detectados durante a detecção de esquema e o conector precisa de ajuda para identificar esses atributos. Por exemplo a seguir é necessário para ser inserida na caixa de aliases atributo identificar corretamente o atributo userCertificate como um atributo binário:

`userCertificate;binary`

Este é um exemplo de como essa configuração pode parecer com:

![Conectividade](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Selecione a caixa de seleção **incluir atributos operacionais no esquema** também inclua atributos criados pelo servidor. Eles incluem atributos como quando o objeto foi criado e o horário da última atualização.

Selecione **incluir extensíveis atributos no esquema** se objetos extensíveis (RFC4512/4.3) são usados e habilitar essa opção permite que cada atributo a ser usado em todos os objetos. Selecionar essa opção torna o esquema muito grande para a menos que o diretório conectado está usando esse recurso a recomendação é manter a opção desmarcada.

### <a name="global-parameters"></a>Parâmetros globais
Na página de parâmetros globais, você deve configurar o DN para o log de alteração de delta e recursos adicionais do LDAP. A página é previamente preenchida com as informações fornecidas pelo servidor LDAP.

![Conectividade](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

A seção superior mostra as informações fornecidas pelo servidor em si, como o nome do servidor. O conector também verifica os controles obrigatórios estão presentes no DSE raiz. Se esses controles não estiverem listados, um aviso é apresentado. Alguns diretórios LDAP não listar todos os recursos no DSE raiz e é possível que o conector funciona sem problemas, mesmo que haja um aviso.

As caixas de seleção **suportados controles** controlam o comportamento de certas operações:

- Com a exclusão de árvore selecionado, uma hierarquia é excluída com uma chamada LDAP. Com a exclusão de árvore desmarcada, o conector faz uma exclusão recursiva, se necessário.
- Com resultados pagináveis selecionados, o conector não uma importação de páginas com o tamanho especificado sobre as etapas em execução.
- A VLVControl e SortControl é uma alternativa para o pagedResultsControl ao ler dados do diretório LDAP.
- Se todas as três opções (pagedResultsControl, VLVControl e SortControl) estão desmarcadas o conector importa todos os objetos em uma operação, que pode falhar se for um diretório grande.
- ShowDeletedControl é usado somente quando o método de importação de Delta é USNChanged.

O log de alterações DN é o contexto de nomenclatura usado pelo log de alterações delta, por exemplo **cn = log de alteração**. Este valor deve ser especificado sejam capazes de fazer delta importação.

A seguir é uma lista de log de alteração de padrão DNs:

Diretório | Log de alteração de delta
--- | ---
Microsoft AD LDS e AD GC | Detectado automaticamente. USNChanged.
Servidor de diretório Apache | Não está disponível.
Diretório 389 | Log de alterações. Valor usar padrão: **cn = log de alteração**
IBM Tivoli DS | Log de alterações. Valor usar padrão: **cn = log de alteração**
Diretório de Isode | Log de alterações. Valor usar padrão: **cn = log de alteração**
Novell/NetIQ eDirectory | Não está disponível. Carimbo de hora. Usa o conector última atualização data/hora para obter adicionados e atualizados de registros.
Abrir DJ/DS | Log de alterações.  Valor usar padrão: **cn = log de alteração**
Abrir LDAP | Log de acesso. Valor usar padrão: **cn = accesslog**
Oracle DSEE | Log de alterações. Valor usar padrão: **cn = log de alteração**
RadiantOne VDS | Diretório virtual. Depende do diretório conectado ao VDS.
DOM um servidor de diretório | Log de alterações. Valor usar padrão: **cn = log de alteração**

O atributo password é o nome do atributo que o conector deve usar para definir a senha de alteração de senha e as operações de conjunto de senha.
Esse valor é definido para **userPassword** por padrão, mas pode ser alterada quando necessário para um determinado sistema LDAP.

Na lista de partições adicionais, é possível adicionar namespaces adicionais detectado não automaticamente. Por exemplo, essa configuração pode ser usada se vários servidores constituem um cluster lógico, que deve ser importado ao mesmo tempo. Assim como o Active Directory pode ter vários domínios em uma floresta, mas todos os domínios compartilham um esquema, a mesma pode ser simulada inserindo os namespaces adicionais nesta caixa. Cada namespace pode importar de servidores diferentes e posterior é configurado na página Configurar partições e hierarquias. Use Ctrl + Enter para obter uma nova linha.

### <a name="configure-provisioning-hierarchy"></a>Configurar a hierarquia de provisionamento
Esta página é usada para mapear o componente DN, por exemplo, OU, para o tipo de objeto que deve ser provisionado, por exemplo organizationalUnit.

![Hierarquia de provisionamento](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

Configurando a hierarquia de provisionamento, você pode configurar o conector para criar automaticamente uma estrutura quando necessário. Por exemplo, se houver um namespace dc = contoso, dc = com e um novo cn de objeto = José, ou = Seattle, c = US, dc = contoso, dc = com está provisionado, em seguida, o conector pode criar um objeto de país de tipo para EUA e um organizationalUnit para Seattle se aqueles não ainda estiver presentes no diretório.

### <a name="configure-partitions-and-hierarchies"></a>Configurar partições e hierarquias
Na página partições e hierarquias, selecione todos os namespaces com objetos que você planeja importar e exportar.

![Partições](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

Para cada namespace, também é possível definir configurações de conectividade que poderia substituir os valores especificados na tela de conectividade. Se esses valores forem para seu valor em branco padrão, as informações na tela de conectividade são usadas.

Também é possível selecionar quais contêineres e organizacionais o conector deve importar e exportar para.

### <a name="configure-anchors"></a>Configurar âncoras
Esta página sempre têm um valor pré-configurado e não pode ser alterada. Se o fornecedor do servidor tiver sido identificado, a âncora pode ser preenchida com um atributo imutável, por exemplo o GUID de um objeto. Se ele não foi detectado ou não ter um atributo imutável conhecido, o conector usa dn (nome distinto) como a âncora.

![âncoras](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)

A seguir é uma lista de servidores LDAP e a âncora sendo usado:

Diretório | Atributo de âncora
--- | ---
Microsoft AD LDS e AD GC | objectGUID
Servidor de diretório 389 | DN
Diretório de Apache | DN
IBM Tivoli DS | DN
Diretório de Isode | DN
Novell/NetIQ eDirectory | GUID
Abrir DJ/DS | DN
Abrir LDAP | DN
Oracle ODSEE | DN
RadiantOne VDS | DN
DOM um servidor de diretório | DN

## <a name="other-notes"></a>Outras anotações
Esta seção fornece informações dos aspectos que são específicos para este conector ou por outros motivos são importantes saber.

### <a name="delta-import"></a>Importação delta
A marca d'água delta no Open LDAP é UTC Data/hora. Por esse motivo, os relógios entre o serviço de sincronização de FIM e Open LDAP devem ser sincronizados. Caso contrário, algumas entradas no log de alteração de delta podem ser omitidas.

Para Novell eDirectory, a importação de delta não detecta exclui qualquer objeto. Por isso, é necessário para executar uma importação completa periodicamente para encontrar todos os objetos excluídos.

Para diretórios com um log de alteração de delta baseada em data/hora, é altamente recomendável para executar uma importação completa em horários periódicos. Esse processo permite que o mecanismo de sincronização para encontrar e dissimilarities entre o servidor LDAP e que está atualmente no espaço de conector.

## <a name="troubleshooting"></a>Solução de problemas

-   Para obter informações sobre como habilitar o registro em log solucionar o conector, consulte [como habilitar o rastreamento ETW para conectores](http://go.microsoft.com/fwlink/?LinkId=335731).
