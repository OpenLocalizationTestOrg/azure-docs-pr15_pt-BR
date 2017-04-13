<properties 
   pageTitle="Especificando configurações de DNS em um arquivo de configuração do serviço | Microsoft Azure"
   description="especificar configurações de DNS personalizadas usando o arquivo de configuração do serviço de rede virtual"
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
   ms.date="02/24/2016"
   ms.author="jdial" />

# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Especificando configurações de DNS em um arquivo de configuração do serviço

## <a name="dns-elements"></a>Elementos DNS

Um arquivo de configuração do serviço pode conter um elemento de DnsServers com uma lista de endereços IPv4 para os servidores de sistema de nome de domínio (DNS) que o serviço usará. Configurações no arquivo de configuração do serviço prevalecem sobre as configurações no arquivo de configuração de rede. Para obter mais informações, consulte [Esquema de configuração de serviço do Azure (.cscfg arquivo)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Elemento de NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

>[AZURE.WARNING] O atributo de **nome** no elemento **ServidorDNS** é usado somente como um nome de referência. Ele não representa o nome do host do servidor DNS. Cada valor de atributo **ServidorDNS** deve ser exclusivo entre a inscrição do Microsoft Azure inteira.

## <a name="see-also"></a>Consulte também

[Esquema de configuração do serviço Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Esquema de configuração de rede Virtual Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Configurar uma rede Virtual usando arquivos de configuração de rede](http://go.microsoft.com/fwlink/?LinkId=248094)

[Sobre as configurações de rede Virtual no Portal de gerenciamento](http://go.microsoft.com/fwlink/?LinkId=248092)

