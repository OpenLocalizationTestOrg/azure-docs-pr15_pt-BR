<properties
   pageTitle="Instalar a atualização 2 no seu dispositivo de StorSimple | Microsoft Azure"
   description="Explica como instalar o StorSimple 8000 série 2 em seu dispositivo de série 8000 StorSimple."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="install-update-2-on-your-storsimple-device"></a>Instalar a atualização 2 no seu dispositivo de StorSimple

## <a name="overview"></a>Visão geral

Este tutorial explica como instalar a atualização 2 em um dispositivo de StorSimple executando uma versão anterior do software através do portal de clássico Azure. O tutorial também aborda as etapas necessárias para a atualização quando um gateway é configurado em uma interface de rede diferente de dados 0 do dispositivo StorSimple e você está tentando atualizar de uma versão de software pré-atualização 1.

Atualização 2 inclui atualizações de software do dispositivo, atualizações de driver LSI e atualizações de firmware de disco. O software de dispositivo e atualizações de LSI atualizações sem interrupções e podem ser aplicadas através do portal de clássico Azure. As atualizações de firmware de disco são atualizações interrupções e só podem ser aplicadas por meio da interface do Windows PowerShell do dispositivo.

> [AZURE.IMPORTANT]

> -  Talvez você não veja 2 de atualização imediatamente porque podemos fazer uma distribuição em fases das atualizações. Procurar por atualizações em poucos dias novamente como essa atualização estará disponível em breve.
> - Pré-verificações de automáticas e manuais de terminar antes da instalação para determinar a integridade do dispositivo em termos de hardware estado e conectividade de rede. Essas pré-verificações são executadas somente se você aplicar as atualizações do Azure portal clássico.
> - Recomendamos que você instale as atualizações de software e driver através do portal de clássico Azure. Você só deve ir para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de pré-atualização gateway falhar no portal. As atualizações podem levar horas de 4 a 7 para instalar (incluindo as atualizações do Windows). As atualizações de modo de manutenção devem ser instaladas por meio da interface do Windows PowerShell do dispositivo. Como as atualizações de modo de manutenção são atualizações interrupções, esses resultará em um tempo de inatividade para seu dispositivo.
> - Se executando o Gerenciador de instantâneo StorSimple opcionais, certifique-se de que você atualizou sua versão do Gerenciador de instantâneo para atualização 2 antes da atualização do dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-2-via-the-azure-classic-portal"></a>Instalar a atualização 2 através do portal de clássico Azure

Execute as seguintes etapas para atualizar o seu dispositivo para [Update 2](storsimple-update2-release-notes.md).


> [AZURE.NOTE]
Atualização 2 permite que a Microsoft obter informações adicionais de diagnóstico do dispositivo. Como resultado, quando nossa equipe de operações identifica dispositivos que estão tendo problemas, estamos melhor equipados para coletar informações do dispositivo e diagnosticar problemas. Aceitando atualização 2, você nos permite fornecer este suporte proativo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Verificar se o seu dispositivo está executando **StorSimple 8000 série atualização 2 (6.3.9600.17673)**. A **última data de atualização** também devem ser modificadas. Você também verá que atualizações de modo de manutenção estão disponíveis (esta mensagem pode continuar a ser exibida para até 24 horas depois de instalar as atualizações).

    Atualizações de modo de manutenção são interrupções atualizações que resultam em tempo de inatividade do dispositivo e só podem ser aplicadas por meio da interface do Windows PowerShell do seu dispositivo. Em alguns casos quando você estiver executando 1.2 de atualização, seu firmware de disco pode já estar atualizado, caso no qual você não precisa instalar as atualizações de modo de manutenção.

13. Baixe as atualizações de modo de manutenção usando as etapas listadas na [Baixar hotfixes](#to-download-hotfixes) para pesquisar e baixar KB3121899, quais atualizações de firmware de disco instalações (as outras atualizações já devem estar instaladas agora).

13. Siga as etapas indicadas no [instalar e verificar hotfixes do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar as atualizações de modo de manutenção.


## <a name="install-update-2-as-a-hotfix"></a>Instale a atualização 2 como um hotfix

Use este procedimento se você não a verificação de gateway ao tentar instalar as atualizações por meio do portal de clássico Azure. A verificação falhar enquanto você tem um gateway atribuído a uma interface de rede de 0 sem dados em seu dispositivo está executando uma versão de software antes da atualização 1.

As versões de software que podem ser atualizadas usando o método de hotfix são atualização 0,1, 0,2, atualização e atualização 0,3, Update 1, atualização 1.1 e 1.2 de atualização. O método de hotfix envolve estas três etapas:

- Baixe os hotfixes do catálogo do Microsoft Update.
- Instalar e verifique se os hotfixes de modo normal.
- Instalar e verifique se o hotfix de modo de manutenção.

Para instalar a atualização 2 como um hotfix, você deve baixar e instalar os seguintes hotfixes:

| Ordem  | KB        | Descrição                    | Tipo de atualização  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3121901 | Atualização de software         |  Regular     |
| 2      | KB3121900 | Driver LSI              |  Regular     |
| 3      | KB3080728 | Correção de Storport </br> Windows Server 2012 R2 |  Regular     |
| 4      | KB3090322 | Correção de spaceport </br> Windows Server 2012 R2 |  Regular     |
| 5      | KB3121899 | Firmware do disco           | Manutenção  |


> [AZURE.IMPORTANT]
>
> - Se seu dispositivo estiver executando a versão de lançamento (GA), entre em contato com o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudá-lo com a atualização.
> - Este procedimento deve ser executado apenas uma vez para aplicar atualização 2. Você pode usar o portal de clássico Azure para aplicar atualizações subsequentes.
> - Cada instalação pode levar cerca de 20 minutos para ser concluída. Tempo total de instalação for próximo 2 horas.
> - Antes de usar este procedimento para aplicar a atualização, certifique-se de que ambos os controladores de dispositivo estão online e todos os componentes de hardware são íntegros.

Execute as seguintes etapas para aplicar essa atualização como um hotfix.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]



## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [lançamento de atualização 2](storsimple-update2-release-notes.md).
