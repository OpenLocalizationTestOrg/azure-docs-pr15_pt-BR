<properties
   pageTitle="Instalar a atualização 1.2 no seu dispositivo de StorSimple | Microsoft Azure"
   description="Explica como instalar StorSimple 8000 série atualização 1.2 em seu dispositivo de série 8000 StorSimple."
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
   ms.date="08/22/2016"
   ms.author="alkohli" />

# <a name="install-update-12-on-your-storsimple-device"></a>Instalar a atualização 1.2 no seu dispositivo de StorSimple

## <a name="overview"></a>Visão geral

Este tutorial explica como instalar 1.2 de atualização em um dispositivo de StorSimple que está executando uma versão de software antes da atualização 1. O tutorial também aborda as etapas adicionais necessárias para a atualização quando um gateway está configurado em uma interface de rede diferente de dados 0 do dispositivo StorSimple.

Atualização 1.2 inclui atualizações de software do dispositivo, atualizações de driver de LSI e atualizações de firmware de disco. O software e atualizações de driver LSI atualizações sem interrupções e podem ser aplicadas através do portal de clássico Azure. As atualizações de firmware de disco são atualizações interrupções e só podem ser aplicadas por meio da interface do Windows PowerShell do dispositivo.

Dependendo de qual versão do seu dispositivo está executando, você pode determinar se atualização 1.2 será aplicada. Você pode verificar a versão de software do seu dispositivo, navegando até a seção **rapidamente** seu dispositivo **Dashboard**.

</br>

| Se executando a versão de software...   | O que acontece no portal?                              |
|---------------------------------|--------------------------------------------------------------|
| Lançamento - GA                    | Se você estiver executando a versão de lançamento (GA), não aplica essa atualização. Faça a [contatar o suporte da Microsoft](storsimple-contact-microsoft-support.md) para atualizar seu dispositivo.|
| Atualizar 0,1                      | Portal aplica 1.2 de atualização.                                |
| Atualizar 0,2                      | Portal aplica 1.2 de atualização.                                |
| Atualizar 0,3                      | Portal aplica 1.2 de atualização.                                |
| Atualização 1                        | Essa atualização não estará disponível.                           |
| Atualizar 1.1                      | Essa atualização não estará disponível.                           |

</br>

> [AZURE.IMPORTANT]

> -  Talvez você não veja 1.2 atualização imediatamente porque podemos fazer uma distribuição em fases das atualizações. Procurar por atualizações em poucos dias novamente como essa atualização estará disponível em breve.
> - Essa atualização inclui um conjunto de pré-verificações de manuais e automáticas para determinar a integridade do dispositivo em termos de hardware estado e conectividade de rede. Essas pré-verificações são executadas somente se você aplicar as atualizações do Azure portal clássico.
> - Recomendamos que você instale as atualizações de software e driver através do portal de clássico Azure. Você só deve ir para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de pré-atualização gateway falhar no portal. As atualizações podem levar horas de 5 a 10 para instalar (incluindo as atualizações do Windows). As atualizações de modo de manutenção devem ser instaladas por meio da interface do Windows PowerShell do dispositivo. Como as atualizações de modo de manutenção são atualizações interrupções, esses resultará em um tempo de inatividade para seu dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-12-via-the-azure-classic-portal"></a>Instalar atualização 1.2 através do portal de clássico Azure

Execute as seguintes etapas para atualizar o seu dispositivo para [Atualizar 1.2](storsimple-update1-release-notes.md). Use este procedimento somente se você tiver um gateway configurado na interface de rede 0 de dados em seu dispositivo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Verificar se o seu dispositivo está executando **StorSimple 8000 série atualização 1.2 (6.3.9600.17584)**. A **última data de atualização** também devem ser modificadas. Você também verá que atualizações de modo de manutenção estão disponíveis (esta mensagem pode continuar a ser exibida para até 24 horas depois de instalar as atualizações).

    Atualizações de modo de manutenção são interrupções atualizações que resultam em tempo de inatividade do dispositivo e só podem ser aplicadas por meio da interface do Windows PowerShell do seu dispositivo.

    ![Página de manutenção] (./media/storsimple-install-update-1/InstallUpdate12_10M.png "Página de manutenção")

13. Baixe as atualizações de modo de manutenção usando as etapas listadas na [Baixar hotfixes]( #to-download-hotfixes) para pesquisar e baixar KB3063416, quais atualizações de firmware de disco instalações (as outras atualizações já devem estar instaladas agora).

13. Siga as etapas indicadas no [instalar e verificar hotfixes do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar as atualizações de modo de manutenção.

14. No portal do Azure clássico, navegue até a página de **manutenção** e na parte inferior da página, clique em **Verificar atualizações** para verificar se há alguma atualização do Windows e clique em **Instalar atualizações**. Terminar após todas as atualizações são instaladas com êxito.



## <a name="install-update-12-on-a-device-that-has-a-gateway-configured-for-a-non-data-0-network-interface"></a>Instalar a atualização 1.2 em um dispositivo que tenha um gateway configurado para uma interface de rede 0 sem dados

Você deve usar este procedimento somente se a verificação de gateway é falhar ao tentar instalar as atualizações por meio do portal de clássico Azure. A verificação falhar enquanto você tem um gateway atribuído a uma interface de rede de 0 sem dados em seu dispositivo está executando uma versão de software antes da atualização 1. Se seu dispositivo não tiver um gateway em uma interface de rede 0 sem dados, você pode atualizar seu dispositivo diretamente do Azure portal clássico. Consulte [instalar atualizar 1.2 através do portal de clássico Azure](#install-update-1.2-via-the-azure-classic-portal).

As versões de software que podem ser atualizadas usando esse método são atualização do 0,1 0.2 e atualização 0,3.


> [AZURE.IMPORTANT]
>
> - Se seu dispositivo estiver executando a versão de lançamento (GA), entre em contato com o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudá-lo com a atualização.
> - Este procedimento deve ser executado apenas uma vez para aplicar 1.2 de atualização. Você pode usar o portal de clássico Azure para aplicar atualizações subsequentes.

Se seu dispositivo está funcionando pré-atualização 1 o software e possui um gateway definido para uma interface de rede diferente de dados 0, você pode aplicar 1.2 de atualização em duas maneiras a seguir:

- **Opção 1**: baixar a atualização e aplicá-lo usando o `Start-HcsHotfix` cmdlet da interface do Windows PowerShell do dispositivo. Este é o método recomendado. **Não use esse método para aplicar atualização 1.2 se seu dispositivo estiver executando Update 1.0 ou 1.1 de atualização.**

- **Opção 2**: remover a configuração do gateway e instale a atualização diretamente do Azure portal clássico.


Instruções detalhadas para cada um desses são fornecidas nas seções a seguir.

## <a name="option-1-use-windows-powershell-for-storsimple-to-apply-update-12-as-a-hotfix"></a>Opção 1: Usar o Windows PowerShell para StorSimple aplicar atualização 1.2 como um hotfix

Você deve usar este procedimento somente se você estiver executando atualização 0.1, 0.2, 0.3 e se a sua seleção de gateway falhou ao tentar instalar atualizações do portal clássico do Azure. Se você estiver executando o software de lançamento (GA), entre o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para atualizar seu dispositivo.

Para instalar a atualização 1.2 como um hotfix, você deve baixar e instalar os seguintes hotfixes:

| Ordem  | KB        | Descrição             | Tipo de atualização  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3063418 | Atualização de software         |  Regular     |
| 2      | KB3043005 | Atualização de controlador SAS LSI |  Regular     |
| 3      | KB3063416 | Firmware do disco           | Manutenção  |

Antes de usar este procedimento para aplicar a atualização, verifique se:

- Ambos os controladores de dispositivo estão online.

Execute as seguintes etapas para aplicar 1.2 de atualização. **As atualizações podem levar cerca de 2 horas (aproximadamente 30 minutos para software, 30 minutos para driver de 45 minutos para firmware do disco).**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## <a name="option-2-use-the-azure-classic-portal-to-apply-update-12-after-removing-the-gateway-configuration"></a>Opção 2: Usar o portal de clássico Azure para aplicar atualização 1.2 depois de remover a configuração do gateway

Este procedimento se aplica somente a dispositivos de StorSimple que estão executando uma versão de software anteriores a 1 de atualização e tem um gateway configurar em uma interface de rede diferente de dados 0. Você precisará limpar a configuração do gateway antes de aplicar a atualização.

A atualização pode levar algumas horas para ser concluída. Se seus hosts estiverem em sub-redes diferentes, removendo a configuração do gateway nas interfaces iSCSI pode resultar em tempo de inatividade. Recomendamos que você configure dados 0 para tráfego iSCSI para reduzir o tempo de inatividade.

Execute as seguintes etapas para desativar a interface de rede com o gateway e aplique a atualização.

[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [versão 1.2 de atualização](storsimple-update1-release-notes.md).
