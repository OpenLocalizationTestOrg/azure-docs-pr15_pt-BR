<properties
    pageTitle="Considerações de segurança para o SQL Server no Azure | Microsoft Azure"
    description="Este tópico se refere a recursos criados com o modelo clássico de implantação e fornece orientações gerais para proteger o SQL Server em execução em uma máquina Virtual do Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
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
    ms.date="06/24/2016"
    ms.author="jroth" />

# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Considerações de segurança para SQL Server no Azure máquinas virtuais
 
Este tópico inclui geral diretrizes de segurança que ajudam a estabelecer acesso seguro a instâncias do SQL Server em uma máquina virtual do Azure. No entanto, para garantir o melhor proteção para suas instâncias de banco de dados do SQL Server no Azure, é recomendável que você implemente as práticas de segurança tradicionais local além das práticas recomendadas de segurança para o Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Para saber mais sobre as práticas de segurança do SQL Server, consulte o [SQL Server 2008 R2 práticas recomendadas de segurança - operacionais e tarefas administrativas](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)

Azure esteja em conformidade com várias normas e setor padrões que podem permitir que você criar uma solução compatível com SQL Server em execução em uma máquina Virtual. Para obter informações sobre conformidade regulamentar com o Azure, consulte [Central de confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/).

A seguir é uma lista de recomendações de segurança que devem ser considerados ao configurar e conexão com a instância do SQL Server em uma máquina virtual do Azure.

## <a name="considerations-for-managing-accounts"></a>Considerações para gerenciar contas:

- Crie uma conta de administrador local exclusivo que não é nomeada como **administrador**.

- Use complexas senhas fortes para todas as contas. Para obter mais informações sobre como criar uma senha forte, consulte o artigo de [dicas para a criação de um senhas fortes](http://windows.microsoft.com/en-us/windows-vista/Tips-for-creating-a-strong-password) .

- Por padrão, o Azure seleciona a autenticação do Windows durante a instalação do SQL Server Virtual Machine. Portanto, o logon do **administrador** está desabilitado e uma senha é atribuída pelo programa de instalação. Nós recomendamos que o logon do **administrador** deve não ser usado ou habilitado. A seguir estão estratégias alternativas se um logon de SQL for desejado:
    - Crie uma conta SQL que tem associação de administrador do sistema.
    - Se você deve usar um logon **SA** , habilite o logon e renomeá-la e atribuir uma nova senha.
    - As opções que foram mencionadas anteriormente exigem uma alteração o modo de autenticação **do SQL Server**e o modo de autenticação do Windows. Para obter mais informações, consulte [Alterar modo de autenticação de servidor](https://msdn.microsoft.com/library/ms188670.aspx).

## <a name="considerations-for-securing-connections-to-azure-virtual-machine"></a>Considerações sobre a proteção de conexões com o Azure Máquina Virtual:

- Considere o uso de [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) para administrar as máquinas virtuais em vez de públicas portas RDP.

- Use um [Grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) (NSG) para permitir ou negar tráfego de rede à sua máquina virtual. Se você quiser usar um NSG e já tiver um ponto de extremidade ACL no lugar, primeiro remova o ponto de extremidade ACL. Para obter informações sobre como fazer isso, consulte [Gerenciar listas de controle acesso (ACLs) para pontos de extremidade usando o PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

- Se você estiver usando pontos de extremidade, remova quaisquer pontos de extremidade na máquina virtual se você não usá-los. Para obter instruções sobre como usar ACLs com pontos de extremidade, consulte [Gerenciar a ACL em um ponto de extremidade](../virtual-network/virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).

- Habilite uma opção de conexão criptografada para uma instância do mecanismo de banco de dados do SQL Server em máquinas virtuais do Azure. Configure a instância do SQL server com um certificado auto-assinado. Para obter mais informações, consulte [Habilitar conexões criptografadas ao mecanismo de banco de dados](https://msdn.microsoft.com/library/ms191192.aspx) e [Sintaxe de cadeia de Conexão](https://msdn.microsoft.com/library/ms254500.aspx).

- Se suas máquinas virtuais deve ser acessadas somente a partir de uma rede específica, use o Firewall do Windows para restringir o acesso a determinados endereços IP ou sub-redes de rede.

## <a name="next-steps"></a>Próximas etapas

Se você também estiver interessado em práticas recomendadas sobre desempenho, consulte [Práticas recomendadas de desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md).

Para outros tópicos relacionados a execução do SQL Server no Azure VMs, consulte [SQL Server em máquinas virtuais do Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
