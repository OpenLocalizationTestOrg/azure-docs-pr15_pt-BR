<properties 
pageTitle="Executar tarefas de inicialização em serviços de nuvem Azure | Microsoft Azure" 
description="Tarefas de inicialização ajudam a preparar o ambiente de serviço de nuvem para seu aplicativo. Isso ensina como funcionam as tarefas de inicialização e como para torná-las" 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>



# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Como configurar e executar tarefas de inicialização para um serviço de nuvem

Você pode usar tarefas de inicialização para executar operações antes de inicia uma função. Operações que talvez você queira executar incluem instalando um componente, registrando componentes COM, definindo as chaves do registro ou iniciando um processo longo em execução.

>[AZURE.NOTE] Tarefas de inicialização não são aplicáveis às máquinas virtuais, apenas para funções de trabalho e da Web de serviços de nuvem.

## <a name="how-startup-tasks-work"></a>Como funcionam as tarefas de inicialização

As tarefas de inicialização são ações que são executadas antes de suas funções começam e são definidas no arquivo [ServiceDefinition.csdef] usando o elemento de [tarefa] dentro do elemento de [inicialização] . Frequentemente tarefas de inicialização são arquivos em lotes, mas eles também podem ser aplicativos de console ou arquivos em lotes que inicie scripts do PowerShell.

Variáveis de ambiente passam informações para uma tarefa de inicialização e armazenamento local pode ser usado para passar informações fora de uma tarefa de inicialização. Por exemplo, uma variável de ambiente pode especificar o caminho para um programa que você deseja instalar e arquivos podem ser gravados para o armazenamento local, em seguida, pode ser lido posteriormente por suas funções.

A tarefa de inicialização pode efetuar logon informações e erros no diretório especificado pela variável de ambiente **TEMP** . Durante a tarefa de inicialização, a variável de ambiente **TEMP** resolve para o *c:\\recursos\\temp\\[guid]. [ nome de função]\\RoleTemp* directory durante a execução na nuvem.

Tarefas de inicialização também podem ser executadas várias vezes entre reinicializar. Por exemplo, a tarefa de inicialização será executada sempre que a função reciclagem e reciclagem de função pode não incluir sempre uma reinicialização. Tarefas de inicialização devem ser escritas de uma maneira que permita executar várias vezes sem problemas.

Tarefas de inicialização devem terminar com um **errorlevel** (ou o código de saída) de zero para o processo de inicialização para ser concluída. Se uma tarefa de inicialização termina com um diferente de zero **errorlevel**, a função não será iniciado.


## <a name="role-startup-order"></a>Ordem de inicialização de função

A seguir apresenta o procedimento de inicialização de função no Azure:

1. A instância está marcada como **inicial** e não receber o tráfego.

2. Todas as tarefas de inicialização são executadas de acordo com seu atributo **taskType** .
    - As tarefas **simples** serão executadas em sincronia, uma de cada vez.
    - As tarefas e **plano** de **fundo** são iniciadas assíncrona, paralelo para a tarefa de inicialização.  
       
    > [AZURE.WARNING] IIS pode não estar totalmente configurado durante o estágio de tarefa de inicialização no processo de inicialização, para que os dados de funções específicas podem não estar disponíveis. Tarefas de inicialização que exigem dados específicos de função devem usar [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

3. O processo de host de função é iniciado e o site é criado no IIS.

4. O método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) é chamado.

5. A instância está marcada como **pronto** e tráfego é roteado para a instância.

6. O método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) é chamado.


## <a name="example-of-a-startup-task"></a>Exemplo de uma tarefa de inicialização

Tarefas de inicialização são definidas no arquivo [ServiceDefinition.csdef] , no elemento de **tarefa** . O atributo de **linha de comando** Especifica o nome e os parâmetros do arquivo de inicialização lote ou comando de console, o atributo **executionContext** Especifica o nível de privilégio da tarefa inicialização e o atributo de **taskType** Especifica como a tarefa será executada.

Neste exemplo, uma variável de ambiente, **MyVersionNumber**, é criada para a tarefa de inicialização e defina com o valor "**1.0.0.0**".

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

No exemplo a seguir, o arquivo em lotes **Startup.cmd** grava a linha "a versão atual é 1.0.0.0" para o arquivo de StartupLog.txt no diretório especificado pela variável de ambiente TEMP. O `EXIT /B 0` linha garante que a tarefa de inicialização termina com um **errorlevel** de zero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [AZURE.NOTE] No Visual Studio, a propriedade **Copiar para diretório de saída** para o arquivo de lote de inicialização deve ser definida como **Copiar sempre** Certifique-se de que o arquivo de lote de inicialização está implantado corretamente ao seu projeto no Azure (**approot\\bin** para funções da Web e **approot** para funções de trabalho).

## <a name="description-of-task-attributes"></a>Descrição dos atributos da tarefa

A tabela a seguir descreve os atributos do elemento **Task** no arquivo [ServiceDefinition.csdef] :

**linha de comando** - Especifica a linha de comando para a tarefa de inicialização:

- O comando, com parâmetros de linha de comando opcionais, que começa a tarefa de inicialização.
- Frequentemente, isso é o nome de um arquivo de lote cmd ou. bat.
- A tarefa é relativo a AppRoot\\pasta Bin para a implantação. Variáveis de ambiente não são expandidas determinar o caminho e o arquivo da tarefa. Se o ambiente expansão for necessária, você pode criar um script de pequenas cmd que chama sua tarefa de inicialização.
- Pode ser um aplicativo de console ou um arquivo em lotes que inicia um [script do PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** - Especifica o nível de privilégio da tarefa de inicialização. O nível de privilégio pode ser limitado ou elevado:

- **limitado**  
A tarefa de inicialização é executado com os mesmos privilégios que a função. Quando o atributo **executionContext** para o elemento de [tempo de execução] também é **limitado**, privilégios de usuário são usados.

- **elevados**  
A tarefa de inicialização é executado com privilégios de administrador. Isso permite que as tarefas de inicialização para instalar programas, faça alterações de configuração do IIS, executar alterações de registro e outras tarefas de nível de administrador, sem aumentar o nível de privilégio da função em si.  

> [AZURE.NOTE] O nível de privilégio de uma tarefa de inicialização não precisa ser o mesmo que a função em si.

**taskType** - Especifica a maneira como uma tarefa de inicialização é executada.

- **simples**  
Tarefas são executados em sincronia, uma de cada vez, na ordem especificada no arquivo [ServiceDefinition.csdef] . Quando uma tarefa **simples** de inicialização termina com um **errorlevel** de zero, a próxima tarefa **simples** de inicialização é executada. Se não houver nenhuma mais tarefas **simples** de inicialização para executar, em seguida, a função em si será iniciada.   

    > [AZURE.NOTE] Se a tarefa **simples** termina com um diferente de zero **errorlevel**, a instância será bloqueada. Tarefas subsequentes **simples** de inicialização e a função em si, não serão iniciado.

    Para garantir que seu arquivo em lotes termina com um **errorlevel** de zero, execute o comando `EXIT /B 0` no final do seu processo de arquivo em lote.

- **plano de fundo**  
Tarefas são executados de forma assíncrona, em paralelo com a inicialização da função.

- **primeiro plano**  
Tarefas são executados de forma assíncrona, em paralelo com a inicialização da função. A principal diferença entre um **primeiro plano** e uma tarefa de **plano de fundo** é que uma tarefa de **primeiro plano** impede a função de reciclagem ou desligar até que a tarefa foi concluída. As tarefas de **plano de fundo** não tem essa restrição.

## <a name="environment-variables"></a>Variáveis de ambiente

Variáveis de ambiente são uma maneira para passar informações para uma tarefa de inicialização. Por exemplo, você pode colocar o caminho para um blob que contém um programa para instalar, números de porta que sua função usará ou configurações aos recursos de controle de sua tarefa de inicialização.

Existem dois tipos de variáveis de ambiente para tarefas de inicialização; ambiente estático variáveis e com base em membros da classe [RoleEnvironment] . Ambos estão na seção [ambiente] do arquivo [ServiceDefinition.csdef] e usam o atributo elemento e o **nome** da [variável] .

Variáveis de ambiente estático usa o atributo de **valor** do elemento [variável] . O exemplo acima cria a variável de ambiente **MyVersionNumber** que tem um valor estático de "**1.0.0.0**". Outro exemplo seria criar uma variável de ambiente **StagingOrProduction** que você pode definir manualmente valores de "**teste**" ou "**produção**" para executar ações de inicialização diferente com base no valor da variável de ambiente **StagingOrProduction** .

Variáveis de ambiente baseadas em membros da classe RoleEnvironment não use o atributo de **valor** do elemento [variável] . Em vez disso, o elemento filho [RoleInstanceValue] , com o valor de atributo **XPath** apropriado, são usados para criar uma variável de ambiente com base em um membro específico da classe [RoleEnvironment] . Valores para o atributo **XPath** acessar vários valores de [RoleEnvironment] podem ser encontrados [aqui](cloud-services-role-config-xpath.md).



Por exemplo, para criar uma variável de ambiente que é "**true**" quando a instância está em execução no emulador de computação e "**false**" durante a execução na nuvem, use os seguintes elementos de [variável] e [RoleInstanceValue] :

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
    
            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->
    
            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>
    
        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Próximas etapas
Saiba como realizar algumas [tarefas comuns de inicialização](cloud-services-startup-tasks-common.md) com o seu serviço de nuvem.

[Pacote](cloud-services-model-and-package.md) seu serviço de nuvem.  


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tarefa]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Inicialização]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Tempo de execução]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Ambiente]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variável]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx