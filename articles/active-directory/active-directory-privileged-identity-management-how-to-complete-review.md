<properties
   pageTitle="Como concluir uma revisão do access | Microsoft Azure"
   description="Depois que você iniciou uma revisão de acesso no gerenciamento de identidades do Azure AD privilegiados, Aprenda a concluí-la e exibir os resultados"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/30/2016"
   ms.author="kgremban"/>

# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Como concluir uma revisão de acesso no gerenciamento de identidades do Azure AD privilegiado


Os administradores de função privilegiado podem analisar acesso privilegiado uma vez uma [revisão de segurança foi iniciado](active-directory-privileged-identity-management-how-to-start-security-review.md). Gerenciamento de identidades do Azure AD privilegiado (PIM) enviará automaticamente um email solicitando que os usuários para examinar seu acesso. Se um usuário não obteve um email, você pode enviar as instruções em [como executar uma análise de segurança](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Depois que o período de revisão de segurança está acima ou todos os usuários terminar suas revisar auto, siga as etapas neste artigo para gerenciar a revisão e ver os resultados.

## <a name="manage-security-reviews"></a>Gerenciar revisões de segurança

1. Acesse o [portal do Azure](https://portal.azure.com/) e selecione o aplicativo de **gerenciamento de identidades do Azure AD privilegiado** no seu painel.
2. Selecione a seção **revisões de acesso** do painel de controle.
3. Selecione a revisão de acesso que você deseja gerenciar.

Na lâmina de detalhes de revisão access há um número opções de gerenciamento de revisão.

![Botões de revisão de acesso do Gerenciador - captura de tela][1]

### <a name="remind"></a>Lembrar

Se uma revisão de acesso estiver configurada para que os usuários examinam próprios, o botão de **Lembrar** envia uma notificação. 

### <a name="stop"></a>Parar

Todas as revisões de acesso tem uma data de término, mas você pode usar o botão **Parar** para concluir o processo antecipado. Se todos os usuários ainda não foram revisados desta vez, eles não conseguirá depois que você parar de revisão. Você não pode reiniciar uma revisão depois que ele seja interrompido.

### <a name="apply"></a>Aplicar

Após uma revisão de acesso, ou porque você atingiu a data de término ou interrompido-la manualmente, o botão **Aplicar** implementa o resultado da revisão. Se o acesso de um usuário foi negado na revisão, esta é a etapa que removerá sua atribuição de função.  

### <a name="export"></a>Exportar

Se você quiser aplicar os resultados da revisão de segurança manualmente, você pode exportar a revisão. O botão **Exportar** iniciará o download de um arquivo CSV. Você pode gerenciar os resultados no Excel ou outros programas que abrem arquivos CSV.

### <a name="delete"></a>Excluir

Se você não estiver interessado em mais de revisão, excluí-lo. Botão **Excluir** remove o aplicativo do Gerenciador de revisão.

> [AZURE.IMPORTANT] Você não recebe um aviso antes de exclusão ocorre, então certifique-se de que você deseja excluir revisão.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
