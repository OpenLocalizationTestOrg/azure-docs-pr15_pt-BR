
<properties
    pageTitle="Usando atributos para criar regras avançadas para associações de grupo na visualização do Active Directory do Azure | Microsoft Azure"
    description="Como criar regras avançadas para membros do grupo dinâmico, incluindo suporte para operadores de regra de expressão e parâmetros."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="curtand"/>


# <a name="using-attributes-to-create-advanced-rules-for-group-membership-in-azure-active-directory-preview"></a>Usando atributos para criar regras avançadas para associações de grupo na visualização do Active Directory do Azure

O portal do Azure oferece a capacidade de criar regras avançadas para habilitar mais complexa com base no atributo dinâmicas associações para os grupos de visualização do Azure Active Directory (AD Azure). [O que é no preview?](active-directory-preview-explainer.md) Este artigo detalha os atributos de regra e sintaxe para criar essas regras avançadas.

## <a name="to-create-the-advanced-rule"></a>Para criar a regra avançada

1.  Entrar no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2.  Selecione **mais serviços**, insira os **usuários e grupos** na caixa de texto e, em seguida, pressione **Enter**.

  ![Gerenciamento de usuários de abertura](./media/active-directory-groups-dynamic-membership-azure-portal/search-user-management.png)

3.  Na lâmina **usuários e grupos** , selecione **todos os grupos**.

  ![Abrindo a lâmina de grupos](./media/active-directory-groups-dynamic-membership-azure-portal/view-groups-blade.png)

4. Na lâmina **usuários e grupos - todos os grupos** , selecione o comando **Adicionar** .

  ![Adicionar novo grupo](./media/active-directory-groups-dynamic-membership-azure-portal/add-group-type.png)

5. No **grupo** blade, insira um nome e uma descrição para o novo grupo. Selecione uma **associação digite** do **Usuário dinâmico** ou **Dispositivo dinâmico**, dependendo se você quiser criar uma regra para usuários ou dispositivos e selecione **Adicionar dinâmica consulta**. Para os atributos usados para regras de dispositivo, consulte [usando atributos para criar regras para objetos de dispositivo](#using-attributes-to-create-rules-for-device-objects).

  ![Adicionar regra de associação dinâmico](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

6. Na lâmina **regras de participação dinâmico** , insira sua regra na caixa **Adicionar associação dinâmica avançada regra** , pressione Enter e, em seguida, selecione **criar** na parte inferior da lâmina.

7. Selecione **criar** na lâmina **grupo** para criar o grupo.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construindo corpo de uma regra avançada

A regra avançada que você pode criar para os membros dinâmicos para grupos é essencialmente uma expressão binária que consiste em três partes e resulta em um resultado true ou false. As três partes são:

- Parâmetro à esquerda
- Operador binário
- Constante à direita

Uma regra avançada completa fica assim: (leftParameter binaryOperator "RightConstant"), onde a abertura e o parêntese de fechamento são necessários para toda a expressão binária, aspas duplas são necessárias para a constante à direita e a sintaxe para o parâmetro esquerda é user.property. Uma regra avançada pode consistir em mais de um binárias expressões separadas por- e, - ou e - operadores lógicos não.

Estes são exemplos de uma regra avançada construído corretamente:

- (user.department - eq "Vendas")- ou (user.department - eq "Marketing")
- (user.department - eq "Vendas")- e - não (user.jobTitle-contém "SDE")

Para a lista completa de parâmetros com suporte e operadores de regra de expressão, consulte as seções a seguir. Para os atributos usados para regras de dispositivo, consulte [usando atributos para criar regras para objetos de dispositivo](#using-attributes-to-create-rules-for-device-objects).

O tamanho total do corpo da sua regra avançada não pode exceder 2048 caracteres.

> [AZURE.NOTE]
>Operações de cadeia de caracteres e regex diferenciam maiusculas de minúsculas. Você também pode executar verificações de nulo, usando $null como uma constante, por exemplo, user.department - eq $null.
Cadeias de caracteres que contém as cotações "deve haver escape usando ' caractere, por exemplo, user.department - eq \`"Vendas".

## <a name="supported-expression-rule-operators"></a>Operadores de regra de expressões com suporte
A tabela a seguir lista todos os operadores de regra de expressão compatíveis e sua sintaxe a ser usado no corpo da regra avançada:

| Operador        | Sintaxe         |
|-----------------|----------------|
| Não é igual a      | -ne            |
| É igual a          | -eq            |
| Não começa com | -notStartsWith |
| Começa com     | -startsWith    |
| Não contém    | -notContains   |
| Contém        | -contém      |
| Não correspondência       | -notMatch      |
| CORRESP           | -corresponder         |


## <a name="query-error-remediation"></a>Correção de erro de consulta
A tabela a seguir lista os possíveis erros e como corrigi-los se ocorrerem

| Erro de análise de consulta     | Uso de erro       | Uso corrigido             |
|-----------------------|-------------------|-----------------------------|
| Erro: Atributo não têm suportado.                                      | user.invalidProperty - eq ("Valor")       | user.department - eq ("valor")<br/>Propriedade deve corresponder um na [lista propriedades de suporte](#supported-properties).                          |
| Erro: Não é suportada no atributo.                       | (user.accountEnabled-contém VERDADEIRO)                                                                               | (user.accountEnabled - eq verdadeiro)<br/>Propriedade é de digite booliano. Usar os operadores com suporte (-eq ou - ne) em tipo booliano da lista acima.                                                                                                                                   |
| Erro: Erro de compilação de consulta.                                      | (user.department - eq "Vendas")- e (user.department - eq "Marketing")(user.userPrincipalName-match"*@domain.ext") | (user.department - eq "Vendas")- e (user.department - eq "Marketing")<br/>Operador lógico deve coincidir um na lista de propriedades com suporte acima. (user.userPrincipalName-corresponder ".*@domain.ext")or(user.userPrincipalName -corresponder "@domain.ext$")Error na expressão regular. |
| Erro: Expressão binário não está no formato correto.                     | user.department – eq ("Vendas") user.department - eq ("Vendas") user.department-eq ("Vendas")                             | (user.accountEnabled - eq verdadeiro)- e (user.userPrincipalName-contém"alias@domain")<br/>Consulta tem vários erros. Parêntese não no lugar certo.                                                                                                                            |
| Erro: Ocorreu um erro desconhecido durante a configuração de associações dinâmicas. | (user.accountEnabled - eq "True" e user.userPrincipalName-contém"alias@domain")                               | (user.accountEnabled - eq verdadeiro)- e (user.userPrincipalName-contém"alias@domain")<br/>Consulta tem vários erros. Parêntese não no lugar certo.                                                                                                                            |

## <a name="supported-properties"></a>Propriedades com suporte
A seguir estão todas as propriedades do usuário que você pode usar na regra avançada:

### <a name="properties-of-type-boolean"></a>Propriedades de digite booliano

Operadores permitidos

* -eq


* -ne


| Propriedades     | Valores permitidos  | Uso                          |
|----------------|-----------------|--------------------------------|
| accountEnabled | Verdadeiro Falso      | user.accountEnabled - eq verdadeiro)  |
| dirSyncEnabled | True null false | (user.dirSyncEnabled - eq verdadeiro) |

### <a name="properties-of-type-string"></a>Propriedades do tipo cadeia de caracteres

Operadores permitidos

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -contém


* -notContains


* -corresponder


* -notMatch

| Propriedades                 | Valores permitidos                                                                                        | Uso                                                     |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Cidade                       | Qualquer valor de cadeia de caracteres ou $null                                                                           | user.city - eq ("valor")                                   |
| país/região                    | Qualquer valor de cadeia de caracteres ou $null                                                                            | user.country - eq ("valor")                                |
| departamento                 | Qualquer valor de cadeia de caracteres ou $null                                                                          | user.department - eq ("valor")                             |
| displayName                | Qualquer valor de cadeia de caracteres                                                                                 | user.displayName - eq ("valor")                            |
| facsimileTelephoneNumber   | Qualquer valor de cadeia de caracteres ou $null                                                                           | user.facsimileTelephoneNumber - eq ("valor")               |
| givenName                  | Qualquer valor de cadeia de caracteres ou $null                                                                           | user.givenName - eq ("valor")                              |
| jobTitle                   | Qualquer valor de cadeia de caracteres ou $null                                                                           | user.jobTitle - eq ("valor")                               |
| mail                       | Qualquer valor de cadeia de caracteres ou $null (endereço SMTP do usuário)                                                  | user.mail - eq ("valor")                                   |
| mailNickName               | Qualquer valor de cadeia de caracteres (alias de email do usuário)                                                            | user.mailNickName - eq ("valor")                           |
| móvel                     | Qualquer valor de cadeia de caracteres ou $null                                                                           | user.mobile - eq ("valor")                                 |
| ID do objeto                   | GUID de objeto do usuário                                                                            | user.objectId - eq ("1111111-1111-1111-1111-111111111111") |
| passwordPolicies           | Nenhum DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |   user.passwordPolicies - eq ("DisableStrongPassword")                                                      |
| physicalDeliveryOfficeName | Qualquer valor de cadeia de caracteres ou $null                                                                            | user.physicalDeliveryOfficeName - eq ("valor")             |
| CEP                 | Qualquer valor de cadeia de caracteres ou $null                                                                            | user.postalCode - eq ("valor")                             |
| preferredLanguage          | Código ISO 639-1                                                                                        | user.preferredLanguage - eq ("en-US")                      |
| sipProxyAddress            | Qualquer valor de cadeia de caracteres ou $null                                                                            | user.sipProxyAddress - eq ("valor")                        |
| estado                      | Qualquer valor de cadeia de caracteres ou $null                                                                            | user.state - eq ("valor")                                  |
| streetAddress              | Qualquer valor de cadeia de caracteres ou $null                                                                            | user.streetAddress - eq ("valor")                          |
| Sobrenome                    | Qualquer valor de cadeia de caracteres ou $null                                                                            | user.surname - eq ("valor")                                |
| telephoneNumber            | Qualquer valor de cadeia de caracteres ou $null                                                                            | user.telephoneNumber - eq ("valor")                        |
| usageLocation              | Código do país com letras dois                                                                           | user.usageLocation - eq ("EUA")                             |
| userPrincipalName          | Qualquer valor de cadeia de caracteres                                                                                     | user.userPrincipalName - eq ("alias@domain")               |
| userType                   | convidado membro $null                                                                                    | user.userType - eq ("Membro")                              |

### <a name="properties-of-type-string-collection"></a>Propriedades do conjunto de cadeia de caracteres de tipo

Operadores permitidos

* -contém


* -notContains

| Poperties      | Valores permitidos                        | Uso                                                |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails     | Qualquer valor de cadeia de caracteres                      | (user.otherMails-contém"alias@domain")           |
| proxyAddresses | SMTP: alias@domain smtp:alias@domain | (user.proxyAddresses-contém "SMTP:alias@domain") |

## <a name="extension-attributes-and-custom-attributes"></a>Atributos de extensão e atributos personalizados
Atributos de extensão e atributos personalizados são compatíveis com regras de participação dinâmico.

Atributos de extensão são sincronizados do AD do servidor de janela local e tire o formato do "ExtensionAttributeX", onde X é igual a 1-15.
Um exemplo de uma regra que usa um atributo de extensão seria

(user.extensionAttribute15 - eq "Marketing")

Atributos personalizados são sincronizados do AD do Windows Server local ou de um aplicativo de SaaS conectado e o formato do "user.extension_[GUID]\__ [atributo]", onde [GUID] é o identificador exclusivo no AAD para o aplicativo que criou o atributo no AAD e [atributo] é o nome do atributo como ela foi criada.
Um exemplo de uma regra que usa um atributo personalizado é

User.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

O nome de atributo personalizado pode ser encontrado no diretório consultando um usuário do atributo usando o Explorador de gráfico e procurando o nome do atributo.

## <a name="direct-reports-rule"></a>Regra de relatórios diretos
Agora você pode preencher membros em um grupo com base no atributo gerente de um usuário.

**Configurar um grupo como um grupo de "Gerente"**

1. Siga as etapas 1 a 5 [para criar a regra avançada](#to-create-the-advanced-rule)e selecione um **tipo de associação** de **Usuário dinâmico**.

2. Na lâmina **regras de participação dinâmico** , insira a regra com a seguinte sintaxe:

    Direcione relatórios para *relatórios diretos para {obectID_of_manager}*. É um exemplo de uma regra válida para relatórios diretos

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    onde o "62e19b97-8b3d-4d4a-a106-4ce66896a863" é a ID do objeto do gerente. O ID do objeto pode ser encontrado no Azure AD na **guia perfil** da página do usuário para o usuário que é o gerente.

3. Ao salvar esta regra, todos os usuários que satisfaçam a regra ingressarão como membros do grupo. Pode levar alguns minutos para o grupo para preencher inicialmente.


## <a name="using-attributes-to-create-rules-for-device-objects"></a>Usando atributos para criar regras para objetos de dispositivo

Você também pode criar uma regra que seleciona objetos de dispositivo para associação em um grupo. Os seguintes atributos de dispositivo podem ser usados:

| Propriedades           | Valores permitidos                  | Uso                                                |
|----------------------|---------------------------------|------------------------------------------------------|
| displayName          | qualquer valor de cadeia de caracteres                | device.displayName - eq ("Pedro Iphone")                 |
| deviceOSType         | qualquer valor de cadeia de caracteres                | device.deviceOSType - eq ("IOS")                      |
| deviceOSVersion      | qualquer valor de cadeia de caracteres                | (dispositivo. OSVersion - eq "9.1")                         |
| isDirSynced          | True null false                 | device.isDirSynced - eq ("true")                      |
| isManaged            | True null false                 | device.isManaged - eq ("false")                       |
| isCompliant          | True null false                 | device.isCompliant - eq ("true")                      |


## <a name="additional-information"></a>Informações adicionais
Estes artigos fornecem informações adicionais sobre grupos no Active Directory do Azure.

* [Consulte grupos existentes](active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionando participantes](active-directory-groups-create-azure-portal.md)
* [Gerenciar as configurações de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerenciar membros de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](active-directory-groups-dynamic-membership-azure-portal.md)
