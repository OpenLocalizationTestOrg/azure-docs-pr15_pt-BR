<properties
    pageTitle="Migrar um banco de dados do SQL Server para o SQL Server em uma VM | Microsoft Azure"
    description="Saiba mais sobre como migrar um banco de dados de usuário local para o SQL Server em uma máquina virtual do Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="sabotta"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="carlasab"/>


# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrar um banco de dados do SQL Server para o SQL Server em uma VM do Windows Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]Modelo do Gerenciador de recursos.


Há vários métodos para migrar um banco de dados de usuário do local do SQL Server para o SQL Server em uma VM do Windows Azure. Este artigo será brevemente discutem os diversos métodos, recomendável o melhor método para diversos cenários e incluir um [tutorial](#azure-vm-deployment-wizard-tutorial) para guiá-lo com o uso o Assistente de **implantação de um banco de dados SQL Server a uma VM do Microsoft Azure** . 

O método usando o Assistente de **implantação de um banco de dados SQL Server a uma VM do Microsoft Azure** descrito no [tutorial](#azure-vm-deployment-wizard-tutorial) destina-se apenas o modelo de implantação clássico. 

## <a name="what-are-the-primary-migration-methods"></a>Quais são os métodos de migração principal?

Os métodos de migração principais são:

- Use a implantação de um banco de dados SQL Server para um assistente do Microsoft Azure VM
- Executar o uso da compactação de backup de local e copie manualmente o arquivo de backup para a máquina virtual do Azure
- Executar um backup para a URL e restauração na máquina virtual do Azure da URL
- Desanexar e, em seguida, copie os arquivos de dados e log para armazenamento de blob Azure e conectar ao SQL Server no Windows Azure VM da URL
- Converter máquina física do local VHD do Hyper-V, carregar para armazenamento de Blob do Windows Azure e então implantar como novo VM usando carregados VHD
- No disco rígido Ship usando serviço de importação/exportação do Windows
- Se você tiver uma implantação do AlwaysOn no local, use o [Assistente para adicionar réplica de Windows Azure](virtual-machines-windows-classic-sql-onprem-availability.md) para criar uma réplica no Azure e, em seguida, o failover, apontando para os usuários para a instância do banco de dados do Azure
- Use o SQL Server [replicação transacional](https://msdn.microsoft.com/library/ms151176.aspx) para configurar a instância do SQL Server do Azure como um assinante e, em seguida, desabilite a replicação, apontando para os usuários para a instância do banco de dados do Azure



## <a name="choosing-your-migration-method"></a>Escolhendo o método de migração

Para o desempenho de transferência de dados ideal, migração dos arquivos de banco de dados para a VM do Windows Azure usando um arquivo de backup compactado geralmente é o melhor método. Esse é o método que usa a [implantar um banco de dados SQL Server para um assistente do Microsoft Azure VM](#azure-vm-deployment-wizard-tutorial) . Este assistente é o método recomendado para a migração de uma execução de banco de dados de usuário local no SQL Server 2005 ou posterior para SQL Server 2014 ou maior quando o arquivo de backup do banco de dados compactado for menor que 1 TB.

Para minimizar o tempo ocioso durante o processo de migração de banco de dados, use a opção AlwaysOn ou a opção de replicação transacional.

Se não for possível usar os métodos acima, migre seu banco de dados manualmente. Usar esse método, você geralmente vai começar com um backup de banco de dados, seguido por uma cópia do banco de dados de backup no Windows Azure e, em seguida, executar uma restauração de banco de dados. Você pode também copiar os próprios arquivos de banco de dados em Windows Azure e, em seguida, anexá-los. Há vários métodos pelos quais você pode realizar esse processo manual da migração de um banco de dados em uma VM do Windows Azure.

> [AZURE.NOTE] Quando você atualiza para 2014 do SQL Server ou SQL Server 2016 de versões mais antigas do SQL Server, você deve considerar se forem necessárias alterações. Recomendamos que você resolva todas as dependências de recursos não compatíveis com a nova versão do SQL Server como parte do seu projeto de migração. Para obter mais informações sobre os cenários e as edições com suporte, consulte [atualizar para o SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

A tabela a seguir lista cada um dos métodos de migração primário e discute quando o uso de cada método é mais apropriado.

| Método  | Versão do banco de dados de origem  |  Versão de banco de dados de destino | Restrição de tamanho do backup do banco de dados de origem  | Notas  |
|---|---|---|---|---|
| [Use a implantação de um banco de dados SQL Server para um assistente do Microsoft Azure VM](#azure-vm-deployment-wizard-tutorial) | SQL Server 2005 ou posterior | SQL Server 2014 ou maior | < 1TB  | Método rápido e simples, use sempre que possível migrar para uma instância do SQL Server nova ou existente em uma máquina virtual do Azure | 
| [Use o Assistente de réplica Azure para adicionar](virtual-machines-windows-classic-sql-onprem-availability.md) | SQL Server 2012 ou posterior | SQL Server 2012 ou posterior | [Limite de armazenamento do Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Minimiza o tempo de inatividade, use quando tiver uma implantação local do AlwaysOn |
| [Usar a replicação transacional do SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) | SQL Server 2005 ou posterior | SQL Server 2005 ou posterior | [Limite de armazenamento do Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Usar quando você precisa para minimizar o tempo de inatividade e não têm uma implantação local do AlwaysOn |
| [Executar o uso da compactação de backup de local e copie manualmente o arquivo de backup para a máquina virtual do Azure](#backup-to-file-and-copy-to-vm-and-restore) | SQL Server 2005 ou posterior | SQL Server 2005 ou posterior | [Limite de armazenamento do Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Use somente quando você não pode usar o assistente, como quando a versão do banco de dados de destino é menor que o SQL Server 2012 SP1 CU2 ou o tamanho do backup de banco de dados for maior do que 1 TB (12,8 TB com 2016 do SQL Server) |
| [Executar um backup para a URL e restauração na máquina virtual do Azure da URL](#backup-to-url-and-restore) | CU2 do SQL Server 2012 SP1 ou posterior | CU2 do SQL Server 2012 SP1 ou posterior | < 12,8 TB para SQL Server 2016, caso contrário < 1TB | Geralmente usando o [backup do URL](https://msdn.microsoft.com/library/dn435916.aspx) é o equivalente no desempenho usando o assistente e não tão fácil |
| [Desanexar e copie os arquivos de dados e log para o armazenamento de blob Azure e conectar ao SQL Server na máquina virtual do Azure da URL](#detach-and-copy-to-url-and-attach-from-url) | SQL Server 2005 ou posterior | SQL Server 2014 ou maior | [Limite de armazenamento do Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Use este método quando você planeja [armazenar esses arquivos usando o serviço de armazenamento de Blob do Windows Azure](https://msdn.microsoft.com/library/dn385720.aspx) e anexá-los ao SQL Server em execução em uma VM do Azure, particularmente com bancos de dados muito grandes |
| [Converter a máquina local em Hyper-V VHDs, carregar para armazenamento de Blob do Windows Azure e então implantar uma nova máquina virtual usando VHD carregado](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) | SQL Server 2005 ou posterior | SQL Server 2005 ou posterior | [Limite de armazenamento do Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Use quando [trazendo sua própria licença do SQL Server](../sql-database/sql-database-paas-vs-sql-server-iaas.md), ao fazer a migração de um banco de dados que serão executadas em uma versão mais antiga do SQL Server, ou durante a migração de sistema e usuário databases juntos como parte da migração de banco de dados dependente de outros bancos de dados de usuário e/ou bancos de dados do sistema. |
| [Unidade de disco rígido Ship usando o serviço de importação/exportação do Windows](#ship-hard-drive) | SQL Server 2005 ou posterior | SQL Server 2005 ou posterior | [Limite de armazenamento do Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Usar o [Serviço de importação/exportação do Windows](../storage/storage-import-export-service.md) quando o método copy manual está muito lento, tais como com bancos de dados muito grandes |

## <a name="azure-vm-deployment-wizard-tutorial"></a>Tutorial de Assistente de implantação do Azure VM

Usar o Assistente de **implantação de um banco de dados SQL Server a uma VM do Microsoft Azure** no Microsoft SQL Server Management Studio para migrar um SQL Server 2005, SQL Server 2008, SQL Server 2008 R2, SQL Server 2012, 2014 do SQL Server ou SQL Server 2016 usuário do local do banco de dados (até 1 TB) 2014 do SQL Server ou SQL Server 2016 em uma máquina virtual do Azure. Use este assistente para migrar um banco de dados do usuário para uma máquina virtual do Azure existente ou para uma VM Azure com o SQL Server criado pelo assistente durante o processo de migração. Quando você migra um banco de dados para uma versão mais recente do SQL Server, o banco de dados é atualizado automaticamente durante o processo.

O método destina-se apenas o modelo de implantação clássico. 

### <a name="get-latest-version-of-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Obter versão mais recente do que a implantação de um banco de dados SQL Server para um assistente do Microsoft Azure VM

Use a versão mais recente do Microsoft SQL Server Management Studio para o SQL Server para garantir que você tenha a versão mais recente do Assistente de **implantação de um banco de dados SQL Server a uma VM do Microsoft Azure** . A versão mais recente deste assistente incorpora as atualizações mais recentes para o portal clássico Azure e suporta as imagens de máquina virtual do Azure mais recentes na galeria (versões mais antigas do que o assistente não podem não funcionar). Para obter a versão mais recente do Microsoft SQL Server Management Studio para o SQL Server, [baixá-lo](http://go.microsoft.com/fwlink/?LinkId=616025) e instalá-lo em um computador cliente com conectividade para o banco de dados que você planejar a migração e à internet.

### <a name="configure-the-existing-azure-virtual-machine-and-sql-server-instance-if-applicable"></a>Configurar o existente máquina virtual do Azure e a instância do SQL Server (se aplicável)

Se você estiver migrando para uma VM existente do Azure, as etapas de configuração a seguir são necessárias:

- Configure a VM do Windows Azure e a instância do SQL Server para habilitar a conectividade de outro computador seguindo as etapas para [se conectar à instância do SQL Server VM no SSMS em outro computador](virtual-machines-windows-sql-connect.md). Somente as imagens de 2014 do SQL Server e SQL Server 2016 na galeria são suportadas se você estiver migrando usando o assistente.
- Configure um ponto de extremidade aberto para o serviço de adaptador de nuvem do SQL Server no gateway Microsoft Azure com porta privada de 11435. Essa porta é criada como parte do provisionamento de 2014 do SQL Server ou SQL Server 2016 em uma VM do Microsoft Azure. Adaptador de nuvem também cria uma regra de Firewall do Windows para permitir que suas conexões TCP recebidas na porta padrão 11435. Neste ponto de extremidade permite que o assistente utilizar o serviço de nuvem adaptador para copiar os arquivos de backup da instância local para a VM do Windows Azure. Para obter mais informações, consulte [Adaptador de nuvem para o SQL Server](https://msdn.microsoft.com/library/dn169301.aspx).

    ![Criar um ponto de extremidade de adaptador de nuvem](./media/virtual-machines-windows-migrate-sql/cloud-adapter-endpoint.png)

### <a name="run-the-use-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Execute o uso de implantar um banco de dados do SQL Server para um assistente do Microsoft Azure VM

1. Abrir Microsoft SQL Server Management Studio para Microsoft SQL Server 2016 e conecte-se à instância do SQL Server que contém o banco de dados de usuário que você migrará para uma VM do Windows Azure.
2. O banco de dados que você está migrando do mouse em, aponte para tarefas e clique em Deploy a uma VM do Microsoft Azure.

    ![Iniciar o Assistente](./media/virtual-machines-windows-migrate-sql/start-wizard.png)

3. Na página Introdução, clique em Avançar.
4. Na página Configurações da fonte, conecte-se à instância do SQL Server que contém o banco de dados que você pretende migrar para uma VM do Windows Azure.
5. Especifique um local temporário para os arquivos de backup. Se conectando a um servidor remoto, você deve especificar uma unidade de rede.

    ![Configurações da fonte](./media/virtual-machines-windows-migrate-sql/source-settings.png)

6. Clique em Avançar.
7. Na página Microsoft Azure entrar, clique em entrar e entrar em sua conta do Azure.
8. Selecione a assinatura que você deseja usar e clique em Avançar.

    ![Entrar Azure](./media/virtual-machines-windows-migrate-sql/azure-signin.png)

9. Na página Definições de implantação, você pode especificar um novo ou um serviço de nuvem existente nome e o nome da máquina Virtual:

 - Especifique um novo nome de nome de serviço de nuvem e máquina Virtual para criar um novo serviço de nuvem com uma nova máquina virtual do Azure usando uma imagem de 2014 do SQL Server ou SQL Server 2016 Galeria.

     - Se você especificar um novo nome de serviço de nuvem, especifique a conta de armazenamento que você usará.

     - Se você especificar um nome de serviço em nuvem existente, a conta de armazenamento será recuperada e inserida para você.

 - Especifique um nome de serviço em nuvem existente e o novo nome de máquina Virtual para criar uma nova máquina virtual do Azure em um serviço de nuvem existente. Especifica apenas uma imagem de galeria de 2014 do SQL Server ou SQL Server 2016.
 - Especifique um nome de serviço em nuvem existente e o nome da máquina Virtual para usam uma máquina virtual de Azure existente. Isso deve ser uma imagem criada usando uma imagem da Galeria 2014 do SQL Server ou SQL Server 2016.

        ![Deployment Settings](./media/virtual-machines-windows-migrate-sql/deployment-settings.png)

10. Clique em configurações
  - Se você especificou um nome de serviço em nuvem existente e o nome da máquina Virtual, você será solicitado a fornecer o nome de usuário e senha.

        ![Configurações de máquina do Azure](./media/virtual-machines-windows-migrate-sql/azure-machine-settings.png)

    - Se você tiver especificado um novo nome de máquina Virtual, você será solicitado para selecionar uma imagem da lista de imagens de galeria e forneça as seguintes informações:
      - Imagem – selecione apenas 2014 do SQL Server ou SQL Server 2016
        - Nome de usuário
        - Nova senha
        - Confirmar senha
        - Local
        - Tamanho.
    - Além disso, clique para aceitar o certificado gerado automaticamente para essa nova Microsoft Azure Máquina Virtual e clique em Okey.

    ![Novas configurações de máquina Azure](./media/virtual-machines-windows-migrate-sql/azure-new-machine-settings.png)

11. Especifique o nome do banco de dados de destino, se for diferente do nome do banco de dados de origem. Se o banco de dados de destino já existir, o sistema será automaticamente incrementar o nome de banco de dados em vez de substituir o banco de dados existente.
12. Clique em Avançar e, em seguida, clique em Concluir.

    ![Resultados](./media/virtual-machines-windows-migrate-sql/results.png)

13. Quando o assistente for concluído, conecte-se em sua máquina virtual e verificar se o seu banco de dados foram migrado.
14. Se você criou uma nova máquina virtual, configure a máquina virtual do Windows Azure e a instância do SQL Server seguindo as etapas para [se conectar à instância do SQL Server VM no SSMS em outro computador](virtual-machines-windows-sql-connect.md).

## <a name="backup-to-file-and-copy-to-vm-and-restore"></a>Backup em arquivo e cópia a VM e restauração

Use este método quando você não pode usar a implantação de um banco de dados SQL Server para um assistente do Microsoft Azure VM porque você está migrando para uma versão do SQL Server antes de 2014 do SQL Server ou o seu arquivo de backup for maior do que 1 TB. Se o arquivo de backup for maior que 1 TB, você deve distribui-lo porque o tamanho máximo de um disco de máquina virtual é 1 TB. Use as seguintes etapas gerais para migrar um banco de dados de usuário usando este método manual:

1.  Execute um backup completo do banco de dados para um local nas instalações.
2.  Criar ou carregar uma máquina virtual com a versão do SQL Server desejado.
3.  Conectividade com base nos seus requisitos de instalação. Consulte [conectar-se a uma máquina Virtual no Azure (Resource Manager) do servidor SQL](virtual-machines-windows-sql-connect.md).
4.  Copie o arquivo de backup (s) para sua VM usando a área de trabalho remota, o Windows Explorer ou o comando de cópia em um prompt de comando.

## <a name="backup-to-url-and-restore"></a>Backup a URL e restauração

Use o método de [backup para URL](https://msdn.microsoft.com/library/dn435916.aspx) quando você não pode usar a implantação de um banco de dados SQL Server para um assistente Microsoft Azure VM porque seu arquivo de backup for maior do que 1 TB e você estiver migrando from e to 2016 do SQL Server. Bancos de dados menores do que 1 TB ou executando uma versão do SQL Server anteriores ao SQL Server 2016, recomenda-se o uso do assistente. Com 2016 do SQL Server, conjuntos de backup distribuídos são suportados, são recomendados para desempenho e necessárias para exceder os limites de tamanho por blob. Bancos de dados muito grandes, recomenda-se o uso do [Serviço de importação/exportação do Windows](../storage/storage-import-export-service.md) .

## <a name="detach-and-copy-to-url-and-attach-from-url"></a>Desanexar e copie a URL e anexar da URL

Use este método quando você planeja [armazenar esses arquivos usando o serviço de armazenamento de Blob do Windows Azure](https://msdn.microsoft.com/library/dn385720.aspx) e anexá-los ao SQL Server em execução em uma VM do Azure, particularmente com bancos de dados muito grandes. Use as seguintes etapas gerais para migrar um banco de dados do usuário usando este método manual:

1.  Desanexe os arquivos de banco de dados da instância do banco de dados local.
2.  Copie os arquivos de banco de dados desanexados no armazenamento de blob Azure usando o [utilitário de linha de comando AZCopy](../storage/storage-use-azcopy.md).
3.  Anexe os arquivos de banco de dados à instância do SQL Server no Windows Azure VM da URL do Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Converter a VM e carregar a URL e implantar como nova VM

Use esse método para migrar todos sistema e usuário bancos de dados em uma instância do SQL Server no local para a máquina virtual do Azure. Use as seguintes etapas gerais para migrar de uma instância do SQL Server inteira usando este método manual:

1.  Converta máquinas virtuais ou físicos VHDs Hyper-V usando o [Conversor de máquina Virtual da Microsoft](http://technet.microsoft.com/library/dn873998.aspx).
2.  Carregar arquivos VHD para armazenamento do Windows Azure usando o [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3.  Implante uma nova máquina virtual usando o VHD carregado.

> [AZURE.NOTE] Para migrar um aplicativo inteiro, considere o uso de [Recuperação de sites do Windows Azure](../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Unidade de disco rígido de remessa

Use o [método de serviço de importação/exportação do Windows](../storage/storage-import-export-service.md) para transferir grandes quantidades de dados de arquivo para o armazenamento de Blob do Windows Azure em situações onde carregar pela rede é muito mais alto ou não é viável. Com esse serviço, você deve enviar um ou mais discos rígidos que contém esses dados para um centro de dados do Azure, onde seus dados serão carregados para sua conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a execução do SQL Server nas máquinas virtuais do Windows Azure, consulte [SQL Server em máquinas virtuais do Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).

Para obter instruções sobre como criar uma máquina Virtual do Windows Azure SQL Server a partir de uma imagem capturada, consulte [dicas e truques 'clonagem' de máquinas virtuais do Windows Azure SQL de imagens capturadas](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) no blog do CSS engenheiros do SQL Server.
