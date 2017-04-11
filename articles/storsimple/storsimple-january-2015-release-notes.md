<properties 
   pageTitle="Notas da versão 0.2 StorSimple 8000 atualização | Microsoft Azure"
   description="Descreve os novos recursos e correções, questões abertas e soluções alternativas disponíveis de janeiro de 2015 lançamento do Microsoft Azure StorSimple (atualização de 0,2)."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/16/2016"
   ms.author="v-sharos" />


# <a name="storsimple-8000-series-update-02-release-notes---january-2015"></a>Atualização de série 8000 StorSimple 0.2 notas - janeiro de 2015

## <a name="overview"></a>Visão geral

As seguintes notas de versão identificam os problemas críticos abrir a versão de janeiro de 2015 do Microsoft Azure StorSimple. Eles também contêm uma lista das atualizações de software e firmware StorSimple incluídos nesta versão. Esta é a versão de segunda depois que a versão de lançamento do StorSimple 8000 série foi disponibilizada em julho de 2014.
  
Essa atualização não altera a versão do software de dispositivo físico da atualização outubro. Ele continua a ser versão 6.3.9600.17312. A imagem usada pela imagem de dispositivo virtual foi alterado nesta versão. Portanto, todos os dispositivos de virtuais novo criados após 20/1/2015 mostrará a versão 6.3.9600.17361.  

Revise as seguintes informações contidas nas notas de versão para a atualização de janeiro de 2015.

> [AZURE.IMPORTANT]  
>
>- Essa atualização não está disponível no Windows Update e não pode ser instalada como outras atualizações. Seu dispositivo não receberão essa atualização mesmo se você aplicou as atualizações usando o portal de clássico Azure. Essa atualização será aplicada somente a dispositivos virtuais criados após 20 de janeiro de 2015. 
> 
>- Na versão de janeiro de StorSimple não contém todas as atualizações para o dispositivo físico StorSimple. Você ainda poderá aplicar as atualizações disponíveis do Windows para o dispositivo virtual, incluindo recentes correções de segurança, mas você não verá uma alteração na versão para o dispositivo físico StorSimple.

## <a name="whats-new-in-the-january-release"></a>O que há de novo na versão de janeiro

Essa atualização contém uma correção relacionada aos volumes de ficar offline do dispositivo virtual. (Consulte [problemas corrigidos nesta versão](#issues-fixed-in-the-january-release)).  

A atualização não contém novos recursos ou funcionalidade.  

## <a name="issues-fixed-in-the-january-release"></a>Problemas corrigidos na versão de janeiro

A tabela a seguir descreve o problema que foi corrigido nessa atualização.

|Não.|Recurso|Problema|Aplica-se ao dispositivo físico|Aplica-se ao dispositivo virtual 
|---|-------|-----|--------------------------|-------------------------
|1|Volumes de ficar offline|Ao latências de nuvem alta persistirem por vários minutos, os volumes de dispositivo virtual StorSimple ficar offline nos hosts. Este fix aumenta o limite para latências de nuvem, minimizando as situações que faria com que os volumes de ficar offline em hosts.|Não|Sim  

## <a name="known-issues-in-the-january-release"></a>Problemas conhecidos na versão de janeiro

A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.
 
|Não.|Recurso|Problema|Comentários/solução alternativa|Aplica-se ao dispositivo físico|Aplica-se ao dispositivo virtual 
|---|-------|-----|-------------------|--------------------------|-------------------------
|1| Redefinição de fábrica|  Em alguns casos, quando você executa uma redefinição de fábrica, o dispositivo StorSimple podem estar presas e exibir esta mensagem: **Redefinir para fábrica está em andamento (fase 8).** Isso acontece se você pressionar CTRL + C enquanto o cmdlet está em andamento.| Não pressione CTRL + C após iniciar uma redefinição de fábrica. Se você já estiver nesse estado, contate o Microsoft Support para as próximas etapas.|Sim|Não|
|2|Quorum de disco| Em casos raros, se a maioria dos discos no compartimento EBOD de um dispositivo 8600 estiver desconectada, resultando em quorum sem disco, então o pool de armazenamento será offline. Ela permanecerá offline, mesmo se os discos estão reconectados.|Você precisará reiniciar o dispositivo. Se o problema persistir, contate o Microsoft Support para as próximas etapas.|Sim |Não
|3|Falhas de instantâneo de nuvem|Em casos raros, um instantâneo de nuvem pode falhar com o erro de **limite de backup máximo atingido**. Isso ocorre se você exceder 255 clones onlinehttps no mesmo dispositivo, do mesmo volume original que foi excluído.||Sim|Sim
|4|Identificação do controlador incorreto|Quando uma substituição de controlador é executada, controlador 0 pode aparecer como controlador 1. Durante a substituição de controlador, quando a imagem é carregada do nó ponto, a identificação do controlador pode aparecer inicialmente como ID. do controlador de ponto  Em casos raros, esse comportamento também pode ser visto após a reinicialização do sistema.|Nenhuma ação é necessária. Essa situação se resolverá após a substituição de controlador é concluída.|Sim|Não 
|5| Dispositivo monitorando gráficos|No serviço StorSimple Manager, os gráficos de monitoramento do dispositivo não funcionam quando básica ou a autenticação NTLM está habilitada na configuração do servidor proxy para o dispositivo.|Modificar a configuração de proxy da web para o dispositivo registrado com o serviço do Gerenciador de StorSimple para que a autenticação está definida como nenhum. Para fazer isso, execute do Windows PowerShell para o cmdlet Set-HcsWebProxy StorSimple.|Sim|Sim
|6| Contas de armazenamento|Usando o serviço de armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso levará a uma situação na qual os dados do usuário não podem ser recuperados.|| Sim |  Sim
|7|Failover de dispositivo| Não há suporte para vários failovers de um contêiner de volume do dispositivo de origem mesmo para diferentes dispositivos de destino.| Failover de um único dispositivo inativo em vários dispositivos tornará os contêineres de volume na primeira falha sobre dispositivo perdem a propriedade de dados. Após o failover, esses contêineres de volume aparecerá ou se comportam de forma diferente quando você vê-los no portal de clássico do Azure.|Sim|Não
|8| Instalação|Durante StorSimple adaptador de instalação do SharePoint, você precisa fornecer um IP de dispositivo, para que a instalação concluir com êxito.||Sim|Não
|9| Proxy da Web|Se sua configuração de proxy web tiver HTTPS como o protocolo especificado, sua comunicação de serviço do dispositivo será afetada e o dispositivo irão offline. Pacotes de suporte também serão gerados no processo, consumindo recursos significativos no seu dispositivo.|Garantir que a URL da web proxy HTTP como o protocolo especificado. Consulte mais informações sobre como [Configurar o proxy de web para o seu dispositivo](storsimple-configure-web-proxy.md).|Sim |Não
|10|Proxy da Web|  Se você configurar e habilitar o proxy da web em um dispositivo registrado, você precisará reiniciar o controlador de ativo no seu dispositivo.|| Sim |Não
|11|Nuvem de alta latência e alta carga de trabalho e/s|Quando seu dispositivo StorSimple encontra uma combinação de latências de nuvem muito alto (ordem de segundos) e alta carga de trabalho e/s, os volumes de dispositivo entram em um estado degradado e as i/os pode falhar com um erro "o dispositivo não está pronto".|Você precisará reinicializar os controladores de dispositivo ou realizar um failover de dispositivo para recuperar dessa situação manualmente.|Sim|Não

## <a name="physical-device-updates-in-the-january-release"></a>Atualizações de dispositivo físico na janeiro versão

Essa atualização não contém quaisquer outras alterações no dispositivo StorSimple.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-january-release"></a>Controlador de SCSI (SAS) anexados a série e atualizações de firmware na janeiro de lançamento

Esta versão não contém todas as atualizações para o controlador de SCSI (SAS) anexados a série ou o firmware. A atualização de driver foi em outubro, lançamento de 2014. 

## <a name="virtual-device-updates-in-the-january-release"></a>Atualizações de dispositivo virtual na janeiro versão

Esta versão contém uma imagem atualizada para o dispositivo virtual. Todos os dispositivos virtuais criados após 20 de janeiro de 2015 mostrará a versão do software 6.3.9600.17361.

 
