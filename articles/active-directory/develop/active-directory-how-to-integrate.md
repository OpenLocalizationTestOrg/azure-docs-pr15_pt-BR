<properties
   pageTitle="Como fazer a integração com o Active Directory do Azure | Microsoft Azure"
   description="Um guia para benefícios e recursos para integração com o Active Directory do Azure."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="integrating-with-azure-active-directory"></a>Integração com o Active Directory do Azure

[AZURE.INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Active Directory do Azure fornece a organizações com gerenciamento de identidades de nível empresarial para aplicativos em nuvem.  Integração do Azure AD oferece aos usuários uma experiência simplificada entrar e ajuda seu aplicativo estão em conformidade com a política de TI.

## <a name="how-to-integrate"></a>Como integrar

Há várias maneiras para seu aplicativo para integrar com o Azure AD.  Aproveite como muitos ou pouquíssimos desses cenários conforme apropriado para seu aplicativo.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Azure AD como uma forma de entrada em seu aplicativo de suporte

**Reduzir entrar em conflito e os custos de suporte.** Usando o Azure AD para entrar em seu aplicativo, os usuários não têm um nome mais e lembrar senha.  Como um desenvolvedor, você terá uma menos senha para armazenar e proteger.  Não ter de lidar com redefinições de senha esquecida pode ser uma economia significativa sozinha.  Azure AD alimenta entrar para alguns dos mais populares aplicativos de nuvem do mundo, incluindo o Office 365 e Microsoft Azure.  Com centenas de milhões usuários milhões de organizações, chances são de usuário já está conectado ao Azure AD.  Saiba mais sobre [Adicionar suporte para entrar do Azure AD](../active-directory-authentication-scenarios.md).

**Simplifica a entrada para cima para seu aplicativo.**  Durante a inscrição para o seu aplicativo, o Azure AD pode enviar informações essenciais sobre um usuário para que você pode preencher previamente o formulário de inscrição ou eliminá-lo completamente.  Os usuários podem inscrever-se para seu aplicativo usando sua conta do Azure AD por meio de uma experiência de consentimento familiar semelhante aos encontrados em mídia social e aplicativos móveis.  Qualquer usuário pode inscrever-se e entrar em um aplicativo que é integrado ao Azure AD sem a necessidade de envolvimento de TI.  Saiba mais sobre [assinatura-up seu aplicativo para logon Azure AD conta](../../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Procurar por usuários, gerenciar provisionamento do usuário e controlar o acesso ao seu aplicativo

**Procure por usuários no diretório.**  Usar a API de gráfico para ajudar os usuários a pesquisar e navegar para outras pessoas em sua organização ao convidar outras pessoas ou endereços de concedendo acesso, em vez de exigir que eles digitar email.  Os usuários podem procurar usando uma interface de estilo de catálogo de endereços familiar, incluindo exibam os detalhes da hierarquia organizacional.  Saiba mais sobre a [API do gráfico](../active-directory-graph-api.md).

**Reutilizar grupos do Active Directory e listas de distribuição que do cliente já está gerenciando.**  Azure AD contém os grupos que seu cliente já está usando para distribuição de email e gerenciando o acesso.  Novamente usando a API do gráfico, use a esses grupos em vez de exigindo a seu cliente criar e gerenciar um conjunto de grupos em seu aplicativo separado.  Informações de grupo também podem ser enviadas para seu aplicativo no tokens de entrada.  Saiba mais sobre a [API do gráfico](../active-directory-graph-api.md).

**Use o Azure AD para controlar quem tem acesso ao seu aplicativo.**  Os administradores e proprietários de aplicativo no Azure AD podem atribuir acesso a aplicativos para usuários e grupos específicos.  Usando a API do gráfico, você pode ler esta lista e usá-lo para controlar o fornecimento e cancelamento de recursos e acessar dentro de seu aplicativo.

**Controle de acesso baseado em uso Azure AD para funções.**  Os administradores e proprietários de aplicativo podem atribuir usuários e grupos a funções que você definir quando você registrar seu aplicativo no Azure AD.  Informações de função são enviadas para seu aplicativo em tokens de entrada e também podem ser lidos usando a API do gráfico.  Saiba mais sobre como [usar o Azure AD para autorização](http://blogs.technet.com/b/ad/archive/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles.aspx).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Obter acesso a perfil do usuário, calendário, Email, contatos, arquivos e muito mais

**Azure AD é o servidor de autorização para Office 365 e outros serviços de negócios da Microsoft.**  Se você suporte Azure AD para entrar no seu aplicativo ou suporte vinculação suas contas de usuário atual para contas de usuário do Azure AD usando OAuth 2.0, você pode solicitar o acesso de leitura e gravação para um perfil de usuário, calendário, email, contatos, arquivos e outras informações.  Você pode perfeitamente gravar eventos ao calendário do usuário e ler ou gravar arquivos em seu OneDrive.  Saiba mais sobre como [acessar as APIs do Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promover seu aplicativo no Azure e mercados do Office 365

**Promova seu aplicativo para milhões de organizações que já utilizam Azure AD.**  Os usuários que e pesquise esses mercados já estiver usando um ou mais serviços de nuvem, facilitando qualificados clientes de serviço de nuvem.  Saiba mais sobre como promover seu aplicativo [Do Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Quando os usuários se inscrever para seu aplicativo, ele aparecerá no seu painel de acesso do Azure AD e o inicializador de aplicativos do Office 365.**  Os usuários poderão rapidamente e facilmente retorne ao seu aplicativo mais tarde, melhorando o contrato de usuário.  Saiba mais sobre o [Painel de acesso do Azure AD](../active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Comunicação segura de serviço do dispositivo e ao serviço

**Usando o Azure AD para gerenciamento de identidades dos serviços e dispositivos reduz o código que necessário para escrever e permite que a TI para gerenciar o acesso.**  Serviços e dispositivos podem obter tokens do Azure AD usando OAuth e use esses tokens para acessar web APIs.  Usando o Azure AD você pode evitar escrever código complexas de autenticação.  Como as identidades dos serviços e dispositivos são armazenadas no AD Azure, IT pode gerenciar chaves e revogação em um só lugar em vez de ter que fazer isso separadamente em seu aplicativo.

## <a name="benefits-of-integration"></a>Benefícios de integração

Integração com o Azure AD vem com benefícios que não exigem a escrever código adicional.

### <a name="integration-with-enterprise-identity-management"></a>Integração com o gerenciamento de identidades corporativas

**Ajuda seu aplicativo cumprir as políticas de TI.**  Organizações integram seus sistemas de gerenciamento de identidade empresarial com o Azure AD, para que quando uma pessoa sai uma organização, eles perderão o acesso automaticamente ao seu aplicativo sem precisar tomar medidas adicionais de IT.  IT pode gerenciar quem pode acessar seu aplicativo e determinar quais políticas de acesso são necessárias - para autenticação multifator de exemplo - reduzindo sua necessidade de escrever código para cumprir políticas corporativas complexas.  Azure AD fornece aos administradores um log de auditoria detalhada de quem conectado ao seu aplicativo portanto IT pode controlar o uso.

**Azure AD estende Active Directory na nuvem para que seu aplicativo pode integrar com o AD.**  Muitas organizações em todo o mundo usam o Active Directory como seus entrar principal e o sistema de gerenciamento de identidade e exigem seus aplicativos para trabalhar com o AD.  Integração com o Azure AD integra o seu aplicativo com o Active Directory.

### <a name="advanced-security-features"></a>Recursos avançados de segurança

**Autenticação multifator.**  Azure AD fornece nativa autenticação multifator.  Os administradores de TI podem exigir autenticação multifator para acessar o aplicativo, para que você não precisa codificar esse suporte.  Saiba mais sobre [Autenticação multifator](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Detecção de entrar anômalos.**  Azure AD processa suplementos sinal de mais de um bilhões por dia, usando algoritmos de aprendizado de máquina para detectar atividades suspeitas e notificar os administradores de TI sobre possíveis problemas.  Com o suporte Azure AD entrar, seu aplicativo obtém o benefício desta proteção. Saiba mais sobre [Exibindo Azure Active Directory acessar o relatório](../active-directory-view-access-usage-reports.md).

**Acesso condicional.**  Além de autenticação multifator, os administradores podem requerer condições específicas ser atendidas antes que os usuários possam entrar no aplicativo.  Condições que podem ser definidas incluir o intervalo de endereços IP de dispositivos cliente, participação em grupos especificados e o estado do dispositivo sendo usado para o access.  Saiba mais sobre [acesso condicional do Active Directory do Azure](../active-directory-conditional-access.md).

### <a name="easy-development"></a>Desenvolvimento fácil

**Protocolos padrão do setor.**  A Microsoft está comprometida em oferecer suporte a padrões do setor.  Azure AD é compatível com os protocolos de autenticação SAML 2.0, OpenID conectar 1.0, 2.0 OAuth e Web Services Federation 1.2.  A API do gráfico é 4.0 do OData compatíveis.  Se seu aplicativo já suporta os protocolos SAML 2.0 ou OpenID conectar 1.0 para entrar federados, adicionar suporte para Azure AD pode ser simples.  Saiba mais sobre [protocolos de autenticação do Azure AD com suporte](../active-directory-authentication-protocols.md).

**Bibliotecas de código-fonte aberto.**  A Microsoft fornece bibliotecas totalmente suportado Abrir fonte para plataformas para acelerar o desenvolvimento e idiomas populares.  O código-fonte está licenciado sob Apache 2.0, e estiver livre bifurcação e contribuam para os projetos.  Saiba mais sobre [bibliotecas de autenticação do Azure AD](../active-directory-authentication-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Presença mundial e alta disponibilidade

**Azure AD está implantado em data centers em todo o mundo e é gerenciado e monitorado sem parar.**  Azure AD é o sistema de gerenciamento de identidades do Microsoft Azure e o Office 365 e está implantado em 28 dos data centers em todo o mundo.  Dados de diretório certamente ser replicados para pelo menos três dos data centers.  Balanceadores de carga global garantem o acesso de usuários a cópia mais próxima do Azure AD que contém seus dados e novamente rotear automaticamente solicitações aos outros dos data centers se um problema é detectado.

## <a name="next-steps"></a>Próximas etapas

[Começar a escrever código](../active-directory-developers-guide.md#getting-started).

[Usuários de entrada usando o Azure AD](../active-directory-authentication-scenarios.md)
