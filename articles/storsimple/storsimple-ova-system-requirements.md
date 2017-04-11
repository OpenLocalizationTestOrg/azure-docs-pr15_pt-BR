<properties
   pageTitle="Requisitos de sistema do Array Virtual StorSimple"
   description="Saiba mais sobre o software e os requisitos de rede para o conjunto de Virtual StorSimple"
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
   ms.workload="NA"
   ms.date="10/17/2016"
   ms.author="alkohli"/>

# <a name="storsimple-virtual-array-system-requirements"></a>Requisitos de sistema do Array Virtual StorSimple

## <a name="overview"></a>Visão geral

Este artigo descreve requisitos de sistema importantes para sua matriz Virtual do Microsoft Azure StorSimple (também conhecido como StorSimple local virtual ou dispositivo virtual StorSimple) e para os clientes de armazenamento acessando a matriz. Recomendamos que você examine as informações cuidadosamente antes de implantar o seu sistema de StorSimple e, em seguida, voltar a ele conforme necessário durante a implantação e a operação subsequente.

Os requisitos de sistema incluem:

-   **Requisitos de software para clientes de armazenamento** - descreve as plataformas de virtualização suportados, navegadores da web, clientes iniciadores iSCSI, SMB, os requisitos mínimos de dispositivo virtual e quaisquer requisitos adicionais para esses sistemas operacionais.

-   **Requisitos para o dispositivo de StorSimple de rede** - fornece informações sobre as portas que precisam ser abertas no firewall para permitir o tráfego de gerenciamento, nuvem ou iSCSI.

As informações de requisitos de sistema StorSimple publicadas neste artigo se aplica somente a StorSimple Virtual matrizes.

- Para dispositivos de 8000 série, vá para [requisitos de sistema para o seu dispositivo de série 8000 StorSimple](storsimple-system-requirements.md).
 
- Para dispositivos de série 7000, vá para [requisitos de sistema para o seu dispositivo de série de 5000-7000 StorSimple](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).


## <a name="software-requirements"></a>Requisitos de software

Os requisitos de software incluem as informações sobre os navegadores da web com suporte, versões SMB, plataformas de virtualização e os requisitos mínimos de dispositivo virtual.

### <a name="supported-virtualization-platforms"></a>Plataformas de virtualização suportados


| **Hipervisor** | **Versão**                          |
|----------------|--------------------------------------|
| Hyper-V        | Windows Server 2008 R2 SP1 e posterior |
| VMware ESXi    | 5,5 e posterior                        |

### <a name="virtual-device-requirements"></a>Requisitos de dispositivo virtual

| **Componente**                                | **Requisito**            |
|----------------------------------------------|----------------------------|
| Número mínimo de processadores virtuais (cores) | 4                          |
| Mínimo de memória RAM)                         | 8 GB                       |
| Espaço de disco<sup>1</sup>                       | Disco do sistema operacional - 80 GB <br></br>Disco de dados - 500 GB a 8 TB|
| Número mínimo de interfaces de rede       | 1                          |
| Mínimo de largura de banda de Internet<sup>2</sup>       | 5 Mbps                     |

<sup>1</sup> - provisionado thin

<sup>2</sup> - requisitos de rede podem variar dependendo da taxa de alteração de dados diária. Por exemplo, se precisar de um dispositivo de backup de 10 GB ou mais alterações durante um dia, o backup diário ao longo de uma conexão de Mbps 5 pode levar até 4,25 horas (se os dados não podem ser compactados ou eliminação de duplicação).

### <a name="supported-web-browsers"></a>Navegadores da web com suporte

| **Componente**     | **Versão** | **Requisitos adicionais/anotações** |
|-------------------|-----------------|-----------------------------------|
| Microsoft Edge    | Versão mais recente  |                                   |
| Internet Explorer | Versão mais recente  | Testado com o Internet Explorer 11  |
| Google Chrome     | Versão mais recente  | Testado com Chrome 46             |

### <a name="supported-storage-clients"></a>Clientes de armazenamento com suporte 

Os seguintes requisitos de software são para os iniciadores iSCSI que acessar seu Array Virtual StorSimple (configurados como um servidor iSCSI).

| **Sistemas operacionais compatíveis** | **Versão necessária** | **Requisitos adicionais/anotações** |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008R2 SP1, 2012, 2012R2 |StorSimple pode criar provisionamento thin e totalmente provisionado volumes. Ele não é possível criar volumes parcialmente provisionados. Volumes de iSCSI StorSimple têm suporte apenas para: <ul><li>Volumes simples em discos básicos do Windows.</li><li>Para formatar um volume do Windows NTFS.</li>|


Os seguintes requisitos de software são para os clientes SMB que acessar seu Array Virtual StorSimple (configurado como um servidor de arquivo).

| **Versão SMB** |
|-------------|
| SMB 2. x     |
| SMB 3.0     |
| SMB 3.02    |

> [AZURE.IMPORTANT] Não copie ou armazenar arquivos protegidos pelo Windows arquivo EFS (sistema) ao servidor de arquivos Array Virtual StorSimple; Isso resultará em uma configuração sem suporte. 

## <a name="networking-requirements"></a>Requisitos de rede 

A tabela a seguir lista as portas que precisam ser abertas no firewall para permitir o tráfego de gerenciamento, SMB, nuvem ou iSCSI. Nesta tabela, *no* ou *entrada* se refere a direção da qual solicitações de entrada de cliente acessar seu dispositivo. *Check-out* ou *saída* se refere a direção em que o seu dispositivo StorSimple envia dados externamente, além da implantação: por exemplo, saído para a Internet.

| **Porta n º<sup>1</sup>** | **Ou reduzir** | **Escopo de porta** | **Necessário**              | **Anotações**                                                                                                            |
|--------------------------|---------------|----------------|---------------------------|----------------------------------------------------------------------------------------------------------------------|
| TCP 80 (HTTP)            | Check-out           | WAN            | Não                        | Porta de saída é usada para acesso à Internet para recuperar atualizações. <br></br>O proxy da web de saída é configurável pelo usuário. |
| TCP 443 (HTTPS)          | Check-out           | WAN            | Sim                       | Porta de saída é usada para acessar dados na nuvem. <br></br>O proxy da web de saída é configurável pelo usuário. |
| UDP 53 (DNS)             | Check-out           | WAN            | Em alguns casos; Consulte anotações. | Esta porta é necessária apenas se você estiver usando um servidor DNS baseados na Internet. <br></br> **Observação**: se Implantando um servidor de arquivos, recomendamos o uso do servidor DNS local.|
| UDP 123 (NTP)            | Check-out           | WAN            | Em alguns casos; Consulte anotações. | Esta porta é necessária apenas se você estiver usando um servidor NTP baseados na Internet.<br></br> **Observação:** Se Implantando um servidor de arquivos, é recomendável sincronizar a hora com os controladores de domínio do Active Directory.  |
| TCP 80 (HTTP)           | Em            | LAN            | Sim                       | Esta é a porta de entrada para a interface de usuário local no dispositivo StorSimple gerenciamento local. <br></br> **Observação**: acessando a interface do usuário local sobre HTTP redirecionará automaticamente para HTTPS.|
| TCP 443 (HTTPS)          | Em            | LAN            | Sim                       | Esta é a porta de entrada para a interface de usuário local no dispositivo StorSimple gerenciamento local.|
| TCP 3260 (iSCSI)         | Em            | LAN            | Não                        | Esta porta é utilizada para acessar dados sobre iSCSI.|

<sup>1</sup> não portas de entrada precisam ser aberto na Internet pública.

> [AZURE.IMPORTANT] Certifique-se de que o firewall não modifiquem ou descriptografar qualquer tráfego SSL entre o dispositivo de StorSimple e Azure.


### <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall 

Os administradores de rede geralmente podem configurar regras de firewall avançado com base em padrões de URL para filtrar a entrada e o tráfego de saída. Seu array virtual e o serviço do Gerenciador de StorSimple dependem de outros aplicativos da Microsoft como o barramento de serviço do Azure, controle de acesso do Azure Active Directory, contas de armazenamento e servidores Microsoft Update. Padrões de URL associados a esses aplicativos podem ser usados para configurar regras de firewall. É importante entender o que podem alterar os padrões de URL associados a esses aplicativos. Isso por sua vez exigirá o administrador de rede monitorar e atualizar regras de firewall para sua StorSimple como e quando necessário. 

Recomendamos que você defina suas regras de firewall para tráfego de saída, com base em StorSimple fixo endereços IP, livremente na maioria dos casos. No entanto, você pode usar as informações abaixo para definir regras de firewall avançado que são necessárias para criar ambientes seguros.

> [AZURE.NOTE] 
> 
> - O dispositivo (origem) IPs sempre deve ser definido para todas as interfaces de rede habilitados para a nuvem. 
> - O destino que IPS deve ser definida como [intervalos de IP do data center Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


| Padrão de URL                                                      | Funcionalidade do componente                                           |
|------------------------------------------------------------------|---------------------------------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | Serviço Gerenciador de StorSimple<br>Serviço de controle de acesso<br>Barramento de serviço Azure|
|`http://*.backup.windowsazure.com`|Registro de dispositivo|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Revogação de certificado |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Contas de armazenamento do Azure e monitoramento |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Servidores Microsoft Update<br>                        |
| `http://*.deploy.akamaitechnologies.com`                         |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*`                    | Pacote de suporte                                                  |
| `http://*.data.microsoft.com `                   | Serviço de telemetria no Windows, consulte a [atualização para a experiência do cliente e telemetria diagnóstico](https://support.microsoft.com/en-us/kb/3068708)                                                  |

## <a name="next-step"></a>Próxima etapa

-   [Preparar o portal para implantar sua matriz Virtual StorSimple](storsimple-ova-deploy1-portal-prep.md)
