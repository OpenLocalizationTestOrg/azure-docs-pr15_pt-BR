<properties 
   pageTitle="Práticas recomendadas para Array Virtual StorSimple | Microsoft Azure"
   description="Descreve as práticas recomendadas para implantar e gerenciar a matriz Virtual StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-best-practices"></a>Práticas recomendadas de Array Virtual StorSimple

## <a name="overview"></a>Visão geral

Array Virtual do Microsoft Azure StorSimple é uma solução de armazenamento integrado que gerencia tarefas de armazenamento entre um dispositivo virtual local executado em um hipervisor e o armazenamento de nuvem do Microsoft Azure. Matriz Virtual StorSimple é uma alternativa eficiente, econômica à matriz física 8000 série. A matriz virtual pode ser executados em sua infraestrutura de hipervisor existente, dá suporte a iSCSI e os protocolos SMB e é adequada para cenários de escritório remoto/filial. Para obter mais informações sobre as soluções de StorSimple, vá para [Microsoft Azure StorSimple Overview](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Este artigo aborda as práticas recomendadas implementadas durante a configuração inicial, implantação e gerenciamento da matriz Virtual StorSimple. Estas práticas recomendadas fornecem validadas diretrizes para a configuração e o gerenciamento de sua matriz virtual. Este artigo é dirigido os administradores de TI que implantar e gerenciar os conjuntos virtuais em seus centros de dados.

Recomendamos que uma revisão periódica das práticas recomendadas para ajudar a garantir que seu dispositivo ainda está em conformidade quando forem feitas alterações ao fluxo de configuração ou operação. Se você encontrar problemas durante a implementação dessas práticas recomendadas em seu array virtual, [entre em contato com o suporte da Microsoft](storsimple-contact-microsoft-support.md) para obter assistência.

## <a name="configuration-best-practices"></a>Práticas recomendadas de configuração 

Estas práticas recomendadas abrangem as diretrizes que devem ser seguidas durante a configuração inicial e implantação das matrizes virtuais. Estas práticas recomendadas incluem aqueles relacionados para o provisionamento da máquina virtual, configurações de política de grupo, dimensionamento, configuração de rede, configurando contas de armazenamento e criando compartilhamentos e volumes para a matriz virtual. 

### <a name="provisioning"></a>Provisionamento 

Matriz de Virtual StorSimple é uma máquina virtual (VM) provisionada no hipervisor (Hyper-V ou VMware) do seu servidor de host. Ao provisionar a máquina virtual, certifique-se de que seu host é capaz de dedicar recursos suficientes. Para obter mais informações, vá para [requisitos mínimos de recurso](storsimple-ova-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements) para provisionar uma matriz. 

Implemente as seguintes práticas recomendadas ao provisionar a matriz virtual:


|                        | Hyper-V                                                                                                                                        | VMware                                                                                                               |
|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| **Tipo de máquina virtual**   | **Geração 2** Máquina virtual para uso com o Windows Server 2012 ou posterior e uma imagem de *.vhdx* . <br></br> **Geração 1** Máquina virtual para uso com um Windows Server 2008 ou posterior e uma imagem de *VHD* .                                                                                                              | Use máquina virtual versão 8-11 com imagem de *vmdk* .                                                                      |
| **Tipo de memória**            | Configure como **memória estática**. <br></br> Não use a opção de **memória dinâmica** .            |                                                    |
| **Tipo de dados de disco**         | Provisionar como **expansível dinamicamente**.<br></br> **Tamanho fixo** demora muito tempo. <br></br> Não use a opção de **diferenciação** .                                                                                                                   | Use a opção de **provisionar fina** .                                                                                      |
| **Modificações de disco de dados** | Não é permitida expansão ou redução. Uma tentativa de fazer isso resulta em perda de todos os dados locais no dispositivo.                       | Não é permitida expansão ou redução. Uma tentativa de fazer isso resulta em perda de todos os dados locais no dispositivo. |

### <a name="sizing"></a>Dimensionamento

Quando sua matriz Virtual StorSimple de dimensionamento, considere os seguintes fatores:

- Reserva de local para volumes ou compartilhamentos. Cerca de 12% do espaço é reservado no nível local para cada volume hierárquico provisionado ou compartilhar. Aproximadamente 10% do espaço também é reservada para um volume localmente fixado para o sistema de arquivos.
- Instantâneo de sobrecarga. Aproximadamente 15% de espaço no nível local é reservada para instantâneos.
- Necessidade de restaura. Se fazendo restaurar como uma nova operação, dimensionamento deve conta para o espaço necessário para restaurar. Restaurar é concluído para um compartilhamento ou o volume do mesmo tamanho ou maior.
- Alguns buffer deve ser alocado para qualquer crescimento inesperado.

Com base nos fatores anteriores, os requisitos de dimensionamento podem ser representados pela seguinte equação:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`


Os exemplos a seguir ilustram como você pode dimensionar uma matriz virtual de acordo com suas necessidades.

#### <a name="example-1"></a>Exemplo 1:
No seu array virtual, você deseja ser capaz de 

- provisionar um 2 TB hierárquico volume ou compartilhamento.
- provisionar um 1 TB hierárquico volume ou compartilhamento.
- provisionar um 300 GB de volume localmente fixada ou compartilhar.


Para os volumes ou compartilhamentos de anterior, vamos calcule os requisitos de espaço no nível local. 

Primeiro, para cada volume hierárquico/compartilhamento, reserva local seria igual a 12% do tamanho do volume/compartilhamento. Para o volume localmente fixado/compartilhamento, reserva local é 10% do tamanho do volume/compartilhamento localmente fixada (além do tamanho provisionado). Neste exemplo, você precisa de:

- Reserva local 240 GB (para um 2 TB hierárquico volume/compartilhamento)
- Reserva de local de 120 GB (para um 1 TB hierárquico volume/compartilhamento)
- 330 GB para volume localmente fixada ou compartilhamento (adicionando 10% de reserva local para o tamanho de 300 GB provisionado)

O espaço total obrigatório no nível de local até o momento é: 240 GB + 120 GB + 330 GB = 690 GB.

Segundo, precisamos pelo menos o espaço na camada local como a reserva de única maior. Este valor extra é usado no caso de precisar restaurar a partir de um instantâneo de nuvem. Neste exemplo, a maior reserva local é 330 GB (incluindo reserva para o sistema de arquivos), para que você adiciona que o 690 GB: 690 GB + 330 GB = 1020 GB.
Se nós realizada subsequentes restaura adicionais, estamos sempre pode liberar o espaço da operação de restauração anterior.

Terceiro, precisamos 15% do total espaço local até o momento para armazenar instantâneos locais, para que apenas 85% dele está disponível. Neste exemplo, que seria em torno 1020 GB = 0.85&ast;dados provisionados disco TB. Portanto, o disco de dados provisionados seria (1020&ast;(1/0.85)) = 1200 GB = 1,20 TB ~ 1,25 TB (arredondamento para quartil mais próximo)

Cálculo de crescimento inesperado e restaura novo, você deve provisionar um disco local ao redor 1,25-1,5 TB.

> [AZURE.NOTE] Também é recomendável que o disco local está provisionado thin. Essa recomendação é porque o espaço de restauração é necessária somente quando você deseja restaurar os dados que são mais de cinco dias. Recuperação de nível de item permite restaurar dados para os últimos cinco dias sem a necessidade do espaço extra para restauração.

#### <a name="example-2"></a>Exemplo 2: 
No seu array virtual, você deseja ser capaz de 

- provisionar um 2 TB hierárquico volume
- Configurar um volume de 300 GB localmente fixada

Com base em 12% de reserva de espaço local para volumes/compartilhamentos hierárquicos e 10% para volumes/compartilhamentos localmente fixados, precisamos

- Reserva local 240 GB (para 2 TB hierárquico volume/compartilhamento)
- 330 GB para volume localmente fixada ou compartilhamento (adicionando 10% de reserva local para o espaço de 300 GB provisionado)

Espaço total necessário na camada local é: 240 GB + 330 GB = 570 GB

O espaço de local mínimo necessário para restaurar é 330 GB. 

15% do disco total é usado para armazenar instantâneos para que 0.85 somente está disponível. Portanto, é o tamanho do disco (900&ast;(1/0.85)) = TB 1,06 ~ 1,25 TB (arredondamento para quartil mais próximo) 

Cálculo em qualquer crescimento inesperado, você pode provisionar um disco local 1,25-1,5 TB.


### <a name="group-policy"></a>Política de grupo

Política de grupo é uma infraestrutura que permite que você implemente configurações específicas para usuários e computadores. Configurações de política de grupo estão contidas em objetos de política de grupo (GPOs), que estão vinculados aos seguintes contêineres de serviços de domínio Active Directory (AD DS): sites, domínios ou unidades organizacionais (organizacionais). 

Se sua matriz virtual estiver domínio, GPOs podem ser aplicados a ele. Esses GPOs podem instalar aplicativos como um software antivírus que podem afetar negativamente a operação da matriz Virtual StorSimple.

Portanto, recomendamos que você:

-   Certifique-se de que seu array virtual está em sua própria unidade organizacional (OU) para o Active Directory. 

-   Certifique-se de que nenhuma objetos de política de grupo (GPOs) são aplicados à sua matriz virtual. Você pode bloquear a herança para garantir que a matriz virtual (nó filho) não herdam automaticamente qualquer GPO do pai. Para obter mais informações, vá para [Bloquear a herança](https://technet.microsoft.com/library/cc731076.aspx).


### <a name="networking"></a>Rede

A configuração de rede para o conjunto virtual é feita através da interface do usuário da web local. Uma interface de rede virtual está habilitada por meio de hipervisor em que a matriz virtual está provisionada. Use a página de [Configurações de rede](storsimple-ova-deploy3-fs-setup.md) para configurar o endereço IP da interface de rede virtual, sub-rede e gateway.  Você também pode configurar o servidor DNS primário e secundário, configurações de tempo e configurações de proxy opcional para seu dispositivo. Na maioria da configuração de rede é uma configuração única. Examine os [requisitos de rede StorSimple](storsimple-ova-system-requirements.md#networking-requirements) antes de implantar a matriz virtual.

Ao implantar sua matriz virtual, recomendamos que você siga estas práticas recomendadas:

-   Certifique-se de que a rede na qual a matriz virtual é implantada sempre tem a capacidade de dedicar largura de banda de Internet de Mbps 5 (ou mais). 

    -   Necessidade de largura de banda de Internet varia dependendo suas características de carga de trabalho e a taxa de alteração de dados.

    -   A alteração de dados que pode ser tratada é diretamente proporcional à sua largura de banda de Internet. Como exemplo ao fazer um backup, uma largura de banda Mbps 5 pode acomodar uma alteração de dados de cerca de 18 GB em 8 horas. Com quatro vezes mais largura de banda (20 Mbps), você pode lidar com mais de quatro vezes alteração de dados (72 GB). 

-   Certifique-se de que a conectividade com a Internet sempre está disponível. As conexões de Internet esporádicas ou não confiáveis para os dispositivos podem resultar em perda de acesso aos dados na nuvem e podem resultar em uma configuração sem suporte.

-   Se você planeja implantar seu dispositivo como um servidor de iSCSI: 
    -   Recomendamos que você desative a opção de **endereço IP obter automaticamente** (DHCP). 
    -   Configure endereços IP estáticos. Você deve configurar um primário e um servidor DNS secundário.

    -   Se definir várias interfaces de rede em seu virtual de matriz, apenas a primeira interface de rede (por padrão, essa interface é **Ethernet**) pode alcançar a nuvem. Para controlar o tipo de tráfego, você pode criar várias interfaces de rede virtual em seu array virtual (configurado como um servidor iSCSI) e conectar essas interfaces para diferentes sub-redes.

-   Para reduzir a largura de banda nuvem apenas (usado pelo array virtual), configurar a otimização do roteador ou o firewall. Se você definir a otimização no seu hipervisor, ele será controlar todos os protocolos incluindo iSCSI e SMB em vez de apenas a largura de banda de nuvem. 

-   Garantir que a sincronização tempo hipervisores estiver ativado. Se usando Hyper-V, selecione seu array virtual no Gerenciador de Hyper-V, vá para **configurações &gt; Integration Services**e certifique-se de que a **sincronização de horário** está marcada.

### <a name="storage-accounts"></a>Contas de armazenamento

Matriz Virtual StorSimple podem ser associada uma conta de armazenamento único. Esta conta de armazenamento pode ser uma conta de armazenamento gerado automaticamente, uma conta na mesma assinatura como o serviço, ou uma conta de armazenamento relacionados para outra assinatura. Para obter mais informações, consulte como [Gerenciar contas de armazenamento para sua matriz virtual](storsimple-ova-manage-storage-accounts.md).

Use as seguintes recomendações para contas de armazenamento associadas ao seu array virtual.

-   Ao vincular vários matrizes virtuais com uma conta de armazenamento único, calcular a capacidade máxima (64 TB) para uma matriz virtual e o tamanho máximo (500 TB) para uma conta de armazenamento. Isso limita o número de matrizes virtuais tamanho real que podem ser associadas essa conta de armazenamento para cerca de 7.

-   Ao criar uma nova conta de armazenamento
    -   Recomendamos que você criá-lo na região mais próxima ao escritório remoto/filial onde seu Array Virtual StorSimple é implantado para minimizar as latências.

    -   Tenha em mente que você não pode mover uma conta de armazenamento em diferentes regiões. Também é possível mover um serviço em assinaturas.

    -   Use uma conta de armazenamento que implementa redundância entre data centers. Armazenamento de localização geográfica redundante (GRS), zona redundantes armazenamento (ZRS) e armazenamento localmente redundantes (LRS) há suporte para uso com o seu array virtual. Para obter mais informações sobre os diferentes tipos de contas de armazenamento, vá para [replicação de armazenamento do Azure](../storage/storage-redundancy.md).


### <a name="shares-and-volumes"></a>Compartilhamentos e volumes

Para seu Array Virtual StorSimple, você pode provisionar compartilhamentos quando ele é configurado como um servidor de arquivos e volumes quando configurado como um servidor iSCSI. As práticas recomendadas para a criação de compartilhamentos e volumes estão relacionadas ao tamanho e o tipo configurado.

#### <a name="volumeshare-size"></a>Tamanho de volume/compartilhamento

Em seu array virtual, você pode provisionar compartilhamentos quando ele é configurado como um servidor de arquivos e volumes quando configurado como um servidor iSCSI. As práticas recomendadas para a criação de compartilhamentos e volumes relacionam o tamanho e o tipo de configurado. 

Tenha em mente as seguintes práticas recomendadas ao provisionar o compartilhamento ou volume no seu dispositivo virtual.

-   Os tamanhos de arquivo relativo o tamanho provisionado de um compartilhamento hierárquico podem afetar o desempenho hierárquico. Trabalhar com arquivos grandes pode resultar em um nível lento check-out. Ao trabalhar com arquivos grandes, recomendamos que o arquivo maior é menor do que 3% do tamanho do compartilhamento.

-   Um máximo de 16 volumes/compartilhamentos pode ser criado no array virtual. Se localmente fixado, os volumes/compartilhamentos pode estar entre 50 GB a 2 TB. Se hierárquica, os volumes/compartilhamentos deve estar entre 500 GB para 20 TB. 

-   Ao criar um volume, fator no consumo de dados esperados bem como crescimento futuro. Enquanto o volume não pode ser expandido mais tarde, você sempre pode restaurar um volume maior.

-   Depois que o volume tiver sido criado, você não pode reduzir o tamanho do volume em StorSimple.
   
-   Ao gravar um volume hierárquico em StorSimple, quando os dados do volume atingem um limite determinado (relativo local espaço reservado para o volume), o AE está limitada. Continuar a gravar este volume diminui o IO significativamente. Embora você possa gravar um volume hierárquico além da sua capacidade provisionado (nós não ativamente interrompa o usuário de escrita além da capacidade provisionada), você pode ver uma notificação de alerta para o efeito que com assinaturas que você tem em excesso. Quando você vir o alerta, é fundamental que você tome medidas corretivas como excluir dados do volume ou restaurar o volume um volume maior (expansão de volume não é suportado atualmente).

-   Para exemplos de uso de recuperação de desastres, como o número de compartilhamentos/volumes permitidos é 16 e o número máximo de compartilhamentos/volumes que podem ser processados em paralelo também é 16, o número de compartilhamentos/volumes não têm uma influência em seu RPO e RTOs. 

#### <a name="volumeshare-type"></a>Tipo de volume/compartilhamento

StorSimple suporta dois tipos de volume/compartilhamento baseados no uso: localmente fixos e hierárquico. Localmente fixados volumes/compartilhamentos thickly são provisionados enquanto os volumes/compartilhamentos hierárquicos thin são provisionados. 

Recomendamos que você implemente as seguintes práticas recomendadas ao configurar volumes/compartilhamentos de StorSimple:

-   Identifique o tipo de volume com base nas cargas de trabalho que você pretende implantar antes de criar um volume. Use volumes localmente fixados para cargas de trabalho que exigem garantias locais de dados (mesmo durante uma interrupção de nuvem) e que latências de nuvem baixa. Depois de criar um volume em seu array virtual, você não pode alterar o tipo de volume de localmente fixos para hierárquico ou *vice-versa*. Como exemplo, criar volumes localmente fixados ao implantar cargas de trabalho SQL ou cargas de trabalho hospedando máquinas virtuais (VMs); Use volumes hierárquicos para cargas de trabalho de compartilhamento de arquivo.

-   Marque a opção para dados de arquivamento menos usadas com frequência ao lidar com arquivos grandes. Um tamanho de bloco de duplicação maior de 512 K é usado quando essa opção estiver habilitada para acelerar a transferência de dados para a nuvem.

#### <a name="volume-format"></a>Formatar volume

Depois de criar volumes de StorSimple no seu servidor iSCSI, você precisa inicializar, montar e formatar os volumes. Essa operação é executada no host conectado ao dispositivo StorSimple. Práticas recomendadas a seguir são recomendadas quando montagem e formatando volumes do host de StorSimple.

-   Execute uma formatação rápida em todos os volumes de StorSimple.

-   Ao formatar um volume StorSimple, use um tamanho de unidade de alocação (Austrália) de 64 KB (o padrão é 4 KB). A 64 KB Austrália baseia-se nos testes realizados internamente para cargas de trabalho de StorSimple comuns e outras cargas de trabalho.

-   Ao usar a matriz Virtual StorSimple configurado como um servidor de iSCSI, não use volumes divididos ou discos dinâmicos como esses volumes ou discos não são suportados pelo StorSimple.

#### <a name="share-access"></a>Compartilhar o acesso

Ao criar compartilhamentos no seu servidor de arquivos de array virtual, siga estas diretrizes:

-   Ao criar um compartilhamento, atribua um grupo de usuário como um administrador de compartilhamento em vez de um único usuário.

-   Você pode gerenciar as permissões de NTFS após o compartilhamento é criado pela edição os compartilhamentos através do Windows Explorer.

#### <a name="volume-access"></a>Acesso de volume

Ao configurar os volumes iSCSI no Array Virtual StorSimple, é importante controlar o acesso sempre que necessário. Para determinar quais servidores host podem acessar volumes, crie e associar registros de controle de acesso (ACRs) volumes de StorSimple.

Use as seguintes práticas recomendadas ao configurar ACRs para volumes de StorSimple:

-   Sempre associe pelo menos um ACR um volume.

-   Defina várias ACRs apenas em um ambiente agrupado.

-   Ao atribuir ACR mais de um a um volume, certifique-se de que o volume não seja exposto de maneira onde ele pode ser acessado simultaneamente por mais de um host não-clusterizados. Se você tiver atribuído ACRs vários para um volume, uma mensagem de aviso aparece para que você examine a configuração.

### <a name="data-security-and-encryption"></a>Criptografia e segurança de dados

A matriz de Virtual StorSimple tem recursos de segurança e criptografia de dados que garantir a confidencialidade e a integridade dos dados. Ao usar esses recursos, é recomendável que você siga estas práticas recomendadas: 

-   Defina uma chave de criptografia de armazenamento de nuvem para gerar criptografia AES-256 antes que os dados são enviados do seu array virtual para a nuvem. Esta chave não é necessária se seus dados são criptografados começar com. A chave pode ser gerada e mantida segura usando um sistema de gerenciamento de chaves como [Azure cofre chave](../key-vault/key-vault-whatis.md).

-   Ao configurar a conta de armazenamento por meio do serviço do Gerenciador de StorSimple, certifique-se de que você ative o modo de SSL para criar um canal seguro para comunicação de rede entre seu dispositivo StorSimple e a nuvem.

-   Gerar novamente as chaves para suas contas de armazenamento (acessando o serviço de armazenamento do Azure) periodicamente conta de quaisquer alterações acessar com base na lista alterada de administradores.

-   Os dados em sua matriz virtual são compactados e eliminação de duplicação antes que ela é enviada para o Azure. Não recomendamos usando o serviço de função de eliminação de duplicação de dados em seu host do Windows Server.


## <a name="operational-best-practices"></a>Práticas recomendadas operacionais

As práticas recomendadas operacionais são diretrizes que devem ser seguidas durante a operação da matriz virtual ou gerenciamento diário. Estas práticas cobrem tarefas específicas de gerenciamento como fazer backups, restaurar um conjunto de backup, realizar um failover, desativando e excluindo a matriz, monitoramento de saúde e o uso do sistema e executa um antivírus examinem no array virtual.

### <a name="backups"></a>Backups

É feito backup dos dados no array virtual para a nuvem de duas maneiras, um padrão automatizado backup diário de todo o dispositivo iniciando às 22:30 ou por meio de um backup manual de sob demanda. Por padrão, o dispositivo cria automaticamente diária instantâneos de nuvem de todos os dados que reside nele. Para obter mais informações, vá para [fazer backup de seu Array Virtual StorSimple](storsimple-ova-backup.md).

A frequência e retenção associados com os backups padrão não podem ser alteradas, mas você pode configurar o tempo em que os backups diários são iniciados diariamente. Ao configurar a hora de início para backups automatizados, recomendamos que:

-   Agende backups pico. Hora de início de backup não deve coincidir com vários host IO.

-   Inicie um backup manual de sob demanda durante o planejamento para executar um dispositivo failover ou prévia para a janela de manutenção, para proteger os dados em sua matriz virtual.

### <a name="restore"></a>Restaurar

Você pode restaurar de um conjunto de duas maneiras de backup: restaurar para outro volume ou compartilhamento ou realizar uma recuperação de nível de item (disponível apenas em uma matriz virtual configurada como um servidor de arquivo). Recuperação de nível de item permite que você faça uma recuperação granular de arquivos e pastas de um backup de nuvem de todos os compartilhamentos no dispositivo StorSimple. Para obter mais informações, vá para [restaurar a partir de um backup](storsimple-ova-restore.md).

Ao realizar uma restauração, tenha as seguintes diretrizes em mente:

-   Sua matriz Virtual StorSimple não oferece suporte a restauração no local. No entanto pode ser prontamente alcançado por um processo de duas etapas: adicionar espaço na virtual de matriz e restaurar para outro volume/compartilhamento.

-   Quando a restauração de um volume local, lembre-se a restauração será uma longa operação em execução. Embora o volume rapidamente pode ficar online, os dados continuam a ser alimentado no plano de fundo.

-   O tipo de volume permanece o mesmo durante o processo de restauração. Um volume hierárquico é restaurado para outro volume hierárquico e um volume localmente fixado para outro localmente fixos volume.

-   Ao tentar restaurar um volume ou em um compartilhamento de um backup definido, se o trabalho de restauração falha, um volume de destino ou compartilhar ainda pode ser criada no portal. É importante que você excluir este volume de destino não utilizados ou compartilha do portal para minimizar problemas futuros decorrente desse elemento.

### <a name="failover-and-disaster-recovery"></a>Failover e recuperação de desastres

Um failover de dispositivo permite que você migrar seus dados de um dispositivo de *origem* no data center para outro dispositivo de *destino* localizado no mesmo ou em um local geográfico diferente. O failover de dispositivo é para todo o dispositivo. Durante o failover, os dados de nuvem para o dispositivo de origem altera a propriedade do dispositivo de destino.

Para seu Array Virtual StorSimple, você só pode falhar sobre a outra matriz virtual gerenciado pelo serviço Gerenciador de StorSimple mesmo. Um failover para um dispositivo de 8000 série ou uma matriz gerenciadas pelo outro serviço de Gerenciador de StorSimple (para o dispositivo de origem) não é permitido. Para saber mais sobre as considerações de failover, vá para [os pré-requisitos para o failover do dispositivo](storsimple-ova-failover-dr.md).

Ao executar uma falha ao longo para seu array virtual, lembre-se do seguinte:

-   Para um failover planejado, ele é uma prática recomendada para tirar todos os volumes/compartilhamentos offline antes de iniciar o failover. Siga as instruções específicas do sistema operacional para os volumes/compartilhamentos offline do host primeiro dar e faça aqueles offline em seu dispositivo virtual.

-   Para uma arquivo servidor recuperação (DR), recomendamos que você ingressar o dispositivo de destino para o mesmo domínio como fonte para que as permissões de compartilhamento são automaticamente resolvidas. Somente o failover para um dispositivo de destino no mesmo domínio é compatível com esta versão.

-   Depois que o DR é concluído com êxito, o dispositivo de origem é excluído automaticamente. Embora o dispositivo não está mais disponível, a máquina virtual que você provisionado no sistema host ainda está consumindo recursos. Recomendamos que você exclua esta máquina virtual do seu sistema de host para impedir que todos os encargos acúmulo.

-   Observe que, mesmo se o failover não for bem-sucedido, **os dados estão sempre seguros na nuvem**. Considere os seguintes três cenários em que o failover não foi concluída com êxito:

    -   Ocorreu uma falha nos estágios iniciais da cobertura de falha como quando as pré-verificações de DR estão sendo executadas. Nessa situação, seu dispositivo de destino é ainda poderá ser usado. Você pode repetir o failover no mesmo dispositivo de destino.

    -   Ocorreu uma falha durante o processo de failover real. Nesse caso, o dispositivo de destino está marcado inutilizável. Você deve provisionar configurar outro array virtual de destino e usá-lo para failover.

    -   O failover foi concluído depois que o dispositivo de origem foi excluído, mas o dispositivo de destino tem problemas e você não consegue acessar os dados. Os dados ainda seja seguros na nuvem e podem ser facilmente recuperados criando outra matriz virtual e usá-lo como um dispositivo de destino para o DR.

### <a name="deactivate"></a>Desativar

Quando você desativa uma matriz Virtual StorSimple, você servidor a conexão entre o dispositivo e o serviço de Gerenciador de StorSimple correspondente. Desativação é uma operação **permanente** e não pode ser desfeita. Um dispositivo desativado não pode ser registrado com o serviço Gerenciador de StorSimple novamente. Para obter mais informações, vá para [desativar e excluir sua matriz Virtual StorSimple](storsimple-deactivate-and-delete-device.md).

Ter as seguintes práticas recomendadas em mente ao desativando seu array virtual:

-   Tirar um instantâneo de nuvem de todos os dados antes de desativar um dispositivo virtual. Quando você desativa uma matriz virtual, todos os dados de dispositivo local é perdido. Tirar um instantâneo de nuvem permitirá que você recupere dados em um estágio posterior.

-   Antes de desativar uma matriz Virtual StorSimple, certifique-se parar ou excluir clientes e hosts que dependem desse dispositivo.

-   Exclua um dispositivo desativado se você já não estiver usando para que ele não acumular encargos.

### <a name="monitoring"></a>Monitoramento

Para garantir que sua matriz Virtual StorSimple está em um estado de integridade contínuo, você precisa monitorar a matriz e garantir que você receba informações do sistema inclusive alertas. Para monitorar a integridade geral da matriz virtual, implemente as seguintes práticas recomendadas:

- Configure o monitoramento para controlar o uso do disco do seu disco de dados array virtual assim como o disco de sistema operacional. Se executando Hyper-V, você pode usar uma combinação de sistema de máquina Virtual SCVMM (Center Manager) e System Center Operations Manager (SCOM) para monitorar seus hosts de virtualização.   

- Configure notificações por email em seu array virtual para enviar alertas em determinados níveis de uso.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Aplicativos de verificação de vírus e pesquisa de índice

Uma matriz de Virtual StorSimple automaticamente pode nível dados da camada de locais na nuvem do Microsoft Azure. Quando um aplicativo como uma pesquisa de índice ou uma verificação de vírus é usado para verificar os dados armazenados em StorSimple, você precisa cuidam que os dados de nuvem não obter acessados e puxados de volta para o nível local.

Recomendamos que você implemente as seguintes práticas recomendadas ao configurar a verificação de vírus ou de pesquisa de índice em sua matriz virtual:

-   Desabilite todas as operações de verificação completa configurada automaticamente.

-   Para volumes hierárquicos, configure a índice pesquisa ou vírus digitalização do aplicativo para executar uma verificação incremental. Isso seria examinar somente os novos dados provável que residem no nível local. Os dados que é hierárquico na nuvem não são acessados durante uma operação de incremental.

-   Verifique as configurações e filtros de pesquisa corretos estão configuradas para que apenas pretendidos tipos de arquivos obtém verificados. Por exemplo, imagem arquivos (JPEG, GIF e TIFF) e desenhos de engenharia não deve ser examinado durante a recriação de índice incrementais ou completas.

Se usando o processo de indexação do Windows, siga estas diretrizes:

-   Não use o indexador do Windows para volumes hierárquicos como recupera grandes quantidades de dados (TB) da nuvem, se o índice precisa ser recriado com frequência. Recriar o índice seria recuperar todos os tipos de arquivo para indexar seu conteúdo.

-   Use as janelas indexação processo para volumes localmente fixados como isso só seria acessar os dados nos níveis locais para criar o índice (os dados de nuvem não serão acessados).

### <a name="byte-range-locking"></a>Bloqueio de faixa de bytes

Aplicativos podem bloquear um intervalo de bytes especificado dentro dos arquivos. Se o bloqueio de faixa de bytes estiver habilitado nos aplicativos que estiver escrevendo para sua StorSimple, então hierárquico não funciona no array virtual. Para a hierarquia para trabalhar, todas as áreas dos arquivos acessados devem ser desbloqueadas. Bloqueio de faixa de bytes não é compatível com volumes hierárquicos em seu array virtual.

Medidas recomendadas para minimizar o bloqueio de faixa de bytes incluem:

-   Desative o bloqueio em lógica do seu aplicativo de intervalo de bytes.

-   Uso localmente fixos volumes (em vez de hierárquico) para os dados associados com este aplicativo. Volumes localmente fixados não nível na nuvem.

-   Quando localmente usando fixos volumes com byte intervalo bloqueio ativado, o volume pode ficar online antes da restauração for concluída. Nesses casos, você deve esperar a restauração seja concluída.

## <a name="multiple-arrays"></a>Várias matrizes

Vários conjuntos de virtuais talvez precise ser implantado conta de um conjunto de trabalho crescente de dados que poderiam passando para a nuvem, portanto, afetar o desempenho do dispositivo. Nesses casos, é melhor escala dispositivos que o conjunto de trabalho cresce. Isso requer um ou mais dispositivos a ser adicionado no Centro de dados local. Ao adicionar os dispositivos, você pode:

-   Divida o conjunto de dados atual.
-   Implante novas cargas de trabalho em dispositivos de novo.
-   Se Implantando várias matrizes virtuais, recomendamos que de balanceamento de carga perspectiva, distribuir a matriz entre hosts de hipervisor diferentes.

-  Vários conjuntos de virtuais (quando configurado como um servidor de arquivos ou um servidor iSCSI) podem ser implantados em um Namespace do sistema de arquivo distribuído. Para obter etapas detalhadas, vá para [Distribuído solução Namespace de sistema de arquivos com o guia de implantação de armazenamento de nuvem híbrida](https://www.microsoft.com/download/details.aspx?id=45507). Replicação de sistema de arquivo distribuído atualmente não é recomendável para uso com a matriz virtual. 


## <a name="see-also"></a>Consulte também
Saiba como [administrar sua matriz Virtual StorSimple](storsimple-ova-manager-service-administration.md) por meio do serviço do Gerenciador de StorSimple.
