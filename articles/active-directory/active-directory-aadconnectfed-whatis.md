<properties
    pageTitle="Azure AD Connect e federação | Microsoft Azure"
    description="Esta página é o local central para toda a documentação sobre operações de AD FS usando Azure AD Connect"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="anandy"/>


# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect e federação

Azure AD Connect permite que você configurar a federação com o local AD FS e Azure AD. Com a federação logon, você pode habilitar usuários para entrar serviços do Azure AD com base com suas senhas de local e, enquanto estiver na rede corporativa, sem precisar digitar suas senhas novamente. A opção de federação com o AD FS permite que você implantar uma nova ou especifique um existente do AD FS no Windows Server 2012 R2 farm.

Este tópico é a página inicial para obter informações sobre a federação relacionados funcionalidades de Azure AD Connect e listas de links para todos os outros tópicos relacionados a ele. Para obter links para Azure AD Connect, consulte integração suas identidades do local com o Active Directory do Azure.

## <a name="azure-ad-connect---federation-topics"></a>Azure AD Connect - tópicos de Federação

| Tópico | O que ele cubra e quando a leitura |
|:------|:-----------|
| **Azure AD Connect entrar opções do usuário** ||
| [Noções básicas sobre opções de entrada do usuário](active-directory-aadconnect-user-signin.md) | Descrição das diversas entrar opções do usuário e como elas afetam experiência do usuário de entrada Azure |
| **Instalação do AD FS usando Azure AD Connect**||
| [Pré-requisitos](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) | Pré-requisitos para uma instalação bem-sucedida do AD FS via Azure AD Connect|
| [Configurar o AD FS farm](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) | Como instalar um novo farm do AD FS usando Azure AD Connect |
| **Modificar a configuração do AD FS** | |
| [Reparando a relação de confiança](active-directory-aadconnect-federation-management.md#reparing-the-trust) | Como reparar atual confiança entre locais do AD FS e no Office 365 / Azure |
| [Adicionando um novo servidor do AD FS](active-directory-aadconnect-federation-management.md#adding-a-new-ad-fs-server) | Expandindo farm do AD FS com adicionais do AD FS servidor postagem inicial de instalação |
| [Adicionando um novo servidor do AD FS WAP](active-directory-aadconnect-federation-management.md#adding-a-new-wap-server) | Expandindo farm do AD FS com adicionais WAP servidor postagem inicial de instalação |
| [Adicionar um novo domínio federado](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain) | Adicionar outro domínio seja federado com o Azure AD |
|**Tarefas de instalação de postagem**||
| [Adicionar o logotipo da empresa personalizado / ilustração](active-directory-aadconnect-federation-management.md#add-custom-company-logo-or-illustration)| Modificar a experiência de entrada, especificando o logotipo personalizado que será mostrado na página de entrada do AD FS |
| [Adicionar descrição entrar](active-directory-aadconnect-federation-management.md#add-sign-in-description) | Alterando a descrição de entrar na página de entrada do AD FS | 
| [Modificando o AD FS reivindicar regras](active-directory-aadconnect-federation-management.md#modifying-ad-fs-claim-rules) | Modificar / adicionar regras de declaração do AD FS correspondente a configuração de sincronização do Azure AD Connect |


## <a name="additional-resources"></a>Recursos adicionais

* [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
* [Implantação do AD FS no Azure](active-directory-aadconnect-azure-adfs.md)
* [Implantação do alta disponibilidade entre fronteiras geográficas AD FS no Azure com o Gerenciador de tráfego do Azure](active-directory-adfs-in-azure-with-azure-traffic-manager.md)


