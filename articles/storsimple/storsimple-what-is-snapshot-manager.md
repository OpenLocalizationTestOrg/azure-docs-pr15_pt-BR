<properties 
   pageTitle="O que é o Gerenciador de instantâneo StorSimple? | Microsoft Azure"
   description="Descreve o Gerenciador de instantâneo StorSimple, sua arquitetura e seus recursos."
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

# <a name="what-is-storsimple-snapshot-manager"></a>O que é o Gerenciador de instantâneo StorSimple?

## <a name="overview"></a>Visão geral

Gerenciador de instantâneo StorSimple é um snap-in do Console de gerenciamento da Microsoft (MMC) que simplifica a proteção de dados e gerenciamento de backup em um ambiente do Microsoft Azure StorSimple. Com o Gerenciador de instantâneo de StorSimple, você pode gerenciar dados do Microsoft Azure StorSimple no Centro de dados e na nuvem como uma solução de armazenamento integrado único, portanto, simplificando processos de backup e reduzindo custos.

Esta visão geral apresenta o Gerenciador de instantâneo StorSimple, descreve seus recursos e explica sua função no Microsoft Azure StorSimple. 

Para obter uma visão geral do sistema Microsoft Azure StorSimple inteiro, incluindo o dispositivo de StorSimple, serviço Gerenciador de StorSimple, Gerenciador de instantâneo StorSimple e StorSimple adaptador para SharePoint, consulte [série 8000 StorSimple: uma solução de armazenamento de nuvem híbrida](storsimple-overview.md). 
 
>[AZURE.NOTE] 
>
>- Você não pode usar o Gerenciador de instantâneo StorSimple para gerenciar o Microsoft Azure StorSimple Virtual matrizes (também conhecido como StorSimple local dispositivos virtuais).
>
>- Se você planeja instalar StorSimple atualização 2 no seu dispositivo StorSimple, certifique-se de baixar a versão mais recente do Gerenciador de instantâneo StorSimple e instalá-lo **antes de instalar StorSimple atualização 2**. A versão mais recente do Gerenciador de instantâneo StorSimple é compatível e funciona com todas as versões do Microsoft Azure StorSimple. Se você estiver usando a versão anterior do Gerenciador de instantâneo StorSimple, você precisará atualizá-lo (você não precisa desinstalar a versão anterior antes de instalar a nova versão).

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Arquitetura e a finalidade do Gerenciador de instantâneo StorSimple

Gerenciador de instantâneo StorSimple fornece um console de gerenciamento central que você pode usar para criar consistente, cópias de backup no momento de local e dados de nuvem. Por exemplo, você pode usar o console para:

- Configurar, fazer backup e excluir volumes.
- Configurar grupos de volume para garantir que o backup dos dados é consistente com o aplicativo.
- Gerencie políticas de backup para que o backup dos dados em um cronograma predeterminado.
- Criar locais e na nuvem instantâneos, que podem ser armazenados na nuvem e usados para recuperação de dados.

O Gerenciador de instantâneo StorSimple busca na lista de aplicativos registrados com o provedor VSS do host. Em seguida, para criar backups consistentes com o aplicativo, ele verifica os volumes usados por um aplicativo e sugere grupos de volume para configurar. Gerenciador de instantâneo StorSimple usa esses grupos de volume para gerar cópias de backup que são consistentes com o aplicativo. (A consistência do aplicativo existe quando todos os arquivos relacionados e bancos de dados são sincronizados e representam o verdadeiro estado do aplicativo em um ponto específico no tempo.) 

Gerenciador de instantâneo StorSimple backups assumem a forma de instantâneos incrementais, que capture apenas as alterações feitas desde o último backup. Como resultado, backups exigir menos armazenamento e podem ser criados e restaurados rapidamente. Gerenciador de instantâneo StorSimple usa o Windows Volume Shadow cópia Service (VSS) para garantir que os instantâneos capturam dados consistentes com o aplicativo. (Para obter mais informações, vá para a integração com a seção de serviço de cópia de sombra de Volume do Windows). Com o Gerenciador de instantâneo de StorSimple, você pode criar agendas de backup ou fazer backups imediatos conforme necessário. Se você precisar restaurar dados de um backup, permite StorSimple Gerenciador de instantâneo que você selecione um catálogo de local ou instantâneos de nuvem. Azure StorSimple restaura apenas os dados que é necessária conforme necessário, o que impede atrasos na disponibilidade de dados durante operações de restauração.)

![Arquitetura de StorSimple Gerenciador de instantâneo](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Arquitetura de StorSimple Gerenciador de instantâneo** 

## <a name="support-for-multiple-volume-types"></a>Suporte para vários tipos de volume

Você pode usar o Gerenciador de instantâneo StorSimple para configurar e backup os seguintes tipos de volumes: 

- **Volumes básicos** – um volume básico é uma única partição em um disco básico. 

- **Volumes simples** – um volume simples é um volume dinâmico que contém o espaço em disco de um único disco dinâmico. Um volume simples consiste em uma única região em um disco ou várias regiões vinculadas no mesmo disco. (Você pode criar volumes simples somente em discos dinâmicos). Volumes simples não são tolerância.

- **Volumes dinâmicos** – um volume dinâmico é um volume criado em um disco dinâmico. Discos dinâmicos usam um banco de dados para controlar informações sobre volumes que estão contidos em discos dinâmicos em um computador. 

- **Volumes dinâmicos com espelhamento** – volumes dinâmicos com espelhamento são criados na arquitetura RAID 1. Com o RAID 1, dados idênticos escritos em duas ou mais disco, produzir um conjunto espelhado. Uma solicitação de leitura, em seguida, pode ser tratada por qualquer disco que contém os dados solicitados.

- **Volumes compartilhados de cluster** – com volumes compartilhados de cluster (CSVs), vários nós em um cluster de failover podem simultaneamente ler ou escrever no mesmo disco. Failover de um nó para outro nó pode ocorrer rapidamente, sem a necessidade de uma alteração na propriedade de unidade ou montar, desmontar e removendo um volume. 

>[AZURE.IMPORTANT] Não misture CSVs e CSVs no mesmo instantâneo. Não há suporte para a mistura CSVs e CSVs em um instantâneo. 
 
Você pode usar o Gerenciador de instantâneo StorSimple para restaurar os grupos de todo o volume ou clonar volumes individuais e recuperar arquivos individuais.

- [Volumes e grupos de volume](#volumes-and-volume-groups) 
- [Tipos de backup e políticas de backup](#backup-types-and-backup-policies) 

Para obter mais informações sobre recursos do Gerenciador de instantâneo StorSimple e como usá-las, consulte [Gerenciador de instantâneo StorSimple interface de usuário](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Volumes e grupos de volume

Com o Gerenciador de instantâneo de StorSimple, você cria volumes e configurá-los em grupos de volume. 

Gerenciador de instantâneo StorSimple usa grupos de volume para criar cópias de backup que são consistentes com o aplicativo. Consistência do aplicativo existe quando todos os arquivos relacionados e bancos de dados são sincronizados e representam o verdadeiro estado de um aplicativo em um ponto específico no tempo. Grupos de volume (que são também conhecidos como *grupos de consistência*) formam a base de um backup ou restaurar o trabalho.

Os grupos de volume não são iguais a contêineres de volume. Um contêiner de volume contém um ou mais volumes que compartilham uma conta de armazenamento de nuvem e outros atributos, como o consumo de largura de banda e criptografia. Um contêiner de único volume pode conter até 256 volumes StorSimple provisionamento thin. Para saber mais sobre os contêineres de volume, vá para [gerenciar seus contêineres de volume](storsimple-manage-volume-containers.md). Grupos de volume são coleções de volumes que você configura para facilitar as operações de backup. Se você selecionar dois volumes que pertencem aos contêineres de volume diferente, colocá-los em um grupo único volume e, em seguida, criar uma política de backup para esse grupo de volume, cada volume será feito backup no contêiner de volume apropriado, usando a conta de armazenamento apropriado.

>[AZURE.NOTE] Todos os volumes em um grupo de volume devem ser de um provedor de serviços de nuvem único.

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integração com o serviço de cópia de sombra de Volume do Windows

Gerenciador de instantâneo StorSimple usa o Windows Volume Shadow cópia Service (VSS) para capturar dados consistentes com o aplicativo. VSS facilita a consistência do aplicativo, comunicar-se com aplicativos de reconhecimento de VSS para coordenar a criação de instantâneos incrementais. VSS garante que os aplicativos estejam temporariamente inativos ou inativo, quando os instantâneos são criados. 

A implementação de StorSimple Gerenciador de instantâneo do VSS funciona com SQL Server e genéricos volumes NTFS. O processo é da seguinte maneira: 

1. Um solicitante, que normalmente é um gerenciamento de dados e a solução de proteção (como o StorSimple instantâneo Manager) ou um aplicativo de backup, invoca VSS e pergunta para reunir informações do software gravador no aplicativo de destino.

2. VSS contata o componente writer para recuperar uma descrição dos dados. O gravador retorna a descrição dos dados de backup. 

3. VSS sinaliza o gravador para preparar o aplicativo para backup. O gravador prepara os dados para backup realizando transações abertas, atualizando logs de transação e assim por diante e o notifica VSS.

4. VSS instrui o gravador temporariamente parar armazenamentos de dados do aplicativo e certifique-se de que não há dados sejam gravados o volume enquanto a cópia de sombra é criada. Esta etapa garante a consistência de dados e leva não mais de 60 segundos.

5. VSS instrui o provedor para criar a cópia de sombra. Provedores, que podem ser o software ou hardware são baseados, gerenciam os volumes que estão em execução e criar cópias de sombra delas sob demanda. O provedor cria a cópia de sombra e VSS o notificará quando ela estiver concluída.

6. VSS contata o gravador para notificar o aplicativo que pode continuar a e/s e também para confirmar que a e/s foi pausado com êxito durante a criação de cópia de sombra. 

7. Se a cópia foi bem-sucedida, o VSS retornará local da cópia ao solicitante. 

8. Se os dados foram gravados enquanto a cópia de sombra foi criada, o backup será inconsistente. VSS exclui a cópia de sombra e notifica o solicitante. O solicitante pode repetir o processo de backup automaticamente ou notificar o administrador para repeti-la posteriormente.

Consulte a ilustração a seguir.

![Processo VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Processo de serviço de cópia de sombra de Volume do Windows** 

## <a name="backup-types-and-backup-policies"></a>Tipos de backup e políticas de backup

Com o Gerenciador de instantâneo de StorSimple, você pode fazer backup de dados e armazená-lo localmente e na nuvem. Você pode usar o Gerenciador de instantâneo StorSimple para fazer backup de dados imediatamente, ou você pode usar uma política de backup para criar um agendamento para fazer backups automaticamente. Políticas de backup também permitem especificar quantos instantâneos serão mantidos. 

### <a name="backup-types"></a>Tipos de backup

Você pode usar o Gerenciador de instantâneo StorSimple para criar os seguintes tipos de backups:

- **Instantâneos locais** – instantâneos locais são cópias de point-in-time dos dados de volume que estão armazenados no dispositivo StorSimple. Normalmente, esse tipo de backup pode ser criado restaurado e rapidamente. Você pode usar um instantâneo local como faria com uma cópia de backup local.

- **Instantâneos de nuvem** – nuvem instantâneos são cópias point-in-time dos dados de volume que são armazenados na nuvem. Um instantâneo de nuvem é equivalente a um instantâneo replicado em um sistema de armazenamento externo diferente. Nuvem instantâneos são particularmente úteis em cenários de recuperação de desastres.

### <a name="on-demand-and-scheduled-backups"></a>Backups sob demanda e agendados

Com o Gerenciador de instantâneo de StorSimple, você pode iniciar um backup único seja criado imediatamente, ou você pode usar uma política de backup para agendar operações de backup recorrentes.

Uma política de backup é um conjunto de regras automatizadas que você pode usar para agendar backups regulares. Uma política de backup permite que você defina a frequência e parâmetros de criação de instantâneos de um grupo de volume específico. Você pode usar políticas para especificar datas de início e vencimento, horas, frequências e os requisitos de retenção, para locais e na nuvem instantâneos. Uma política é aplicada imediatamente após você defini-lo. 

Você pode usar o Gerenciador de instantâneo StorSimple para configurar ou reconfigurar políticas de backup sempre que necessário. 

Configurar as seguintes informações para cada política de backup que você criar:

- **Nome** – o nome exclusivo da política de backup selecionada.

- **Tipo** – o tipo de política de backup; instantâneo local ou instantâneo de nuvem.

- **Grupo de volume** – o grupo de volume para o qual a política de backup selecionada é atribuída.

- **Retenção** – o número de cópias de backup para reter. Se você marcar a caixa **tudo** , todas as cópias de backup são mantidas até que o número máximo de cópias de backup por volume é atingido, ponto em que a política serão falhar e gerar uma mensagem de erro. Como alternativa, você pode especificar um número de backups para reter (entre 1 e 64).

- **Data** – a data quando a política de backup foi criada.

Para obter informações sobre como configurar políticas de backup, vá para [Usar o Gerenciador de instantâneo de StorSimple para criar e gerenciar políticas de backup](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Trabalho de backup monitoramento e gerenciamento

Você pode usar o Gerenciador de instantâneo StorSimple para monitorar e gerenciar trabalhos de backup futuros, agendados e concluídos. Além disso, o Gerenciador de instantâneo StorSimple fornece um catálogo de até 64 backups concluídos. Você pode usar o catálogo para encontrar e restaurar volumes ou arquivos individuais. 

Para obter informações sobre monitoramento trabalhos de backup, vá para [Usar o Gerenciador de instantâneo de StorSimple para exibir e gerenciar trabalhos de backup](storsimple-snapshot-manager-manage-backup-jobs.md).


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [usar o Gerenciador de instantâneo StorSimple administrar sua solução de StorSimple](storsimple-snapshot-manager-admin.md).

- [Instantâneo de StorSimple Gerenciador](https://www.microsoft.com/download/details.aspx?id=44220)de download.
