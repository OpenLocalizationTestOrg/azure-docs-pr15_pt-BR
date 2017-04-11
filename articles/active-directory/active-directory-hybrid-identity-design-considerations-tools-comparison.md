<properties
    pageTitle="Identidade híbrido: Comparação de ferramentas de integração de diretório | Microsoft Azure"
    description="Esta página fornece uma tabela abrangente que compara as diversas ferramentas de integração de diretório que podem ser usadas para integração de diretório."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Comparação de ferramentas de integração de diretório do híbrido identidade

Passar dos anos, as ferramentas de integração de diretório tem crescido e desenvolvido.  Este documento é ajudar a fornecer uma visão consolidada dessas ferramentas e uma comparação dos recursos que estão disponíveis em cada uma.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

>[AZURE.NOTE] Azure AD Connect incorpora os componentes e funcionalidade disponibilizadas anteriormente como Dirsync e AAD Sync. Essas ferramentas já não estejam sendo lançadas individualmente e todas as futuras melhorias serão incluídas em atualizações Azure AD Connect, para que você sempre saiba onde obter a funcionalidade mais atual.
>
>DirSync e sincronização do Azure AD são preteridos. Mais informações podem ser encontradas na [aqui](active-directory-aadconnect-dirsync-deprecated.md).


Use a seguinte chave para cada uma das tabelas.

● = Disponível agora  
FR = versão futura  
PP = demonstração pública  

## <a name="on-premises-to-cloud-synchronization"></a>Local para sincronização de nuvem

| Recurso  | Conectar-se do Active Directory do Azure  | Serviços de sincronização do Active Directory do Azure AAD Sync) | Ferramenta de sincronização do Active Directory do Azure (DirSync)| Gerenciador de identidades Forefront 2010 R2 (FIM) |Gerenciador de identidades do Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Conectar-se a única floresta do AD local | ● | ● | ● | ● |● |
| Conectar a várias florestas do AD local |●  | ● |  | ● |● |
| Conectar a várias organizações de Exchange local | ● |  |  |  | |
| Conectar ao diretório LDAP de local único | FR |  |  | ● |● |
| Conectar-se a vários diretórios LDAP de local |FR  |  |  | ● |● |
| Conectar ao AD locais e diretórios LDAP local |FR  |  |  | ● |● |
| Conectar-se a sistemas personalizados (ou seja, SQL, Oracle, MySQL, etc.) | FR |  |  | ● |● |
| Sincronizar atributos de definido pelo cliente (extensões de diretório) | ● |  |  |  |  |
|Conectar ao local HR (ou seja, SAP, Oracle eBusiness, PeopleSoft)| FR |  |  | ● |● |
|Compatível com regras de sincronização de FIM e conectores para provisionamento para sistemas de local.|  |  |  | ● |● |

## <a name="cloud-to-on-premises-synchronization"></a>Nuvem à sincronização de local

| Recurso  | Conectar-se do Active Directory do Azure  | Serviços de sincronização do Active Directory do Azure | Ferramenta de sincronização do Active Directory do Azure (DirSync) | Gerenciador de identidades Forefront 2010 R2 (FIM) |Gerenciador de identidades do Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Write-back de dispositivos | ● |  | ● |  ||
| Atributo write-back (para implantação híbrida do Exchange) | ● | ● | ● | ● |● |
| Write-back de objetos de usuários e grupos |  ●|  | |  ||
| Write-back de senhas (de redefinição de senha de autoatendimento (SSPR) e alteração de senha) |  ● | ● |  |  ||



## <a name="authentication-feature-support"></a>Suporte de recursos de autenticação

| Recurso  | Conectar-se do Active Directory do Azure | Serviços de sincronização do Active Directory do Azure | Ferramenta de sincronização do Active Directory do Azure (DirSync) | Gerenciador de identidades Forefront 2010 R2 (FIM) |Gerenciador de identidades do Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Sincronização de senha de um único floresta do AD local | ● | ● | ● |  ||
| Sincronização de senha para vários florestas do AD local |  ●| ● |  |  ||
| Logon único com a federação | ● | ● | ● | ● |● |
| Write-back de senhas (da alteração SSPR e senha) |●  | ● |  |  ||



## <a name="set-up-and-installation"></a>Instalação e configuração

| Recurso  | Conectar-se do Active Directory do Azure  | Serviços de sincronização do Active Directory do Azure | Ferramenta de sincronização do Active Directory do Azure (DirSync) | Gerenciador de identidades do Microsoft 2016 (MIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Suporta a instalação em um controlador de domínio | ● | ● | ● |  |
| Oferece suporte para instalação usando o SQL Express | ● | ● | ● |  |
| Atualização fácil de DirSync |● |  |  |  |
| Localização da administração UX idiomas do Windows Server | ● | ● | ● |  |
|Localização do usuário final UX idiomas do Windows Server| |  |  |● |
| Suporte para o Windows Server 2008 e Windows Server 2008 R2 | ● sincronização, não para a federação| ● | ●  | ● |
| Suporte para o Windows Server 2012 e Windows Server 2012 R2 | ● | ● | ● | ● |

## <a name="filtering-and-configuration"></a>Filtragem e configuração

Recurso  | Conectar-se do Active Directory do Azure | Serviços de sincronização do Active Directory do Azure | Ferramenta de sincronização do Active Directory do Azure (DirSync) | Gerenciador de identidades Forefront 2010 R2 (FIM)| Gerenciador de identidades do Microsoft 2016 (MIM)
:-------- |:--------:|:--------:|:--------:|:--------:|:--------:|
Filtrar em domínios e unidades organizacionais | ● | ● | ● | ●  | ●
Filtrar valores de atributo de objetos | ● | ● | ● | ●| ●
Permitir que o conjunto mínimo de atributos a ser sincronizado (MinSync) | ● | ● |  ||
Permitir que os modelos de serviço diferente para serem aplicadas fluxos de atributo |●  | ● |  ||
Permitir removendo atributos do fluindo de AD para Azure AD | ● | ● |  |  |
Permitir personalização avançada para fluxos de atributo | ● | ● |  | ●  | ●

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
