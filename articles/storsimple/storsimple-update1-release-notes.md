<properties 
   pageTitle="Notas de versão do StorSimple 8000 série atualização 1.2 | Microsoft Azure"
   description="Descreve os novos recursos, problemas e soluções alternativas para StorSimple 8000 série atualização 1.2."
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

# <a name="storsimple-8000-series-update-12-release-notes"></a>Notas de versão do StorSimple 8000 série atualização 1.2  

## <a name="overview"></a>Visão geral

As seguintes notas de versão descrevem os novos recursos e identificam as questões abertas críticos para StorSimple 8000 série atualização 1.2. Eles também contêm uma lista do software StorSimple, driver e atualizações de firmware de disco incluídas nesta versão. 

Atualização 1.2 pode ser aplicada a qualquer dispositivo StorSimple executando o lançamento (GA), atualização 0,1, atualização 0,2 ou software Update 0,3. Atualização 1.2 não estará disponível se seu dispositivo está executando Update 1 ou 1.1 de atualização. Caso seu dispositivo está executando o lançamento (GA), entre [em contato com o suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudá-lo ao instalar essa atualização.

A tabela a seguir lista as versões de software de dispositivo correspondente ao atualizações 1, 1.1 e 1.2.

| Se executando update... | Esta é sua versão de software do dispositivo. |
|---------------------|---------------------------------------|
| Atualizar 1.2          | 6.3.9600.17584                        |
| Atualizar 1.1          | 6.3.9600.17521                        |
| Atualizar 1.0          | 6.3.9600.17491                        |

Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução de StorSimple. Para obter mais informações, consulte como [instalar 1.2 de atualização em seu dispositivo de StorSimple](storsimple-install-update-1.md). 

>[AZURE.IMPORTANT]
> 
- Leva aproximadamente 5 a 10 horas para instalar essa atualização (incluindo as atualizações do Windows). 
- Atualização 1.2 possui atualizações de firmware de disco, driver LSI e software. Para instalar, siga as instruções em [instalar 1.2 de atualização em seu dispositivo de StorSimple](storsimple-install-update-1.md).
- Para novas versões, talvez você não veja as atualizações imediatamente porque podemos fazer uma distribuição em fases das atualizações. Procurar por atualizações em poucos dias novamente como eles ficarão disponível em breve.


## <a name="whats-new-in-update-12"></a>Novidades na atualização 1.2

Esses recursos foram lançados primeiro com Update 1 que foi disponibilizados para um conjunto limitado de usuários. Com a versão 1.2 de atualização, a maioria dos usuários StorSimple prefere ver os aperfeiçoamentos e novos recursos a seguir:

- **Migração da série de 5000-7000 8000 dispositivos de série** – esta versão apresenta um novo recurso de migração que permite a StorSimple 7000 5000 série aparelho aos usuários migrar seus dados para um dispositivo físico de série 8000 StorSimple ou um dispositivo virtual. O recurso de migração tem dois principais propostas de valor:                                                                  

    - **Continuidade de negócios**, permitindo a migração dos dados existentes em dispositivos de série 5000-7000 para dispositivos da 8000 série.
    - **Ofertas de recurso aprimorado dos dispositivos de série 8000**, como eficiente gerenciamento centralizado de vários dispositivos por meio de serviço do Gerenciador de StorSimple, melhor classe de hardware e atualizados firmware, dispositivos virtuais, mobilidade de dados e recursos no roteiro futuro.

    Consulte o [guia de migração](http://www.microsoft.com/download/details.aspx?id=47322) para obter detalhes sobre como migrar uma série de 5000-7000 StorSimple para um dispositivo de série 8000. 

- **Disponibilidade no Portal do governo Azure** – StorSimple agora está disponível no portal do governo do Azure. Veja como [implantar um dispositivo StorSimple no Portal do governo do Azure](storsimple-deployment-walkthrough-gov.md).

- **Suporte para outros provedores de serviços de nuvem** – os outros provedores de serviço nuvem com suporte são Amazon S3, Amazon S3 com RR, HP e OpenStack (beta).

- **Atualização mais recente APIs de armazenamento** – com esta versão, StorSimple foi atualizado para o serviço de armazenamento do Azure APIs mais recente. Dispositivos da série StorSimple 8000 que executam versões de software de pré-atualização 1 (versão, 0.1, 0.2 e 0,3) estão usando versões das APIs de serviço de armazenamento do Azure mais antigas do que 17 de julho de 2009. Conforme mencionado no atualizado [comunicado sobre remoção de versões de serviço de armazenamento](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), por 1 de agosto de 2016, essas APIs serão substituídas. É fundamental que você aplicar StorSimple 8000 série Update 1 anteriores a 1 de agosto de 2016. Se você não conseguir fazer isso, StorSimple dispositivos deixarão de funcionar corretamente.

- **Suporte para zona redundantes armazenamento (ZRS)** – com a atualização para a versão mais recente das APIs de armazenamento, a série 8000 StorSimple oferecerá suporte armazenamento redundantes de zona (ZRS) além de armazenamento localmente redundantes (LRS) e armazenamento geográfica redundantes (GRS). Consulte este [artigo sobre opções de redundância de armazenamento do Azure](../storage/storage-redundancy.md) para obter detalhes ZRS.

- **Experiência de implantação e atualização inicial avançada** – nesta versão, instalação e processos de atualização foram aprimorados. A instalação através do Assistente de configuração é aprimorada para fornecer comentários ao usuário se a configuração de rede e as configurações de firewall estão incorretas. Cmdlets de diagnóstico adicional foram fornecido para ajudá-lo com a solução de problemas de rede do dispositivo. Consulte o [artigo de implantação de solução de problemas](storsimple-troubleshoot-deployment.md) para obter mais informações sobre os novos cmdlets de diagnósticos usados para solucionar problemas.

## <a name="issues-fixed-in-update-12"></a>Problemas corrigidos na atualização 1.2

A tabela a seguir fornece um resumo dos problemas que foram corrigidos em atualizações 1.2, 1.1 e 1.    


| Não. | Recurso | Problema | Corrigidos na atualização | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|-----------------|---------------------------------|--------------------------------|
| 1 | Windows PowerShell para StorSimple | Quando um usuário acessado remotamente o dispositivo StorSimple usando o Windows PowerShell para StorSimple e, em seguida, iniciar o Assistente de configuração, uma falha ocorreu assim que dados 0 IP foi digitado. Esse bug agora é fixo na atualização 1. | Atualização 1 | Sim | Sim |
| 2 | Redefinição de fábrica | Em alguns casos, quando você executou a redefinição de fábrica, o dispositivo StorSimple ficou preso e exibida esta mensagem: **Redefinir para fábrica está em andamento (fase 8)**. Isso aconteceu pressionar CTRL + C enquanto o cmdlet estava em andamento. Esse bug agora é fixo.| Atualização 1 | Sim | Não |
| 3 | Redefinição de fábrica | Depois de redefinir uma fábrica falha controlador duplo, você estava permissão para continuar com o registro do dispositivo. Isso resultou em uma configuração de sistema sem suporte. Em Update 1, uma mensagem de erro é mostrada e o registro é bloqueado em um dispositivo que tem uma fábrica falha redefinir. | Atualização 1 | Sim | Não |
| 4 | Redefinição de fábrica | Em alguns casos, alertas de incompatibilidade positivo falso foram geradas. Alertas de incompatibilidade incorretos já não serão geradas em dispositivos executando Update 1. | Atualização 1 | Sim | Não |
| 5 | Redefinição de fábrica | Se uma redefinição de fábrica for interrompida antes da conclusão, o dispositivo entrou no modo de recuperação e não permitiu que você acessar o Windows PowerShell para StorSimple. Esse bug agora é fixo. | Atualização 1 | Sim | Não |
| 6 | Recuperação de dados | Um desastre recuperação (DR) era corrigido na qual DR falhará durante a descoberta de backups no dispositivo de destino. | Atualização 1 | Sim | Sim |
| 7 | Monitoramento LED | Em determinadas circunstâncias, monitoramento LED final aparelho não indicou status correto. O LED azul foi desativado. DADOS 0 e LED de 1 de dados foram piscando mesmo quando essas interfaces não foram definidas. O problema foi resolvido e monitoramento LED agora indica o status correto.  | Atualização 1 | Sim | Não |
| 8 | Monitoramento LED | Em determinadas circunstâncias, após a aplicação de Update 1, a luz azul no controlador de ativos desativado tornando difícil identificar o controlador de ativo. Esse problema foi corrigido nesta versão do patch.| Atualizar 1.2 | Sim | Não |
| 9 | Interfaces de rede | Em versões anteriores, um dispositivo StorSimple configurado com um gateway não roteáveis pode ficar offline. Nesta versão, a métrica de roteamento para dados 0 foi feita os mais baixos; Portanto, mesmo que outras interfaces de rede estejam habilitados para a nuvem, todo o tráfego de nuvem do dispositivo será roteado via dados 0. | Atualização 1 | Sim | Sim | 
| 10 | Backups | Um bug na atualização 1 que causou backups falha após 24 dias foi corrigido na versão patch 1.1 de atualização. | Atualizar 1.1 | Sim | Sim |
| 11 | Backups | Um bug nas versões anteriores resultou em desempenho ruim para instantâneos de nuvem com taxas de alteração baixa. Esse bug foi corrigido nesta versão do patch.| Atualizar 1.2 | Sim | Sim |
| 12 | Atualizações | Um bug na atualização 1 relatadas uma atualização com falha e causados os controladores ir para o modo de recuperação, foi corrigido nesta versão do patch.| Atualizar 1.2 | Sim | Sim |


## <a name="known-issues-in-update-12"></a>Problemas conhecidos do Update 1.2

A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.

| Não. | Recurso | Problema | Comentários/solução alternativa | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Quorum de disco | Em casos raros, se a maioria dos discos no compartimento EBOD de um dispositivo 8600 estiver desconectada, resultando em quorum sem disco, então o pool de armazenamento será offline. Ela permanecerá offline, mesmo se os discos estão reconectados. | Você precisará reiniciar o dispositivo. Se o problema persistir, contate o Microsoft Support para as próximas etapas. | Sim | Não |
| 2 | Identificação do controlador incorreto | Quando uma substituição de controlador é executada, controlador 0 pode aparecer como controlador 1. Durante a substituição de controlador, quando a imagem é carregada do nó ponto, a identificação do controlador pode aparecer inicialmente como ID. do controlador de ponto Em casos raros, esse comportamento também pode ser visto após a reinicialização do sistema. | Nenhuma ação é necessária. Essa situação se resolverá após a substituição de controlador é concluída. | Sim | Não |
| 3 | Contas de armazenamento | Usando o serviço de armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso levará a uma situação na qual os dados do usuário não podem ser recuperados. | Sim | Sim |
| 4 | Failover de dispositivo | Não há suporte para vários failovers de um contêiner de volume do dispositivo de origem mesmo para diferentes dispositivos de destino. Failover de dispositivos de um único dispositivo inativo em vários dispositivos fará os contêineres de volume na primeira falha sobre dispositivo perdem a propriedade de dados. Após o failover, esses contêineres de volume aparecerá ou se comportam de forma diferente quando você vê-los no portal de clássico do Azure. | | Sim | Não |
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

## <a name="physical-device-updates-in-update-12"></a>Atualizações de dispositivo físico no 1.2 de atualização

Se atualizar patch 1.2 é aplicado a um dispositivo físico (executando versões anteriores Update 1), a versão do software será alterado para 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Atualizações de controlador e firmware no 1.2 de atualização

Esta versão atualiza o driver e o firmware de disco em seu dispositivo.
 
- Para obter mais informações sobre a atualização de controlador SAS, consulte [Update 1 para controladores SAS LSI no Microsoft Azure StorSimple aparelho](https://support.microsoft.com/kb/3043005). 

- Para obter mais informações sobre o firmware do disco atualização, consulte [firmware do disco Update 1 para Microsoft Azure StorSimple aparelho](https://support.microsoft.com/kb/3063416).
 
## <a name="virtual-device-updates-in-update-12"></a>Atualizações de dispositivo virtual no 1.2 de atualização

Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtuais precisará ser criado. 

## <a name="next-steps"></a>Próximas etapas

- [Instalar atualização 1.2 em seu dispositivo](storsimple-install-update-1.md).
 
