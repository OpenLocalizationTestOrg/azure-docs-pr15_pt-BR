<properties 
    pageTitle="Passo a passo: Monitorar Microsoft Dynamics CRM com ideias de aplicativo" 
    description="Obtenha telemetria do Microsoft Dynamics CRM Online usando o aplicativo ideias. Explicação passo a passo da instalação, obtendo dados, visualização e exportação." 
    services="application-insights" 
    documentationCenter=""
    authors="mazharmicrosoft" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="awills"/>
 
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Passo a passo: Habilitando telemetria para Microsoft Dynamics CRM Online usando o aplicativo ideias

Este artigo mostra como obter dados de telemetria do [Microsoft Dynamics CRM Online](https://www.dynamics.com/) usando [Ideias de aplicativo do Visual Studio](https://azure.microsoft.com/services/application-insights/). Examinaremos o processo completo da adição de script de obtenção de informações do aplicativo para seu aplicativo, capturando dados e visualização de dados.

>[AZURE.NOTE] [Procurar a solução de exemplo](https://dynamicsandappinsights.codeplex.com/).

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Adicionar aplicativo ideias a instância CRM Online nova ou existente 

Para monitorar seu aplicativo, você pode adicionar um SDK de obtenção de informações do aplicativo para o seu aplicativo. O SDK envia telemetria no [portal de obtenção de informações do aplicativo](https://portal.azure.com), onde você pode usar nossas ferramentas de diagnósticos e análise avançada ou exportar os dados para armazenamento.

### <a name="create-an-application-insights-resource-in-azure"></a>Criar um recurso de obtenção de informações de aplicativo no Azure

1. Obter [uma conta do Microsoft Azure](http://azure.com/pricing). 
2. Entre no [portal do Azure](https://portal.azure.com) e adicione um novo recurso de obtenção de informações do aplicativo. Isso é onde seus dados serão processados e exibidos.

    ![Clique em +, serviços de desenvolvedor, obtenção de informações do aplicativo.](./media/app-insights-sample-mscrm/01.png)

    Escolha ASP.NET como o tipo de aplicativo.

3. Abra a guia de início rápido e abra o script de código.

    ![](./media/app-insights-sample-mscrm/03.png)

**Mantenha a página de código aberta** enquanto você fazer a próxima etapa em outra janela do navegador. Em breve, você precisará o código. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Criar um recurso de web JavaScript no Microsoft Dynamics CRM

1. Abra sua instância CRM Online e faça logon com privilégios de administrador.
2. Abrir o Microsoft Dynamics CRM configurações, personalizações, personalizar o sistema

    ![](./media/app-insights-sample-mscrm/04.png)
    
    ![](./media/app-insights-sample-mscrm/05.png)


    ![](./media/app-insights-sample-mscrm/06.png)

3. Crie um recurso de JavaScript.

    ![](./media/app-insights-sample-mscrm/07.png)

    Dê um nome, selecione **Script (JScript)** e abra o editor de texto.

    ![](./media/app-insights-sample-mscrm/08.png)
    
4. Copie o código de obtenção de informações do aplicativo. Ao copiar Certifique-se de ignorar marcas de script. Consulte abaixo de captura de tela:

    ![](./media/app-insights-sample-mscrm/09.png)

    O código inclui a chave de instrumentação que identifica o recurso de obtenção de informações do aplicativo.

5. Salvar e publicar.

    ![](./media/app-insights-sample-mscrm/10.png)

### <a name="instrument-forms"></a>Formulários de instrumentos

1. No Microsoft CRM Online, abra o formulário de conta

    ![](./media/app-insights-sample-mscrm/11.png)

2. Abra o formulário de propriedades

    ![](./media/app-insights-sample-mscrm/12.png)

3. Adicionar o recurso da web de JavaScript que você criou

    ![](./media/app-insights-sample-mscrm/13.png)

    ![](./media/app-insights-sample-mscrm/14.png)

4. Salvar e publicar suas personalizações formulário.


## <a name="metrics-captured"></a>Métricas capturadas

Agora, você configurou a captura de telemetria do formulário. Sempre que ele é usado, dados serão enviados para o recurso de obtenção de informações do aplicativo.

Aqui estão exemplos dos dados que você verá.

#### <a name="application-health"></a>Integridade do aplicativo

![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

Exceções do navegador:

![](./media/app-insights-sample-mscrm/17.png)

Clique no gráfico para obter mais detalhes:

![](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Uso

![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Navegadores

![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Localização geográfica

![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Solicitação de modo de exibição de página internas

![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Código de exemplo

[Procure o código de exemplo](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI

Você pode fazer uma análise mais profunda mesmo se você [exportar os dados para o Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Solução do exemplo Microsoft Dynamics CRM

[Aqui é a solução de exemplo implementada no Microsoft Dynamics CRM] (https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Saiba Mais

* [O que é a obtenção de informações de aplicativo?](app-insights-overview.md)
* [Obtenção de informações de aplicativo para páginas da web](app-insights-javascript.md)
* [Mais exemplos e instruções passo a passo](app-insights-code-samples.md)

 
