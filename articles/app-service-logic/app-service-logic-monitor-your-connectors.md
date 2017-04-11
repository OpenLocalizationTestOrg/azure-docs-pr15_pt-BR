<properties
    pageTitle="Gerenciar e monitorar seus conectores e aplicativos de API no aplicativo de serviço | Microsoft Azure"
    description="Desempenho do modo de exibição de conectores e aplicativos de API nos aplicativos de lógica; arquitetura de microservices"
    services="app-service\logic"
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger"
    manager="anneta"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="mandia"/>

# <a name="manage-and-monitor-your-built-in-api-apps-and-connectors"></a>Gerenciar e monitorar seus aplicativos de API e conectores internos

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2014-12-01-visualização de aplicativos de lógica.

Você criou um App API interno. E agora?

No Azure, cada API App é um site separado hospedado no Azure. Como resultado, você pode ver facilmente quantas solicitações feitas e ver a quantidade de dados está sendo usado pelo conector. Você também pode backup sua API App, criar alertas, ativar a segurança de Tinfoil e adicionar usuários e funções.

Este tópico descreve algumas das diferentes opções para gerenciar sua API App.

Para ver esses recursos internos, abra o App API no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Se a API App estiver em sua startboard, selecione-o para abrir as propriedades. Você pode também selecionar **Procurar**, selecione **Aplicativos de API**e selecione sua API App:

![][browse]

## <a name="see-the-properties-you-entered"></a>Ver as propriedades que você inseriu

Quando você abre o App API, há vários recursos e tarefas disponíveis:

![][settings]

É possível:

- **Configurações** mostra informações específicas sobre a API App, incluindo detalhes da sua assinatura e lista os usuários que têm acesso ao seu aplicativo de API. Você também pode aumentar ou diminuir o número de instâncias do seu API App usando o recurso de escala; entre outros recursos.
- Use os botões de **Iniciar** e **Parar** para controlar a API App.
- Quando atualizações de produtos são feitas aos arquivos subjacentes usados pelo seu App API, você pode clicar em **Atualizar** para obter as versões mais recentes. Por exemplo, se houver uma correção ou uma atualização de segurança lançado pela Microsoft, clique em **Atualizar** automaticamente atualiza sua App API para incluir este fix.
- Selecione o **Plano de alteração** para atualização ou downgrade com base no uso de dados do App API. Você também pode usar esse recurso para ver seu uso de dados.
- Quando você cria um conector que possui tabelas, como o conector SQL, você pode inserir um nome de tabela para se conectar ao opcionalmente. Um esquema baseado na tabela é automaticamente criada e está disponível quando você clica em **Esquemas de Download**. Em seguida, você pode usar este esquema baixada para criar uma transformação ou um mapa.

## <a name="change-your-connector-or-api-configuration-values-you-entered"></a>Alterar seu conector ou valores de configuração de API inseridos

Depois de configurado ou criou o conector criado, você pode alterar os valores que você inseriu. Por exemplo, se você configurou o conector de SQL e você quiser alterar o nome do SQL Server ou o nome da tabela, você pode fazer isso na lâmina API App para o conector.

Etapas incluem:

1. Abra seu conector ou API App. Quando você fizer, a lâmina API App é aberta.
2. No **Essentials**, clique no hiperlink sob a propriedade de Host. O hiperlink é chamado algo como *slackconnector* ou *microsoftsqlconnector123*:

    ![][apiapphost]

3. Na lâmina API App Host, selecione **configurações**. Na lâmina configurações, selecione **Configurações de aplicativo**. Os valores de configuração são listados em **Configurações de aplicativo**:

    ![][hostsettings]

4. Clique na configuração que você deseja alterar, insira o novo valor e **Salve** suas alterações.


## <a name="install-the-hybrid-connection-manager---optional"></a>Instalar o Gerenciador de Conexão híbrido - opcional

![][hcsetup]

O Gerenciador de Conexão híbrido oferece a capacidade de se conectar a um sistema local, como o SQL Server ou SAP. Essa conectividade híbrido usa barramento de serviço do Azure para se conectar e controlar a segurança entre seus recursos Azure e seus recursos locais.

Consulte [usando o Gerenciador de Conexão de híbrido no serviço de aplicativo do Azure](app-service-logic-hybrid-connection-manager.md).

> [AZURE.NOTE] Gerenciador de Conexão de híbrida é necessário somente se você estiver se conectando a um recurso local por trás do firewall. Se você não estiver se conectando a um sistema local, o Gerenciador de Conexão híbrido podem não ser listado em seu lâmina de conector.

## <a name="monitor-the-performance"></a>Monitorar o desempenho
Métricas de desempenho são recursos internos e incluído com cada App API que você criar. Essas métricas são específicas para sua API App hospedado no Azure. Métricas de amostra:

![][monitoring]

É possível:

- Selecione **erros e as solicitações** para adicionar medições de desempenho diferentes, incluindo códigos de erro HTTP comumente conhecidos, como 200, 400 ou 500 códigos de status HTTP. Você também pode ver tempos de resposta, ver quantas solicitações são feitas a API App e ver a quantidade de dados vem e quanto aos dados sai. Com base em métricas de desempenho, você pode criar emails alertas se uma métrica exceder um limite de sua escolha.
- Em **uso**, você pode ver quanto **CPU** é usada pelo App API, examine a **Cota de uso** atual em MB e ver seu uso máxima de dados com base em seu nível de custo. **Estimada passam** pode ajudá-lo a determinar os custos potenciais da execução sua API App.
- Selecione **processos** para abrir o Explorer do processo. Isso mostra suas instâncias de web e suas propriedades, incluindo o uso de memória e a contagem de segmento.

Usando essas ferramentas, você pode determinar se o plano de serviço de aplicativo devem ser aumentado ou dimensionado para baixo, com base nas suas necessidades de negócios. Esses recursos estão internos portal as ferramentas adicionais necessárias.

## <a name="control-the-security"></a>Controlar a segurança

Aplicativos de API usar segurança baseada em função. Essas funções se aplicam a toda a experiência Azure, incluindo aplicativos de API e outros recursos Azure. As funções incluem:

Função | Descrição
--- | ---
Proprietário | Ter acesso total à experiência de gerenciamento e pode fornecer acesso a outros usuários ou grupos.
Colaborador | Têm acesso completo para a experiência de gerenciamento. Não é possível conceder acesso a outros usuários ou grupos.
Leitor | Pode exibir todos os recursos, exceto segredos.
Administrador de acesso do usuário | Pode exibir todos os recursos, criar/gerenciar funções e criar/gerenciar tíquetes de suporte.

Consulte [controle de acesso baseado em função no portal do Microsoft Azure](../active-directory/role-based-access-control-configure.md).

Você pode facilmente adicionar usuários e atribuí-las funções específicas a sua API App. O portal mostra os usuários que têm acesso e sua função atribuída:

![][access]  

- Selecione **os usuários** para adicionar um usuário, atribuir uma função e remover um usuário.
- Selecione **funções** para ver todos os usuários em uma função específica, adicione um usuário a uma função e remover um usuário de uma função.


## <a name="more-good-stuff"></a>Mais coisas boas
- Selecione a **definição de API** para abrir o arquivo de Swagger criado automaticamente para o aplicativo de API específico.
- Selecione **dependências** para exibir os arquivos requeridos pelo seu API App. Por exemplo, se você estiver usando o conector SAP, instalar alguns arquivos adicionais sobre o Gerenciador de Conexão do híbrido local. Essas dependências são mostradas na sua blade de aplicativo de API.

>[AZURE.IMPORTANT] Quando você abrir as propriedades de aplicativo de API e procure **Essentials**, há links de **Host** e **Gateway** que abrem blades novas:
>
> ![][host]
>
>Essas propriedades são específicas para o site que hospeda sua API App. Ao usar uma API App internos ou um conector, a maioria dessas propriedades realmente não aplica e recomendamos que você não atualize essas propriedades. Se você criou sua própria API App no Visual Studio e implantado para sua assinatura do Azure, você pode usar as lâminas de Host e o Gateway. <br/><br/>


>[AZURE.NOTE] Para começar com aplicativos de lógica antes de se inscrever para uma conta do Azure, vá para [Experimentar o aplicativo de lógica](https://tryappservice.azure.com/?appservice=logic). Você pode criar um aplicativo de lógica de curta duração starter. Não há compromissos e nenhum cartões de crédito obrigatórios.

## <a name="read-more"></a>Leia mais

[Monitore seus aplicativos de lógica](app-service-logic-monitor-your-logic-apps.md)<br/>
[Lista de aplicativos de API no aplicativo de serviço e conectores](app-service-logic-connectors-list.md)<br/>
[Controle de acesso baseado em função no portal do Microsoft Azure](../active-directory/role-based-access-control-configure.md)<br/>
[Usando o Gerenciador de Conexão de híbrido no serviço de aplicativo do Azure](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png
