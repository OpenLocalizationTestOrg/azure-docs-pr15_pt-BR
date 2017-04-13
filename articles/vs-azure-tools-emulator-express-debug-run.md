<properties
   pageTitle="Usando o emulador Express para executar e depurar um serviço de nuvem em uma máquina local | Microsoft Azure"
   description="Usando o emulador Express para executar e depurar um serviço de nuvem em um computador local"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />


# <a name="using-emulator-express-to-run-and-debug-a-cloud-service-on-a-local-machine"></a>Usando o emulador Express para executar e depurar um serviço de nuvem em um computador local

Usando o emulador Express, você pode testar e depurar um serviço na nuvem sem executando o Visual Studio como administrador. Você pode definir as configurações do projeto para usar emulador Express ou emulador completo, dependendo dos requisitos do seu serviço de nuvem. Para saber mais sobre o emulador completo, consulte [executar um aplicativo do Azure no emulador calcular](./storage/storage-use-emulator.md). Emulador Express foi incluído primeiro no Azure SDK 2.1 e a partir do Azure SDK 2.3, ele é o emulador padrão.

## <a name="using-emulator-express-in-the-visual-studio-ide"></a>Usando o emulador Express no Visual Studio IDE

Quando você cria um novo projeto no Azure SDK 2.3 ou posterior, emulador Express já está selecionado. Projetos existentes que foram criados com uma versão anterior do SDK, siga estas etapas para selecionar emulador Express.

### <a name="to-configure-a-project-to-use-emulator-express"></a>Para configurar um projeto para usar o emulador Express

1. No menu de atalho para o projeto Azure, escolha **Propriedades**e escolha a guia **Web** .

1. Em **Servidor de desenvolvimento Local**, escolha o botão de **opção usar IIS Express** . Emulador Express não é compatível com o servidor Web do IIS.

1. Em **emulador**, escolha o botão de opção **Usar emulador Express** .

    ![Emulador Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## <a name="launching-emulator-express-at-a-command-prompt"></a>Iniciando emulador Express no prompt de comando

No prompt de comando, você poderá iniciar a versão expressa do Azure calcular emulador, csrun.exe, usando a opção /useemulatorexpress.

## <a name="limitations"></a>Limitações

Antes de usar emulador Express, você deve estar ciente de algumas limitações:

- Emulador Express não é compatível com o servidor Web do IIS.

- Seu serviço de nuvem pode conter várias funções, mas cada função está limitada a uma instância.

- Você não consegue acessar os números de porta abaixo 1000. Por exemplo, se você usar um provedor de autenticação que normalmente usa uma porta abaixo 1000, convém alterar esse valor para um número de porta que está acima de 1000.

- Quaisquer limitações que se aplicam ao emulador de computação do Azure também se aplicam ao emulador Express. Por exemplo, você não pode ter mais de 50 instâncias de função por implantação. Consulte [executar um aplicativo do Azure no emulador computação](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## <a name="next-steps"></a>Próximas etapas

[Depuração de serviços de nuvem](https://msdn.microsoft.com/library/azure/ee405479.aspx)
