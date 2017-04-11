<properties
    pageTitle="Sincronização do Azure AD Connect: Noções básicas sobre declarativa provisionamento | Microsoft Azure"
    description="Explica o modelo de configuração de provisionamento declarativa no Azure AD Connect."
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
    ms.date="08/29/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Sincronização do Azure AD Connect: Noções básicas sobre declarativa provisionamento
Este tópico explica o modelo de configuração do Azure AD Connect. O modelo é chamado de provisionamento declarativa e permite que você altere uma configuração com facilidade. Muitas coisas descritas neste tópico são avançadas e não é necessário para a maioria dos cenários de cliente.

## <a name="overview"></a>Visão geral
Provisionamento declarativa está processando objetos provenientes um diretório de conectada de origem e determina como os objetos e atributos devem ser transformados de uma fonte para um destino. Um objeto é processado em um pipeline de sincronização e o pipeline é o mesmo usado para regras de entrada e saídas. Uma regra de entrada é de um espaço de conector para o metaverso e é uma regra de saída do metaverso para um espaço de conector.

![Pipeline de sincronização](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

O pipeline tem vários módulos diferentes. Cada um é responsável por um conceito na sincronização do objeto.

![Pipeline de sincronização](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

- Fonte, o objeto de origem
- [Escopo](#scope), localiza todas as regras de sincronização que estão no escopo
- [Ingressar](#join), determina o relacionamento entre espaço do conector e metaverso
- [Transformar](#transform), calcula como atributos devem ser transformados e fluem
- [Precedência](#precedence), resolve conflitantes contribuições de atributo
- Destino, o objeto de destino

## <a name="scope"></a>Escopo
O módulo de escopo está avaliando um objeto e determina as regras que estão no escopo e devem ser incluídas no processamento. Dependendo dos valores de atributos no objeto, regras de sincronização diferentes são avaliadas para do escopo. Por exemplo, um usuário desabilitado com nenhuma caixa de correio do Exchange têm diferentes regras que um usuário habilitado com uma caixa de correio.  
![Escopo](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

O escopo é definido como cláusulas e grupos. As cláusulas estão dentro de um grupo. Um e lógico é usado entre todas as cláusulas em um grupo. Por exemplo, (Departamento = TI e país = Dinamarca). Um OR lógico é usado entre grupos.

![Escopo](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
O escopo nesta imagem deve ser lido como (Departamento = TI e país = Dinamarca) ou (país = Suécia). Se o grupo 1 ou 2 é avaliada como true, a regra está no escopo.

O módulo de escopo suporta as seguintes operações.

Operação | Descrição
--- | ---
IGUAL, NOTEQUAL | Uma comparação de cadeia de caracteres que avalia se valor for igual ao valor do atributo. Atributos de valores múltiplos, consulte ISIN e ISNOTIN.
LESSTHAN, LESSTHAN_OR_EQUAL | Uma comparação de cadeia de caracteres que avalia se valor for menos do valor do atributo.
CONTÉM, NOTCONTAINS | Uma comparação de cadeia de caracteres que avalia se o valor pode ser encontrado em algum lugar dentro do valor do atributo.
STARTSWITH, NOTSTARTSWITH | Uma comparação de cadeia de caracteres que avalia se valor for no início do valor do atributo.
ENDSWITH, NOTENDSWITH | Uma comparação de cadeia de caracteres que avalia se valor for no final do valor do atributo.
GREATERTHAN, GREATERTHAN_OR_EQUAL | Uma comparação de cadeia de caracteres que avalia se o valor for maior que o valor do atributo.
ISNULL, ISNOTNULL | Avalia se o atributo está ausente do objeto. Se o atributo não estiver presente e, portanto, null, a regra está no escopo.
ISIN, ISNOTIN | Avalia se o valor estiver presente no atributo definido. Essa operação é a variação de valores múltiplos de igual e NOTEQUAL. O atributo deve para ser um atributo de valores múltiplos e se o valor pode ser encontrado em qualquer um dos valores de atributo, em seguida, a regra está no escopo.
ISBITSET, ISNOTBITSET | Avalia se um determinado bit é definido. Por exemplo, pode ser usada para avaliar os bits em userAccountControl para ver se um usuário está ativado ou desativado.
ISMEMBEROF, ISNOTMEMBEROF | O valor deve conter um DN a um grupo no espaço do conector. Se o objeto é um membro do grupo especificado, a regra está no escopo.

## <a name="join"></a>Junção
O módulo de junção no pipeline de sincronização é responsável por localizar a relação entre o objeto na fonte e um objeto de destino. Em uma regra de entrada, essa relação seria um objeto em um espaço de conector localizando uma relação a um objeto no metaverso.  
![Ingressar entre cs e mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
O objetivo é ver que se há um objeto já no metaverse, criado por outro conector, ele deve ser associado. Por exemplo, em uma floresta de conta-recurso o usuário da floresta conta deve ser associado com o usuário na floresta de recursos.

Junções são usadas principalmente em regras de entrada para unir objetos de espaço de conector ao mesmo objeto metaverso.

Junções são definidas como um ou mais grupos. Dentro de um grupo, você tem cláusulas. Um e lógico é usado entre todas as cláusulas em um grupo. Um OR lógico é usado entre grupos. Os grupos são processados na ordem de cima para baixo. Quando um grupo encontrou exatamente uma correspondência com um objeto de destino, não há outras regras de associação são avaliadas. Se zero ou mais de um objeto está localizado, o processamento continua para o próximo grupo de regras. Por esse motivo, as regras devem ser criado na ordem mais explícito primeiro e mais confusa no final.  
![Ingressar em definição](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
Junções nesta imagem são processadas de cima para baixo. Primeiro o pipeline de sincronização vê se existe uma correspondência em employeeID. Caso contrário, a segunda regra vê se o nome da conta pode ser usado para unir os objetos. Se isso não for uma correspondência ou, a terceira e última regra é uma correspondência mais confusa usando o nome do usuário.

Se todas as regras de junção tenham sido avaliadas e não houver exatamente uma correspondência, o **Tipo de vínculo** na página de **Descrição** é usado. Se essa opção estiver definida para **provisionar**, um novo objeto de destino é criado.  
![Provisionar ou junção](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

Um objeto deve ter apenas uma regra de sincronização única com regras de junção no escopo. Se houver várias regras de sincronização onde junção é definida, ocorrerá um erro. Precedência não é usada para resolver conflitos de junção. Um objeto deve ter uma regra de junção no escopo para atributos a fluir com a mesma direção de entrada/saída. Se você precisar atributos de fluxo de entrada e saída ao mesmo objeto, você deve ter uma regra de saída de sincronização com associação e também de entrada.

Junção saída tem um comportamento especial quando tentar provisionar um objeto para um espaço de conector de destino. O atributo DN é usado para tentar primeiro uma reversão-participar. Se já houver um objeto no espaço de conector de destino com o mesmo DN, os objetos são Unidos.

O módulo de junção é avaliado apenas uma vez quando uma nova regra de sincronização entra em escopo. Quando um objeto ingressou, ele não está saindo mesmo se os critérios de união já não estiver satisfeito. Se você quiser disjoin um objeto, a regra de sincronização que ingressou os objetos deve ir sair do escopo.

### <a name="metaverse-delete"></a>Excluir metaverso
Um objeto metaverso permanecerá como longa como não há uma regra de sincronização no escopo com o **Tipo de vínculo** definido como **provisionar** ou **StickyJoin**. Um StickyJoin é usado quando um conector não é permitido para provisionar um novo objeto para o metaverso, mas quando ela ingressou, devem ser excluída na fonte antes do objeto metaverso é excluído.

Quando um objeto metaverso é excluído, todos os objetos associados a uma regra de saída sincronização marcada para **provisionar** são marcados para uma exclusão.

## <a name="transformations"></a>Transformações
As transformações são usadas para definir como atributos deverão fluir da origem para o destino. Os fluxos podem ter um dos seguintes **tipos de fluxo**: diretos, constante ou expressão. Um fluxo direto, flua um valor de atributo como-está com nenhum transformações adicionais. Um valor constante define o valor especificado. Uma expressão usa a linguagem de expressão de provisionamento declarativa para expressar como a transformação deve ser. Os detalhes para a linguagem de expressão podem ser encontrados no tópico da [linguagem de expressão de provisionamento com declarativa de compreensão](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) .

![Provisionar ou junção](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

A caixa de seleção **Aplicar uma vez** define que o atributo só deve ser definido quando o objeto é criado inicialmente. Por exemplo, esta configuração pode ser usada para definir uma senha inicial para um novo objeto de usuário.

### <a name="merging-attribute-values"></a>Mesclagem de valores de atributo
Nos fluxos de atributo há uma configuração para determinar se os atributos de valores múltiplos devem ser mesclados em vários conectores diferentes. O valor padrão é de **atualização**, que indica que a regra de sincronização com prioridade mais alta deve win.

![Tipos de mesclagem](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

Também há **Mesclar** e **MergeCaseInsensitive**. Essas opções permitem que você mesclar os valores de diferentes fontes. Por exemplo, ele pode ser usado para mesclar o atributo proxyAddresses ou de membro de várias florestas diferentes. Quando você usa esta opção, todas as regras de sincronização no escopo de um objeto devem usar o mesmo tipo de mala direta. Você não pode definir a **atualização** de um conector e a **mesclagem** de outra. Se você tentar, você recebe um erro.

A diferença entre **Mesclar** e **MergeCaseInsensitive** é como processar valores de atributo duplicado. O mecanismo de sincronização garante que os valores duplicados não são inseridos no atributo de destino. Com **MergeCaseInsensitive**, duplicar valores com apenas uma diferença caso não serão presente. Por exemplo, você não verá ambos "SMTP:bob@contoso.com" e "smtp:bob@contoso.com" no atributo de destino. **Mesclar** está examinando somente os valores exatos e vários valores onde não há apenas uma diferença em maiusculas e minúsculas podem estar presentes.

A opção **Substituir** é a mesma que a **atualização**, mas não é usado.

### <a name="control-the-attribute-flow-process"></a>Controlar o processo de fluxo de atributo
Quando várias regras de sincronização de entrada são configuradas para contribuir com o mesmo atributo metaverso, precedência é usada para determinar as vencedoras. A regra de sincronização com prioridade mais alta (menor valor numérico) será o valor de contribuição. O mesmo acontece com regras de saída. A sincronização com maior vence precedência de regras e contribuir o valor a ser diretório conectado.

Em alguns casos, em vez de contribuir um valor, a regra de sincronização deve determinar como outras regras devem se comportar. Há alguns literais especiais usados para esse caso.

Sincronização para regras de entrada, o literal **Nulo** pode ser usado para indicar que o fluxo não tem nenhum valor para contribuir. Outra regra com menor precedência pode contribuir um valor. Se nenhuma regra contribuiu com um valor, o atributo do metaverso é removido. Para uma regra de saída, se **NULL** é o valor final após terem sido processadas todas as regras de sincronização, em seguida, o valor é removido no diretório conectado.

O literal **AuthoritativeNull** é semelhante a **Nulo** , mas com a diferença que nenhum regras de precedência inferiores podem contribuir um valor.

Um fluxo de atributo também pode usar **IgnoreThisFlow**. Ele é semelhante a nulo no sentido de que ele indica nada a contribuição. A diferença é que ela não remove um valor já existente no destino. É como o fluxo de atributo nunca foi lá.

Aqui está um exemplo:

Na *saída para AD - usuário Exchange híbrido* o fluxo a seguir pode ser encontrado:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Essa expressão deve ser lido como: se a caixa de correio do usuário está localizada no Azure AD, fluxo, em seguida, o atributo do Azure AD para AD. Caso contrário, não fluem nada para o Active Directory. Nesse caso, ele prefere manter o valor existente no AD.

### <a name="importedvalue"></a>ImportedValue
A função ImportedValue é diferente de todas as outras funções, como o nome do atributo deve ser colocado entre aspas em vez de colchetes:  
`ImportedValue("proxyAddresses")`.

Geralmente durante a sincronização um atributo usa o valor esperado, mesmo se ele ainda não tiver sido exportado ou um erro foi recebido durante a exportação ("superior da torre"). Uma sincronização entrada pressupõe que um atributo que ainda não foi encontrado um diretório conectado eventualmente atinge-lo. Em alguns casos, é importante sincronizar apenas um valor que foi confirmado por diretório conectado ("holograma e delta torre de importar").

Um exemplo dessa função pode ser encontrado na regra prontos de sincronização *do AD – comuns de usuário do Exchange*. No Exchange híbrido, o valor agregado pelo Exchange online só deve ser sincronizado quando ele foi confirmado que o valor foi exportado com êxito:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Precedência
Quando várias regras de sincronização tentam contribuir o mesmo valor de atributo para o destino, o valor de prioridade é usado para determinar as vencedoras. A regra com prioridade mais alta, o menor valor numérico, será o atributo em um conflito de contribuição.

![Tipos de mesclagem](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

Essa ordem pode ser usado para definir mais precisos fluxos de atributo de um pequeno subconjunto de objetos. Por exemplo, as out--caixa-as regras de Certifique-se de que os atributos de uma conta habilitada (**AccountEnabled de usuário**) tem precedência de outras contas.

Precedência pode ser definida entre conectores. Que permite que os conectores com dados melhores para contribuir valores primeiro.

### <a name="multiple-objects-from-the-same-connector-space"></a>Vários objetos do mesmo espaço do conector
Se você tiver vários objetos no mesmo espaço de conector unido ao mesmo objeto metaverso, precedência deve ser ajustada. Se vários objetos estão no escopo da mesma regra sincronização, o mecanismo de sincronização não é capaz de determinar a precedência. É ambígua qual objeto de origem deve contribuir o valor para o metaverso. Esta configuração é relatada como ambíguos, mesmo se os atributos da fonte de tem o mesmo valor.  
![Vários objetos Unidos ao mesmo objeto mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

Para esse cenário, você precisa alterar o escopo das regras de sincronização para que os objetos de origem tem regras de sincronização diferente no escopo. Que permite que você defina precedência diferentes.  
![Vários objetos Unidos ao mesmo objeto mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre a linguagem de expressão em [Noções básicas sobre expressões de provisionamento declarativa](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
- Consulte como declarativa provisionamento é usado de prontos em [Noções básicas sobre a configuração padrão](active-directory-aadconnectsync-understanding-default-configuration.md).
- Veja como fazer uma mudança prática usando provisionamento declarativa em [como fazer uma alteração para a configuração padrão](active-directory-aadconnectsync-change-the-configuration.md).
- Continue a ler como usuários e contatos funcionam juntos em [Noções básicas sobre usuários e contatos](active-directory-aadconnectsync-understanding-users-and-contacts.md).

**Tópicos de visão geral**

- [Sincronização do Azure AD Connect: entender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)

**Tópicos de referência**

- [Sincronização do Azure AD Connect: referência de funções](active-directory-aadconnectsync-functions-reference.md)
