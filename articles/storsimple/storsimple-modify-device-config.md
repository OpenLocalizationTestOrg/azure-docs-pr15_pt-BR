<properties 
   pageTitle="Modificar a configuração de dispositivo de StorSimple | Microsoft Azure" 
   description="Descreve como usar o serviço do Gerenciador de StorSimple para reconfigurar um dispositivo StorSimple que já tenha sido implantado." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="09/29/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-modify-your-storsimple-device-configuration"></a>Usar o serviço do Gerenciador de StorSimple para modificar a configuração de dispositivo de StorSimple

## <a name="overview"></a>Visão geral 

O portal de clássico Azure página **Configure** contém todos os parâmetros de dispositivo que você pode reconfigurar em um dispositivo de StorSimple que é gerenciado por um serviço Gerenciador de StorSimple. Este tutorial explica como você pode usar a página **Configurar** para executar as seguintes tarefas de nível de dispositivo:

- Modificar as configurações de dispositivo 
- Modificar as configurações de tempo 
- Modificar as configurações de DNS 
- Modificar interfaces de rede
- Troque ou reatribuir IPs

## <a name="modify-device-settings"></a>Modificar as configurações de dispositivo

As configurações de dispositivo incluem o nome amigável do dispositivo e a descrição do dispositivo.

Um dispositivo StorSimple que está conectado ao serviço Gerenciador de StorSimple é atribuído um nome padrão. O nome padrão normalmente reflete o número de série do dispositivo. Por exemplo, um nome de dispositivo padrão que é 15 caracteres, como 8600-SHX0991003G44HT, indica o seguinte:

- **8600** – indica o modelo do dispositivo.
- **SHX** – indica o site de fabricação.
- **0991003** - indica um produto específico.
- **G44HT**- os últimos 5 dígitos são incrementados para criar números de série exclusivos. Isso pode não ser um conjunto sequencial.

Você pode usar o portal de clássico Azure para alterar o nome do dispositivo e atribuir um nome amigável exclusivo de sua escolha. O nome amigável pode conter quaisquer caracteres e pode ter no máximo 64 caracteres.

Você também pode especificar uma descrição do dispositivo. Descrição de um dispositivo geralmente ajuda a identificar o proprietário e a localização física do dispositivo. O campo Descrição deve conter menos de 256 caracteres.
 
## <a name="modify-time-settings"></a>Modificar as configurações de tempo

Seu dispositivo deve sincronizar o tempo para autenticar com seu provedor de serviço de armazenamento de nuvem. Selecione seu fuso horário na lista suspensa e especifique até dois servidores de protocolo NTP (Network Time). O servidor NTP primário é necessário e é especificado quando você usa o Windows PowerShell para StorSimple para configurar seu dispositivo. Você pode especificar o Windows Server do padrão **time.windows.com** como seu servidor NTP. Você pode exibir a configuração do servidor NTP primária por meio do portal de clássico Azure, mas você deve usar a interface do Windows PowerShell para alterá-lo.

A configuração do servidor NTP secundária é opcional. Você pode usar o portal clássico para configurar um servidor NTP secundário. 

Ao configurar o servidor NTP, certifique-se de que sua rede permite o tráfego NTP passar do data center à Internet. Ao especificar um servidor NTP público, você deve garantir que seus firewalls de rede e outros dispositivos de segurança estão configurados para permitir NTP tráfego de e para a rede externa. Se o tráfego NTP bidirecional não é permitido, você deve usar um servidor NTP interno (um controlador de domínio do Windows fornece esta função). Se seu dispositivo não é possível sincronizar o tempo, ela não poderá se comunicar com seu provedor de armazenamento de nuvem.

Para ver uma lista de servidores NTP públicos, acesse [NTP servidores Web](http://support.ntp.org/bin/view/Servers/WebHome). 

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>O que acontece se o dispositivo for implantado em um fuso horário diferente?

Se o dispositivo for implantado em um fuso horário diferente, o fuso horário dispositivo será alterado. Considerando que todas as políticas de backup usam o fuso horário de dispositivo, as políticas de backup ajustará automaticamente de acordo com o novo fuso horário. Nenhuma intervenção do usuário é necessária.

## <a name="modify-dns-settings"></a>Modificar as configurações de DNS

Um servidor DNS é usado quando seu dispositivo tenta se comunicar com seu provedor de serviço de armazenamento de nuvem. Para alta disponibilidade, você precisa configurar o principal e os servidores DNS secundários durante a implantação inicial do dispositivo. Para reconfigurar o servidor DNS primário, você precisará usar a interface do Windows PowerShell em seu dispositivo de StorSimple.

Para modificar o servidor DNS secundário, você pode usar o portal de clássico Azure.



## <a name="modify-network-interfaces"></a>Modificar interfaces de rede

Seu dispositivo tem seis interfaces de rede de dispositivo, quatro das quais são 1 GbE e dois dos quais são 10 GbE. Essas interfaces são rotuladas como dados 0 – 5 de dados. DADOS 0, 1 de dados, dados 4 e 5 de dados são 1 GbE, enquanto dados 2 e 3 de dados são 10 interfaces de rede GbE.

Configure **As configurações de Interface de rede** para cada uma das interfaces a ser usado. Para garantir alta disponibilidade, recomendamos que você tenha pelo menos duas interfaces de iSCSI e duas interfaces habilitados para a nuvem em seu dispositivo. Nós recomendamos, mas não exigem que as interfaces não utilizadas ser desabilitado.

Quando você configura qualquer uma das interfaces de rede, você deve configurar um IP virtual (VIP).

DADOS 0 são habilitado para a nuvem por padrão. Ao configurar dados 0, você também precisa configurar dois endereços IP fixos, um para cada controlador. Esses endereços IP fixos podem ser usados para acessar os controladores de dispositivo diretamente e são úteis quando você instalar atualizações no dispositivo ou quando você acessa os controladores para solucionar este problema.

StorSimple 8000 série Update 1, a métrica de roteamento de dados 0 está definida para o menor; Portanto, se seu dispositivo está executando StorSimple 8000 série Update 1, todo o tráfego de nuvem será roteado por meio de dados 0. Tome nota desse se você tiver mais de uma interface de rede habilitados para a nuvem em seu dispositivo de StorSimple.

>[AZURE.NOTE] Os endereços IP fixos para o controlador são usados para manutenção as atualizações para o dispositivo. Portanto, os endereços de IP fixo devem ser roteáveis e possa se conectar à Internet.

Para cada interface de rede, os seguintes parâmetros são exibidos:

- **Velocidade** – não é um parâmetro configurável pelo usuário. DADOS 0, 1 de dados, dados 4 e 5 de dados são sempre 1 GbE, enquanto dados 2 e 3 de dados são 10 interfaces GbE.

     >[AZURE.NOTE] Velocidade e duplex são sempre autonegociação. Quadros jumbo não são suportados.
 
- **Estado de interface** – uma interface pode ser habilitada ou desabilitada. Se habilitada, o dispositivo tentará usar a interface. Recomendamos que apenas essas interfaces que estão conectados à rede e usados esteja ativado. Desabilite quaisquer interfaces que você não estiver usando.

- **Tipo de interface** – este parâmetro permite isolar tráfego iSCSI do tráfego de armazenamento de nuvem. Esse parâmetro pode ser um destes procedimentos:

    - **Nuvem habilitado** – quando habilitada, o dispositivo usará essa interface para se comunicar com a nuvem.
    - **iSCSI habilitado** – quando habilitada, o dispositivo usará essa interface para se comunicar com o host iSCSI.

    Recomendamos que você isolar tráfego iSCSI do tráfego de armazenamento de nuvem. Observe também que se seu host estiver dentro da mesma sub-rede como seu dispositivo, você não precisa atribuir um gateway; No entanto, se o host estiver em uma sub-rede diferente seu dispositivo, você precisará atribuir um gateway.

- **Endereço IP** – isso pode ser IPv4 ou IPv6 ou ambos. Famílias de endereços IPv4 e IPv6 são suportadas para as interfaces de rede do dispositivo. Ao usar IPv4, especifique um endereço IP de 32 bits (*xxx.xxx*) em notação de ponto decimal. Ao usar o IPv6, simplesmente forneça um prefixo de 4 dígitos e um endereço de 128 bits será gerado automaticamente para a sua interface de rede do dispositivo com base em que o prefixo.

- **Sub-rede** – isso se refere a máscara de sub-rede e está configurado por meio da interface do Windows PowerShell.

- **Gateway** – este é o gateway padrão que deve ser usado por esta interface quando tenta se comunicar conosco que não estão dentro do mesmo espaço de endereço IP (sub-rede). O gateway padrão deve estar no mesmo espaço de endereço (sub-rede) como a interface endereço IP, conforme determinado pela máscara de sub-rede.

- **Endereço IP fixo** – este campo está disponível somente enquanto você configure os dados 0 interface. Para operações como atualizações ou o dispositivo de solução de problemas, talvez você precise conectar diretamente ao controlador de dispositivo. O endereço IP fixo pode ser utilizado para acessar o ativo e o controlador passivo em seu dispositivo.

Você pode reconfigurar o controlador 0 e 1 de controlador por meio do portal de clássico Azure.

>[AZURE.NOTE] 
>
>- Para garantir a operação correta, verifique se a velocidade da interface e o duplex no switch que cada interface de dispositivo está conectado ao. Alternar interfaces ou deve negocie com ou ser configuradas para Gigabit Ethernet (1000 Mbps) e ser full duplex. Interfaces operacional em velocidades ou em half-duplex resultará em problemas de desempenho.
>
>- Para minimizar as interrupções e tempo de inatividade, recomendamos que você habilite portfast em cada uma das portas do switch que se conectarem à interface de rede iSCSI do seu dispositivo. Isso garante que a conectividade de rede pode ser estabelecida rapidamente em caso de um failover.
 
## <a name="swap-or-reassign-ips"></a>Troque ou reatribuir IPs

Atualmente, se qualquer interface de rede no controlador é atribuído um VIP que está em uso (por mesmo dispositivo ou outro dispositivo da rede), o controlador falhará sobre. Portanto, você precisa siga o procedimento adequado se você está trocando VIPs para a interface de rede do dispositivo, pois você irá criar uma situação de IP duplicada.

Execute as seguintes etapas para trocar ou reatribuir VIPs qualquer uma das interfaces de rede:

#### <a name="to-reassign-ips"></a>Para reatribuir IPs

1. Desmarque o endereço IP para ambas as interfaces.

2. Depois que os endereços IP estão desmarcados, atribua os novos endereços IP a das respectivas interfaces.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Configurar MPIO para seu dispositivo de StorSimple](storsimple-configure-mpio-windows-server.md).

- Saiba como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
     
