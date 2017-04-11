<properties
    pageTitle="Sincronização do Azure AD Connect: Noções básicas sobre a configuração padrão | Microsoft Azure"
    description="Este artigo descreve a configuração padrão do Azure AD Connect sincronização."
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
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Sincronização do Azure AD Connect: Noções básicas sobre a configuração padrão
Este artigo explica as regras de configuração de prontos. Ele documentos as regras e como essas regras afetam a configuração. Ele também orienta a configuração padrão de sincronização do Azure AD Connect. O objetivo é que o leitor entenda como o modelo de configuração, chamado provisionamento declarativa, está trabalhando em um exemplo do mundo real. Este artigo pressupõe que você já tiver instalado e configure a sincronização do Azure AD Connect usando o Assistente de instalação.

Para entender os detalhes do modelo de configuração, leia a [Compreensão declarativa provisionamento](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Regras de prontos do local ao Azure AD
As expressões a seguir podem ser encontradas na configuração de prontos.

### <a name="user-out-of-box-rules"></a>Regras de prontos de usuário
Essas regras também se aplicam ao tipo de objeto iNetOrgPerson.

Um objeto de usuário deve satisfazer procedimentos para ser sincronizado:

- Deve ter um sourceAnchor.
- Depois que o objeto tiver sido criado no Azure AD, sourceAnchor não pode alterar. Se o valor for alterado no local, o objeto interrompe a sincronização até que o sourceAnchor seja alterada para seu valor anterior.
- Deve ter o atributo accountEnabled (userAccountControl) preenchido. Com um local Active Directory, esse atributo está sempre presente e preenchida.

Os seguintes objetos de usuário são **não** sincronizada com Azure AD:

- `IsPresent([isCriticalSystemObject])`. Garantir muitos prontos de objetos no Active Directory, como a conta de administrador interna, não são sincronizados.
- `IsPresent([sAMAccountName]) = False`. Certifique-se de objetos de usuário sem o atributo sAMAccountName não são sincronizados. Nesse caso seria apenas praticamente acontecer em um domínio atualizado do NT4.
- `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Não sincronize a conta de serviço usada pelo sincronização do Azure AD Connect e suas versões anteriores.
- Não sincronize contas do Exchange que não funcionaria no Exchange Online.
    - `[sAMAccountName] = "SUPPORT_388945a0"`
    - `Left([mailNickname], 14) = "SystemMailbox{"`
    - `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
    - `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
- Não sincronizam objetos que não funcionaria no Exchange Online.
`CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
Esta máscara de bits (& H21C07000) seriam filtrar os seguintes objetos:
    - Pasta pública habilitados para email
    - Caixa de correio de Atendedor do sistema
    - Caixa de correio de banco de dados de caixa de correio (caixa de correio de sistema)
    - Grupo de segurança universal (não aplicar para um usuário, mas está presente por razões herdados)
    - Grupo não Universal (não aplicar para um usuário, mas está presente por razões herdados)
    - Plano de caixa de correio
    - Caixa de correio de descoberta
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize os objetos de vítima de replicação.

As seguintes regras de atributo se aplicar:

- `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. O atributo sourceAnchor não é contribuiu de uma caixa de correio vinculada. Será considerado que se uma caixa de correio vinculada foi encontrada, a conta real ingressou mais tarde.
- Exchange relacionada atributos são sincronizados somente se o atributo **mailNickName** tem um valor.
- Quando houver várias florestas, atributos são consumidos na seguinte ordem:
    1. Atributos relacionados a entrada (por exemplo, userPrincipalName) são contribuiu da floresta com uma conta habilitada.
    2. Atributos que podem ser encontrados em uma GAL (lista de endereços Global) do Exchange são contribuiu da floresta com uma caixa de correio do Exchange.
    3. Se nenhuma caixa de correio pode ser encontrada, esses atributos podem vir de qualquer floresta.
    4. Exchange relacionada atributos (técnicos atributos não visíveis na GAL) são uma contribuição da floresta onde `mailNickname ISNOTNULL`.
    5. Se houver várias florestas que faria satisfaçam uma dessas regras, a ordem de criação (data/hora) dos conectores (florestas) é usada para determinar quais floresta contribui os atributos.

### <a name="contact-out-of-box-rules"></a>Regras de prontos de contato
Um objeto de contato deve satisfazer procedimentos para ser sincronizado:

- O contato deve ser habilitado para email. É verificado com as seguintes regras:
    - `IsPresent([proxyAddresses]) = True)`. Atributo proxyAddresses deve ser preenchido.
    - Um endereço de email principal pode ser encontrado no atributo proxyAddresses ou o atributo de email. A presença de um @ é usado para confirmar que o conteúdo é um endereço de email. Uma dessas dois regras deve ser avaliada como True.
        - `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Existe uma entrada com "SMTP:" e, se houver, pode um @ ser encontradas na cadeia de caracteres?
        - `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. É o atributo de email preenchido e se for, pode um @ ser encontradas na cadeia de caracteres?

Os seguintes objetos de contato são **não** sincronizado com o Azure AD:

- `IsPresent([isCriticalSystemObject])`. Certifique-se de que nenhuma objetos de contato marcados como críticos são sincronizados. Não devem ser qualquer um com uma configuração padrão.
- `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
- `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Esses objetos não funcionaria no Exchange Online.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize os objetos de vítima de replicação.

### <a name="group-out-of-box-rules"></a>Agrupar prontos de regras
Um objeto de grupo deve satisfazer procedimentos para ser sincronizado:

- Deve ter menos de 50.000 membros. Essa contagem é o número de membros do grupo local.
    - Se ele tiver mais membros antes de sincronização começa na primeira vez, o grupo não está sincronizado.
    - Se o número de membros crescer de quando foi criado inicialmente, em seguida, quando ele atingir 50.000 membros Parar sincronização até que a contagem de associação for menor que 50.000 novamente.
    - Observação: A contagem de 50.000 associação também é imposta pelo Azure AD. Não é possível sincronizar grupos com mais membros, mesmo se você modificar ou remove esta regra.
- Se o grupo é um **Grupo de distribuição**, em seguida, ele também deve ser habilitado para email. Consulte [regras de fora da caixa de contato](#contact-out-of-box-rules) para essa regra é aplicada.

Os seguintes objetos de grupo são **não** sincronizada com Azure AD:

- `IsPresent([isCriticalSystemObject])`. Garantir muitos prontos de objetos no Active Directory, como o grupo de administradores internos, não são sincronizados.
- `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Grupo legado usado pelo DirSync.
- `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Grupo de função.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize os objetos de vítima de replicação.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Regras de prontos de ForeignSecurityPrincipal
FSPs fazem parte de "qualquer" (\*) objeto no metaverso. Na verdade, esta associação ocorre apenas para usuários e grupos de segurança. Essa configuração garante que as associações de cruz floresta são resolvidas e são representadas corretamente no Azure AD.

### <a name="computer-out-of-box-rules"></a>Regras de prontos de computador
Um objeto de computador deve satisfazer o seguinte a ser sincronizado:

- `userCertificate ISNOTNULL`. Somente os computadores Windows 10 preencher esse atributo. Todos os objetos de computador com um valor nesse atributo são sincronizados.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Noções básicas sobre o cenário prontos de regras
Neste exemplo, estamos usando uma implantação com uma floresta de conta (A), uma floresta de recurso (R) e um diretório do Azure AD.

![Imagem com descrição do cenário](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

Nessa configuração, pressupõe-se que há uma conta habilitada da floresta de conta e uma conta desabilitada na floresta de recursos com uma caixa de correio vinculada.

Nosso objetivo com a configuração padrão é:

- Atributos relacionados ao entrar são sincronizados da floresta com a conta habilitada.
- Atributos que podem ser encontrados na GAL (lista de endereços Global) são sincronizados da floresta com a caixa de correio. Se nenhuma caixa de correio pode ser encontrada, qualquer outra floresta será usada.
- Se uma caixa de correio vinculada for encontrada, a conta habilitada vinculada deve ser encontrada para o objeto a ser exportados para o Azure AD.

### <a name="synchronization-rule-editor"></a>Editor de regras de sincronização
A configuração pode ser exibida e alterada com a ferramenta Editor de regras de sincronização (SRE) e um atalho para ele pode ser encontrado no menu Iniciar.

![Ícone de Editor de regras de sincronização](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

O SRE é uma ferramenta do kit de recursos e ele estiver instalado com a sincronização do Azure AD Connect. Para poder para iniciá-lo, você deve ser um membro do grupo ADSyncAdmins. Quando ele for iniciado, você verá algo parecido com isto:

![Regras de sincronização de entrada](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

Nesse painel, você ver todas as regras de sincronização criadas para sua configuração. Cada linha na tabela é uma regra de sincronização. Para a esquerda em tipos de regra, os dois tipos diferentes são listados: entrada e saída. Entrada e saída está no modo de exibição de metaverso. Você pretende principalmente foco nas regras de entrada nesta visão geral. A lista atual de regras de sincronização depende do esquema detectado no AD. Na imagem acima, a floresta de conta (fabrikamonline.com) não tem quaisquer serviços, como o Exchange e o Lync, e nenhum regras de sincronização foram criadas para esses serviços. No entanto, na floresta do recurso (res.fabrikamonline.com) encontre regras de sincronização para esses serviços. O conteúdo das regras é diferente dependendo da versão detectada. Por exemplo, em uma implantação com Exchange 2013 há mais fluxos de atributo configurados que no Exchange 2010/2007.

### <a name="synchronization-rule"></a>Regra de sincronização
Uma regra de sincronização é um objeto de configuração com um conjunto de atributos fluindo quando uma condição é satisfeita. Ele também é usado para descrever como um objeto em um espaço de conector está relacionado a um objeto no metaverso, conhecido como **ingressar** ou **corresponder**. As regras de sincronização têm um valor de precedência indicando como se relacionam entre si. Uma regra de sincronização com um valor numérico inferior tem precedência e em um conflito de fluxo de atributo, precedência wins a resolução de conflitos.

Como exemplo, examine a regra de sincronização **do AD – usuário AccountEnabled**. Marcar esta linha a SRE e selecione **Editar**.

Como essa regra é uma regra de prontos, você receberá um aviso quando você abrir a regra. Você não deve fazer as [alterações para fora da caixa regras](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), para que você será solicitado a quais são suas intenções. Nesse caso, você só deseja exibir a regra. Selecione **não**.

![Sincronização de regras de aviso](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

Uma regra de sincronização tem quatro seções de configuração: descrição, filtro, regras de junção e transformações de escopo.

#### <a name="description"></a>Descrição
A primeira seção fornece informações básicas, como um nome e descrição.

![Descrição da guia editor de regras em sincronia ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Você também encontrará informações sobre qual sistema conectado esta regra está relacionada, qual objeto tipo no sistema conectado se aplica ao e o tipo de objeto do metaverso. O tipo de objeto do metaverso é sempre pessoa independentemente quando o tipo de objeto de origem for um usuário, iNetOrgPerson ou contato. O tipo de objeto do metaverso nunca deve alterar para que ele será criado como um tipo genérico. O tipo de Link podem ser definido para ingressar, StickyJoin ou provisionar. Essa configuração funciona em conjunto com a seção ingressar regras e é coberta mais tarde.

Você também pode ver que essa regra de sincronização é usada para sincronização de senha. Se um usuário está no escopo para esta regra de sincronização, a senha será sincronizada do local na nuvem (presumindo que você tiver habilitado o recurso de sincronização de senha).

#### <a name="scoping-filter"></a>Filtro de escopo
A seção filtro de escopo é usada para configurar quando uma regra de sincronização deve ser aplicada. Como o nome da regra de sincronização que você está analisando indica que ele deve ser aplicado somente para usuários habilitados, o escopo está configurado para que o atributo do AD **userAccountControl** não deve ter o bit 2 definido. Quando o mecanismo de sincronização encontra um usuário no AD, ele se aplica esta regra de sincronização quando **userAccountControl** está definido como o valor decimal 512 (enabled usuário normal). Ele não se aplica a regra quando o usuário tiver **userAccountControl** definida como 514 (desabilitado usuário normal).

![Guia escopo no editor de regra de sincronização ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

O filtro de escopo tem grupos e cláusulas que podem ser aninhadas. Todas as cláusulas dentro de um grupo devem ser obedecidas para uma regra de sincronização para aplicar. Quando vários grupos são definidos, pelo menos um grupo deve ser obedecido para aplicar a regra. Ou seja, um OR lógico é avaliado entre grupos e uma lógica e é avaliado dentro de um grupo. Um exemplo dessa configuração pode ser encontrado na regra de sincronização saída **Out para AAD – Group Join**. Há vários grupos de filtro de sincronização, por exemplo, uma para os grupos de segurança (`securityEnabled EQUAL True`) e outra para grupos de distribuição (`securityEnabled EQUAL False`).

![Guia escopo no editor de regra de sincronização ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Essa regra é usada para definir quais grupos devem ser configurados para o Azure AD. Grupos de distribuição devem ser habilitado para ser sincronizado com o Azure AD para email, mas um email para os grupos de segurança não é necessário.

#### <a name="join-rules"></a>Participar de regras
A terceira seção é usada para configurar como objetos no espaço do conector se relacionam aos objetos do metaverso. A regra que você procurou no anteriormente não tem nenhuma configuração para participar de regras, portanto, em vez disso você vai examinar **do AD – usuário ingressar**.

![Ingressar em guia regras no editor de regras de sincronização ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

O conteúdo da regra de junção depende da opção correspondente selecionada no Assistente de instalação. Para uma regra de entrada, a avaliação começa com um objeto no espaço de conector de origem e cada grupo nas regras de junção é avaliado em sequência. Se um objeto de origem é avaliado para corresponder exatamente um objeto no metaverso usando uma das regras de junção, os objetos são Unidos. Se todas as regras tenham sido avaliadas e não há nenhuma correspondência, o tipo de vínculo na página de descrição é usado. Se essa configuração for definida para **provisionar**, um novo objeto é criado no destino metaverso. Para provisionar um novo objeto para o metaverso é também conhecido como **projeto** um objeto para o metaverso.

As regras de junção só são avaliadas uma vez. Quando um objeto de espaço do conector e um objeto de metaverso são associadas, eles permanecem Unidos desde o escopo da regra de sincronização é ainda satisfeito.

Ao avaliar regras de sincronização, somente uma regra de sincronização com regras de junção definidas deve estar no escopo. Se forem encontradas várias regras de sincronização com regras de junção para um objeto, será gerado um erro. Por isso, a prática recomendada é ter apenas uma regra de sincronização com definido quando várias regras de sincronização estão no escopo de um objeto de junção. Na configuração de prontos para sincronização do Azure AD Connect, essas regras podem ser encontradas examinando o nome e encontrar aqueles com a palavra **ingressar** no final do nome. Uma regra de sincronização sem qualquer regras de junção definidas aplica os fluxos de atributo quando outra regra de sincronização unidas os objetos ou provisionado um novo objeto de destino.

Se você olhar imagem acima, você pode ver que a regra está tentando ingressar **objectSID** com **msExchMasterAccountSid** (Exchange) e **msRTCSIP-OriginatorSid** (Lync), que é o esperado em uma topologia de floresta conta-recurso. Você encontrar a mesma regra em todas as florestas. Pressupõe-se que cada floresta poderia ser floresta de uma conta ou um recurso. Esta configuração também funciona se você tiver contas live em uma única floresta e que não têm a serem agrupados.

#### <a name="transformations"></a>Transformações
A seção de transformação define todos os fluxos de atributo que se aplicam ao objeto de destino, quando os objetos são Unidos e o filtro de escopo é satisfeito. Voltar para a **do AD – usuário AccountEnabled** regra de sincronização, encontrar as transformações seguintes:

![Transformações guia editor de regras em sincronia ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Para colocar essa configuração em contexto, em uma implantação de floresta conta-recurso, ele espera encontrar uma conta habilitada da floresta de conta e uma conta desabilitada na floresta de recursos com as configurações do Exchange e o Lync. A regra de sincronização que você está analisando contém os atributos necessários para entrar e esses atributos devem fluem da floresta onde não há uma conta habilitada. Todos esses fluxos de atributo são colocados juntas em uma regra de sincronização.

Uma transformação pode ter diferentes tipos: constante, direta e expressão.

- Um fluxo constante sempre fluirá um valor codificado. No caso acima, ele sempre define o valor **True** no metaverso atributo chamado **accountEnabled**.
- Um fluxo direto sempre flua o valor do atributo na fonte para o atributo de destino como-é.
- O terceiro tipo de fluxo é expressão e permite configurações mais avançadas.

A linguagem de expressão é VBA (Visual Basic for Applications), então as pessoas com experiência do Microsoft Office ou VBScript reconhecerá o formato. Atributos são colocados entre colchetes, [attributeName]. Nomes de função e nomes de atributo diferenciam maiusculas de minúsculas, mas o Editor de regras de sincronização avalia as expressões e fornecer um aviso se a expressão não é válida. Todas as expressões são expressas em uma única linha com funções aninhadas. Para mostrar o poder do idioma de configuração, veja aqui o fluxo para pwdLastSet, mas com comentários adicionais inseridos:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Consulte [Noções básicas sobre expressões de provisionamento declarativa](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) para obter mais informações sobre a linguagem de expressão para fluxos de atributo.

### <a name="precedence"></a>Precedência
Agora você procurou no algumas regras de sincronização individuais, mas as regras trabalham juntos na configuração. Em alguns casos, um valor de atributo é contribuiu de várias regras de sincronização para o mesmo atributo de destino. Nesse caso, a precedência de atributo é usada para determinar qual atributo wins. Como exemplo, examine o atributo sourceAnchor. Este é um atributo importante sejam capazes de entrar no Azure AD. Você pode encontrar um fluxo de atributo para este atributo no duas regras de sincronização diferentes, **do AD – usuário AccountEnabled** e **do AD – comuns do usuário**. Devido a precedência de regras de sincronização, o atributo sourceAnchor é contribuiu com da floresta com uma conta habilitada primeiro quando há vários objetos associados ao objeto metaverso. Não se houver nenhum contas habilitadas, o mecanismo de sincronização usa a regra de sincronização de variável-all **do AD – comuns do usuário**. Essa configuração garante que até mesmo para as contas que estão desabilitadas, ainda há um sourceAnchor.

![Regras de sincronização de entrada](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

A precedência de regras de sincronização é definida em grupos pelo Assistente de instalação. Todas as regras em um grupo têm o mesmo nome, mas eles estão conectados a diferentes diretórios conectados. O Assistente de instalação oferece a regra **do AD – usuário ingressar** mais altos precedência e ele itera sobre todos os conectado diretórios do AD. Então, ele continuará com os grupos de próximos das regras em uma ordem predefinida. Dentro de um grupo, as regras são adicionadas na ordem em que os conectores foram adicionados no assistente. Se outro conector é adicionado através do assistente, as regras de sincronização são reordenadas e regras do conector novos são inseridas última em cada grupo.

### <a name="putting-it-all-together"></a>Juntando as peças
Agora sabemos que suficiente sobre regras de sincronização sejam capazes de compreender como a configuração funciona com as regras de sincronização diferente. Se você examinar um usuário e os atributos que são contribuiu para o metaverso, as regras são aplicadas na seguinte ordem:

Nome | Comentário
:------------- | :-------------
Do AD – junção de usuário | Regra para ingressar em objetos de espaço de conector com metaverso.
Do AD – conta habilitado | Atributos necessários para entrar no Azure AD e o Office 365. Queremos que esses atributos da conta habilitado.
Do AD – comum de usuário do Exchange | Atributos encontrados na lista de endereços Global. Vamos supor que a qualidade de dados é melhor na floresta onde encontramos caixa de correio do usuário.
Do AD – comum de usuário | Atributos encontrados na lista de endereços Global. Caso não encontramos uma caixa de correio, qualquer outro objeto Unido pode contribuir o valor do atributo.
Do AD – Exchange do usuário | Existe somente se Exchange foi detectado. Flua todos os atributos do Exchange de infraestrutura.
Do AD – Lync do usuário | Existe somente se o Lync foi detectado. Flua todos os atributos do Lync de infraestrutura.

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre o modelo de configuração em [Noções básicas sobre declarativa provisionamento](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Leia mais sobre a linguagem de expressão em [Noções básicas sobre expressões de provisionamento declarativa](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
- Continue a leitura como a configuração de prontos funciona em [Noções básicas sobre usuários e contatos](active-directory-aadconnectsync-understanding-users-and-contacts.md)
- Veja como fazer uma mudança prática usando provisionamento declarativa em [como fazer uma alteração para a configuração padrão](active-directory-aadconnectsync-change-the-configuration.md).

**Tópicos de visão geral**

- [Sincronização do Azure AD Connect: entender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
