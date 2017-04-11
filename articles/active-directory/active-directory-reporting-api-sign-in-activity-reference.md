<properties
    pageTitle="Relatório de atividade de entrada do Azure Active Directory Referência API | Microsoft Azure"
    description="Referência para a API de relatório de atividade de entrada do Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Relatório de atividade de entrada do Azure Active Directory Referência API


Este tópico é parte de um conjunto de tópicos sobre o Active Directory do Azure API de relatório.  
Relatórios do Azure AD oferece uma API que permite que você acesse dados de relatório de atividade de entrada usando o código ou ferramentas relacionadas.
O escopo deste tópico é fornecer informações de referência sobre o **entrar API de relatório de atividade**.

Consulte:

- [Atividades de entrada](active-directory-reporting-azure-portal.md#sign-in-activities) para obter mais informações conceituais
- [Introdução ao Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md) para obter mais informações sobre a API de relatórios.

Para dúvidas, problemas ou comentários, entre em contato com o [AAD relatórios ajudam](mailto:aadreportinghelp@microsoft.com).



## <a name="who-can-access-the-api-data"></a>Quem pode acessar os dados de API?

- Usuários na função de administrador de segurança ou leitor de segurança

- Administradores globais

- Qualquer aplicativo que tenha autorização para acessar a API (o autorização de aplicativo pode ser configurado somente com base na permissão de Administrador Global)



## <a name="prerequisites"></a>Pré-requisitos

Para acessar este relatório por meio da API de relatórios, você deve ter:

- Um [Azure Active Directory Premium P1 ou P2 edition](active-directory-editions.md)

- Concluído os [pré-requisitos para acessar o Azure AD API de relatório](active-directory-reporting-api-prerequisites.md). 


##<a name="accessing-the-api"></a>Acessando a API

Ou você pode acessar essa API através do [Explorador de gráfico](https://graphexplorer2.cloudapp.net) ou programaticamente usando, por exemplo, PowerShell. Em ordem para o PowerShell para interpretar corretamente a sintaxe de filtro de OData usada em chamadas AAD Graph REST, você deve usar o backtick (também conhecidos como: acento grave) caractere "escape" o caractere $. O caractere de backtick serve como [caractere de escape do PowerShell](https://technet.microsoft.com/library/hh847755.aspx), permitindo que o PowerShell para fazer uma interpretação literal do caractere $ e evitar confuso-lo como um nome de variável do PowerShell (ie: $filter).

O foco deste tópico é o Explorador de gráfico. Para obter um exemplo do PowerShell, consulte este [script do PowerShell](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script).


## <a name="api-endpoint"></a>Ponto de extremidade de API

Você pode acessar essa API usando o seguinte URI base:  
    
    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



Devido ao volume de dados, essa API tem um limite de um milhão de registros retornados. 

Esta chamada retorna os dados em lotes. Cada lote tem um máximo de 1000 registros.  
Para obter o próximo lote de registros, use o seguinte link. Obter as informações de [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) do primeiro conjunto de registros retornados. O token de ignorar será no final do resultado definido.  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>Filtros com suporte

Você pode restringir o número de registros que são retornados por uma API chamar no formulário de um filtro.  
Para entrar API relacionadas a dados, os seguintes filtros são suportados:

- **$top =\<número de registros a serem retornados\> ** - para limitar o número de registros retornados. Esta é uma operação cara. Você não deve usar esse filtro se quiser retornar milhares de objetos.  
- **$filter =\<seu demonstrativo de filtro\> ** - especificar, com base em campos de filtro com suporte, o tipo de registro que me preocupar com



## <a name="supported-filter-fields-and-operators"></a>Operadores e campos de filtro com suporte

Para especificar o tipo de registro que me preocupar com, você pode criar uma instrução de filtro que pode conter um ou uma combinação dos campos de filtro a seguir:

- [signinDateTime](#signindatetime) - define uma data ou um intervalo

- [ID do usuário](#userid) - define um determinado usuário com base em ID. do usuário

- [userPrincipalName](#userprincipalname) - define um determinado usuário com base no nome do usuário usuário principal (UPN)

- [appId](#appid) - define um determinado aplicativo com base em ID do aplicativo

- [appDisplayName](#appdisplayname) - define um determinado aplicativo com base em nome de exibição do aplicativo

- [loginStatus](#loginStatus) - define o status do logon (sucesso / falha)


> [AZURE.NOTE] Ao usar o Explorador de gráfico, você precisar usar o caso correto para cada letra é os campos de filtro.


Para restringir o escopo dos dados retornados, você pode criar combinações dos campos de filtro e filtros com suporte. Por exemplo, a instrução a seguir retorna os registros de 10 principais entre de 2016 1º de julho e de 2016 de 6 de julho:

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


----------

### <a name="signindatetime"></a>signinDateTime

**Operadores de suportados**: eq, ge, le, gt, lt

**Exemplo**:

Usando uma data específica

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z  



Usando um intervalo de datas    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**Anotações**:

O parâmetro datetime deve estar no formato UTC 


----------

### <a name="userid"></a>ID de usuário

**Operadores de suportados**: eq

**Exemplo**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**Anotações**:

O valor da ID de usuário é um valor de cadeia de caracteres



----------

### <a name="userprincipalname"></a>userPrincipalName

**Operadores de suportados**: eq

**Exemplo**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**Anotações**:

O valor de userPrincipalName é um valor de cadeia de caracteres

----------

### <a name="appid"></a>appId

**Operadores de compatíveis**: eq

**Exemplo**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**Anotações**:

O valor de appId é um valor de cadeia de caracteres

----------


### <a name="appdisplayname"></a>appDisplayName

**Operadores de suportados**: eq

**Exemplo**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**Anotações**:

O valor de appDisplayName é um valor de cadeia de caracteres

----------

### <a name="loginstatus"></a>loginStatus

**Operadores de compatíveis**: eq

**Exemplo**:

    $filter=loginStatus+eq+'1'  


**Anotações**:

Há duas opções para o loginStatus: 0 - sucesso, 1 - Falha

----------



## <a name="next-steps"></a>Próximas etapas

- Você deseja ver exemplos de atividades filtradas entrar? Confira as [amostras de relatório API do Active Directory do Azure entrar atividade](active-directory-reporting-api-sign-in-activity-samples.md).

- Deseja saber mais sobre o Azure AD API de relatório? Consulte [Introdução ao Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md).