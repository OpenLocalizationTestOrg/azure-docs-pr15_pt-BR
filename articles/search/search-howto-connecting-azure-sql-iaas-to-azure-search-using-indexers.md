<properties 
    pageTitle="Configurar uma conexão de um indexador de pesquisa do Azure para o SQL Server em uma máquina virtual Azure | Microsoft Azure | Indexadores" 
    description="Habilitar as conexões criptografadas e configurar o firewall para permitir conexões com o SQL Server em uma máquina virtual Azure (máquina virtual) de um indexador na pesquisa do Azure." 
    services="search" 
    documentationCenter="" 
    authors="jack4it" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="09/26/2016" 
    ms.author="jackma"/>

# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurar uma conexão de um indexador de pesquisa do Azure para o SQL Server em uma máquina virtual do Azure

Conforme observado na [Conexão Azure SQL Database para pesquisa de Azure usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions), criar indexadores contra **SQL Server no Azure VMs** (ou **SQL Azure VMs** abreviada) é suportado pela pesquisa do Azure, mas há algumas segurança pré-requisitos para cuidam da primeira. 

**Duração da tarefa:** Cerca de 30 minutos, supondo que você já instalado um certificado na máquina virtual.

## <a name="enable-encrypted-connections"></a>Habilitar as conexões criptografadas

Pesquisa Azure requer um canal criptografado para todas as solicitações de indexador ao longo de uma conexão de internet pública. Esta seção apresenta as etapas para que isso funcione.

1. Verifique as propriedades do certificado para verificar que o nome de assunto é o nome de domínio totalmente qualificado (FQDN) da máquina virtual do Azure. Você pode usar uma ferramenta como CertUtils ou o snap-in de certificados para exibir as propriedades. Você pode obter o FQDN na seção de Essentials da lâmina de serviço na máquina virtual no campo de **rótulo de nome do endereço de IP público/DNS** , no [portal do Azure](https://portal.azure.com/).

    - Para VMs criadas usando o modelo do **Gerenciador de recursos** mais recente, o FQDN é formatado como `<your-VM-name>.<region>.cloudapp.azure.com`. 

    - Para mais antigas VMs criadas como um **clássico** máquina virtual, o FQDN é formatado como `<your-cloud-service-name.cloudapp.net>`. 

2. Configure o SQL Server para usar o certificado usando o Editor do registro (regedit). 

    Embora o Gerenciador de configuração do SQL Server geralmente é usado para essa tarefa, você não pode usá-lo para esse cenário. Ele não encontrará o certificado importado porque o FQDN da máquina virtual no Azure não coincidir com o FQDN conforme determinado pela máquina virtual (ele identifica o domínio como o computador local ou o domínio de rede à qual ele está associado). Quando os nomes não corresponderem, use regedit para especificar o certificado.

    - Em regedit, navegue até essa chave do registro: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
    O `[MSSQL13.MSSQLSERVER]` parte varia com base no nome de instância e versão. 

    - Defina o valor da chave de **certificado** para a **impressão digital** do certificado SSL que você importou para a máquina virtual.

    Há várias maneiras de obter a impressão digital, alguns melhor do que outras pessoas. Se você copiá-lo o snap-in de **certificados no MMC** , você provavelmente selecionará um invisível levam caractere [conforme descrito neste artigo de suporte](https://support.microsoft.com/kb/2023869/), que resulta em um erro ao tentar uma conexão. Existem várias soluções alternativas para corrigir esse problema. A maneira mais fácil é backspace sobre e digite novamente o primeiro caractere da impressão digital para remover o caractere à esquerda no campo valor da chave regedit. Como alternativa, você pode usar uma ferramenta diferente para copiar a impressão digital.

3. Conceder permissões para a conta de serviço. 

    Certificar-se de que a conta de serviço do SQL Server é concedida a permissão apropriada na chave privada do certificado SSL. Se ignorar esta etapa, o SQL Server não iniciará. Você pode usar o snap-in **certificados** ou **CertUtils** para essa tarefa.

4. Reinicie o serviço SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurar conectividade do SQL Server na máquina virtual

Após configurar a conexão criptografada obrigatório pela pesquisa do Azure, há etapas adicionais de configuração intrínsecas ao SQL Server no Azure VMs. Se você ainda não fez isso, a próxima etapa é concluir a configuração usando qualquer um dos seguintes artigos:

- Para um **Gerenciador de recursos de** máquina virtual, consulte [conectar a um SQL Máquina Virtual do servidor no Azure usando o Gerenciador de recursos](../virtual-machines/virtual-machines-windows-sql-connect.md). 

- Para um **clássico** máquina virtual, consulte [conectar a uma SQL Server Máquina Virtual em clássico do Azure](../virtual-machines/virtual-machines-windows-classic-sql-connect.md).

Em particular, examine a seção cada artigo para "Conectando através da internet".

## <a name="configure-the-network-security-group-nsg"></a>Configurar o grupo de segurança de rede (NSG)

Não é incomum configurar o NSG e ponto de extremidade Azure correspondente ou lista de controle de acesso (ACL) para tornar sua máquina virtual do Azure acessíveis a outros participantes. Chances são que você já fez isso antes para permitir que sua própria lógica de aplicativo para se conectar à sua máquina de virtual do SQL Azure. Ele não é diferente para uma conexão de pesquisa do Azure para sua máquina de virtual do SQL Azure. 

Os links a seguir fornecem instruções sobre configuração de NSG para implantações de máquina virtual. Use essas instruções para ACL um ponto de extremidade do Azure pesquisa com base em seu endereço IP.

> [AZURE.NOTE] Para o plano de fundo, consulte [o que é um grupo de segurança de rede?](../virtual-network/virtual-networks-nsg.md)

- Para um **Gerenciador de recursos de** máquina virtual, consulte [como criar NSGs para implantações de ARM](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 

- Para um **clássico** máquina virtual, consulte [como criar NSGs para implantações de clássico](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Endereçamento IP pode representar alguns desafios que são solucionar facilmente se você conhecer o problema e possíveis soluções alternativas. As seções restantes fornecem recomendações para lidar com problemas relacionados a endereços IP na ACL.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Restringir o acesso para o endereço IP do serviço de pesquisa

É altamente recomendável que você restrinja o acesso ao endereço IP do seu serviço de pesquisa na ACL em vez de tornar suas VMs do SQL Azure aberto a qualquer solicitação de conexão. Você pode localizar facilmente o endereço IP, ping o FQDN (por exemplo, `<your-search-service-name>.search.windows.net`) do seu serviço de pesquisa.

#### <a name="managing-ip-address-fluctuations"></a>Gerenciando flutuações de endereço IP

Se seu serviço de pesquisa tiver apenas uma unidade de pesquisa (ou seja, uma réplica e uma partição), o endereço IP será alterada durante a reinicialização do serviço de rotina, invalidem uma ACL existente com endereço IP do seu serviço de pesquisa.

Uma maneira de evitar o erro de conectividade subsequentes é usar mais de uma réplica e uma partição na pesquisa do Azure. Isso aumenta o custo, mas ele também resolve o problema de endereço IP. Em pesquisa do Azure, endereços IP não são alterados quando você tiver mais de uma unidade de pesquisa.

Uma segunda abordagem é permitir que a conexão falhar e reconfigure ACLs na NSG. Em média, você pode esperar endereços IP para alterar todas as semanas. Para clientes que faça a indexação controlado raramente, essa abordagem talvez seja viável.

Uma terceira abordagem viável (mas não particularmente segura) é especificar o intervalo de endereços IP do Azure região onde seu serviço de pesquisa provisionado. Na lista de intervalos de IP do qual os endereços IP públicos estão alocados para recursos Azure é publicada em [intervalos de IP do data center Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Incluir os endereços IP de portal de pesquisa do Azure

Se você estiver usando o portal do Azure para criar um indexador, lógica de portal de pesquisa do Azure também precisa de acesso para sua máquina de virtual do SQL Azure durante a hora de criação. Endereços IP portal de pesquisa Azure podem ser encontrados pelo ping `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Próximas etapas

Com a configuração do caminho, agora você pode especificar um SQL Server na máquina virtual do Azure como fonte de dados para um indexador de pesquisa do Azure. Consulte [Conectando Azure SQL Database para pesquisa de Azure usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) para obter mais informações.
