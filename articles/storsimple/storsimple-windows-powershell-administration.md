<properties 
   pageTitle="PowerShell para gerenciamento de dispositivo de StorSimple | Microsoft Azure"
   description="Aprenda a usar o Windows PowerShell para StorSimple para gerenciar seu dispositivo StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/18/2016"
   ms.author="alkohli@microsoft.com" />

# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Usar o Windows PowerShell para StorSimple para administrar seu dispositivo

## <a name="overview"></a>Visão geral

Windows PowerShell para StorSimple fornece uma interface de linha de comando que você pode usar para gerenciar seu dispositivo de StorSimple do Microsoft Azure. Como o nome sugere, é uma interface de linha baseado no Windows PowerShell, que é criada em um espaço de execução restrito. Sob a perspectiva do usuário na linha de comando, um espaço de execução restrito é exibido como uma versão restrita do Windows PowerShell. Mantendo alguns dos recursos básicos do Windows PowerShell, essa interface possui cmdlets dedicado adicionais que são voltado para gerenciar seu dispositivo de StorSimple do Microsoft Azure. 

Este artigo descreve o Windows PowerShell para os recursos StorSimple, incluindo como você pode conectar a essa interface e contém links para procedimentos passo a passo ou fluxos de trabalho que você pode executar usando esta interface. Os fluxos de trabalho incluem como registrar seu dispositivo, configurar a interface de rede em seu dispositivo, instale as atualizações que exigem o dispositivo para estar no modo de manutenção, altere o estado do dispositivo e solucionar problemas que podem ocorrer.

Depois de ler este artigo, você será capaz de:

- Conectar-se ao seu dispositivo de StorSimple usando o Windows PowerShell para StorSimple.

- Administre seu dispositivo StorSimple usando o Windows PowerShell para StorSimple.

- Obter ajuda no Windows PowerShell para StorSimple.

>[AZURE.NOTE]   

>- Windows PowerShell para StorSimple cmdlets permitem que você gerencie seu dispositivo StorSimple de um console serial ou remotamente através do Windows PowerShell remoto. Para obter mais informações sobre cada um dos cmdlets individuais que podem ser usados nessa interface, vá para [referência de cmdlet do Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

>- Os cmdlets do Azure PowerShell StorSimple são um conjunto diferente de cmdlets que permitem automatizar StorSimple nível de serviço e as tarefas de migração da linha de comando. Para obter mais informações sobre os cmdlets do PowerShell do Azure para StorSimple, vá para a [referência de cmdlets do Azure StorSimple](https://msdn.microsoft.com/library/azure/dn920427.aspx).

Você pode acessar o Windows PowerShell para StorSimple usando um dos seguintes métodos:

- [Conectar ao console serial do dispositivo StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
- [Conectar-se remotamente a StorSimple usando o Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)
    

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Conectar ao Windows PowerShell para StorSimple por meio do console de dispositivo serial

Você pode [Baixar acabamento](http://www.putty.org/) ou terminal semelhante software de emulação para se conectar ao Windows PowerShell para StorSimple. Você precisa configurar acabamento especificamente para acessar o dispositivo de StorSimple do Microsoft Azure. Os tópicos a seguir contêm as etapas detalhadas sobre como configurar Acabamento e conectar ao dispositivo. Várias opções de menu no console serial também são explicadas.

### <a name="putty-settings"></a>Configurações de acabamento

Certifique-se de que você use as seguintes configurações de acabamento para conectar-se para a interface do Windows PowerShell do console serial.

#### <a name="to-configure-putty"></a>Para configurar acabamento

1. Na caixa de diálogo acabamento **reconfiguração** , no painel **categorias** , selecione o **teclado**.

2. Certifique-se de que as seguintes opções estão selecionadas (esses são as configurações padrão quando você inicia uma nova sessão). 

  	|Item de teclado|Selecione|
  	|---|---|
  	|Tecla BACKSPACE|Controle-? (127)|
  	|Teclas de início e término|Padrão|
  	|Teclado e teclas de função|ESC [n ~|
  	|Estado inicial de chaves do cursor|Normal|
  	|Estado inicial do teclado numérico|Normal|
  	|Habilitar recursos extra de teclado|Controle Alt é diferente do AltGr|

    ![Configurações de Acabamento com suporte](./media/storsimple-windows-powershell-administration/IC740877.png)

3. Clique em **Aplicar**.

4. No painel **categorias** , selecione **tradução**.

5. Na caixa de listagem **conjunto de caracteres remoto** , selecione **UTF-8**.

6. Em **tratamento de caracteres de desenho de linha**, selecione **pontos de código Unicode de uso desenho de linha**. A ilustração a seguir mostra as seleções de acabamento corretas.

    ![UTF Putty configurações](./media/storsimple-windows-powershell-administration/IC740878.png)

7. Clique em **Aplicar**.


Agora você pode usar acabamento para se conectar ao console serial dispositivo seguindo as etapas a seguir.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]


### <a name="about-the-serial-console"></a>Sobre o console serial

Quando você acessar o Windows PowerShell interface do seu dispositivo de StorSimple por meio do console serial, uma mensagem de faixa é apresentada, seguido de opções do menu. 

A mensagem de faixa contém informações básicas de dispositivo de StorSimple como o modelo, nome, versão de software instalado e status do controlador que estiver acessando. A imagem a seguir mostra um exemplo de uma mensagem de faixa.

![Mensagem de faixa serial](./media/storsimple-windows-powershell-administration/IC741098.png)

>[AZURE.IMPORTANT] Você pode usar a mensagem de faixa para identificar se o controlador que estiver conectado à está ativo ou passivo.

A imagem a seguir mostra as várias opções de espaço de execução que estão disponíveis no menu console serial.

![Registrar seu dispositivo 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Você pode escolher entre as seguintes configurações:

1. **Fazer logon com acesso total** Essa opção permite que você se conecte (com as credenciais apropriadas) espaço **SSAdminConsole** no controlador de local. (O local é o controlador que você está acessando atualmente por meio do console serial do seu dispositivo StorSimple.) Essa opção também pode ser usada para permitir que o Support da Microsoft acessar irrestrito espaço de execução (uma sessão de suporte) para solucionar qualquer problema de dispositivo possíveis. Após usar a opção 1 para fazer logon, você pode permitir a engenharia do Microsoft Support acessar o espaço de execução irrestrito executando um cmdlet específico. Para obter detalhes, consulte para [Iniciar uma sessão de suporte](storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).

2. **Faça logon controlador de ponto com acesso total** Essa opção é a mesma que a opção 1, exceto que você pode conectar (com as credenciais apropriadas) ao espaço **SSAdminConsole** no controlador de ponto. Como o dispositivo StorSimple é um dispositivo de alta disponibilidade com dois controladores em uma configuração ativo-passivo, ponto se refere a outro controlador de dispositivo que você está acessando por meio do console serial).
Semelhante a opção 1, esta opção também pode ser usada para permitir que o Support da Microsoft acessar o espaço de execução irrestrito em um controlador de ponto.

3. **Conectar-se com acesso limitado** Esta opção é usada para acessar a interface do Windows PowerShell no modo limitado. Você não será solicitado para credenciais de acesso. Esta opção se conecta a um espaço de execução mais restrito em comparação com opções 1 e 2.  Algumas das tarefas que estão disponíveis por meio da opção 1 que **não pode* ser executada neste espaço de execução são:

    - Redefinir para as configurações de fábrica
    - Alterar a senha
    - Habilitar ou desabilitar o acesso ao suporte
    - Aplicar atualizações
    - Instalar hotfixes 
                                                

    >[AZURE.NOTE] **Essa é a opção preferencial se você tiver esquecido a senha de administrador do dispositivo e não consegue se conectar por meio de opção 1 ou 2.**

4. **Alterar idioma** Essa opção permite que você alterar o idioma de exibição na interface do Windows PowerShell. Os idiomas com suporte são inglês, japonês, russo, francês, Sul coreano, espanhol, italiano, alemão, chinês e português (Brasil).


## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Conectar-se remotamente a StorSimple usando o Windows PowerShell para StorSimple

Você pode usar o Windows PowerShell remoto para se conectar ao seu dispositivo StorSimple. Quando você conecta dessa maneira, você não verá um menu. (Você verá um menu apenas se você usar o console serial no dispositivo para se conectar. Conectando-se remotamente leva você diretamente para o equivalente a "opção 1 – acesso total" no console do serial.) Comunicação remota do Windows PowerShell, você se conectar a um espaço de execução específico. Você também pode especificar o idioma de exibição. 

O idioma de exibição é independente do idioma que você definir usando a opção de **Alterar idioma** no menu console serial. PowerShell remoto selecionará automaticamente a localidade do dispositivo que você se conecta se nenhum for especificado.

>[AZURE.NOTE] Se você estiver trabalhando com hosts virtuais do Microsoft Azure e dispositivos virtuais StorSimple, você pode usar o Windows PowerShell remoto e o host virtual para conectar o dispositivo virtual. Se você tiver configurado um local de compartilhamento do host na qual deseja salvar as informações da sessão do Windows PowerShell, você deve estar ciente de que a principal todos inclui somente os usuários autenticados. Portanto, se você configurou o compartilhamento para permitir acesso pelo todos e você conectar sem especificar credenciais, o capital anônimo não autenticado será usado e você verá um erro. Para corrigir esse problema, no compartilhamento hospedar você deve habilitar a conta de convidado e dê o convidado acesso total de conta para o compartilhamento ou especifique credenciais válidas juntamente com o cmdlet do Windows PowerShell.

Você pode usar HTTP ou HTTPS para conectar-se através do Windows PowerShell remoto. Use as instruções nos seguintes tutoriais:

- [Conectar remotamente usando HTTP](storsimple-remote-connect.md#connect-through-http)
- [Conectar-se remotamente usando HTTPS](storsimple-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Considerações de segurança de Conexão

Ao decidir como se conectar ao Windows PowerShell para StorSimple, considere o seguinte:

- Conectar-se diretamente para o console serial do dispositivo é seguro, mas se conectando ao console serial sobre opções de rede não está. Tenha cuidado do risco de segurança ao conectar com o dispositivo serial sobre opções de rede.

- Conectando através de uma sessão HTTP pode oferecer mais segurança do que conectando através do console serial através de rede. Embora não seja o método mais seguro, é aceitável em redes confiáveis.

- Conectando através de uma sessão HTTPS é o mais seguro e a opção recomendada.


## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrar seu dispositivo StorSimple usando o Windows PowerShell para StorSimple
A tabela a seguir mostra um resumo de todas as tarefas comuns de gerenciamento e fluxos de trabalho complexos que podem ser realizados dentro da interface do Windows PowerShell do seu dispositivo de StorSimple. Para obter mais informações sobre cada fluxo de trabalho, clique na entrada apropriada na tabela.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell para fluxos de trabalho de StorSimple

|Se você quiser fazer isso...|Use este procedimento.|
|---|---|
|Registrar seu dispositivo|[Configurar e registrar o dispositivo usando o Windows PowerShell para StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
|Configurar o proxy da web</br>Configurações de proxy do modo de exibição da web|[Configurar o proxy da web para o seu dispositivo de StorSimple](storsimple-configure-web-proxy.md)|
|Modificar configurações de interface de rede 0 de dados em seu dispositivo|[Modificar a interface de rede 0 de dados para o seu dispositivo de StorSimple](storsimple-modify-data-0.md)|
|Parar um controlador </br> Reiniciar ou desligar um controlador </br> Desligar um dispositivo</br>Redefinir o dispositivo para as configurações padrão de fábrica|[Gerenciar controladores de dispositivo](storsimple-manage-device-controller.md)|
|Instalar atualizações do modo de manutenção e hotfixes|[Atualizar seu dispositivo](storsimple-update-device.md)|
|Insira o modo de manutenção </br>Sair do modo de manutenção|[Modos de dispositivo de StorSimple](storsimple-device-modes.md)|
|Criar um pacote de suporte</br>Descriptografar e editar um pacote de suporte|[Criar e gerenciar um pacote de suporte](storsimple-create-manage-support-package.md)|
|Iniciar uma sessão de suporte</br>|[Iniciar uma sessão de suporte no Windows PowerShell para StorSimple](/storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)
 

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Obter ajuda no Windows PowerShell para StorSimple

No Windows PowerShell para StorSimple, cmdlet ajuda está disponível. Uma versão atualizada online dessa ajuda também está disponível, que você pode usar para atualizar a Ajuda em seu sistema.

Obtendo ajuda nesta interface é semelhante ao Windows PowerShell e a maioria dos cmdlets relacionados a Ajuda funcionarão. Você pode encontrar ajuda para Windows PowerShell online na biblioteca do TechNet: [script com o Windows PowerShell](http://go.microsoft.com/fwlink/?LinkID=108518).

A seguir está uma breve descrição dos tipos de ajuda para a interface do Windows PowerShell, incluindo como atualizar a Ajuda.

#### <a name="to-get-help-for-a-cmdlet"></a>Para obter ajuda para um cmdlet

- Para obter ajuda para qualquer cmdlet ou a função, use o seguinte comando:`Get-Help <cmdlet-name>`

- Para obter ajuda online para qualquer cmdlet, use o cmdlet anterior com a `-Online` parâmetro:`Get-Help <cmdlet-name> -Online`

- Para obter ajuda completa, você pode usar o `–Full` parâmetro e para obter exemplos, use o `–Examples` parâmetro.

#### <a name="to-update-help"></a>Atualizar ajuda

Você pode atualizar facilmente a Ajuda da interface do Windows PowerShell. Execute as seguintes etapas para atualizar a Ajuda em seu sistema.

#### <a name="to-update-cmdlet-help"></a>Para atualizar o cmdlet ajuda

1. Inicie o Windows PowerShell com a opção **Executar como administrador** .

1. No prompt de comando, digite: `Update-Help`

1. Os arquivos de Ajuda atualizados serão instalados.

1. Depois que os arquivos de ajuda são instalados, digite: `Get-Help Get-Command`. Isso exibirá uma lista de cmdlets do qual Ajuda está disponível.


>[AZURE.NOTE] Para obter uma lista de todos os cmdlets disponíveis em um espaço de execução, faça logon na opção de menu correspondente e executar o `Get-Command` cmdlet.

## <a name="next-steps"></a>Próximas etapas
Se você tiver problemas com o dispositivo StorSimple ao executar um dos fluxos de trabalho acima, consulte [Ferramentas para implantações de StorSimple de solução de problemas](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

