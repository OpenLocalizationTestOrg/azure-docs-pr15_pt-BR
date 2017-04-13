<properties 
   pageTitle="Especificando configurações de DNS em um arquivo de configuração de rede virtual | Microsoft Azure"
   description="Como alterar as configurações do servidor DNS em uma rede virtual usando um arquivo de configuração de rede virtual no modelo clássico de implantação"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" 
   tags="azure-service-management" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="jdial" /> 


# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Especificando configurações de DNS em um arquivo de configuração de rede virtual

Um arquivo de configuração de rede tem dois elementos que você pode usar para especificar as configurações do sistema de nome de domínio (DNS): **DnsServers** e **DnsServerRef**. Você pode adicionar uma lista de servidores DNS especificando seus endereços IP e nomes de referência ao elemento **DnsServers** . Em seguida, você pode usar um elemento **DnsServerRef** para especificar quais entradas de servidor DNS do elemento DnsServers são usadas para sites de rede diferente dentro de sua rede virtual.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação clássico.

O arquivo de configuração de rede pode conter os seguintes elementos. O título de cada elemento está vinculado a uma página que fornece informações adicionais sobre as configurações de valor do elemento.

>[AZURE.IMPORTANT] Para obter informações sobre como configurar o arquivo de configuração de rede, consulte [Configurar uma rede Virtual utilizando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md). Para obter informações sobre cada elemento contido no arquivo de configuração de rede, consulte [Esquema de configuração de rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

[Elemento de DNS](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

>[AZURE.WARNING] O atributo de **nome** no elemento **ServidorDNS** é usado apenas como uma referência para o elemento **DnsServerRef** . Ele não representa o nome do host do servidor DNS. Cada valor de atributo **ServidorDNS** deve ser exclusivo entre a assinatura inteira do Microsoft Azure

[Elemento de Sites de rede virtual](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

>[AZURE.NOTE] Para especificar esta configuração para o elemento de Sites de rede Virtual, ele deve ser anteriormente definido no elemento de DNS. O *nome* do DnsServerRef no elemento de Sites de rede Virtual deve se referir a um valor de nome especificado no elemento de DNS para ServidorDNS *nome*.

## <a name="next-steps"></a>Próximas etapas

- Compreenda o [esquema de configuração de rede Virtual Azure](http://go.microsoft.com/fwlink/?LinkId=248093).
- Compreenda o [esquema de configuração do serviço Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
- [Configurar uma rede virtual usando arquivos de configuração de rede](virtual-networks-using-network-configuration-file.md).
