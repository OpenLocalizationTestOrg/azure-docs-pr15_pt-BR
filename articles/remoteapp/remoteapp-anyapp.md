<properties
   pageTitle="Executar qualquer aplicativo do Windows em qualquer dispositivo com o Azure RemoteApp | Microsoft Azure"
   description="Aprenda a compartilhar qualquer aplicativo do Windows com seus usuários usando RemoteApp do Azure."
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="run-any-windows-app-on-any-device-with-azure-remoteapp"></a>Executar qualquer aplicativo do Windows em qualquer dispositivo com o Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Você pode executar um aplicativo do Windows em qualquer lugar em qualquer dispositivo, agora, sério - apenas usando RemoteApp do Azure. Se você tiver um aplicativo personalizado escrito 10 anos atrás, ou um aplicativo do Office, os usuários não tem mais seja vinculada a um sistema operacional específico (como o Windows XP) para alguns aplicativos.

Com o Azure RemoteApp, seus usuários também podem usar seus próprios dispositivos Android ou Apple e obter a mesma experiência que você tem, Windows (ou em telefones do Windows). Isso é feito por hospedar seu aplicativo do Windows em uma coleção de máquinas virtuais do Windows no Azure - os usuários possam acessá-los de qualquer lugar que eles têm uma conexão de internet. 

Leia mais para obter um exemplo de como fazer isso.

Neste artigo, vamos abordar a compartilhar o acesso com todos os seus usuários. No entanto, você pode usar qualquer aplicativo. Como você pode instalar o aplicativo em um computador Windows Server 2012 R2, você pode compartilhá-la usando as etapas abaixo. Você pode examinar os [requisitos do aplicativo](remoteapp-appreqs.md) para garantir que seu aplicativo funcionará.

Observe que porque o Access é um banco de dados e queremos ser úteis do banco de dados, podemos fará algumas etapas adicionais para que os usuários acessem o compartilhamento de dados do Access. Se seu aplicativo não é um banco de dados ou seus usuários possam acessar um compartilhamento de arquivo não é necessário, você pode pular essas etapas neste tutorial

> [AZURE.NOTE] <a name="note"></a>Você precisará de uma conta do Azure para concluir este tutorial:
> - Você pode [Abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F): obtenha créditos que você pode usar para experimentar serviços Azure pagos e até mesmo depois que eles são usados até você pode manter a conta e uso livre Azure serviços, como sites. Seu cartão de crédito nunca será cobrado, a menos que você explicitamente alterar suas configurações e peça para ser cobrados.
> - Você pode [Ativar benefícios do assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): assinatura MSDN Your lhe créditos todo mês que você pode usar para serviços do Azure pagos.


## <a name="create-a-collection-in-remoteapp"></a>Criar uma coleção no RemoteApp

Comece criando um conjunto. O conjunto serve como um contêiner de seus aplicativos e usuários. Cada coleção baseia-se em uma imagem, você pode criar seus próprios ou use um fornecido com sua assinatura. Para este tutorial, estamos usando a imagem de avaliação do Office 2013 - ele contém o aplicativo que você deseja compartilhar.

1. No portal do Azure, role para baixo na árvore de navegação à esquerda até ver RemoteApp. Abra a página.
2. Clique em **criar um conjunto de RemoteApp**.
3. Clique em **criar rápido** e insira um nome para o conjunto.
4. Selecione a região que você deseja usar para criar sua coleção. Para obter a melhor experiência, selecione a região mais próximo geograficamente até o local onde os usuários acessarão o aplicativo. Por exemplo, neste tutorial, os usuários estará localizados em Redmond, Washington. A região Azure mais próxima é **Oeste EUA**.
5. Selecione o plano de cobrança que você deseja usar. O plano de cobrança básico coloca 16 usuários em uma grande máquina virtual do Azure, enquanto o plano de faturamento padrão tem 10 usuários em uma grande VM do Azure. Como um exemplo geral, o plano básico funciona bem para fluxo de trabalho de tipo de entrada de dados. Para um aplicativo de produtividade, como o Office, você desejaria o plano padrão.
6. Por fim, selecione a imagem do Office 2013 Professional. Esta imagem contém aplicativos do Office 2013. Apenas um lembrete - esta imagem só é bom para conjuntos de avaliação e POCs. Você ' não pode usar essa imagem em uma coleção de produção.
7. Agora, clique em **criar RemoteApp conjunto**.

![Criar um conjunto de nuvem no RemoteApp](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

Isso inicia a criar seu conjunto, mas pode levar uma hora.

Agora você está pronto para adicionar usuários.

## <a name="share-the-app-with-users"></a>Compartilhar o aplicativo com usuários

Depois que seu conjunto tiver sido criado com êxito, é hora de publicar o acesso aos usuários e adicionar os usuários que precisam ter acesso a ele.

Se você tiver navegados afastando-o nó do Azure RemoteApp enquanto a coleção sendo criada, comece fazendo sua maneira voltar a ele na home page do Azure.

2. Clique na coleção criado anteriormente para acessar opções adicionais e configurar o conjunto.
![Um novo conjunto de nuvem RemoteApp](./media/remoteapp-anyapp/ra-anyappcollection.png)
3. Na guia **publicação** , clique em **Publicar** na parte inferior da tela e clique em **programas do menu Iniciar publicar**.
![Publicar um programa RemoteApp](./media/remoteapp-anyapp/ra-anyapppublish.png)
4. Selecione os aplicativos que você deseja publicar na lista. Para nosso objetivo, escolhemos acesso. Clique em **Concluir**. Aguarde os aplicativos concluir a publicação.
![Acesso a publicação no RemoteApp](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)


1. Quando o aplicativo tiver terminado publicação, cabeça sobre a guia **Acesso do usuário** para adicionar todos os usuários que precisam de acesso aos seus aplicativos. Insira os nomes de usuário (endereço de email) para seus usuários e clique em **Salvar**.

![Adicionar usuários ao RemoteApp](./media/remoteapp-anyapp/ra-anyappaddusers.png)


1. Agora, é hora de informar seus usuários sobre esses novos aplicativos e como acessá-los. Para fazer isso, envie aos usuários um email que apontem para o URL de download do cliente de área de trabalho remota.
![O cliente baixar URL para RemoteApp](./media/remoteapp-anyapp/ra-anyappurl.png)

## <a name="configure-access-to-access"></a>Configurar o acesso ao Access

Alguns aplicativos necessário configuração extra após implantá-los através de RemoteApp. Em particular, para obter acesso, vamos criar um compartilhamento de arquivo no Azure que qualquer usuário pode acessar. (Se você não quiser fazer isso, você pode criar uma [coleção de híbrido](remoteapp-create-hybrid-deployment.md) [em vez de nossa coleção de nuvem] que permite aos usuários acesso a arquivos e informações em sua rede local.) Em seguida, podemos precisará informar nossos usuários para mapear uma unidade local em seu computador para o sistema de arquivos Azure.

A primeira parte que é como administrador fazer. Em seguida, temos algumas etapas para seus usuários.

1. Iniciar publicando a interface de linha de comando (cmd.exe). Na guia **publicação** , selecione **cmd**e clique em **Publicar > Publicar programa usando caminho**.
2. Insira o nome do aplicativo e o caminho. Para nosso objetivo, use "File Explorer" como o nome e "% SYSTEMDRIVE%\windows\explorer.exe" como o caminho.
![Publica o arquivo cmd.exe.](./media/remoteapp-anyapp/ra-publishcmd.png)
3. Agora você precisa criar uma [conta de armazenamento](../storage/storage-create-storage-account.md)do Azure. Nomeamos o nosso "accessstorage", então escolha um nome que seja significativo para você. (Para misquote Highlander, pode haver apenas uma "accessstorage.") ![Conta de armazenamento do Azure nossa](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. Agora volte ao seu painel para que você possa acessar o caminho do seu armazenamento (local de ponto de extremidade). Você vai usar isso em um pouco, portanto, verifique se que você copiá-lo em outro.
![O caminho da conta de armazenamento](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5. Em seguida, depois que a conta de armazenamento tiver sido criada, é necessário a chave primária de acesso. Clique em **Gerenciar as teclas de acesso**e copie a chave primária de acesso.
6. Agora, defina o contexto da conta de armazenamento e crie um novo compartilhamento de arquivo para o Access. Execute os seguintes cmdlets em uma janela elevada do Windows PowerShell:

        $ctx=New-AzureStorageContext <account name> <account key>
        $s = New-AzureStorageShare <share name> -Context $ctx

    Portanto para nosso compartilhamento, estes são os cmdlets que podemos executar:

        $ctx=New-AzureStorageContext accessstorage <key>
        $s = New-AzureStorageShare <share name> -Context $ctx


Agora, é vez do usuário. Primeiro, peça aos usuários instalar um [cliente de RemoteApp](remoteapp-clients.md). Em seguida, os usuários necessário mapear uma unidade de sua conta para esse compartilhamento Azure que você criou e adicionar seus arquivos do Access. Isso é como fazer isso:

1. No cliente RemoteApp, acesse os aplicativos publicados. Inicie o programa de cmd.exe.
2. Execute o seguinte comando para mapear uma unidade de seu computador para o compartilhamento de arquivo:

        net use z: \\<accountname>.file.core.windows.net\<share name> /u:<user name> <account key>

    Se você definir o parâmetro **/ persistente** como Sim, a unidade mapeada irá persistir nas sessões.
1. Agora, inicie o aplicativo do Explorador de arquivos de RemoteApp. Copie os arquivos de acesso que você deseja usar no aplicativo compartilhado para o compartilhamento de arquivos.
![Inserção de arquivos do Access em um compartilhamento Azure](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
1. Finalmente, abra o Access e abra o banco de dados que você compartilhou. Você deve ver seus dados no Access em execução da nuvem.
![Um banco de dados do Access real executando da nuvem](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

Agora você pode usar o Access em qualquer um dos seus dispositivos - apenas certifique-se de que instalar um cliente RemoteApp.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

Agora que você dominar criando uma coleção, tente criar um [conjunto que usa o Office 365](remoteapp-tutorial-o365anywhere.md). Ou você pode criar uma [coleção de híbrido ](remoteapp-create-hybrid-deployment.md)que podem acessar sua rede local.

<!--Image references-->
 
