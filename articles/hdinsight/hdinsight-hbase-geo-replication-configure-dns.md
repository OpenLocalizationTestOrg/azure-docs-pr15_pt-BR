<properties 
   pageTitle="Configurar o DNS entre duas redes virtuais Azure | Microsoft Azure" 
   description="Saiba como configurar conexões VPN e resolução de nomes de domínio entre duas redes virtuais e como configurar replicação HBase geográfica." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/28/2016"
   ms.author="jgao"/>

# <a name="configure-dns-between-two-azure-virtual-networks"></a>Configurar o DNS entre duas redes virtuais Azure

> [AZURE.SELECTOR]
- [Configurar a conectividade VPN](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurar o DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurar a replicação de HBase](hdinsight-hbase-geo-replication.md) 


Saiba como adicionar e configurar os servidores DNS Azure redes virtuais para lidar com resolução de nomes em e entre as redes virtuais.

Este tutorial é a segunda parte da [série] de[ hdinsight-hbase-geo-replication] sobre a criação de replicação HBase geográfica:

- [Configurar uma conectividade VPN entre duas redes virtuais][hdinsight-hbase-geo-replication-vnet]
- Configurar o DNS para as redes virtuais (Este tutorial)
- [Configurar a replicação de localização geográfica HBase][hdinsight-hbase-geo-replication]


O diagrama a seguir ilustra as duas redes virtuais criado na [Configurar uma conectividade VPN entre duas redes virtuais][hdinsight-hbase-geo-replication-vnet]:

![Diagrama de rede virtual de replicação de HDInsight HBase][img-vnet-diagram]

##<a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o PowerShell do Azure**.

    Antes de executar scripts do PowerShell, verifique se que você está conectado à sua assinatura do Azure usando o seguinte cmdlet:

        Add-AzureAccount

    Se você tiver várias assinaturas Azure, use o cmdlet a seguir para configurar a assinatura atual:

        Select-AzureSubscription <AzureSubscriptionName>
        
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Rede virtual do Azure dois com conectividade VPN**.  Para obter instruções, consulte [Configurar uma conexão VPN entre duas redes virtuais Azure][hdinsight-hbase-geo-replication-vnet].

>[AZURE.NOTE] Nomes de serviço Azure e máquina virtual deve ser exclusivo. O nome usado neste tutorial é Contoso-[nome do serviço do Azure/máquina virtual]-[UE / EUA]. Por exemplo, Contoso-VNet-UE é a rede virtual Azure no Centro de dados do Norte da Europa; Contoso-DNS-US é o servidor DNS máquina virtual no Centro de dados do Leste EUA. Você deve criar seus próprios nomes.
 
 
##<a name="create-azure-virtual-machines-to-be-used-as-dns-servers"></a>Criar máquinas virtuais Azure a ser usado como servidores DNS

**Para criar uma máquina virtual dentro Contoso-VNet-UE, chamado Contoso-DNS-UE**

1.  Clique em **novo**, **CALCULAR**, **máquina VIRTUAL**, **De GALERIA**.
2.  Escolha o **Windows Server 2012 R2 data center**.
3.  Inserir:
    - **Nome de máquina VIRTUAL**: Contoso-DNS-UE
    - **Novo nome de usuário**: 
    - **Nova senha**: 
4.  Inserir:
    - **Serviço de nuvem**: criar um novo serviço de nuvem
    - **Região/grupo de AFINIDADE/VIRTUAL de rede**: (selecione Contoso VNet-da UE)
    - **Sub-redes da rede VIRTUAL**: sub-rede-1
    - **Conta de armazenamento**: usar uma conta de armazenamento gerado automaticamente
    
        O nome do serviço de nuvem será o mesmo que o nome da máquina virtual. Nesse caso, que é Contoso-DNS-UE. Para máquinas virtuais subsequentes, posso pode optar por usar o mesmo serviço de nuvem.  Todas as máquinas virtuais sob o mesmo serviço de nuvem compartilhar a mesma rede virtual e o sufixo do domínio.

        A conta de armazenamento é usada para armazenar o arquivo de imagem de máquina virtual. 
    - **Pontos de EXTREMIDADE**: (Role para baixo e selecione **DNS**) 

Após a máquina virtual é criada, descubra os internos externo e IP.

1.  Clique no nome da máquina virtual, **Contoso DNS-da UE**.
2.  Clique em **Painel de controle**.
3.  Anote:
    - ENDEREÇO IP VIRTUAL PÚBLICO
    - ENDEREÇO IP INTERNO


**Para criar uma máquina virtual dentro de Contoso-VNet-US, chamado Contoso-DNS-US** 

- Repita o mesmo procedimento para criar uma máquina virtual com os seguintes valores:
    - NOME de máquina VIRTUAL: Contoso-DNS-US
    - REGIÃO/grupo de AFINIDADE/rede VIRTUAL: selecione Contoso-VNET-US
    - REDE VIRTUAL sub-redes: Sub-rede-1
    - CONTA de armazenamento: Usar uma conta de armazenamento gerado automaticamente
    - Pontos de EXTREMIDADE: (selecione DNS)

##<a name="set-static-ip-addresses-for-the-two-virtual-machines"></a>Definir endereços IP estáticos para as duas máquinas virtuais

Servidores DNS requer endereços IP estáticos.  Esta etapa não pode ser feita a partir do Portal de clássico do Azure. Você usará o PowerShell do Azure.

**Para configurar o endereço IP estático para as duas máquinas virtuais**

1. Abra o Windows PowerShell ISE.
2. Execute os seguintes cmdlets.  

        Add-AzureAccount
        Select-AzureSubscription [YourAzureSubscriptionName]
        
        Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
        Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 

    ServiceName é o nome de serviço de nuvem. Porque o servidor DNS é a primeira máquina virtual do serviço de nuvem, o nome do serviço de nuvem é o mesmo nome da máquina virtual.

    Talvez seja necessário atualizar ServiceName e o nome para corresponder os nomes que você tem.


##<a name="add-the-dns-server-role-the-two-virtual-machines"></a>Adicionar máquinas virtuais função duas servidor DNS

**Para adicionar a função de servidor DNS para Contoso-DNS-UE**

1.  No Portal do Azure clássico, clique em **máquinas virtuais** à esquerda. 
2.  Clique em **Contoso DNS-da UE**.
3.  Clique em **Painel de controle** da parte superior.
4.  Clique em **conectar-se** da parte inferior e siga as instruções para conectar-se à máquina virtual via RDP.
2.  Dentro da sessão RDP, clique no botão do Windows no canto inferior esquerdo para abrir a tela inicial.
3.  Clique no bloco do **Gerenciador de servidor** .
4.  Clique em **Adicionar funções e recursos**.
5.  Clique em **Avançar**
6.  Selecione **instalação baseada em função ou baseada em recursos**e clique em **Avançar**.
7.  Selecione sua máquina virtual do DNS (ele deve estar realçado já) e clique em **Avançar**.
8.  Verifique o **servidor DNS**.
9.  Clique em **Adicionar recursos**e, em seguida, clique em **continuar**.
10. Clique três vezes em **Avançar** e, em seguida, clique em **instalar**. 

**Para adicionar a função de servidor DNS para Contoso-DNS-US**

- Repita as etapas para adicionar a função DNS a **Contoso-DNS-US**.

##<a name="assign-dns-servers-to-the-virtual-networks"></a>Atribuir servidores DNS para as redes virtuais

**Para registrar os dois servidores DNS**

1.  No Portal do Azure clássico, clique em **novo**, **Serviços de rede**, **Uma rede VIRTUAL**, **Registrar servidor DNS**.
2.  Inserir:
    - **Nome**: Contoso-DNS-UE
    - **Endereço de IP do servidor DNS**: 10.1.0.4 – o endereço IP deve correspondência o endereço IP de máquina virtual do servidor DNS.
     
3.  Repita o processo para registrar Contoso-DNS-US com as seguintes configurações:
    - **Nome**: Contoso-DNS-US
    - **Endereço de IP do servidor DNS**: 10.2.0.4

**Para atribuir os dois servidores DNS para as duas redes virtuais**

1.  No painel esquerdo no Portal do clássico, clique em **redes** .
2.  Clique em **Contoso-VNet-UE**.
3.  Clique em **Configurar**.
4.  Selecione **Contoso-DNS-UE** na seção **dns servers** .
5.  Clique em **Salvar** na parte inferior da página e clique em **Sim** para confirmar.
6.  Repita o processo para atribuir o servidor DNS **Contoso-DNS-US** à rede virtual **Contoso-VNet-US** .

Todas as máquinas virtuais que foram implantadas para as redes virtuais deve ser reinicializadas para atualizar a configuração do servidor DNS.

**Para reinicializar as máquinas virtuais**

1. No Portal do Azure clássico, clique em **máquinas virtuais** à esquerda.
2. Clique em **Contoso DNS-da UE**.
3. Clique em **Painel de controle** da parte superior.
4. Na parte inferior, clique em **Reiniciar** .
5. Repita as mesmas etapas para reinicializar **Contoso-DNS-US**.


##<a name="configure-dns-conditional-forwarders"></a>Configurar os encaminhadores condicionais DNS

O servidor DNS em cada rede virtual só pode resolver nomes DNS dentro dessa rede virtual. Você precisa configurar um encaminhador condicional para apontar para o servidor DNS de ponto para resoluções de nome da rede virtual do ponto.

Para configurar encaminhadores condicionais, você precisa saber o sufixo dos dois servidores DNS referente ao domínio. Os sufixos DNS podem ser diferentes dependendo a configuração de serviços de nuvem que você usou quando você criou as máquinas virtuais. Para cada sufixo DNS usado na rede virtual, você deve adicionar um encaminhador condicional. 

**Para localizar o sufixo dos dois servidores DNS referente ao domínio**

1. RDP em **Contoso DNS-da UE**.
2. Abra o console do Windows PowerShell, ou prompt de comando.
3. Executar **ipconfig**e anote o **sufixo DNS específico de Conexão**.
4. Não fechar a sessão RDP, você ainda precisará dele. 
5. Repita as mesmas etapas para descobrir o **sufixo DNS específico de Conexão** da **Contoso-DNS-US**.


**Para configurar os encaminhadores DNS**
 
1.  Da sessão RDP para **Contoso DNS-da UE**, clique na chave do Windows no canto inferior esquerdo.
2.  Clique em **Ferramentas administrativas**.
3.  Clique em **DNS**.
4.  No painel esquerdo, expanda **DSN**, **Contoso DNS-da UE**.
5.  Insira as seguintes informações:
    - **Domínio DNS**: insira o sufixo DNS de Contoso-DNS-US. Por exemplo: Contoso-DNS-US.b5.internal.cloudapp.net.
    - **Endereços IP dos servidores mestres**: insira 10.2.0.4, que é endereço IP do Contoso-DNS-US.
6.  Pressione **ENTER**e, em seguida, clique em **Okey**.  Agora você poderá resolver endereço IP do Contoso-DNS-US da Contoso DNS-da UE.
7.  Repita as etapas para adicionar um encaminhadores DNS para o serviço DNS na máquina virtual Contoso-DNS-US com os seguintes valores:
    - **Domínio DNS**: insira o sufixo DNS da Contoso-DNS-UE. 
    - **Endereços IP dos servidores mestres**: insira 10.2.0.4, que é endereço IP do Contoso-DNS-UE.

##<a name="test-the-name-resolution-across-the-virtual-networks"></a>Testar a resolução de nome em redes virtuais

Agora você pode testar a resolução de nome de host entre as redes virtuais. Ping é bloqueada pelo firewall por padrão.  Você pode usar o nslookup para resolver as máquinas virtuais do servidor DNS (você deve usar FQDN) nas redes ponto.  


##<a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar a resolução de nome em redes virtual com conexões VPN. Os outros dois artigos na série abrangem:

- [Configurar uma conexão VPN entre duas redes virtuais Azure][hdinsight-hbase-geo-replication-vnet]
- [Configurar a replicação de localização geográfica HBase][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[powershell-install]: powershell-install-configure.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png 
