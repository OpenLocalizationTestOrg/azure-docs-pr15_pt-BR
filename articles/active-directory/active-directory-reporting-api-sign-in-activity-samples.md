<properties
    pageTitle="Exemplos de relatório API do Azure Active Directory entrar atividade | Microsoft Azure"
    description="Como começar com o Azure Active Directory Reporting API"
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

# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Exemplos de relatório API do Azure Active Directory entrar atividade

Este tópico é parte de um conjunto de tópicos sobre o Active Directory do Azure API de relatório.  
Relatórios do Azure AD oferece uma API que permite que você acesse dados de atividade de entrada usando o código ou ferramentas relacionadas.  
O escopo deste tópico é forneça um código de exemplo para a **atividade de entrada API**.

Consulte:

- [Logs de auditoria](active-directory-reporting-azure-portal.md#audit-logs) para obter mais informações conceituais
- [Introdução ao Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md) para obter mais informações sobre a API de relatórios.

Para dúvidas, problemas ou comentários, entre em contato com o [AAD relatórios ajudam](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Pré-requisitos
Antes de poder usar os exemplos deste tópico, você precisa concluir os [pré-requisitos para acessar o Azure AD API de relatório](active-directory-reporting-api-prerequisites.md).  


##<a name="powershell-script"></a>Script do PowerShell

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.windows.net/"
    $tenantdomain   = "<tenantDomain>"
    $ daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"
    
    $i=0
    
    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {
    
        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>Executando o script
Depois que você terminar de editar o script, executá-lo e verificar se os dados esperados de auditoria efetua relatório será retornado.

O script retorna saída do relatório entrar no formato JSON. Ele também cria um `SigninActivities.json` arquivo com a mesma saída. Você pode experimentar modificando o script para retornar dados de outros relatórios e comente os formatos de saída que não é necessário.



## <a name="next-steps"></a>Próximas etapas

- Você gostaria de personalizar os exemplos deste tópico? Confira a [atividade de entrada do Active Directory do Azure Referência API](active-directory-reporting-api-sign-in-activity-reference.md). 

- Se você quiser ver uma visão geral completa de como usar o Active Directory do Azure API de relatório, consulte [Introdução ao Azure Active Directory do API de relatório](active-directory-reporting-api-getting-started.md).

- Se você gostaria de saber mais sobre relatórios do Azure Active Directory, consulte o [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).  