<properties 
   pageTitle="Adaptador de StorSimple do SharePoint | Microsoft Azure"
   description="Descreve como instalar e configurar ou remover o adaptador StorSimple do SharePoint em um farm do SharePoint server."
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
   ms.date="07/11/2016"
   ms.author="v-sharos" />

# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Instalar e configurar o adaptador StorSimple do SharePoint

## <a name="overview"></a>Visão geral

O adaptador StorSimple do SharePoint é um componente que permite que você forneça armazenamento flexível do Microsoft Azure StorSimple e proteção de dados em farms do SharePoint server. Você pode usar o adaptador mover conteúdo binário grande BLOB (objeto) de bancos de dados de conteúdo do SQL Server para o dispositivo de armazenamento de nuvem do Microsoft Azure StorSimple híbrido.

O adaptador StorSimple para SharePoint funciona como um provedor de armazenamento de BLOB remoto (EDR) e usa o recurso de armazenamento de BLOB remoto do SQL Server para armazenar conteúdo do SharePoint não estruturado (em forma de BLOBs) em um servidor de arquivo que é feito por um dispositivo StorSimple.

>[AZURE.NOTE] O adaptador StorSimple do SharePoint é compatível com armazenamento de BLOB do SharePoint Server 2010 remoto (EDR). Ele não suporta o armazenamento de BLOB do SharePoint Server 2010 externo (EBS).

- Para baixar o adaptador StorSimple do SharePoint, vá para [StorSimple adaptador para SharePoint] [ 1] no Microsoft Download Center.

- Para obter informações sobre o planejamento de EDR e EDR limitações, acesse [Decidindo usar EDR no SharePoint 2013] [ 2] ou [Planejar EDR (SharePoint Server 2010)][3].

O restante desta visão geral descreve brevemente a função do adaptador StorSimple para SharePoint e os limites de capacidade e desempenho do SharePoint que você deve estar ciente antes de instalar e configurar o adaptador. Depois de examinar essas informações, vá para [StorSimple adaptador para instalação do SharePoint](#storsimple-adapter-for-sharepoint-installation) para começar a configurar o adaptador.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>Adaptador StorSimple para os benefícios do SharePoint

Em um site do SharePoint, o conteúdo é armazenado como dados BLOB não estruturados em um ou mais bancos de dados de conteúdo. Por padrão, esses bancos de dados são hospedados em computadores que estiver executando o SQL Server e estão localizados no SharePoint server farm. BLOBs rapidamente podem aumentar o tamanho, consumindo grandes quantidades de armazenamento local. Por esse motivo, talvez você queira encontrar armazenamento barato outra solução. SQL Server fornece uma tecnologia chamada remota Blob Storage (EDR) que permite armazenar conteúdo BLOB no sistema de arquivos, fora do banco de dados do SQL Server. Com EDR, BLOBs podem residem no sistema de arquivos no computador que executa o SQL Server ou eles podem ser armazenados no sistema de arquivos em outro computador servidor.

EDR requer que você use um provedor de EDR, como o adaptador StorSimple para SharePoint, para habilitar EDR no SharePoint. O adaptador StorSimple do SharePoint funciona com EDR, permitindo que você mover BLOBs para um servidor backup pelo sistema Microsoft Azure StorSimple. Microsoft Azure StorSimple, em seguida, armazena os dados BLOB localmente ou na nuvem, com base em uso. Bolhas que são muito ativas (normalmente denomina-nível 1 ou dados quente) residem localmente. Dados menos ativos e arquivamento residem na nuvem. Depois de habilitar EDR um banco de dados de conteúdo, qualquer conteúdo BLOB novo criado no SharePoint é armazenado no dispositivo StorSimple e não no banco de dados conteúdo.

A implementação do Microsoft Azure StorSimple de EDR fornece os seguintes benefícios:

- Ao mover o conteúdo BLOB para um servidor separado, você pode reduzir a carga de consulta no SQL Server, que pode melhorar a capacidade de resposta do SQL Server. 

- StorSimple Azure usa duplicação e compactação para reduzir o tamanho dos dados.

- StorSimple Azure fornece proteção de dados na forma de local e instantâneos de nuvem. Além disso, se você colocar o próprio banco de dados no dispositivo StorSimple, você pode fazer backup do banco de dados de conteúdo e BLOBs juntos de maneira consistente falha. (Movendo o banco de dados de conteúdo para o dispositivo tem suporte apenas para o dispositivo de série 8000 StorSimple. Este recurso não tem suporte para a série 5000 ou 7000.)

- StorSimple Azure inclui recursos de recuperação de desastres, inclusive failover, recuperação de arquivo e volume (incluindo a recuperação de teste) e restauração rápida de dados.

- Você pode usar o software de recuperação de dados, como Kroll Ontrack PowerControls, com StorSimple instantâneos dos dados BLOB para realizar a recuperação de nível de item de conteúdo do SharePoint. (Esse software de recuperação de dados é uma compra separadas.)

- O adaptador StorSimple para SharePoint conecta-se para o portal de Administração Central do SharePoint, permitindo que você gerencie sua solução SharePoint inteira de um local central.

Movendo BLOB conteúdo para o sistema de arquivos pode oferecer outros economia e benefícios. Por exemplo, usar EDR pode reduzir a necessidade de armazenamento de nível 1 e, porque ele reduz o banco de dados de conteúdo, EDR pode reduzir o número de bancos de dados necessários no farm de servidores do SharePoint. No entanto, outros fatores, como limites de tamanho do banco de dados e a quantidade de conteúdo não EDR, também pode afetar os requisitos de armazenamento. Para obter mais informações sobre os custos e benefícios do uso de EDR, consulte [Planejar EDR (SharePoint Foundation 2010)] [ 4] e [Decidindo usar EDR no SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Limites de capacidade e desempenho

Antes de considerar usando EDR em sua solução do SharePoint, você deve estar ciente de desempenho testado e limites de capacidade de SharePoint Server 2010 e do SharePoint Server 2013 e como esses limites se relacionam com desempenho aceitável. Para obter mais informações, consulte [limites de Software para o SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Revise o seguinte antes de configurar EDR:

- Certifique-se de que o tamanho total do conteúdo (o tamanho de um banco de dados de conteúdo) mais o tamanho do qualquer BLOBs externalizados associados não exceda o limite de tamanho de EDR suportado pelo SharePoint. Esse limite é 200 GB. 

    **Banco de dados de conteúdo do medida e tamanho BLOB**

     1. Execute essa consulta na WFE de Administração Central. Iniciar o Shell de gerenciamento do SharePoint e insira o seguinte comando do Windows PowerShell para obter o tamanho de bancos de dados do conteúdo:

        `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`

         Esta etapa obtém o tamanho do banco de dados conteúdo no disco.

     2. Execute uma das seguintes consultas SQL no SQL Management Studio na caixa SQL server cada banco de dados de conteúdo e adicionar o resultado para o número obtido na etapa 1.

        No SharePoint 2013 bancos de dados, digite:

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`

        Em bancos de dados conteúdos do SharePoint 2010, insira:

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`

        Esta etapa obtém o tamanho das bolhas que tenha sido exteriorizado.

- Recomendamos que você armazene todo o conteúdo de banco de dados e BLOB localmente no dispositivo StorSimple. O dispositivo StorSimple é um cluster de dois nós para alta disponibilidade. Colocar os bancos de dados de conteúdo e BLOBs no dispositivo StorSimple fornece alta disponibilidade.

    Use tradicionais práticas recomendadas de migração de SQL Server para mover o banco de dados de conteúdo para o dispositivo StorSimple. Mova o banco de dados somente depois que todo o conteúdo BLOB do banco de dados foi movido para o compartilhamento de arquivos via EDR. Se você optar por mover o banco de dados de conteúdo para o dispositivo de StorSimple, recomendamos que você configura o armazenamento de banco de dados de conteúdo no dispositivo como um volume principal.

- No Microsoft Azure StorSimple, se usando volumes hierárquicos, não há nenhuma maneira para garantir que o conteúdo armazenado localmente no StorSimple dispositivo não será ser hierárquico para armazenamento de nuvem do Microsoft Azure. Portanto, é recomendável usar volumes de StorSimple localmente fixada em conjunto com o SharePoint RBS. Isso garantirá que todo o conteúdo BLOB permanece localmente no dispositivo StorSimple e não é movido para Microsoft Azure.

- Se você não armazenar os bancos de dados de conteúdo no dispositivo StorSimple, use tradicional do SQL Server alta disponibilidade práticas recomendadas que oferecem suporte a EDR. Clusters do SQL Server oferece suporte a EDR, enquanto o SQL Server espelhamento não. 

>[AZURE.WARNING] Se você não tiver ativado EDR, não recomendamos movendo o banco de dados de conteúdo para o dispositivo StorSimple. Esta é uma configuração testada.
 
## <a name="storsimple-adapter-for-sharepoint-installation"></a>Adaptador StorSimple para a instalação do SharePoint

Antes de instalar o adaptador StorSimple do SharePoint, você deve configurar o dispositivo StorSimple e certifique-se de que o farm do SharePoint server e instanciação do SQL Server atendam a todos os pré-requisitos. Este tutorial descreve requisitos de configuração, além de procedimentos para instalar e atualizar o adaptador StorSimple do SharePoint. 

## <a name="configure-prerequisites"></a>Configurar pré-requisitos

Antes de instalar o adaptador StorSimple do SharePoint, certifique-se de que o dispositivo de StorSimple, o farm do SharePoint server e instanciação do SQL Server atender aos seguintes pré-requisitos.

### <a name="system-requirements"></a>Requisitos do sistema

O adaptador StorSimple do SharePoint funciona com o hardware e software a seguir:

- Sistema operacional – Windows Server 2008 R2 SP1, Windows Server 2012 ou Windows Server 2012 R2 

- Versões do SharePoint com suporte – SharePoint Server 2010 ou o SharePoint Server 2013

- Compatíveis com versões do SQL Server – Enterprise Edition do SQL Server 2008, SQL Server 2008 R2 Enterprise Edition ou SQL Server 2012 Enterprise Edition

- Suporte para dispositivos de StorSimple – série 8000 StorSimple, série StorSimple 7000 ou série 5000 StorSimple.

### <a name="storsimple-device-configuration-prerequisites"></a>Pré-requisitos de configuração de dispositivo StorSimple

O dispositivo StorSimple é um dispositivo de bloco e como tal requer um servidor de arquivo no qual os dados podem ser hospedados. Recomendamos que você use um servidor separado em vez de um servidor existente do farm do SharePoint. Este servidor de arquivos deve estar na mesma rede local (LAN) como o computador do SQL Server que hospedará os bancos de dados de conteúdo. 

>[AZURE.TIP] 
>
>- Se você configurar seu farm do SharePoint para alta disponibilidade, você deve implantar o servidor de arquivos para alta disponibilidade também.
>
>- Se você não armazene o banco de dados de conteúdo no dispositivo StorSimple, use tradicionais alta disponibilidade as práticas recomendadas que oferecem suporte a EDR. Clusters do SQL Server oferece suporte a EDR, enquanto o SQL Server espelhamento não. 

Certifique-se de que seu dispositivo StorSimple está configurado corretamente e que volumes apropriados para dar suporte a sua implantação do SharePoint são configurados e acessíveis a partir do computador do SQL Server. Vá para [implantar seu dispositivo de StorSimple local](storsimple-deployment-walkthrough.md) se você ainda não tiver implantado e configurado seu dispositivo StorSimple. Anote o endereço IP do dispositivo StorSimple; Você precisará dele durante StorSimple adaptador para instalação do SharePoint. 

Além disso, certifique-se de que o volume a ser usado para externalization BLOB atende aos seguintes requisitos:

- O volume deve ser formatado com um tamanho de unidade de alocação de 64 KB.

- Seu web front-end (WFE) e os servidores de aplicativo deverá conseguir acessar o volume por meio de um caminho de convenção Universal de nomenclatura (UNC). 

- O farm do SharePoint server deverá ser configurado para gravar no volume.

>[AZURE.NOTE] Depois de instalar e configurar o adaptador, todos os externalization de BLOB deve dar uma olhada no dispositivo StorSimple (o dispositivo será apresentar os volumes para SQL Server e gerenciar os níveis de armazenamento). Você não poderá usar todos os outros destinos externalization BLOB.
 
Se você planeja usar o Gerenciador de instantâneo StorSimple para tirar instantâneos dos dados BLOB e banco de dados, certifique-se de instalar o Gerenciador de instantâneo StorSimple no servidor de banco de dados para que ele possa usar o serviço de gravador SQL para implementar o Windows Volume Shadow cópia Service (VSS). 

>[AZURE.IMPORTANT] Gerenciador de instantâneo StorSimple não suporta o gravador de VSS do SharePoint e não é possível obter instantâneos consistentes com o aplicativo de dados do SharePoint. Em um cenário de SharePoint, Gerenciador de instantâneo StorSimple fornece apenas backups consistente após falhas. 
 
## <a name="sharepoint-farm-configuration-prerequisites"></a>Pré-requisitos de configuração de farm do SharePoint

Certifique-se de que seu farm do SharePoint server está configurado corretamente, da seguinte maneira:

- Verifique se seu farm do SharePoint server está em um estado de integridade e verifique o seguinte: 

- Todos os SharePoint WFE e registrados no farm de servidores de aplicativo estiver executando e podem ser feito o ping do servidor no qual você irá instalar o adaptador StorSimple do SharePoint.

- O serviço de Timer do SharePoint (SPTimerV3 ou SPTimerV4) está em execução em cada servidor WFE e o servidor de aplicativos.

- O serviço de Timer do SharePoint e o pool de aplicativos do IIS em que o site de Administração Central do SharePoint está executando tem privilégios administrativos. 

- Certifique-se de que o contexto de segurança de Enhanced Explorer Internet (IE ESC) está desativado. Siga estas etapas para desabilitar o IE ESC:

    1. Feche todas as instâncias do Internet Explorer.

    2. Inicie o Gerenciador de servidor.

    3. No painel esquerdo, clique em **Servidor Local**.

    4. No painel direito, ao lado de **Configuração de segurança aprimorada do Internet Explorer**, clique **em**.

    5. Em **administradores**, clique em **Desativar**.

    6. Clique em **Okey**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Pré-requisitos de armazenamento de BLOB (EDR) remoto

Certifique-se de que você está usando uma versão compatível do SQL Server. Somente as seguintes versões são capazes de usar EDR e suportados:

- SQL Server 2008 Enterprise Edition

- SQL Server 2008 R2 Enterprise Edition

- SQL Server 2012 Enterprise Edition

BLOBs podem ser exteriorizados apenas nesses volumes que apresenta o dispositivo de StorSimple para o SQL Server. Não há outros destinos para externalization BLOB são suportados.

Quando você tiver concluído todas as etapas de configuração de pré-requisito, vá para [instalar o adaptador StorSimple do SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Instale o adaptador StorSimple do SharePoint

Use as seguintes etapas para instalar o adaptador StorSimple do SharePoint. Se você estiver reinstalando o software, consulte [atualizar ou reinstalar o adaptador StorSimple para SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). O tempo necessário para a instalação depende o número total de bancos de dados do SharePoint em seu farm do SharePoint server.

[AZURE.INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]


## <a name="configure-rbs"></a>Configurar EDR

Depois de instalar o adaptador StorSimple do SharePoint, configure EDR conforme descrito no procedimento a seguir.

>[AZURE.TIP] O adaptador StorSimple para SharePoint conectado na página Administração Central do SharePoint, permitindo EDR ser habilitado ou desabilitado em cada banco de dados de conteúdo no farm do SharePoint. No entanto, ativando ou desativando EDR o banco de dados de conteúdo causa uma redefinição do IIS, que, dependendo da configuração farm, pode interromper temporariamente a disponibilidade do SharePoint web front-end (WFE). (Fatores como o uso de um balanceador de carga de front-end, a carga de trabalho atual do servidor e assim por diante, podem limitar ou eliminar essa interrupção). Para proteger os usuários de uma interrupção, recomendamos que você habilitar ou desabilitar EDR somente durante uma janela de manutenção planejada.

[AZURE.INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]
 

## <a name="configure-garbage-collection"></a>Configurar coleta de lixo

Quando objetos são excluídos de um site do SharePoint, elas não são excluídas automaticamente do volume de armazenamento de EDR. Em vez disso, um assíncrono, programa de manutenção de plano de fundo exclui BLOBs órfãos o armazenamento de arquivos. Os administradores de sistema podem agendar esse processo para executar periodicamente ou eles podem iniciá-lo sempre que necessário.

Este programa de manutenção (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) é instalado automaticamente em todos os servidores do SharePoint WFE e servidores de aplicativo quando você habilita EDR. O programa está instalado no seguinte local: *unidade de inicialização*: \Program Files\Microsoft armazenamento de Blob remoto do SQL 10.50\Maintainer\

Para obter informações sobre como configurar e usar o programa de manutenção, consulte [Manter EDR no SharePoint Server 2013][8].

>[AZURE.IMPORTANT] O programa de mantenedor EDR é uso de recursos. Você deve agendá-lo seja executado somente durante períodos de atividade de luz no farm do SharePoint.

### <a name="delete-orphaned-blobs-immediately"></a>Excluir BLOBs órfãos imediatamente

Se você precisar excluir BLOBs órfãos imediatamente, você pode usar as instruções a seguir. Observe que essas instruções são um exemplo de como isso pode ser feito em um ambiente do SharePoint 2013 com os seguintes componentes:

- O nome do banco de dados de conteúdo é WSS-Content.
- O nome do SQL Server é SHRPT13-SQL12\SHRPT13.
- O nome do aplicativo web é SharePoint – 80.

[AZURE.INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]


## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Atualizar ou reinstalar o adaptador StorSimple do SharePoint

Use o procedimento a seguir para atualizar o SharePoint server e reinstale o adaptador StorSimple do SharePoint ou para simplesmente atualizar ou reinstalar o adaptador em um farm de servidores do SharePoint existente. 

>[AZURE.IMPORTANT] Revise as informações a seguir antes de tentar atualizar seu software do SharePoint e/ou a atualização ou reinstalar o adaptador StorSimple para SharePoint:
>
>- Todos os arquivos que anteriormente foram movidos para armazenamento externo via EDR não estará disponíveis até a reinstalação ser concluída e o recurso de EDR está habilitado novamente. Para limitar o impacto do usuário, realize qualquer atualização ou reinstalação durante uma janela de manutenção planejada.
>
>- O tempo necessário para a atualização/reinstalação pode variar, dependendo do número total de bancos de dados do SharePoint no farm de servidores do SharePoint.
>
>- Após a atualização/reinstalação estiver concluída, você precisa habilitar EDR para os bancos de dados de conteúdo. Consulte [Configurar EDR](#configure-rbs) para obter mais informações.
>
>- Se você estiver configurando EDR para um farm do SharePoint que tem um número muito grande de bancos de dados (maiores que 200), a página de **Administração Central do SharePoint** poderão expirar. Se isso ocorrer, atualize a página. Isso não afeta o processo de configuração.

[AZURE.INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]
 
## <a name="storsimple-adapter-for-sharepoint-removal"></a>Adaptador StorSimple para remoção do SharePoint

Os procedimentos a seguir descrevem como mover os BLOBs de volta para os bancos de dados de conteúdo do SQL Server e depois desinstalar o adaptador StorSimple do SharePoint. 

>[AZURE.IMPORTANT] Você tem que mover os BLOBs de volta para os bancos de dados de conteúdo antes de desinstalar o software do adaptador. 

### <a name="before-you-begin"></a>Antes de começar 

Colete as informações a seguir antes de mover os dados de volta para os bancos de dados de conteúdo do SQL Server e iniciar o processo de remoção do adaptador:

- Os nomes de todos os bancos de dados para o qual EDR está habilitado
- O caminho UNC do armazenamento de BLOB configurado

### <a name="move-the-blobs-back-to-the-content-databases"></a>Mover os BLOBs de volta para os bancos de dados de conteúdo

Antes de desinstalar o adaptador StorSimple para software do SharePoint, você deve migrar todas as bolhas que foram exteriorizadas volta para os bancos de dados de conteúdo do SQL Server. Se você tentar desinstalar o adaptador StorSimple do SharePoint antes de você mover todos os BLOBs de volta para os bancos de dados de conteúdo, você verá a seguinte mensagem de aviso.

![Mensagem de aviso](./media/storsimple-adapter-for-sharepoint/sasp1.png)
 
####<a name="to-move-the-blobs-back-to-the-content-databases"></a>Para mover os BLOBs de volta para os bancos de dados de conteúdo 

1. Fazer o download de cada um dos objetos externalizados.

2. Abra a página de **Administração Central do SharePoint** e navegue até **Configurações do sistema**. 

3. Em **StorSimple do Azure**, clique em **Configurar adaptador de StorSimple**.

4. Na página **Configurar adaptador de StorSimple** , clique no botão **Desativar** abaixo de cada um dos bancos de dados de conteúdo que você deseja remover do armazenamento BLOB externo. 

5. Excluir os objetos do SharePoint e carregue-las novamente.

Como alternativa, você pode usar o Microsoft` RBS Migrate()` cmdlet do PowerShell incluído com o SharePoint. Para obter mais informações, consulte [Migrar conteúdo em ou reduzir EDR](https://technet.microsoft.com/library/ff628255.aspx).

Depois que você move os BLOBs de volta para o banco de dados de conteúdo, vá para a próxima etapa: [desinstalar o adaptador](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Desinstalar o adaptador

Depois que você move os BLOBs de volta para os bancos de dados de conteúdo do SQL Server, use uma das opções a seguir para desinstalar o adaptador StorSimple do SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Usar o programa de instalação para desinstalar o adaptador 

1. Use uma conta com privilégios de administrador para fazer logon no servidor web front-end (WFE).
2. Clique duas vezes o adaptador StorSimple para o instalador do SharePoint. Inicia o Assistente de configuração.

    ![Assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp2.png)

3. Clique em **Avançar**. A seguinte página é exibida.

    ![Remover página do Assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp3.png)

4. Clique em **Remover** para selecionar o processo de remoção. A seguinte página é exibida.

    ![Página de confirmação do Assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp4.png)

5. Clique em **Remover** para confirmar a remoção. A seguinte página de andamento é exibida.

    ![Página de progresso do Assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp5.png)

6. Quando a remoção for concluída, a página de término aparece. Clique em **Concluir** para fechar o Assistente de configuração.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Usar o painel de controle para desinstalar o adaptador 

1. Abra o painel de controle e clique em **programas e recursos**.

2. Selecione **Adaptador de StorSimple do SharePoint**e, em seguida, clique em **desinstalar**. 

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/en-us/library/ff943565.aspx
