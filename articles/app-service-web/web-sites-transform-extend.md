<properties
    pageTitle="Azure serviço de aplicativo web app config e extensões avançadas"
    description="Use declarações de Transformation(XDT) de documento XML para transformar o arquivo applicationHost no seu aplicativo web do serviço de aplicativo do Azure e adicionar extensões privadas para habilitar ações de administração personalizada."
    authors="cephalin"
    writer="cephalin"
    editor="mollybos"
    manager="wpickett"
    services="app-service"
    documentationCenter=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="azure-app-service-web-app-advanced-config-and-extensions"></a>Azure serviço de aplicativo web app config e extensões avançadas

Usando declarações de [Transformação de documento XML](http://msdn.microsoft.com/library/dd465326.aspx) (XDT), você pode transformar o arquivo [applicationHost](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) em seu aplicativo web em um serviço de aplicativo do Azure. Você também pode usar declarações XDT adicionar extensões privadas para habilitar ações de administração do aplicativo web personalizado. Este artigo inclui uma extensão de aplicativo de web PHP Gerenciador de amostra que permite o gerenciamento das configurações de PHP por meio de uma interface da web.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a id="transform"></a>Configuração avançada por meio de applicationHost
A plataforma de serviço de aplicativo fornece flexibilidade e controle para configuração do aplicativo da web. Embora o arquivo de configuração do IIS applicationHost padrão não está disponível para edição direta no aplicativo de serviço, a plataforma aceita um modelo de transformação applicationHost declarativa com base na transformação de documento XML (XDT).

Para aproveitar essa funcionalidade de transformação, você pode criar um arquivo de ApplicationHost.xdt com conteúdo XDT e local na raiz do site (d:\home\site) no [Console de Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console). Talvez você precise reiniciar o aplicativo Web para alterações tenham efeito.

Exemplo de applicationHost.xdt a seguir mostra como adicionar uma nova variável de ambiente personalizado a um aplicativo web que usa PHP 5.4.

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.webServer>
                <fastCgi>
                    <application>
                        <environmentVariables>
                                <environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
                        </environmentVariables>
                    </application>
                </fastCgi>
        </system.webServer>
    </configuration>


Um arquivo de log com detalhes e status de transformação está disponível na raiz FTP em LogFiles\Transform.

Para exemplos adicionais, consulte [https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples).

**Observação**<br />
Elementos da lista de módulos em `system.webServer` não podem ser removidos ou um novo pedido, mas adições à lista são possíveis.


##<a id="extend"></a>Estender seu aplicativo web

###<a id="overview"></a>Visão geral das extensões de aplicativo web particular

Serviço de aplicativo oferece suporte a extensões de aplicativo da web como um ponto de extensibilidade para ações administrativas. Na verdade, alguns recursos de plataforma do serviço de aplicativo são implementados como extensões pré-instalados. Enquanto as extensões de plataforma pré-instalado não podem ser modificadas, você pode criar e configurar as extensões de privada para seu próprio aplicativo web. Essa funcionalidade também depende de declarações XDT. As principais etapas para a criação de uma extensão de aplicativo web particulares são as seguintes:

1. Extensão de aplicativo **conteúdo**da Web: criar qualquer aplicativo da web com suporte pelo serviço de aplicativo
2. Extensão de aplicativo **declaração**da Web: criar um arquivo de ApplicationHost.xdt
3. De extensão **implantação**do aplicativo da Web: colocar conteúdo na pasta SiteExtensions em`root`

Links internos para o aplicativo web devem apontar para um caminho relativo o caminho de aplicativo especificado no arquivo ApplicationHost.xdt. Qualquer alteração no arquivo ApplicationHost.xdt requer uma reciclagem de aplicativo web.

**Observação**: informações adicionais para esses elementos principais estão disponíveis em [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

Um exemplo detalhado é incluído para ilustrar as etapas para criar e ativar uma extensão de aplicativo web particular. O código-fonte para que o exemplo de Gerenciador de PHP que segue pode ser baixado do [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager).

###<a id="SiteSample"></a>Exemplo de extensão do aplicativo Web: Gerenciador de PHP

Gerenciador de PHP é uma extensão de aplicativo da web que permite web administradores de aplicativo exibir e configurar suas configurações de PHP usando uma interface da web em vez de ter que modificar ini PHP diretamente facilmente. Arquivos de configuração comuns para PHP incluem o arquivo de ini localizado em arquivos de programa e. user.ini arquivo localizado na pasta raiz do seu aplicativo web. Como o arquivo ini não for editável diretamente na plataforma de serviço de aplicativo, a extensão do Gerenciador de PHP usa o. user.ini arquivo para aplicar alterações de configuração.

####<a id="PHPwebapp"></a>O aplicativo da web de Gerenciador de PHP

A seguir é a home page da implantação do Gerenciador de PHP:

![TransformSitePHPUI][TransformSitePHPUI]

Como você pode ver, uma extensão de aplicativo da web é como um aplicativo web normal, mas com um arquivo de ApplicationHost.xdt adicional colocado na pasta raiz do aplicativo web (mais detalhes sobre o arquivo de ApplicationHost.xdt estão disponíveis na próxima seção deste artigo).

A extensão do Gerenciador de PHP foi criada usando o modelo de aplicativo de Web do Visual Studio ASP.NET MVC 4. Modo de exibição acompanhando do Solution Explorer mostra a estrutura da extensão do Gerenciador de PHP.

![TransformSiteSolEx][TransformSiteSolEx]

A lógica somente especial necessária para o arquivo e/s é indicar onde se encontra o diretório wwwroot do aplicativo web. Como mostra o exemplo de código a seguir, o ambiente variável "HOME" indica o caminho da raiz do aplicativo da web e o caminho de wwwroot pode ser construído anexando "site\wwwroot":

    /// <summary>
    /// Gives the location of the .user.ini file, even if one doesn't exist yet
    /// </summary>
    private static string GetUserSettingsFilePath()
    {
            var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
            if (rootPath == null)
            {
                rootPath = System.IO.Path.GetTempPath(); // For testing purposes
            };
            var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
            return userSettingsFile;
    }


Depois que o caminho do diretório, você pode usar operações de e/s de arquivo normal para ler e gravar arquivos.

Um ponto de cuidado com as extensões de aplicativo web considera a manipulação de links internos.  Se você tiver quaisquer links em seus arquivos HTML que fornecem caminhos absolutos para links internos no seu aplicativo web, você deve garantir que esses links são anexados com seu nome de extensão como sua raiz. Isso é necessário porque a raiz para o ramal agora é "/`[your-extension-name]`/" em vez de sendo links apenas "/", então qualquer interno deverá ser atualizado adequadamente. Por exemplo, suponha que seu código inclui um link para o seguinte:

`"<a href="/Home/Settings">PHP Settings</a>"`

Quando o link for parte de uma extensão de aplicativo da web, o link deve ser da seguinte forma:

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

Você pode contornar esse requisito por qualquer um usando somente os caminhos relativos no seu aplicativo web ou no caso de aplicativos ASP.NET, usando a `@Html.ActionLink` método que cria os links apropriados para você.

####<a id="XDT"></a>O arquivo de applicationHost.xdt

O código para sua extensão de aplicativo web vai em %HOME%\SiteExtensions\[seu nome de extensão]. Vai chamamos isso a raiz de extensão.  

Para registrar sua extensão de aplicativo web com o arquivo applicationHost, você precisa colocar um arquivo denominado ApplicationHost.xdt na raiz do ramal. O conteúdo do arquivo ApplicationHost.xdt deve ser da seguinte maneira:

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.applicationHost>
                <sites>
                    <site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
                        <!-- NOTE: Add your extension name in the application paths below -->
                        <application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
                        <application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
                            <virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
                        </application>
                    </site>
                </sites>
        </system.applicationHost>
    </configuration>

O nome que você selecionar como seu nome de extensão deve ter o mesmo nome como sua pasta raiz de extensão.

Isso tem o efeito de adicionar um novo caminho de aplicativo para o `system.applicationHost` lista de sites sob o site SCM. O site SCM é um ponto de fim de administração do site com credenciais de acesso específico. Ela tem a URL `https://[your-site-name].scm.azurewebsites.net`.  

    <system.applicationHost>
        ...
        <site name="~1[your-website]" id="1716402716">
                <bindings>
                    <binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
                    <binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
                </bindings>
                <traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
                <detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
                <logFile logSiteId="false" />
                <application path="/" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
                </application>
                <!-- Note the custom changes that go here -->
                <application path="/[your-extension-name]" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
                </application>
            </site>
    </sites>
      ...
    </system.applicationHost>

###<a id="deploy"></a>Implantação de extensão do aplicativo Web

Para instalar a extensão de aplicativo web, você pode usar FTP para copiar todos os arquivos de seu aplicativo web para o `\SiteExtensions\[your-extension-name]` pasta do aplicativo da web no qual você deseja instalar a extensão.  Certifique-se de copiar o arquivo ApplicationHost.xdt para esse local também. Reinicie o seu aplicativo web para habilitar a extensão.

Você deve ser capaz de ver sua extensão de aplicativo da web em:

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

Observe que a URL parece com a URL para o aplicativo web, exceto que ele usa HTTPS e contém ".scm".

É possível desabilitar todas as extensões de (não pré-instaladas) privadas para seu aplicativo web durante o desenvolvimento e investigações, adicionando um configurações do aplicativo com a chave `WEBSITE_PRIVATE_EXTENSIONS` e um valor de `0`.

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="whats-changed"></a>O que mudou
* Para um guia para a alteração de sites para o serviço de aplicativo consulte: [o serviço de aplicativo do Azure e seu impacto sobre serviços existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
 
