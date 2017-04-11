<properties 
   pageTitle="Entre em contato com o suporte da Microsoft | Microsoft Azure"
   description="Saiba como criar uma solicitação de suporte e iniciar uma sessão de suporte em seu dispositivo de StorSimple."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="contact-microsoft-support"></a>Entre em contato com o suporte da Microsoft

Se você encontrar problemas com sua solução Microsoft Azure StorSimple, você pode criar uma solicitação de serviço para obter suporte técnico. Em uma sessão online com seu engenharia de suporte, você talvez precise iniciar uma sessão de suporte em seu dispositivo de StorSimple. Este artigo conduz você por:

- Como criar uma solicitação de suporte.
- Como iniciar uma sessão de suporte na interface do Windows PowerShell do seu dispositivo de StorSimple.

Examine os [StorSimple 8000 série suporte SLAs e informações](https://msdn.microsoft.com/library/mt433077.aspx) antes de criar uma solicitação de suporte.

## <a name="create-a-support-request"></a>Criar uma solicitação de suporte

Execute as seguintes etapas para criar uma solicitação de suporte:

#### <a name="to-create-a-support-request"></a>Para criar uma solicitação de suporte

1. No [portal de clássico Azure](https://manage.windowsazure.com/), no canto superior direito, clique no seu nome de conta e clique em **Contato do suporte da Microsoft**.

    ![Suporte de contato MS via ManagementPortal](./media/storsimple-contact-microsoft-support/Ibiza1.png)

2. Você será redirecionado para o novo portal Azure (portal.azure.com). Clique no bloco **nova solicitação de suporte** .

    ![Suporte de contato MS via novo portal](./media/storsimple-contact-microsoft-support/Ibiza2.png)

    No lado direito da tela, a **nova solicitação de suporte** aparece. 

    ![Novo painel de solicitação de suporte](./media/storsimple-contact-microsoft-support/Ibiza3a.png)

3. Na caixa de diálogo **Noções básicas** , preencha o seguinte:                                
    1. Na lista suspensa **tipo de problema** , selecione **técnico**.
    2. Selecione uma **assinatura** na lista suspensa.
    3. Na lista suspensa **serviço** , selecione **StorSimple**. 
    4. Selecione um **plano de suporte** da lista suspensa. Você precisa de um plano de suporte pago para habilitar o suporte técnico.

4. Clique em **Avançar**. A caixa de diálogo de **problema** é exibida.

    ![Novo painel de solicitação de suporte](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 

5. Na caixa de diálogo **problema** , preencha o seguinte:

    1.  Selecione um nível de **gravidade** da lista suspensa.
    2.  Selecione um **tipo de problema** na lista suspensa.
    3.  Selecione uma **categoria** na lista suspensa. 
    4.  Na caixa **detalhes** , descreva resumidamente o problema.
    5.  Na caixa **intervalo de tempo** , indique a data, hora e fuso horário que corresponde à ocorrência mais recente do seu problema.
    6.  Em **carregamento de arquivo**, clique no ícone de pasta para navegar até o pacote de suporte.
    7.  Marque a caixa de seleção **compartilhar informações de diagnóstico** .

6. Clique em **Avançar**. Caixa de diálogo **informações de contato** é exibida.

    ![Novo painel de solicitação de suporte](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 

7. Insira suas informações de contato e selecione um método de contato (telefone ou email). 

8. Marque a caixa de seleção **Salvar alterações de contato para solicitações de suporte futuro** .

9. Clique em **criar**.

Depois que você enviou sua solicitação, uma engenharia de suporte entrará em contato com você assim que possível para continuar com sua solicitação.

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Iniciar uma sessão de suporte no Windows PowerShell para StorSimple

Para solucionar problemas que podem ocorrer com o dispositivo StorSimple, você precisará entre em contato com a equipe do Microsoft Support. Microsoft Support talvez precise usar uma sessão de suporte para fazer logon no seu dispositivo. 

Execute as seguintes etapas para iniciar uma sessão de suporte:

#### <a name="to-start-a-support-session"></a>Para iniciar uma sessão de suporte

1. Acesse o dispositivo diretamente por meio do console serial ou por meio de uma sessão de telnet de um computador remoto. Para fazer isso, siga as etapas no [Acabamento de uso para se conectar ao console serial do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Na sessão que é aberta, pressione a tecla **Enter** para obter um prompt de comando.

3. No menu console serial, selecione a opção 1, **fazer logon com acesso total**.

4. No prompt, digite a senha a seguir: 

    `Password1`

5. No prompt, digite o seguinte comando:

    `Enable-HcsSupportAccess`

6. Uma cadeia de caracteres criptografada será apresentada a você. Copie essa cadeia de caracteres para um editor de texto como o bloco de notas.

7. Salve essa cadeia de caracteres e enviá-lo em uma mensagem de email ao Microsoft Support. 

> [AZURE.IMPORTANT] Você pode desativar o acesso ao suporte executando `Disable-HcsSupportAccess`. O dispositivo de StorSimple também tentará desabilitar o acesso ao suporte 8 horas após a sessão foi iniciada. É uma prática recomendada para alterar suas credenciais de dispositivo StorSimple após iniciar uma sessão de suporte.
