<properties 
   pageTitle="Notas de versão do StorSimple 8000 série atualização 2.2 | Microsoft Azure"
   description="Descreve os novos recursos, problemas e soluções alternativas para StorSimple 8000 série atualização 2.2."
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
   ms.date="07/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-22-release-notes"></a>Notas de versão do StorSimple 8000 série atualização 2.2  

## <a name="overview"></a>Visão geral

As notas de versão a seguir descrevem os novos recursos e identificam as questões abertas críticos para StorSimple 8000 série atualização 2.2. Eles também contêm uma lista das atualizações de software StorSimple incluídos nesta versão. 

Atualização 2.2 pode ser aplicada a qualquer dispositivo StorSimple percorrer lançamento (GA) ou atualização 0,1 2.1 de atualização. A versão de dispositivo associada 2.2 atualização é 6.3.9600.17708.

Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução de StorSimple.

>[AZURE.IMPORTANT]
> 
> - Atualização 2.2 tem apenas as atualizações de software. Leva cerca de 1,5 a 2 horas para instalar essa atualização. 

> - Se você estiver executando 2.1 de atualização, recomendamos que você aplicar 2.2 atualização assim que possível.

> - Para novas versões, talvez você não veja as atualizações imediatamente porque podemos fazer uma distribuição em fases das atualizações. Aguarde alguns dias e, em seguida, procure as atualizações novamente como elas se tornarão disponível em breve.


## <a name="whats-new-in-update-22"></a>O que há de novo no 2.2 de atualização

As seguintes melhorias chaves foram feitas no 2.2 de atualização.

 
- **Otimização de recuperação de espaço de automatizada** – quando os dados são excluídos em volumes de provisionamento thin, os blocos de armazenamento não utilizada precisam ser recuperadas. Esta versão aumentou o processo de recuperação de espaço da nuvem resulta no espaço não utilizado se tornar disponível com mais rapidez em comparação com as versões anteriores.


- **Aperfeiçoamentos de desempenho de instantâneo** – atualização 2.2 aumentou o tempo para processar um instantâneo em determinados cenários onde grandes volumes estão sendo usados e não há mínima sem rotatividade de dados na nuvem. Um cenário que faria se beneficiar com esse aprimoramento seria os volumes de arquivamento.


- **Reunir informações de pacote de proteção de suporte** – houve melhorias da maneira que o pacote de suporte é reunido e carregado nesta versão. 


- **Atualizar melhorias de confiabilidade** – esta versão tem correções de bugs que resultam em uma maior confiabilidade de atualização.

  
 

## <a name="issues-fixed-in-update-22"></a>Problemas corrigidos no 2.2 de atualização

As tabelas a seguir fornece um resumo dos problemas que foram corrigidos no atualizações 2.2 e 2.1.    

| Não | Recurso                                    | Problema                                                                                                                                                                                                                                                                                        | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|----|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|---------------------------|
| 1  | Desempenho do host                      | Na versão anterior, problemas de desempenho do lado do host foram observados durante a criação de um volume localmente fixado e durante a conversão de um volume hierárquico para um volume localmente fixada. Esses problemas são corrigidos nesta versão, portanto, resultando em uma melhora o desempenho do host durante os procedimentos de criação e conversão de volume.                                                                        | Sim                        | Não                        |
| 2  | Volumes localmente fixados                     | Em casos raros, o sistema seria falhar ao criar um volume localmente fixado. Esse bug foi corrigido nesta versão.                                                                                                                                                               | Sim                        | Não                        |
| 3  | Hierarquia                                    | Havia esporádica travar quando os metadados para os dispositivos de nuvem StorSimple (8010 e 8020) hierárquico para a nuvem. Esse problema é corrigido nesta versão.                                                                                                                              | Não                         | Sim                       |
| 4  | Criação de instantâneo                          | Havia problemas relacionados à criação de instantâneos incrementais em cenários com grandes volumes e mínima sem rotatividade de dados. Esses problemas são corrigidos nesta versão.                                                                                                                 | Sim                        | Sim                       |
| 5  | Autenticação de Openstack                   | Ao usar Openstack como o provedor de serviços de nuvem, o usuário será executado em um bug raros relacionado a autenticação onde o analisador JSON resultou em uma falha. Este bug será corrigido nesta versão.                                                                                                                              | Sim                        | Não                        |
| 6  | Cópia do lado do host                             | Em versões anteriores do software, um bug raros relacionado ao intervalo de ODX foi visto ao copiar os dados de um volume para outro volume. Isso resultaria em um failover de controlador e o sistema potencialmente pode entrar no modo de recuperação. Este bug será corrigido nesta versão. | Sim                        | Não       |
| 7  | (Instrumentação de gerenciamento do Windows) | Nas versões anteriores do software, havia várias instâncias de falha de proxy da web com a exceção "<ManagementException> falha de carregamento do provedor". Esse bug foi atribuída a uma perda de memória WMI e agora é fixo.                                                               | Sim                        | Não                        |
| 8  | Atualização                                     | Em determinadas circunstâncias raras, nas versões anteriores do software, o usuário recebeu um "CisPowershellHcsscripterror" ao tentar digitalizar ou instalar atualizações. Esse problema é corrigido nesta versão.                                                                                        | Sim                        | Sim                       |
| 9  | Pacote de suporte                            | Nesta versão, foram melhorias na maneira como o pacote de suporte é reunido e carregado.                                                                                                                                                                                                      | Sim                        | Sim                                    |


## <a name="known-issues-in-update-22"></a>Problemas conhecidos do 2.2 de atualização

A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.

| Não. | Recurso | Problema | Comentários / solução alternativa | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Quorum de disco | Em casos raros, se a maioria dos discos no compartimento EBOD de um dispositivo 8600 estiver desconectada, resultando em quorum sem disco, então o pool de armazenamento vai offline. Ela permanecerá offline, mesmo se os discos estão reconectados. | Você precisará reiniciar o dispositivo. Se o problema persistir, contate o Microsoft Support para as próximas etapas. | Sim | Não |
| 2 | Identificação do controlador incorreto | Quando uma substituição de controlador é executada, controlador 0 pode aparecer como controlador 1. Durante a substituição de controlador, quando a imagem é carregada do nó ponto, a identificação do controlador pode aparecer inicialmente como ID. do controlador de ponto Em casos raros, esse comportamento também pode ser visto após a reinicialização do sistema. | Nenhuma ação é necessária. Essa situação se resolverá após a substituição de controlador é concluída. | Sim | Não |
| 3 | Contas de armazenamento | Usando o serviço de armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso levará a uma situação na qual os dados do usuário não podem ser recuperados.|  | Sim | Sim |
| 4 | Failover de dispositivo | Não há suporte para vários failovers de um contêiner de volume do dispositivo de origem mesmo para diferentes dispositivos de destino. Failover de um único dispositivo inativo em vários dispositivos tornará os contêineres de volume na primeira falha sobre dispositivo perdem a propriedade de dados. Após o failover, esses contêineres de volume aparecerá ou se comportam de forma diferente quando você vê-los no portal de clássico do Azure. | | Sim | Não |
| 5 | Instalação | Durante StorSimple adaptador de instalação do SharePoint, você precisa fornecer um IP de dispositivo, para que a instalação concluir com êxito.    | | Sim | Não |
| 6 | Proxy da Web | Se sua configuração de proxy web tiver HTTPS como o protocolo especificado, sua comunicação de serviço do dispositivo será afetada e o dispositivo irão offline. Pacotes de suporte também serão gerados no processo, consumindo recursos significativos no seu dispositivo. | Garantir que a URL da web proxy HTTP como o protocolo especificado. Para obter mais informações, vá para [Configurar proxy de web para o seu dispositivo](storsimple-configure-web-proxy.md). | Sim | Não |
| 7 | Proxy da Web | Se você configurar e habilitar o proxy da web em um dispositivo registrado, você precisará reiniciar o controlador de ativo no seu dispositivo. | | Sim | Não |
| 8 | Nuvem de alta latência e alta carga de trabalho e/s | Quando seu dispositivo StorSimple encontra uma combinação de latências de nuvem muito alto (ordem de segundos) e alta carga de trabalho e/s, os volumes de dispositivo entram em um estado degradado e as i/os pode falhar com um erro "o dispositivo não está pronto". | Você precisará reinicializar os controladores de dispositivo ou realizar um failover de dispositivo para recuperar dessa situação manualmente. | Sim | Não |
| 9 | PowerShell Azure | Quando você usa o cmdlet Get-AzureStorSimpleStorageAccountCredential **do StorSimple & #124; Select-Object - primeiro 1 - espera** para selecionar o primeiro objeto para que você possa criar um novo objeto de **VolumeContainer** , o cmdlet retorna todos os objetos. | Quebrar o cmdlet entre parênteses da seguinte maneira: **(Get-Azure-StorSimpleStorageAccountCredential) & #124; Select-Object - primeiro 1 - espera** | Sim | Sim |
| 10| Migração | Quando vários contêineres de volume são passados para a migração, o ETA para backup mais recente é preciso apenas para o primeiro contêiner de volume. Além disso, migração paralela iniciará após os primeiro 4 backups do primeiro contêiner de volume são migrados. | Recomendamos que você migrar um contêiner de volume por vez. | Sim | Não |
| 11| Migração | Após a restauração, volumes não são adicionados à diretiva de backup ou do grupo de disco virtual. | Você precisará adicionar esses volumes a uma política de backup para criar backups. | Sim | Sim |
| 12| Migração | Depois que a migração é concluída, o dispositivo de série 5000/7000 não deve acessar os contêineres de dados migrados. | Recomendamos que você exclua os contêineres de dados migrados após a migração é concluída e confirmada. | Sim | Não |
| 13| Clonar e DR | Um dispositivo de StorSimple executando Update 1 não é possível clonar ou executar recuperação de dados para um dispositivo executando pré-atualização 1 o software. | Você precisará atualizar o dispositivo de destino para Update 1 para permitir que essas operações | Sim | Sim |
| 14 | Migração | Backup de configuração para a migração pode falhar em um dispositivo de série 5000-7000 quando há grupos de volume sem volumes associados. | Excluir todos os grupos de volume vazio sem volumes associados e tente novamente o backup de configuração.| Sim | Não |
| 15 | Azure cmdlets do PowerShell e volumes localmente fixados | Você não pode criar um volume localmente fixado via cmdlets do PowerShell do Azure. (Qualquer volume criado por meio do PowerShell do Azure vai ser hierárquico). |Sempre use o serviço do Gerenciador de StorSimple para configurar volumes localmente fixados.| Sim | Não |
| 16 |Espaço disponível para volumes localmente fixados | Se você excluir um volume localmente fixado, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O serviço do Gerenciador de StorSimple atualiza o local espaço disponível aproximadamente a cada hora.| Aguarde uma hora antes de tentar criar o novo volume. | Sim | Não |
| 17 | Volumes localmente fixados | O trabalho de restauração expõe o backup de instantâneo temporário no catálogo de Backup, mas somente para a duração do trabalho de restauração. Além disso, ele expõe um grupo de disco virtual com prefixo **tmpCollection** na página **Políticas de Backup** , mas somente para a duração do trabalho de restauração. | Esse comportamento pode ocorrer se o seu trabalho de restauração apenas localmente tem fixos volumes ou uma mistura de volumes localmente fixados e hierárquicos. Se o trabalho de restauração inclui apenas volumes hierárquicos, em seguida, esse comportamento não ocorrerá. Nenhuma intervenção do usuário é necessária. | Sim | Não |
| 18 | Volumes localmente fixados | Se você cancelar um trabalho de restauração e um failover do controlador ocorre imediatamente posteriormente, o trabalho de restauração mostrará **Falha** em vez de **cancelado**. Se uma tarefa de restauração falhar e houver uma falha de controlador imediatamente posteriormente, o trabalho de restauração mostrará **cancelado** em vez de **Falha**. | Esse comportamento pode ocorrer se o seu trabalho de restauração apenas localmente tem fixos volumes ou uma mistura de volumes localmente fixados e hierárquicos. Se o trabalho de restauração inclui apenas volumes hierárquicos, em seguida, esse comportamento não ocorrerá. Nenhuma intervenção do usuário é necessária. | Sim | Não |
| 19 |Volumes localmente fixados | Se você cancelar um trabalho de restauração ou se a restauração falha e, em seguida, ocorre um failover de controlador, um trabalho de restauração adicionais aparece na página **trabalhos** . | Esse comportamento pode ocorrer se o seu trabalho de restauração apenas localmente tem fixos volumes ou uma mistura de volumes localmente fixados e hierárquicos. Se o trabalho de restauração inclui apenas volumes hierárquicos, em seguida, esse comportamento não ocorrerá. Nenhuma intervenção do usuário é necessária. | Sim | Não |
| 20 |Volumes localmente fixados | Se você tentar converter um volume hierárquico (criado e clonado com atualização 1.2 ou anterior) em um volume localmente fixado e seu dispositivo está ficando sem espaço ou não há uma queda de nuvem, o clone(s) pode ser corrompida.| Esse problema ocorre apenas com volumes que foram criadas e clonado com pré-atualizar 2.1 software. Isso deve ser um cenário raros.|
| 21 | Conversão de volume | Não atualize os ACRs conectados a um volume enquanto a conversão de volume está em andamento (hierárquico para localmente fixos ou vice-versa). Atualizar os ACRs pode resultar em corrupção de dados. | Se necessário, atualize os ACRs antes da conversão de volume e não faça qualquer outra atualizações ACR enquanto a conversão estiver em andamento. |

## <a name="controller-and-firmware-updates-in-update-22"></a>Atualizações de controlador e firmware no 2.2 de atualização

Esta versão tem atualizações somente de software. No entanto, se você estiver atualizando de uma versão antes de 2 de atualização, você precisa instalar o driver, Storport, Spaceport e (em alguns casos) atualizações de firmware em seu dispositivo de disco.
 
Para obter mais informações sobre como instalar o driver, Storport, Spaceport e atualizações de firmware de disco, consulte [instalar 2.2 de atualização](storsimple-install-update-21.md) em seu dispositivo de StorSimple.

 
## <a name="virtual-device-updates-in-update-22"></a>Atualizações de dispositivo virtual no 2.2 de atualização

Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtuais precisará ser criado. 

## <a name="next-step"></a>Próxima etapa

Saiba como [instalar 2.2 de atualização](storsimple-install-update-21.md) em seu dispositivo de StorSimple.
