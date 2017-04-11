<properties 
    pageTitle="Notas da versão 0.1 StorSimple 8000 atualização | Microsoft Azure"
    description="Descreve os novos recursos e correções, questões abertas e soluções alternativas disponíveis de outubro de 2014 lançamento do Microsoft Azure StorSimple (atualização de 0,1)."
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

# <a name="storsimple-8000-series-update-01-release-notes--october-2014"></a>Notas de versão 0.1 de atualização de série 8000 StorSimple – outubro de 2014  

## <a name="overview"></a>Visão geral

As seguintes notas identificam as questões abertas críticas para atualização de série StorSimple 8000 0,1 lançado em outubro de 2014. Eles também contêm uma lista das atualizações de software e firmware StorSimple incluídos nesta versão. Este é o primeiro lançamento após a versão de lançamento do StorSimple 8000 série foi disponibilizada em julho de 2014 e corresponde à versão do software 6.3.9600.17312.  

Recomendamos que você procurar e aplique as atualizações disponíveis imediatamente após a instalação do dispositivo. Você também pode ativar atualizações automáticas para baixar e instalar atualizações de alta prioridade da Microsoft, assim que eles são lançados. Para obter mais informações, consulte como [atualizar seu dispositivo StorSimple](storsimple-update-device.md).  

Examine as informações contidas nas notas de versão antes de implantar as atualizações em sua solução de StorSimple.  

>[AZURE.IMPORTANT]
> 
-   Use o serviço do Gerenciador de StorSimple e não o Windows PowerShell para StorSimple para instalar as atualizações de outubro.  
-   As atualizações geralmente levar cerca de 3 horas.  
-   Edição de outubro do StorSimple não contém todas as atualizações para o dispositivo virtual StorSimple. Você ainda poderá aplicar as atualizações disponíveis do Windows, incluindo recentes correções de segurança, mas você não verá uma alteração na versão para o dispositivo virtual.  

Verifique se que os seguintes pré-requisitos são atendidos antes de atualizar o seu dispositivo StorSimple.  

- Certifique-se de que ambos os controladores de dispositivo estão executando antes de verificar se há atualizações. Se o controlador não estiver executando, a verificação falhará. Para verificar se os controladores estão em um estado de integridade, navegue até o **Status de Hardware** sob a página de **manutenção** . Se houver componentes de que **precisa de atenção**, contate o Microsoft Support antes de continuar qualquer.  
- Verifique se IPs fixo para ambas as controlador 0 e 1 de controlador são roteáveis e podem se conectar à Internet como elas são usadas para manutenção as atualizações para o dispositivo. Você pode usar o [cmdlet de Conexão de teste](https://technet.microsoft.com/library/hh849808.aspx) para ping um endereço conhecido fora da rede como o outlook.com, para verificar se o controlador tem conectividade com a rede externa.  
- Certifique-se de que as portas de saída necessárias estão disponíveis em seu dispositivo StorSimple para comunicação de saída. Para obter mais informações, consulte os [requisitos para o seu dispositivo de StorSimple de rede](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).  
- Se a versão do software de dispositivo é mais antiga que 6.3.9600.17312 (atualização de outubro de 2014), desative as portas de dados 2 e 3 de dados, se habilitada, antes de iniciar a atualização. Se você deixar a Data 2 ou 3 de dados portas habilitadas quando você aplicar a atualização, ele pode causar seu controlador de dispositivo ir para o modo de recuperação. Observe que, quando você desativa as interfaces de rede, todos os volumes associados serão colocados offline e i / o será interrompida para a duração da atualização.  

## <a name="whats-new-in-the-october-release"></a>O que há de novo na edição de outubro

Esta atualização inclui as seguintes melhorias:

- Agora você pode usar o serviço do Gerenciador de StorSimple da interface do usuário para gerenciar seus controladores de dispositivo. O gerenciamento de ações incluem reiniciar, desligamento, ou ativar um controlador. Para obter mais informações, vá para [Gerenciar StorSimple controladores de dispositivo](storsimple-manage-device-controller.md).  
- Você pode programar a alocação de largura de banda WAN acordo com combinações de dia da semana e a hora do dia. Isso permite que você usar melhor largura de banda WAN durante o horário de pico. Modelos de largura de banda diferentes são permitidos para contêineres de volume diferente. Para obter mais informações, vá para [gerenciar seus modelos de largura de banda de StorSimple](storsimple-manage-bandwidth-templates.md).  
- Você pode configurar notificações de email proativamente para notificar o administrador (es) e outros problemas existentes ou possivelmente futuras. Para obter mais informações, vá para [configurações de alerta de configurar](storsimple-manage-alerts.md#configure-alert-settings).  

## <a name="issues-fixed-in-the-october-release"></a>Problemas corrigidos na edição de outubro


A tabela a seguir fornece um resumo dos problemas que foram corrigidos nesta atualização.  

| Não. | Recurso | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|---------------------------------|--------------------------------|
| 1 | Interfaces de rede | Na versão anterior, a DATA 2 de interfaces de rede e dados 3 foram trocadas no software. Isso foi corrigido nessa atualização. Limpe as configurações e desabilitar essas interfaces de rede antes de instalar a atualização. Depois de instalar a atualização, você precisará reconfigurar essas interfaces. | Sim | Não |
| 2 | Pacote de suporte | Na versão anterior, se você executou o cmdlet do Windows PowerShell **HcsSupportPackage de exportação** para recuperar os logs de controlador BMC (Baseboard Management), a operação falhou com o seguinte aviso: "a operação bem-sucedida neste controlador, mas falhou no controlador de ponto devido aos erros a seguir. Verifique se o ponto está íntegro e se o nó atual pode se conectar ao correspondente." Esse problema agora é fixo. | Sim | Não |
| 3 | Failover de dispositivo | Na versão anterior, houve uma chance de inconsistência de dados se um trabalho **Descubra o backup** falhou durante um failover do dispositivo. Esse problema agora é fixo. | Sim | Não |
| 4 | Failover de dispositivo | Na versão anterior, após um failover de dispositivo, backups estavam visíveis, mas o contêiner de volume associado não estava presente no dispositivo de destino. Esse problema agora é fixo. | Sim | Não |
| 5 | Failover de dispositivo | Na versão anterior, houve um bug na enumeração de backups de nuvem durante a operação de restauração de registro que poderia levar a uma inconsistência de dados se houver problemas de conectividade de nuvem. | Sim | Não |
| 6 | Atualização do firmware | Na versão anterior, o trabalho de atualização de firmware de dispositivo falhou e exibido um erro que consta que os cmdlets não foram reconhecidos e que falha na atualização como resultado. O controlador, em seguida, entrar em modo de recuperação. Esse problema agora é fixo. | Sim | Não |
| 7 | Instalação | Erros causados pelo dispositivo não está sendo espelhado corretamente durante a instalação agora foram corrigidos. | Sim | Não |
| 8 | Redefinição de fábrica | Agora, opcionalmente, pule a verificação do firmware para redefinição de fábrica. Esta é uma alteração da versão anterior. | Sim | Não |
| 9 | Redefinição de fábrica | Na versão anterior, quando um cmdlet de redefinição de fábrica foi executado, verificações de versão de firmware foram feitas somente para alguns componentes de hardware. Verificações de firmware adicionais foram feitas após a primeira reinicialização no processo, que pode causar a redefinição falha. Este fix garante que todas as verificações de firmware são feitas quando a fábrica redefinir cmdlet é executado e antes do primeiro sistema reinicializar. | Sim | Não |
| 10 | Rotação de chaves de conta de armazenamento | O cmdlet **Chamar HcsmServiceDataEncryptionKeyChange** usado para girar as teclas de conta de armazenamento agora solicita que o usuário insira a chave de criptografia de dados de serviço. Esta é uma alteração da versão anterior em que a chave de criptografia de dados de serviço foi passada como um parâmetro embutida. | Sim | Não |
| 11 | Failback dentro de 24 horas | Durante a recuperação, a limpeza no dispositivo de origem não ocorreram failback integralmente, causando falha. Isso foi corrigido nesta versão. | Sim | Não |

## <a name="known-issues-in-the-october-release"></a>Problemas conhecidos na versão de outubro

A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.

| Não. | Recurso | Problema | Comentários/solução alternativa | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Redefinição de fábrica | Em alguns casos, quando você executa uma redefinição de fábrica, o dispositivo StorSimple podem estar presas e exibir esta mensagem: **Redefinir para fábrica está em andamento (fase 8)**. Isso acontece se você pressionar CTRL + C enquanto o cmdlet está em andamento. | Não pressione CTRL + C após iniciar uma redefinição de fábrica. Se você já estiver nesse estado, contate o Microsoft Support para as próximas etapas. | Sim | Não |
| 2 | Redefinição de fábrica | Eu não fábrica redefinir um dispositivo StorSimple que é atualizado do GA para outubro de 2014 de lançamento. | Essa operação só funcionará se um patch estiver instalado. Contate o Microsoft Support para obter esse patch necessário. | Sim | Não | 
| 3 | Quorum de disco | Em casos raros, se a maioria dos discos no compartimento EBOD de um dispositivo 8600 estiver desconectada, resultando em quorum sem disco, então o pool de armazenamento será offline. Ela permanecerá offline, mesmo se os discos estão reconectados. | Você precisará reiniciar o dispositivo. Se o problema persistir, contate o Microsoft Support para as próximas etapas. | Sim | Não |
| 4 | Falhas de instantâneo de nuvem | Em casos raros, um instantâneo de nuvem pode falhar com o erro de **limite de backup máximo atingido**. Isso ocorre se você exceder 255 clones onlinehttps no mesmo dispositivo, do mesmo volume original que foi excluído. | | Sim | Sim |
| 5 | Identificação do controlador incorreto | Quando uma substituição de controlador é executada, controlador 0 pode aparecer como controlador 1. Durante a substituição de controlador, quando a imagem é carregada do nó ponto, a identificação do controlador pode aparecer inicialmente como ID. do controlador de ponto Em casos raros, esse comportamento também pode ser visto após a reinicialização do sistema. |Nenhuma ação é necessária. Essa situação se resolverá após a substituição de controlador é concluída. | Sim | Não |
| 6 | Dispositivo monitorando gráficos | No serviço StorSimple Manager, os gráficos de monitoramento do dispositivo não funcionam quando básica ou a autenticação NTLM está habilitada na configuração do servidor proxy para o dispositivo. | Modificar a configuração de proxy da web para o dispositivo registrado com o serviço do Gerenciador de StorSimple para que a autenticação está definida como nenhum. Para fazer isso, execute do Windows PowerShell para o cmdlet Set-HcsWebProxy StorSimple. | Sim | Sim |
| 7 | Contas de armazenamento | Usando o serviço de armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso levará a uma situação na qual os dados do usuário não podem ser recuperados. | | Sim | Sim |
| 8 | Failover de dispositivo | Não há suporte para vários failovers de um contêiner de volume do dispositivo de origem mesmo para diferentes dispositivos de destino. | Failover de um único dispositivo inativo em vários dispositivos tornará os contêineres de volume na primeira falha sobre dispositivo perdem a propriedade de dados. Após o failover, esses contêineres de volume aparecerá ou se comportam de forma diferente quando você vê-los no portal de clássico do Azure. | Sim | Não |
| 9 | Instalação | Durante StorSimple adaptador de instalação do SharePoint, você precisa fornecer um IP de dispositivo, para que a instalação concluir com êxito.    | | Sim | Não |
| 10 | Proxy da Web | Se sua configuração de proxy web tiver HTTPS como o protocolo especificado, sua comunicação de serviço do dispositivo será afetada e o dispositivo irão offline. Pacotes de suporte também serão gerados no processo, consumindo recursos significativos no seu dispositivo. | Garantir que a URL da web proxy HTTP como o protocolo especificado. Mais informações sobre como [Configurar o proxy de web para o seu dispositivo](storsimple-configure-web-proxy.md). | Sim | Não |
| 11 | Proxy da Web | Se você configurar e habilitar o proxy da web em um dispositivo registrado, você precisará reiniciar o controlador de ativo no seu dispositivo. | | Sim | Não |
| 12 | Nuvem de alta latência e alta carga de trabalho e/s | Quando seu dispositivo StorSimple encontra uma combinação de latências de nuvem muito alto (ordem de segundos) e alta carga de trabalho e/s, os volumes de dispositivo entram em um estado degradado e as i/os pode falhar com um erro "o dispositivo não está pronto". | Você precisará reinicializar os controladores de dispositivo ou realizar um failover de dispositivo para recuperar dessa situação manualmente. | Sim | Não |

## <a name="physical-device-updates-in-the-october-release"></a>Atualizações de dispositivo físico na outubro de lançamento

Quando essas atualizações são aplicadas a um dispositivo físico, a versão do software será alterado para 6.3.9600.17312. A menos que especificado em contrário, essas notas de versão se aplicam a todos os modelos do dispositivo StorSimple. Para obter mais informações sobre essas atualizações, consulte [o software de dispositivo físico de outubro de 2014 atualizar para o Microsoft Azure StorSimple aparelho](http://support.microsoft.com/kb/2986997).  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-october-release"></a>Controlador de SCSI (SAS) anexados a série e atualizações de firmware na outubro de lançamento

Esta versão atualiza o driver e o firmware no controlador de SAS do seu dispositivo físico. Para obter mais informações sobre o controlador SAS atualização, consulte [atualizar para controladores SAS LSI no Microsoft Azure StorSimple aparelho de outubro de 2014](http://support.microsoft.com/kb/2987020).   

Esta versão também se aplica a uma atualização de firmware cumulativa que aborda os problemas de confiabilidade com os componentes de hardware do dispositivo. Para obter mais informações sobre a atualização de firmware, consulte [atualizar para o Microsoft Azure StorSimple aparelho de firmware de outubro de 2014](http://support.microsoft.com/kb/2987015).  

## <a name="virtual-device-updates-in-the-october-release"></a>Atualizações de dispositivo virtual na outubro de lançamento

Esta versão não contém todas as atualizações para o dispositivo virtual. Aplicar essa atualização não alterará a versão de software do dispositivo virtual.
 
