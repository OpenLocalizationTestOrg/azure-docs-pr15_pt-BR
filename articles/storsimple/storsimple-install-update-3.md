<properties
   pageTitle="Instalar a atualização 3 em seu dispositivo de StorSimple | Microsoft Azure"
   description="Explica como instalar o StorSimple 8000 série atualização 3 em seu dispositivo de série 8000 StorSimple."
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
   ms.date="10/05/2016"
   ms.author="alkohli" />

# <a name="install-update-3-on-your-storsimple-device"></a>Instalar a atualização 3 em seu dispositivo de StorSimple

## <a name="overview"></a>Visão geral

Este tutorial explica como instalar a atualização 3 em um dispositivo de StorSimple executando uma versão anterior do software através do portal de clássico Azure e usando o método de hotfix. O método de hotfix é usado quando um gateway é configurado em uma interface de rede diferente de dados 0 do dispositivo StorSimple e você está tentando atualizar de uma versão de software pré-atualização 1.

Atualização 3 inclui o software do dispositivo, driver LSI e firmware, Storport e Spaceport atualizações. Se estiver atualizando de atualização 2 ou uma versão anterior, também será necessário para aplicar iSCSI, WMI e em alguns casos, atualizações de firmware de disco. O software do dispositivo, WMI, iSCSI, LSI driver, Spaceport e Storport correções atualizações sem interrupções e podem ser aplicadas através do portal de clássico Azure. As atualizações de firmware de disco são atualizações interrupções e só podem ser aplicadas por meio da interface do Windows PowerShell do dispositivo. 

> [AZURE.IMPORTANT]

> - Pré-verificações de automáticas e manuais de terminar antes da instalação para determinar a integridade do dispositivo em termos de hardware estado e conectividade de rede. Essas pré-verificações são executadas somente se você aplicar as atualizações do Azure portal clássico.
> - Recomendamos que você instale as atualizações de software e driver através do portal de clássico Azure. Você só deve ir para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de pré-atualização gateway falhar no portal. Dependendo da versão que você está atualizando do, as atualizações podem levar horas de 1,5-2,5 para instalar. As atualizações de modo de manutenção devem ser instaladas por meio da interface do Windows PowerShell do dispositivo. Como as atualizações de modo de manutenção são atualizações interrupções, esses resultará em um tempo de inatividade para seu dispositivo.
> - Se executando o Gerenciador de instantâneo StorSimple opcionais, certifique-se de que você atualizou sua versão do Gerenciador de instantâneo para atualização 2 antes da atualização do dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-3-via-the-azure-classic-portal"></a>Instale a atualização 3 através do portal de clássico Azure

Execute as seguintes etapas para atualizar o seu dispositivo para [Atualizar 3](storsimple-update3-release-notes.md).


> [AZURE.NOTE]
Se você estiver aplicando atualização 2 ou posterior (incluindo atualização 2.1), Microsoft poderá retirar informações adicionais de diagnóstico do dispositivo. Como resultado, quando nossa equipe de operações identifica dispositivos que estão tendo problemas, estamos melhor equipados para coletar informações do dispositivo e diagnosticar problemas. Aceitando atualização 2 ou posterior, você nos permite fornecer este suporte proativo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Verificar se o seu dispositivo está executando **StorSimple 8000 série atualização 3 (6.3.9600.17759)**. A **última data de atualização** também devem ser modificadas. 

    Se você estiver atualizando de uma versão antes da atualização 2, você também verá que as atualizações de modo de manutenção estão disponíveis (esta mensagem pode continuar a ser exibida para até 24 horas depois de instalar as atualizações).

    Atualizações de modo de manutenção são interrupções atualizações que resultam em tempo de inatividade do dispositivo e só podem ser aplicadas por meio da interface do Windows PowerShell do seu dispositivo. Em alguns casos quando você estiver executando 1.2 de atualização, seu firmware de disco pode já estar atualizado, caso no qual você não precisa instalar as atualizações de modo de manutenção.

    Se você estiver atualizando de atualização 2 ou posterior, seu dispositivo agora deve ser atualizado. Você pode ignorar as etapas restantes.

13. Baixe as atualizações de modo de manutenção usando as etapas listadas na [Baixar hotfixes](#to-download-hotfixes) para pesquisar e baixar KB3121899, quais atualizações de firmware de disco instalações (as outras atualizações já devem estar instaladas agora).

13. Siga as etapas indicadas no [instalar e verificar hotfixes do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar as atualizações de modo de manutenção. 

  

## <a name="install-update-3-as-a-hotfix"></a>Instale a atualização 3 como um hotfix

Use este procedimento se você não a verificação de gateway ao tentar instalar as atualizações por meio do portal de clássico Azure. A verificação falhar enquanto você tem um gateway atribuído a uma interface de rede de 0 sem dados em seu dispositivo está executando uma versão de software antes da atualização 1.

As versões de software que podem ser atualizadas usando o método de hotfix são:

- Atualizar 0.1, 0.2, 0.3
- Atualizar 1, 1.1, 1.2
- Atualização 2, 2.1, 2.2 

> [AZURE.IMPORTANT]
>
> - Se seu dispositivo estiver executando a versão de lançamento (GA), entre em contato com o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudá-lo com a atualização.

O método de hotfix envolve estas três etapas:

1.  Baixe os hotfixes do catálogo do Microsoft Update.

2.  Instalar e verifique se os hotfixes de modo normal.

3.  Instalar e verifique se o hotfix de modo de manutenção (somente quando a atualização do software de 2 de pré-atualização).


#### <a name="download-updates-for-your-device"></a>Baixar atualizações para o seu dispositivo

**Se seu dispositivo estiver executando 2.1 de atualização ou 2.2**, você deve baixar e instalar os seguintes hotfixes na ordem indicada:

| Ordem  | KB        | Descrição                    | Tipo de atualização  | Tempo de instalação |
|--------|-----------|-------------------------|------------- |-------------|
| 1.      | KB3186843 | Atualização de software & 42;  |  Regular <br></br>Sem interrupções     | ~ 45 min |
| 2.      | KB3186859 | Firmware e driver LSI             |  Regular <br></br>Sem interrupções      | ~ 20 min |
| 3.      | KB3121261 | Correção Storport e Spaceport </br> Windows Server 2012 R2 |  Regular <br></br>Sem interrupções      | ~ 20 min |

& 42;  Atualização de *anotação, software consiste em dois arquivos binários: atualização de software do dispositivo exibirá `all-hcsmdssoftwareupdate` e o agente Cis e Mds exibirá `all-cismdsagentupdatebundle`. A atualização de software do dispositivo deve ser instalada antes do agente Cis e Mds. Também será necessário reiniciar o controlador de ativo por meio do `Restart-HcsController` cmdlet após você aplicar o agente Cis e Mds atualizar (e antes de aplicar o restante atualiza).* 


**Se seu dispositivo está executando atualização 0.1, 0.2, 0.3, 1.0, 1.1, 1.2 ou 2.0**, você deve baixar e instalar os seguintes hotfixes além do driver LSI, atualizações de firmware (mostradas na tabela anterior) e software na ordem indicada:

| Ordem  | KB        | Descrição                    | Tipo de atualização  | Tempo de instalação |
|--------|-----------|-------------------------|------------- |-------------|
| 4.      | KB3146621 | pacote iSCSI | Regular <br></br>Sem interrupções  | ~ 20 min |
| 5.      | KB3103616 | Pacote WMI |  Regular <br></br>Sem interrupções      | ~ min 12 |


<br></br>

**Se seu dispositivo está executando versões 0.2, 0.3, 1.0, 1.1 e 1.2**, você também precisa instalar atualizações de firmware de disco na parte superior de todas as atualizações mostradas nas tabelas anteriores. Você pode verificar se você precisa as atualizações de firmware de disco executando o `Get-HcsFirmwareVersion` cmdlet. Se você estiver executando estas versões de firmware: `XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08`, então você não precisa instalar estas atualizações.


| Ordem  | KB        | Descrição                    | Tipo de atualização  | Tempo de instalação |
|--------|-----------|-------------------------|------------- |-------------|
| 6.      | KB3121899 | Firmware do disco              |  Manutenção <br></br>Interrupções      | ~ 30 minutos |
 
<br></br>

> [AZURE.IMPORTANT]
>
> - Este procedimento deve ser executado apenas uma vez para aplicar atualização 3. Você pode usar o portal de clássico Azure para aplicar atualizações subsequentes.
> - Se estiver atualizando do 2.2 de atualização, o tempo de instalação total é próximo 1.1 horas.
> - Antes de usar este procedimento para aplicar a atualização, certifique-se de que tanto os controladores de dispositivo estão online e todos os componentes de hardware são íntegros.

Execute as seguintes etapas para baixar e instalar os hotfixes.

[AZURE.INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [lançamento de atualização 3](storsimple-update3-release-notes.md).
