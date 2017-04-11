<properties
   pageTitle="Requisitos de sistema do StorSimple | Microsoft Azure"
   description="Descreve o software, redes e os requisitos de alta disponibilidade e as práticas recomendadas para uma solução Microsoft Azure StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/31/2016"
   ms.author="alkohli"/>

# <a name="storsimple-software-high-availability-and-networking-requirements"></a>Requisitos de rede, alta disponibilidade e software de StorSimple

## <a name="overview"></a>Visão geral

Bem-vindo ao Microsoft Azure StorSimple. Este artigo descreve requisitos de sistema importantes e práticas recomendadas para o seu dispositivo StorSimple e para os clientes de armazenamento acessar o dispositivo. Recomendamos que você examine as informações cuidadosamente antes de implantar o seu sistema de StorSimple e, em seguida, voltar a ele conforme necessário durante a implantação e a operação subsequente.

Os requisitos de sistema incluem:

- **Requisitos de software para clientes de armazenamento** - descreve os sistemas operacionais compatíveis e quaisquer requisitos adicionais para esses sistemas operacionais.
- **Requisitos para o dispositivo de StorSimple de rede** - fornece informações sobre as portas que precisam ser abertas no firewall para permitir o tráfego de gerenciamento, nuvem ou iSCSI.
- **Requisitos de alta disponibilidade para StorSimple** - descreve requisitos de alta disponibilidade e as práticas recomendadas para o computador de dispositivo e host StorSimple. 


## <a name="software-requirements-for-storage-clients"></a>Requisitos de software para clientes de armazenamento

Os seguintes requisitos de software são para os clientes de armazenamento que acessar o seu dispositivo StorSimple.

| Sistemas operacionais compatíveis | Versão necessária | Requisitos adicionais/anotações |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008R2 SP1, 2012, 2012R2 |Volumes de iSCSI StorSimple são suportados para uso em somente os seguintes tipos de disco de Windows:<ul><li>Volume simples em disco básico</li><li>Volume Simple e espelhado em disco dinâmico</li></ul>Provisionamento fino do Windows Server 2012 e os recursos ODX são suportados se você estiver usando um volume de iSCSI StorSimple.<br><br>StorSimple pode criar provisionamento thin e totalmente provisionado volumes. Ele não é possível criar volumes parcialmente provisionados.<br><br>Reformatação um volume de provisionamento thin pode levar muito tempo. É recomendável excluir o volume e, em seguida, criar um novo em vez de reformatação. No entanto, se você ainda prefere reformatar um volume:<ul><li>Execute o seguinte comando antes da reformatação para evitar atrasos de recuperação de espaço: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Depois que a formatação é concluída, use o seguinte comando para reabilitar recuperação de espaço:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Aplique-o Windows Server 2012 conforme descrito em [2878635 KB](https://support.microsoft.com/kb/2870270) para seu computador Windows Server.</li></ul></li></ul></ul> Se você estiver configurando o Gerenciador de instantâneo StorSimple ou adaptador de StorSimple do SharePoint, vá para [requisitos de Software para componentes opcionais](#software-requirements-for-optional-components).|
| VMWare ESX | 5.5 e 6.0 | Suporte para VMWare vSphere como cliente iSCSI. Bloco VAAI recurso é compatível com VMware vSphere em dispositivos de StorSimple.
| Linux RHEL/CentOS | 5, 6 e 7 | Suporte para Linux iSCSI clientes com versões de iniciador open-iSCSI 5, 6 e 7. |
| Linux | SUSE Linux 11 | |
 > [AZURE.NOTE] IBM AIX não é suportado atualmente com StorSimple.

## <a name="software-requirements-for-optional-components"></a>Requisitos de software para componentes opcionais

Os seguintes requisitos de software são para os componentes de StorSimple opcionais (Gerenciador de instantâneo StorSimple e adaptador de StorSimple do SharePoint).

| Componente | Plataforma de host | Requisitos adicionais/anotações |
| --------------------------- | ---------------- | ------------- |
| Gerenciador de instantâneo StorSimple | Windows Server 2008R2 SP1, 2012, 2012R2 | Uso de StorSimple Gerenciador de instantâneo no Windows Server é necessário para backup e restauração de discos dinâmicos espelhados e quaisquer backups consistentes com o aplicativo.<br> Gerenciador de instantâneo StorSimple é suportado apenas no Windows Server 2008 R2 SP1 (64 bits), Windows 2012 R2 e Windows Server 2012.<ul><li>Se você estiver usando a janela Server 2012, você deve instalar .NET 3.5 – 4,5 antes de instalar o Gerenciador de instantâneo StorSimple.</li><li>Se você estiver usando o Windows Server 2008 R2 SP1, você deve instalar o Windows Management Framework 3.0 antes de instalar o Gerenciador de instantâneo StorSimple.</li></ul> |
| Adaptador de StorSimple do SharePoint | Windows Server 2008R2 SP1, 2012, 2012R2 |<ul><li>Adaptador de StorSimple do SharePoint só é suportado no SharePoint 2010 e o SharePoint 2013.</li><li>EDR requer o SQL Server Enterprise Edition, versão 2008 R2 ou 2012.</li></ul>|

## <a name="networking-requirements-for-your-storsimple-device"></a>Requisitos de rede para o seu dispositivo de StorSimple

Seu dispositivo StorSimple é um dispositivo bloqueado. No entanto, portas precisam ser abertas no firewall para permitir o tráfego de gerenciamento, nuvem e iSCSI. A tabela a seguir lista as portas que precisam ser abertas no firewall. Nesta tabela, *no* ou *entrada* se refere a direção da qual solicitações de entrada de cliente acessar seu dispositivo. *Check-out* ou *saída* se refere a direção em que o seu dispositivo StorSimple envia dados externamente, além da implantação: por exemplo, saído para a Internet.

| Porta n º<sup>1,2</sup> | Ou reduzir | Escopo de porta | Necessário | Anotações |
|------------------------|-----------|------------|----------|-------|
|TCP 80 (HTTP)<sup>3</sup>|  Check-out |  WAN | Não |<ul><li>Porta de saída é usada para acesso à Internet para recuperar atualizações.</li><li>O proxy da web de saída é configurável pelo usuário.</li><li>Para permitir atualizações de sistema, esta porta também deve estar aberta para que o controlador fixo IPs.</li></ul> |
|TCP 443 (HTTPS)<sup>3</sup>| Check-out | WAN | Sim |<ul><li>Porta de saída é usada para acessar dados na nuvem.</li><li>O proxy de saída web é configurável pelo usuário.</li><li>Para permitir atualizações de sistema, esta porta também deve estar aberta para que o controlador fixo IPs.</li><li>Esta porta também é usada nos dois controladores para coleta de lixo.</li></ul>|
|UDP 53 (DNS) | Check-out | WAN | Em alguns casos; Consulte anotações. |Esta porta é necessária apenas se você estiver usando um servidor DNS baseados na Internet. |
| UDP 123 (NTP) | Check-out | WAN | Em alguns casos; Consulte anotações. |Esta porta é necessária apenas se você estiver usando um servidor NTP baseados na Internet. |
| TCP 9354 | Check-out | WAN | Sim |A porta de saída é usada pelo dispositivo StorSimple para se comunicar com o serviço do Gerenciador de StorSimple. |
| 3260 (iSCSI) | Em | LAN | Não | Esta porta é utilizada para acessar dados sobre iSCSI.|
| 5985 | Em | LAN | Não | Porta de entrada é usada pelo Gerenciador de instantâneo StorSimple para se comunicar com o dispositivo StorSimple.<br>Esta porta também é usada quando você se conectar remotamente ao Windows PowerShell para StorSimple sobre HTTP. |
| 5986 | Em | LAN | Não | Esta porta é usada quando você se conectar remotamente ao Windows PowerShell para StorSimple por HTTPS. |

<sup>1</sup> não portas de entrada precisam ser aberto na Internet pública.

<sup>2</sup> se várias portas realizar uma configuração de gateway, a ordem de saída tráfego roteado será determinada com base na ordem de roteamento porta descrito no [roteamento de porta](#routing-metric), abaixo.

<sup>3</sup> o controlador fixado IPs em seu dispositivo de StorSimple deve ser roteáveis e possa se conectar à Internet. Os endereços IP fixos são usados para manutenção as atualizações para o dispositivo. Se os controladores de dispositivo não conseguem se conectar na Internet por meio de IPs fixo, não será capaz de atualizar o seu dispositivo StorSimple.

> [AZURE.IMPORTANT] Certifique-se de que o firewall não modifiquem ou descriptografar qualquer tráfego SSL entre o dispositivo de StorSimple e Azure.

### <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede geralmente podem configurar regras de firewall avançado com base em padrões de URL para filtrar a entrada e o tráfego de saída. Seu dispositivo de StorSimple e o serviço do Gerenciador de StorSimple dependem de outros aplicativos da Microsoft como o barramento de serviço do Azure, controle de acesso do Azure Active Directory, contas de armazenamento e servidores Microsoft Update. Padrões de URL associados a esses aplicativos podem ser usados para configurar regras de firewall. É importante entender o que podem alterar os padrões de URL associados a esses aplicativos. Isso por sua vez exigirá o administrador de rede monitorar e atualizar regras de firewall para sua StorSimple como e quando necessário.

Recomendamos que você defina suas regras de firewall para tráfego de saída, com base em StorSimple fixo endereços IP, livremente na maioria dos casos. No entanto, você pode usar as informações abaixo para definir regras de firewall avançado que são necessárias para criar ambientes seguros.

> [AZURE.NOTE] O dispositivo (origem) IPs sempre deve ser definido para todas as interfaces de rede habilitado. O destino que IPS deve ser definida como [intervalos de IP do data center Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).

#### <a name="url-patterns-for-azure-portal"></a>Padrões de URL para o portal do Azure
| Padrão de URL                                                      | Funcionalidade do componente                                           | IPs de dispositivo                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | Serviço Gerenciador de StorSimple<br>Serviço de controle de acesso<br>Barramento de serviço Azure| Interfaces de rede habilitados para a nuvem        |
|`https://*.backup.windowsazure.com`|Registro de dispositivo| Somente dados 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Revogação de certificado |Interfaces de rede habilitados para a nuvem |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Contas de armazenamento do Azure e monitoramento | Interfaces de rede habilitados para a nuvem        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Servidores Microsoft Update<br>                             | Controlador fixo IPs somente               |
| `http://*.deploy.akamaitechnologies.com`                         |Akamai CDN |Controlador fixo IPs somente   |
| `https://*.partners.extranet.microsoft.com/*`                    | Pacote de suporte                                                  | Interfaces de rede habilitados para a nuvem        |

#### <a name="url-patterns-for-azure-government-portal"></a>Padrões de URL para o portal do governo do Azure
| Padrão de URL                                                      | Funcionalidade do componente                                           | IPs de dispositivo                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`   | Serviço Gerenciador de StorSimple<br>Serviço de controle de acesso<br>Barramento de serviço Azure| Interfaces de rede habilitados para a nuvem        |
|`https://*.backup.windowsazure.us`|Registro de dispositivo| Somente dados 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Revogação de certificado |Interfaces de rede habilitados para a nuvem |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Contas de armazenamento do Azure e monitoramento | Interfaces de rede habilitados para a nuvem        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Servidores Microsoft Update<br>                             | Controlador fixo IPs somente               |
| `http://*.deploy.akamaitechnologies.com`                         |Akamai CDN |Controlador fixo IPs somente   |
| `https://*.partners.extranet.microsoft.com/*`                    | Pacote de suporte                                                  | Interfaces de rede habilitados para a nuvem        |

### <a name="routing-metric"></a>Métrica de roteamento

Uma métrica de roteamento está associada com as interfaces e o gateway que rotear os dados para redes especificadas. Métrica de roteamento é usada pelo protocolo de roteamento para calcular o melhor caminho para um determinado destino, se ele aprende que existirem vários caminhos para o mesmo destino. Inferior a métrica de roteamento, maior será a preferência.

No contexto de StorSimple, se várias interfaces de rede e gateways estiverem configurados para tráfego de canal, a métrica de roteamento entram play para determinar a ordem relativa na qual as interfaces obter usadas. As métricas de roteamento não podem ser alteradas pelo usuário. No entanto, você pode usar o `Get-HcsRoutingTable` cmdlet para imprimir tabela de roteamento (e métricas) em seu dispositivo de StorSimple. Mais informações sobre o cmdlet Get-HcsRoutingTable na [implantação de solução de problemas StorSimple](storsimple-troubleshoot-deployment.md).

Os algoritmos métricos roteamento são diferentes dependendo da versão do software executado no seu dispositivo de StorSimple.

**Versões anteriores a 1 de atualização**

Isso inclui as versões de software anteriores a 1 de atualização como o GA, 0.1, 0.2 ou 0,3 lançamento. A ordem com base em métricas de roteamento é da seguinte maneira:

   *Última configurado interface de rede 10 GbE > interface de rede de outros 10 GbE > última configurado 1 interface de rede GbE > interface de rede de outros GbE de 1*


**Versões começando em 1 de atualização e antes da atualização 2**

Isso inclui versões de software como 1, 1.1 ou 1.2. A ordem com base em métricas de roteamento é decidida da seguinte maneira:

   *DADOS 0 > última configurado interface de rede 10 GbE > interface de rede de outros 10 GbE > última configurado 1 interface de rede GbE > interface de rede de outros GbE de 1*

   Em Update 1, a métrica de roteamento de dados 0 é feita menor; Portanto, todo o tráfego de nuvem é roteado através de dados 0. Tome nota desse se houver mais de uma interface de rede habilitados para a nuvem em seu dispositivo de StorSimple.


**Versões iniciando da atualização 2**

Atualização 2 tem vários aperfeiçoamentos relacionados a rede e a métrica de roteamento foi alterado. O comportamento pode ser explicado da seguinte maneira.

- Um conjunto de valores predeterminados foram atribuídas a interfaces de rede.   

- Considere a possibilidade de uma tabela de exemplo mostrada abaixo, com valores atribuídos a várias interfaces rede quando eles estão habilitados para a nuvem ou nuvem desabilitada, mas com um gateway configurado. Observe os valores atribuídos aqui são somente valores de exemplo.


  	| Interface de rede | Habilitado para a nuvem | Nuvem desabilitada com gateway |
  	|-----|---------------|---------------------------|
  	| Dados 0  | 1            | -                        |
  	| Dados 1  | 2            | 20                       |
  	| Dados 2  | 3            | 30                       |
  	| Dados 3  | 4            | 40                       |
  	| Dados 4  | 5            | 50                       |
  	| Dados 5  | 6            | 60                       |


- A ordem na qual o tráfego de nuvem será roteado por meio de interfaces de rede é:

    *Dados 0 > dados 1 > Data 2 > dados 3 > dados 4 > dados 5*

    Isso pode ser explicado pelo exemplo a seguir.

    Considere a possibilidade de um dispositivo StorSimple com duas interfaces de rede habilitados para a nuvem, dados 0 e 5 de dados. Dados de 1 a 4 de dados são nuvem desabilitada mas tem um gateway configurado. A ordem na qual o tráfego será roteado para esse dispositivo será:

    *Dados 0 (1) > dados 5 (6) > dados 1 (20) > dados 2 (30) > dados 3 (40) > dados 4 (50)*

    *onde os números entre parênteses indicam as respectivas métricas de roteamento.*

    Se dados 0 falhar, o tráfego de nuvem é roteado por meio de dados 5. Considerando que um gateway é configurado em todos os outro rede, se dados 0 e 5 de dados falhar, o tráfego de nuvem irão por meio de dados 1.


- Se uma interface de rede habilitados para a nuvem falhar, então serão 3 tentativas com um atraso de segunda 30 para conectar-se para a interface. Se todas as tentativas falharem, o tráfego é roteado para o próximo habilitados para a nuvem interface disponível conforme determinado pela tabela de roteamento. Se todas as rede habilitados para a nuvem interfaces falhas, o dispositivo falhará sobre para o outro controlador (nenhuma reinicialização nesse caso).

- Se houver uma falha de VIP para uma interface de rede iSCSI habilitada, haverá 3 tentativas com um atraso de 2 segundos. Esse comportamento tem ficado a mesma relação às versões anteriores. Se todas as interfaces de rede iSCSI falharem, um failover de controlador ocorrerá (acompanha por uma reinicialização).


- Um alerta também é gerado em seu dispositivo de StorSimple quando houver uma falha de VIP. Para obter mais informações, vá para [referência rápida do alerta](storsimple-manage-alerts.md).

- Em termos de tentativas, iSCSI terá precedência sobre nuvem.

    Considere o seguinte exemplo: StorSimple de um dispositivo tem duas interfaces de rede habilitadas, dados 0 e 1 de dados. Dados 0 são habilitado para a nuvem enquanto dados 1 é ambas as nuvem e habilitados para iSCSI. Não há outras interfaces de rede neste dispositivo estão habilitados para iSCSI ou nuvem.

    Se falhar de dados 1, deu a ela for a interface de rede iSCSI última, isso resultará em um failover de controlador a dados 1 no outro controlador.


### <a name="networking-best-practices"></a>Práticas recomendadas de rede

Além dos requisitos de rede acima, para obter o desempenho ideal da sua solução de StorSimple, atender as seguintes práticas recomendadas:

- Certifique-se de que seu dispositivo de StorSimple tem uma largura de banda 40 por Mbps dedicado (ou mais) disponível em todos os momentos. Essa largura de banda não deve ser compartilhada (ou alocação deve ser garantia por meio do uso diretivas QoS) com outros aplicativos.

- Certifique-se de que a conectividade de rede com a Internet está disponível em todos os momentos. Conexões de Internet esporádicas ou não confiáveis para os dispositivos, incluindo sem conectividade com a Internet qualquer, resultará em uma configuração sem suporte.

- Isole o tráfego iSCSI e nuvem por tendo dedicada interfaces de rede em seu dispositivo para acesso iSCSI e nuvem. Para obter mais informações, consulte como [Modificar interfaces de rede](storsimple-modify-device-config.md#modify-network-interfaces) em seu dispositivo de StorSimple.

- Não use uma configuração de protocolo de controle de agregação de Link (LACP) interfaces de rede. Esta é uma configuração sem suporte.


## <a name="high-availability-requirements-for-storsimple"></a>Requisitos de alta disponibilidade para StorSimple

A plataforma de hardware que acompanha a solução StorSimple possui recursos de disponibilidade e confiabilidade que fornecem uma base para uma infraestrutura de armazenamento tolerância altamente disponível em seu data center. No entanto, há requisitos e práticas recomendadas que deve cumprir para ajudar a garantir a disponibilidade de sua solução de StorSimple. Antes de implantar StorSimple, examine com atenção os seguintes requisitos e práticas recomendadas para o dispositivo de StorSimple e computadores de host conectado.

Para obter mais informações sobre como monitorar e manter os componentes de hardware do dispositivo StorSimple, vá para [usar o serviço de Gerenciador de StorSimple para monitorar o status e componentes de hardware](storsimple-monitor-hardware-status.md) e [substituição de componentes de hardware StorSimple](storsimple-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Requisitos de alta disponibilidade e procedimentos para seu dispositivo de StorSimple

Examine as informações a seguir com atenção para assegurar a alta disponibilidade do seu dispositivo de StorSimple.

#### <a name="pcms"></a>PCMs

Dispositivos de StorSimple incluem alimentação redundantes, trocados e refrigeração módulos (PCMs). Cada PCM tem capacidade suficiente para fornecer serviço para o gabinete inteiro. Para garantir alta disponibilidade, os dois PCMs devem ser instalados.

- Conecte seu PCMs a fontes de energia diferente para fornecer disponibilidade se uma fonte de alimentação falhar.
- Se um PCM falhar, solicite uma substituição imediatamente.
- Remova uma falha PCM somente quando você tiver a substituição e estiver pronto para instalá-lo.
- Não remova os dois PCMs simultaneamente. O módulo PCM inclui o módulo de bateria de backup. A remoção de ambas as PCMs resultará em um desligamento sem proteção de bateria e o estado do dispositivo não serão salvas. Para obter mais informações sobre a bateria, vá para [Manter o módulo de bateria de backup](storsimple-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Módulos do controlador

Dispositivos StorSimple incluem controlador redundantes trocados módulos. Os módulos do controlador funcionam de maneira ativo/passivo. A qualquer momento, um módulo de controlador está ativo e está fornecendo serviço, enquanto o outro módulo de controlador é passivo. O módulo do controlador passivo está ligado e se torna operacional se o módulo active controlador falha ou for removido. Cada módulo de controlador tem capacidade suficiente para fornecer serviço para o gabinete inteiro. Ambos os módulos do controlador devem ser instalados para garantir alta disponibilidade.

- Certifique-se de que ambos os módulos controlador estão instalados em todos os momentos.

- Se um módulo de controlador falhar, solicite uma substituição imediatamente.

- Remova um módulo de controlador com falha somente quando você tiver a substituição e estiver pronto para instalá-lo. Removendo um módulo de longos períodos afetarão o fluxo de ar e, portanto, a refrigeração do sistema.

- Certifique-se de que as conexões de rede para ambos os módulos do controlador são idênticas, e as interfaces de rede conectada tem uma configuração de rede idênticas.

- Se um módulo de controlador falha ou precisa de substituição, verifique se o outro módulo de controlador está em um estado ativo antes de substituir o módulo de controlador com falha. Para verificar se um controlador está ativo, vá para [identificar o controlador ativo em seu dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Não remova ambos os módulos do controlador ao mesmo tempo. Se um failover controlador estiver em andamento, não encerrar o módulo do controlador reserva ou removê-lo do gabinete.

- Após um failover de controlador, aguarde pelo menos cinco minutos antes de remover o módulo do controlador.

#### <a name="network-interfaces"></a>Interfaces de rede

StorSimple dispositivo controlador módulos cada têm quatro 1 Gigabit e duas 10 interfaces de rede de Gigabit Ethernet.

- Certifique-se de que as conexões de rede para ambos os módulos do controlador são idênticas e interfaces de rede que as interfaces de módulo do controlador estão conectadas para ter uma configuração de rede idênticas.

- Quando possível, implante conexões de rede entre diferentes opções para garantir a disponibilidade de serviço em caso de uma falha de dispositivo de rede.

- Ao desconectar a única ou a última interface restante habilitados para iSCSI (com IPs atribuídas), desabilitar a interface primeiro e, em seguida, desconecte os cabos. Se a interface for desconectada pela primeira vez, ele causará o controlador de ativo para alternar para o controlador de passivo. Se o controlador de passivo também tiver suas interfaces correspondentes desconectados, os dois controladores de irá reinicializar várias vezes antes de liquidar em um controlador.

- Conectar-se pelo menos duas interfaces de dados à rede de cada módulo de controlador.

- Se você tiver ativado as duas interfaces de 10 GbE, implantar aqueles por diferentes opções.

- Quando possível, use MPIO em servidores para garantir que os servidores podem suportar um link, rede ou falha de interface.

Para obter mais informações sobre a rede seu dispositivo para alta disponibilidade e desempenho, vá para [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSDs e unidades de disco rígido

Dispositivos de StorSimple incluem disco de estado sólido (SSDs) e unidades de disco rígido (unidades de disco rígido) protegidos usando espelhados espaços. Uso de espaços espelhados garante que o dispositivo é capaz de suportar a falha de um ou mais SSDs ou unidades de disco rígido.

- Certifique-se de que todos os módulos SSD e unidade de disco rígido estão instalados.

- Se um SSD ou unidade de disco rígido falhar, solicite uma substituição imediatamente.

- Se um SSD ou unidade de disco rígido falha ou requer substituição, certifique-se de que você remova apenas o SSD ou unidade de disco rígido que requer a substituição.

- Não remova mais de um SSD ou unidade de disco rígido do sistema em qualquer ponto no tempo.
Uma falha de 2 ou mais discos de determinado tipo (disco rígido, SSD) ou consecutivas em um período curto pode resultar em perda de dados de mau funcionamento e possíveis de sistema. Se isso acontecer, [entre em contato com o suporte da Microsoft](storsimple-contact-microsoft-support.md) para obter assistência.

- Durante a substituição, monitore o **Status de Hardware** na página de **manutenção** para as unidades no SSDs e unidades de disco rígido. Um status de seleção verde indica que os discos são íntegra ou Okey, enquanto o ponto de exclamação uma vermelha indica um falha SSD ou unidade de disco rígido.

- Recomendamos que você configure instantâneos de nuvem para todos os volumes que você precisa proteger no caso de uma falha do sistema.

#### <a name="ebod-enclosure"></a>Compartimento EBOD

Modelo de dispositivo de StorSimple 8600 inclui uma circunscrição estendido um monte de discos (EBOD) além do compartimento principal. Um EBOD contém EBOD controladores e unidades de disco rígido (unidades de disco rígido) protegidos usando espelhados espaços. Uso de espaços espelhados garante que o dispositivo é capaz de suportar a falha de um ou mais HDDs. Compartimento EBOD está conectado ao compartimento primário por meio de cabos SAS redundantes.

- Verifique se os dois módulos de controlador de compartimento EBOD, tanto cabos SAS e todas as unidades de disco rígido estão instaladas em todos os momentos.

- Se um módulo de controlador de compartimento EBOD falhar, solicite uma substituição imediatamente.

- Se um módulo de controlador de compartimento EBOD falhar, certifique-se de que o outro módulo de controlador está ativo antes de substituir o módulo falhou. Para verificar se um controlador está ativo, vá para [identificar o controlador ativo em seu dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Durante uma substituição de módulo do controlador EBOD, monitorar continuamente o status do componente no serviço do Gerenciador de StorSimple acessando **manutenção** > **status de Hardware**.

- Se um cabo SAS falha ou requer substituição (Microsoft Support devem ser envolvido para tomar uma decisão tal), certifique-se de que você remova somente o cabo SAS que requer a substituição.

- Não simultaneamente remova os cabos SAS do sistema em qualquer ponto no tempo.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Recomendações de alta disponibilidade para seus computadores de host

Revise cuidadosamente estas práticas recomendadas para garantir a alta disponibilidade de hosts conectados ao seu dispositivo StorSimple.

- Configurar StorSimple com [configurações de cluster de servidor de arquivos de dois nós][1]. Removendo pontos únicos de falha e construindo no redundância no lado do host, a solução inteira fica altamente disponível.

- Use disponíveis continuamente compartilhamentos de (CA) disponível com o Windows Server 2012 (SMB 3.0) para alta disponibilidade durante failover dos controladores de armazenamento. Para obter informações adicionais para a configuração de clusters de servidor de arquivo e compartilhamentos continuamente disponíveis com o Windows Server 2012, consulte esta [demonstração em vídeo](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre limites de sistema StorSimple](storsimple-limits.md).
- [Saiba como implantar sua solução de StorSimple](storsimple-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
