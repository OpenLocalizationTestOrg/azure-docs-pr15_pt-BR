Alguns pacotes não podem instalar usando pip quando executado no Azure.  Simplesmente pode ser que o pacote não está disponível no índice Python pacote.  É possível que um compilador é necessário (um compilador não está disponível na máquina executando o web app em um serviço de aplicativo do Azure).

Nesta seção, vamos examinar maneiras de lidar com esse problema.

### <a name="request-wheels"></a>Solicitação de rodas

Se a instalação do pacote requer um compilador, tente entrar em contato com o proprietário do pacote para solicitar que rodas disponibilizados para o pacote.

Com a disponibilidade recente do [Compilador do Microsoft Visual C++ para 2.7 Python][], agora é mais fácil criar pacotes que têm código nativo para 2.7 Python.

### <a name="build-wheels-requires-windows"></a>Construir rodas (requer o Windows)

Observação: Ao usar essa opção, certifique-se de compilação do pacote usando um ambiente de Python que corresponde à plataforma/arquitetura/versão que é usada no aplicativo web do serviço de aplicativo do Azure (Windows/32-bit/2.7 ou 3.4).

Se o pacote não instalar porque ela requer um compilador, você pode instalar o compilador em sua máquina local e criar uma roda para o pacote, o que você incluirão em seu repositório.

Os usuários do Mac/Linux: Se você não tiver acesso a um computador Windows, consulte [criar uma máquina Virtual executando o Windows][] como criar uma máquina virtual no Azure.  Você pode usá-lo para criar as rodas, adicioná-los ao repositório e descartar a máquina virtual se desejar. 

Para Python 2.7, você pode instalar o [Compilador do Microsoft Visual C++ para Python 2.7][].

Para Python 3.4, você pode instalar o [Microsoft Visual C++ 2010 Express][].

Para criar rodas, você precisará do pacote de roda:

    env\scripts\pip install wheel

Você usará `pip wheel` para compilar uma dependência:

    env\scripts\pip wheel azure==0.8.4

Isso cria um arquivo de .whl na pasta \wheelhouse.  Adicione os arquivos de pasta e roda de \wheelhouse ao seu repositório.

Editar seu requirements.txt para adicionar o `--find-links` opção na parte superior. Isso informa pip para procurar uma correspondência exata na pasta local antes de ir para o índice de pacote de python.

    --find-links wheelhouse
    azure==0.8.4

Se você quiser incluir todas as suas dependências na pasta \wheelhouse e não usar o índice de pacote python em todos os, você também pode forçar pip para ignorar o índice de pacote adicionando `--no-index` na parte superior da sua requirements.txt.

    --no-index

### <a name="customize-installation"></a>Personalizar instalação

Você pode personalizar o script de implantação para instalar um pacote no ambiente virtual usando um instalador alternativo, como fácil\_instalar.  Consulte Deploy para obter um exemplo que é comentado.  Certifique-se de que tais pacotes não estão listados em requirements.txt, para impedir que pip instalá-los.

Adicione isto como o script de implantação:

    env\scripts\easy_install somepackage

Você também poderá usar fácil\_para instalar de um instalador exe (alguns são zip compatível, tão fácil\_instalação oferece suporte a eles).  Adicionar o instalador ao seu repositório e invocar fácil\_instalar passando o caminho para o executável.

Adicione isto como o script de implantação:

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>Incluir o ambiente virtual no repositório (requer o Windows)

Observação: Quando usar essa opção, verifique se usar um ambiente virtual que corresponde à plataforma/arquitetura/versão que é usada no aplicativo web do serviço de aplicativo do Azure (Windows/32-bit/2.7 ou 3.4).

Se você incluir o ambiente virtual no repositório, você pode impedir que o script de implantação fazendo o gerenciamento de ambiente virtual no Azure, criando um arquivo vazio:

    .skipPythonDeployment

Recomendamos que você exclua o ambiente virtual existente no aplicativo, para impedir que arquivos abandonados de quando o ambiente virtual foi gerenciado automaticamente.


[Criar uma máquina Virtual executando o Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Compilador do Microsoft Visual C++ para Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
