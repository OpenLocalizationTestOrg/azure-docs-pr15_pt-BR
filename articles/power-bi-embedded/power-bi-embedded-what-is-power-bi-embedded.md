<properties
   pageTitle="O que é o Microsoft Power BI incorporado?"
   description="Power BI incorporado permite integrar relatórios do Power BI seu web ou aplicativos móveis para que você não precisa criar soluções personalizadas para visualizar dados para seus usuários"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="what-is-microsoft-power-bi-embedded"></a>O que é o Microsoft Power BI incorporado?

Com o **Power BI inserida**, você pode integrar relatórios do Power BI direita sua web ou aplicativos móveis.

![](media\powerbi-embedded-whats-is\what-is.png)

Power BI incorporado é um **serviço Azure** que permite ISVs e desenvolvedores de aplicativo para expor experiências de dados do Power BI dentro de seus aplicativos. Como um desenvolvedor, você já criou aplicativos e esses aplicativos têm seus próprios usuários e distinto conjunto de recursos. Esses aplicativos também podem acontecer ter alguns elementos de dados internas, como gráficos e relatórios que podem agora ser da plataforma do Microsoft Power BI inserida. Os usuários não é necessário uma conta do Power BI para usar seu aplicativo. Eles podem continuar a entrar no seu aplicativo assim como antes e exibir e interagir com o Power BI experiência de relatório sem a necessidade de qualquer licenciamento adicional.

## <a name="licensing-for-microsoft-power-bi-embedded"></a>Licenciamento para Microsoft Power BI incorporados

No modelo de uso do **Microsoft Power BI inserida** , licenciamento do Power BI não é responsabilidade do usuário final.  Em vez disso, **processa** adquiridos pelo desenvolvedor do aplicativo que está consumindo os elementos visuais e cobrado para a assinatura que possui esses recursos.

## <a name="microsoft-power-bi-embedded-conceptual-model"></a>Microsoft Power BI incorporado modelo conceitual

![](media\powerbi-embedded-whats-is\model.png)

Como qualquer outro serviço no Azure, os recursos para o Power BI inserida são provisionados através de do [Azure BRAÇO APIs](https://msdn.microsoft.com/library/mt712306.aspx). Nesse caso, o recurso que você provisionar é uma **Coleção de espaço de trabalho do Power BI**.

## <a name="workspace-collection"></a>Conjunto de espaço de trabalho

Uma **Coleção de espaço de trabalho** é o primeiro nível Azure contêiner para recursos contém 0 ou mais **espaços de trabalho**.  Um **espaço de trabalho** **conjunto** tem todas as propriedades padrão do Azure, bem como o seguinte:

-   **Teclas de acesso** – teclas usadas ao chamar com segurança as APIs do Power BI (descrito em uma seção posterior).
-   **Usuários** – usuários Azure Active Directory (AAD) que tem direitos de administrador para gerenciar a coleção de espaço de trabalho do Power BI por meio do portal Azure ou API de ARM.
-   **Região** – como parte de provisionamento uma **Coleção de espaço de trabalho**, você pode selecionar uma região para ser provisionados em. Para obter mais informações, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Espaço de trabalho

Um **espaço de trabalho** é um contêiner de conteúdo de Power BI, que pode incluir conjuntos de dados, relatórios e painéis. Um **espaço de trabalho** está vazia quando criado pela primeira vez. Durante a visualização, você vai criar todo o conteúdo usando o Power BI Desktop e você carregará a um de seus espaços de trabalho usando as [APIs REST do Power BI](http://docs.powerbi.apiary.io/reference).

## <a name="using-workspace-collections-and-workspaces"></a>Usar espaços de trabalho e conjuntos de espaço de trabalho
**Espaços de trabalho** e **Conjuntos de espaço de trabalho** são contêineres de conteúdo que são usados e organizados em melhor forma se encaixa no design do aplicativo que você está criando. Haverá muitas maneiras diferentes que você pode organizar o conteúdo contido nelas. Você pode optar por colocar todo o conteúdo dentro de um espaço de trabalho e depois use posteriormente tokens de aplicativo para subdividir ainda mais o conteúdo entre seus clientes. Você também pode optar por colocar todos os seus clientes em espaços de trabalho separados para que haja alguma separação entre elas. Ou, você pode optar por organizar usuários por região em vez de cliente. Este design flexível permite que você escolha a melhor maneira de organizar o conteúdo.

## <a name="cached-datasets"></a>Conjuntos de dados armazenados em cache

Conjuntos de dados armazenados em cache podem ser usados na visualização.  No entanto, é possível atualizar dados armazenados em cache quando ele foi carregado no **Microsoft Power BI inserida**.

## <a name="authentication-and-authorization-with-app-tokens"></a>Autenticação e autorização com tokens de aplicativo

**Microsoft Power BI incorporado** diferente ao seu aplicativo para executar todas as a autenticação de usuário necessárias e a autorização. Não há nenhum requisito explícito que os usuários finais sejam clientes do Azure Active Directory (AD Azure).  Em vez disso, seu aplicativo expressa para **Microsoft Power BI incorporado** autorização para renderizar um relatório do Power BI usando **Tokens de autenticação de aplicativo (App Tokens)**.  Esses **Tokens de aplicativo** são criadas conforme necessário quando quiser que seu aplicativo renderizar um relatório.  Consulte [Tokens de aplicativo](power-bi-embedded-get-started-sample.md#key-flow).

![](media\powerbi-embedded-whats-is\app-tokens.png)

**Tokens de autenticação de aplicativo (App Tokens)** são usados para autenticar o **Microsoft Power BI inserida**.  Há três tipos de **Tokens de aplicativo**:

1.  Provisionamento Tokens - usados ao provisionar um novo **espaço de trabalho** em uma **Coleção de espaço de trabalho**
2.  Tokens de desenvolvimento - usados ao fazer chamadas diretamente para as **APIs REST do Power BI**
3.  Incorporação Tokens - usados ao fazer chamadas para renderizar um relatório no iframe inserido

Esses tokens são usados para as diversas fases de suas interações com o **Microsoft Power BI inserida**.  Os tokens foram projetados para que você pode delegar permissões de seu aplicativo Power BI. Para obter mais informações, consulte [Fluxo Token do aplicativo](power-bi-embedded-app-token-flow.md).

## <a name="see-also"></a>Consulte também
- [Cenários comuns de Microsoft Power BI inserida](power-bi-embedded-scenarios.md)
- [Introdução ao Microsoft Power BI inserida](power-bi-embedded-get-started.md)
