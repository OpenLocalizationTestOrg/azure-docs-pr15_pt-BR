<properties 
   pageTitle="Notas da versão 0,3 StorSimple 8000 atualização | Microsoft Azure"
   description="Descreve os novos recursos e correções, questões abertas e soluções alternativas disponíveis para a fevereiro de 2015 lançamento do Microsoft Azure StorSimple (atualização de 0,3)."
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-update-03-release-notes---february-2015"></a>Atualização de série 8000 StorSimple 0,3 notas - fevereiro de 2015

## <a name="overview"></a>Visão geral

As seguintes notas identificam as questões abertas críticas para atualização de série StorSimple 8000 0,3 lançado em fevereiro de 2015. Eles também contêm uma lista das atualizações de software e firmware StorSimple incluídos nesta versão. Este é o terceiro lançamento após a versão de lançamento do StorSimple 8000 série foi disponibilizada em julho de 2014.
  
Essa atualização não altera a versão do software de dispositivo da atualização janeiro. Ele continua a ser versão 6.3.9600.17312. Você pode confirmar que a atualização foi instalada marcando a data da **Última atualização** . Se a data for 2/10/2015 ou posterior, a atualização foi instalada com êxito.  

Recomendamos que você procurar e aplique as atualizações disponíveis imediatamente após a instalação do seu dispositivo de StorSimple. Você também pode ativar atualizações automáticas para baixar e instalar atualizações de alta prioridade da Microsoft, assim que eles são lançados. Para obter mais informações, consulte [atualizar seu dispositivo StorSimple](storsimple-update-device.md).  

Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução de StorSimple.  

>[AZURE.IMPORTANT]   
>
> - Use o serviço de Gerenciador de StorSimple e não o Windows PowerShell para StorSimple para instalar a atualização de fevereiro.   
> - Leva aproximadamente uma hora para instalar essa atualização. No entanto, se você estiver instalando atualizações cumulativas, o processo pode levar cerca de 3 horas para ser concluída.  
> - Versão de fevereiro do StorSimple não contém todas as atualizações para o dispositivo virtual StorSimple. Você ainda poderá aplicar as atualizações disponíveis do Windows para o dispositivo virtual, incluindo recentes correções de segurança, mas você não verá uma alteração na versão para o dispositivo virtual.  

Certifique-se de que os seguintes pré-requisitos são atendidos antes de atualizar o seu dispositivo StorSimple.  

- Certifique-se de que ambos os controladores de dispositivo estão executando antes de verificar se há atualizações. Se o controlador não estiver executando, a verificação falhará. Para verificar se os controladores estão em um estado de integridade, navegue até o **Status de Hardware** sob a página de **manutenção** . Se houver componentes de que **precisa de atenção**, contate o Microsoft Support antes de continuar qualquer.
- Verifique se IPs fixo para controlador de 0 e controlador 1 são roteáveis e pode se conectar à Internet como elas são usadas para manutenção as atualizações para o dispositivo. Você pode usar o [cmdlet de Conexão de teste](https://technet.microsoft.com/library/hh849808.aspx) para ping um endereço conhecido fora da rede, como o outlook.com, para verificar se o controlador tem conectividade com a rede externa.
- Certifique-se de que as portas 80 e 443 estão disponíveis em seu dispositivo StorSimple para comunicação de saída. Para obter mais informações, consulte os [requisitos para o seu dispositivo de StorSimple de rede](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
- Se a versão do software de dispositivo é mais antiga que 6.3.9600.17312 (atualização de outubro de 2014), desative as portas de dados 2 e 3 de dados, se habilitada, antes de iniciar a atualização. Deixando a Data 2 ou 3 de dados portas habilitadas quando você aplicar a atualização pode causar seu controlador de dispositivo ir para o modo de recuperação. Observe que, quando você desativa as interfaces de rede, todos os volumes associados serão colocados offline e as saídas será interrompida para a duração da atualização.  
  
## <a name="whats-new-in-the-february-release"></a>O que há de novo na versão de fevereiro

Essa atualização contém uma correção para redefinição de fábrica problema que ocorreu no dispositivos que tinham sido atualizados do GA de lançamento para a versão de outubro de 2014. Para obter mais informações, consulte [problemas corrigidos nesta versão](#issues-fixed-in-the-february-release).   

Essa atualização não contém novos recursos ou funcionalidade.  

## <a name="issues-fixed-in-the-february-release"></a>Problemas corrigidos na versão de fevereiro

A tabela a seguir descreve o problema que foi corrigido nessa atualização.  
 
| Não. | Recurso | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|---------------------------------|-------------------------------|
| 1 | Redefinição de fábrica | Você tenta executar uma fábrica redefinir em um dispositivo que originalmente tinha o lançamento de GA (versão 6.3.9600.17215) instalado, mas foi atualizado para o outubro lançamento (versão 6.3.9600.17312). Fábrica redefinir falhar e o dispositivo se torna instável. | Sim | Não |


## <a name="known-issues-in-the-february-release"></a>Problemas conhecidos na versão de fevereiro

A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.
 
| Não. | Recurso | Problema | Comentários/solução alternativa | Aplica-se ao dispositivo físico  | Aplica-se ao dispositivo virtual |
|-----|---------|-------|----------------------------|-----------------------------|--------------------------|
| 1 | Redefinição de fábrica | Em alguns casos, quando você executa uma redefinição de fábrica, o dispositivo StorSimple podem estar presas e exibir esta mensagem: **Redefinir para fábrica está em andamento (fase 8)**. Isso acontece se você pressionar CTRL + C enquanto o cmdlet está em andamento. | Não pressione CTRL + C após iniciar uma redefinição de fábrica. Se você já estiver nesse estado, contate o Microsoft Support para as próximas etapas. | Sim | Não |
| 2 | Quorum de disco | Em casos raros, se a maioria dos discos no compartimento EBOD de um 8600device estiver desconectada, resultando em quorum sem disco, então o pool de armazenamento será offline. Ela permanecerá offline, mesmo se os discos estão reconectados. | Você precisará reiniciar o dispositivo. Se o problema persistir, contate o Microsoft Support para as próximas etapas. | Sim | Não |
| 3 | Falhas de instantâneo de nuvem | Em casos raros, um instantâneo de nuvem pode falhar com o erro de **limite de backup máximo atingido**. Isso ocorre se você exceder 255 clones onlinehttps no mesmo dispositivo, do mesmo volume original que foi excluído. |  | Sim | Sim |
| 4 | Identificação do controlador incorreto | Quando uma substituição de controlador é executada, controlador 0 pode aparecer como controlador 1. Durante a substituição de controlador, quando a imagem é carregada do nó ponto, a identificação do controlador pode aparecer inicialmente como ID. do controlador de ponto Em casos raros, esse comportamento também pode ser visto após a reinicialização do sistema. | Nenhuma ação é necessária. Essa situação se resolverá após a substituição de controlador é concluída. | Sim | Não |
| 5 | Dispositivo monitorando gráficos | No serviço StorSimple Manager, os gráficos de monitoramento do dispositivo não funcionam quando básica ou a autenticação NTLM está habilitada na configuração do servidor proxy para o dispositivo. | Modificar a configuração de proxy da web para o dispositivo registrado com o serviço do Gerenciador de StorSimple para que a autenticação está definida como nenhum. Para fazer isso, execute do Windows PowerShell para o cmdlet Set-HcsWebProxy StorSimple. | Sim | Sim |
| 6 | Contas de armazenamento | Usando o serviço de armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso levará a uma situação na qual os dados do usuário não podem ser recuperados. |  | Sim | Sim |
| 7 | Failover de dispositivo | Não há suporte para vários failovers de um contêiner de volume do dispositivo de origem mesmo para diferentes dispositivos de destino.  Failover de um único dispositivo inativo em vários dispositivos tornará os contêineres de volume na primeira falha sobre dispositivo perdem a propriedade de dados. Após o failover, esses contêineres de volume aparecerá ou se comportam de forma diferente quando você vê-los no portal de clássico do Azure. |   | Sim | Não |
| 8 | Instalação | Durante StorSimple adaptador de instalação do SharePoint, você precisa fornecer um IP de dispositivo, para que a instalação concluir com êxito. |  | Sim | Não |
| 9 | Proxy da Web | Se sua configuração de proxy web tiver HTTPS como o protocolo especificado, sua comunicação de serviço do dispositivo será afetada e o dispositivo irão offline. Pacotes de suporte também serão gerados no processo, consumindo recursos significativos no seu dispositivo. | Garantir que a URL da web proxy HTTP como o protocolo especificado. Mais informações sobre como [Configurar o proxy de web para o seu dispositivo](storsimple-configure-web-proxy.md). | Sim | Não |
| 10 | Proxy da Web | Se você configurar e habilitar o proxy da web em um dispositivo registrado, você precisará reiniciar o controlador de ativo no seu dispositivo. |  | Sim | Não |
| 11 | Nuvem de alta latência e alta carga de trabalho e/s | Quando seu dispositivo StorSimple encontra uma combinação de latências de nuvem muito alto (ordem de segundos) e alta carga de trabalho e/s, os volumes de dispositivo entram em um estado degradado e as i/os pode falhar com um erro "o dispositivo não está pronto". | Você precisará reinicializar os controladores de dispositivo ou realizar um failover de dispositivo para recuperar dessa situação manualmente. | Sim | Não |

## <a name="physical-device-updates-in-the-february-release"></a>Atualizações de dispositivo físico na fevereiro de lançamento

Essa atualização corrige o problema de redefinição de fábrica que ocorreu no dispositivos que tinham sido atualizados do GA de lançamento para a versão de outubro de 2014. Ele não contém quaisquer outras atualizações para o dispositivo StorSimple.  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-february-release"></a>Controlador de SCSI (SAS) anexados a série e atualizações de firmware na fevereiro de lançamento

Esta versão não contém todas as atualizações para o controlador de SCSI (SAS) anexados a série ou o firmware. A atualização de driver foi em outubro, lançamento de 2014.  

## <a name="virtual-device-updates-in-the-february-release"></a>Atualizações de dispositivo virtual na fevereiro de lançamento

Esta versão não contém todas as atualizações para o dispositivo virtual. Aplicar essa atualização não alterará a versão de software do dispositivo virtual.
 
