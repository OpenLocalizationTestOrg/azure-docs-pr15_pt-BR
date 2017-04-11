<properties 
   pageTitle="Dispositivo virtual StorSimple atualização 2 | Microsoft Azure"
   description="Aprenda a criar, implantar e gerenciar um dispositivo virtual StorSimple em uma rede virtual do Microsoft Azure. (Se aplica a atualização de StorSimple 2)."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/23/2016"
   ms.author="alkohli" />

# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>Implantar e gerenciar um dispositivo virtual StorSimple no Azure


##<a name="overview"></a>Visão geral
O dispositivo virtual de série 8000 StorSimple é um recurso adicional que acompanha a sua solução Microsoft Azure StorSimple. O dispositivo virtual StorSimple é executado em uma máquina virtual em uma rede virtual do Microsoft Azure e você pode usá-lo para fazer backup e clonar dados de seus hosts. Este tutorial descreve como implantar e gerenciar um dispositivo virtual no Azure e é aplicável a todos os dispositivos virtuais executando a versão de software atualização 2 e inferior.


#### <a name="virtual-device-model-comparison"></a>Comparação de modelo de dispositivo virtual

O dispositivo virtual StorSimple está disponível em dois modelos, um 8010 padrão (conhecida anteriormente como 1100) e um premium 8020 (introduzido na atualização 2). Uma comparação dos dois modelos é tabulação abaixo.


| Modelo do dispositivo          | 8010<sup>1</sup>                                                                     | 8020                                                                                                                               |
|-----------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Capacidade máxima**      | 30 TB                                                                     | 64 TB                                                                                                                                |
| **Máquina virtual Azure**              | Standard_A3 (4 cores, 7 GB de memória)                                                                      | Standard_DS3 (4 cores, 14 GB de memória)                                                                                                                          |
| **Compatibilidade de versão** | Versões executando previamente atualização 2 ou posterior                                             | Versões executando atualização 2 ou posterior                                                                                                  |
| **Disponibilidade de região**   | Todas as regiões Azure                                                         | Azure regiões que oferecem suporte ao armazenamento de Premium<br></br>Para obter uma lista das regiões, veja [compatíveis regiões para 8020](#supported-regions-for-8020) |
| **Tipo de armazenamento**          | Usa o armazenamento padrão do Azure para discos locais<br></br> Saiba como [criar uma conta de armazenamento padrão]() | Usa o armazenamento do Azure Premium para discos locais<sup>2</sup> <br></br>Saiba como [criar uma conta de armazenamento Premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)                                                               |
| **Orientação de carga de trabalho**     | Recuperação de nível de item de arquivos de backups                                              | Teste cenários, baixa latência, cargas de trabalho maiores de desempenho e desenvolvimento em nuvem <br></br>Dispositivo secundário para recuperação de dados                                                                                            |
 
<sup>1</sup> *conhecido anteriormente como 1100*.

<sup>2</sup> *tanto o 8010 8020 usam armazenamento padrão do Azure para a camada de nuvem. A diferença só existe no nível local do dispositivo*.

#### <a name="supported-regions-for-8020"></a>Regiões com suporte para 8020

As regiões de armazenamento Premium que são suportadas atualmente para 8020 são tabulação abaixo. Esta lista será atualizada continuamente que Premium armazenamento fica disponível em regiões mais. 

| S. Não.                                                  | Há suporte para atualmente em regiões |
|---------------------------------------------------------|--------------------------------|
| 1                                                       | Centro dos EUA                     |
| 2                                                       |  Leste EUA                       |
| 3                                                       |  Leste dos EUA 2                     |
| 4                                                       | Oeste EUA                        |
| 5                                                       | Norte da Europa                   |
| 6                                                       | Europa Ocidental                    |
| 7                                                       | Sudeste Asiático                 |
| 8                                                       | Japão Leste                     |
| 9                                                       | Japão Oeste                     |
| 10                                                      | Austrália Oriental                 |
| 11                                                      | Austrália Sudeste *           |
| 12                                                      | Da Ásia Oriental *                     |
| 13                                                      | Sul Central EUA *              |

* Armazenamento de premium foi lançado recentemente nesses geos.

Este artigo descreve o processo passo a passo de implantação StorSimple virtual em um dispositivo Azure. Após ler este artigo, você irá:

- Compreenda como o dispositivo virtual difere do dispositivo físico.

- Poderá criar e configurar o dispositivo virtual.

- Conecte ao dispositivo virtual.

- Aprenda a trabalhar com o dispositivo virtual.

Este tutorial se aplica a todos os StorSimple virtuais dispositivos executando atualização 2 e superior. 

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>Como o dispositivo virtual difere do dispositivo físico

O dispositivo virtual StorSimple é uma versão somente de software do StorSimple que é executado em um único nó em uma máquina Virtual Microsoft Azure. O dispositivo virtual oferece suporte a cenários de recuperação de desastres no qual seu dispositivo físico não está disponível e é adequado para uso em recuperação de nível de item de backups, recuperação de dados locais e cenários de desenvolvimento e teste de nuvem.

#### <a name="differences-from-the-physical-device"></a>Diferenças do dispositivo físico

A tabela a seguir mostra algumas das principais diferenças entre o dispositivo virtual StorSimple e o dispositivo físico StorSimple.

|                             | Dispositivo físico                                          | Dispositivo virtual                                                                            |
|-----------------------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **Local**                    | Reside no data center.                               | É executado no Azure.                                                                            |
| **Interfaces de rede**          | Tem seis interfaces de rede: dados 0 a 5 de dados.                  | Tem somente uma interface de rede: dados 0.                                        |
| **Registro**                | Registrado durante a etapa de configuração.                | O registro é uma tarefa separada.                                                          |
| **Chave de criptografia de dados de serviço** | Gerar no dispositivo físico e atualize o dispositivo virtual com a nova chave.           | Não é possível gerar do dispositivo virtual. |


## <a name="prerequisites-for-the-virtual-device"></a>Pré-requisitos para o dispositivo virtual

As seções a seguir explicam os pré-requisitos de configuração para seu dispositivo virtual StorSimple. Antes de implantar um dispositivo virtual, examine as [Considerações de segurança para usar um dispositivo virtual](storsimple-security.md#storsimple-virtual-device-security).

#### <a name="azure-requirements"></a>Requisitos do Azure

Antes de você provisionar o dispositivo virtual, você precisa fazer os preparativos seguintes no seu ambiente do Azure:

- Para o dispositivo virtual, [configure uma rede virtual no Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Se usar o armazenamento de Premium, você deve criar uma rede virtual em uma região Azure que suporta o armazenamento de Premium. Mais informações sobre [regiões são aceitos atualmente para 8020](#supported-regions-for-8020).
- É recomendável usar o servidor DNS padrão fornecido pelo Azure em vez de especificar seu próprio nome de servidor DNS. Se o nome do seu servidor DNS não é válido ou se o servidor DNS não é capaz de resolver endereços IP corretamente, a criação do dispositivo virtual falhará.
- Ponto-a-site e to-site são opcional, mas não é necessária. Se desejar, você pode configurar essas opções para cenários mais avançados. 
- Você pode criar [máquinas virtuais do Azure](../virtual-machines/virtual-machines-linux-about.md) (servidores de host) na rede virtual que pode usar os volumes expostos pelo dispositivo virtual. Esses servidores devem atender aos seguintes requisitos:                            
    - Ser Windows ou Linux VMs com iSCSI software iniciador instalado.
    - Estar em execução na mesma rede virtual como o dispositivo virtual.
    - Se capaz de se conectar ao destino iSCSI do dispositivo virtual por meio do endereço IP interno do dispositivo virtual.

- Verifique se que você configurou o suporte para tráfego iSCSI e nuvem na mesma rede virtual.


#### <a name="storsimple-requirements"></a>Requisitos de StorSimple

Fazer as seguintes atualizações em seu serviço Azure StorSimple antes de criar um dispositivo virtual:


- Adicione [registros de controle de acesso](storsimple-manage-acrs.md) para as VMs que vai ser servidores de host de seu dispositivo virtual.

- Use uma [conta de armazenamento](storsimple-manage-storage-accounts.md#add-a-storage-account) na mesma região como o dispositivo virtual. Contas de armazenamento em diferentes regiões podem resultar em baixo desempenho. Você pode usar uma conta padrão ou Premium armazenamento com o dispositivo virtual. Obter mais informações sobre como criar uma [conta de armazenamento padrão]() ou uma [conta de armazenamento Premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)

- Use uma conta de armazenamento diferentes para a criação de dispositivo virtual daquele usado para os seus dados. Usando a mesma conta de armazenamento pode resultar em baixo desempenho.

Certifique-se de que você tem as seguintes informações antes de começar:

- Sua conta de portal clássica Azure com credenciais de acesso.

- Uma cópia da chave de criptografia de dados de serviço, do seu dispositivo físico.


## <a name="create-and-configure-the-virtual-device"></a>Criar e configurar o dispositivo virtual

Antes de executar estes procedimentos, certifique-se de que você atingiu os [pré-requisitos para o dispositivo virtual](#prerequisites-for-the-virtual-device). 

Depois que você tiver criado uma rede virtual, configurou um serviço de Gerenciador de StorSimple e registrado seu dispositivo StorSimple físico com o serviço, você pode usar as etapas a seguir para criar e configurar um dispositivo virtual StorSimple. 

### <a name="step-1-create-a-virtual-device"></a>Etapa 1: Criar um dispositivo virtual

Execute as seguintes etapas para criar o dispositivo virtual StorSimple.

[AZURE.INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

Se a criação do dispositivo virtual falhar nesta etapa, você pode não ter conectividade com a Internet. Para obter mais informações, vá para [Solucionar problemas de falhas de conectividade de Internet](#troubleshoot-internet-connectivity-errors) quando creatig um dispositivo virtual.


### <a name="step-2-configure-and-register-the-virtual-device"></a>Etapa 2: Configurar e registrar o dispositivo virtual

Antes de iniciar este procedimento, certifique-se de que você tem uma cópia da chave de criptografia de dados de serviço. A chave de criptografia de dados de serviço foi criada quando você configurou seu dispositivo StorSimple primeiro e você estava instruções salvá-lo em um local seguro. Se você não tiver uma cópia da chave de criptografia de dados de serviço, você deve contatar o Microsoft Support para obter assistência.

Execute as seguintes etapas para configurar e registrar seu dispositivo virtual StorSimple.
[AZURE.INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Etapa 3: (Opcional) modificar as configurações do dispositivo

A seção a seguir descreve as definições de configuração de dispositivo necessários para o StorSimple dispositivo virtual se você quiser usar CHAP, Gerenciador de instantâneo StorSimple ou alterar a senha de administrador do dispositivo.

#### <a name="configure-the-chap-initiator"></a>Configure o iniciador CHAP

Este parâmetro contém as credenciais que seu dispositivo virtual (destino) espera dos iniciadores (servidores) que estão tentando acessar os volumes. Os iniciadores fornecerá nome de usuário e uma senha de CHAP se identifiquem para seu dispositivo durante essa autenticação. Para obter etapas detalhadas, vá para [Configurar o CHAP para seu dispositivo](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Configurar o destino CHAP

Este parâmetro contém as credenciais que seu dispositivo virtual usa quando um iniciador CHAP habilitado solicita autenticação comum ou bidirecional. Seu dispositivo virtual usará reverter CHAP nome de usuário e senha reverter CHAP para identificar-se para o iniciador durante este processo de autenticação. Observe que as configurações de destino CHAP são configurações globais. Quando elas são aplicadas, todos os volumes conectados ao dispositivo virtual armazenamento usará autenticação CHAP. Para obter etapas detalhadas, vá para [Configurar o CHAP para seu dispositivo](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Configurar a senha de StorSimple Gerenciador de instantâneo

Gerenciador de instantâneo StorSimple software reside em seu host do Windows e permite aos administradores gerenciar backups do seu dispositivo de StorSimple na forma de locais e na nuvem instantâneos.

>[AZURE.NOTE] Para o dispositivo virtual, o seu host do Windows é uma máquina virtual Azure.

Ao configurar um dispositivo no Gerenciador de instantâneo de StorSimple, você será solicitado a fornecer o endereço IP do dispositivo de StorSimple e a senha para autenticar seu dispositivo de armazenamento. Para obter etapas detalhadas, vá para [Configurar o Gerenciador de instantâneo StorSimple senha](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Alterar a senha de administrador do dispositivo 

Quando você usa a interface do Windows PowerShell para acessar o dispositivo virtual, você precisará digitar uma senha de administrador do dispositivo. Para a segurança dos seus dados, é necessário alterar essa senha para que o dispositivo virtual possa ser usado. Para obter etapas detalhadas, vá para [Configurar senha de administrador do dispositivo](storsimple-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-virtual-device"></a>Conectar-se remotamente para o dispositivo virtual
Acesso remoto ao seu dispositivo virtual por meio da interface do Windows PowerShell não está habilitado por padrão. Você precisa habilitar o gerenciamento remoto do dispositivo virtual primeiro e depois ativá-lo no cliente que será usado para acessar o dispositivo virtual.

O processo de duas etapas para se conectar remotamente é detalhado abaixo.

### <a name="step-1-configure-remote-management"></a>Etapa 1: Configurar o gerenciamento remoto

Execute as seguintes etapas para configurar o gerenciamento remoto para seu dispositivo virtual StorSimple.

[AZURE.INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>Etapa 2: Acessar remotamente o dispositivo virtual

Depois de você habilitou gerenciamento remoto na página de configuração de dispositivo de StorSimple, você pode usar o Windows PowerShell remoto para conectar o dispositivo virtual de outra máquina virtual dentro da mesma rede virtual; Por exemplo, você pode conectar do host de máquina virtual que você configurou e usado para conectar iSCSI. Na maioria das implantações, você irá já abriu um ponto de extremidade público para acessar o seu host de máquina virtual que você pode usar para acessar o dispositivo virtual.

>[AZURE.WARNING] **Para maior segurança, é altamente recomendável que você use HTTPS ao conectar com os pontos de extremidade e exclua os pontos de extremidade após concluir sua sessão remota do PowerShell.**

Você deve seguir os procedimentos [Conectando-se remotamente ao seu dispositivo StorSimple](storsimple-remote-connect.md) para configurar a comunicação remota para seu dispositivo virtual.

## <a name="connect-directly-to-the-virtual-device"></a>Conectar-se diretamente para o dispositivo virtual

Você também pode conectar diretamente para o dispositivo virtual. Se desejar conectar-se diretamente para o dispositivo virtual do outro computador fora da rede virtual ou fora do ambiente do Microsoft Azure, você precisa criar pontos de extremidade adicionais conforme descrito no procedimento a seguir. 

Execute as seguintes etapas para criar um ponto de extremidade público do dispositivo virtual.

[AZURE.INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

Recomendamos que você conectar outra máquina virtual dentro da mesma rede virtual porque esta prática minimiza o número de pontos de extremidade públicos na sua rede virtual. Quando você usa esse método, você simplesmente conectar na máquina virtual através de uma sessão de área de trabalho remota e configure máquina virtual para usar como faria com qualquer outro cliente do Windows em uma rede local. Você não precisa acrescentar o número da porta pública porque a porta já será conhecida.

## <a name="work-with-the-storsimple-virtual-device"></a>Trabalhar com o dispositivo virtual StorSimple

Agora que você tiver criado e configurado o dispositivo virtual StorSimple, você está pronto para começar a trabalhar com ele. Você pode trabalhar com contêineres de volume, volumes e políticas de backup em um dispositivo virtual como faria em um dispositivo StorSimple físico; a única diferença é que você precisa certificar-se de que você selecionar o dispositivo virtual na sua lista de dispositivos. Consulte [usar o serviço de Gerenciador de StorSimple para gerenciar um dispositivo virtual](storsimple-manager-service-administration.md) para obter procedimentos passo a passo de várias tarefas de gerenciamento do dispositivo virtual.

As seções a seguir discutem algumas das diferenças que você encontrará ao trabalhar com o dispositivo virtual.

### <a name="maintain-a-storsimple-virtual-device"></a>Manter um dispositivo virtual StorSimple

Como é um dispositivo somente de software, manutenção do dispositivo virtual é mínima quando comparadas a manutenção do dispositivo físico. Você tem as seguintes opções:

- **Atualizações de software** – você pode exibir a data em que o software foi atualizado pela última vez, junto com qualquer atualizar mensagens de status. Você pode usar o botão **atualizações de verificação** na parte inferior da página para realizar uma varredura manual se você quiser verificar se há novas atualizações. Se houver atualizações disponíveis, clique em **Instalar atualizações** para instalar. Porque não há apenas uma única interface do dispositivo virtual, isso significa que haverá uma interrupção do serviço ligeiras quando atualizações são aplicadas. O dispositivo virtual será desligar e reiniciar (se necessário) para aplicar as atualizações que foram lançadas. Para obter um procedimento passo a passo, vá para [atualizar o dispositivo](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).
- **Suporte a pacote** – você pode criar e carregar um pacote de suporte para ajudar a solucionar problemas com o dispositivo virtual do Microsoft Support. Para obter um procedimento passo a passo, vá para [criar e gerenciar um pacote de suporte](storsimple-create-manage-support-package.md).

### <a name="storage-accounts-for-a-virtual-device"></a>Contas de armazenamento para um dispositivo virtual

Contas de armazenamento são criadas para uso pelo serviço Gerenciador de StorSimple pelo dispositivo virtual e pelo dispositivo físico. Quando você cria suas contas de armazenamento, recomendamos que você use um identificador de região no nome amigável para ajudar a garantir que a região seja consistente em toda a todos os componentes do sistema. Para um dispositivo virtual, é importante que todos os componentes de estar na mesma região para evitar problemas de desempenho.

Para obter um procedimento passo a passo, vá para [Adicionar uma conta de armazenamento](storsimple-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-virtual-device"></a>Desativar um dispositivo virtual StorSimple

Desativar um dispositivo virtual exclui a máquina virtual e os recursos que criou quando ele foi provisionado. Depois do dispositivo virtual está desativado, ele não pode ser restaurado ao seu estado anterior. Antes de desativar o dispositivo virtual, certifique-se parar ou excluir clientes e hosts que dependem dele.

Desativar um dispositivo virtual resulta nas seguintes ações:

- O dispositivo virtual é removido.

- O disco de sistema operacional e discos de dados criados para o dispositivo virtual são removidos.

- O serviço hospedado e criada durante a configuração de rede virtual são mantidas. Se você não estiver usando-los, você deve excluí-los manualmente.

- Instantâneos de nuvem criados para o dispositivo virtual são mantidos.

Para obter um procedimento passo a passo, vá para [desativar e excluir seu dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

Assim que o dispositivo virtual é mostrado como desativado na página Gerenciador de StorSimple serviço, você pode excluir o dispositivo virtual da lista de dispositivos na página **dispositivos** .


### <a name="start-stop-and-restart-a-virtual-device"></a>Iniciar, parar e reiniciar o dispositivo virtual
Ao contrário de dispositivo físico StorSimple, há sem energia em ou desligue botão para envio em um dispositivo virtual StorSimple. No entanto, pode haver ocasiões em que você precisa parar e reiniciar o dispositivo virtual. Por exemplo, algumas atualizações podem exigir que a máquina virtual seja reiniciado para concluir o processo de atualização. A maneira mais fácil para você começar, parar e reiniciar um dispositivo virtual é usar o Console de gerenciamento de máquinas virtuais.

Quando você examinar o Console de gerenciamento, o status do dispositivo virtual é **executando** porque ele é iniciado por padrão, após sua criação. Você pode iniciar, parar e reiniciar uma máquina virtual a qualquer momento.

[AZURE.INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>Padrões de fábrica

Se você decidir que deseja apenas recomeçar com seu dispositivo virtual, simplesmente desativar e excluí-la e, em seguida, crie um novo. Assim como quando seu dispositivo físico é redefinido, seu novo dispositivo virtual não terá qualquer atualizações instaladas; Portanto, certifique-se de verificar se há atualizações antes de utilizá-lo.


## <a name="fail-over-to-the-virtual-device"></a>Alternar para o dispositivo virtual

Recuperação de dados (DR) é um dos principais cenários de que o dispositivo virtual StorSimple foi projetado para. Neste cenário, o dispositivo StorSimple físico ou data center inteiro pode não estar disponível. Felizmente, você pode usar um dispositivo virtual para restaurar as operações em um local alternativo. Durante DR, os contêineres de volume do dispositivo de origem alterar a propriedade e são transferidos para o dispositivo virtual. Os pré-requisitos para DR são que o dispositivo virtual foi criado e configurado, todos os volumes dentro do contêiner de volume foram tomados offline e o contêiner de volume tem um associado instantâneo de nuvem.

>[AZURE.NOTE] 
>
> - Ao usar um dispositivo virtual como o dispositivo secundário para DR, tenha em mente que o 8010 tem 30 TB de armazenamento padrão e 8020 tem 64 TB de armazenamento Premium. O dispositivo virtual do maior capacidade 8020 pode ser mais adequado para um cenário de DR.
> - Você não consegue failover ou clonar em um dispositivo executando 2 de atualização para um dispositivo executando pré-atualização 1 o software. No entanto, você pode falhar ao longo de um dispositivo que executa a atualização 2 para um dispositivo executando Update 1 (1.1 ou 1.2)

Para obter um procedimento passo a passo, vá para [failover para um dispositivo virtual](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device).
 

## <a name="shut-down-or-delete-the-virtual-device"></a>Desligar ou excluir o dispositivo virtual

Se você anteriormente configurado e usado um StorSimple dispositivo virtual mas agora deseja parar de acúmulo de encargos de computação para seu uso, você pode desligar o dispositivo virtual. Desligar o dispositivo virtual não exclui o seu sistema operacional ou discos de dados em armazenamento. Ele parar encargos acúmulo na sua assinatura, mas encargos de armazenamento para o sistema operacional e dados discos continuará.

Se você excluir ou desligar o dispositivo virtual, ele será exibido como **Offline** na página serviço StorSimple Gerenciador de dispositivos. Você pode optar por desativar ou excluir o dispositivo, se desejar excluir os backups criados pelo dispositivo virtual. Para obter mais informações, consulte [desativar e excluir um dispositivo de StorSimple](storsimple-deactivate-and-delete-device.md).

[AZURE.INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[AZURE.INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

   
## <a name="troubleshoot-internet-connectivity-errors"></a>Solucionar problemas de conectividade de Internet 

Durante a criação de um dispositivo virtual, se há nenhuma conectividade com a Internet, a etapa da criação falhará. Para solucionar se a falha for devido a conectividade com a Internet, execute as seguintes etapas no portal de clássico do Azure:

1. Crie uma máquina virtual do Windows server 2012 no Azure. Esta máquina virtual devem usar a mesma conta de armazenamento, VNet e sub-rede como usado pelo seu dispositivo virtual. Se você já tiver um host do Windows Server existente no Azure usando a mesma conta de armazenamento, Vnet e sub-rede, você pode usá-lo para solucionar a conectividade com a Internet.
2. Log remoto na máquina virtual criada na etapa anterior. 
3. Abra uma janela de comando na máquina virtual (Win + R e digite `cmd`).
4. Execute o seguinte cmd no prompt.

    `nslookup windows.net`

5. Se `nslookup` falhar, então falha de conectividade de Internet está impedindo que o dispositivo virtual registrando ao serviço Gerenciador de StorSimple. 
6. Faça as alterações necessárias à sua rede virtual para garantir que o dispositivo virtual seja capaz de acessar sites Azure como "no windows.net".

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o serviço de Gerenciador de StorSimple para gerenciar um dispositivo virtual](storsimple-manager-service-administration.md).
 
- Entender como [restaurar um volume StorSimple um conjunto de backup](storsimple-restore-from-backup-set.md). 

