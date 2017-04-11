<properties
    pageTitle="Referência do desenvolvedor funções Azure | Microsoft Azure"
    description="Entenda os conceitos de funções do Azure e componentes que são comuns a todos os idiomas e ligações."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funções, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-developer-reference"></a>Referência do desenvolvedor funções Azure

Funções Azure compartilham alguns conceitos técnicos core e componentes, independentemente do idioma ou associação usada. Antes que pular detalhes específicos para um determinado idioma ou associação de aprendizagem, certifique-se de ler esta visão geral que se aplica a todas elas.

Este artigo pressupõe que você já leu a [Visão geral de funções do Azure](functions-overview.md) e estiver familiarizado com os [conceitos de WebJobs SDK como disparadores, ligações e o tempo de execução de JobHost](../app-service-web/websites-dotnet-webjobs-sdk.md). Funções Azure baseia-se no SDK WebJobs. 


## <a name="function-code"></a>Código de função

Uma *função* é o principal conceito em funções do Azure. Você pode escreve código para uma função em um idioma de sua escolha e salva os arquivos de código e um arquivo de configuração na mesma pasta. Configuração é em JSON, e o arquivo é nomeado `function.json`. Uma variedade de idiomas com suporte, e cada um deles tem uma experiência ligeiramente diferentes otimizada para funcionar melhor para esse idioma. 

O `function.json` arquivo contém configuração específica para uma função, incluindo suas ligações. O tempo de execução lê esse arquivo para determinar quais eventos disparar da, quais dados para incluir ao chamar a função e onde enviar dados passaram da função em si. 

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Você pode impedir que o tempo de execução da execução da função definindo a `disabled` propriedade `true`.

O `bindings` propriedade é onde você configura disparadores e ligações. Cada ligação compartilha algumas configurações comuns e algumas configurações que são específicas para um determinado tipo de vinculação. Cada ligação requer as seguintes configurações:

|Propriedade|Valores/tipos|Comentários|
|---|-----|------|
|`type`|cadeia de caracteres|Tipo de vinculação. Por exemplo, `queueTrigger`.
|`direction`|'in', 'out'| Indica se a vinculação é para receber dados para a função ou enviar dados a partir da função.
| `name` | cadeia de caracteres | O nome que será usado para os dados associados na função. Para c# este será o nome de um argumento; para JavaScript será a chave em uma lista de chave/valor.

## <a name="function-app"></a>Aplicativo de função

Um aplicativo de função é composto por uma ou mais funções individuais que são gerenciadas em conjunto pelo serviço de aplicativo do Azure. Todas as funções em um aplicativo de função compartilham o mesmo plano de preços, a implantação contínua e a versão de runtime. Funções escritas em vários idiomas podem compartilhar o mesmo aplicativo de função. Pense em um aplicativo de função como uma maneira de organizar e gerenciar coletivamente suas funções. 

## <a name="runtime-script-host-and-web-host"></a>Tempo de execução (host de scripts e host da web)

O tempo de execução ou host de scripts, é o host de WebJobs SDK subjacente que ouve para eventos, coleta e envia dados e, por fim, executa o seu código. 

Para facilitar a gatilhos de HTTP, há também um host da web que foi projetado para ficar na frente do host de scripts em cenários de produção. Isso ajuda a isolar o host de scripts do tráfego de front-end gerenciado pelo host da web.

## <a name="folder-structure"></a>Estrutura da pasta

[AZURE.INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Ao configurar um projeto para implantar funções em um aplicativo de função em um serviço de aplicativo do Azure, você pode tratar essa estrutura de pastas como o código do seu site. Você pode usar ferramentas existentes como integração contínua e implantação ou scripts de implantação personalizada para fazer implantar a instalação do pacote de tempo ou transpilation de código.

>[AZURE.NOTE] O `wwwroot` pasta aqui é onde seus arquivos irá obter implantados para. No entanto, você não deve incluir nessa pasta nos arquivos de implantar, seriam acabar tendo `wwwroot\wwwroot`. Em vez disso, sua `host.json` pastas de arquivo e função devem ser diretamente na raiz do qual você implanta.

## <a id="fileupdate"></a>Como atualizar arquivos de aplicativo de função

O editor de função incorporado no portal do Azure permite que você atualize o arquivo *function.json* e o arquivo de código para uma função. Para carregar ou atualizar outros arquivos como *package.json* ou *project.json* ou dependências, você precisa usar outros métodos de implantação.

Aplicativos de função são criados no serviço de aplicativo, para que todas as [Opções de implantação disponíveis para aplicativos web padrão](../app-service-web/web-sites-deploy.md) estão disponíveis para aplicativos de função também. Aqui estão alguns métodos que você pode usar para carregar ou atualizar arquivos de aplicativo de função. 

#### <a name="to-use-app-service-editor"></a>Usar o Editor de serviço de aplicativo

1. No portal do Azure funções, clique em **configurações do aplicativo de função**.

2. Na seção **Configurações avançadas** , clique em **Ir para configurações de serviço do aplicativo**.

3. Clique em **Editor de serviço de aplicativo** no Menu do aplicativo de navegação em **Ferramentas de desenvolvimento**.

4.  Clique em **Ir**.

    Depois de Editor de serviço de aplicativo carrega, você verá as pastas de arquivo e função *host.json* em *wwwroot*. 

5. Abrir arquivos para editá-los, ou arraste e solte da sua máquina de desenvolvimento de carregar arquivos.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>Usar o ponto de extremidade do aplicativo função SCM (Kudu)

1. Navegue até: `https://<function_app_name>.scm.azurewebsites.net`.

2. Clique em **Depurar Console > CMD**.

3. Navegue até `D:\home\site\wwwroot\` atualizar *host.json* ou `D:\home\site\wwwroot\<function_name>` para atualizar arquivos de uma função.

4. Arrastar e soltar um arquivo que você deseja carregar para a pasta apropriada na grade de arquivo. Há duas áreas na grade de arquivo onde você pode soltar um arquivo. Para arquivos *. zip* , uma caixa aparece com o rótulo "Arraste aqui para carregar e descompacte". Para outros tipos de arquivo, solte na grade de arquivo, mas fora da caixa "Descompacte".

#### <a name="to-use-ftp"></a>Usar FTP

1. Siga as instruções [aqui](../app-service-web/web-sites-deploy.md#ftp) para obter FTP configurado.

2. Quando você está conectado ao site do aplicativo de função, copiar um arquivo atualizado *host.json* para `/site/wwwroot` ou copiar arquivos de função para `/site/wwwroot/<function_name>`.

#### <a name="to-use-continuous-deployment"></a>Usar implantação contínua

Siga as instruções no tópico [implantação contínua para funções do Azure](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Execução paralela

Quando vários eventos de disparo ocorrem mais rápido do que um tempo de execução de segmentação única função pode processá-los, o tempo de execução pode chamar a função várias vezes em paralelo.  Se um aplicativo de função estiver usando o [Plano de serviço dinâmico](functions-scale.md#dynamic-service-plan), o aplicativo de função poderia dimensionar automaticamente.  Cada instância do aplicativo função, se o aplicativo é executado no plano de serviço dinâmico ou um regulares [Plano de serviço do aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), talvez processar chamadas simultâneas de função em paralelo usando vários threads.  O número máximo de chamadas simultâneas de função em cada instância do aplicativo de função varia de acordo com o tamanho de memória do aplicativo função. 

## <a name="azure-functions-pulse"></a>Funções Azure pulso  

Pulso é um fluxo de evento ao vivo que mostra a frequência sua função é executado, bem como êxitos e falhas. Você também pode monitorar seu tempo médio de execução. Podemos vai adicionar mais recursos e personalização-lo ao longo do tempo. Você pode acessar a página de **pulso** da guia **monitoramento** .

## <a name="repositories"></a>Repositórios

O código para funções do Azure é abrir origem e armazenado em GitHub repositórios:

* [Tempo de execução de funções Azure](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Azure portal de funções](https://github.com/projectkudu/AzureFunctionsPortal)
* [Modelos de funções Azure](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/)
* [Extensões do Azure SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Ligações

Aqui está uma tabela de todas as ligações com suporte.

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

## <a name="reporting-issues"></a>Relatório de problemas

[AZURE.INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)] 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos:

* [Referência de desenvolvedor do Azure funções c#](functions-reference-csharp.md)
* [Referência de desenvolvedor do Azure funções F #](functions-reference-fsharp.md)
* [Referência do desenvolvedor funções NodeJS Azure](functions-reference-node.md)
* [Ligações e azure disparadores de funções](functions-triggers-bindings.md)
* [Azure funções: A jornada](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) no blog da equipe de serviço de aplicativo do Azure. Um histórico de como as funções do Azure foi desenvolvida.





