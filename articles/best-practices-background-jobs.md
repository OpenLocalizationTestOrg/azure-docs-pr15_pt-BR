<properties
   pageTitle="Orientação de trabalhos de plano de fundo | Microsoft Azure"
   description="Orientação sobre plano de fundo tarefas que executar independentemente da interface do usuário."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2016"
   ms.author="masashin"/>

# <a name="background-jobs-guidance"></a>Orientação de trabalhos de plano de fundo

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

## <a name="overview"></a>Visão geral

Muitos tipos de aplicativos requerem tarefas em segundo plano que são executadas independentemente da interface do usuário (UI). Alguns exemplos incluem trabalhos em lotes, tarefas de processamento intenso e processos de execução demorada como fluxos de trabalho. Trabalhos em segundo plano podem ser executados sem a necessidade de interação do usuário, o aplicativo pode iniciar o trabalho e, em seguida, continue a processar interativas solicitações de usuários. Isso pode ajudar a reduzir a carga no aplicativo de interface do usuário, que pode melhorar a disponibilidade e reduzir os tempos de resposta interativa.

Por exemplo, se um aplicativo é necessária para gerar miniaturas das imagens que são carregadas por usuários, pode fazer isso como um trabalho de plano de fundo e salve a miniatura ao armazenamento quando ela for concluída, sem que o usuário precisar aguardar o processo ser concluído. Da mesma forma, um usuário fazer um pedido pode iniciar um fluxo de trabalho do plano de fundo que processa a ordem, enquanto a interface do usuário permite que o usuário continue a navegação do web app. Quando o trabalho de plano de fundo estiver concluído, ele pode atualizar os dados armazenados pedidos e enviar um email para o usuário que confirma o pedido.

Quando você considera se implementar uma tarefa como um trabalho de plano de fundo, o critério principal é se a tarefa pode ser executado sem interação do usuário e a interface do usuário precisar esperar o trabalho ser concluída. Tarefas que exigem o usuário ou a interface do usuário aguardar enquanto elas são concluídas talvez não seja apropriadas como trabalhos de plano de fundo.

## <a name="types-of-background-jobs"></a>Tipos de trabalhos em segundo plano

Trabalhos em segundo plano normalmente incluem um ou mais dos seguintes tipos de trabalhos:

- Trabalhos de CPU intenso, como cálculos matemáticos ou análise de modelo estrutural.
- Posso/O-que requer muita trabalhos, como executar uma série de transações de armazenamento ou arquivos de indexação.
- Trabalhos em lotes, como atualizações de dados à noite ou processamento agendado.
- Fluxos de trabalho de execução demorada, como pedidos ou serviços e sistemas de provisionamento.
- Processamento de dados confidenciais onde a tarefa é entregue a um local mais seguro para processamento. Por exemplo, talvez você não queira processar dados confidenciais dentro de um aplicativo web. Em vez disso, você pode usar um padrão como [Gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx) para transferir os dados para um processo de plano de fundo isolados que tenha acesso ao armazenamento protegido.

## <a name="triggers"></a>Disparadores

Trabalhos de plano de fundo podem ser iniciados de diversas maneiras. Eles se encaixam em uma das categorias a seguir:

- [**Gatilhos de controlada por evento**](#event-driven-triggers). A tarefa é iniciada em resposta a um evento, normalmente uma ação executada por um usuário ou uma etapa de fluxo de trabalho.
- [**Controlado pelo cronograma disparadores**](#schedule-driven-triggers). A tarefa é chamada em um cronograma com base em um temporizador. Isso pode ser uma agenda recorrente ou uma chamada unitárias especificado para posteriormente.

### <a name="event-driven-triggers"></a>Gatilhos de controlada por evento

Controlado pelo evento invocação usa um disparador para iniciar a tarefa de plano de fundo. Exemplos do uso de disparadores controlada por evento incluem:

- A interface do usuário ou outro trabalho coloca uma mensagem em uma fila. A mensagem contém dados sobre uma ação que tenha ocorrido, como o usuário fazer um pedido. A tarefa de plano de fundo ouve nessa fila e detecta a chegada de uma nova mensagem. Lê a mensagem e usa os dados contidos como a entrada para o trabalho de plano de fundo.
- A interface do usuário ou outro trabalho salva ou atualiza um valor em armazenamento. A tarefa de plano de fundo monitora o armazenamento e detecta alterações. Lê os dados e usa como entrada para o trabalho de plano de fundo.
- A interface do usuário ou outro trabalho faz uma solicitação para um ponto de extremidade, como um URI HTTPS ou uma API exposto como um serviço da web. Ela passa os dados que é necessário para concluir a tarefa de plano de fundo como parte da solicitação. O ponto de extremidade ou serviço web invoca a tarefa de plano de fundo, que usa os dados como sua entrada.

Exemplos de tarefas que são adequados para invocação orientados a eventos típicos incluem processamento, fluxos de trabalho, enviando informações para serviços remota, enviando mensagens de email e novos usuários em vários locatários aplicativos de provisionamento de imagem.

### <a name="schedule-driven-triggers"></a>Controlado pelo cronograma disparadores

Controlado pelo cronograma invocação usa um temporizador para iniciar a tarefa de plano de fundo. Exemplos do uso de disparadores controlado pelo cronograma incluem:

- Um temporizador que esteja executando localmente dentro do aplicativo ou como parte do sistema operacional do aplicativo invoca uma tarefa de plano de fundo regularmente.
- Um temporizador que é executado em um aplicativo diferente, ou um serviço de timer como o Agendador do Azure, envia uma solicitação para um serviço da web ou API regularmente. O serviço web ou API invoca a tarefa de plano de fundo.
- Um processo separado ou aplicativo inicia um cronômetro que faz com que a tarefa de plano de fundo a ser chamado uma vez após um intervalo de tempo especificado, ou em uma hora específica.

Exemplos de tarefas que são adequados para invocação controlado pelo cronograma típicos incluem rotinas de processamento em lotes (como atualizar listas de produtos relacionados para usuários com base em seu comportamento recente), tarefas de rotina de processamento de dados (como atualização de índices ou gerando resultados acumulados), análises de dados para relatórios de diários, limpeza de retenção de dados e verificações de consistência de dados.

Se você usa uma tarefa controlado pelo cronograma que deve ser executado como uma única instância, lembre-se dos seguintes procedimentos:

- Se a instância de computação que está executando o Agendador (como uma máquina virtual usando tarefas agendadas do Windows) estiver em escala, você terá várias instâncias do Agendador executando. Esses podem iniciar várias instâncias da tarefa.
- Se executar tarefas for maior que o período entre eventos do Agendador, o Agendador pode iniciar outra instância da tarefa enquanto anterior ainda está sendo executado.

## <a name="returning-results"></a>Retornando resultados

Trabalhos em segundo plano assíncrona executam em um processo separado, ou até mesmo em um local separado, da interface do usuário ou processo que invocado a tarefa de plano de fundo. Ideal seria tarefas em segundo plano são operações "disparar e esquecer" e seu progresso de execução não tem nenhum impacto sobre a interface do usuário ou o processo de chamada. Isso significa que o processo de chamada não aguardar a conclusão das tarefas. Portanto, ele não pode detectar automaticamente quando a tarefa termina.

Se você precisar de uma tarefa de plano de fundo para se comunicar com a tarefa chamada para indicar o andamento ou conclusão, você deve implementar um mecanismo para isso. Alguns exemplos são:

- Gravar um valor de indicador de status armazenamento acessível para a tarefa de interface do usuário ou chamador, que pode monitorar ou verificar esse valor quando necessário. Outros dados que a tarefa de plano de fundo deve retornar para o chamador podem ser colocados no mesmo armazenamento.
- Estabelece uma fila de resposta que a interface do usuário ou o chamador ouve. A tarefa de plano de fundo pode enviar mensagens à fila que indicam status e conclusão. Dados que a tarefa de plano de fundo deve retornar para o chamador podem ser colocados nas mensagens. Se você estiver usando o barramento de serviço do Azure, você pode usar as propriedades **ReplyTo** e **CorrelationId** implementar esse recurso. Para obter mais informações, consulte [correlação no serviço barramento orientado Messaging](http://www.cloudcasts.net/devguide/Default.aspx?id=13029).
- Expor uma API ou ponto de extremidade da tarefa plano de fundo que a interface do usuário ou o chamador pode acessar para obter informações de status. Dados que a tarefa de plano de fundo deve retornar para o chamador podem ser incluídos na resposta.
- Têm a tarefa de plano de fundo de retorno de chamada para a interface do usuário ou o chamador através de uma API para indicar o status em pontos predefinidos ou conclusão. Isso pode ser por meio de eventos gerados localmente ou um mecanismo de publicação e assinatura. Dados que a tarefa de plano de fundo deve retornar para o chamador podem ser incluídos na carga solicitação ou evento.

## <a name="hosting-environment"></a>Ambiente de hospedagem

Você pode hospedar tarefas em segundo plano usando uma variedade de serviços de plataforma Windows Azure diferentes:

- [**WebJobs e azure Web Apps**](#azure-web-apps-and-webjobs). Você pode usar WebJobs para executar trabalhos personalizados com base em um intervalo de diferentes tipos de scripts ou programas executáveis dentro do contexto de um aplicativo web.
- [**Funções de trabalho e da web de serviços de nuvem do azure**](#azure-cloud-services-web-and-worker-roles). Você pode escrever código dentro de uma função que é executado como uma tarefa de plano de fundo.
- [**Azure máquinas virtuais**](#azure-virtual-machines). Se você tiver um serviço do Windows ou deseja usar o Agendador de tarefas do Windows, é comum hospedar suas tarefas de plano de fundo em uma máquina virtual dedicada.
- [**Lote azure**](./batch/batch-technical-overview.md). É um serviço de plataforma que agenda o trabalho pesados para executar em um conjunto gerenciado de máquinas virtuais, e pode automaticamente escala de recursos de computação para atender às necessidades dos seus trabalhos.

As seções a seguir descrevem cada uma dessas opções detalhadamente e incluem considerações para ajudá-lo a escolher a opção apropriada.

## <a name="azure-web-apps-and-webjobs"></a>WebJobs e azure Web Apps

Você pode usar o Azure WebJobs para executar trabalhos personalizados como tarefas de plano de fundo dentro de um aplicativo Web do Azure. WebJobs executar dentro do contexto de seu aplicativo web como um processo contínuo. WebJobs também são executados em resposta a um evento acionador do Agendador do Azure ou fatores externos, como as alterações blobs de armazenamento e filas de mensagens. Trabalhos podem ser iniciados e interrompidos sob demanda e desligar normalmente. Se um WebJob continuamente em execução falhar, ele é automaticamente reiniciado. Ações de repetição e de erro são configuráveis.

Quando você configura um WebJob:

- Se quiser que o trabalho para responder a um gatilho controlada por evento, você deve configurá-lo como **executar continuamente**. O script ou programa é armazenado na pasta denominada site/wwwroot/app_data/trabalhos/contínuo.
- Se quiser que o trabalho para responder a um disparador controlado pelo cronograma, você deve configurá-lo como **executados em um cronograma**. O script ou programa é armazenado na pasta denominada site/wwwroot/app_data/trabalhos/disparadas.
- Se você escolher a opção **executar sob demanda** quando você configura um trabalho, ele executará o mesmo código que a opção **executar em um cronograma** quando você iniciá-lo.

Azure WebJobs executados na área restrita do aplicativo web. Isso significa que eles podem acessar variáveis de ambiente e compartilhar informações, como cadeias de caracteres de conexão, com o aplicativo web. O trabalho tem acesso ao identificador exclusivo da máquina que está executando o trabalho. A cadeia de conexão denominada **AzureWebJobsStorage** fornece acesso filas de armazenamento do Azure, blobs e tabelas para dados de aplicativos e ao barramento de serviço de mensagens e comunicação. A cadeia de conexão denominada **AzureWebJobsDashboard** fornece acesso aos arquivos de log de ação de trabalho.

WebJobs Azure têm as seguintes características:

- **Segurança**: WebJobs protegidos pelas credenciais de implantação do aplicativo web.
- **Tipos de arquivo com suporte**: você pode definir WebJobs usando scripts de comando (. cmd), arquivos em lotes (. bat), scripts do PowerShell (. ps1), bash scripts do shell (sh), scripts PHP (. PHP), scripts Python (.py), código JavaScript (. js) e programas executáveis (.exe. jar e muito mais).
- **Implantação**: você pode implantar scripts e executáveis usando o portal do Azure, usando o suplemento [WebJobsVs](https://visualstudiogallery.msdn.microsoft.com/f4824551-2660-4afa-aba1-1fcc1673c3d0) para Visual Studio ou o [Visual Studio 2013 atualização 4](http://www.visualstudio.com/news/vs2013-update4-rc-vs) (você pode criar e implantar com essa opção), usando o [SDK do Azure WebJobs](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md), ou copiá-los diretamente para os seguintes locais:
  - Para disparou execução: site/wwwroot/app_data/trabalhos/disparadas / {nome do trabalho}
  - Para execução contínua: site/wwwroot/app_data/trabalhos/contínua / {nome do trabalho}
- **Registro em log**: Console.Out será tratado (marcado) como informações. Console.Error será tratado como erro. Você pode acessar as informações de monitoramento e diagnóstico usando o portal do Azure. Você pode baixar arquivos de log diretamente do site. Eles são salvos nos seguintes locais:
  - Para disparou execução: Vnode/dados/trabalhos/disparadas/nome_do_trabalho
  - Para execução contínua: Vnode/dados/trabalhos/contínua/nome_do_trabalho
- **Configuração**: você pode configurar WebJobs usando o portal, API REST e PowerShell. Você pode usar um arquivo de configuração chamado settings.job na mesma pasta raiz como o script de trabalho para fornecer informações de configuração para um trabalho. Por exemplo:
  - {"stopping_wait_time": 60}
  - {"is_singleton": true}

### <a name="considerations"></a>Considerações

- Por padrão, escala de WebJobs com o web app. No entanto, você pode configurar trabalhos sejam executados em única instância definindo a propriedade de configuração de **is_singleton** como **true**. Única instância WebJobs são úteis para tarefas que você não deseja dimensionar ou executar como simultânea várias instâncias, como reindexação, análises de dados e tarefas semelhantes.
- Para minimizar o impacto de trabalhos no desempenho do aplicativo web, considere a criação de uma instância do Azure Web App vazia em um novo plano de serviço de aplicativo ao host WebJobs que pode ser longo executando ou recurso intenso.

### <a name="more-information"></a>Mais informações

- [Azure WebJobs recomendados recursos](./app-service-web/websites-webjobs-resources.md) lista os muitos recursos úteis, downloads e exemplos para WebJobs.

## <a name="azure-cloud-services-web-and-worker-roles"></a>Funções de trabalho e da web de serviços de nuvem Azure

Você pode executar tarefas em segundo plano dentro de uma função web ou em uma função de trabalho separada. Quando decidir usar uma função de trabalho, considere escalabilidade e requisitos de elasticidade, ciclo de vida de tarefa, solte cadência, segurança, tolerância, disputa, complexidade e arquitetura lógica. Para obter mais informações, consulte [Calcular padrão de consolidação de recurso](http://msdn.microsoft.com/library/dn589778.aspx).

Há várias maneiras de implementar tarefas de plano de fundo dentro de uma função de serviços de nuvem:

- Criar uma implementação da classe **RoleEntryPoint** na função e usar seus métodos para executar tarefas em segundo plano. As tarefas executadas no contexto de WaIISHost.exe. Ele podem usar o método de **GetSetting** da classe **CloudConfigurationManager** para carregar configurações. Para obter mais informações, consulte [ciclo de vida (serviços de nuvem)](#lifecycle-cloud-services).
- Use tarefas de inicialização para executar tarefas em segundo plano quando o aplicativo é iniciado. Para forçar as tarefas para continuar a executar em segundo plano, defina a propriedade **taskType** ao **plano de fundo** (se você não fizer isso, o processo de inicialização do aplicativo será interromper e aguarde a conclusão da tarefa). Para obter mais informações, consulte a [executar tarefas de inicialização no Azure](./cloud-services/cloud-services-startup-tasks.md).
- Use o SDK WebJobs implementar tarefas de plano de fundo como WebJobs iniciadas como uma tarefa de inicialização. Para obter mais informações, consulte [Introdução ao SDK do WebJobs do Azure](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md).
- Use uma tarefa de inicialização para instalar um serviço do Windows que executa uma ou mais tarefas de plano de fundo. Você deve definir a propriedade **taskType** ao **plano de fundo** para que o serviço é executado em segundo plano. Para obter mais informações, consulte a [executar tarefas de inicialização no Azure](./cloud-services/cloud-services-startup-tasks.md).

### <a name="running-background-tasks-in-the-web-role"></a>Executando tarefas em segundo plano na função da web

A vantagem principal de executar tarefas em segundo plano na função da web é o salvamento em custos de hospedagem porque há nenhuma exigência para implantar funções adicionais.

### <a name="running-background-tasks-in-a-worker-role"></a>Executando tarefas em segundo plano em uma função de trabalho

Executando tarefas em segundo plano em uma função de trabalho tem várias vantagens:

- Ele permite que você gerencie o dimensionamento separadamente para cada tipo de função. Por exemplo, talvez seja necessário mais instâncias de uma função da web para dar suporte a carga atual, mas menos instâncias da função trabalhador que executa tarefas em segundo plano. Dimensionamento instâncias de computação de tarefa de plano de fundo separadamente das funções de interface do usuário, você pode reduzir os custos de hospedagem, mantendo o desempenho aceitável.
- Ele alivia a sobrecarga de processamento de tarefas de plano de fundo da função web. A função de web que fornece a interface do usuário pode permanecer responde e talvez significa que menos instâncias são necessários para dar suporte a um determinado volume de solicitações de usuários.
- Ele permite que você implemente separação de preocupações. Cada tipo de função pode implementar um conjunto específico de tarefas claramente definidas e relacionados. Isso torna a criação e manutenção o código mais fácil porque há menos interdependência de código e funcionalidade entre cada função.
- Isso pode ajudar a isolar confidenciais processos e dados. Por exemplo, funções da web que implementam a interface do usuário não é necessário ter acesso aos dados que são gerenciados e controlados por uma função de trabalho. Isso pode ser útil em reforçar segurança, especialmente quando você usa um padrão como o [Padrão de Gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx).  

### <a name="considerations"></a>Considerações

Considere os seguintes pontos ao escolher como e onde implantar tarefas em segundo plano usando funções de trabalho e da web de serviços de nuvem:

- Tarefas de plano de fundo em uma função da web existente de hospedagem pode salvar o custo da execução de uma função de trabalho separada apenas para essas tarefas. Entretanto, é provável que afetam o desempenho e a disponibilidade do aplicativo, se houver disputa por processamento e outros recursos. Usar uma função de trabalho separada protege a função web contra o impacto das tarefas de plano de fundo demorada ou recursos.
- Se você hospedar tarefas em segundo plano usando a classe de **RoleEntryPoint** , você pode mover isso facilmente a outra função. Por exemplo, se você cria a classe em uma função da web e decide posteriormente que você precisa executar as tarefas em uma função de trabalho, você pode mover a implementação da classe **RoleEntryPoint** para a função de trabalho.
- Tarefas de inicialização foram projetadas para executar um programa ou um script. Implantar um trabalho de plano de fundo como um programa executável pode ser mais difícil, especialmente se ele também requer implantação de conjuntos de módulos dependentes. Talvez seja mais fácil de implantar e usar um script para definir um trabalho de plano de fundo ao usar tarefas de inicialização.
- Exceções que causam uma tarefa de plano de fundo falha tem um impacto diferente, dependendo da maneira que eles são hospedados:
  - Se você usar a abordagem de classe **RoleEntryPoint** , uma tarefa com falha fará com que a função reiniciar para que a tarefa é reiniciado automaticamente. Isso pode afetar a disponibilidade do aplicativo. Para evitar isso, certifique-se de que você inclua exceção robusta manipulando dentro da classe **RoleEntryPoint** e todas as tarefas de plano de fundo. Usar o código para reiniciar tarefas que falham onde isso é apropriado e lançar a exceção para reiniciar a função somente se você normalmente não é possível recuperar da falha dentro do seu código.
  - Se você usar tarefas de inicialização, você é responsável por gerenciar a execução da tarefa e verificar se ele falhar.
- Gerenciando e monitorando tarefas de inicialização são mais difícil que usando a abordagem de classe **RoleEntryPoint** . Entretanto, o SDK do Azure WebJobs inclui um painel para tornar mais fácil gerenciar WebJobs que você iniciar por meio de tarefas de inicialização.

### <a name="more-information"></a>Mais informações

- [Calcular padrão de consolidação de recurso](http://msdn.microsoft.com/library/dn589778.aspx)
- [Começar a usar o SDK do Azure WebJobs](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)

## <a name="azure-virtual-machines"></a>Azure máquinas virtuais

Tarefas de plano de fundo podem ser implementadas de uma maneira que impede que está sendo implantado para aplicativos Web do Azure ou serviços de nuvem ou essas opções podem não estar convenientes. Exemplos típicos são serviços do Windows e utilitários de terceiros e programas executáveis. Outro exemplo pode ser programas escritos para um ambiente de execução que é diferente do que hospeda o aplicativo. Por exemplo, talvez seja um programa Unix ou Linux que você deseja executar a partir de um aplicativo Windows ou .NET. Você pode escolher entre uma variedade de sistemas operacionais para uma máquina virtual Azure e executar o serviço ou executável na máquina virtual.

Para ajudá-lo a escolher quando usar máquinas virtuais, consulte [Serviços de aplicativo do Azure, serviços de nuvem e comparação de máquinas virtuais](./app-service-web/choose-web-site-cloud-service-vm.md). Para obter informações sobre as opções para máquinas virtuais, consulte [tamanhos de máquina Virtual e serviço de nuvem do Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Para obter mais informações sobre os sistemas operacionais e pré-criadas imagens que estão disponíveis para máquinas virtuais, consulte o [Marketplace de máquinas virtuais do Azure](https://azure.microsoft.com/gallery/virtual-machines/).

Para iniciar a tarefa de plano de fundo em uma máquina virtual separada, você tem uma variedade de opções:

- Você pode executar a tarefa sob demanda diretamente no seu aplicativo enviando uma solicitação para um ponto de extremidade que expõe a tarefa. Isso passa em quaisquer dados que requer a tarefa. Neste ponto de extremidade invoca a tarefa.
- Você pode configurar a tarefa para executar em um cronograma usando um agendador ou timer que está disponível no seu sistema operacional escolhido. Por exemplo, no Windows, você pode usar o Agendador de tarefas do Windows para executar scripts e tarefas. Ou, se você tiver instalado na máquina virtual do SQL Server, você pode usar o SQL Server Agent para executar scripts e tarefas.
- Você pode usar o Agendador do Azure para iniciar a tarefa, adicionando uma mensagem para uma fila que atende a tarefa ou enviando uma solicitação para uma API que expõe a tarefa.

Consulte a seção anterior [disparadores](#triggers) para obter mais informações sobre como você pode iniciar tarefas em segundo plano.  

### <a name="considerations"></a>Considerações

Considere os seguintes pontos ao decidir se deseja implantar tarefas de plano de fundo em uma máquina virtual Azure:

- Tarefas de plano de fundo em uma outra máquina virtual Azure de hospedagem fornece flexibilidade e permite o controle preciso sobre inicialização, execução, agendamento e alocação de recursos. No entanto, ele aumentará o custo de tempo de execução se uma máquina virtual deve ser implantada apenas para executar tarefas em segundo plano.
- Há um recurso para monitorar as tarefas no portal do Azure e nenhum recurso de reinicialização automatizada para tarefas falhas – Embora você possa monitorar o status básico da máquina virtual e gerenciá-lo usando os [Cmdlets de gerenciamento de serviço do Azure](http://msdn.microsoft.com/library/azure/dn495240.aspx). No entanto, não há nenhum recursos para controlar processos e segmentos em nós de computação. Normalmente, usar uma máquina virtual exigem esforço adicional para implementar um mecanismo de coleta de dados de instrumentação na tarefa e de sistema operacional na máquina virtual. Uma solução que pode ser apropriada é usar o [Pacote de gerenciamento de centro de sistema para o Azure](http://technet.microsoft.com/library/gg276383.aspx).
- Você pode considerar a criação de monitoramento testes que são expostos por meio de pontos de extremidade HTTP. O código para esses testes pode executar verificações de integridade, coletar informações operacionais e estatísticas – ou agrupar informações de erro e retorná-lo para um aplicativo de gerenciamento. Para obter mais informações, consulte [Padrão de monitoramento de ponto de extremidade de integridade](http://msdn.microsoft.com/library/dn589789.aspx).

### <a name="more-information"></a>Mais informações

- [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) no Azure
- [Perguntas Frequentes do Azure máquinas virtuais](virtual-machines/virtual-machines-linux-classic-faq.md)

## <a name="design-considerations"></a>Considerações de design

Há vários fatores fundamentais a considerar ao criar tarefas em segundo plano. As seções a seguir discutem partição, conflitos e coordenação.

## <a name="partitioning"></a>Partição

Se você decidir incluir tarefas de plano de fundo dentro de uma instância de computação existente (como um aplicativo web, função web, função de trabalho existente ou máquina virtual), você deve considerar como isso afeta os atributos de qualidade da instância computação e a tarefa de plano de fundo em si. Esses fatores ajudarão você a decidir se deseja colocar as tarefas com a instância de computação existente ou separá-las em uma instância de computação separada:

- **Disponibilidade**: tarefas em segundo plano podem não precisam ter o mesmo nível de disponibilidade como outras partes do aplicativo, em particular, a interface do usuário e outras partes diretamente envolvidas na interação do usuário. Tarefas de plano de fundo podem ser mais tolerância a latência, falhas de conexão repetidos, e outros fatores que afetam disponibilidade porque as operações podem ser enfileiradas. No entanto, deve haver capacidade suficiente para impedir que o backup de solicitações que poderia bloquear filas e afetam o aplicativo como um todo.
- **Escalabilidade**: tarefas em segundo plano serão prováveis que um requisito de escalabilidade diferente que a interface do usuário e as partes interativas do aplicativo. Dimensionamento da interface do usuário pode ser necessário para atender aos picos de demanda, enquanto tarefas pendentes em segundo plano podem ser concluídas menos horários pelo menos número de instâncias de computação.
- **Resiliência**: falha de uma instância de computação que hospeda apenas tarefas em segundo plano pode não fatalmente afetam o aplicativo como um todo se as solicitações para essas tarefas podem ser enfileiradas ou adiadas até que a tarefa está disponível novamente. Se a instância de computação e/ou tarefas podem ser reiniciadas dentro de um intervalo apropriado, os usuários do aplicativo não podem ser afetados.
- **Segurança**: tarefas em segundo plano podem ter requisitos de segurança diferentes ou restrições de interface do usuário ou outras partes do aplicativo. Usando uma instância de computação separada, você pode especificar um ambiente de segurança diferente para as tarefas. Você também pode usar os padrões como Gatekeeper para isolar as instâncias de computação de plano de fundo da interface para maximizar a segurança e separação.
- **Desempenho**: você pode escolher o tipo da instância de computação para tarefas em segundo plano para correspondência especificamente os requisitos de desempenho das tarefas. Isso significa usando uma opção de computação mais econômica se as tarefas não exigem os mesmos recursos de processamento que a interface do usuário ou uma instância maior se eles exigem recursos e capacidade adicional.
- **Capacidade de gerenciamento**: tarefas em segundo plano podem ter um ritmo diferente de desenvolvimento e implantação do código do aplicativo principal ou a interface do usuário. Implantá-los para uma instância de computação separada pode simplificar atualizações e controle de versão.
- **Custo**: adicionando calcular instâncias para executar o plano de fundo tarefas aumenta os custos de hospedagem. Você deve considerar cuidadosamente a troca entre capacidade adicional e esses custos extras.

Para obter mais informações, consulte [Padrão de eleição de preenchimento](http://msdn.microsoft.com/library/dn568104.aspx) e [Padrão de consumidores que competem](http://msdn.microsoft.com/library/dn568101.aspx).

## <a name="conflicts"></a>Conflitos

Se você tiver várias instâncias de um trabalho de plano de fundo, é possível que eles irão competir pelo acesso aos recursos e serviços, como bancos de dados e armazenamento. Esse acesso simultâneo pode resultar em disputa de recursos, que pode causar conflitos na disponibilidade dos serviços e na integridade dos dados no armazenamento. Você pode resolver disputa de recursos usando uma abordagem de bloqueio pessimista. Isso impede que competem instâncias de uma tarefa de simultaneamente acessando um serviço ou danificar dados.

Outra abordagem para resolver conflitos é definir tarefas em segundo plano como um singleton, para que haja apenas nunca uma instância em execução. No entanto, isso elimina os benefícios de confiabilidade e o desempenho que fornece uma configuração de várias instâncias. Isso é especialmente verdadeiro se a interface do usuário pode fornecer trabalho suficiente para manter mais de uma tarefa de plano de fundo ocupado.

É fundamental para garantir que a tarefa de plano de fundo pode reiniciado automaticamente e que ele possui capacidade suficiente para lidar com picos em demanda. Você pode obter isso por alocar uma instância de computação com recursos suficientes, Implementando um mecanismo de fila que pode armazenar solicitações para execução posterior quando demanda diminui ou usando uma combinação dessas técnicas.

## <a name="coordination"></a>Coordenação

As tarefas de plano de fundo podem ser complexas e podem exigir várias tarefas individuais para executar para produzir um resultado ou fulfil todos os requisitos. É comum nesses cenários para dividir a tarefa em menores discretas etapas ou subtarefas que podem ser executadas por vários consumidores. Várias etapas trabalhos podem ser mais eficiente e mais flexível porque etapas individuais podem ser reutilizáveis em vários trabalhos. Também é fácil adicionar, remover ou modificar a ordem das etapas.

Coordenar várias tarefas e etapas pode ser desafiador, mas há três padrões comuns que você pode usar para orientar a implementação de uma solução:

- **Decomposição de uma tarefa em várias etapas reutilizáveis**. Um aplicativo pode ser necessário para executar uma variedade de tarefas de complexidade variável nas informações que ele processa. Uma abordagem simples, mas inflexível para implementar este aplicativo pode ser executar esse processamento como um módulo monolítico. No entanto, essa abordagem é provavelmente a reduzir as oportunidades para refatorar o código, otimizá-lo ou reutilizá-la se partes do mesmo processamento forem necessários em outro lugar dentro do aplicativo. Para obter mais informações, consulte [Pipes e filtros padrão](http://msdn.microsoft.com/library/dn568100.aspx).
- **Gerenciando a execução das etapas de uma tarefa**. Um aplicativo pode realizar tarefas que compõem um número de etapas (alguns dos quais podem chamar serviços remotos ou acessar recursos remotos). As etapas individuais podem ser independentes entre si, mas eles são organizados pela lógica do aplicativo que implementa a tarefa. Para obter mais informações, consulte [Agendador agente Supervisor padrão](http://msdn.microsoft.com/library/dn589780.aspx).
- **Gerenciamento de recuperação para as etapas da tarefa que falham**. Um aplicativo pode precisar desfazer o trabalho realizado por uma série de etapas (que juntos definir uma operação eventualmente consistente) se uma ou mais das etapas falharem. Para obter mais informações, consulte [Compensating transação padrão](http://msdn.microsoft.com/library/dn589804.aspx).

## <a name="lifecycle-cloud-services"></a>Ciclo de vida (serviços de nuvem)

 Se você decidir implementar trabalhos de plano de fundo para aplicativos de serviços de nuvem que usam funções da web e trabalhador usando a classe de **RoleEntryPoint** , é importante entender o ciclo de vida desta classe para usá-lo corretamente.

Funções da Web e trabalhador passam por um conjunto de fases distintas conforme eles Iniciar, executam e parem. A classe **RoleEntryPoint** expõe uma série de eventos que indicam quando esses estágios estão ocorrendo. Você usá-los para inicializar, executar e parar de suas tarefas de plano de fundo personalizado. O ciclo completo é:

- Azure carrega o assembly de função e ele procura uma classe que deriva de **RoleEntryPoint**.
- Se encontrar essa classe, ele chama **RoleEntryPoint.OnStart()**. Você substitui esse método para inicializar suas tarefas de plano de fundo.
- Após o método **OnStart** , chamadas Azure **Application_Start()** no arquivo Global do aplicativo se este for apresentam (por exemplo, asax em uma função de web executando o ASP.NET).
- Azure chamadas **RoleEntryPoint.Run()** em um novo thread de primeiro plano que é executado em paralelo com **OnStart()**. Você substitui esse método para iniciar as tarefas de plano de fundo.
- Quando o método de execução encerrado, o Azure primeiro chama **Application_End()** no arquivo Global do aplicativo se isso estiver presente e, em seguida, chama **RoleEntryPoint.OnStop()**. Substituir o método **OnStop** para parar de suas tarefas de plano de fundo, limpar recursos, descartar objetos e fechar conexões que as tarefas podem ter usado.
- O processo de host de função do Azure operador for interrompido. Neste ponto, a função será reciclada e será reiniciado.

Para obter mais detalhes e um exemplo de como usar os métodos da classe **RoleEntryPoint** , consulte [Calcular padrão de consolidação de recurso](http://msdn.microsoft.com/library/dn589778.aspx).

## <a name="considerations"></a>Considerações

Considere os seguintes pontos quando você estiver planejando como você executará tarefas em segundo plano em uma função da web ou de trabalho:

- A implementação padrão **Executar** método na classe **RoleEntryPoint** contém uma chamada para **Thread.Sleep(Timeout.Infinite)** que mantém a função de funcionamento indefinidamente. Se você substituir o método **execute** (que é geralmente necessário para executar tarefas em segundo plano), você não deve permitir seu código sair do método, a menos que deseje Lixeira a instância de função.
- Uma implementação típica do método **Executar** inclui código para iniciar cada uma das tarefas em segundo plano e uma construção de loop que verifica periodicamente o estado de todas as tarefas de plano de fundo. Ele pode reiniciar qualquer que falham ou monitorar os tokens de cancelamento que indicam que concluiu trabalhos.
- Se uma tarefa de plano de fundo gera uma exceção não tratada, essa tarefa deve ser reciclada permitindo quaisquer outras tarefas de plano de fundo na função continuar sendo executado. No entanto, se a exceção é causada por corrupção de objetos fora da tarefa, como o armazenamento compartilhado, a exceção deve ser tratada pelo sua classe **RoleEntryPoint** , todas as tarefas devem ser canceladas e o método **Executar** deve ser permitido para encerrar. Azure reinicie a função.
- Use o método **OnStop** pausar ou eliminar tarefas em segundo plano e limpar recursos. Isso pode envolver Interrompendo tarefas demorada ou várias etapas. É fundamental levar em consideração como isso pode ser feito para evitar inconsistências de dados. Se uma instância da função parar por algum motivo diferente de um desligamento iniciado pelo usuário, o código em execução no método **OnStop** deve ser concluído dentro de cinco minutos antes que ele é encerrado. Certifique-se de que seu código pode ser concluído no momento ou pode suportar não executando para conclusão.  
- O balanceador de carga Azure inicia direciona o tráfego para a instância de função quando o método **RoleEntryPoint.OnStart** retorna o valor **true**. Portanto, considere a possibilidade de colocar todo o código de inicialização no método **OnStart** para que instâncias de função que não inicializar com êxito não receberá qualquer tráfego.
- Você pode usar tarefas de inicialização além os métodos da classe **RoleEntryPoint** . Você deve usar tarefas de inicialização ao inicializar quaisquer configurações que você precisa alterar no balanceador de carga Azure porque essas tarefas serão executadas antes da função recebe qualquer solicitações. Para obter mais informações, consulte a [executar tarefas de inicialização no Azure](./cloud-services/cloud-services-startup-tasks.md).
- Se houver um erro em uma tarefa de inicialização, ele pode forçar a função reiniciado continuamente. Isso pode impedir que você execute uma troca de endereço IP (VIP) virtual para uma versão anteriormente em estágios porque a troca requer acesso exclusivo à função. Isso não pode ser obtido enquanto a função estiver reiniciando. Para resolver esse problema:
    -  Adicione o seguinte código para o início dos métodos **OnStart** e **Executar** na sua função:

    ```C#
    var freeze = CloudConfigurationManager.GetSetting("Freeze");
    if (freeze != null)
    {
        if (Boolean.Parse(freeze))
        {
            Thread.Sleep(System.Threading.Timeout.Infinite);
        }
    }
    ```

   - Adicione a definição da configuração **Congelar** como um valor booliano ao ServiceDefinition.csdef e ServiceConfiguration. *.cscfg arquivos para a função e defini-lo* *Falso* *. Se a função entra em um modo de reiniciar repetidos, você poderá alterar a configuração para * *verdadeiro** congelar a execução de função e permitir a ser trocado com uma versão anterior.

## <a name="resiliency-considerations"></a>Considerações sobre resiliência

Tarefas em segundo plano devem ser flexíveis para fornecer serviços confiáveis para o aplicativo. Quando você estiver planejando e criando tarefas em segundo plano, considere os seguintes pontos:

- Tarefas em segundo plano devem ser capazes de lidar com reiniciar serviço ou função sem danificar dados ou apresentando inconsistência no aplicativo. Tarefas de execução demorada ou várias etapas, considere usar _Verificar apontando_ salvando o estado de trabalhos em armazenamento persistente ou como mensagens em uma fila se isso for apropriado. Por exemplo, você pode manter informações de estado em uma mensagem em uma fila e atualizar de forma incremental essas informações de estado com o andamento da tarefa para que a tarefa pode ser processada da última conhecida boa verificação – em vez de reinicialização desde o começo. Ao usar filas de barramento de serviço do Azure, você pode usar sessões de mensagens para habilitar o cenário mesmo. Sessões permitem que você salvar e recuperar o estado de processamento do aplicativo usando os métodos [SetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) e [GetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx) . Para obter mais informações sobre como criar fluxos de trabalho e processos de várias etapas confiáveis, consulte [O Agendador do agente Supervisor padrão](http://msdn.microsoft.com/library/dn589780.aspx).
- Quando você usa funções da web ou de trabalho para hospedar várias tarefas de plano de fundo, design sua substituição do método **Executar** para monitorar para tarefas interrompidas ou falhas e reiniciá-los. Onde não é prático e você estiver usando uma função de trabalho, força a função trabalhador reiniciar saindo do método **Executar** .
- Quando você usa filas para se comunicar com tarefas de plano de fundo, as filas podem atuar como um buffer para armazenar solicitações que são enviadas para as tarefas enquanto o aplicativo está sob superiores a carga de costume. Isso permite que as tarefas para acompanhar a interface do usuário durante períodos menos ocupados. Isso também significa que a reciclagem a função não bloqueará a interface do usuário. Para obter mais informações, consulte [padrão de redistribuição de carga baseado em fila](http://msdn.microsoft.com/library/dn589783.aspx). Se algumas tarefas são mais importantes que outras pessoas, considere a possibilidade de implementar o [Padrão de fila de prioridade](http://msdn.microsoft.com/library/dn589794.aspx) para garantir que essas tarefas executadas antes de menos importantes.
- Tarefas de plano de fundo que são iniciadas por mensagens ou processo devem ser projetadas para tratar inconsistências, como as mensagens que chegam fora de ordem, mensagens que causam repetidamente um erro (conhecido como _mensagens suspeitas_) e mensagens que são entregues mais de uma vez. Considere o seguinte:
  - Mensagens que devem ser processados em uma ordem específica, tais como aqueles que alterar dados com base no valor de dados existente (por exemplo, adicionando um valor para um valor existente), não podem chegar na ordem original em que foram enviadas. Como alternativa, eles podem ser tratados por diferentes instâncias de uma tarefa de plano de fundo em uma ordem diferente devido a cargas de variáveis em cada instância. Mensagens que devem ser processadas em uma ordem específica devem incluir um número de sequência, chave ou algum outro indicador que tarefas em segundo plano podem usar para garantir que eles são processados na ordem correta. Se você estiver usando o barramento de serviço do Azure, você pode usar sessões de mensagens para garantir a ordem de entrega. Entretanto, é geralmente mais eficiente, sempre que possível, para projetar o processo para que a ordem de mensagem não é importante.
  - Normalmente, uma tarefa de plano de fundo será inspecionar mensagens na fila, que oculta-las temporariamente de outros consumidores de mensagem. Em seguida, exclui as mensagens depois que elas tenham sido processadas com êxito. Se uma tarefa de plano de fundo falha quando o processamento de uma mensagem, essa mensagem reaparecerá na fila depois que o tempo limite espiada expira. Ele será processado por outra instância da tarefa ou durante o próximo ciclo de processamento desta instância. Se a mensagem consistentemente causa um erro no consumidor, ele bloqueará a tarefa, fila e eventualmente o próprio aplicativo quando fila fica cheio. Portanto, é fundamental para detectar e remover mensagens suspeitas da fila. Se você estiver usando o barramento de serviço do Azure, mensagens que causam um erro podem ser movidas automaticamente ou manualmente a uma fila de inatividade associado.
  - Filas garantem ao _menos uma vez_ mecanismos de entrega, mas eles podem oferecer a mesma mensagem mais de uma vez. Além disso, se uma tarefa de plano de fundo falhar após o processamento de uma mensagem, mas antes de excluí-lo da fila, a mensagem serão disponibilizados para processamento novamente. Tarefas em segundo plano deverão ser idempotentes, o que significa que a mesma mensagem de processamento mais de uma vez não causará um erro ou inconsistência nos dados do aplicativo. Algumas operações naturalmente são idempotentes, como definir um valor armazenado para um novo valor específico. No entanto, operações como adicionando um valor para um valor armazenado existente sem verificar que o valor armazenado ainda é a mesma que quando a mensagem foi enviada originalmente causará inconsistências. Azure filas de barramento de serviço podem ser configuradas para remover automaticamente mensagens duplicadas.
  - Alguns sistemas de mensagens, como filas de armazenamento do Azure e barramento de serviço do Azure, suportam uma propriedade de contagem de de-queue que indica o número de vezes que uma mensagem foi lida da fila. Isso pode ser útil em manipulação de mensagens suspeitas e repetidas. Para obter mais informações, consulte [Introdução de mensagens assíncrona](http://msdn.microsoft.com/library/dn589781.aspx) e [Padrões de idempotência](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/).

## <a name="scaling-and-performance-considerations"></a>Considerações de dimensionamento e desempenho

Tarefas em segundo plano devem oferecer desempenho suficiente para garantir que eles não bloquear o aplicativo, ou causar inconsistências devido a operação atrasada quando o sistema está sob carga. Geralmente, o desempenho é aprimorado dimensionando as instâncias de computação que hospedam as tarefas de plano de fundo. Quando você estiver planejando e criando tarefas em segundo plano, considere os seguintes pontos ao redor de desempenho e escalabilidade:

- Suporta Azure autoscaling (dimensionamento e dimensionamento de volta ao) com base em demanda atual e carga – ou em um cronograma predefinido, para aplicativos Web, web de serviços de nuvem e funções de trabalho e máquinas virtuais hospedado implantações. Use esse recurso para garantir que o aplicativo como um todo tenha suficientes recursos de desempenho e minimizar os custos de tempo de execução.
- Onde tarefas em segundo plano tem um recurso de desempenho diferentes de outras partes de um aplicativo de serviços de nuvem (por exemplo, a interface do usuário ou componentes como camada de acesso a dados), as tarefas de plano de fundo juntos em uma função de trabalho separada de hospedagem permite a interface do usuário e o plano de fundo funções de tarefa para dimensionar de maneira independente para gerenciar a carga. Se várias tarefas de plano de fundo tiverem recursos de desempenho significativamente diferentes uns dos outros, considere dividindo-las em funções de trabalho separada e a escala cada tipo de função de maneira independente. Entretanto, observe que isso pode aumentar os custos de tempo de execução comparados a combinação de todas as tarefas em menos de funções.
- As funções de dimensionamento simplesmente pode não ser suficiente para evitar a perda de desempenho sob carga. Talvez você também precise dimensionar filas de armazenamento e outros recursos para impedir que um único ponto de gerais corrente de se tornar um gargalo de processamento. Além disso, considere outras limitações, como a taxa de transferência máxima de armazenamento e outros serviços que o aplicativo e as tarefas de plano de fundo dependem.
- Tarefas em segundo plano devem ser projetadas para dimensionar. Por exemplo, ele devem ser capaz de detectar dinamicamente o número de filas de armazenamento em uso para escutar ou enviar mensagens à fila apropriada.
- Por padrão, escala de WebJobs com seu instância associada do Azure Web Apps. No entanto, se você quiser um WebJob para executar como apenas uma única instância, você pode criar um arquivo de Settings.job que contém os dados JSON **{"is_singleton": true}**. Isso força Azure para executar apenas uma instância do WebJob, mesmo se houver várias instâncias do aplicativo web associado. Isso pode ser uma técnica útil para tarefas agendadas que devem ser executados como apenas uma única instância.

## <a name="related-patterns"></a>Padrões relacionados

- [Introdução de mensagens assíncrona](http://msdn.microsoft.com/library/dn589781.aspx)
- [Orientação de AutoScaling](http://msdn.microsoft.com/library/dn589774.aspx)
- [Compensating transação padrão](http://msdn.microsoft.com/library/dn589804.aspx)
- [Padrão de consumidores concorrência](http://msdn.microsoft.com/library/dn568101.aspx)
- [Calcular partição orientação](http://msdn.microsoft.com/library/dn589773.aspx)
- [Calcular padrão de consolidação de recurso](http://msdn.microsoft.com/library/dn589778.aspx)
- [Padrão de gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx)
- [Padrão de eleição do líder](http://msdn.microsoft.com/library/dn568104.aspx)
- [Pipes e filtros padrão](http://msdn.microsoft.com/library/dn568100.aspx)
- [Padrão de fila de prioridade](http://msdn.microsoft.com/library/dn589794.aspx)
- [Carga baseado em fila nivelamento padrão](http://msdn.microsoft.com/library/dn589783.aspx)
- [Agendador agente Supervisor padrão](http://msdn.microsoft.com/library/dn589780.aspx)

## <a name="more-information"></a>Mais informações

- [Dimensionamento de aplicativos do Azure com funções de trabalho](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
- [Executar tarefas em segundo plano](http://msdn.microsoft.com/library/ff803365.aspx)
- [Ciclo de vida de inicialização de função Azure](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (postagem de blog)
- [Ciclo de vida do Azure Cloud Services função](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (vídeo)
- [Introdução ao SDK do Azure WebJobs](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
- [Azure filas e serviço barramento - comparados e comparados](./service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Como habilitar o diagnóstico em um serviço de nuvem](./cloud-services/cloud-services-dotnet-diagnostics.md)
