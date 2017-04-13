<properties
   pageTitle="Configurar um projeto de serviço de nuvem Azure com o Visual Studio | Microsoft Azure"
   description="Saiba como configurar um projeto de serviço de nuvem Azure no Visual Studio, dependendo de seus requisitos para o projeto."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Configurar um projeto de serviço de nuvem Azure com o Visual Studio

Você pode configurar um projeto de serviço de nuvem Azure, dependendo de seus requisitos para o projeto. Você pode definir as propriedades do projeto para as seguintes categorias:

- **Publicar um serviço de nuvem do Azure**

  Você pode definir uma propriedade para certificar-se de que um serviço de nuvem existente implantado Azure não é excluído acidentalmente.

- **Executar ou depurar um serviço de nuvem no computador local**

  Você pode selecionar uma configuração de serviço usar e indicar se você deseja iniciar o emulador de armazenamento do Azure.

- **Validar um pacote de serviço de nuvem quando ele é criado**

  Você pode decidir tratar avisos como erros, para que você pode certificar-se de que o pacote de serviço de nuvem implantará sem problemas. Isso reduz o tempo de espera se você implantar e, em seguida, descubra que ocorreu uma falha.

A ilustração a seguir mostra como selecionar uma configuração para usar quando você executar ou depurar seu serviço de nuvem localmente. Você pode definir qualquer uma das propriedades do projeto que exigem nesta janela, conforme mostrado na ilustração.

![Configurar um projeto do Microsoft Azure](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## <a name="to-configure-an-azure-cloud-service-project"></a>Para configurar um projeto de serviço de nuvem do Azure

1. Para configurar um projeto de serviço de nuvem do **Solution Explorer**, abra o menu de atalho para o projeto de serviço de nuvem e escolha **Propriedades**.

  Uma página com o nome do projeto de serviço de nuvem aparece no editor do Visual Studio.

1. Escolha a guia de **desenvolvimento** .

1. Para garantir que você não excluir acidentalmente uma implantação existente no Azure, a solicitação antes de excluir uma lista de distribuição existente, escolha **True**.

1. Para selecionar a configuração do serviço que você deseja usar quando você executar ou depurar seu serviço de nuvem localmente, na lista de **configuração do serviço** escolha a configuração do serviço.

  >[AZURE.NOTE] Se você deseja criar uma configuração de serviço usar, consulte como: gerenciar configurações de serviço e perfis. Se você deseja modificar uma configuração de serviço para uma função, consulte [como configurar as funções para um serviço de nuvem Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Para iniciar o emulador de armazenamento do Azure, quando você executar ou depurar seu serviço de nuvem localmente, no **emulador de armazenamento do Azure iniciar**, escolha **True**.

1. Para certificar-se de que você não pode publicar se há erros de validação de pacote, no **tratar avisos como erros**, escolha **True**.

1. Para garantir que sua função web usa a mesma porta toda vez que iniciar localmente no IIS Express, em **usar portas de projeto da web**, escolha **True**. Para usar uma porta específica em um projeto da web específico, abrir o menu de atalho para o projeto da web, escolha a guia **Propriedades** , escolha a guia da **Web** e alterar o número da porta na configuração da **Url do Project** na seção **IIS Express** . Por exemplo, insira `http://localhost:14020` como a URL do project.

1. Para salvar as alterações feitas nas propriedades do projeto de serviço de nuvem, escolha o botão **Salvar** na barra de ferramentas.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como configurar projetos de serviço de nuvem Azure no Visual Studio, consulte [Configurando o projeto do Azure usando várias configurações de serviço](vs-azure-tools-multiple-services-project-configurations.md).
