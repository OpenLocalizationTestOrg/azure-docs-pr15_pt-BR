<properties
    pageTitle="Azure AD Connect na Alemanha de nuvem da Microsoft"
    description="Azure AD Connect serão integradas seus diretórios locais com o Active Directory do Azure. Isso permite que você forneça uma identidade comum para aplicativos do Office 365, do Azure e SaaS integrados ao Azure AD."
    keywords="Introdução ao Azure AD Connect, visão geral do Azure AD Connect, o que é Azure AD Connect, instale o active directory, Alemanha, preto floresta"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/08/2016"
    ms.author="billmath"/>

#<a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect no Microsoft Cloud Alemanha – demonstração pública

## <a name="introduction"></a>Introdução
Azure AD Connect fornece sincronização entre o Active Directory local e o Active Directory do Azure.
Atualmente, muitos dos cenários na [Alemanha de nuvem da Microsoft](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) devem ser feitos pelo operador. Ao usar o Microsoft Cloud Alemanha, você deve estar ciente do seguinte:


- As seguintes URLs devem ser abertos em um servidor proxy para sincronização com êxito:
    - *. microsoftonline.de
    - *. no windows.net
    - + Listas de certificados revogados

- Quando você entrar no seu diretório Azure AD, você deve usar uma conta no domínio de onmicrosoft.de.
- Os seguintes recursos não estão disponíveis:
    - Azure AD Connect integridade
    - Atualizações automáticas
    - Write-back de senha

## <a name="download"></a>Baixar
Você pode baixar o Azure AD Connect da lâmina do Azure AD Connect dentro do portal.  Use as instruções abaixo para localizar a lâmina Azure AD Connect.

### <a name="the-azure-ad-connect-blade"></a>O Azure AD Connect Blade

Depois que você tiver conectado ao portal do Azure, faça o seguinte:

1. Vá para procurar
2.  Selecione o Active Directory do Azure
3.  Selecione Azure AD Connect

Você verá o seguinte:

![Azure AD Connect Blade](media\active-directory-aadconnect-germany\germany1.png)

 
A tabela a seguir descreve os recursos mostrados na lâmina.


Título|Descrição|
----- | ----- |
STATUS DE SINCRONIZAÇÃO|Vamos sabe se a sincronização está habilitada ou desabilitada.|
ÚLTIMA SINCRONIZAÇÃO|A última vez em uma sincronização bem-sucedida concluída.|
DOMÍNIOS FEDERADOS|Mostra o número de domínios federados atualmente configurado.|


## <a name="installation"></a>Instalação
Para instalar o Azure AD Connect, você pode usar a documentação [aqui](active-directory-aadconnect.md#install-azure-ad-connect).

## <a name="advanced-features-and-additional-information"></a>Recursos avançados e informações adicionais
Para obter informações adicionais e orientação sobre configurações personalizadas ou configurações avançadas, comece com [integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).  Esta página fornece informações e links para orientações adicionais.
