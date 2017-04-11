<properties
    pageTitle="Active Directory do Azure: Criar tópico de suporte de locatário | Microsoft Azure"
    description="Criando um locatário do Azure Active Directory ou um locatário do Azure Active Directory B2C: problemas e resoluções"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="creating-an-azure-active-directory-azure-ad-tenant-or-azure-ad-b2c-tenant-issues-and-resolutions"></a>Criando um locatário do Azure Active Directory (AD Azure) ou o Azure AD B2C locatário: problemas e resoluções

## <a name="creating-an-azure-ad-tenant"></a>Criando um locatário do Azure AD

Se você não pode criar um locatário do Azure AD na primeira vez, tente novamente. Se o problema persistir, contate o suporte.

## <a name="creating-an-azure-ad-b2c-tenant"></a>Criando um locatário do Azure AD B2C

Se você encontrar problemas durante a [criação de um locatário do Azure AD B2C](active-directory-b2c-get-started.md), tente o seguinte:
 
- Se o locatário do Azure AD B2C não aparecer na sua lista de locatários, tente novamente.
- Se o locatário do Azure AD B2C aparecem em sua lista de locatários e você recebe uma mensagem de erro ("não foi possível concluir a criação do locatário B2C 'contosob2c'. Visite este [link](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) para obter mais orientações."), exclua o locatário que você acabou de criar e tente novamente.
- Observe que há conhecidos problemas quando você excluir um locatário existente do B2C e recriá-la com o mesmo nome de domínio. Você precisa criar um locatário B2C com um nome de domínio diferente.
- Se essas resoluções não funcionarem para você, contate o suporte. Saiba mais sobre [como solicitações de suporte de arquivo para Azure AD B2C](active-directory-b2c-support.md).
