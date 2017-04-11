<properties 
    pageTitle="Configurando Python com aplicativos do Azure serviço de aplicativo Web" 
    description="Este tutorial descreve opções para criação e configurar um servidor de Web básico aplicativo do Gateway Interface (WSGI) compatível com Python nos aplicativos do Azure aplicativo de serviço Web." 
    services="app-service" 
    documentationCenter="python" 
    tags="python"
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="02/26/2016" 
    ms.author="huvalo"/>




# <a name="configuring-python-with-azure-app-service-web-apps"></a>Configurando Python com aplicativos do Azure serviço de aplicativo Web

Este tutorial descreve opções para criação e configurar um aplicativo de básico Python de conformidade com do Interface de Gateway de servidor da Web (WSGI) nos [Aplicativos do Azure aplicativo de serviço Web](http://go.microsoft.com/fwlink/?LinkId=529714).

Ele descreve recursos adicionais de implantação de gito, como ambiente virtual e a instalação do pacote usando requirements.txt.


## <a name="bottle-django-or-flask"></a>Garrafa, Django ou Flask?

Do Azure Marketplace contém modelos para as estruturas de garrafa, Django e Flask. Se você estiver desenvolvendo seu aplicativo web do primeiro no serviço de aplicativo do Azure, ou você não estiver familiarizado com gito, recomendamos que você siga um desses tutoriais, que incluem instruções passo a passo para criar um aplicativo de trabalho na galeria usando a implantação gito do Windows ou Mac:

- [Criando aplicativos web com garrafa](web-sites-python-create-deploy-bottle-app.md)
- [Criando aplicativos web com Django](web-sites-python-create-deploy-django-app.md)
- [Criando aplicativos web com Flask](web-sites-python-create-deploy-flask-app.md)


## <a name="web-app-creation-on-azure-portal"></a>Criação de aplicativo da Web no Portal do Azure

Este tutorial supõe uma assinatura existente do Azure e acesso ao Portal do Azure.

Se você não tiver um aplicativo web existente, você pode criar um a partir do [Portal do Azure](https://portal.azure.com).  Clique no botão novo no canto superior esquerdo, clique em **Web + Mobile** > **Web app**.

## <a name="git-publishing"></a>Publicação de gito

Configure a publicação de gito para o aplicativo web recém-criado, seguindo as instruções na [Implantação Local do gito ao serviço de aplicativo do Azure](app-service-deploy-local-git.md). Este tutorial usa gito para criar, gerenciar e publicar nosso aplicativo da web de Python serviço de aplicativo do Azure.

Após a publicação de gito estiver configurada, um repositório gito será criado e associado ao seu aplicativo web. URL do repositório será exibido e daqui em diante pode ser usado para enviar dados do ambiente de desenvolvimento local na nuvem. Para publicar aplicativos via gito, verifique se que um cliente gito também é instalado e use as instruções fornecidas para enviar seu conteúdo de aplicativo da web para o serviço de aplicativo do Azure.


## <a name="application-overview"></a>Visão geral do aplicativo

Nas próximas seções, os seguintes arquivos são criados. Eles devem ser colocados na raiz do repositório gito.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>Manipulador WSGI

WSGI é um padrão de Python descrito por [PEP 3333](http://www.python.org/dev/peps/pep-3333/) definindo uma interface entre o servidor web e Python. Ele fornece uma interface padronizada para escrever vários aplicativos da web e estruturas usando Python. Estruturas de web populares Python hoje usam WSGI. Azure dá a aplicativo serviço Web Apps que você suporte para tais estruturas; Além disso, usuários avançados podem até mesmo criar seus próprios desde o manipulador personalizado segue as diretrizes de especificação de WSGI.

Aqui está um exemplo de um `app.py` que define um manipulador personalizado:

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

Você pode executar esse aplicativo localmente com `python app.py`, navegue até `http://localhost:5555` no navegador da web.


## <a name="virtual-environment"></a>Ambiente virtual

Embora o aplicativo de exemplo acima não exijam quaisquer pacotes externos, é provável que seu aplicativo exigirá alguns.

Para ajudar a gerenciar dependências externas pacote, implantação do Azure gito suporta a criação de ambientes virtuais.

Quando o Azure detecta um requirements.txt na raiz do repositório, ele cria automaticamente um ambiente virtual chamado `env`. Isso ocorre apenas na primeira implantação ou durante qualquer implantação após o Python selecionado runtime foi alterado.

Você provavelmente desejará criar um ambiente virtual localmente para desenvolvimento, mas não inclua-o no seu repositório gito.


## <a name="package-management"></a>Gerenciamento de pacote

Pacotes listados em requirements.txt serão instalados automaticamente no ambiente virtual usando pip. Isso acontece em cada implantação, mas pip ignorará instalação se um pacote já estiver instalado.

Exemplo `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Versão de Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Exemplo `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Web. config

Você precisará criar um arquivo Web. config para especificar como o servidor deve lidar com solicitações.

Observe que se você tiver um arquivo de web.x.y.config no repositório, onde y corresponde o tempo de execução do Python selecionado, em seguida, Azure copia automaticamente o arquivo apropriado como Web. config.

Nos seguintes exemplos de Web. config dependem de um script de proxy do ambiente virtual, que é descrito na próxima seção.  Elas funcionam com o manipulador WSGI usado no exemplo `app.py` acima.

Exemplo `web.config` para Python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Exemplo `web.config` para Python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Arquivos estáticos serão tratados pelo servidor web diretamente, sem passar pelo código do Python, para melhorar o desempenho.

Nos exemplos acima, o local dos arquivos estáticos no disco deve coincidir o local na URL. Isso significa que uma solicitação para `http://pythonapp.azurewebsites.net/static/site.css` servirá o arquivo no disco em `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER`é onde você especifica o manipulador WSGI. Nos exemplos acima, ele tem `app.wsgi_app` porque o manipulador é uma função chamada `wsgi_app` na `app.py` na pasta raiz.

`PYTHONPATH`pode ser personalizado, mas se você instalar todas as suas dependências no ambiente virtual especificando-os na requirements.txt, você não deve precisar alterá-lo.


## <a name="virtual-environment-proxy"></a>Proxy do ambiente virtual

O script a seguir é usado para recuperar o manipulador WSGI, ative os erros de log e ambiente virtuais. Ele é projetado para ser genérico e usados sem modificações.

Conteúdo do `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys
    import traceback

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')
    
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)
    
        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None
                last_tb = ': ' + traceback.format_exc()
    
        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))
    
        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
    
        site.main()
    
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## <a name="customize-git-deployment"></a>Personalizar gito implantação

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]


## <a name="troubleshooting---package-installation"></a>Solução de problemas - instalação do pacote

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## <a name="troubleshooting---virtual-environment"></a>Solução de problemas - ambiente Virtual

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte o [Python Developer Center](/develop/python/).

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="whats-changed"></a>O que mudou
* Para um guia para a alteração de sites para o serviço de aplicativo consulte: [o serviço de aplicativo do Azure e seu impacto sobre serviços existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)





 
