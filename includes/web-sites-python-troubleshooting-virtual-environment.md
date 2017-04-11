O script de implantação irá ignorar a criação do ambiente virtual no Azure caso ele detecte que já existe um ambiente virtual compatível.  Isso pode acelerar a implantação consideravelmente.  Pacotes que já estão instalados serão ignorados por pip.

Em determinadas situações, talvez você queira forçar excluir esse ambiente virtual.  Você vai querer fazer isso se você decidir incluir um ambiente virtual como parte do seu repositório.  Você também pode querer fazer isso se você precisa eliminar determinados pacotes ou teste é alterado para requirements.txt.

Há algumas opções para gerenciar o ambiente virtual existente no Azure:

### <a name="option-1-use-ftp"></a>Opção 1: Use FTP

Com um cliente FTP, conectar ao servidor e você poderá excluir a pasta de envelope.  Observe que alguns clientes FTP (como navegadores da web) podem ser somente leitura e não permitem que você excluir pastas, então você deverá certificar-se de usar um cliente FTP com esse recurso.  O nome do host FTP e o usuário são exibidas em lâmina da seu aplicativo web no [Portal do Azure](https://portal.azure.com).

### <a name="option-2-toggle-runtime"></a>Opção 2: Tempo de execução de alternância

Aqui está uma alternativa que tira proveito do fato de que o script de implantação excluirá a pasta env quando ele não corresponde a versão desejada do Python.  Isso efetivamente excluirá o ambiente existente e crie um novo.

1. Alternar para uma versão diferente do Python (via runtime.txt ou a lâmina de **Configurações do aplicativo** no Portal do Azure)
1. gito push algumas alterações (ignorar quaisquer erros de instalação do pip se houver)
1. Alternar de volta para a versão inicial do Python
1. gito push algumas alterações novamente

### <a name="option-3-customize-deployment-script"></a>Opção 3: Personalizar script de implantação

Se você personalizou o script de implantação, você pode alterar o código no Deploy forçá-la para excluir a pasta de envelope.
