<properties 
   pageTitle="Solucionar problemas de implantação de StorSimple | Microsoft Azure"
   description="Descreve como diagnosticar e corrigir os erros que ocorrem quando você implanta StorSimple."
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
   ms.author="alkohli" />

# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Solucionar problemas de implantação de dispositivo StorSimple

## <a name="overview"></a>Visão geral

Este artigo fornece orientações de solução de problemas úteis para a sua implantação do Microsoft Azure StorSimple. Ele descreve problemas comuns, possíveis causas e etapas recomendadas para ajudá-lo a resolver problemas que podem ocorrer quando você configura o StorSimple. Essas informações se aplicam ao dispositivo físico StorSimple local e o dispositivo virtual StorSimple.

> [AZURE.NOTE] Problemas relacionados à configuração do dispositivo que você pode enfrentar podem ocorrer quando você implantar o dispositivo pela primeira vez ou podem ocorrer mais tarde, quando o dispositivo está funcionando. Este artigo aborda como solucionar problemas de implantação pela primeira vez. Para solucionar um dispositivo operacional, vá para [Solucionar problemas de dispositivo operacional](storsimple-troubleshoot-operational-device.md).

Este artigo também descreve as ferramentas para implantações de StorSimple de solução de problemas e fornece um exemplo de solução de problemas passo a passo.

## <a name="first-time-deployment-issues"></a>Problemas de implantação pela primeira vez

Se você enfrentar um problema ao implantar o seu dispositivo pela primeira vez, considere o seguinte:

- Se você está solucionando problemas de um dispositivo físico, verifique se que o hardware foi instalado e configurado conforme descrito em [instalar seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
- Verificar os pré-requisitos para implantação. Certifique-se de que você tenha todas as informações descritas na lista [lista de verificação de configuração de implantação](storsimple-deployment-walkthrough.md#deployment-configuration-checklist)de.
- Examine as notas de versão do StorSimple para ver se o problema é descrito. As notas de versão incluem soluções alternativas para problemas de instalação conhecidos. 

Durante a implantação do dispositivo, a mais comum problemas que os usuários face ocorrem quando eles executar o Assistente de configuração e quando eles registrar o dispositivo por meio do Windows PowerShell para StorSimple. (Você usa o Windows PowerShell para StorSimple para registrar e configurar seu dispositivo StorSimple. Para obter mais informações sobre o registro de dispositivos, consulte [etapa 3: configurar e registrar seu dispositivo através do Windows PowerShell para StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

As seções a seguir podem ajudá-lo a resolver problemas que ocorrer quando você configura o dispositivo StorSimple pela primeira vez.

## <a name="first-time-setup-wizard-process"></a>Processo do Assistente de configuração de pela primeira vez

As etapas a seguir resumem o processo do Assistente de configuração. Para obter informações detalhadas de configuração, consulte [implantar seu dispositivo de StorSimple local](storsimple-deployment-walkthrough.md).

1. Execute o cmdlet [HcsSetupWizard chamar](https://technet.microsoft.com/library/dn688135.aspx) para iniciar o Assistente de configuração que o orientará durante as etapas restantes. 
2. Configurar a rede: o Assistente de configuração permite que você definir configurações de rede para a interface de rede 0 de dados em seu dispositivo de StorSimple. Essas configurações incluem o seguinte:
  - Virtual IP (VIP), máscara de sub-rede e gateway – o cmdlet [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) é executado em segundo plano. Ele configura o endereço IP, máscara de sub-rede e gateway para a interface de rede 0 de dados em seu dispositivo de StorSimple.
  - Servidor DNS primário – o cmdlet [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) é executado em segundo plano. Ele configura as configurações de DNS para a sua solução de StorSimple.
  - Servidor NTP – o cmdlet [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) é executado em segundo plano. Ele configura as configurações de servidor NTP para sua solução de StorSimple.
  - Proxy de web opcional – o cmdlet [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) é executado em segundo plano. Define e permite a configuração de proxy da web para a sua solução de StorSimple.
3. Configurar as senhas: a próxima etapa é configurar o administrador do dispositivo e senhas do Gerenciador de instantâneo StorSimple. Se você estiver executando Update 1, então você não deverão configurar a senha do Gerenciador de instantâneo StorSimple.
  - A senha de administrador do dispositivo é usada para fazer logon no seu dispositivo. A senha do dispositivo padrão é **Senha1**.
  - A senha do Gerenciador de instantâneo StorSimple é necessária quando você configura um dispositivo para usar o Gerenciador de instantâneo StorSimple. Você precisa primeiro definir a senha no Assistente de configuração e, em seguida, você pode definir e altere-a do serviço do Gerenciador de StorSimple. Essa senha autentica o dispositivo com o Gerenciador de instantâneo StorSimple.
 
    > [AZURE.IMPORTANT] As senhas são coletadas antes de registro, mas aplicadas somente depois que você registrar o dispositivo com êxito. Se houver uma falha ao aplicar uma senha, você será solicitado a fornecer a senha novamente até que as senhas necessárias (que atendem aos requisitos de complexidade) são coletadas.

4. Registrar o dispositivo: a etapa final é registrar o dispositivo com o serviço de Gerenciador de StorSimple em execução no Microsoft Azure. O registro requer que você obter [a chave do registro de serviço](storsimple-manage-service.md#get-the-service-registration-key) do portal clássico do Azure e forneça-la no Assistente de configuração. Depois que o dispositivo é registrado com êxito, uma chave de criptografia de dados de serviço é fornecida a você. Certifique-se de manter essa chave de criptografia em um local seguro, porque ele será solicitado a registrar todos os dispositivos subsequentes com o serviço.

## <a name="common-errors-during-device-deployment"></a>Erros comuns durante a implantação do dispositivo

As tabelas a seguir listam os erros comuns que podem ocorrer quando você:

- Defina as configurações de rede necessárias.
- Defina as configurações de proxy web opcional.
- Configure o administrador do dispositivo e Gerenciador de instantâneo StorSimple senhas. 
- Registre o dispositivo. 

## <a name="errors-during-the-required-network-settings"></a>Erros durante as configurações de rede necessárias

| Não.| Mensagem de erro | Causas possíveis | Ação recomendada |
| ---| ------------- | --------------- | ------------------ |
| 1  | Invocar-HcsSetupWizard: Este comando só pode ser executado no controlador de ativos. | Configuração estava sendo realizada no controlador de passivo.| Execute este comando do controlador de ativos. Para obter mais informações, consulte [identificar um controlador ativo em seu dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).|
| 2 | Invocar-HcsSetupWizard: Dispositivo não está pronto. | Há problemas com a conectividade de rede nos dados 0.| Verifique a conectividade de rede física em dados 0.|
| 3 | Invocar-HcsSetupWizard: Há um conflito de endereço IP com outro sistema na rede (exceção de HRESULT: 0x80070263). | O IP fornecido para dados 0 já estava em uso por outro sistema. | Forneça um novo IP que não está em uso.|
| 4 | Invocar-HcsSetupWizard: Falha de um recurso de cluster. (Exceção de HRESULT: 0x800713AE). | Duplicar VIP. O IP fornecido já está em uso.| Forneça um novo IP que não está em uso.|
| 5 | Invocar-HcsSetupWizard: IPv4 inválidos endereço. | O endereço IP é fornecido em um formato incorreto.| Verifique o formato e forneça seu endereço IP novamente. Para obter mais informações, consulte [Endereçamento Ipv4][1]. |
| 6 | Invocar-HcsSetupWizard: Endereço de IPv6 inválido. | O endereço IP é fornecido em um formato incorreto.| Verifique o formato e forneça seu endereço IP novamente. Para obter mais informações, consulte [Endereçamento Ipv6][2].|
| 7 | Invocar-HcsSetupWizard: não há nenhum ponto de extremidade mais disponíveis o mapeador. (Exceção de HRESULT: 0x800706D9) | A funcionalidade de cluster não está funcionando. | [Contate o suporte do Microsoft](storsimple-contact-microsoft-support.md) para as próximas etapas.

## <a name="errors-during-the-optional-web-proxy-settings"></a>Erros durante as configurações de proxy web opcional

| Não.| Mensagem de erro | Causas possíveis | Ação recomendada |
| ---| ------------- | --------------- | ------------------ |
| 1  | Invocar-HcsSetupWizard: Parâmetro inválido (exceção de HRESULT: 0x80070057) | Um dos parâmetros fornecidos para as configurações de proxy não é válido.| O URI não é fornecido no formato correto. Use o seguinte formato: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 | Invocar-HcsSetupWizard: Servidor RPC não está disponível (exceção de HRESULT: 0x800706ba) | A causa é uma das seguintes opções:<ol><li>O cluster não é até.</li><li>Controlador de passivo não consegue se comunicar com o controlador de ativo e o comando é executado de controlador de passivo.</li></ol> | Dependendo da causa raiz:<ol><li>[Contate o suporte do Microsoft](storsimple-contact-microsoft-support.md) para certificar-se de que o cluster é para cima.</li><li>Execute o comando do controlador de ativos. Se você quiser executar o comando do controlador de passivo, você precisará garantir que o controlador de passivo possa se comunicar com o controlador de ativo. Você precisará entrar [em contato com o suporte da Microsoft](storsimple-contact-microsoft-support.md) se essa conectividade foi desfeita.</li></ol> |
| 3 | Invocar-HcsSetupWizard: Chamada RPC falhou (exceção de HRESULT: 0x800706be) | Cluster está inoperante. | [Contate o suporte do Microsoft](storsimple-contact-microsoft-support.md) para certificar-se de que o cluster estiver ativo.|
| 4 | Invocar-HcsSetupWizard: Cluster recurso não encontrado (exceção de HRESULT: 0x8007138f) | O recurso de cluster não foi encontrado. Isso pode acontecer quando a instalação não está correta. | Talvez você precise reiniciar o dispositivo para as configurações padrão de fábrica. [Contate o suporte do Microsoft](storsimple-contact-microsoft-support.md) para criar um recurso de cluster.|
| 5 | Invocar-HcsSetupWizard: Cluster recurso não online (exceção de HRESULT: 0x8007138c)| Recursos de cluster não estão online. | [Contate o suporte do Microsoft](storsimple-contact-microsoft-support.md) para as próximas etapas.|

## <a name="errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords"></a>Erros relacionados ao administrador do dispositivo e senhas de StorSimple Gerenciador de instantâneo

A senha de administrador do dispositivo padrão é **Senha1**. Essa senha expira após o primeiro logon; Portanto, você precisará usar o Assistente de configuração para alterá-lo. Quando você registra o dispositivo pela primeira vez, você deve fornecer uma nova senha de administrador do dispositivo. 

Se você usar o software de Gerenciador de instantâneo StorSimple executado no host do servidor Windows para gerenciar o dispositivo, também deve fornecer uma senha de Gerenciador de instantâneo StorSimple durante o registro pela primeira vez. 

Certifique-se de que suas senhas cumprir os seguintes requisitos:

- Sua senha de administrador do dispositivo deve estar entre 8 e 15 caracteres.
- Sua senha do Gerenciador de instantâneo StorSimple deve ser 14 ou 15 caracteres.
- As senhas devem conter 3 dos seguintes tipos de 4 caractere: minúsculas, maiusculas, numéricos e especiais. 
- Sua senha não pode ser o mesmo que as últimas 24 senhas.

Além disso, tenha em mente que as senhas expiram todo ano e podem ser alteradas somente depois que você registra com êxito o dispositivo. Se o registro falhar por algum motivo, as senhas não serão alteradas. Para obter mais informações sobre o administrador do dispositivo e senhas do Gerenciador de instantâneo StorSimple, vá para [usar o serviço de Gerenciador de StorSimple para alterar suas senhas de StorSimple](storsimple-change-passwords.md).

Você pode encontrar um ou mais dos seguintes erros ao configurar o administrador do dispositivo e Gerenciador de instantâneo StorSimple senhas.

| Não.| Mensagem de erro | Ação recomendada |
| ---| ------------- | ------------------ | 
| 1  | A senha excede o comprimento máximo. |Use uma senha que atenda a esses requisitos:<ul><li>Sua senha de administrador do dispositivo deve estar entre 8 e 15 caracteres.</li><li>Sua senha do Gerenciador de instantâneo StorSimple deve ser 14 ou 15 caracteres.</li></ul> | 
| 2 | A senha não atende o comprimento necessário. | Use uma senha que atenda a esses requisitos:<ul><li>Sua senha de administrador do dispositivo deve estar entre 8 e 15 caracteres.</li><li>Sua senha do Gerenciador de instantâneo StorSimple deve ser 14 ou 15 caracteres.</lu></ul> |
| 3 | A senha deve conter caracteres minúsculos. | As senhas devem conter 3 dos seguintes tipos de 4 caractere: minúsculas, maiusculas, numéricos e especiais. Certifique-se de que sua senha atende a esses requisitos. |
| 4 | A senha deve conter caracteres numéricos. | As senhas devem conter 3 dos seguintes tipos de 4 caractere: minúsculas, maiusculas, numéricos e especiais. Certifique-se de que sua senha atende a esses requisitos. |
| 5 | A senha deve conter caracteres especiais. | As senhas devem conter 3 dos seguintes tipos de 4 caractere: minúsculas, maiusculas, numéricos e especiais. Certifique-se de que sua senha atende a esses requisitos. |
| 6 | A senha deve conter 3 dos seguintes tipos de 4 caractere: letras maiusculas, minúsculas, numéricos e especiais. | Sua senha não contém necessários tipos de caracteres. Certifique-se de que sua senha atende a esses requisitos. |
| 7 | Parâmetro não correspondem a confirmação. | Certifique-se de que sua senha atende a todos os requisitos e que ele foi inserido corretamente. |
| 8 | Sua senha não pode corresponder padrão. | A senha padrão é *Senha1*. Você precisa alterar essa senha depois de fazer logon pela primeira vez. |
| 9 | A senha que você digitou não corresponde a senha do dispositivo. Redigite a senha. | Verifique a senha e digite-a novamente. |

As senhas são coletadas antes do dispositivo está registrado, mas são aplicadas somente depois do registro bem-sucedido. O fluxo de trabalho de recuperação de senha requer o dispositivo para ser registrado. 

> [AZURE.IMPORTANT] Em geral, se uma tentativa de aplicar uma senha falhar, o software repetidamente tenta coletar a senha até que ela seja bem-sucedido. Em casos raros, a senha não pode ser aplicada. Nessa situação, você pode registrar o dispositivo e continuar, porém as senhas não serão alteradas. Você não receberá nenhuma indicação como para que a senha não foi alterada — a senha de administrador do dispositivo ou a senha do Gerenciador de instantâneo StorSimple. Se essa situação ocorre, recomendamos que você altere as duas senhas.

Você pode redefinir as senhas no portal do clássico Azure por meio do serviço do Gerenciador de StorSimple. Para obter mais informações, vá para: 

- [Alterar a senha de administrador do dispositivo](storsimple-change-passwords.md#change-the-device-administrator-password).
- [Alterar a senha do Gerenciador de instantâneo StorSimple](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

## <a name="errors-during-device-registration"></a>Erros durante o registro de dispositivo

Use o serviço de Gerenciador de StorSimple em execução no Microsoft Azure para registrar o dispositivo. Você pode encontrar um ou mais dos seguintes problemas durante o registro do dispositivo.

| Não.| Mensagem de erro | Causas possíveis | Ação recomendada |
| ---| ------------- | --------------- | ------------------ |
| 1  | Erro 350027: Falha ao registrar o dispositivo com o Gerenciador de StorSimple. |  | Aguarde alguns minutos e tente novamente a operação. Se o problema persistir, [entre em contato com o suporte da Microsoft](storsimple-contact-microsoft-support.md).|
| 2  | Erro 350013: Ocorreu um erro no registro do dispositivo. Isso pode ser devido a chave do registro de serviço incorreta. | | Registre o dispositivo novamente com a chave do registro de serviço correto. Para obter mais informações, consulte [obter a chave do registro de serviço.](storsimple-manage-service.md#get-the-service-registration-key) |
| 3 | Erro 350063: Falha de autenticação do serviço de Gerenciador de StorSimple passado, mas registro. Repita a operação após alguns minutos. | Este erro indica que autenticação com ACS passou, mas a chamada de registro feita no serviço falhou. Isso pode ser um resultado de um problema de rede esporádica. | Caso o problema persistir, entre [em contato com o suporte da Microsoft](storsimple-contact-microsoft-support.md). |
| 4 | Erro 350049: O serviço não pôde ser alcançado durante o registro. | Quando a chamada é feita para o serviço, uma exceção de web for recebida. Em alguns casos, isso pode obter corrigido com repetindo a operação mais tarde. | Verifique seu endereço IP e o nome DNS e, em seguida, repita a operação. Se o problema persistir, [contate o Microsoft Support.](storsimple-contact-microsoft-support.md) | 
| 5 | Erro 350031: O dispositivo já foi registrado. | | Nenhuma ação é necessária. |
| 6 | Erro 350016: Falha no registro do dispositivo. | |Verifique se que a chave do registro está correta. |
| 7 | Invocar-HcsSetupWizard: Ocorreu um erro ao registrar seu dispositivo; Isso pode ser devido a endereço IP incorreto ou o nome DNS. Verifique suas configurações de rede e tente novamente. Se o problema persistir, [entre em contato com o suporte da Microsoft](storsimple-contact-microsoft-support.md). (Erro 350050) | Certifique-se de que seu dispositivo pode executar ping da rede externa. Se você não tiver conectividade com a rede externa, o registro poderá falhar com esse erro. Esse erro pode ser uma combinação de um ou mais destes procedimentos:<ul><li>IP incorreto</li><li>Sub-rede incorretas</li><li>Gateway incorreto</li><li>Configurações de DNS incorretas</li></ul> | Consulte as etapas no [exemplo de solução de problemas passo a passo](#step-by-step-storsimple-troubleshooting-example). |
| 8 | Invocar-HcsSetupWizard: A operação atual falhou devido a um erro de serviço interno [0x1FBE2]. Repita a operação após algum tempo. Se o problema persistir, entre em contato com o Microsoft Support. | Este é um erro genérico lançado para todos os erros de invisível de usuário de agente ou serviço. O motivo mais comum pode ser que a autenticação do ACS falhou. Uma causa possível da falha é que haja problemas com a configuração do servidor NTP e hora do dispositivo não está definida corretamente. | Corrigir o tempo (se há problemas) e, em seguida, repita a operação de registro. Se você usar o comando Set-HcsSystem - fuso horário para ajustar o fuso horário, cada palavra em maiuscula no fuso horário (por exemplo "hora do Pacífico padrão").  Se esse problema persistir, [contato do suporte da Microsoft](storsimple-contact-microsoft-support.md) para próximos passos. |
| 9 | Aviso: Não foi possível ativar o dispositivo. O administrador do dispositivo e senhas do Gerenciador de instantâneo StorSimple não foram alteradas. | Se o registro falhar, o administrador do dispositivo e Gerenciador de instantâneo StorSimple senhas não são alteradas. |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Ferramentas para implantações de StorSimple de solução de problemas

StorSimple inclui diversas ferramentas que você pode usar para solucionar problemas de sua solução de StorSimple. Eles incluem:

- Pacotes de suporte e logs de dispositivo 
- Cmdlets projetados especificamente para solução de problemas 

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Logs de dispositivo disponíveis e pacotes de suporte para solução de problemas

Um pacote de suporte contém todos os logs relevantes que podem ajudar a equipe do Microsoft Support para solucionar problemas de dispositivo. Você pode usar o Windows PowerShell para StorSimple para gerar um pacote de suporte criptografada que você pode compartilhar com a equipe de suporte.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Para exibir os logs ou o conteúdo do pacote de suporte

1. Usar o Windows PowerShell para StorSimple para gerar um pacote de suporte, conforme descrito em [criar e gerenciar um pacote de suporte](storsimple-create-manage-support-package.md).

2. Baixe o [script de descriptografia](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente em seu computador cliente.

3. Use este [procedimento passo a passo](storsimple-create-manage-support-package.md#edit-a-support-package) para abrir e descriptografar o pacote de suporte.

4. Os logs de pacote de suporte descriptografados estão em formato de etw/etvx. Você pode executar as seguintes etapas para ver esses arquivos no Visualizador de eventos do Windows:
  1. Execute o comando **eventvwr** em seu cliente do Windows. Isso irá iniciar o Visualizador de eventos.
  2. No painel de **ações** , clique em **Abrir Log salvo** e aponte para os arquivos de log no formato de etvx/etw (o pacote de suporte). Agora você pode exibir o arquivo. Após abrir o arquivo, você pode com o botão direito e salve o arquivo como texto.
   
    > [AZURE.IMPORTANT] Você também pode usar o cmdlet **Get-WinEvent** para abrir esses arquivos no Windows PowerShell. Para obter mais informações, consulte [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) na documentação de referência de cmdlet do Windows PowerShell.

5. Quando abrir os logs no Visualizador de eventos, procure os seguintes logs que contêm problemas relacionados à configuração do dispositivo:

  - hcs_pfconfig/operacional Log
  - hcs_pfconfig/Config

6. Nos arquivos de log, procure as cadeias relacionadas aos cmdlets chamados pelo Assistente de instalação. Consulte o [processo do Assistente de configuração de pela primeira vez](#first-time-setup-wizard-process) para obter uma lista desses cmdlets. 

7. Se você não conseguir descobrir a causa do problema, você pode [contatar o suporte da Microsoft](storsimple-contact-microsoft-support.md) para as próximas etapas. Use as etapas em [criar uma solicitação de suporte](storsimple-contact-microsoft-support.md#create-a-support-request) quando você contatar Microsoft Support para obter assistência.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlets disponíveis para solução de problemas

Use os seguintes cmdlets do Windows PowerShell para detectar erros de conectividade.

- `Get-NetAdapter`: Use esse cmdlet para detectar a integridade de interfaces de rede. 

- `Test-Connection`: Use esse cmdlet para verificar a conectividade de rede dentro e fora da rede.

- `Test-HcsmConnection`: Use esse cmdlet para verificar a conectividade de um dispositivo registrado com êxito.

Se você estiver executando Update 1 em seu dispositivo de StorSimple, os seguintes cmdlets de diagnósticos também estão disponíveis.

- `Sync-HcsTime`: Use esse cmdlet para exibir a hora do dispositivo e forçar uma sincronização de horário com o servidor NTP.

- `Enable-HcsPing`e `Disable-HcsPing`: usar esses cmdlets para permitir que os hosts ping as interfaces de rede em seu dispositivo de StorSimple. Por padrão, as interfaces de rede StorSimple não responder à solicitações de ping.

- `Trace-HcsRoute`: Use esse cmdlet como uma ferramenta de rastreamento de rota. Ele envia pacotes para cada roteador no caminho para um destino final durante um período de tempo e depois calcula os resultados com base nos pacotes retornados de cada salto. Como `Trace-HcsRoute` mostra o grau de perda de pacotes em qualquer roteador ou link determinado, você pode identificar quais roteadores ou links podem estar provocando problemas de rede. 

- `Get-HcsRoutingTable`: Use esse cmdlet para exibir a tabela de roteamento IP local.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Solucionar problemas com o cmdlet Get-NetAdapter

Quando você configura interfaces de rede para uma implantação do dispositivo pela primeira vez, o status de hardware não está disponível no serviço do Gerenciador de StorSimple UI porque o dispositivo ainda não esteja registrado com o serviço. Além disso, a página de Status de Hardware pode não sempre corretamente refletir o estado do dispositivo, especialmente se há problemas que afetam a sincronização de serviço. Nesses casos, você pode usar o `Get-NetAdapter` cmdlet para determinar a integridade e o status das suas interfaces de rede.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Para ver uma lista de todos os adaptadores de rede em seu dispositivo

1. Iniciar o Windows PowerShell para StorSimple e digite `Get-NetAdapter`. 

2. Usar a saída da `Get-NetAdapter` cmdlet e as seguintes diretrizes para entender o status de sua interface de rede.
  - Se a interface está íntegra e habilitado, o status de **ifIndex** é mostrado como **backup**.
  - Se a interface é eficaz, mas não está conectada física (por um cabo de rede), **ifIndex** é mostrado como **desativado**.
  - Se a interface for eficaz, mas não é ativado, o status de **ifIndex** é mostrado como **NotPresent**.
  - Se a interface não existir, ele não aparecem nesta lista. O serviço do Gerenciador de StorSimple UI ainda mostrará essa interface em um estado de falha.

Para obter mais informações sobre como usar esse cmdlet, vá para [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) na referência de cmdlet do Windows PowerShell. 

As seções a seguir mostram exemplos de saída do `Get-NetAdapter` cmdlet. 

 Nesses exemplos, controlador 0 foi o controlador passivo e foi configurado da seguinte maneira:

- DADOS 0, dados 1, 2 de dados e rede de dados 3 interfaces existiam no dispositivo.
- DADOS 4 e 5 dados placas de interface de rede não estavam presentes; Portanto, elas não são relacionadas na saída.
- DADOS 0 foi ativados.

Controlador de 1 era o controlador de ativo e foi configurado da seguinte maneira:

- DADOS 0, dados 1, 2 de dados, dados 3, 4 de dados e interfaces existiam no dispositivo de rede de dados 5.
- DADOS 0 foi ativados.

**Exemplo de saída – controlador 0**

A seguir é a saída do controlador de 0 (o controlador passivo). DADOS 1, dados 2 e 3 de dados não estão conectado. DADOS 4 e 5 de dados não são listados, porque não estão presentes no dispositivo. 

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Exemplo de saída – controlador 1**

A seguir é a saída do controlador de 1 (o controlador ativo). Somente os dados 0 interface de rede do dispositivo está configurado e funcionando.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent

 
## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Solucionar problemas com o cmdlet de Conexão de teste

Você pode usar o `Test-Connection` cmdlet para determinar se o seu dispositivo StorSimple pode se conectar com a rede externa. Se todos os parâmetros de rede, incluindo o DNS estão configurados corretamente no Assistente de configuração, você pode usar o `Test-Connection` cmdlet para ping um endereço conhecido fora da rede, como o outlook.com. 

Você deve ativar ping solucionar problemas de conectividade com esse cmdlet se ping estiver desativado.

Consulte os seguintes exemplos de saída do `Test-Connection` cmdlet. 

> [AZURE.NOTE] No primeiro exemplo, o dispositivo está configurado com um DNS incorreta. No segundo exemplo, o DNS está correto.
 
**Exemplo de saída – DNS incorreta**

No exemplo a seguir, não há nenhuma saída para os endereços IPV4 e IPV6, que indica que o DNS não for resolvido. Isso significa que há nenhuma conectividade com a rede externa e um DNS correto precisa ser fornecido. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Exemplo de saída – DNS corretos**

No exemplo a seguir, o DNS retorna o endereço IPV4, indicando que o DNS está configurado corretamente. Isso confirma que não há conectividade com a rede externa. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Solucionar problemas com o cmdlet Test-HcsmConnection

Use o `Test-HcsmConnection` cmdlet para um dispositivo que já está conectado ao e registrado com o serviço do Gerenciador de StorSimple. Esse cmdlet ajuda você a verificar a conectividade entre um dispositivo registrado e o serviço de Gerenciador de StorSimple correspondente. Você pode executar este comando no Windows PowerShell para StorSimple. 

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Para executar o cmdlet Test-HcsmConnection

1. Certifique-se de que o dispositivo é registrado.

2. Verificar o status do dispositivo. Se o dispositivo está desativado, no modo de manutenção, ou offline, você poderá ver os seguintes erros: 

   - ErrorCode.CiSDeviceDecommissioned – isso indica que o dispositivo está desativado.
   - ErrorCode.DeviceNotReady – isso indica que o dispositivo está no modo de manutenção.
   - ErrorCode.DeviceNotReady – isso indica que o dispositivo não está online.

3. Verificar se está executando o serviço do Gerenciador de StorSimple (use o cmdlet [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) ). Se o serviço não estiver executando, você poderá ver os seguintes erros:

   - ErrorCode.CiSApplianceAgentNotOnline
   - ErrorCode.CisPowershellScriptHcsError – isso indica que houve uma exceção quando você executou Get-ClusterResource.

4. Verifique o token de serviço de controle de acesso (ACS). Se ele gera uma exceção de web, talvez seja o resultado de um problema de gateway, uma autenticação de proxy ausente, um DNS incorreto ou uma falha de autenticação. Talvez você veja os seguintes erros:

   - ErrorCode.CiSApplianceGateway – isso indica que uma exceção HttpStatusCode.BadGateway: o serviço de resolução de nome não conseguiu resolver o nome de host. 
   - ErrorCode.CiSApplianceProxy – isso indica que uma exceção de HttpStatusCode.ProxyAuthenticationRequired (código de status HTTP 407): o cliente não pôde autenticar com o servidor proxy. 
   - ErrorCode.CiSApplianceDNSError – isso indica que uma exceção WebExceptionStatus.NameResolutionFailure: o serviço de resolução de nome não conseguiu resolver o nome de host.
   - ErrorCode.CiSApplianceACSError – isso indica que o serviço retornou um erro de autenticação, mas há conectividade.
   
    Se ele não lançar uma exceção de web, verifique ErrorCode.CiSApplianceFailure. Isso indica que o dispositivo falhou.

5. Verifique a conectividade de serviço de nuvem. Se o serviço gera uma exceção de web, você poderá ver os seguintes erros:

  - ErrorCode.CiSApplianceGateway – isso indica que uma exceção de HttpStatusCode.BadGateway: um servidor proxy intermediário recebeu uma solicitação inválida de outro proxy ou do servidor original.
  - ErrorCode.CiSApplianceProxy – isso indica que uma exceção de HttpStatusCode.ProxyAuthenticationRequired (código de status HTTP 407): o cliente não pôde autenticar com o servidor proxy. 
  - ErrorCode.CiSApplianceDNSError – isso indica que uma exceção WebExceptionStatus.NameResolutionFailure: o serviço de resolução de nome não conseguiu resolver o nome de host.
  - ErrorCode.CiSApplianceACSError – isso indica que o serviço retornou um erro de autenticação, mas há conectividade.
  
    Se ele não lançar uma exceção de web, verifique ErrorCode.CiSApplianceSaasServiceError. Isso indica um problema com o serviço Gerenciador de StorSimple.
 
6. Verifique a conectividade de barramento de serviço do Azure. ErrorCode.CiSApplianceServiceBusError indica se o dispositivo não pode se conectar o barramento de serviço.
 
O log de arquivos CiSCommandletLog0Curr.errlog e CiSAgentsvc0Curr.errlog terão mais informações, como detalhes de exceção. 

Para obter mais informações sobre como usar o cmdlet, vá para [Teste-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) no Windows PowerShell se referir a documentação.

> [AZURE.IMPORTANT] Você pode executar esse cmdlet para ativo e passivo controlador. 
 
Consulte os seguintes exemplos de saída do `Test-HcsmConnection` cmdlet. 

**Amostra de saída – dispositivo registrado com êxito executando StorSimple versão (julho de 2014)**

O primeiro exemplo é de um dispositivo que está registrado com êxito com o serviço Gerenciador de StorSimple e tem sem problemas de conectividade. 

     Controller1>Test-HcsmConnection -verbose
     Checking device state  ... Success.
     Device registered successfully
     Checking device authentication.  ... This operation will take few minutes to complete....
     Checking device authentication.  ... Success.
     Checking connectivity from device to StorSimple Manager service.  ... This operation will take few minutes to complete....
     Checking connectivity from device to StorSimple Manager service.  ... Success.
     Checking connectivity from StorSimple Manager service to StorSimple device. .... Success.
     Controller1>

**Exemplo de saída – dispositivo registrado com êxito executando StorSimple Update 1**

Se você estiver executando Update 1 em seu dispositivo de StorSimple, não precisará executá-lo com a opção detalhada.

      Controller1>Test-HcsmConnection
       
      Checking device registration state  ... Success
      Device registered successfully
       
      Checking primary NTP server [time.windows.com] ... Success
       
      Checking web proxy  ... NOT SET
       
      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET
       
      Checking device online  ... Success
 
      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success
       
      Checking connectivity from device to service  ... This will take a few minutes.
       
      Checking connectivity from device to service  ... Success
       
      Checking connectivity from service to device  ... Success
       
      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Amostra de saída – dispositivo offline executando StorSimple versão (julho de 2014)**

Este exemplo é em um dispositivo que tenha um status de **off-line** no portal de clássico do Azure.

     Checking device state: Success 
     Device is registered successfully 
     Checking connectivity from device to SaaS.. Failure

O dispositivo não pôde conectar usando a configuração de proxy da web atual. Isso pode ser um problema com a configuração de proxy da web ou um problema de conectividade de rede. Nesse caso, você deverá garantir que as configurações de proxy da web estão corretas e os servidores de proxy da web estão online e acessível. 

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Solucionar problemas com o cmdlet HcsTime de sincronização

Use esse cmdlet para exibir a hora do dispositivo. Se o tempo de dispositivo tem um deslocamento com o servidor NTP, você pode usar esse cmdlet para forçar-sincronizar a hora com o servidor NTP. Se o deslocamento entre o dispositivo e servidor NTP for maior do que 5 minutos, você verá um aviso. Se o deslocamento exceder 15 minutos, o dispositivo irão offline. Você ainda pode usar esse cmdlet para forçar uma sincronização de tempo. No entanto, se o deslocamento exceder 15 horas, em seguida, não será capaz de sincronia forçar o tempo e uma mensagem de erro serão mostrados.

**Exemplo de saída – sincronização de horário forçada usando HcsTime de sincronização**
 
     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.
 
     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Solucionar problemas com os cmdlets HcsPing ativar e desativar HcsPing

Use esses cmdlets para garantir que as interfaces de rede em seu dispositivo respondem a solicitações de ping ICMP. Por padrão as interfaces de rede StorSimple não responder à solicitações de ping. Usar esse cmdlet é a maneira mais fácil saber se seu dispositivo está online e acessível.  

**Exemplo de saída – HcsPing ativar e desativar HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Solucionar problemas com o cmdlet HcsRoute de rastreamento

Use esse cmdlet como uma ferramenta de rastreamento de rota. Ele envia pacotes para cada roteador no caminho para um destino final durante um período de tempo e depois calcula os resultados com base nos pacotes retornados de cada salto. Como o cmdlet mostra o grau de perda de pacotes em qualquer roteador ou link determinado, você pode identificar quais roteadores ou links podem estar provocando problemas de rede.

**Exemplo mostrando como rastrear a rota de um pacote com HcsRoute de rastreamento de saída**

     Controller0>Trace-HcsRoute -Target 10.126.174.25
     
     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]
      
     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]
      
     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Solucionar problemas com o cmdlet Get-HcsRoutingTable

Use esse cmdlet para exibir a tabela de roteamento para o seu dispositivo StorSimple. Uma tabela de roteamento é um conjunto de regras que podem ajudar a determinar onde os pacotes de dados transmitidos em uma rede de protocolo Internet (IP) serão direcionados. 

A tabela de roteamento mostra as interfaces e o gateway que roteia os dados para redes especificadas. Ele também dá a métrica de roteamento que é o criador de decisão para o caminho levado para alcançar um destino específico. Inferior a métrica de roteamento, maior será a preferência. 

Por exemplo, se você tiver 2 interfaces de rede, dados 2 e 3 de dados, conectado à Internet. Se a métrica de roteamento para dados 2 e 3 de dados é 15 e 261 respectivamente, 2 de dados com a menor métrica de roteamento é a interface preferencial usada para acessar a Internet.

Se você estiver executando Update 1 em seu dispositivo de StorSimple, sua interface de rede 0 de dados tem a preferência mais alta para o tráfego de nuvem. Isso significa que, mesmo se houver outras interfaces habilitados para a nuvem, o tráfego de nuvem seria roteado por meio de dados 0. 

Se você executar o `Get-HcsRoutingTable` cmdlet sem especificar quaisquer parâmetros (como mostra o exemplo a seguir), o cmdlet produzirá tabelas de roteamento IPv4 e IPv6. Como alternativa, você pode especificar `Get-HcsRoutingTable -IPv4` ou `Get-HcsRoutingTable -IPv6` para obter uma tabela de roteamento relevante.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================
       
      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================
       
      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None
       
      Controller0>
 
## <a name="step-by-step-storsimple-troubleshooting-example"></a>Exemplo de solução de problemas de StorSimple passo a passo

O exemplo a seguir mostra a solução de problemas passo a passo de uma implantação de StorSimple. No cenário de exemplo, o registro do dispositivo falha com uma mensagem de erro indicando que as configurações de rede ou o nome DNS está incorreto.

A mensagem de erro retornada é:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

O erro pode ser causado por qualquer um destes procedimentos:

- Instalação de hardware incorreto
- Interfaces de rede com defeito
- Endereço IP incorreto, máscara de sub-rede, gateway, servidor DNS primário ou proxy da web
- Chave do registro incorreto
- Configurações de firewall incorretas

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Para localizar e corrigir o problema de registro do dispositivo

1. Verifique a configuração de dispositivo: no controlador de ativos, executar `Invoke-HcsSetupWizard`.

     > [AZURE.NOTE] O Assistente de configuração deve executar no controlador de ativos. Para verificar se você está conectado ao controlador de ativos, examine o banner apresentado no console serial. O banner indica se você está conectado a controlador de 0 ou 1, e se o controlador está ativo ou passivo. Para obter mais informações, vá para [identificar um controlador ativo em seu dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).
 
2. Certifique-se de que o dispositivo está conectado corretamente: verificar a rede cabeamento do dispositivo volta plano. O cabeamento é específico para o modelo do dispositivo. Para obter mais informações, vá para [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

     > [AZURE.NOTE] Se você estiver usando 10 portas GbE de rede, você precisará usar os cabos SFP e adaptadores de QSFP-SFP fornecidos. Para obter mais informações, consulte a [lista de cabos, opções e transceptores recomendados pelo fornecedor OEM para Mellanox portas](http://www.mellanox.com/page/cables?mtag=cable_overview).
 
3. Verificar a integridade da interface de rede:

   - Use o cmdlet Get-NetAdapter para detectar a integridade interfaces de rede dados 0. 
   - Se o link não estiver funcionando, o status de **ifindex** indicará que a interface é pressionada. Em seguida, será necessário verificar a conexão de rede da porta para o dispositivo e a mudança. Você também precisa eliminar cabos incorretos. 
   - Se você suspeitar de que os dados 0 porta no controlador de ativos falhou, você pode confirmar isso conectando-se aos dados porta 0 no controlador 1. Para confirmar isso, desconecte o cabo de rede da parte traseira do dispositivo do controlador 0, conecte o cabo ao controlador 1 e execute o cmdlet Get-NetAdapter novamente. 
   Se os dados 0 porta em um controlador falhar, [entre em contato com o suporte da Microsoft](storsimple-contact-microsoft-support.md) para as próximas etapas. Talvez seja necessário substituir o controlador no seu sistema.
 
4. Verificar a conectividade com a opção:
   - Certifique-se de que interfaces de rede 0 dados controlador 0 e 1 de controlador no seu compartimento primário estejam na mesma sub-rede. 
   - Verifique o hub ou roteador. Normalmente, você deve conectar dois controladores ao mesmo hub ou roteador. 
   - Certifique-se de que as opções que você usar para a conexão tem dados 0 para ambos os controladores na mesma vLAN.
   
5. Elimine os erros de usuário:

  - Execute o Assistente de configuração novamente (execução **Chamar HcsSetupWizard**) e insira os valores novamente para certificar-se de que não existem erros. 
  - Verificar o registro chave usada. A mesma chave do registro pode ser usada para conectar vários dispositivos a um serviço Gerenciador de StorSimple. Use o procedimento em [obter a chave do registro de serviço](storsimple-manage-service.md#get-the-service-registration-key) para garantir que você está usando a chave do registro correto.

    > [AZURE.IMPORTANT] Se você tiver vários serviços em execução, você precisará garantir que a chave do registro para o serviço apropriado é usada para registrar o dispositivo. Se você tiver registrado um dispositivo com o serviço de Gerenciador de StorSimple errado, será necessário [contatar o suporte da Microsoft](storsimple-contact-microsoft-support.md) para as próximas etapas. Você pode precisar executar uma redefinição de fábrica do dispositivo (que pode resultar em perda de dados) e em seguida, conecte-o para o serviço desejado.

6. Use o cmdlet de Conexão de teste para verificar se você tem conectividade com a rede externa. Para obter mais informações, vá para [Solucionar problemas com o cmdlet de Conexão de teste](#troubleshoot-with-the-test-connection-cmdlet).

7. Verificar se há interferência de firewall. Se você tiver confirmado que as configurações de IP (VIP), sub-rede, gateway e DNS virtuais estão corretas e você ainda encontrar problemas de conectividade, é possível que o firewall está bloqueando a comunicação entre o dispositivo e a rede externa. Você precisa garantir que as portas 80 e 443 estão disponíveis em seu dispositivo StorSimple para comunicação de saída. Para obter mais informações, consulte [os requisitos para o seu dispositivo de StorSimple de rede](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

8. Examine os logs. Vá para [pacotes de suporte e logs de dispositivo disponíveis para solução de problemas](#support-packages-and-device-logs-available-for-troubleshooting).

9. Se as etapas anteriores não solucionarem o problema, [entre em contato com o suporte da Microsoft](storsimple-contact-microsoft-support.md) para obter assistência.

## <a name="next-steps"></a>Próximas etapas
[Saiba como solucionar problemas de um dispositivo operacional](storsimple-troubleshoot-operational-device.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
