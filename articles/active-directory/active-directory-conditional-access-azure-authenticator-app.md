
<properties
    pageTitle="Azure autenticador para Android | Microsoft Azure"
    description="Aplicativo do Microsoft Azure autenticador pode ser usado para entrar para acessar os recursos de trabalho. O aplicativo do Azure autenticador notifica sobre uma solicitação de verificação de dois fatores pendente exibindo um alerta para seu dispositivo móvel."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="azure-authenticator-for-android"></a>Azure autenticador para Android

Seu administrador de TI pode ter recomendável usar o Microsoft Azure autenticador para entrar para acessar os recursos de trabalho. Este aplicativo oferece essas duas opções de entrada:

* Autenticação multifator permite que você proteja suas contas corporativas ou de Estudante com verificação em duas etapas. Você entrar usando algo que você conhece (por exemplo, sua senha) e proteger a conta ainda mais com algo que tenha (uma chave de segurança deste aplicativo). O aplicativo do Azure autenticador notifica sobre uma solicitação de verificação de dois fatores pendente exibindo um alerta para seu dispositivo móvel. Você precisa simplesmente exibir a solicitação do aplicativo e toque em confirmar ou Cancelar. Como alternativa, você pode ser solicitado a inserir a senha exibida no aplicativo.

* Conta corporativa permite transformar seu telefone ou tablet Android em um dispositivo confiável e forneça Single Sign-On (SSO) para aplicativos de empresa. O administrador de TI pode exigir que você adicionar uma conta de trabalho para acessar os recursos da empresa. SSO permite que você entrar uma vez e automaticamente avaliar de entrar todos os aplicativos de que sua empresa disponibilizou para você.

## <a name="installing-the-azure-authenticator-app"></a>Instalar o aplicativo de autenticador Azure

Você pode instalar o aplicativo do Azure autenticador Google Play Store.
As instruções para adicionar a conta corporativa do Samsung Android dispositivo vs Samsung Android dispositivo são ligeiramente diferentes. As instruções para ambos estão listadas abaixo.

<a name="adding-the-work-account-from-samsung-android-device"></a>Adicionar a conta de trabalho do dispositivo Samsung Android
----------------------------------------------------------------------------------------------------------------
###<a name="adding-the-work-account-through-the-app-home-screen"></a>Adicionar a conta de trabalho por meio da tela inicial do aplicativo

As instruções a seguir são aplicáveis a Samsung GS3 e acima telefones ou nota 2 e acima tablets.

1. Na tela inicial do aplicativo, aceite o contrato de licença de usuário final (EULA).
2. Na tela Ativar conta, clique no menu de contexto à direita e selecione **conta de trabalho**.
3. Na tela Adicionar conta, selecione** Conta de trabalho**.
4. Na tela de administrador do dispositivo de ativar, clique em **Ativar**.
5. Na tela de política de privacidade, marque a caixa de seleção e clique em **Confirmar**.
6. Na tela de participar do local de trabalho, insira a ID de usuário fornecido por sua organização e clique em **ingressar**.
7. Para entrar no aplicativo do Azure autenticador, insira seu organizacional um * * * conta e senha e clique em **entrar**.
8. A próxima tela que exibe informações sobre autenticação multifator (MFA) é para segurança adicional e é opcional. Você verá esta tela se seu trabalho ou escola requer autenticação de segundo fator para criar a conta de trabalho. Ele fornece instruções para verificar sua conta.
9. A tela ingressar no local de trabalho exibe a mensagem, "**ingressar em seu local de trabalho**". O aplicativo do Azure autenticador está tentando ingressar em seu dispositivo para seu local de trabalho.
10. Você verá a mensagem local de trabalho ingressou na próxima tela.

>[AZURE.NOTE]
> Você tem permissão uma conta de trabalho único em seu dispositivo.

### <a name="adding-the-work-account-from-the-settings-menu"></a>Adicionar a conta de trabalho no menu Configurações
Após ter instalado o aplicativo do Azure autenticador, você também pode criar uma conta de trabalho do Gerenciador de conta do Android.

1. No menu Configurações, navegue até **contas** e clique em **Adicionar conta**.
2. Siga as etapas de 3 a 10 do procedimento, adicionando a conta de trabalho por meio da tela inicial do aplicativo, para adicionar uma conta de trabalho.

<a name="adding-the-work-account-from-a-non-samsung-android-device"></a>Adicionar a conta de trabalho em um dispositivo sem Samsung Android
------------------------------------------------------------------------------------------------------------------
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Adicionar a conta de trabalho por meio da tela inicial do aplicativo

1. Na tela inicial do aplicativo, aceite o contrato de licença de usuário final (EULA).
2. Na tela Ativar conta, clique no menu de contexto à direita e selecione **conta de trabalho**.
3. Na tela contas, clique em **Adicionar conta**.
4. Se você vir a tela de contas, clique em **Adicionar conta**. Se uma conta de trabalho já é criada anteriormente, você verá uma tela de sincronização mostrando o existente conta corporativa. Você pode manter a conta de trabalho tocando simplesmente na seta para voltar para a tela inicial. Como alternativa, você pode selecionar a conta remover e recriar uma novo trabalho no local de trabalho ingressar tela conta, insira a ID de usuário fornecido por sua organização e clique em ingressar.
5. Para entrar no aplicativo do Azure autenticador, insira sua conta organizacional e senha e clique em **entrar**.
7. A próxima tela que exibe informações sobre autenticação multifator (MFA) é para segurança adicional e é opcional. Você verá esta tela se seu trabalho ou escola requer autenticação de segundo fator para criar a conta de trabalho. Ele fornece instruções para verificar sua conta.
8. Na tela seguinte, clique em **Okey** . Não altere o nome do certificado.
a mensagem, "Ingressar em seu local de trabalho". O aplicativo do Azure autenticador está tentando ingressar em seu dispositivo para seu local de trabalho.
Você verá a mensagem local de trabalho ingressou na próxima tela.

>[AZURE.NOTE]
> Você tem permissão uma conta de trabalho único em seu dispositivo.

Após ter instalado o aplicativo do Azure autenticador, você também pode criar uma conta de trabalho do Gerenciador de conta do Android.

1. No menu **configurações** , navegue até contas e clique em **Adicionar conta**.
2. Siga as etapas 2 a 7 do procedimento, adicionando a conta de trabalho por meio do aplicativo inicial tela * *, para adicionar uma conta de trabalho.

### <a name="how-to-find-out-which-version-is-installed"></a>Como descobrir qual versão está instalada

1. Você pode descobrir qual versão do aplicativo autenticador do Azure e versões de serviço associado são instaladas no seu dispositivo.
2. No menu pop-up, clique em **sobre**.
3. Tela sobre exibe os serviços de aplicativo e as versões instaladas no seu dispositivo.
 
### <a name="sending-log-files-to-report-issues"></a>Enviar arquivos de log para relatar problemas

1. Siga as orientações Support da Microsoft para relatar um incidente com o aplicativo do Azure autenticador, obter um número de incidente e enviar arquivos de log contra o número de incidente atribuído da seguinte maneira:
2. No menu pop-up, clique em **log**.
3. Se você tiver um incidente de abrir com o Microsoft Support, anote o número de incidente (você precisará-lo para uma etapa posterior). Se você ainda não tiver criado um incidente de suporte e gostaria de Ajuda, siga as orientações em [Suporte da Microsoft](https://support.microsoft.com/en-us/contactus) para abrir um incidente de novo.
4. Na tela de registro em log, clique em **Enviar agora**.
5. Selecione o provedor de email que você deseja usar.
7. Se você já tiver um incidente de abrir Microsoft Support, contate a engenharia reversa de suporte atribuído ao seu problema para descobrir como enviar os dados de log e associado ao seu incidente. O suporte de engenharia fornecerá informações para a linha de endereço e o assunto do email. Se você ainda não tiver um incidente de suporte, siga as orientações no Microsoft Support para abrir um incidente de novo.
9. Edite a linha **para** e a linha de **assunto** com as informações que você recebeu da Microsoft Support.
10. O aplicativo do Azure autenticador anexa o arquivo de log para o email que você está enviando. Descreva o problema que você está enfrentando, atualizar a lista de destinatários (opcional) e envie o email.

### <a name="deleting-the-work-account-and-leaving-your-workplace"></a>Excluir a conta de trabalho e deixando seu local de trabalho

Você pode remover a conta de trabalho que você criou a qualquer momento, da seguinte maneira:

**Para excluir a conta de trabalho no menu Configurações**

1. No Gerenciador de contas, selecione **conta de trabalho**.
2. Na tela conta de trabalho, em **Configurações gerais**, selecione **configurações de conta – sair da sua rede local de trabalho**.
3. Selecione **Sair** na tela do **Local de trabalho ingressar** .
4. Clique em **Okey** quando a mensagem "Tem certeza de que deseja deixar o local de trabalho" é exibida.
5. Isso garante que você excluiu sua conta corporativa do seu local de trabalho.

>[AZURE.NOTE]
>É recomendável que você não use a opção Remover conta para excluir uma conta de trabalho como esta opção pode não funcionar em algumas versões anteriores do Android.

##<a name="uninstalling-the-app"></a>A desinstalação do aplicativo

Em um dispositivo Samsung Android, os privilégios de administrador do dispositivo devem ser removidos da seguinte maneira antes de desinstalar o 
1. Em **configurações**, em **sistema**, selecione **segurança**.
2. D**dispositivo de administração**, clique em **administradores de dispositivo**. Certifique-se de que a caixa de seleção ao lado do **Azure autenticador** está desmarcada.

##<a name="troubleshooting"></a>Solução de problemas

Se você vir o **Erro de repositório de chaves**, isso pode ser porque você não tem o bloqueio conjunto de tela para cima com um PIN. Para contornar esse problema, desinstale o aplicativo do Azure autenticador, configurar um PIN para a tela de bloqueio e reinstale o aplicativo.
