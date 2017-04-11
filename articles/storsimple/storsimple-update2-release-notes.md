<properties 
   pageTitle="Notas de versão do StorSimple 8000 série atualização 2 | Microsoft Azure"
   description="Descreve os novos recursos, problemas e soluções alternativas para StorSimple 8000 série atualização 2."
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
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-update-2-release-notes"></a>Notas de versão do StorSimple 8000 série atualização 2  

## <a name="overview"></a>Visão geral

As notas de versão a seguir descrevem os novos recursos e identificam as questões abertas críticos para StorSimple 8000 série atualização 2. Eles também contêm uma lista das StorSimple software, driver e atualizações de firmware de disco incluídas nesta versão. 

Atualização 2 pode ser aplicada a qualquer dispositivo StorSimple percorrer lançamento (GA) ou atualização 0,1 1.2 de atualização. A versão de dispositivo associada a atualização 2 é 6.3.9600.17673.

Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução de StorSimple.

>[AZURE.IMPORTANT]
> 
- Leva cerca de 4 a 7 horas para instalar essa atualização (incluindo as atualizações do Windows). 
- Atualização 2 tem atualizações de firmware SSD, driver LSI e software.
- Para novas versões, talvez você não veja as atualizações imediatamente porque podemos fazer uma distribuição em fases das atualizações. Aguarde alguns dias e, em seguida, procure as atualizações novamente como elas se tornarão disponível em breve.


## <a name="whats-new-in-update-2"></a>Novidades na atualização 2

Atualização 2 apresenta os novos recursos a seguintes.

- **Localmente fixos volumes** – em versões anteriores da série 8000 StorSimple, blocos de dados foram hierárquico na nuvem com base em uso. Não houve nenhuma maneira para garantir que os blocos seriam permaneça no local. Na atualização 2, quando você cria um volume, você pode designar um volume como dados localmente fixados e principais de volume não serão ser hierárquico para a nuvem. Instantâneos de volumes localmente fixados ainda serão copiados na nuvem para backup para que a nuvem pode ser usada para fins de recuperação de desastres e mobilidade de dados. Além disso, você pode alterar o tipo de volume (ou seja, converter hierárquico volumes para volumes localmente fixados e volumes de converter localmente fixada hierárquico). 

- **Melhorias de dispositivo virtual StorSimple** – anteriormente, a série 8000 StorSimple posicionado o dispositivo virtual como uma solução de recuperação ou desenvolvimento/teste de desastres. Houve apenas um modelo de dispositivo virtual (modelo 1100). Atualização 2 apresenta dois modelos de dispositivo virtual: 

     - 8010 (anteriormente chamado 1100) – nenhuma alteração; tem uma capacidade de 30 TB e usa o armazenamento do Azure padrão.
     - 8020 – tem a capacidade de 64 TB e usa o armazenamento do Azure Premium para melhorar o desempenho.

    Não há um único VHD para ambos os modelos de dispositivo virtual (8010/8020). Quando você inicia o dispositivo virtual, ele detecta os parâmetros de plataforma e aplica-se a versão de modelo correto.

- **Aprimoramentos de rede** – atualização 2 contém as seguintes melhorias de rede:

     - Várias placas de rede podem ser habilitadas para a nuvem para que failover pode ocorrer se uma NIC falhar.
     - Melhorias de roteamento, com métricas fixas para nuvem habilitado blocos.
     - Repetir online dos recursos com falha antes de um failover.
     - Novos alertas para falhas de serviço.

- **Atualizando melhorias** – em atualização 1.2 e versões anterior, a série 8000 StorSimple foi atualizado por meio de dois canais: Windows Update para agrupamento, iSCSI e assim por diante e Microsoft Update para binários e firmware.
    Atualização 2 usa o Microsoft Update para todos os pacotes de atualização. Isso deve levar a menos tempo fazendo failovers ou aplicar patches. 

- **Atualizações de firmware** – o seguinte atualizações de firmware estão incluídas:
    - LSI: lsi_sas2.sys 2.00.72.10 de versão do produto.
    - SSD somente (sem atualizações de disco rígido): XMGG, XGEG, KZ50, F6C2 e VR08

- **Suporte proativo** – atualização 2 permite que a Microsoft obter informações adicionais de diagnóstico do dispositivo. Quando nossa equipe de operações identifica dispositivos que estão tendo problemas, estamos melhor equipados para coletar informações do dispositivo e diagnosticar problemas. **Aceitando 2 de atualização, nos permite fornecer este suporte proativo**.    
 

## <a name="issues-fixed-in-update-2"></a>Problemas corrigidos na atualização 2

As tabelas a seguir fornece um resumo dos problemas que foram corrigidos no 2 de atualizações.    

| Não. | Recurso | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|--------------------------------|--------------------------------|
| 1 | Interfaces de rede | Após uma atualização para Update 1, o serviço do Gerenciador de StorSimple relatado que as portas Data2 e Data3 falhou em um controlador. Esse problema foi resolvido. | Sim | Não |
| 2 | Atualizações | Após uma atualização para Update 1, alertas de alarme sonoro ocorreram no portal de clássico do Azure em vários dispositivos. Esse problema foi resolvido. | Sim | Não |
| 3 | Autenticação de Openstack | Ao usar Openstack como seu provedor de serviços de nuvem, você poderá receber um erro que sua cadeia de caracteres de autenticação de nuvem era muito longa. Isso foi corrigido. | Sim | Não |


## <a name="known-issues-in-update-2"></a>Problemas conhecidos do Update 2

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
| 20 |Volumes localmente fixados | Se você tentar converter um volume hierárquico (criado e clonado com atualização 1.2 ou anterior) em um volume localmente fixado e seu dispositivo está ficando sem espaço ou não há uma queda de nuvem, o clone(s) pode ser corrompida.| Esse problema ocorre apenas com volumes que foram software criado e clonado com pré-atualização 2. Isso deve ser um cenário raros.|
| 21 | Conversão de volume | Não atualize os ACRs conectados a um volume enquanto a conversão de volume está em andamento (hierárquico para localmente fixos ou vice-versa). Atualizar os ACRs pode resultar em corrupção de dados. | Se necessário, atualize os ACRs antes da conversão de volume e não faça qualquer outra atualizações ACR enquanto a conversão estiver em andamento. |

## <a name="controller-and-firmware-updates-in-update-2"></a>Atualizações de controlador e firmware na atualização 2

Esta versão atualiza o driver e o firmware de disco em seu dispositivo.
 
- Para obter mais informações sobre o firmware LSI atualização, consulte o artigo da base de Conhecimento Microsoft 3121900. 
- Para obter mais informações sobre o firmware do disco atualização, consulte o artigo da base de Conhecimento Microsoft 3121899.
 
## <a name="virtual-device-updates-in-update-2"></a>Atualizações de dispositivo virtual no atualização 2

Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtuais precisará ser criado. 

## <a name="next-step"></a>Próxima etapa

Saiba como [instalar 2](storsimple-install-update-2.md) em seu dispositivo de StorSimple.
