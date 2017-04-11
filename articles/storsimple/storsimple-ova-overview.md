<properties
   pageTitle="Visão geral de Array Virtual StorSimple | Microsoft Azure"
   description="Descreve a matriz Virtual StorSimple, uma solução de armazenamento integrado que gerencia tarefas de armazenamento entre um dispositivo virtual local e o armazenamento de nuvem do Microsoft Azure."
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
   ms.date="10/06/2016"
   ms.author="alkohli" />

# <a name="introduction-to-the-storsimple-virtual-array"></a>Introdução à matriz Virtual StorSimple

## <a name="overview"></a>Visão geral

Bem-vindo ao Microsoft Azure StorSimple Virtual Array, uma solução de armazenamento integrado que gerencia tarefas de armazenamento entre um dispositivo virtual local executado em um hipervisor e o armazenamento de nuvem do Microsoft Azure. A matriz Virtual (também conhecido como o StorSimple local virtual dispositivo) é um servidor de arquivos eficiente, econômica e facilmente gerenciável ou solução de servidor de iSCSI que elimina muitos dos problemas e despesas associadas com a proteção de dados e armazenamento do enterprise. A matriz Virtual é especialmente adequada para cenários do office (ROBO) remoto/filial.

Esta visão geral se concentra no Array Virtual. 

- Para obter uma visão geral da série 8000 StorSimple, vá para [série 8000 StorSimple: uma solução de nuvem híbrida](storsimple-overview.md). 

- Para obter informações sobre o dispositivo de série StorSimple 5000/7000, vá para [A Ajuda Online do StorSimple](http://onlinehelp.storsimple.com/).

A matriz Virtual suporta o iSCSI ou o protocolo de bloco de mensagens do servidor (SMB). Ele é executado em sua infraestrutura de hipervisor existente e fornece armazenamento hierárquico para a nuvem, nuvem backup, restauração rápida, recuperação de nível de item e os recursos de recuperação de desastres.

A tabela a seguir resume os recursos importantes da matriz Virtual.

| Recurso | Array virtual |
| ------- | ------------- |
|Requisitos de instalação | Usa a infraestrutura de virtualização (Hyper-V ou VMware)|
| Disponibilidade | Nó único |
| Capacidade total (incluindo a nuvem) |Até 64 TB de capacidade útil por dispositivo virtual |
| Capacidade de local | 390 GB para 6,4 TB de capacidade útil por dispositivo virtual (necessidade provisionar 500 GB a 8 TB de espaço em disco)|
| Protocolos nativos | iSCSI ou SMB |
| Objetivo de tempo de recuperação (RTO) | iSCSI: menos de 2 minutos, independentemente de tamanho |
| Objetivo de ponto de recuperação (RPO) | Backups diários e sob demanda |
| Armazenamento hierárquico | Usos de calor mapeamento para determinar quais dados devem ser hierárquico ou reduzir |
| Suporte | Infraestrutura de virtualização suportada pelo fornecedor |
| Desempenho | Varia dependendo da infraestrutura subjacente |
| Mobilidade de dados | Pode restaurar para o mesmo dispositivo ou nível de item recuperação (servidor de arquivos) |
| Níveis de armazenamento | Nuvem e armazenamento de hipervisor local |
| Tamanho de compartilhamento |Hierárquico: até 20 TB; localmente fixos: até 2 TB |
| Tamanho do volume |Hierárquico: até 5 TB; localmente fixos: até 500 GB |
| Instantâneos | Falha consistente |
| Recuperação de nível de item | Sim; os usuários poderão restaurar de compartilhamentos |

## <a name="why-use-storsimple"></a>Por que usar StorSimple?

StorSimple conecta usuários e servidores ao armazenamento do Azure em minutos, sem modificações de aplicativo.

A tabela a seguir descreve alguns dos principais benefícios que fornece a solução de Array Virtual.

| Recurso | Benefício |
|---------|---------|
| Integração transparente | A matriz Virtual suporta o iSCSI ou o protocolo SMB. A movimentação de dados entre o nível local e a camada de nuvem é transparente para o usuário.|
| Custos de armazenamento reduzidos | Com StorSimple, você provisionar armazenamento local suficiente para atender às exigências atuais para os dados de alta mais usados. Conforme as necessidades de armazenamento crescer, StorSimple níveis frio dados econômico armazenamento em nuvem. Os dados são eliminação de duplicação e compactados antes de enviar para a nuvem para reduzir ainda mais os requisitos de armazenamento e despesas.|
| Gerenciamento simplificado do armazenamento | StorSimple fornece gerenciamento centralizado na nuvem usando o Gerenciador de StorSimple para gerenciar vários dispositivos.| 
| Recuperação de dados aprimorada e conformidade | StorSimple facilita a recuperação de dados mais rápida restaurando os metadados imediatamente e restaurar os dados conforme necessário. Isso significa que as operações poderão continuar com interrupção mínima.|
| Mobilidade de dados | Dados hierárquico para a nuvem pode ser acessado de outros sites para fins de recuperação e migração. Observe que você pode restaurar dados somente para a matriz Virtual original. No entanto, você usa os recursos de recuperação de desastres para restaurar toda a matriz Virtual a outra matriz Virtual.|

## <a name="storsimple-workload-summary"></a>Carga de trabalho de StorSimple resumo

Um resumo das cargas de trabalho StorSimple com suporte está tabulação abaixo.

| Cenário                | Carga de trabalho              | Com suporte |  Restrições                                  | Versão              |
|-------------------------|-----------------------|-----------|------------------------------------------------|----------------------|
| Colaboração de ROBO      | Compartilhamento de arquivo          | Sim       | Consulte [limites máximos para servidor de arquivos](storsimple-ova-limits.md). <br>Consulte [requisitos do sistema para as versões suportadas do SMB](storsimple-ova-system-requirements.md).   | Todas as versões      |


## <a name="workflows"></a>Fluxos de trabalho

A matriz Virtual StorSimple é particularmente adequada para os seguintes fluxos de trabalho:

- [Gerenciamento de armazenamento baseado em nuvem](#cloud-based-storage-management)
- [Backup independente de local](#location-independent-backup)
- [Recuperação de proteção e de dados](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Gerenciamento de armazenamento baseado em nuvem

Você pode usar o serviço de Gerenciador de StorSimple em execução no portal de clássico do Azure para gerenciar dados armazenados em vários dispositivos e em vários locais. Isso é especialmente útil em cenários de ramificação distribuído. Observe que você deve criar instâncias separadas do serviço Gerenciador de StorSimple para gerenciar matrizes Virtual e dispositivos de StorSimple físicos. 

![gerenciamento de armazenamento baseado em nuvem](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Backup independente de local

Com a matriz Virtual, instantâneos de nuvem fornecem uma cópia independente de local no momento de um volume ou compartilhar. Nuvem instantâneos estão ativados por padrão e não podem ser desabilitados. Todos os volumes e compartilhamentos são backup ao mesmo tempo por meio de uma única diretiva de backup diário, e você pode realizar backups adhoc adicionais sempre que necessário.

### <a name="data-protection-and-disaster-recovery"></a>Recuperação de proteção e de dados

A matriz Virtual compatível com os dados proteção e recuperação cenários a seguir:

- **Restaurar volume ou compartilhamento** – uso a restauração como novo fluxo de trabalho para recuperar um volume ou compartilhar. Use essa abordagem para recuperar o compartilhamento ou todo o volume.
- **Recuperação de nível de item** — compartilhamentos permitem acesso simplificado aos backups recentes. Você pode facilmente recuperar um arquivo individual de uma pasta de .backup especial disponível na nuvem. Esse recurso de restauração é controlado pelo usuário e nenhuma intervenção administrativa é necessária.
- **Recuperação de dados** – Use o recurso de failover de recuperar todos os volumes ou compartilhamentos para um novo Array Virtual. Você cria o novo Array Virtual Registre-se com o serviço Gerenciador de StorSimple e Falha ao longo da matriz Virtual original. O novo Array Virtual depois assumirá os recursos provisionados. 

## <a name="virtual-array-components"></a>Componentes de matriz virtual

A matriz Virtual inclui os seguintes componentes:

- [Array Virtual](#virtual-array) – um dispositivo de armazenamento de nuvem híbrida com base em uma máquina virtual provisionada no seu ambiente virtualizado ou hipervisor.  
- [Serviço Gerenciador de StorSimple](#storsimple-manager-service) – uma extensão do portal de clássico do Azure que permite que você gerencie um ou mais dispositivos de StorSimple de uma única interface da web que você pode acessar de diferentes locais geográficos. Você pode usar o serviço do Gerenciador de StorSimple para criar e gerenciar serviços, exibir e gerenciar dispositivos e alertas e gerenciar volumes, compartilhamentos e instantâneos existentes.
- [Interface de usuário do web local](#local-web-user-interface) – uma interface de usuário baseada na web que é usado para configurar o dispositivo para que ele pode se conectar à rede local e, em seguida, registrar o dispositivo com o serviço Gerenciador de StorSimple. 
- [Interface de linha](#command-line-interface) – uma interface do Windows PowerShell que você pode usar para iniciar uma sessão de suporte no Array Virtual.
As seções a seguir descrevem cada um desses componentes mais detalhadamente e explicam como a solução organiza dados aloca o armazenamento e facilita o gerenciamento de armazenamento e proteção de dados.

### <a name="virtual-array"></a>Array virtual

A matriz Virtual é uma solução de armazenamento de nó único que fornece armazenamento principal, gerencia a comunicação com armazenamento em nuvem e ajuda a garantir a segurança e a confidencialidade de todos os dados armazenados no dispositivo.

A matriz Virtual está disponível em um modelo que está disponível para download. A matriz de armazenamento tem uma capacidade máxima de 6,4 TB no dispositivo (com um requisito de armazenamento subjacente de 8 TB) e 64 TB incluindo armazenamento em nuvem. 

A matriz Virtual tem os seguintes recursos:

- Ele é econômico. Ele usa sua infraestrutura de virtualização existente e pode ser implantado em seu hipervisor Hyper-V ou VMware existente.
- Ele está localizado no data center e pode ser configurado como um servidor de iSCSI ou um arquivo. 
- Ele é integrado com a nuvem.
- Backups são armazenados na nuvem, que pode facilitar a recuperação de dados e simplificar a recuperação de nível de item (ILR). 
- Você pode aplicar atualizações ao Array Virtual, assim como você faria aplicá-los a um dispositivo físico.

>[AZURE.NOTE] Uma matriz Virtual não pode ser expandida. Portanto, é importante provisionar armazenamento adequado quando você cria o dispositivo virtual. 

### <a name="storsimple-manager-service"></a>Serviço Gerenciador de StorSimple

Microsoft Azure StorSimple fornece uma interface de usuário baseada na web (o serviço do Gerenciador de StorSimple) que permite que você gerenciar data center centralmente e armazenamento em nuvem. Você pode usar o serviço do Gerenciador de StorSimple para executar as seguintes tarefas:

- Gerencie vários conjuntos de Virtual StorSimple a partir de um único serviço. 
- Configurar e gerenciar configurações de segurança para dispositivos StorSimple. (Criptografia na nuvem depende Microsoft Azure APIs.)
- Configure propriedades e credenciais de conta de armazenamento.
- Configurar e gerenciar volumes ou compartilhamentos.
- Fazer backup e restaurar dados em volumes ou compartilhamentos.
- Monitorar o desempenho.
- Examine as configurações do sistema e identificar possíveis problemas.

Use o serviço do Gerenciador de StorSimple para executar administração diária da sua matriz Virtual.

Para obter mais informações, vá para [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Interface de usuário do local da web

A matriz Virtual inclui uma interface de usuário baseada na web que é usado para configuração única e registro do dispositivo com o serviço Gerenciador de StorSimple. Você pode usá-lo para desligar e reiniciar a matriz Virtual, executar testes de diagnóstico, atualizar software, alterar a senha de administrador do dispositivo, exibir logs do sistema e contate o Microsoft Support para enviar uma solicitação de serviço. 

Para obter informações sobre como usar a interface do usuário baseada na web, vá para [usar a interface do usuário baseada na web para administrar sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interface de linha

A interface do Windows PowerShell incluída permite que você iniciar uma sessão de suporte com o Microsoft Support para que eles podem ajudar a solucionar e resolver problemas que podem ocorrer em seu dispositivo virtual.

## <a name="storage-management-technologies"></a>Tecnologias de gerenciamento de armazenamento

Além da matriz Virtual e outros componentes, a solução de StorSimple usa as seguintes tecnologias de software para fornecer acesso rápido aos dados importantes, reduzir o consumo de armazenamento e proteger os dados armazenados em seu Array Virtual:

- [Armazenamento automático hierárquico](#automatic-storage-tiering) 
- [Localmente fixados compartilhamentos e volumes](#locally-pinned-shares-and-volumes)
- [Duplicação e compactação de dados hierárquico ou backup na nuvem](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
- [Backups programados e sob demanda](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Armazenamento automático hierárquico

A matriz Virtual usa um novo mecanismo hierárquico para gerenciar dados armazenados em matriz Virtual e a nuvem. Existem apenas duas camadas: armazenamento em nuvem Array Virtual local e do Azure. A matriz Virtual StorSimple automaticamente organiza dados em camadas com base em um mapa de calor, que rastreia uso atual, idade e relações a outros dados. Dados que são mais ativos (melhores) são armazenados localmente, enquanto menos dados ativos e inativos são migrados automaticamente para a nuvem. (Todos os backups são armazenados na nuvem). StorSimple ajustará reorganiza dados e atribuições de armazenamento como padrões de uso. Por exemplo, algumas informações podem se tornar menos ativas ao longo do tempo. Como ele se torna cada vez menos ativo, ele é hierárquica check-out para a nuvem. Se esses mesmos dados fica ativos novamente, ele é hierárquica para a matriz de armazenamento.

Dados de um determinado compartilhar hierárquico ou volume certamente seu próprio espaço de nível local. (aproximadamente 10% do total provisionado espaço para o compartilhamento ou volume). Enquanto isso reduz o armazenamento disponível no dispositivo virtual para o compartilhamento ou volume, ele garante que hierárquico para um compartilhamento ou volume não será afetada pelas necessidades hierárquica de outros compartilhamentos ou volumes. Portanto, uma carga de trabalho muito ocupada em um compartilhamento ou volume não pode forçar todas as outras cargas de trabalho na nuvem. 

![armazenamento automático hierárquico](./media/storsimple-ova-overview/automatic-storage-tiering.png)

>[AZURE.NOTE] Você pode especificar um volume localmente fixos, caso em que os dados permanecerão no Array Virtual e nunca é hierárquico para a nuvem. Para obter mais informações, vá para [localmente fixos compartilhamentos e volumes](#locally-pinned-shares-and-volumes).

### <a name="locally-pinned-shares-and-volumes"></a>Localmente fixados compartilhamentos e volumes

Você pode criar compartilhamentos apropriados e volumes fixados como localmente. Esse recurso garante que os dados necessários por aplicativos essenciais permanecem na matriz Virtual e nunca é hierárquico para a nuvem. Localmente fixados compartilhamentos e volumes têm os seguintes recursos: 

- Eles não estão sujeitos a nuvem latências ou problemas de conectividade.
- Eles ainda se beneficiar dos recursos de recuperação StorSimple nuvem desastres e backup.

Você pode restaurar um compartilhamento localmente fixado ou volume como hierárquico ou um compartilhamento hierárquico ou volume como localmente fixos. 

Para obter mais informações sobre volumes localmente fixados, vá para [usar o serviço de Gerenciador de StorSimple para gerenciar volumes](storsimple-manage-volumes-u2.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Duplicação e compactação de dados hierárquico ou backup na nuvem

StorSimple usa duplicação e compactação de dados para reduzir ainda mais os requisitos de armazenamento na nuvem. Duplicação reduz a quantidade total de dados armazenados eliminando redundância no conjunto de dados armazenado. Como as informações forem alteradas, StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, StorSimple reduz a quantidade de dados armazenados, identificando e removendo informações duplicadas. 

>[AZURE.NOTE] Dados armazenados no Array Virtual não é eliminação de duplicação ou compactados. Todos eliminação de duplicação e compactação ocorre logo antes que os dados são enviados para a nuvem.

### <a name="scheduled-and-on-demand-backups"></a>Backups programados e sob demanda

Recursos de proteção de dados de StorSimple permitem que você crie backups sob demanda. Além disso, um agendamento de backup padrão garante que os dados são feitos backup diariamente. São realizados backups no formulário de instantâneos incrementais, que são armazenados na nuvem. Instantâneos, que registrar apenas as alterações feitas desde o último backup, podem ser restaurados rapidamente e criados. Esses instantâneos podem ser muito importantes no cenários de recuperação de desastres porque eles substituam sistemas de armazenamento secundário (como backup de fita) e permitem restaurar dados para o data center ou para locais alternativos, se necessário.

## <a name="next-steps"></a>Próximas etapas

Saiba como [preparar o portal de Array Virtual](storsimple-ova-deploy1-portal-prep.md).


