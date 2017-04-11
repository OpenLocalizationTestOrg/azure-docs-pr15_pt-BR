<properties 
    pageTitle="Erro durante a detecção de autenticação" 
    description="O Assistente de conexão do active directory detectou um tipo de autenticação incompatível" 
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

# <a name="error-during-authentication-detection"></a>Erro durante a detecção de autenticação

Ao detectar código de autenticação anterior, o assistente detectou um tipo de autenticação incompatíveis.   

##<a name="what-is-being-checked"></a>O que está sendo verificado?

**Observação:** Para detectar corretamente o código de autenticação anterior em um projeto, o projeto deve ser criado.  Se você encontrou esse erro e você não tiver um código de autenticação anterior em seu projeto, recriar e tente novamente.

###<a name="project-types"></a>Tipos de projeto

O assistente verifica qual tipo de projeto que você está desenvolvendo para que ele pode introduzir a lógica de autenticação à direita no projeto.  Se houver qualquer controlador que deriva `ApiController` no projeto, o projeto será considerado um projeto WebAPI.  Se houver somente controladores que derivam de `MVC.Controller` no projeto, o projeto será considerado um projeto MVC.  Mais alguma coisa não é suportada pelo assistente.  Projetos de formulários da Web não são suportados no momento.

###<a name="compatible-authentication-code"></a>Código de autenticação compatíveis

O assistente também verifica as configurações de autenticação que foi configuradas anteriormente com o assistente ou são compatíveis com o assistente.  Se todas as configurações estiverem presentes, ela é considerada uma ocorrência reentrantes e o assistente será aberto e exibirá as configurações.  Se apenas algumas das configurações estiverem presentes, ele é considerado um caso de erro.

Em um projeto MVC, o assistente verifica qualquer uma das configurações a seguir, que resultam do uso anterior do assistente:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Além disso, o assistente verifica qualquer uma das configurações a seguir em um projeto de Web API, que resultam do uso anterior do assistente:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

###<a name="incompatible-authentication-code"></a>Código de autenticação incompatíveis

Por fim, o assistente tenta detectar versões do código de autenticação que foram configuradas com versões anteriores do Visual Studio. Se você receber esse erro, significa que seu projeto contém um tipo de autenticação incompatíveis. O assistente detecta os seguintes tipos de autenticação de versões anteriores do Visual Studio:

* Autenticação do Windows 
* Contas de usuário individuais 
* Contas organizacionais 
 

Para detectar autenticação do Windows em um projeto do MVC, o assistente procurará o `authentication` elemento do seu arquivo **Web. config** .

<pre>
    &lt;configuração&gt;
        &lt;System. Web&gt;
            <span style="background-color: yellow">&lt;modo de autenticação = "Windows" /&gt;</span>
        &lt;sozinhas&gt;
    &lt;/configuração&gt;
</pre>

Para detectar autenticação do Windows em um projeto de API da Web, o assistente procurará o `IISExpressWindowsAuthentication` elemento do arquivo **. csproj** do seu projeto:

<pre>
    &lt;Projeto&gt;
&lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;habilitado&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup > &lt;/projeto        &gt;
</pre>

Para detectar autenticação de contas de usuário individuais, o assistente procurará o elemento de pacote do seu arquivo de **Packages.config** .

<pre>
    &lt;pacotes&gt;
        <span style="background-color: yellow">&lt;empacotar id="Microsoft.AspNet.Identity.EntityFramework" versão = "2.1.0" targetFramework = "net45" /&gt;</span>
    &lt;/pacotes&gt;
</pre>

Para detectar um formulário antigo de autenticação de conta organizacional, o assistente procurará o seguinte elemento de **Web. config**:

<pre>
    &lt;configuração&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;Adicionar chave = valor de "ida: território" = "* * *" /&gt;</span>
        &lt;/appSettings&gt;
    &lt;/configuração&gt;
</pre>

Para alterar o tipo de autenticação, remova o tipo de autenticação incompatível e execute o assistente novamente.

Para obter mais informações, consulte [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md).