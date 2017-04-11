<properties
    pageTitle="Automatizado de provisionamento no Azure AD do usuário de aplicativo SaaS | Microsoft Azure"
    description="Uma introdução ao como você pode usar o Azure AD para provisionar automaticamente, eliminação provisionar e atualizar continuamente contas de usuário em vários aplicativos SaaS de terceiros."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatizar usuário provisionamento e desprovisionamento para aplicativos SaaS com o Active Directory do Azure

##<a name="what-is-automated-user-provisioning-for-saas-apps"></a>O que é provisionamento automatizado do usuário para os aplicativos de SaaS?

Azure Active Directory (AD Azure) permite automatizar a criação, manutenção e remoção de identidades do usuário em aplicativos de nuvem ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) como Dropbox, Salesforce, ServiceNow e muito mais.

**Abaixo estão alguns exemplos do que esse recurso permite que você faça:**

- Crie automaticamente novas contas nos aplicativos SaaS certos para novas pessoas ao ingressar em sua equipe.
- Desative contas de aplicativos SaaS automaticamente quando as pessoas inevitavelmente deixam a equipe.
- Certifique-se de que as identidades em seus aplicativos SaaS são mantidas atualizadas com base nas alterações no diretório.
- Provisionar objetos de não usuário, como grupos, para aplicativos SaaS que dão suporte a eles.

**Provisionamento automatizado usuário também inclui as seguintes funções:**

- A capacidade de corresponder identidades existentes entre Azure AD e aplicativos SaaS.
- Opções de personalização para a Ajuda Azure AD ajustar as configurações atuais dos aplicativos SaaS que sua organização está usando atualmente.
- Alertas de email opcional para erros de provisionamento.
- Logs de atividades e geração de relatórios para ajudá-lo com o monitoramento e solução de problemas.

##<a name="why-use-automated-provisioning"></a>Por que usar provisionamento automatizado?

Algumas motivações comuns para usar esse recurso incluem:

- Para evitar os custos, deficiências e erros humanos associada manuais processos de provisionamento.
- Proteger sua organização removendo instantaneamente identidades dos usuários da chaves aplicativos SaaS quando eles saem da organização.
- Para importar facilmente um número em massa de usuários para um determinado aplicativo SaaS.
- Para aproveitar a conveniência de sua solução de provisionamento executar pessoas de lá as mesmas políticas de acesso do aplicativo que você definiu para Azure AD Single Sign-On.

##<a name="frequently-asked-questions"></a>Perguntas frequentes

**Frequência Azure AD escrever as alterações de diretório para o aplicativo de SaaS?**

Azure AD verifica se há alterações de cada cinco a dez minutos. Se o aplicativo de SaaS está retornando vários erros (tais como no caso de credenciais de administrador inválido), Azure AD gradualmente reduzir sua frequência para até uma vez por dia até que os erros sejam corrigidos.

**Quanto tempo levará para provisionar meus usuários?**

Alterações incrementais acontecem quase instantaneamente, mas se você estiver tentando provisionar na maioria das seu diretório, então ela depende o número de usuários e grupos que você tem. Diretórios pequenos levar apenas alguns minutos, médias diretórios podem demorar vários minutos e diretórios muito grandes podem levar horas.

**Como posso acompanhar o progresso do trabalho provisionamento atual?**

Você pode examinar o relatório de provisionamento de conta sob a seção relatórios do seu diretório. Outra opção é visitar a guia de painel para o aplicativo de SaaS são provisionamento e procure a seção "Integração com o Status" na parte inferior da página.

**Como saber se os usuários não sejam provisionados corretamente?**

No final da configuração de provisionamento Assistente lá é uma opção para assinar notificações de email de falhas de provisionamento. Você também pode verificar o relatório de erros de provisionamento para ver quais usuários Falha ao ser provisionado e por quê.

**Pode Azure AD gravar alterações do aplicativo SaaS volta ao diretório?**

Para a maioria dos aplicativos de SaaS, provisionamento é apenas de saída, que significa que os usuários são gravados do diretório para o aplicativo e alterações do aplicativo não podem ser gravadas volta ao diretório. Para [DIATRABALHO](https://msdn.microsoft.com/library/azure/dn762434.aspx), no entanto, provisionamento é apenas de entrada, que significa que os usuários são importados para o diretório do dia de trabalho e da mesma forma, as alterações no diretório não obter gravadas novamente no dia de trabalho.

**Como posso enviar comentários à equipe de engenharia?**

Entre em contato conosco por meio do [Active Directory do Azure Fórum de comentários](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="how-does-automated-provisioning-work"></a>Como funciona o trabalho de provisionamento automatizado?

Azure AD provisiona usuários aos aplicativos SaaS conectando-se a pontos de extremidade fornecidos por cada aplicativo fornecedor de provisionamento. Esses pontos de extremidade permitem Azure AD para criar, atualizar e remover usuários programaticamente. Abaixo está uma breve visão geral das etapas diferentes que Azure AD leva para automatizar provisionamento.

1. Quando você habilita a configuração para um aplicativo pela primeira vez, as seguintes ações são executadas:
 - Azure AD tentará coincidir qualquer usuários existentes no aplicativo SaaS com suas identidades correspondentes no diretório. Quando um usuário for encontrado, eles são *não* automaticamente habilitado para logon único. Ordem de um usuário tenha acesso ao aplicativo, eles devem ser explicitamente atribuídos ao aplicativo no Azure AD, diretamente ou por meio de membros do grupo.
 - Se você já especificou quais usuários devem ser atribuídos ao aplicativo, e se Azure AD não conseguir localizar contas existentes para esses usuários, o Azure AD provisionar novas contas para eles no aplicativo.
2. Após a sincronização inicial foi concluída conforme descrito acima, o Azure AD verificará cada 10 minutos para as seguintes alterações:
 - Se novos usuários tem sido atribuídos ao aplicativo (diretamente ou por meio de membros do grupo), em seguida, eles serão provisionado uma nova conta no aplicativo SaaS.
 - Se o acesso de um usuário foi removido, então suas contas no aplicativo SaaS serão marcada como desabilitado (os usuários são nunca totalmente excluídos, que protege contra perda de dados em caso de um erro de configuração).
 - Se um usuário recentemente foi atribuído para o aplicativo e elas já tinham uma conta no aplicativo SaaS, essa conta será marcada como ativado e determinadas propriedades de usuário podem ser atualizadas se eles estiverem desatualizados em comparação com o diretório.
 - Se informações de um usuário (como o número de telefone, localização do escritório, etc) foi alteradas no diretório, e em seguida, essas informações também serão atualizadas no aplicativo SaaS.

Para obter mais informações sobre como os atributos são mapeados entre Azure AD e seu aplicativo SaaS, consulte o artigo sobre [Como personalizar os mapeamentos de atributo](active-directory-saas-customizing-attribute-mappings.md).

##<a name="list-of-apps-that-support-automated-user-provisioning"></a>Lista de aplicativos que oferecem suporte ao provisionamento automatizado do usuário

Clique em um aplicativo para ver um tutorial sobre como configurar provisionamento automatizado para ele:

- [Caixa](http://go.microsoft.com/fwlink/?LinkId=286016)
- [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
- [Coincidem](http://go.microsoft.com/fwlink/?LinkId=309575)
- [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
- [Dropbox para empresas](http://go.microsoft.com/fwlink/?LinkId=309581)
- [Aplicativos do Google](http://go.microsoft.com/fwlink/?LinkId=309577)
- [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
- [SalesForce](http://go.microsoft.com/fwlink/?LinkId=286017)
- [Área restrita de SalesForce](http://go.microsoft.com/fwlink/?LinkId=327869)
- [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
- [DIATRABALHO](http://go.microsoft.com/fwlink/?LinkId=690250) (provisionamento de entrada)

Ordem de um aplicativo suportar provisionamento automatizado do usuário, ele deve fornecer primeiro os pontos de extremidade necessários que permitem programas externos automatizar a criação, manutenção e remoção de usuários. Portanto, nem todos os aplicativos de SaaS são compatíveis com esse recurso. Para os aplicativos que oferecem suporte para isso, a equipe de engenharia do Azure AD será capaz de criar um conector de provisionamento para os aplicativos e esse trabalho é priorizamos às necessidades dos clientes potenciais e atuais.

Para contatar a equipe de engenharia do Azure AD para solicitar suporte provisionamento para aplicativos adicionais, envie uma mensagem por meio do [Active Directory do Azure Fórum de comentários](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Personalizando os mapeamentos de atributo de provisionamento do usuário](active-directory-saas-customizing-attribute-mappings.md)
- [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Escopo filtros para provisionamento do usuário](active-directory-saas-scoping-filters.md)
- [Usando SCIM para habilitar o fornecimento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md)
- [Notificações de provisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
- [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)
