<properties
    pageTitle="Visão geral dos conectores de aplicativos de lógica | Microsoft Azure"
    description="Visão geral dos conectores que podem ser usados em um aplicativo de lógica"
    services=""
    documentationCenter="" 
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# <a name="using-connectors-in-a-logic-app"></a>Usar conectores em um aplicativo de lógica

Conectores fornecem acesso rápido aos dados, eventos e ações em serviços, protocolos e plataformas.  A lista completa de conectores que dá suporte a lógica de aplicativos podem [ser encontradas aqui](apis-list.md).  Conectores pode ser usados como um disparador ou uma ação em um aplicativo de lógica e podem exigir uma *conexão* configurada para usar (por exemplo: autorizar uma conta do Twitter para acessar ou postar em seu nome).

## <a name="basics"></a>Noções básicas

Conectores são serviços hospedados, você pode acessar como parte de um aplicativo de lógica para integrar com outros serviços como Dynamics, Azure, Salesforce, [e mais](apis-list.md).  Eles são implantados e gerenciados pela Microsoft, para poder criar seus fluxos de trabalho de integração com escala, produtividade e segurança atendidas.  Você pode adicionar um conector para um aplicativo de lógica pesquisando e selecionando uma ação de conector ou disparador em **Mostrar Microsoft APIs gerenciadas**.

![Menu de ação para selecionar disparador][1]

Cada ação de conector ou disparador terá seu conjunto de propriedades para configurar.  Você pode clicar nos botões de informações para saber mais sobre a ação, ou referência sua documentação [para saber mais](apis-list.md).

Se você deseja integrar com um serviço ou API que não está ainda um conector, você também pode estender aplicativos lógica através de um [conector personalizado](../app-service-logic/app-service-logic-create-api-app.md) ou simplesmente chamar diretamente para o serviço por um protocolo HTTP.

## <a name="triggers"></a>Disparadores

Alguns conectores têm um disparador, o que significa um evento a partir desse conector será acionar um aplicativo de lógica e passar em quaisquer dados como parte do disparador.  Um disparador é sempre a primeira etapa em um aplicativo de lógica.  Disparadores populares incluem operações como:
 
 * Recorrência - executada a cada hora
 * Quando uma solicitação HTTP for recebida
 * Quando um item é adicionado a uma fila
 * Quando um email é recebido
 
Alguns disparadores serão acionada instantâneas que um evento ocorre por meio de uma notificação para o aplicativo de lógica e outras pessoas precisarão um intervalo de recorrência configurado em com que frequência o aplicativo de lógica verificará o serviço para um evento (até cada 15 segundos).  

Depois que um evento for recebido, o aplicativo de lógica executar será acionada e as ações no fluxo de trabalho serão iniciado.  Você também poderá acessar quaisquer dados do disparador em todo o fluxo de trabalho (por exemplo o disparador 'em um tweet novo' passará a tweet para executar).

## <a name="actions"></a>Ações

A maioria dos conectores têm uma ou várias ações que podem ser executadas como parte do fluxo de trabalho.  Ações são as etapas que ocorrer após a execução foi acionado de um disparador.  Para adicionar um clique ação o botão **Nova etapa** e procure o conector que você deseja usar.  Depois de selecionado (e depois configurando todas as [conexões](#connections) que podem ser necessárias) você verá o cartão de ação que você pode configurar.  Você pode selecionar dados de etapas anteriores, clicando em qualquer um dos tokens para saídas ou inserir em qualquer outra configuração conforme necessário.

![Configurar uma ação de conector][2]

## <a name="connections"></a>Conexões

A maioria dos conectores exigem que você configurar uma *conexão* antes de poder usar o conector.  Uma *conexão* é qualquer configuração de login ou conexão necessária para acessar o conector.  Para os conectores que use OAuth, criar uma conexão significa entrar no serviço (como o Office 365, Salesforce ou GitHub) onde seu token de acesso pode ser criptografada e armazenada com segurança em uma loja secreta Azure.  Outros conectores (como FTP e SQL) requerem uma conexão que contém a configuração como o endereço do servidor, nome de usuário e senha.  Esses detalhes de configuração de conexão sejam também criptografadas e armazenadas com segurança.  Conexões poderão acessar o serviço para desde que o serviço permite.  Para conexões do Azure Active Directory OAuth (como o Office 365 e Dynamics) nós pode continuar atualizar o token de acesso indefinidamente.  Outros serviços podem colocar limites em quanto tempo podemos usar um token sem ele sendo atualizados.  Em geral determinadas ações como alterar uma senha serão invalidadas todos os tokens de acesso.  

Conexões podem ser visualizadas e gerenciadas no Azure clicando em **Procurar** e selecionando **API conexões**.  Do recurso de conexões de API que você pode exibir, editar, atualizar ou autorizar novamente as conexões que você criou.

## <a name="next-steps"></a>Próximas etapas

- [Criar seu primeiro aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Aprenda comuns usa e exemplos de aplicativos de lógica](../app-service-logic/app-service-logic-examples-and-scenarios.md)
- [Introdução ao ações e disparadores de integração do enterprise](../app-service-logic/app-service-logic-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png