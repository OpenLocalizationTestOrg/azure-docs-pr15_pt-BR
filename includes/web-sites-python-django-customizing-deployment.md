Azure determinará que seu aplicativo usará Python **se ambas essas condições forem verdadeiras**:

- arquivo de Requirements.txt na pasta raiz
- qualquer arquivo .py na pasta raiz ou um runtime.txt que especifica python

Quando for o caso, ele usará um script de implantação específicas Python, que realiza a sincronização padrão de arquivos, bem como as operações de Python adicionais, como:

- Gerenciamento automático do ambiente virtual
- Instalação de pacotes listados no requirements.txt usando pip
- Criação de apropriado Web. config com base na versão Python selecionada.
- Coletar arquivos estáticos para aplicativos de Django

Você pode controlar determinados aspectos das etapas de implantação de padrão sem precisar personalize o script.

Se você quiser ignorar todas as etapas de implantação específicas de Python, você pode criar esse arquivo vazio:

    \.skipPythonDeployment

Se você quiser ignorar a coleção de arquivos estáticos para seu aplicativo de Django:

    \.skipDjango 

Para ter mais controle sobre a implantação, você pode substituir o script de implantação padrão criando os arquivos a seguir:

    \.deployment
    \deploy.cmd

Você pode usar a [interface de linha de comando Azure][] para criar os arquivos.  Use este comando da pasta do projeto:

    azure site deploymentscript --python

Quando esses arquivos não existir, o Azure cria um script de implantação temporária e executa.  É idêntico ao que você cria com o comando acima.

[Interface de linha de comando Azure]: http://azure.microsoft.com/downloads/
