<properties
    pageTitle="Relatório de uso não licenciado | Microsoft Azure"
    description="Uso não licenciado relatório ajuda a que identificar usuários não licenciados que estão usando pago recursos do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="unlicensed-usage-report"></a>Relatório de uso não licenciado

Uso não licenciado relatório ajuda a que identificar usuários não licenciados que estão usando pago recursos do Azure AD. Isso permite que você para fazer o melhor usar de licenças adquiridas e para identificar souber quando você pode precisar licenças adicionais. 

O relatório mostra active uso dos recursos pagos nos últimos 30 dias. 

## <a name="report-structure"></a>Estrutura de relatório
 
| Nome da coluna          |    Descrição |
| :--                  | :--         |
| Usuário não licenciado      |    Nome do usuário |
| Recurso              | O nome do recurso. Por exemplo: acesso condicional |
| Aplicativo acessado | O nome do aplicativo que está sendo acessado com o recurso. Por exemplo: Office 365 SharePoint Online |

 
> [AZURE.NOTE] Se uma conta de usuário tiver sido excluída na coluna 'Usuário não licenciados' será preenchida com uma identificação, como 1003000090D8B285


## <a name="conditional-access-feature"></a>Recurso de acesso condicional

Usuários não licenciados serão sinalizados ao acessar um serviço que tenha política de acesso condicional aplicada se eles não tiverem uma licença do Azure AD Premium. 

Isso se aplica a MFA / políticas de local, bem como dispositivo políticas que usar o Intune.
 

## <a name="see-also"></a>Consulte também

- [Usar o Access condicional com o Office 365 e outros Azure Active Directory conectado aplicativos](active-directory-conditional-access.md)
- [Introdução ao acesso condicional ao Azure AD](active-directory-conditional-access-azuread-connected-apps.md) 


