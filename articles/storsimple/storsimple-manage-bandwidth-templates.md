<properties
   pageTitle="Gerenciar seus modelos de largura de banda de StorSimple | Microsoft Azure"
   description="Descreve como gerenciar modelos de largura de banda de StorSimple, que permitem que você controle o consumo de largura de banda."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storsimple-bandwidth-templates"></a>Use o serviço do Gerenciador de StorSimple para gerenciar modelos de largura de banda de StorSimple

## <a name="overview"></a>Visão geral

Modelos de largura de banda permitem que você configure o uso de largura de banda de rede entre várias agendas de hora do dia para o nível os dados do dispositivo StorSimple na nuvem.

Com largura de banda agendas você pode:

- Especifica cronogramas de largura de banda personalizada dependendo os usos de rede de carga de trabalho.

- Centralizar o gerenciamento e reutilizar as agendas em vários dispositivos de forma fácil e perfeita.

> [AZURE.NOTE] Este recurso está disponível somente para dispositivos físicos StorSimple e não para dispositivos virtuais.

Todos os modelos de largura de banda para o serviço são exibidos em um formato tabular e contêm as seguintes informações:

- **Nome** – um nome exclusivo atribuído para o modelo de largura de banda quando ela foi criada.

- **Cronograma** – o número de agendamentos contidos em um modelo de largura de banda determinado.

- **Usado por** – o número de volumes usando os modelos de largura de banda.

Você pode usar a página de **configuração** do Gerenciador de StorSimple serviço no portal de clássico do Azure para gerenciar modelos de largura de banda.

Você também pode encontrar informações adicionais para ajudar a configurar modelos de largura de banda em:

- Perguntas e respostas sobre modelos de largura de banda
- Práticas recomendadas para modelos de largura de banda

## <a name="add-a-bandwidth-template"></a>Adicionar um modelo de largura de banda

Execute as seguintes etapas para criar um novo modelo de largura de banda.

#### <a name="to-add-a-bandwidth-template"></a>Para adicionar um modelo de largura de banda

1. Na página Gerenciador de StorSimple serviço **Configurar** , clique em **Adicionar/Editar modelo de largura de banda**.

2. Na caixa de diálogo **Add/Edit largura de banda modelo** :

   1. Na lista suspensa **modelo** , selecione **Criar novo** para adicionar um novo modelo de largura de banda.
   2. Especifique um nome exclusivo para o seu modelo de largura de banda.

3. Defina uma **agenda de largura de banda**. Para criar um cronograma:

   1. Na lista suspensa, escolha os dias da semana em que o agendamento está configurado para. Você pode selecionar vários dias marcando as caixas de seleção localizadas antes os respectivos dias na lista.
   2. Selecione a opção de **Dia inteiro** se o cronograma for imposto para o dia inteiro. Quando essa opção está marcada, você não pode especificar uma **Hora de início** ou uma **Hora de término**. O cronograma executa a partir de 12:00 AM para 11:59 PM.
   3. Na lista suspensa, selecione uma **Hora de início**. Isso é quando o cronograma começará.
   4. Na lista suspensa, selecione uma **Hora de término**. Isso é quando o cronograma será interrompida.

         > [AZURE.NOTE] Agendas sobrepostas não são permitidas. Se as horas de início e término resultará em um plano de sobreposição, você verá uma mensagem de erro para esse efeito.

   5. Especifique a **taxa de largura de banda**. Esta é a largura de banda em Megabits por segundo () usado pelo seu dispositivo de StorSimple em operações envolvendo a nuvem (uploads e downloads). Forneça um número entre 1 e 1.000 para esse campo.

   6. Clique no ícone de seleção ![ícone de seleção](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). O modelo que você criou será adicionado à lista de modelos de largura de banda na página de **configuração** do serviço.

    ![Criar novo modelo de largura de banda](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)

4. Clique em **Salvar** na parte inferior da página e, em seguida, clique em **Sim** quando solicitado para confirmação. Isso salvará as alterações de configuração feitas por você.

## <a name="edit-a-bandwidth-template"></a>Editar um modelo de largura de banda

Execute as seguintes etapas para editar um modelo de largura de banda.

### <a name="to-edit-a-bandwidth-template"></a>Para editar um modelo de largura de banda

1. Clique em **Adicionar/Editar modelo de largura de banda**.

2. Na caixa de diálogo **Add/Edit largura de banda modelo** :

   1. Na lista suspensa **modelo** , escolha um modelo de largura de banda existente que você deseja modificar.
   2. Conclua as alterações. (Você pode modificar as configurações existentes.)
   3. Clique no ícone de seleção ![Ícone de seleção](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Você verá o modelo modificado na lista de modelos de largura de banda na página de configuração do serviço.

3. Para salvar suas alterações, clique em **Salvar** na parte inferior da página. Clique em **Sim** quando solicitado para confirmação.

> [AZURE.NOTE] Você não poderá salvar suas alterações se o cronograma editado se sobrepõe a uma agenda existente no modelo de largura de banda que você está modificando.

## <a name="delete-a-bandwidth-template"></a>Excluir um modelo de largura de banda

Execute as seguintes etapas para excluir um modelo de largura de banda.

#### <a name="to-delete-a-bandwidth-template"></a>Para excluir um modelo de largura de banda

1. Na lista tabular dos modelos de largura de banda para o serviço, selecione o modelo que você deseja excluir. Um ícone Excluir (**x**) aparecerá à extrema direita do modelo selecionado. Clique no ícone **x** para excluir o modelo.

2. Você será solicitado para confirmação. Clique em **Okey** para continuar.

Se o modelo está em uso por qualquer volumes, você não poderá excluí-lo. Você verá uma mensagem de erro indicando que o modelo está em uso. Uma caixa de diálogo de mensagem de erro aparecerá indicando que você que devem ser removidas todas as referências para o modelo.

Você pode excluir todas as referências para o modelo acessando a página de **Contêineres de Volume** e modificando os contêineres de volume que usam este modelo para que ele usam outro modelo ou usam uma configuração de largura de banda personalizada ou ilimitado. Quando todas as referências foram removidas, você pode excluir o modelo.

## <a name="use-a-default-bandwidth-template"></a>Usar um modelo de largura de banda padrão

Um modelo de largura de banda padrão é fornecido e é usado por contêineres de volume por padrão para impor controles de largura de banda ao acessar a nuvem. O modelo padrão também serve como uma referência pronta para os usuários que criar seus próprios modelos. Os detalhes desse modelo padrão são:

- **Nome** – ilimitadas noites e fins de semana

- **Cronograma** – um único agendamento de segunda a sexta que se aplica a uma taxa de largura de banda de 1 Mbps entre 8 AM e PM 5 hora do dispositivo. A largura de banda está definida como ilimitado para o restante da semana.

O modelo padrão pode ser editado. O uso desse modelo (incluindo versões editadas) é controlado.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Criar um modelo de largura de banda de dia inteiro que começa em uma hora específica

Siga este procedimento para criar uma agenda que começa em uma hora específica e executa o dia inteiro. No exemplo, o cronograma começa em 9H de manhã e executa até 9 AM da manhã. É importante observar que os horários de início e término de um cronograma de determinado deve estar contido na mesma agenda 24 horas e não pode abranger vários dias. Se você precisar configurar modelos de largura de banda que se estendem por vários dias, você precisará usar várias agendas (como mostrado no exemplo).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Para criar um modelo de largura de banda de dia inteiro

1. Crie um agendamento que começa em 9H de manhã e executa até meia-noite.

2. Adicione outro agendamento. Configure o cronograma segundo para executar de meia-noite até 9 AM de manhã.

3. Salve o modelo de largura de banda.

O cronograma composto depois será iniciado por vez de sua escolha e executar o dia inteiro.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Perguntas e respostas sobre modelos de largura de banda

**Q**. O que acontece com controles de largura de banda quando você estiver entre os agendamentos? (Um cronograma foi encerrado e outro ainda não foi iniciado.)

**A**. Nesses casos, sem controles de largura de banda serão utilizados. Isso significa que o dispositivo pode usar largura de banda ilimitada quando a hierarquia de dados com a nuvem.

**Q**. Você pode modificar os modelos de largura de banda em um dispositivo off-line?

**A**. Você não poderá modificar os modelos de largura de banda em contêineres de volumes se o dispositivo correspondente está offline.

**Q**. Você pode editar um modelo de largura de banda associado a um contêiner de volume quando os volumes associados estiverem offline?

**A**. Você pode modificar um modelo de largura de banda associado a um contêiner de volume cujos volumes estão offline. Observe que quando volumes estiverem offline, nenhum dado será ser hierárquico do dispositivo para a nuvem.

**Q**. Você pode excluir um modelo padrão?

**A**. Embora seja possível excluir um modelo padrão, não é uma boa ideia para fazê-lo. O uso de um modelo padrão, incluindo versões editadas, é controlado. Os dados de controle são analisados e ao longo do tempo, são usados para melhorar o modelo padrão.

**Q**. Como você determinar que seus modelos de largura de banda precisam ser modificados?

**A**. Um dos sinais de que você precisa modificar os modelos de largura de banda é quando você começa a vendo a rede lenta ou restringirá várias vezes em um dia. Se isso acontecer, monitore a rede de armazenamento e o uso examinando os gráficos de desempenho e/s e transferência de rede.

A partir dos dados de transferência de rede, identifique a hora do dia e os contêineres de volume no qual o afunilamento de rede ocorre. Se isso acontece quando dados estão sendo hierárquico na nuvem (get essas informações de desempenho de e/s para todos os contêineres de volume para dispositivo nuvem), você precisará modificar os modelos de largura de banda associados aos seus contêineres de volume.

Depois que os modelos modificados estão em uso, você precisará monitorar a rede novamente para latências significativas. Se eles ainda existirem, você precisará rever seus modelos de largura de banda.

**Q**. O que acontece se tem vários contêineres de volume em meu dispositivo agenda que sobreposição mas diferentes limites se aplicam a cada?

**A**. Vamos supor que você tenha um dispositivo com 3 contêineres de volume. Sobreponham os agendamentos associados desses contêineres completamente. Para cada um desses contêineres, os limites de largura de banda usados são 5, 10 e 15 Mbps respectivamente. Quando saídas estão ocorrendo em todos esses contêineres ao mesmo tempo, o mínimo dos limites de largura de banda de 3 pode ser aplicado: nesse caso, como essas solicitações de i de saída de 5 Mbps compartilhar a mesma fila.

## <a name="best-practices-for-bandwidth-templates"></a>Práticas recomendadas para modelos de largura de banda

Siga estas práticas recomendadas para o seu dispositivo de StorSimple:

- Configure modelos de largura de banda em seu dispositivo para habilitar a variável otimização da taxa rede pelo dispositivo em momentos diferentes do dia. Esses modelos de largura de banda quando usada com agendas de backup podem aproveitar efetivamente largura de banda de rede adicionais para operações de nuvem durante o horário de pico.

- Calcule a largura de banda real necessária para uma determinada implantação com base no tamanho da implantação e o objetivo de tempo de recuperação necessários (RTO).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
