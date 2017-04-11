<properties 
    pageTitle="Get Started com o Active Directory do Azure e os serviços do Visual Studio conectado (projetos MVC) | Microsoft Azure" 
    description="Como começar a usar o Active Directory do Azure em projetos MVC depois conectando ao criar um anúncio Azure usando o Visual Studio conectado serviços" 
    services="active-directory" 
    documentationCenter="" 
    authors="TomArcher" 
    manager="douge" 
    editor=""/>
  
<tags 
    ms.service="active-directory" 
    ms.workload="web" 
    ms.tgt_pltfrm="vs-getting-started" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="tarcher"/>

# <a name="getting-started-with-azure-active-directory-and-visual-studio-connected-services-mvc-projects"></a>Introdução ao Active Directory do Azure e Visual Studio conectado serviços (MVC projetos)

> [AZURE.SELECTOR]
> - [Guia de Introdução](vs-active-directory-dotnet-getting-started.md)
> - [O que aconteceu](vs-active-directory-dotnet-what-happened.md)
 
##<a name="requiring-authentication-to-access-controllers"></a>Exigir autenticação para controladores de acesso 

Todos os controladores em seu projeto foram adornados com o atributo **Autorizar** . Esse atributo exigem o usuário sejam autenticados antes de acessar esses controladores. Para permitir que o controlador ser acessada anonimamente, remova esse atributo do controlador. Se você deseja definir as permissões em um nível mais granular, aplique o atributo a cada método que requer autorização em vez de aplicá-la para a classe de controlador.
 
##<a name="adding-signin--signout-controls"></a>Adicionando entrar / SignOut controles 

Para adicionar um os controles de entrar/SignOut à sua exibição, você pode usar o modo de exibição parcial **_LoginPartial.cshtml** para adicionar a funcionalidade de um dos seus modos de exibição. Aqui está um exemplo da funcionalidade adicionada ao modo de exibição padrão **cshtml** . (Observe o último elemento no div com barra de navegação de classe-recolher):

<pre>
    &lt;! DOCTYPE html&gt; 
&lt;html&gt; 
&lt;head&gt; 
&lt;meta charset="utf-8" /&gt; 
&lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt; 
&lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
@Styles.Render("~/Content/css") @Scripts.Render("~/bundles/modernizr") &lt;/head&gt; 
&lt;body&gt; 
&lt;div class="navbar navbar-inverse navbar-fixed-top"&gt; 
&lt;div class="container"&gt; 
&lt;div class="navbar-header"&gt; 
&lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;/button&gt; 
@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) &lt;/div&gt; 
&lt;div class="navbar-collapse collapse"&gt; 
&lt;ul class="nav navbar-nav"&gt; 
&lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt; 
&lt;/ul&gt; 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/ div&gt; 
&lt;/div&gt; 
&lt;/div&gt; 
&lt;div class="container body-content"&gt; 
@RenderBody() &lt;hr /&gt; 
&lt;footer&gt; 
&lt;p&gt;&amp;copy; @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
&lt;/footer&gt; 
&lt;/div&gt; 
@Scripts.Render("~/bundles/jquery") @Scripts.Render("~/bundles/bootstrap") @RenderSection("scripts", required: false) &lt;/body&gt; 
&lt;/html                                                                                                                                                                                                                                                                                                                                                                                                                                                           &gt;
</pre>

[Saiba mais sobre o Active Directory do Azure](https://azure.microsoft.com/services/active-directory/) 
