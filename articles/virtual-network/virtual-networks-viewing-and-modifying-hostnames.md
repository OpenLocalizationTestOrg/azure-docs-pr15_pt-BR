<properties 
   pageTitle="Visualizar e modificar nomes de host | Microsoft Azure"
   description="Como exibir e alterar nomes de host para Azure máquinas virtuais, da web e funções de trabalho para resolução de nome"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="viewing-and-modifying-hostnames"></a>Visualizar e modificar nomes de host

Para permitir que suas instâncias de função a ser referido pelo nome do host, você deve definir o valor para o nome do host no arquivo de configuração do serviço para cada função. Você pode fazer isso adicionando o nome de host desejado para o atributo **vmName** do elemento de **função** . O valor do atributo **vmName** é usado como base para o nome de host de cada ocorrência de função. Por exemplo, se **vmName** for *webrole* e há três instâncias dessa função, os nomes de host das instâncias serão *webrole0*, *webrole1*e *webrole2*. Você não precisa especificar um nome de host para máquinas virtuais no arquivo de configuração, porque o nome de host para uma máquina virtual é preenchido com base no nome da máquina virtual. Para obter mais informações sobre como configurar um serviço do Microsoft Azure, consulte [Esquema de configuração de serviço do Azure (.cscfg arquivo)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Exibir nomes de host

Você pode exibir os nomes de host de máquinas virtuais e instâncias de função em um serviço de nuvem usando qualquer uma das ferramentas abaixo.

### <a name="azure-portal"></a>Portal do Azure

Você pode usar o [portal do Azure](http://portal.azure.com) para exibir os nomes de host para máquinas virtuais no blade visão geral para uma máquina virtual. Tenha em mente que a lâmina mostra um valor para o **nome** e o **Nome de Host**. Embora inicialmente são os mesmos, a alteração do nome de host não alterará o nome da máquina virtual ou instância de função.

Instâncias de função também podem ser visualizadas no portal do Azure, mas quando você lista as instâncias em um serviço de nuvem, o nome do host não é exibido. Você verá um nome para cada instância, mas esse nome não representa o nome do host.

### <a name="service-configuration-file"></a>Arquivo de configuração do serviço

Você pode baixar o arquivo de configuração do serviço para um serviço implantado da lâmina **Configurar** do serviço no portal do Azure. Em seguida, você pode procurar o atributo **vmName** para o elemento de **nome de função** ver o nome de host. Tenha em mente que esse nome de host é usado como base para o nome de host de cada ocorrência de função. Por exemplo, se **vmName** for *webrole* e há três instâncias dessa função, os nomes de host das instâncias serão *webrole0*, *webrole1*e *webrole2*.

### <a name="remote-desktop"></a>Área de trabalho remota

Após habilitar a área de trabalho remota (Windows), o Windows PowerShell remoto (Windows) ou conexões de SSH (Linux e Windows) à sua máquinas virtuais ou instâncias de função, você pode exibir o nome do host de uma conexão de área de trabalho remota ativa de várias maneiras:

- Digite hostname no prompt de comando ou terminal SSH.

- Digite ipconfig/tudo no comando prompt (somente Windows).

- Exiba o nome do computador nas configurações do sistema (somente Windows).

### <a name="azure-service-management-rest-api"></a>Gerenciamento de serviço Azure API REST

Em um cliente do resto, siga estas instruções:

1. Certifique-se de que você tenha um certificado de cliente para se conectar ao portal do Azure. Para obter um certificado de cliente, siga as etapas apresentadas em [como: Download e importar configurações de publicação e informações de assinatura](https://msdn.microsoft.com/library/dn385850.aspx). 

1. Defina uma entrada de cabeçalho x-ms-version com um valor de 2013-11-01 de chamada.

1. Enviar uma solicitação no seguinte formato: https://management.core.windows.net/\<esta opção-id\>/services/hostedservices/\<nome do serviço\>?embed-detalhe = true

1. Procure o elemento de **nome de host** para cada elemento **RoleInstance** .

>[AZURE.WARNING] Você também pode exibir o sufixo de domínio interno para seu serviço de nuvem da resposta chamada REST, verificando o elemento **InternalDnsSuffix** ou executando ipconfig/tudo a partir de um prompt de comando em uma sessão de área de trabalho remota (Windows) ou por /etc/resolv.conf gato em execução em um terminal SSH (Linux).

## <a name="modifying-a-hostname"></a>Modificando um nome de host

Você pode modificar o nome do host qualquer máquina virtual ou instância da função ao carregar um arquivo de configuração do serviço modificada, ou renomeando o computador a partir de uma sessão de área de trabalho remota.

## <a name="next-steps"></a>Próximas etapas

[Resolução de nomes (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Esquema de configuração do serviço Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Esquema de configuração de rede Virtual Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Especificar as configurações de DNS usando arquivos de configuração de rede](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
