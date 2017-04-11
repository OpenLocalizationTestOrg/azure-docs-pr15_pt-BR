<properties 
   pageTitle="O que é StorSimple? | Microsoft Azure" 
   description="Descreve StorSimple hierárquico, o dispositivo, dispositivo virtual, serviços e gerenciamento de armazenamento e introduz principais termos usados em StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="10/05/2016"
   ms.author="v-sharos@microsoft.com"/>

# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Série 8000 StorSimple: uma solução de armazenamento de nuvem híbrida

## <a name="overview"></a>Visão geral

Bem-vindo ao Microsoft Azure StorSimple, uma solução de armazenamento integrado que gerencia tarefas de armazenamento entre dispositivos de locais e armazenamento de nuvem do Microsoft Azure. StorSimple é uma solução de rede (SAN) de área de armazenamento eficiente, econômica e facilmente gerenciável que elimina muitos dos problemas e despesas associadas com a proteção de dados e armazenamento do enterprise. Ele usa o dispositivo de série 8000 StorSimple proprietário, integra-se com os serviços de nuvem e fornece um conjunto de ferramentas de gerenciamento para um modo de exibição perfeito de todo o armazenamento de empresa, incluindo armazenamento em nuvem. (As informações de implantação de StorSimple publicadas no site do Microsoft Azure se aplica ao StorSimple 8000 somente dispositivos de série. Se você estiver usando um dispositivo de série StorSimple 5000/7000, vá para a [Ajuda de StorSimple](http://onlinehelp.storsimple.com/).)

StorSimple usa o [armazenamento hierárquico](#automatic-storage-tiering) para gerenciar dados armazenados em várias mídias de armazenamento. O conjunto de trabalho atual é armazenado no local em unidades de estado sólido (SSDs), dados que são usados com menos frequência são armazenados em unidades de disco rígido (unidades de disco rígido) e dados de arquivamento são enviados para a nuvem. Além disso, StorSimple usa duplicação e compactação para reduzir a quantidade de armazenamento que consomem os dados. Para obter mais informações, vá para [duplicação e compactação](#deduplication-and-compression). Para obter definições de outros termos e conceitos chave usados na documentação série 8000 StorSimple, vá para [StorSimple terminologia](#storsimple-terminology) no final deste artigo.

Com StorSimple atualização 2, você pode identificar volumes apropriados como *localmente fixos* para garantir que os dados primários permanece no mesmo locais no dispositivo e não nível na nuvem. Isso permite executar cargas de trabalho que são sensíveis a latência de nuvem, como o SQL e máquina virtual cargas de trabalho, em volumes localmente fixos, enquanto continua a usar na nuvem para backup. Para obter mais informações sobre volumes localmente fixados, consulte [usar o serviço de Gerenciador de StorSimple para gerenciar volumes](storsimple-manage-volumes-u2.md). 

Atualização 2 também permite que você crie dispositivos virtuais StorSimple que tirar proveito das latências baixos e alto desempenho fornecido pelo armazenamento do Azure premium. Para obter mais informações sobre os dispositivos virtuais do StorSimple premium, consulte [implantar e gerenciar um dispositivo virtual StorSimple no Azure](storsimple-virtual-device-u2.md). Para obter mais informações sobre o armazenamento do Azure premium, vá para [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](../storage/storage-premium-storage.md).

Além de gerenciamento de armazenamento, os recursos de proteção de dados StorSimple permitem que você crie sob demanda agendado backups e armazená-los localmente ou na nuvem. São realizados backups no formulário de instantâneos incrementais, que significa que eles podem ser criados e restaurados rapidamente. Instantâneos de nuvem podem ser muito importantes no cenários de recuperação de desastres porque eles substituam sistemas de armazenamento secundário (como backup de fita) e permitem restaurar dados para o data center ou para locais alternativos, se necessário.

![ícone de vídeo](./media/storsimple-overview/video_icon.png) Assista ao vídeo para uma rápida introdução ao Microsoft Azure StorSimple.

> [AZURE.VIDEO storsimple-hybrid-cloud-storage-solution]

## <a name="why-use-storsimple"></a>Por que usar StorSimple?

A tabela a seguir descreve algumas das principais vantagens do Microsoft Azure StorSimple.

| Recurso | Benefício |
|---------|---------|
|Integração transparente | Microsoft Azure StorSimple usa o protocolo iSCSI de forma invisível vincular instalações de armazenamento de dados. Isso garante que os dados armazenados na nuvem, do data center, ou em servidores remotos parece estar armazenado em um único local.|
|Custos de armazenamento reduzidos|Microsoft Azure StorSimple alocar local suficiente ou armazenamento em nuvem para atender às exigências atuais e estende armazenamento em nuvem somente quando necessário. Ele reduz ainda mais requisitos de armazenamento e despesas eliminando redundantes versões dos mesmos dados (duplicação) e usando a compactação.|
|Gerenciamento simplificado do armazenamento|Microsoft Azure StorSimple fornece sistema de ferramentas de administração que você pode usar para configurar e gerenciar dados armazenados no local, em um servidor remoto e na nuvem. Além disso, você pode gerenciar o backup e restaurar as funções de um snap-in do Console de gerenciamento da Microsoft (MMC). StorSimple fornece uma interface separada, opcional que você pode usar para estender StorSimple dados e gerenciamento de serviços de proteção ao conteúdo armazenado em servidores do SharePoint. |
|Recuperação de dados aprimorada e conformidade|Microsoft Azure StorSimple não exige tempo de recuperação estendida. Em vez disso, ele restaura dados conforme necessário. Isso significa que as operações poderão continuar com interrupção mínima. Além disso, você pode configurar políticas para especificar agendamentos de backup e retenção de dados.
|Mobilidade de dados|Dados carregados para serviços de nuvem do Microsoft Azure podem ser acessados de outros sites para fins de recuperação e migração. Além disso, você pode usar StorSimple para configurar dispositivos virtuais StorSimple em máquinas virtuais (VMs) em execução no Microsoft Azure. As VMs podem usar dispositivos virtuais para acessar dados armazenados para fins de teste ou recuperação.|
|Suporte para outros provedores de serviços de nuvem |A série de StorSimple 8000 com o software de atualização 1 ou posterior suporta Amazon S3 com RR, HP e OpenStack cloud services, bem como Microsoft Azure. (Você ainda será necessário uma conta de armazenamento do Microsoft Azure para fins de gerenciamento de dispositivo.) Para obter mais informações, vá para [o que há de novo no 1.2 de atualização](storsimple-update1-release-notes.md#whats-new-in-update-12).|
|Continuidade de negócios | Atualização 1 ou posterior fornece um novo recurso de migração que permite aos usuários de série de 5000-7000 migrar seus dados para um dispositivo de série 8000 StorSimple de StorSimple.|
|Disponibilidade no Portal do governo Azure | Atualização de StorSimple 1 ou posterior está disponível no Portal do governo do Azure. Para obter mais informações, consulte [implantar seu dispositivo de StorSimple local no Portal do governo](storsimple-deployment-walkthrough-gov.md).|
|Disponibilidade e proteção de dados | A série 8000 StorSimple com Update 1 ou posterior dá suporte à zona redundantes armazenamento (ZRS), além de armazenamento de armazenamento localmente redundantes (LRS) e localização geográfica redundantes (GRS). Consulte [Este artigo sobre opções de redundância de armazenamento do Azure](https://azure.microsoft.com/documentation/articles/storage-redundancy/) para obter detalhes ZRS.|
|Suporte para aplicativos essenciais | Com StorSimple atualização 2, você pode identificar volumes apropriados fixados como localmente. Esse recurso garante que os dados exigidos por aplicativos críticos não é hierárquico na nuvem. Volumes localmente fixados não estão sujeitos a nuvem latências ou problemas de conectividade. Para obter mais informações sobre volumes localmente fixados, consulte [usar o serviço de Gerenciador de StorSimple para gerenciar volumes](storsimple-manage-volumes-u2.md).|
|Baixa latência e alto desempenho | StorSimple atualização 2 permite que você crie dispositivos virtuais que tirar proveito de alto desempenho, recursos de baixa latência de armazenamento do Azure premium. Para obter mais informações sobre os dispositivos virtuais do StorSimple premium, consulte [implantar e gerenciar um dispositivo virtual StorSimple no Azure](storsimple-virtual-device-u2.md).|

![ícone de vídeo](./media/storsimple-overview/video_icon.png) Assista a [Este vídeo](https://www.youtube.com/watch?v=4MhJT5xrvQw&feature=youtu.be) para obter uma visão geral dos recursos de série 8000 StorSimple e benefícios.

## <a name="storsimple-components"></a>Componentes de StorSimple

A solução Microsoft Azure StorSimple inclui os seguintes componentes:

- **Dispositivo do Microsoft Azure StorSimple** – uma matriz de armazenamento de híbrido local que contém o SSDs e unidades de disco rígido, juntamente com recursos de failover automático e controladores redundantes. Controladores de gerenciam armazenamento hierárquico, colocar atualmente usados (ou quente) dados em armazenamento local (no dispositivo ou local servidores), ao mover menos dados usados com frequência na nuvem.
- **Dispositivo virtual StorSimple** – também conhecido como o dispositivo Virtual de StorSimple, esta é uma versão de software do dispositivo StorSimple que replica a arquitetura e a maioria dos recursos do dispositivo de armazenamento físico híbrido. O dispositivo virtual StorSimple é executado em um único nó em uma máquina virtual Azure. Dispositivos virtuais Premium, que tirar proveito de armazenamento do Azure premium, estão disponíveis na atualização 2 e posterior.
- **Serviço Gerenciador de StorSimple** – uma extensão do portal de clássico do Azure que permite que você gerencie um StorSimple dispositivo ou StorSimple virtual de uma única interface da web. Você pode usar o serviço do Gerenciador de StorSimple para criar e gerenciar serviços, exibir e gerenciar dispositivos, exibir alertas, gerenciar volumes, exibir e gerenciar políticas de backup e o catálogo de backup.
- **Windows PowerShell para StorSimple** – uma interface de linha que você pode usar para gerenciar o dispositivo StorSimple. Windows PowerShell para StorSimple possui recursos que permitem que você registrar seu dispositivo StorSimple, configurar a interface de rede em seu dispositivo, instale certos tipos de atualizações, solucionar problemas de seu dispositivo acessando a sessão de suporte e alteram o estado do dispositivo. Você pode acessar o Windows PowerShell para StorSimple conectando-se ao console serial ou usando o Windows PowerShell remoto.
- **Cmdlets do azure PowerShell StorSimple** – um conjunto de cmdlets do Windows PowerShell que permitam a automatizar tarefas de nível de serviço e migração da linha de comando. Para obter mais informações sobre os cmdlets do PowerShell do Azure para StorSimple, vá para a [referência de cmdlet](https://msdn.microsoft.com/library/dn920427.aspx).
- **Gerenciador de instantâneo StorSimple** – um snap-in do MMC que usa grupos de volume e o serviço de cópia de sombra de Volume do Windows para gerar backups consistentes com o aplicativo. Além disso, você pode usar o Gerenciador de instantâneo StorSimple para criar clonar e agendas de backup ou restaurar volumes. 
- **Adaptador StorSimple para SharePoint** – uma ferramenta que transparente estende a proteção de dados e armazenamento Microsoft Azure StorSimple em farms do SharePoint Server, enquanto faz StorSimple armazenamento visualizável e gerenciável a partir do portal de Administração Central do SharePoint.

O diagrama a seguir fornece uma visão geral da arquitetura do Microsoft Azure StorSimple e componentes.

![Arquitetura de StorSimple](./media/storsimple-overview/overview-big-picture.png)

As seções a seguir descrevem cada um desses componentes mais detalhadamente e explicam como a solução organiza dados aloca o armazenamento e facilita o gerenciamento de armazenamento e proteção de dados. A última seção fornece definições para alguns dos termos importantes e conceitos relacionados ao StorSimple componentes e seu gerenciamento.

## <a name="storsimple-device"></a>Dispositivo StorSimple

O dispositivo do Microsoft Azure StorSimple é uma matriz de armazenamento de híbrido local que fornece armazenamento principal e iSCSI acesso aos dados armazenados nela. Ele gerencia a comunicação com armazenamento em nuvem e ajuda a garantir a segurança e a confidencialidade de todos os dados armazenados na solução Microsoft Azure StorSimple.

O dispositivo de StorSimple inclui SSDs e unidades de disco rígido unidades de disco rígido, bem como suporte para failover automático e agrupamento. Ele contém um processador compartilhado, armazenamento compartilhado e dois controladores espelhados. Cada controlador fornece o seguinte:

- Conexão a um computador host
- Até seis portas de rede para se conectar à rede local (LAN)
- Monitoramento de hardware
- Memória de acesso aleatório não-voláteis (NVRAM), que retém informações mesmo se energia for interrompida
- Reconhecimento de cluster atualizando para gerenciar as atualizações de software em servidores em um cluster de failover para que as atualizações tenham mínimo ou nenhum efeito sobre a disponibilidade de serviço
- Serviço de cluster, que funciona como um cluster de back-end, fornecendo alta disponibilidade e minimizando todos os efeitos negativos que podem ocorrer se uma unidade de disco rígido ou SSD falha ou for colocado off-line

Apenas um controlador está ativo em qualquer ponto no tempo. Se o controlador ativo falhar, o segundo controlador fica ativo automaticamente. 

Para obter mais informações, vá para [status e componentes de hardware de StorSimple](storsimple-monitor-hardware-status.md).

## <a name="storsimple-virtual-device"></a>Dispositivo virtual StorSimple

Você pode usar StorSimple para criar um dispositivo virtual que replica a arquitetura e os recursos do dispositivo de armazenamento físico híbrido. O dispositivo de virtual StorSimple (também conhecido como o StorSimple dispositivo Virtual) é executado em um único nó em uma máquina virtual Azure. (Um dispositivo virtual só pode ser criado em uma máquina virtual Azure. Você não pode criar um em um dispositivo de StorSimple ou um servidor local.) 

O dispositivo virtual tem os seguintes recursos:

- Ele se comporta como um dispositivo físico e pode oferecer uma interface de iSCSI para máquinas virtuais na nuvem. 
- Você pode criar um número ilimitado de dispositivos virtuais na nuvem e ativá-las e desativar conforme necessário. 
- Ele pode ajudar a simular ambientes locais em cenários de teste, desenvolvimento e recuperação de dados e pode ajudar com a recuperação de nível de item de backups. 

Com atualização 2 e posterior, o dispositivo virtual StorSimple está disponível em dois modelos: o dispositivo de 8010 (anteriormente conhecido como o modelo de 1100) e o dispositivo 8020. O dispositivo 8010 tem a capacidade máxima de 30 TB. O dispositivo 8020, que tira vantagem de armazenamento do Azure premium, tem a capacidade máxima de 64 TB. (No níveis locais, armazenamento do Azure premium armazena dados no SSDs enquanto o armazenamento padrão armazena os dados em unidades de disco rígido.) Observe que você deve ter uma conta de armazenamento do Azure premium para usar o armazenamento de premium. Para obter mais informações sobre o armazenamento de premium, vá para [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](../storage/storage-premium-storage.md).

Para obter mais informações sobre o dispositivo virtual StorSimple, vá para [implantar e gerenciar um dispositivo virtual StorSimple no Azure](storsimple-virtual-device-u2.md).

## <a name="storsimple-manager-service"></a>Serviço Gerenciador de StorSimple

Microsoft Azure StorSimple fornece uma interface de usuário baseada na web (o serviço do Gerenciador de StorSimple) que permite que você gerenciar data center centralmente e armazenamento em nuvem. Você pode usar o serviço do Gerenciador de StorSimple para executar as seguintes tarefas:

- Defina configurações de sistema para dispositivos de StorSimple.
- Configurar e gerenciar configurações de segurança para dispositivos StorSimple.
- Configure credenciais de nuvem e propriedades.
- Configurar e gerenciar volumes em um servidor.
- Configure grupos de volume.
- Fazer backup e restaurar os dados.
- Monitorar o desempenho.
- Examine as configurações do sistema e identificar possíveis problemas.

Você pode usar o serviço do Gerenciador de StorSimple para realizar todas as tarefas de administração, exceto aqueles que exigem o sistema de tempo, como configuração inicial e a instalação das atualizações.

Para obter mais informações, vá para [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell para StorSimple

Windows PowerShell para StorSimple fornece uma interface de linha de comando que você pode usar para criar e gerenciar o serviço do Microsoft Azure StorSimple e configurar e monitorar dispositivos de StorSimple. É uma interface de linha baseado no Windows PowerShell, que inclui cmdlets dedicado para gerenciar seu dispositivo StorSimple. Windows PowerShell para StorSimple tem recursos que permitem que você:

- Registre um dispositivo.
- Configure a interface de rede em um dispositivo.
- Instale determinados tipos de atualizações.
- Solucionar problemas de seu dispositivo acessando a sessão de suporte.
- Altere o estado do dispositivo.

Você pode acessar o Windows PowerShell para StorSimple de um console serial (em um computador de host conectado diretamente para o dispositivo) ou remotamente usando o Windows PowerShell remoto. Observe que alguns Windows PowerShell para tarefas de StorSimple, como o registro de dispositivos inicial, só pode ser feito no console serial. 

Para obter mais informações, vá para [Usar o Windows PowerShell para StorSimple administrar seu dispositivo](storsimple-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure cmdlets do PowerShell StorSimple

Os cmdlets do Azure PowerShell StorSimple são uma coleção de cmdlets do Windows PowerShell que permitam a automatizar tarefas de nível de serviço e migração da linha de comando. Para obter mais informações sobre os cmdlets do PowerShell do Azure para StorSimple, vá para a [referência de cmdlet](https://msdn.microsoft.com/library/dn920427.aspx).

## <a name="storsimple-snapshot-manager"></a>Gerenciador de instantâneo StorSimple

Gerenciador de instantâneo StorSimple é um snap-in do Console de gerenciamento da Microsoft (MMC) que você pode usar para criar consistente, cópias de backup no momento de local e dados de nuvem. O snap-in é executado em um host baseado no Windows Server. Você pode usar o Gerenciador de instantâneo StorSimple para:

- Configurar, fazer backup e excluir volumes.
- Configurar grupos de volume para garantir que o backup dos dados é consistente com o aplicativo.
- Gerencie políticas de backup para que os dados são feitos em um cronograma predeterminado e armazenados em um local designado (localmente ou na nuvem).
- Restaure volumes e arquivos individuais.

Backups são capturados como instantâneos, que gravem apenas as alterações feitas desde o último instantâneo e requerem muito menos espaço de armazenamento que backups completos. Você pode criar agendas de backup ou fazer backups imediatos conforme necessário. Além disso, você pode usar Gerenciador de instantâneo StorSimple estabelecer políticas de retenção que controlam quantos instantâneos serão salvos. Se você precisar subsequentemente restaurar dados de um backup, permite Gerenciador de instantâneo StorSimple você selecionar do catálogo de local ou instantâneos de nuvem. 

Se ocorrer um desastre ou se você precisar restaurar dados por outro motivo, Gerenciador de instantâneo StorSimple restaura incremental conforme necessário. A restauração de dados não exige que você desligue todo o sistema enquanto você restaurar um arquivo, substituir equipamento ou move operações para outro site.

Para obter mais informações, vá para [o que é o Gerenciador de instantâneo StorSimple?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Adaptador de StorSimple do SharePoint

Microsoft Azure StorSimple inclui o adaptador StorSimple do SharePoint, um componente opcional que transparente estende recursos de proteção de dados e armazenamento StorSimple em farms do SharePoint server. O adaptador funciona com um provedor de armazenamento de Blob remoto (EDR) e o recurso de EDR do SQL Server, permitindo que você mover BLOBs para um servidor de backup pelo sistema Microsoft Azure StorSimple. Microsoft Azure StorSimple, em seguida, armazena os dados BLOB localmente ou na nuvem, com base em uso.

O adaptador StorSimple do SharePoint é gerenciado a partir dentro do portal de Administração Central do SharePoint. Consequentemente, gerenciamento do SharePoint permanece centralizado e todo o armazenamento parece estar no farm do SharePoint.

Para obter mais informações, vá para [StorSimple adaptador do SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Tecnologias de gerenciamento de armazenamento
 
Além do dispositivo StorSimple dedicado, dispositivo virtual e outros componentes, o Microsoft Azure StorSimple usa as seguintes tecnologias de software para fornecer acesso rápido aos dados e para reduzir o consumo de armazenamento:

- [Armazenamento automático hierárquico](#automatic-storage-tiering) 
- [Provisionamento](#thin-provisioning) 
- [Duplicação e compactação](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Armazenamento automático hierárquico

Microsoft Azure StorSimple organiza automaticamente os dados em camadas lógicas com base em relação a outros dados, idade e uso atual. Dados que são mais ativos são armazenados localmente, enquanto menos dados ativos e inativos são migrados automaticamente para a nuvem. O diagrama a seguir ilustra essa abordagem de armazenamento.
 
![Níveis de armazenamento de StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Para habilitar o acesso rápido, StorSimple armazena dados muito ativos (dados quente) no SSDs no dispositivo StorSimple. Armazena dados usado ocasionalmente (quente dados) em unidades de disco rígido no dispositivo ou em servidores do data center. Move dados inativos, dados de backup, e os dados são mantidos para arquivamento ou fins de conformidade na nuvem. 

>[AZURE.NOTE] Na atualização 2 ou versões posteriores, você pode especificar um volume localmente fixos, caso em que os dados permanecerão no dispositivo local e não é hierárquico para a nuvem. 

StorSimple ajustará reorganiza dados e atribuições de armazenamento como padrões de uso. Por exemplo, algumas informações podem se tornar menos ativas ao longo do tempo. Como ele se torna cada vez menos ativo, ele é migrado do SSDs para unidades de disco rígido e depois para a nuvem. Se esses mesmos dados fica ativos novamente, ele é migrado para o dispositivo de armazenamento.

O processo de armazenamento hierárquica ocorre da seguinte maneira:

1. Um administrador do sistema configura uma conta de armazenamento de nuvem da Microsoft Azure.
2. O administrador usa o console serial e o serviço de Gerenciador de StorSimple (executando no portal de clássico do Azure) para configurar o servidor de arquivo e de dispositivo, criar políticas de proteção de dados e volumes. Máquinas de local (como servidores de arquivo) usam a Internet Small Computer System Interface (iSCSI) para acessar o dispositivo StorSimple.
3. Inicialmente, StorSimple armazena dados na camada SSD rápida do dispositivo.
4. Como a camada SSD aproxima capacidade, StorSimple elimina a duplicação compacta os blocos de dados mais antigo e move-os para a camada de disco rígido.
5. Como a capacidade de abordagens de nível de unidade de disco rígido, StorSimple criptografa os blocos de dados mais antigo e envia com segurança para a conta de armazenamento do Microsoft Azure via HTTPS.
6. Microsoft Azure cria várias réplicas dos dados no seu centro de dados e em um data center remoto, garantindo que os dados podem ser recuperados se ocorrer um desastre. 
7. Quando o servidor de arquivos solicita dados armazenados na nuvem, StorSimple retorna perfeitamente e armazena uma cópia na camada SSD do dispositivo StorSimple.

### <a name="thin-provisioning"></a>Provisionamento

Provisionamento fino é uma tecnologia de virtualização no qual o armazenamento disponível aparece exceda recursos físicos. Em vez de reserva armazenamento suficiente com antecedência, StorSimple usa provisionamento fino para alocar espaço apenas o suficiente para atender às necessidades atuais. A natureza elástica de armazenamento em nuvem facilita essa abordagem porque StorSimple pode aumentar ou diminuir o armazenamento em nuvem para atender às exigências de alteração. 

>[AZURE.NOTE] Volumes localmente fixados não são provisionados thin. Armazenamento alocado para um volume somente local está provisionado integralmente quando o volume é criado.

### <a name="deduplication-and-compression"></a>Duplicação e compactação

Microsoft Azure StorSimple usa duplicação e compactação de dados para reduzir ainda mais os requisitos de armazenamento.

Duplicação reduz a quantidade total de dados armazenados eliminando redundância no conjunto de dados armazenado. Como as informações forem alteradas, StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, StorSimple reduz a quantidade de dados armazenados, identificando e remova informações desnecessárias. 

>[AZURE.NOTE] Dados em volumes localmente fixados não é eliminação de duplicação ou compactados. No entanto, backups de volumes localmente fixados são eliminação de duplicação e compactados.

## <a name="storsimple-workload-summary"></a>Carga de trabalho de StorSimple resumo

Um resumo das cargas de trabalho StorSimple com suporte está tabulação abaixo.

| Cenário                  | Carga de trabalho                | Com suporte |  Restrições                                  | Versão              |
|---------------------------|-------------------------|-----------|------------------------------------------------|----------------------|
| Colaboração             | Compartilhamento de arquivo            | Sim       |                                                | Todas as versões         |
| Colaboração             | Compartilhamento de arquivo distribuído| Sim       |                                                | Todas as versões         |
| Colaboração             | SharePoint              | Sim *      |Suporte apenas com volumes localmente fixados      | Atualização 2 e posterior   |
| Arquivamento                  | Arquivamento simples   | Sim       |                                                | Todas as versões         |
| Virtualização            | Máquinas virtuais        | Sim *      |Suporte apenas com volumes localmente fixados      | Atualização 2 e posterior   |
| Banco de dados                  | SQL                     | Sim *      |Suporte apenas com volumes localmente fixados      | Atualização 2 e posterior   |
| Vídeo        | Vídeo      | Sim *       |Têm suporte quando o dispositivo StorSimple é dedicado somente para essa carga de trabalho| Atualização 2 e posterior   |
| Backup                    | Backup de destino principal | Sim *      |Suporte quando StorSimple dispositivo é dedicado somente para essa carga de trabalho| Atualização 3 e posterior |
| Backup                    | Backup de destino secundário | Sim *      |Têm suporte quando o dispositivo StorSimple é dedicado somente para essa carga de trabalho| Atualização 3 e posterior |

*Sim & 42; -Restrições e diretrizes de solução devem ser aplicadas.*

As cargas de trabalho a seguintes não são suportadas pelos dispositivos de série 8000 StorSimple. Se implantado em StorSimple, essas cargas de trabalho resultará em uma configuração sem suporte.

-  Imagens médicas
-  Exchange
-  VDI
-  Oracle
-  SAP
-  Grande volume
-  Distribuição de conteúdo
-  Inicialização de SCSI

A seguir é uma lista dos componentes de infraestrutura de StorSimple com suporte. 

| Cenário | Carga de trabalho      | Com suporte |  Restrições                                 | Versão      |
|----------|---------------|-----------|-----------------------------------------------|--------------|
| Geral  | Rota expressa | Sim       |                                               |Todas as versões |
| Geral  | DataCore FC   | Sim *       |Compatível com DataCore SANsymphony            | Todas as versões |
| Geral  | DFSR          | Sim *      |Suporte apenas com volumes localmente fixados     | Todas as versões |
| Geral  | Indexação      | Sim *       |Volumes hierárquicos, indexação de metadados somente é suportado (sem dados).<br>Para volumes localmente fixados, a indexação completa é suportado.| Todas as versões |
| Geral  | Antivírus    | Sim *       |Para volumes hierárquicos, é suportado apenas varredura em Abrir e fechar.<br> Para volumes localmente fixados, há suporte para verificação completa.| Todas as versões |

*Sim & 42; -Restrições e diretrizes de solução devem ser aplicadas.*

## <a name="storsimple-terminology"></a>Terminologia de StorSimple 

Antes de implantar sua solução Microsoft Azure StorSimple, recomendamos que você examine os seguintes termos e definições.

### <a name="key-terms-and-definitions"></a>Principais termos e definições

| Termo (acrônimo ou abreviação) | Descrição |
| ------------------------------ | ---------------- |
| registro de controle de acesso (ACR)    | Um registro associado a um volume em seu dispositivo Microsoft Azure StorSimple que determina quais hosts podem se conectar a ele. A determinação baseia-se no iSCSI qualificado nome (IQN) dos hosts (contidos na ACR) que estão se conectando ao seu dispositivo StorSimple.|
| AES-256                        | Um algoritmo de padrão de criptografia avançada (AES) de 256 bits para criptografar dados como ele move de e para a nuvem. |
| tamanho de unidade de alocação (Austrália)     | A menor quantidade de espaço em disco que pode ser alocada para manter um arquivo no seu Windows sistemas de arquivos. Se um tamanho de arquivo não for um múltiplo par do tamanho do cluster, deverá ser usado espaço extra para armazenar o arquivo (até o próximo múltiplo do tamanho do cluster) resulta em espaço perdido e fragmentação do disco rígido. <br>A Austrália recomendada para volumes de Azure StorSimple está 64 KB porque ele funciona bem com os algoritmos de duplicação.|
| armazenamento automatizado hierárquico      | Mover automaticamente os dados menos ativos do SSDs para unidades de disco rígido e depois para um nível na nuvem e, em seguida, permitindo o gerenciamento de todo o armazenamento de uma interface de usuário central.|
| Catálogo de backup | Uma coleção de backups, geralmente relacionadas pelo tipo de aplicativo que foi usado. Essa coleção é exibida na página de catálogo de Backup do serviço do Gerenciador de StorSimple da interface do usuário.|
| arquivo de catálogo de backup             | Um arquivo que contém uma lista de instantâneos disponíveis atualmente armazenados no banco de dados do Gerenciador de instantâneo StorSimple backup. |
| política de backup                   | Uma seleção de volumes, tipo de backup e um calendário que permite que você crie backups em um cronograma predefinido.|
| objetos binários grandes (BLOBs)    | Uma coleção de dados binários armazenados como uma única entidade em um sistema de gerenciamento de banco de dados. BLOBs são geralmente imagens, áudio ou outros objetos multimídia, embora algumas vezes binário código executável é armazenado como um BLOB.|
| Protocolo de autenticação de Handshake de desafio (CHAP) | Um protocolo usado para autenticar o ponto de uma conexão, com base no ponto de compartilhamento de uma senha ou segredo. CHAP pode ser unidirecional ou comum. Com o CHAP unidirecional, o destino autentica um iniciador. Comum CHAP requer que o destino autenticar o iniciador e o iniciador autentica o destino. | 
| clonar                          | Uma cópia de um volume. |
|Nuvem como uma camada (CaaT)          | Armazenamento integrado como uma camada na arquitetura de armazenamento para que todo o armazenamento parece ser parte da rede de uma empresa de armazenamento em nuvem.|
| (provedor de serviço de nuvem)   | Um provedor de serviços de computação em nuvem.|
| instantâneo de nuvem                 | Uma cópia point-in-time dos dados de volume armazenado na nuvem. Um instantâneo de nuvem é equivalente a um instantâneo replicado em um sistema de armazenamento externo diferente. Nuvem instantâneos são particularmente úteis em cenários de recuperação de desastres.|
| chave de criptografia de armazenamento de nuvem   | Uma senha ou uma chave usada pelo seu dispositivo StorSimple para acessar os dados criptografados enviados por seu dispositivo na nuvem.|
| Atualizando reconhecimento de cluster         | Gerenciando atualizações de software em servidores em um cluster de failover para que as atualizações tenham mínimo ou nenhum efeito sobre a disponibilidade do serviço.|
| DataPath                       | Uma coleção de unidades funcionais que realizam operações de processamento de dados interconectados.|
| desativar                     | Uma ação permanente que quebra a conexão entre o dispositivo de StorSimple e o serviço de nuvem associado. Instantâneos de nuvem do dispositivo permanecem após esse processo e podem ser duplicados ou usados para recuperação de dados.|
| espelhamento de disco                 | Replicação de volumes de disco lógico em separada rígido unidades em tempo real para garantir a disponibilidade contínua.|
| espelhamento de disco dinâmico         | Replicação de volumes de disco lógico em discos dinâmicos.|
| discos dinâmicos                  | Um formato de volume de disco que usa o Gerenciador de disco lógico (LDM) para armazenar e gerenciar dados em vários discos físicos. Discos dinâmicos podem ser ampliados para fornecer mais espaço livre.|
| Estendido circunscrição de um monte de discos (EBOD) | Um compartimento secundário do seu dispositivo Microsoft Azure StorSimple que contém discos extra no disco rígido de armazenamento adicional.|
| provisionamento FAT               | Um armazenamento convencional provisionamento no qual armazenamento espaço é alocado com base nas previsto necessidades (e é geralmente além da necessidade atual). Consulte também *provisionamento fina*.|
| unidade de disco rígido (HD)          | Uma unidade que usa platters rotação para armazenar dados.|
| armazenamento em nuvem híbrida           | Uma arquitetura de armazenamento que usa recursos locais e fora da empresa, incluindo armazenamento em nuvem.|
| Internet Small Computer System Interface (iSCSI) | Um padrão de armazenamento baseado em protocolo IP a rede para vinculação de equipamento de armazenamento de dados ou recursos.|
| iniciador iSCSI                 | Um componente de software que permite que um computador host executando o Windows para se conectar a uma rede de armazenamento baseado em iSCSI externo.|
| iSCSI nome qualificado (IQN)      | Um nome exclusivo que identifica um destino ou iniciador iSCSI.|
| destino iSCSI                    | Um componente de software que fornece iSCSI centralizado subsistemas de disco em redes de área de armazenamento.|
| Live arquivamento                  | Uma abordagem de armazenamento em que dados de arquivamento são acessíveis o tempo todo (ele não está armazenado externamente em fita, por exemplo). Microsoft Azure StorSimple usa arquivamento ao vivo.|
|volume localmente fixado | um volume que reside no dispositivo e nunca é hierárquico para a nuvem. |
| instantâneo local                  | Uma cópia point-in-time dos dados de volume armazenado no dispositivo StorSimple do Microsoft Azure.|
| StorSimple do Microsoft Azure      | Uma solução eficiente consiste em um dispositivo de armazenamento do data center e software que permite que as organizações que utilizam o armazenamento de nuvem como se fosse o armazenamento do data center. StorSimple simplifica a proteção de dados e gerenciamento de dados, reduzindo os custos. A solução consolida primária armazenamento, arquivamento, backup e recuperação de desastres (DR) por meio da integração perfeita com a nuvem. Combinando o gerenciamento de dados de armazenamento e nuvem de SAN em uma plataforma corporativa, StorSimple dispositivos habilitar velocidade, simplicidade e confiabilidade para todas as necessidades de armazenamento relacionados.|
| Energia e refrigeração módulo (PCM)  | Componentes de hardware do seu dispositivo de StorSimple consiste em alimentação e o ventilador, portanto, o nome de energia e refrigeração módulo. Compartimento primário do dispositivo tem dois PCMs 764W enquanto o compartimento EBOD tem dois PCMs 580W.|
| compartimento principal               | Compartimento principal do seu dispositivo de StorSimple que contém os controladores de plataforma do aplicativo.|
| objetivo de tempo de recuperação (RTO)   | A quantidade máxima de tempo que deve ser feito antes de um processo de negócios ou um sistema totalmente é restaurada após um desastre.| 
|série anexados SCSI (SAS)       | Um tipo de unidade de disco rígido (HD).|
| chave de criptografia de dados de serviço     | Uma chave disponibilizada para qualquer dispositivo StorSimple novo que registra com o serviço Gerenciador de StorSimple. Os dados de configuração transferidos entre o serviço do Gerenciador de StorSimple e o dispositivo estão criptografados com uma chave pública e, em seguida, podem ser descriptografados somente no dispositivo usando uma chave privada. Chave de criptografia de dados de serviço permite que o serviço obter esta chave privada para descriptografia.|
| chave do registro de serviço        | Uma chave que ajuda a registrar o dispositivo de StorSimple com o serviço Gerenciador de StorSimple para que ele apareça no portal de clássico do Azure para ainda mais ações de gerenciamento.|
| Interface de sistema de computadores pequenos (SCSI) | Um conjunto de padrões para física conectando computadores e passar dados entre elas.|
| unidade de estado sólido (SSD)         | Um disco que contenha sem partes móveis; Por exemplo, uma unidade flash.|
| conta de armazenamento                 | Um conjunto de credenciais de acesso vinculada à sua conta de armazenamento para um provedor de serviços de nuvem determinado.| 
| Adaptador de StorSimple do SharePoint| Um componente do Microsoft Azure StorSimple que transparente estende a proteção de dados e armazenamento de StorSimple em farms do SharePoint server.|
| Serviço Gerenciador de StorSimple      | Extensão do portal de clássico do Azure que permite que você gerencie seu Azure StorSimple locais e dispositivos virtuais.|
| Gerenciador de instantâneo StorSimple     | Um Console de gerenciamento da Microsoft (MMC) snap-in de gerenciamento de operações de backup e restauração no Microsoft Azure StorSimple.|
| fazer backup                     | Um recurso que permite ao usuário fazer um backup interativo de um volume. É uma maneira alternativa de fazer um backup manual de um volume em vez de fazer um backup automatizado por meio de uma diretiva definida.|
| provisionamento               | Um método de otimizar a eficiência com o qual o espaço de armazenamento disponível é usado nos sistemas de armazenamento. No provisionamento fina, o armazenamento é alocado entre vários usuários com base no espaço mínimo necessário por cada usuário a qualquer momento. Consulte também *fat de provisionamento*.|
| hierarquia | Organizar dados em grupos lógicos com base em relação a outros dados, idade e uso atual. StorSimple organiza automaticamente os dados em camadas.   |
| volume                          | Áreas de armazenamento lógico apresentadas em forma de unidades. Volumes de StorSimple correspondem aos volumes montados pelo host, inclusive aqueles descobertos por meio do uso iSCSI e um dispositivo StorSimple.|
 | contêiner de volume                | Um agrupamento de volumes e as configurações que se aplicam a eles. Todos os volumes em seu dispositivo de StorSimple são agrupados em contêineres de volume. Configurações de contêiner de volume incluem contas de armazenamento, configurações de criptografia para dados enviados para a nuvem com chaves de criptografia associadas e largura de banda consumida para operações envolvendo na nuvem.|
| grupo de volume                    | No Gerenciador de instantâneo de StorSimple, um grupo de volume é uma coleção de volumes configurados para facilitar o processamento de backup.|
| Serviço de cópia de sombra de volume (VSS)| Um serviço de sistema operacional Windows Server que facilita a consistência do aplicativo, comunicando com aplicativos de reconhecimento de VSS para coordenar a criação de instantâneos incrementais. VSS garante que os aplicativos são temporariamente inativos quando instantâneos são criados.|
| Windows PowerShell para StorSimple | Uma interface de linha de comando baseado no Windows PowerShell usada para operar e gerenciar seu dispositivo StorSimple. Mantendo alguns dos recursos básicos do Windows PowerShell, essa interface possui cmdlets dedicado adicionais que são voltado para gerenciar um dispositivo StorSimple.|


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [segurança de StorSimple](storsimple-security.md).




 

 
