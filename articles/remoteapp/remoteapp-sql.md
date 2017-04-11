<properties
   pageTitle="O SQL Azure com o Azure RemoteApp | Microsoft Azure"
   description="Aprenda a usar o SQL Azure com o Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="sql-azure-with-azure-remoteapp"></a>O SQL Azure com o Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Muitas vezes, quando os clientes optar por hospedar seus aplicativos do Windows na nuvem com o Azure RemoteApp também queira migrar seus dados como servidores SQL para a nuvem para uma implantação de nuvem inteira. Isso permite solução de nuvem inteira hospedado que pode ser acessada a qualquer momento por qualquer dispositivo em qualquer lugar usando RemoteApp do Azure. Abaixo estão links e referências juntamente com orientação para ajudá-lo com esse processo.  

## <a name="migrate-your-sql-data"></a>Migrar os dados do SQL

Iniciar a [migração de um banco de dados do SQL Server Azure SQL Database](../sql-database/sql-database-cloud-migrate.md). 

## <a name="configure-azure-remoteapp"></a>Configurar o RemoteApp Azure
Hospede seu aplicativo do Windows Azure RemoteApp. A seguir é um nível muito alto passo a passo:

1.     Crie o [modelo do Azure RemoteApp máquina virtual](remoteapp-imageoptions.md). 
2.     Instale o aplicativo necessário na máquina virtual.
3.     Configurar o aplicativo para que ele se conecta ao banco de dados SQL e confirmar que ele funciona.
4.     Sysprep e desligar a máquina virtual. Capture isso como uma imagem para uso com o Azure. **Observação:** Você precisará garantir que o aplicativo seja capaz de manter as informações de conectividade de banco de dados durante o processo de sysprep. Se o aplicativo for incapaz de manter as informações de conexão de banco de dados, talvez você queira entre em contato com o fornecedor do aplicativo para verificar como podemos especificar a cadeia de conexão.
5.     Importe a imagem personalizada para sua biblioteca do Azure RemoteApp selecionando a localização geográfica adequada que reside sua implantação do SQL Azure. 
6.     Implantar um conjunto de RemoteApp no mesmo data center como sua implantação do SQL Azure usando o modelo acima e publicar o aplicativo. Implantando o Azure RemoteApp no mesmo data center como seu SQL Azure implantação ajuda a garantir que as velocidades de conexão mais rápidas e reduzir a latência. 

## <a name="app-and-sql-configuration-considerations"></a>Considerações de configuração do aplicativo e SQL:
Existem alguns pontos a considerar ao usar o SQL Azure com RemoteApp:

Saiba [como configurar um firewall de banco de dados do SQL Azure](../sql-database/sql-database-firewall-configure.md). Um trecho dos Estados artigo, "inicialmente, todo o acesso ao seu servidor de banco de dados do SQL Azure está bloqueado pelo firewall. Para começar a usar seu servidor de banco de dados de SQL Azure, você deve acesse o Portal clássico e especifique uma ou mais regras de firewall de nível de servidor que habilitam o acesso ao seu servidor do Azure SQL Database. Use as regras de firewall para especificar quais intervalos de endereços IP da Internet são permitidos e ou não aplicativos do Azure podem tentar se conectar ao seu servidor do Azure SQL Database."

Além disso, quando um computador tenta se conectar ao seu servidor de banco de dados da Internet, o firewall verifica no endereço IP de origem da solicitação contra o conjunto completo de nível de servidor e (se necessário) regras de firewall de nível de banco de dados. "Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall de nível de servidor, a conexão é concedido ao seu servidor do Azure SQL Database." Portanto, podemos fazer uso de intervalos IP e não apenas endereços IP de origem individuais.

Siga as instruções passo a passo em [como: definir configurações de firewall no banco de dados do SQL usando o Portal do Azure](../sql-database/sql-database-configure-firewall-settings.md) para especificar o intervalo IP. Quando você estiver configurando as regras de Firewall do SQL, forneça o intervalo IP da sub-rede especificado para o conjunto de RemoteApp do Azure. Isso deve permitir que os servidores de ARA para se conectar ao banco de dados SQL mesmo que eles serão tiver-endereços IP atribuídos dinamicamente.

## <a name="troubleshooting"></a>Solução de problemas
Se a experiência do uso de um aplicativo cliente hospedado no Azure RemoteApp que se conecta a um SQL banco de dados onde hospedado no Azure ou local está lento pode ser alguns motivos por que.  

- Latência de rede do seu dispositivo para Azure está alta. Mover para a conexão de rede melhor e mais rápida, você pode para obter melhor desempenho. Use [azurespeed.com](http://azurespeed.com/) como uma ferramenta geral para testar seu latência de dispositivos Azure data center.  
- O aplicativo cliente hospedado no Azure RemoteApp está sob pressão. Selecionar um plano de cobrança diferente como cobrança Premium melhorará o desempenho. Outro truque é monitorar os recursos que seu aplicativo está consumindo: durante uma sessão ativa executar uma combinação de teclas ctrl + alt + end que iniciará a tela SAS, selecione Gerenciador de tarefas e observe a utilização de recursos para o aplicativo.
- SQL server está sob carga excessiva ou não otimizado. Siga a orientação de SQL para solução de problemas. 

