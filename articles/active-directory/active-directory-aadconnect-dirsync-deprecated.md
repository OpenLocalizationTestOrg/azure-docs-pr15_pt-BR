<properties
    pageTitle="Atualização de DirSync e sincronização do Azure AD | Microsoft Azure"
    description="Descreve como atualizar do DirSync e sincronização do Azure AD para o Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>


# <a name="upgrade-windows-azure-active-directory-sync-dirsync-and-azure-active-directory-sync-azure-ad-sync"></a>Atualizar o Windows Azure Active Directory Sync ("DirSync") e sincronização do Active Directory do Azure ("Azure AD sincronizar")
Azure AD Connect é a melhor maneira de conectar seu diretório local com o Azure AD e o Office 365. Este é um ótimo momento para atualizar para o Azure AD Connect do Windows Azure Active Directory Sync (DirSync) ou Azure AD Sync como essas ferramentas agora são preteridas e atingirá fim do suporte em 13 de abril de 2017.

As ferramentas de sincronização de duas identidade que são preteridas foram oferecidas para clientes única floresta (DirSync) e para vários floresta e outros avançadas clientes (Azure AD Sync). Essas ferramentas antigas foram substituídas por uma única solução que está disponível para todos os cenários: Azure AD Connect. Ele oferece suporte para novos cenários, aprimoramentos de recursos e novas funcionalidades. Para poder continuar sincronizar seus dados de identidade de local com Azure AD e o Office 365, recomendamos que você atualize para Azure AD Connect.

A última versão do DirSync foi lançada em julho de 2014 e a última versão do Azure AD Sync foi lançada em maio de 2015.

## <a name="what-is-azure-ad-connect"></a>O que é Azure AD Connect
Azure AD Connect é a sucessora DirSync e sincronização do Azure AD. Ele combina todos os cenários esses dois com suporte. Você pode ler mais sobre isso na [integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).

## <a name="deprecation-schedule"></a>Cronograma de substituição

Data | Comentário
 --- | ---
13 de abril de 2016 | Windows Azure Active Directory Sync ("DirSync") e o Microsoft Azure Active Directory Sync ("Azure AD sincronizar") são anunciado como desaprovado.
13 de abril de 2017 | Suporte termina. Clientes já não será possível abrir um caso de suporte sem atualizar para Azure AD Connect primeiro.

## <a name="how-to-transition-to-azure-ad-connect"></a>Como fazer a transição para Azure AD Connect
Se você estiver executando o DirSync, há duas maneiras que você pode atualizar: In-loco implantação de atualização e paralela. Uma atualização in-loco é recomendada para a maioria dos clientes e se você tiver um recente sistema operacional e menor que 50.000 objetos. Em outros casos é recomendável fazer uma implantação paralela onde sua configuração de DirSync é movida para um novo servidor executando o Azure AD Connect.

Se você usar uma atualização in-loco é recomendada de sincronização do Azure AD. Se desejar, é possível instalar um novo servidor do Azure AD Connect em paralelo e fazer uma migração de giro do seu servidor de sincronização do Azure AD para o Azure AD Connect.

Solução | Cenário
----- | -----
[Atualização de DirSync](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>Se você tiver um servidor DirSync existente esteja em execução.</li>
[Atualização de sincronização do Azure AD](active-directory-aadconnect-upgrade-previous-version.md)| <li>Se você estiver movendo de sincronização do Azure AD.</li>

Se você quiser ver como realizar uma atualização in-loco de DirSync para Azure AD Connect, consulte este vídeo de 9 de canal:

> [AZURE.VIDEO azure-active-directory-connect-in-place-upgrade-from-legacy-tools]

## <a name="faq"></a>Perguntas Freqüentes
**P: posso recebeu uma notificação por email de equipe do Azure e/ou em uma mensagem do Centro de mensagens do Office 365, mas estou usando conectar.**  
A notificação também foi enviada para os clientes usando o Azure AD Connect com um número de compilação 1.0. \*.0 (usando uma versão 1.1 do pré). A Microsoft recomenda aos clientes a se manter atualizado com as versões do Azure AD Connect. Com o recurso de [atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) tornará de 1.1 realmente fácil sempre tenha uma versão recente do Azure AD Connect instalado.

**P: será DirSync/Azure AD Sync parar trabalhando em 13 de abril de 2017?**  
Não. A data de quando esses será mais possível se comunicar com o Azure AD será anunciada em uma data posterior. Você poderá encontrar essas informações neste tópico quando disponível.

**P: quais versões de DirSync pode atualizar do?**  
Há suporte para atualizar a partir de qualquer versão de DirSync sendo usada no momento.

**P: como o Azure AD do conector para FIM/MIM?**  
O conector do Azure AD para o FIM/MIM **não** foi anunciado como desaprovado. Ele está no **recurso congelar**; Nenhuma nova funcionalidade é adicionada e ele recebe sem correções de bugs. A Microsoft recomenda clientes usá-lo para planejar mover dela para Azure AD Connect. É altamente recomendável para não iniciar quaisquer novas implantações usá-lo. Esse conector será anunciado obsoleta no futuro.

## <a name="additional-resources"></a>Recursos adicionais

* [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
