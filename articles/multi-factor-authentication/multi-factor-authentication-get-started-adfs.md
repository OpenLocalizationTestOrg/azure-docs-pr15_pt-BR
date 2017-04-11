<properties
    pageTitle="Azure FS MFA e AD | Microsoft Azure"
    description="Esta é a página de autenticação multifator do Azure que descreve como começar com o Azure MFA e AD FS."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na" ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services"></a>Introdução aos serviços de Federação do Active Directory e de autenticação multifator do Azure



<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Se sua organização tem federados seu local do Active Directory com o Azure Active Directory usam AD FS, há duas opções para usando autenticação multifator do Azure.

- Proteger os recursos de nuvem usando a autenticação multifator Azure ou serviços de Federação do Active Directory
- Proteger os recursos de nuvem e local usando o servidor de autenticação multifator do Azure

A tabela a seguir resume a experiência de verificação entre protegendo recursos com autenticação multifator do Azure e AD FS

|Experiência de verificação - aplicativos baseados em Procurar|Experiência de verificação - aplicativos não baseado em navegador
:------------- | :------------- | :------------- |
Proteção de recursos do Azure AD usando a autenticação multifator do Azure |<li>A primeira etapa de verificação é executada usam AD FS no local.</li> <li>A segunda etapa é um método baseado em telefone realizado usando autenticação de nuvem.</li>|Usuários finais pode usar senhas de aplicativos para entrar.
Proteção de recursos do Azure AD usando os serviços de Federação do Active Directory |<li>A primeira etapa de verificação é executada usam AD FS no local.</li><li>A segunda etapa é local realizado por aceitar a declaração.</li>|Usuários finais pode usar senhas de aplicativos para entrar.

Limitações com senhas de aplicativo para usuários federados:

- Senhas de aplicativos são verificadas usando a autenticação de nuvem, para que eles ignoram federação. Federação é usada somente ativamente ao configurar uma senha de aplicativo.
- Configurações de controle de acesso do cliente local não são observadas por senhas de aplicativo.
- Você perde local recurso de log de autenticação de senhas de aplicativos.
- Exclusão da desativar conta pode levar até três horas para sincronização de diretório, atrasar desabilitar/exclusão de senhas de aplicativos na identidade nuvem.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como configurar a autenticação multifator do Azure ou o servidor de autenticação multifator Azure com o AD FS, consulte os seguintes artigos:

- [Proteger os recursos de nuvem usando autenticação multifator do Azure e AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
- [Proteger os recursos de nuvem e local usando o servidor de autenticação multifator Azure com o Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Proteger os recursos de nuvem e local usando o servidor de autenticação multifator Azure com o AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)
