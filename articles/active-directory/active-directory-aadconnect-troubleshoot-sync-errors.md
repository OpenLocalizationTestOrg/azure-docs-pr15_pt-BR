<properties
    pageTitle="Azure AD Connect: Solucionando erros durante a sincronização | Microsoft Azure"
    description="Explica como solucionar problemas encontrados durante a sincronização com o Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="troubleshooting-errors-during-synchronization"></a>Solucionando problemas de erros durante a sincronização
Podem ocorrer erros quando dados de identidade são sincronizados do Windows Server Active Directory (AD DS) ao Azure Active Directory (AD Azure). Este artigo fornece uma visão geral dos diferentes tipos de erros de sincronização, alguns dos cenários possíveis que causa esses erros e possíveis maneiras para corrigir os erros. Este artigo inclui os tipos de erro comuns e não pode cobrir todos os possíveis erros.

 Este artigo pressupõe que o leitor esteja familiarizado com subjacente [projetar conceitos de Azure AD e Azure AD Connect](active-directory-aadconnect-design-concepts.md).

Com a versão mais recente do Azure AD Connect \(agosto 2016 ou superior\), um relatório de erros de sincronização está disponível no [Portal do Azure](https://aka.ms/aadconnecthealth) como parte do Azure AD conectar integridade para sincronização.


Começando em 1 de setembro de 2016 [Azure Active Directory duplicar atributo resiliência](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) recurso será ativado por padrão para todas as *novas* Azure Active Directory locatários. Este recurso será automaticamente ativado para locatários existentes nos próximos meses.

Azure AD Connect realiza 3 tipos de operações de diretórios mantém em sincronia: importar, sincronização e exportar. Erros podem ocorrer em todas as operações. Este artigo aborda principalmente erros durante a exportação para o Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Erros durante a exportação para o Azure AD
A seção seguinte descreve os diferentes tipos de erros de sincronização que podem ocorrer durante a operação de exportação para o Azure AD utilizando o conector Azure AD. Esse conector pode ser identificado pelo formato de nome sendo "contoso. *onmicrosoft.com*".
Erros durante a exportação para o Azure AD indicam que a operação \(adicionar, atualizar e excluir etc\) tentou por Azure AD Connect \(mecanismo de sincronização\) no Active Directory do Azure falha.

![Visão geral de erros de exportação](.\media\active-directory-aadconnect-troubleshoot-sync-errors\Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Erros de incompatibilidade de dados
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Descrição
- Quando o Azure AD Connect \(mecanismo de sincronização\) instrui o Azure Active Directory para adicionar ou atualizar objetos, o objeto de entrada usando o atributo **sourceAnchor** ao atributo **immutableId** dos objetos no Azure AD corresponde ao Azure AD. Essa correspondência é chamada de um **Disco rígido correspondem**.
- Quando o Azure AD **não localizar** qualquer objeto que corresponde a **immutableId** atributo com o atributo **sourceAnchor** do objeto recebido, antes de provisionar um novo objeto, ele volta para usar os atributos ProxyAddresses e UserPrincipalName para encontrar uma correspondência. Essa correspondência é chamada um **Corresponder suave**. O corresponder suave é criado para coincidir objetos já presentes no Azure AD (que são originar no Azure AD) com os novos objetos sendo adicionado/atualizado durante a sincronização que representam a mesma entidade (usuários, grupos) no local.
- Erro de **InvalidSoftMatch** ocorre quando a correspondência de disco rígida não localizar qualquer objeto correspondente **e** suave corresponder localiza um objeto correspondente, mas esse objeto tem um valor diferente de *immutableId* de *SourceAnchor*, sugerindo que o objeto correspondente foi sincronizado com outro objeto do Active Directory local entrada do objeto.

Em outras palavras, em ordem para a correspondência suave funcione, o objeto a ser correspondido suaves com não deve ter qualquer valor para o *immutableId*. Se qualquer objeto com *immutableId* definida com um valor está falhando o disco rígido-correspondência mas satisfaz os critérios de correspondência de suaves, que a operação resultaria em um erro de sincronização de InvalidSoftMatch.

Esquema do Azure Active Directory não permitir que dois ou mais objetos têm o mesmo valor dos atributos a seguir. \(Isso não é uma lista completa.\)

- ProxyAddresses
- UserPrincipalName
- onPremisesSecurityIdentifier
- ID do objeto

>[AZURE.NOTE] Recurso de [atributo do Azure AD duplicar atributo resiliência](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) também está sendo implantado como o comportamento padrão do Active Directory do Azure.  Isso irá reduzir o número de erros de sincronização visto por Azure AD Connect (bem como outros clientes de sincronização) fazendo Azure AD mais flexível da maneira que trata dobro ProxyAddresses e UserPrincipalName atributos presente em ambientes locais AD. Este recurso não corrigir os erros de duplicação. Então os dados ainda precisam ser corrigido. Mas permite provisionamento de novos objetos que estão bloqueados caso contrário da sendo provisionado devido a valores duplicados no Azure AD. Isso também será reduzir o número de erros de sincronização retornados para o cliente de sincronização.
Se esse recurso estiver habilitado para seu locatário, você não verá os erros de sincronização de InvalidSoftMatch vistos durante a configuração de novos objetos.


#### <a name="example-scenarios-for-invalidsoftmatch"></a>Cenários de exemplo para InvalidSoftMatch
1. Dois ou mais objetos com o mesmo valor do atributo ProxyAddresses existe no local do Active Directory. Somente um está obtendo provisionado no Azure AD.
2. Dois ou mais objetos com o mesmo valor de userPrincipalName existir no local do Active Directory. Somente um está obtendo provisionado no Azure AD.
3. Um objeto foi adicionado no local no Active Directory com o mesmo valor do atributo ProxyAddresses como de um objeto existente no Active Directory do Azure. O objeto adicionado localmente não está recebendo provisionado no Active Directory do Azure.
4. Um objeto foi adicionado no local do Active Directory com o mesmo valor do atributo userPrincipalName que de uma conta no Active Directory do Azure. O objeto não está recebendo provisionado no Active Directory do Azure.
5. Uma conta sincronizada foi movida da floresta A floresta b Azure AD Connect (mecanismo de sincronização) estava usando o atributo ObjectGUID para calcular o SourceAnchor. Após a movimentação de floresta, o valor da SourceAnchor é diferente. O novo objeto (da floresta B) está falhando sincronizar com o objeto existente no Azure AD.
6. Um objeto sincronizado obtido excluído acidentalmente do local do Active Directory e um novo objeto foi criado no Active Directory para a mesma entidade (como usuário) sem excluir a conta do Azure Active Directory. A nova conta Falha ao sincronizar com o objeto Azure AD existente.
7. Azure AD Connect foi desinstalado e reinstalado. Durante a instalação novamente, um atributo diferente foi escolhido como o SourceAnchor. Todos os objetos que anteriormente tinham sincronizadas parado sincronização com o erro InvalidSoftMatch.

#### <a name="example-case"></a>Caso de exemplo:
1. **Paulo Smith** é um usuário sincronizado no Active Directory do Azure do local do Active Directory de *contoso.com*
2. Bob Smith **UserPrincipalName** está definido como **bobs@contoso.com**.
3. **"abcdefghijklmnopqrstuv = ="** é **SourceAnchor** calculados por Azure AD Connect usando Bob Smith **objectGUID** no Active Directory local, que é o **immutableId** para Bob Smith no Active Directory do Azure.
4. Paulo também tem o seguinte valores para o atributo **proxyAddresses** :
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
5.  Um novo usuário, **José de Paulo**, é adicionado para o local no Active Directory.
6. De Bob Taylor **UserPrincipalName** está definido como **bobt@contoso.com**.
7. **"abcdefghijkl0123456789 = =" "** é o **sourceAnchor** calculados por Azure AD Connect usando de Bob Taylor **objectGUID** de ativado local do Active Directory. Objeto de Bob Taylor não foi sincronizado ao Azure Active Directory ainda.
8. Bob Taylor tem os seguintes valores para o atributo proxyAddresses
    - smtp:bobt@contoso.com
    - smtp:bob.taylor@contoso.com
    - **smtp:bob@contoso.com**
9. Durante a sincronização, Azure AD Connect reconhecerá a adição de Bob Taylor no local do Active Directory e pergunte Azure AD para fazer a mesma alteração.
10. Azure AD primeiro executará correspondência de disco rígida. Ou seja, ele irá procurar se houver qualquer objeto com o immutableId igual a "abcdefghijkl0123456789 = =". Coincidir rígido falhará como nenhum outro objeto no Azure AD terão que immutableId.
11. Azure AD tentará suaves-correspondência Bob Taylor. Ou seja, ele irá procurar se houver qualquer objeto com proxyAddresses igual a três valores, incluindosmtp:bob@contoso.com
12. Azure AD encontrará objeto Bob Smith aos critérios suaves-correspondência. Mas esse objeto tem o valor de immutableId = "abcdefghijklmnopqrstuv = =". que indica que esse objeto foi sincronizado de outro objeto do Active Directory local. Portanto, Azure AD suaves não-correspondência desses objetos e resultará em um erro de sincronização de **InvalidSoftMatch** .

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Como corrigir o erro de InvalidSoftMatch
O motivo mais comum para o erro InvalidSoftMatch é dois objetos com diferentes SourceAnchor \(immutableId\) têm o mesmo valor para os atributos ProxyAddresses e/ou UserPrincipalName, que são usados durante o processo de correspondência suaves no Azure AD. Para corrigir a correspondência de suave inválidos

1.  Identificar o dobro proxyAddresses, userPrincipalName ou outro valor de atributo que está causando o erro. Também identificar quais dois \(ou mais\) objetos estão envolvidos no conflito. O relatório gerado pelo [Azure AD conectar integridade para sincronização](https://aka.ms/aadchsyncerrors) pode ajudá-lo a identificar os dois objetos.
2. Identifique qual objeto deve continuar a ter o valor duplicado e qual objeto não deve.
3. Remova o valor duplicado do objeto que não deve ter esse valor. Observe que você deve fazer a alteração no diretório de onde o objeto é provenientes de. Em alguns casos, talvez você precise excluir um dos objetos em conflito.
4. Se você tiver feito a alteração no local no AD, permitem Azure AD Connect sincronizar a alteração.

Observe que o relatório de erros de sincronização do Azure AD conectar funcionamento para sincronizar é atualizado a cada 30 minutos e inclui os erros desde a última tentativa de sincronização.

>[AZURE.NOTE] ImmutableId, por definição, não deve alterar no tempo de vida do objeto. Se o Azure AD Connect não foi configurado com alguns dos cenários em mente na lista acima, você poderia acabar em uma situação onde o Azure AD Connect calcula um valor diferente do SourceAnchor para o objeto do AD que representa a mesma entidade (mesmo usuário/grupo/contato etc) que tenha um objeto de AD Azure existente que você deseja continuar usando.

#### <a name="related-articles"></a>Artigos relacionados
- [Atributos inválidos ou duplicados impedem a sincronização de diretório no Office 365](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Descrição
Quando o Azure AD tenta coincidir flexível dois objetos, é possível que dois objetos de diferentes "tipo de objeto" (como usuário, grupo, contato etc.) tem os mesmos valores para os atributos usados para executar a correspondência suave. Como duplicação desses atributos não é permitida no Azure AD, a operação pode resultar em erro de sincronização "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Cenários de exemplo do erro ObjectTypeMismatch
- Um grupo de segurança habilitado para email é criado no Office 365. Administração adiciona um novo usuário ou contato no local AD (que não está sincronizada ao Azure AD ainda) com o mesmo valor para o atributo ProxyAddresses do grupo do Office 365.

#### <a name="example-case"></a>Caso de exemplo

1. Administrador cria um novo grupo de segurança habilitado para email no Office 365 para o departamento de impostos e fornece um endereço de email como tax@contoso.com. Isso atribui atributo ProxyAddresses para esse grupo com o valor de**smtp:tax@contoso.com**
2. Um novo usuário ingressa Contoso.com e uma conta é criada para o usuário local com o proxyAddress como**smtp:tax@contoso.com**
3. Ao Azure AD Connect sincronizará a nova conta de usuário, ele receberá o erro "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Como corrigir o erro de ObjectTypeMismatch
O motivo mais comum para o erro ObjectTypeMismatch é dois objetos de tipo diferente (usuário, grupo, contato etc.) têm o mesmo valor para o atributo ProxyAddresses. Para corrigir o ObjectTypeMismatch:

1.  Identificar o dobro proxyAddresses (ou outro atributo) valor que está causando o erro. Também identificar quais dois \(ou mais\) objetos estão envolvidos no conflito. O relatório gerado pelo [Azure AD conectar integridade para sincronização](https://aka.ms/aadchsyncerrors) pode ajudá-lo a identificar os dois objetos.
2. Identifique qual objeto deve continuar a ter o valor duplicado e qual objeto não deve.
3. Remova o valor duplicado do objeto que não deve ter esse valor. Observe que você deve fazer a alteração no diretório de onde o objeto é provenientes de. Em alguns casos, talvez você precise excluir um dos objetos em conflito.
4. Se você tiver feito a alteração no local no AD, permitem Azure AD Connect sincronizar a alteração. Relatório de erros de sincronização do Azure AD conectar funcionamento para sincronização for atualizado a cada 30 minutos e inclui os erros desde a última tentativa de sincronização.


## <a name="duplicate-attributes"></a>Atributos duplicados
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Descrição
Esquema do Azure Active Directory não permitir que dois ou mais objetos têm o mesmo valor dos atributos a seguir. Essa é a que cada objeto no Azure AD é forçado a ter um valor exclusivo desses atributos em uma determinada instância.

- ProxyAddresses
- UserPrincipalName

Se tentativas Azure AD Connect adicionar um novo objeto ou atualizar um objeto existente com um valor para os atributos acima que já foi atribuído a outro objeto no Active Directory do Azure, a operação resultará em erro de sincronização "AttributeValueMustBeUnique".
#### <a name="possible-scenarios"></a>Possíveis cenários:
1. Valor de duplicada é atribuído a um objeto já sincronizado, que está em conflito com outro objeto sincronizado.

#### <a name="example-case"></a>Caso de exemplo:
1. **Paulo Smith** é um usuário sincronizado no Active Directory do Azure do local do Active Directory de contoso.com
2. Bob Smith **UserPrincipalName** local está definido como **bobs@contoso.com**.
3. Paulo também tem o seguinte valores para o atributo **proxyAddresses** :
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
4. Um novo usuário, **José de Paulo**, é adicionado para o local no Active Directory.
5. De Bob Taylor **UserPrincipalName** está definido como **bobt@contoso.com**.
6. **Paulo José** tem os seguintes valores para o atributo **ProxyAddresses** i. smtp:bobt@contoso.comII. smtp:bob.taylor@contoso.com
7. Objeto de Bob Taylor é sincronizado com o Azure AD com êxito.
8. Administração decidiu atualizar atributo **ProxyAddresses** de Bob Taylor com o seguinte valor: eu. **smtp:bob@contoso.com**
9. Azure AD tentará atualizar o objeto de Bob Taylor no Azure AD com o valor acima, mas que operação falhará como que valor ProxyAddresses já está atribuído a Bob Smith, resultando em erro de "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Como corrigir o erro de AttributeValueMustBeUnique
O motivo mais comum para o erro AttributeValueMustBeUnique é dois objetos com diferentes SourceAnchor \(immutableId\) têm o mesmo valor para os atributos ProxyAddresses e/ou UserPrincipalName. Para corrigir o erro de AttributeValueMustBeUnique

1.  Identificar o dobro proxyAddresses, userPrincipalName ou outro valor de atributo que está causando o erro. Também identificar quais dois \(ou mais\) objetos estão envolvidos no conflito. O relatório gerado pelo [Azure AD conectar integridade para sincronização](https://aka.ms/aadchsyncerrors) pode ajudá-lo a identificar os dois objetos.
2. Identifique qual objeto deve continuar a ter o valor duplicado e qual objeto não deve.
3. Remova o valor duplicado do objeto que não deve ter esse valor. Observe que você deve fazer a alteração no diretório de onde o objeto é provenientes de. Em alguns casos, talvez você precise excluir um dos objetos em conflito.
4. Se você tiver feito a alteração no local no AD, permitem Azure AD Connect sincronizar a alteração do erro obter corrigido.

#### <a name="related-articles"></a>Artigos relacionados
-[Atributos inválidos ou duplicados impedem a sincronização de diretório no Office 365](https://support.microsoft.com/en-us/kb/2647098)


## <a name="data-validation-failures"></a>Falhas de validação de dados
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Descrição
Azure Active Directory impõe várias restrições sobre os dados antes de permitir que dados sejam gravados no diretório. Isso é para garantir que os usuários finais Obtenha as melhores experiências possíveis ao usar os aplicativos que dependem de dados.
#### <a name="scenarios"></a>Cenários
a. O valor do atributo UserPrincipalName tem caracteres inválida ou não suportada.
b. O atributo UserPrincipalName não segue o formato obrigatório.
#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Como corrigir o erro de IdentityDataValidationFailed

a. Certifique-se de que o atributo userPrincipalName tem suporte caracteres e formato necessário.

#### <a name="related-articles"></a>Artigos relacionados
- [Preparar para provisionar usuários por meio da sincronização de diretório para o Office 365]( https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)


### <a name="datavalidationfailed"></a>DataValidationFailed
#### <a name="description"></a>Descrição
Esse é um caso muito específico que resulta em um erro de sincronização **"DataValidationFailed"** quando o sufixo do UserPrincipalName de um usuário for alterado de um domínio federado para outro domínio federado.

#### <a name="scenarios"></a>Cenários
Para um usuário sincronizado, o sufixo UserPrincipalName foi alterado de um domínio federado para outro domínio federado no local. Por exemplo, *UserPrincipalName = bob@contoso.com * foi alterado para *UserPrincipalName = bob@fabrikam.com *.

#### <a name="example"></a>Exemplo
1. Bob Smith, uma conta para Contoso.com é adicionado como um novo usuário no Active Directory com o UserPrincipalNamebob@contoso.com
2. Paulo move para uma divisão diferente da Contoso.com chamado Fabrikam.com e seu UserPrincipalName é alterado parabob@fabrikam.com
3. Os domínios contoso.com e fabrikam.com são domínios federados com o Active Directory do Azure.
4. UserPrincipalName de Paulo não é atualizado e resulta em um erro de sincronização "DataValidationFailed".

#### <a name="how-to-fix"></a>Como corrigir
Se sufixo de UserPrincipalName de um usuário foi atualizado da bob@ **contoso.com** para bob@ **fabrikam.com**, onde **contoso.com** e **fabrikam.com** estão **domínios federados**, em seguida, siga as etapas a seguir para corrigir o erro de sincronização

1. Atualizar UserPrincipalName do usuário no Azure AD de bob@contoso.com para bob@contoso.onmicrosoft.com. Você pode usar o seguinte comando do PowerShell com o módulo do PowerShell do Azure AD:`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`

2. Permitir que o próximo ciclo de sincronização tentar a sincronização. A sincronização de tempo será bem sucedida e ele atualizará o UserPrincipalName do Paulo para bob@fabrikam.com conforme esperado.


## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Descrição
Quando um atributo excede o limite de tamanho permitido, o limite de tamanho ou o limite de contagem definidos pelo esquema do Active Directory do Azure, a operação de sincronização resultará em erro de sincronização do **LargeObject** ou **ExceededAllowedLength** . Geralmente este erro ocorre para os seguintes atributos

- userCertificate
- thumbnailPhoto
- proxyAddresses

### <a name="possible-scenarios"></a>Cenários possíveis
1. Atributo de userCertificate de Paulo está armazenando muitos certificados atribuídos ao Paulo. Eles podem incluir certificados mais antigos, expirados.
2. ThmubnailPhoto de Paulo definir no Active Directory é muito grande para ser sincronizado no Azure AD.
3. Durante o preenchimento automático de atributo ProxyAddresses no Active Directory, um objeto tem atribuído > 500 ProxyAddresses.

### <a name="how-to-fix"></a>Como corrigir

1. Certifique-se de que o atributo causando o erro é com o limite permitido.

## <a name="related-links"></a>Links relacionados
- [Localizar objetos do Active Directory no Centro Administrativo do Active Directory] (https://technet.microsoft.com/library/dd560661.aspx)
- [Como consultar o Active Directory do Azure para um objeto usando o Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
