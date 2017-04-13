<properties 
   pageTitle="Configurando e usando o emulador de armazenamento com o Visual Studio | Microsoft Azure"
   description="Configurando e usando o emulador de armazenamento com o Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="tarcher" />

# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Configurando e usando o emulador de armazenamento com o Visual Studio

[AZURE.INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Visão geral
Ambiente de desenvolvimento do Azure SDK inclui o emulador de armazenamento, um utilitário que simula os serviços de armazenamento Blob, fila e tabela disponíveis no Azure em sua máquina de desenvolvimento local. Se você estiver criando um serviço de nuvem que utiliza os serviços de armazenamento do Azure ou escrever qualquer aplicativo externo que chama os serviços de armazenamento, você pode testar seu código localmente contra emulador de armazenamento. As ferramentas do Azure para Microsoft Visual Studio integrar o gerenciamento de emulador de armazenamento no Visual Studio. As ferramentas do Azure inicializar o banco de dados de emulador de armazenamento na primeira utilização, inicia o serviço de armazenamento de emulador quando você executar ou depurar seu código do Visual Studio e fornece acesso somente leitura para os dados de emulador de armazenamento por meio do Gerenciador de armazenamento do Azure.

Para obter informações detalhadas sobre o emulador de armazenamento, incluindo os requisitos de sistema e instruções de configuração personalizada, consulte [usar o emulador de armazenamento do Azure para desenvolvimento e teste](./storage/storage-use-emulator.md).

>[AZURE.NOTE] Há algumas diferenças na funcionalidade entre a simulação de emulador de armazenamento e os serviços de armazenamento do Azure. Consulte [diferenças entre o emulador de armazenamento e serviços de armazenamento do Azure](./storage/storage-use-emulator.md) na documentação do SDK do Azure para obter informações sobre as diferenças.

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Configurando uma cadeia de conexão para o emulador de armazenamento

Para acessar o emulador de armazenamento do código dentro de uma função, você desejará configurar uma cadeia de conexão que aponta para o emulador de armazenamento e que podem ser alteradas posteriormente para apontar para uma conta de armazenamento do Azure. Uma cadeia de conexão é uma configuração que possa ler sua função no tempo de execução para se conectar a uma conta de armazenamento. Para obter mais informações sobre como criar cadeias de caracteres de conexão, consulte [Configurar o aplicativo do Azure](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

>[AZURE.NOTE] Você pode retornar uma referência para a conta de emulador de armazenamento do seu código usando a propriedade **DevelopmentStorageAccount** . Essa abordagem funciona corretamente se você deseja acessar o emulador de armazenamento do seu código, mas se você planeja publicar seu aplicativo no Azure, você precisará criar uma cadeia de conexão para acessar sua conta de armazenamento do Azure e modificar seu código para usar essa cadeia de caracteres de conexão antes de publicá-la. Se estiver mudando entre a conta de emulador de armazenamento e uma conta de armazenamento do Azure frequentemente, uma cadeia de conexão irá simplificar esse processo.

## <a name="initializing-and-running-the-storage-emulator"></a>Inicializar e executando o emulador de armazenamento

Você pode especificar que, quando você executa ou depurar o serviço no Visual Studio, Visual Studio automaticamente inicia o emulador de armazenamento. No Solution Explorer, abra o menu de atalho para o seu projeto do **Azure** e escolha **Propriedades**. Na guia **desenvolvimento** , na lista **Iniciar emulador de armazenamento do Azure** , escolha **True** (se ela já não estiver definida para esse valor).

A primeira vez que você executar ou depurar seu serviço do Visual Studio, o emulador de armazenamento inicia um processo de inicialização. Esse processo reserva portas locais para o emulador de armazenamento e cria o banco de dados de emulador de armazenamento. Após a conclusão, esse processo não precisa executar novamente, a menos que o banco de dados de emulador de armazenamento é excluído.

>[AZURE.NOTE] Começando com a versão de junho de 2012 das ferramentas do Azure, o emulador de armazenamento é executado, por padrão, no SQL Express LocalDB. Em versões anteriores das ferramentas do Azure, o emulador de armazenamento executa uma instância padrão do SQL Express 2005 ou 2008, que você deve instalar antes de você pode instalar o SDK do Azure. Você também pode executar o emulador de armazenamento em relação a uma instância nomeada do SQL Express ou um nomeado ou instância padrão do Microsoft SQL Server. Se você precisa configurar o emulador de armazenamento para executar em uma instância diferente a instância padrão, consulte [usar o emulador de armazenamento do Azure para desenvolvimento e teste](./storage/storage-use-emulator.md).

Emulador de armazenamento fornece uma interface de usuário para exibir o status dos serviços de armazenamento local e para iniciar, parar e redefini-los. Depois que o serviço de armazenamento de emulador foi iniciado, você pode exibir a interface do usuário ou iniciar ou interromper o serviço clicando no ícone da área de notificação para o Microsoft Azure Emulator na barra de tarefas do Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Exibindo dados de emulador de armazenamento no Server Explorer

O nó de armazenamento do Azure em Server Explorer permite exibir dados e alterar as configurações de dados blob e tabela em suas contas de armazenamento, incluindo o emulador de armazenamento. Para obter mais informações, consulte [Gerenciar recursos de armazenamento com Server Explorer e navegação](https://msdn.microsoft.com/library/azure/ff683677.aspx) .
