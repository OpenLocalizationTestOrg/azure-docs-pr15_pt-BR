
O código para todas as funções em um aplicativo de determinada função reside em uma pasta raiz que contém um arquivo de configuração de host e uma ou mais subpastas, cada um deles contêm o código para uma função separado, como no exemplo seguinte

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

O arquivo *host.json* contém algumas configurações específicas do runtime e fica na pasta raiz do aplicativo função. Para obter informações sobre as configurações que estão disponíveis, consulte [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) no wiki do repositório WebJobs.Script.

Cada função tem uma pasta que contém um ou mais arquivos de código, a configuração de function.json e outras dependências.