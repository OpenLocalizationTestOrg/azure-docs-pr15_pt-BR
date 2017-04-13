<properties
    pageTitle="Configurar a integração do Azure cofre chave para o SQL Server em VMs Azure (Gerenciador de recurso)"
    description="Saiba como automatizar a configuração de criptografia do SQL Server para uso com o Azure chave cofre. Este tópico explica como usar o Azure chave cofre integração com máquinas virtuais de SQL Server criadas com o Gerenciador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Configurar a integração do Azure cofre chave para o SQL Server em VMs Azure (Gerenciador de recurso)

> [AZURE.SELECTOR]
- [Gerenciador de recursos](virtual-machines-windows-ps-sql-keyvault.md)
- [Clássico](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Visão geral
Há vários recursos de criptografia do SQL Server, como [criptografia de dados transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [criptografia de nível de coluna (LIM)](https://msdn.microsoft.com/library/ms173744.aspx)e [criptografia de backup](https://msdn.microsoft.com/library/dn449489.aspx). Esses formulários de criptografia exigem que você gerenciar e armazenar as chaves de criptografia usado para criptografia. O serviço do Azure chave cofre (AKV) foi projetado para melhorar a segurança e o gerenciamento destas teclas em um local seguro e altamente disponível. O [SQL Server Connector](http://www.microsoft.com/download/details.aspx?id=45344) permite que o SQL Server para utilizar estas teclas do Azure chave cofre.

Se você está executando o SQL Server com locais máquinas, há etapas que [você pode seguir para acessar Azure chave cofre da máquina local do SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Mas, para SQL Server no Azure VMs, você poderá economizar tempo usando o recurso de *Integração de Cofre de chave do Azure* .

Quando este recurso está habilitado, ele automaticamente instala o SQL Server Connector, configura o provedor EKM para acessar Cofre de chave do Azure e cria a credencial para que você possa acessar seu cofre. Se você procurou as etapas na documentação do local mencionado anteriormente, você pode ver que esse recurso automatiza as etapas 2 e 3. A única coisa que você ainda precisa fazer manualmente é criar as teclas e chave cofre. A partir daí, toda a configuração do seu SQL VM é automatizada. Depois que esse recurso concluiu essa configuração, você pode executar instruções T-SQL para começar a criptografar seus bancos de dados ou backups como você faria normalmente.

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>Habilitar e configurar a integração de AKV
Você pode habilitar a integração de AKV durante a configuração ou configurá-lo para VMs existentes.

### <a name="new-vms"></a>Novas VMs
Se você estiver provisionar uma nova máquina de virtual do SQL Server com o Gerenciador de recursos, o portal do Azure fornece uma etapa para habilitar a integração do Azure chave cofre. O recurso de Cofre de chave do Azure está disponível somente para o Enterprise, desenvolvedor e avaliação edições do SQL Server.

![Integração do SQL Azure cofre chave](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Para obter detalhes de provisionamento, consulte [provisionar uma máquina virtual de SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes
Para máquinas virtuais do SQL Server existentes, selecione máquina virtual do SQL Server. Em seguida, selecione a seção de **configuração do SQL Server** da lâmina **configurações** .

![Integração de AKV SQL para VMs existentes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Na lâmina **configuração do SQL Server** , clique no botão **Editar** na seção de integração automatizada chave cofre.

![Configurar a integração de AKV SQL para VMs existentes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

Quando terminar, clique no botão **Okey** na parte inferior da lâmina **configuração do SQL Server** para salvar suas alterações.

>[AZURE.NOTE] Você também pode configurar integração de AKV usando um modelo. Para obter mais informações, consulte o [modelo de início rápido Azure para integração do Azure chave cofre](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]
