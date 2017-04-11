Azure determinará a versão do Python para usar para seu ambiente virtual com a prioridade a seguir:

1. versão especificada no runtime.txt na pasta raiz
1. versão especificada pela configuração de Python na configuração do aplicativo da web (as **configurações** > lâmina de**Configurações do aplicativo** para o aplicativo da web no Portal do Azure)
1. Python 2.7 é o padrão se nenhuma das anteriores forem especificadas

Valores válidos para o conteúdo de 

    \runtime.txt

são:

- Python 2.7
- Python-3,4

Se a versão micro (terceiro dígito) for especificado, ela será ignorada.
