<properties 
   pageTitle="Usando a área de trabalho remota com funções Azure | Microsoft Azure"
   description="Usando a área de trabalho remota com funções Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="using-remote-desktop-with-azure-roles"></a>Usando a área de trabalho remota com funções Azure

Usando o SDK do Azure e os serviços de área de trabalho remota, você pode acessar funções do Azure e máquinas virtuais que são hospedadas por Azure. No Visual Studio, você pode configurar os serviços de área de trabalho remota de um projeto do Azure. Para ativar os serviços de área de trabalho remota, você deve criar um projeto de trabalho que contém uma ou mais funções e depois publicá-lo do Azure.

>[AZURE.IMPORTANT] Você deve acessar uma função Azure para solução de problemas ou desenvolvimento somente. A finalidade de cada máquina virtual é executar uma função específica em seu aplicativo do Azure, não para executar outros aplicativos cliente. Se desejar usar Azure para hospedar uma máquina virtual que você pode usar para qualquer finalidade, consulte Acessando máquinas virtuais do Azure do Server Explorer.

## <a name="to-enable-and-use-remote-desktop-for-an-azure-role"></a>Ativar e usar a área de trabalho remota para uma função do Azure

1. No Solution Explorer, abra o menu de atalho para o seu projeto e, em seguida, escolha **Publicar**.

    O Assistente de **Aplicativo do Azure publicar** aparece.

    ![Comando para um projeto de serviço de nuvem publicar](./media/vs-azure-tools-remote-desktop-roles/IC799161.png)

1. Na parte inferior da página de **Configurações de publicação do Microsoft Azure** do assistente, selecione **Habilitar a área de trabalho remota** para caixa de seleção todas as funções. 

    A caixa de diálogo de **Configuração de área de trabalho remota** é exibida.

1. Na parte inferior da caixa de diálogo **Configuração de área de trabalho remota** , escolha o botão **Mais opções** . 
 
    Isso exibe uma caixa de listagem suspensa que permite criar ou escolha um certificado para que você possa criptografar informações de credenciais ao conectar por meio de área de trabalho remota.

1. Na lista suspensa, escolha ** &lt;criar >**, ou escolha um existente na lista. 

    Se você escolher um certificado existente, ignore as etapas a seguir.

    >[AZURE.NOTE] Os certificados que você precisa para uma conexão de área de trabalho remota são diferentes dos certificados que você usa para outras operações Azure. O certificado de acesso remoto deve ter uma chave privada.

    Caixa de diálogo **Criar certificado** é exibida.

    1. Forneça um nome amigável para o novo certificado e, em seguida, escolha o botão de **Okey** . O novo certificado é exibida na caixa de listagem suspensa.

    1. Na caixa de diálogo **Configuração de área de trabalho remota** , forneça um nome de usuário e uma senha.
    
        Você não pode usar uma conta existente. Não especifique administrador como o nome de usuário para a nova conta.

        >[AZURE.NOTE] Se a senha não atender aos requisitos de complexidade, um ícone vermelho aparece ao lado da caixa de texto de senha. A senha deve incluir letras maiusculas, minúsculas e números ou símbolos.

    1. Escolha uma data em que a conta expirará e depois quais as conexões de área de trabalho remotas serão bloqueadas.

    1. Depois que você forneceu todas as informações necessárias, escolha o botão de **Okey** .
    
        Várias configurações que habilitar serviços de acesso remoto são adicionadas ao .cscfg e .csdef arquivos.

1. No Assistente de **Configurações de publicação do Microsoft Azure** , escolha o botão de **Okey** quando você estiver pronto para publicar seu serviço de nuvem.

    Se você não estiver pronto para publicar, escolha o botão **Cancelar** . As definições de configuração são salvos, e você pode publicar seu serviço de nuvem mais tarde.

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Conectar a uma função do Azure usando a área de trabalho remota

Depois de publicar seu serviço de nuvem no Azure, você pode usar Server Explorer para fazer logon em máquinas virtuais que hospeda Azure. 

1. No Server Explorer, expanda o nó do **Azure** e expanda o nó para um serviço de nuvem e uma das suas funções para exibir uma lista das instâncias.

1. Abrir o menu de atalho de um nó de instância e escolha **Conectar usando área de trabalho remota**.

    ![Conectando-se via área de trabalho remota](./media/vs-azure-tools-remote-desktop-roles/IC799162.png)

1. Insira o nome de usuário e senha que você criou anteriormente. Agora, você está conectado a sua sessão remota.


