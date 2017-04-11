<properties
    pageTitle="Referência da API auditoria de Azure Active Directory | Microsoft Azure"
    description="Como começar com a API de auditoria do Active Directory do Azure"
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
    ms.date="10/24/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-audit-api-reference"></a>Referência da API auditoria de Azure Active Directory

Este tópico é parte de um conjunto de tópicos sobre o Active Directory do Azure API de relatório.  
Relatórios do Azure AD oferece uma API que permite que você acesse dados de auditoria usando o código ou ferramentas relacionadas.
O escopo deste tópico é fornecer informações de referência sobre a **API de auditoria**.

Consulte:

- [Logs de auditoria](active-directory-reporting-azure-portal.md#audit-logs) para obter mais informações conceituais
- [Introdução ao Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md) para obter mais informações sobre a API de relatórios.

Para dúvidas, problemas ou comentários, entre em contato com o [AAD relatórios ajudam](mailto:aadreportinghelp@microsoft.com).


## <a name="who-can-access-the-data"></a>Quem pode acessar os dados?

- Usuários na função de administrador de segurança ou leitor de segurança

- Administradores globais

- Qualquer aplicativo que tenha autorização para acessar a API (o autorização de aplicativo pode ser configurado somente com base na permissão de Administrador Global)



## <a name="prerequisites"></a>Pré-requisitos

Para acessar este relatório por meio da API de relatórios, você deve ter:

- Um [gratuito do Active Directory do Azure ou melhor edition](active-directory-editions.md)

- Concluído os [pré-requisitos para acessar o Azure AD API de relatório](active-directory-reporting-api-prerequisites.md). 
 

##<a name="accessing-the-api"></a>Acessando a API

Ou você pode acessar essa API através do [Explorador de gráfico](https://graphexplorer2.cloudapp.net) ou programaticamente usando, por exemplo, PowerShell. Em ordem para o PowerShell para interpretar corretamente a sintaxe de filtro de OData usada em chamadas AAD Graph REST, você deve usar o backtick (também conhecidos como: acento grave) caractere "escape" o caractere $. O caractere de backtick serve como [caractere de escape do PowerShell](https://technet.microsoft.com/library/hh847755.aspx), permitindo que o PowerShell para fazer uma interpretação literal do caractere $ e evitar confusas-lo como um nome de variável do PowerShell (ie: $filter).

O foco deste tópico é o Explorador de gráfico. Para obter um exemplo do PowerShell, consulte este [script do PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

 
 

## <a name="api-endpoint"></a>Ponto de extremidade de API


Você pode acessar essa API usando o seguinte URI:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Não há nenhum limite no número de registros retornados pela API de auditoria do Azure AD (usando OData paginação).
Para limites retenção de dados de relatórios, confira [As políticas de retenção de relatório](active-directory-reporting-retention.md).

Esta chamada retorna os dados em lotes. Cada lote tem um máximo de 1000 registros.  
Para obter o próximo lote de registros, use o seguinte link. Obter as informações de skiptoken do primeiro conjunto de registros retornados. O token de ignorar será no final do resultado definido.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Filtros com suporte

Você pode restringir o número de registros que são retornados por uma API chamar no formulário de um filtro.  
Para entrar API relacionadas a dados, os seguintes filtros são suportados:

- **$top =\<número de registros a serem retornados\> ** - para limitar o número de registros retornados. Esta é uma operação cara. Você não deve usar esse filtro se quiser retornar milhares de objetos.     
- **$filter =\<seu demonstrativo de filtro\> ** - especificar, com base em campos de filtro com suporte, o tipo de registro que me preocupar com



## <a name="supported-filter-fields-and-operators"></a>Operadores e campos de filtro com suporte

Para especificar o tipo de registro que me preocupar com, você pode criar uma instrução de filtro que pode conter um ou uma combinação dos campos de filtro a seguir:

- [activityDate](#activitydate) - define uma data ou um intervalo
- [activityType](#activitytype) - define o tipo de uma atividade
- [atividade](#activity) - define a atividade como cadeia de caracteres  
- [nome do ator](#actorname) - define o ator no formulário de nome do ator
- [ator/ID do objeto](#actorobjectid) - define o ator no formulário de ID do ator   
- [ator/upn](#actorupn) - define o ator no formulário princípio do ator nome de usuário (UPN) 
- [nome do destino](#targetname) - define o destino no formulário de nome do ator
- [ID do objeto/destino](#targetobjectid) - define o destino no formulário de ID do destino  
- [destino/upn](#targetupn) - define o ator no formulário princípio do ator nome de usuário (UPN)   




----------

### <a name="activitydate"></a>activityDate

**Operadores de suportados**: eq, ge, le, gt, lt

**Exemplo**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=eventTime gt ' + $7daysago    

**Anotações**:

DateTime deve estar no formato UTC

----------

### <a name="activitytype"></a>activityType

**Operadores de compatíveis**: eq

**Exemplo**:

    $filter=activityType eq 'User'  

**Anotações**:

diferencia maiusculas de minúsculas

----------

### <a name="activity"></a>atividade

**Operadores de compatíveis**: eq, contém, startsWith

**Exemplo**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')   

**Anotações**:

diferencia maiusculas de minúsculas

----------

### <a name="actorname"></a>nome do ator

**Operadores de compatíveis**: eq, contém, startsWith

**Exemplo**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')  

**Anotações**:

maiusculas e minúsculas

    

----------
### <a name="actorobjectid"></a>ator/ID do objeto

**Operadores de compatíveis**: eq

**Exemplo**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    

----------
### <a name="targetname"></a>nome do destino

**Operadores de compatíveis**: eq, contém, startsWith

**Exemplo**:

    $filter=targets/any(t: t/name eq 'some name')   

**Anotações**:

Maiusculas e minúsculas

----------

### <a name="targetupn"></a>destino/upn

**Operadores de suportados**: eq, startsWith

**Exemplo**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc')) 

**Anotações**:

- Maiusculas e minúsculas
- Você precisa adicionar o namespace completo ao consultar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity

----------

### <a name="targetobjectid"></a>destino/ID do objeto

**Operadores de compatíveis**: eq

**Exemplo**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

----------

### <a name="actorupn"></a>ator/upn

**Operadores de compatíveis**: eq, startsWith

**Exemplo**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')  

**Anotações**:

- Maiusculas e minúsculas 
- Você precisa adicionar o namespace completo ao consultar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

----------




## <a name="next-steps"></a>Próximas etapas

- Você deseja ver exemplos de atividades de sistema filtrados? Confira os [exemplos de API de auditoria do Active Directory do Azure](active-directory-reporting-api-audit-samples.md).

- Deseja saber mais sobre o Azure AD API de relatório? Consulte [Introdução ao Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md).