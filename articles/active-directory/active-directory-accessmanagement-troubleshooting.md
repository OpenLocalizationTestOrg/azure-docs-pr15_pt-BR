
<properties
    pageTitle="Solução de problemas de associação dinâmica para grupos | Microsoft Azure"
    description="Dicas de solução de associação dinâmica para grupos no Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>


# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Associações dinâmicas para grupos de solução de problemas

**Configurei uma regra em um grupo, mas nenhuma associação atualizadas no grupo**<br/>Verifique se a configuração **Habilitar delegado gerenciamento de grupo** está definida como **Sim** na guia **Configurar** . Você verá esta configuração somente se você estiver conectado como um usuário a quem uma licença do Azure Active Directory Premium é atribuída. Verifique os valores dos atributos de usuário na regra: há usuários que atendam a regra?

**Posso configurado uma regra, mas agora os membros existentes da regra são removidos**<br/>Este é o comportamento esperado. Membros existentes do grupo são removidos quando uma regra é habilitada ou alterada. Os usuários retornados da avaliação da regra são adicionados como membros ao grupo.     

**Eu não vejo a associação altera instantaneamente quando adicionar ou alterar uma regra, por que não?**<br/>Avaliação de associação dedicado é feita periodicamente em um processo de plano de fundo assíncrona. Quanto tempo o processo levou é determinada pelo número de usuários no diretório e o tamanho do grupo criado como resultado da regra. Normalmente, diretórios com números pequenos de usuários verá as alterações de associação de grupo em menos de poucos minutos. Diretórios com um grande número de usuários podem levar 30 minutos ou mais para preencher.

Estes artigos fornecem informações adicionais sobre Active Directory do Azure.

* [Gerenciando o acesso aos recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)
* [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [O que é Azure Active Directory?](active-directory-whatis.md)
* [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
