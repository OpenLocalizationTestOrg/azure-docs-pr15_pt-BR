<properties
   pageTitle="Azure AD Connect: Conceitos de Design | Microsoft Azure"
   description="Este tópico detalha determinadas áreas de design de implementação"
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.custom = "azure-ad-connect"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="09/13/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Conceitos de Design
O objetivo deste tópico é descrever áreas que devem ser consideradas durante o design de implementação do Azure AD Connect. Este tópico é detalhamento em determinadas áreas e esses conceitos são descritos resumidamente em outros tópicos também.

## <a name="sourceanchor"></a>sourceAnchor
O atributo sourceAnchor está definido como *um atributo imutável durante o ciclo de vida de um objeto*. Identifica com exclusividade um objeto como sendo os mesmo objeto locais e no Azure AD. O atributo também é chamado **immutableId** e os dois nomes são usados intercambiáveis.

A palavra imutável, o que é "não podem ser alteradas", é importante neste tópico. Como este valor de atributo não pode ser alterado após ter sido definido, é importante escolher um design que ofereça suporte a seu cenário.

O atributo é usado para os seguintes cenários:

- Quando um novo servidor de mecanismo de sincronização é criado ou recriado após um cenário de recuperação de desastres, esse atributo vincula objetos existentes no Azure AD com objetos locais.
- Se você mover de uma identidade somente na nuvem a um modelo de identidade sincronizada, em seguida, esse atributo permite que objetos objetos existentes "correspondência rígido" no Azure AD com objetos locais.
- Se você usar a federação, esse atributo juntamente com o **userPrincipalName** é usado na declaração para identificar exclusivamente um usuário.

Este tópico aborda sourceAnchor somente em relação aos usuários. As mesmas regras de aplicam a todos os tipos de objeto, mas é apenas para usuários que este problema geralmente é um problema.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Selecionando um atributo sourceAnchor boa
O valor do atributo deve seguir as seguintes regras:

- Ter menos de 60 caracteres
    - Caracteres que não estão sendo-z, A-Z ou 0-9 são codificados e contada como 3 caracteres
- Não contiver um caractere especial: & #92;! # $ % & * + / = ? ^ & #96; { } | ~ (< >) '; : , [ ] " @ _
- Deve ser exclusivo
- Deve ser uma cadeia de caracteres, inteiro ou binário
- Não deve ser baseado no nome do usuário, essas alterações
- Não deve diferenciar maiusculas de minúsculas e evitar valores que podem variar por caso
- Devem ser atribuídas quando o objeto é criado

Se o sourceAnchor selecionado não é do tipo cadeia de caracteres, então Azure AD conectar Base64Encode o valor do atributo para garantir que nenhuma caracteres especiais aparecerá. Se você usar outro servidor de Federação do ADFS, verifique se o servidor também pode Base64Encode o atributo.

O atributo sourceAnchor diferencia maiusculas de minúsculas. Um valor de "Lucianasilva" não é igual a "lucianasilva". Mas você não deve ter dois objetos diferentes com apenas uma diferença em maiuscula.

Se você tiver uma única floresta local, em seguida, o atributo que você deve usar é **objectGUID**. Isso também é o atributo usado quando você usar configurações express no Azure AD Connect e também o atributo usado pelo DirSync.

Se você tiver várias florestas e não mover usuários entre domínios e florestas, **objectGUID** é um atributo boa usar mesmo nesse caso.

Se você mover os usuários entre florestas e domínios, você deve encontrar um atributo que não é alterado ou pode ser movido com os usuários durante a movimentação. Uma abordagem recomendada é apresentar um atributo sintético. Um atributo que pode conter algo parecido com um GUID seriam adequado. Durante a criação de objeto, um novo GUID é criado e data do logon do usuário. Uma regra de sincronização personalizadas pode ser criada no servidor do mecanismo de sincronização para criar esse valor com base em **objectGUID** e atualizar o atributo selecionado no ADICIONA. Quando você move o objeto, certifique-se também copiar o conteúdo desse valor.

Outra solução é escolher um atributo existente que você sabe que não é alterado. Atributos comumente usados incluem **employeeID**. Se você considerar um atributo que contém letras, verifique se não há que nenhuma chance de maiusculas e minúsculas (maiusculo versus minúsculas) pode alterar para o valor do atributo. Bad atributos que não devem ser usados incluem esses atributos com o nome do usuário. Em um casamento ou Divórcio, o nome deve alterar, que não é permitido para este atributo. Isso também é um motivo por atributos como **userPrincipalName**, **email**e **targetAddress** não são mesmo possíveis selecionar no Assistente de instalação do Azure AD Connect. Esses atributos também contêm o @-character, que não é permitido na sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Alterar o atributo sourceAnchor
O valor do atributo sourceAnchor não pode ser alterado depois que o objeto foi criado no Azure AD e a identidade é sincronizada.

Por esse motivo, as seguintes restrições se aplicam ao Azure AD Connect:

- O atributo sourceAnchor só pode ser definido durante a instalação inicial. Se você executar novamente o Assistente de instalação, essa opção é somente leitura. Se você precisar alterar essa configuração, você deve desinstalar e reinstalar.
- Se você instalar outro servidor do Azure AD Connect, você deve selecionar o mesmo atributo de sourceAnchor usado como anteriormente. Se você anteriormente estavam usando o DirSync e move para o Azure AD Connect, você deve usar o **objectGUID** desde que é o atributo usado pelo DirSync.
- Se o valor de sourceAnchor for alterado após o objeto foi exportado para o Azure AD, em seguida, Azure AD Connect gera um erro de sincronização e não permitir que as alterações mais em que o objeto antes do problema foi resolvido e o sourceAnchor é alterado novamente no diretório de origem.

## <a name="azure-ad-sign-in"></a>Azure AD entrar
Ao integrar seu diretório local com o Azure AD, é importante entender como as configurações de sincronização podem afetar o usuário de maneira autentica. Azure AD usa userPrincipalName (UPN) para autenticar o usuário. No entanto, quando você sincroniza seus usuários, você deve escolher o atributo a ser usado para o valor de userPrincipalName cuidadosamente.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Escolhendo o atributo userPrincipalName
Quando você estiver selecionando o atributo para fornecer o valor de nome UPN a ser usado em um Azure deve garantir

- Os valores de atributo está de acordo com a sintaxe UPN (RFC 822), que é que ele deve estar no formatousername@domain
- O sufixo os valores corresponde a um dos domínios personalizados verificados no Azure AD

Em configurações expressas, a opção equivalente para o atributo é userPrincipalName. Se o atributo userPrincipalName não contém o valor que você deseja que os usuários para entrar no Azure, você deve escolher a **Instalação personalizada**.

### <a name="custom-domain-state-and-upn"></a>Estado de domínio personalizado e UPN
É importante garantir que não há um domínio verificado para o sufixo UPN.

John é um usuário em contoso.com. Deseja João para usar o UPN local john@contoso.com para entrar no Azure após você sincronizou usuários ao seu contoso.onmicrosoft.com de diretório do Azure AD. Para fazer isso, você precisa adicionar e verificar contoso.com como um domínio personalizado no Azure AD antes de iniciar a sincronização de usuários. Se o sufixo UPN do John, por exemplo, contoso.com, não coincidir com um domínio verificado no Azure AD, o Azure AD substitui o sufixo UPN por contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Não roteáveis local domínios e UPN Azure AD
Algumas organizações têm domínios não roteáveis, como contoso. local ou domínios de única etiqueta simples como contoso. Você não é capaz de verificar um domínio não roteáveis no Azure AD. Azure AD Connect pode sincronizar com apenas um domínio verificado no Azure AD. Quando você cria um diretório Azure AD, ele cria um domínio roteável que torna-se o domínio padrão para o Azure AD por exemplo, contoso.onmicrosoft.com. Portanto, é necessário verificar qualquer outro domínio roteável em tal cenário, caso você não deseja sincronizar com o domínio do onmicrosoft.com padrão.

Leia [Adicionar seu nome de domínio personalizado ao Azure Active Directory](active-directory-add-domain.md) para obter mais informações sobre como adicionar e verificando domínios.

Azure AD Connect detecta se você estiver executando em um ambiente de domínio não roteáveis e seria adequadamente avisá-lo de prosseguir com configurações express. Se você estiver operando em um domínio não roteáveis, é provável que o UPN dos usuários também tenha sufixos não roteáveis. Por exemplo, se você estiver executando em contoso. local, o Azure AD Connect sugere que você para usar configurações personalizadas em vez de usar configurações express. Usando configurações personalizadas, será possível especificar o atributo que deve ser usado como UPN para entrar no Azure depois que os usuários estão sincronizados com o Azure AD.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
