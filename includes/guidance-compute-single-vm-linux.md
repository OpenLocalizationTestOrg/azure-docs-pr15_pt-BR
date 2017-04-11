Este artigo descreve um conjunto de práticas comprovadas para executar uma máquina virtual do Linux (máquina virtual) no Azure, prestar atenção para escalabilidade, disponibilidade, capacidade de gerenciamento e segurança. Azure suporta executando várias distribuições Linux populares, incluindo CentOS, Debian, Red Hat Enterprise, Ubuntu e FreeBSD. Para obter mais informações, consulte [Azure e Linux][azure-linux].

> [AZURE.NOTE] Azure tem dois diferentes modelos de implantação: [Gerenciador de recursos] [ resource-manager-overview] e clássico. Este artigo usa o Gerenciador de recursos, a Microsoft recomenda para novas implantações.

Não recomendamos usando uma única VM para cargas de trabalho de produção, porque não há nenhum contrato de nível de serviço de tempo (SLA) para VMs único no Azure. Para obter o SLA, você deve implantar várias VMs em um [conjunto de disponibilidade][availability-set]. Para obter mais informações, consulte [executando várias VMs no Azure][multi-vm]. 

## <a name="architecture-diagram"></a>Diagrama de arquitetura

Provisionar uma máquina virtual no Azure envolve mais partes móveis que apenas a máquina virtual em si. Há elementos de computação, rede e armazenamento que você precisa considerar.

> Um documento do Visio que inclui este diagrama de arquitetura está disponível para download no [Centro de download da Microsoft][visio-download]. Este diagrama é sobre o "VM de computação - única" página.

![[0]][0]

- **Grupo de recursos.** Um [_grupo de recursos_] [ resource-manager-overview] é um recipiente que contém recursos relacionados. Crie um grupo de recursos para manter os recursos para esta máquina virtual.

- **VM**. Você pode provisionar uma máquina virtual em uma lista de imagens publicadas ou de um arquivo de disco rígido virtual (VHD) que você carregar ao armazenamento de blob do Microsoft Azure.

- **Disco do sistema operacional.** O disco de sistema operacional é um VHD armazenado no [armazenamento do Azure][azure-storage]. Isso significa que ele persista, mesmo se o computador host diminui. O disco de sistema operacional está `/dev/sda1`.

- **Disco temporário.** A máquina virtual é criada com um disco temporário. Este disco está armazenado em uma unidade física na máquina host. É _não_ salvas armazenamento do Azure e podem desaparecer durante reinicialização e outros eventos de ciclo de vida de máquina virtual. Use este disco somente para dados temporários, como arquivos de página ou troca. O disco temporário está `/dev/sdb1` e é montado em `/mnt/resource` ou `/mnt`.

- **Discos de dados.** Um [disco de dados] [ data-disk] é um VHD persistente usado para dados de aplicativo. Discos de dados são armazenados no armazenamento do Azure, como o disco de sistema operacional.

- **Rede virtual (VNet) e sub-rede.** Cada máquina virtual no Azure é implantado em (VNet), que é ainda mais dividida em sub-redes.

- **Endereço IP público.** Um endereço IP público é necessária para se comunicar com a máquina virtual&mdash;por exemplo via SSH.

- **Interface de rede (NIC)**. NIC habilita a máquina virtual para se comunicar com a rede virtual.

- **Grupo de segurança de rede (NSG)**. O [NSG] [ nsg] é usado para permitir/negar o tráfego de rede à sub-rede. Você pode associar uma NSG com uma NIC individual ou com uma sub-rede. Se você associá-lo com uma sub-rede, as regras NSG aplicam a todas as VMs nessa sub-rede.
 
- **Diagnóstico.** Log de diagnóstico é essencial para gerenciar e solucionar problemas a máquina virtual.

## <a name="recommendations"></a>Recomendações

Azure oferece muitos recursos diferentes e tipos de recurso, portanto, essa arquitetura de referência pode ser provisionado muitas maneiras diferentes. Fornecemos um modelo do Gerenciador de recursos do Azure para instalar a arquitetura de referência que segue essas recomendações. Se você optar por criar sua própria arquitetura de referência que você deve seguir essas recomendações, a menos que tenha um requisito específico que não correspondam a uma recomendação. 

### <a name="vm-recommendations"></a>Recomendações de máquina virtual

Recomendamos as DS-GS séries e porque esses tamanhos de máquina suportam [Premium armazenamento][premium-storage]. Selecione uma desses tamanhos de máquina, a menos que tenha uma carga de trabalho especializada como computação de alto desempenho. Para obter detalhes, consulte [tamanhos de máquina Virtual][virtual-machine-sizes]. Ao mover uma carga de trabalho existente para o Azure, comece com o tamanho de máquina virtual que aproxima aos servidores no local. Em seguida, medir o desempenho de sua carga de trabalho real referentes à CPU, memória e disco operações por segundo (IOPS) de entradam/saídam e ajustar o tamanho, se necessário. Além disso, se você precisar várias placas de rede, lembre-se do limite de tamanho de cada NIC.  

Quando você provisionar a máquina virtual e outros recursos, especifique um local. Em geral, escolha um local mais próximo para seus usuários internos ou clientes. No entanto, nem todos os tamanhos de máquina virtual podem estar disponíveis em todos os locais. Para obter detalhes, consulte [serviços por região][services-by-region]. Para listar os tamanhos de máquina virtual disponíveis em um determinado local, execute o seguinte comando Azure interface de linha (comando):

```
    azure vm sizes --location <location>
```

Para obter informações sobre como escolher uma imagem de máquina virtual publicada, consulte [navegar e selecionar máquina virtual Azure imagens][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Recomendações de disco e de armazenamento

Para obter melhor desempenho de i / disco, recomendamos [Premium armazenamento][premium-storage], que armazena os dados em unidades de estado sólidas (SSDs). Custo baseia-se no tamanho do disco provisionado. IOPS e produtividade (ou seja, dados taxa de transferência) também dependerá no tamanho do disco, por isso quando você provisionar um disco, considere todos os três fatores (capacidade, IOPS e produtividade). 

Uma conta de armazenamento pode oferecer suporte a VMs de 1 a 20.

Adicione um ou mais discos de dados. Quando você cria um VHD, é formatado. Faça logon na máquina virtual para formatar o disco. Os discos de dados mostram como `/dev/sdc`, `/dev/sdd`e assim por diante. Você pode executar `lsblk` para listar os dispositivos de bloco, incluindo os discos. Para usar um disco de dados, crie um partição e sistema de arquivos e montar o disco. Por exemplo:

```bat
    # Create a partition.
    sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     
    
    # Create a file system.
    sudo mkfs -t ext3 /dev/sdc1
    
    # Mount the drive.
    sudo mkdir /data1
    sudo mount /dev/sdc1 /data1
```

Se você tiver muitos dos discos de dados, lembre-se dos limites e/s totais da conta de armazenamento. Para obter mais informações, consulte [Limites de disco de máquina Virtual][vm-disk-limits].

Quando você adiciona um disco de dados, uma unidade lógica número (LUN) ID é atribuída para o disco. Opcionalmente, você pode especificar a identificação de LUN &mdash; por exemplo, se você estiver substituindo um disco e deseja manter a mesma ID de LUN ou tiver um aplicativo que procura por uma ID de LUN específica. No entanto, lembre-se de que as IDs de LUN deve ser exclusivo para cada disco.

Talvez você queira alterar o Agendador de e/s, para otimizar o desempenho em unidades de estado sólidos (SSDs) (usado pelo armazenamento Premium). Uma recomendação comum é usar o Agendador NOOP SSDS, mas você deve usar uma ferramenta como [iostat] para monitorar o desempenho e/s de disco para sua carga de trabalho específica.

- Para obter melhor desempenho, crie uma conta de armazenamento separado para armazenar os logs de diagnóstico. Uma conta de armazenamento localmente redundante (LRS) padrão é suficiente para os logs de diagnóstico.


### <a name="network-recommendations"></a>Recomendações de rede

O endereço IP público pode ser dinâmico ou estático. O padrão é dinâmico.

- Reservar um [endereço IP estático] [ static-ip] se você precisa de um endereço IP fixo que não serão alteradas &mdash; por exemplo, se você precisa criar um registro no DNS ou precisa o endereço IP a ser lista branca.

- Você também pode criar um nome de domínio totalmente qualificado (FQDN) para o endereço IP. Você pode registrar um [registro CNAME] [ cname-record] no DNS que aponta para o FQDN. Para obter mais informações, consulte [criar um nome totalmente qualificado domínio no portal do Azure][fqdn].

Todos os NSGs contêm um conjunto de [regras padrão][nsg-default-rules], incluindo uma regra que bloqueia todo o tráfego de entrada do Internet. As regras padrão não podem ser excluídas, mas outras regras podem substituí-los. Para permitir o tráfego de Internet, crie regras que permitem o tráfego de entrada para portas específicas &mdash; por exemplo, a porta 80 para HTTP.  

Para habilitar SSH, adicione uma regra para o NSG que permite que o tráfego de entrada para a porta TCP 22.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

Você pode dimensionar uma máquina virtual para cima ou para baixo, [alterando o tamanho de máquina virtual][vm-resize]. 

Para dimensionar horizontalmente, coloque duas ou mais VMs uma disponibilidade definir atrás de um balanceador de carga. Para obter detalhes, consulte [executando várias VMs no Azure][multi-vm].

## <a name="availability-considerations"></a>Considerações de disponibilidade

Conforme mencionado acima, não há nenhum SLA para uma única VM. Para obter o SLA, você deve implantar várias VMs em um conjunto de disponibilidade.

Sua máquina virtual pode ser afetado por [manutenção planejada] [ planned-maintenance] ou [manutenção planejada][manage-vm-availability]. Você pode usar [logs de reinicialização de máquina virtual] [ reboot-logs] para determinar se uma reinicialização de máquina virtual foi causada por manutenção planejada.

VHDs contam com [O armazenamento do Azure][azure-storage], que é replicado para durabilidade e disponibilidade.

Para proteger contra perda de dados acidentais durante operações normais (por exemplo, por causa de erro de usuário), você também deve implementar backups no momento, usando o [blob instantâneos] [ blob-snapshot] ou outra ferramenta.

## <a name="manageability-considerations"></a>Considerações de capacidade de gerenciamento

**Grupos de recursos.** Colocar ligação estreita recursos que compartilham o mesmo ciclo de vida no mesmo [grupo de recursos][resource-manager-overview]. Grupos de recursos permitem que você a implantar e monitorar recursos como um grupo e acumular os custos por grupo de recursos de cobrança. Você também pode excluir recursos como um conjunto, que é muito útil para implantações de teste. Dê nomes significativos de recursos. Que torna mais fácil localizar um recurso específico e compreender sua função. Consulte [convenções de nomenclatura recomendadas para recursos Azure][naming conventions].

**SSH**. Antes de criar uma VM Linux, gere um par de chaves pública-particular do 2048 bits RSA. Use o arquivo de chave pública ao criar a máquina virtual. Para obter mais informações, consulte [como usar SSH com Linux e Mac no Azure][ssh-linux].

**Diagnóstico de máquina virtual.** Habilitar monitoramento e diagnóstico, incluindo métricas de integridade básica, logs de infraestrutura de diagnóstico e [diagnósticos de inicialização][boot-diagnostics]. Diagnósticos de inicialização podem ajudá-lo a diagnosticar Falha na inicialização se sua máquina virtual entra em um estado não-inicializável. Para obter mais informações, consulte [Ativar o monitoramento e diagnóstico][enable-monitoring].  

O seguinte comando CLI permite diagnósticos:

```
    azure vm enable-diag <resource-group> <vm-name>
```

**Interromper uma máquina virtual.** Azure faz uma distinção entre os estados "Parado" e "Deallocated". Cobrado quando o status de máquina virtual é "interrompido". Você não é cobrados quando a máquina virtual desalocada.

Use o seguinte comando CLI para desalocar uma máquina virtual:

```
    azure vm deallocate <resource-group> <vm-name>
```

- Botão **Parar** no portal do Azure também desaloca a máquina virtual. No entanto, se você desligar através do sistema operacional enquanto conectado, a máquina virtual for interrompida, mas _não_ desalocadas, assim você será ainda cobrado.

**Excluindo uma máquina virtual.** Se você excluir uma máquina virtual, os VHDs não são excluídos. Isso significa que você pode excluir com segurança a máquina virtual sem perder dados. No entanto, você ainda será cobrado para armazenamento. Para excluir o VHD, exclua o arquivo do [armazenamento]de blob[blob-storage].

Para impedir exclusões acidentais, use um [bloqueio de recurso] [ resource-lock] para bloquear os recurso inteiro grupo ou bloquear recursos individuais, como a máquina virtual. 

## <a name="security-considerations"></a>Considerações de segurança

Automatize atualizações de sistema operacional usando a extensão de máquina virtual [OSPatching] . Instale essa extensão quando você provisionar a máquina virtual. Você pode especificar a frequência de instalar patches e se reinicializar após a aplicação de patches.

Use [o controle de acesso baseado em função] [ rbac] (RBAC) para controlar o acesso aos recursos do Azure que você implantar. RBAC permite que você atribuir funções de autorização aos membros da equipe DevOps. Por exemplo, a função de leitor pode exibir recursos Azure, mas não criar, gerenciar ou excluí-las. Algumas funções são específicas para tipos de determinado recurso Azure. Por exemplo, a função Colaborador de máquina Virtual pode reiniciar ou desalocar uma máquina virtual, redefinir a senha de administrador, criar uma máquina virtual e assim por diante. Outras [funções internas de RBAC] [ rbac-roles] que podem ser úteis para essa arquitetura de referência inclui [DevTest Labs usuário] [ rbac-devtest] e [Colaboradores de rede][rbac-network]. 

Um usuário pode ser atribuído a várias funções, e você pode criar funções personalizadas para permissões refinadas ainda mais.

> [AZURE.NOTE] RBAC não limita as ações que um usuário conectado a uma máquina virtual pode executar. Essas permissões são determinadas pelo tipo de conta no sistema operacional convidado.   

Usar [logs de auditoria] [ audit-logs] para ver os provisionamento ações e outros eventos de máquina virtual.

- Considere a possibilidade de [Criptografia de disco do Azure] [ disk-encryption] se você precisar criptografar o sistema operacional e dados discos. 

## <a name="solution-deployment"></a>Implantação da solução

Uma [implantação] [ github-folder] para uma referência de arquitetura que demonstra estas práticas recomendadas está disponível. Essa arquitetura de referência inclui uma rede virtual (VNet), o grupo de segurança de rede (NSG) e uma única máquina virtual (VM).

Há várias maneiras para implantar essa arquitetura de referência. A maneira mais fácil é, siga estas etapas: 

1. Clique com botão direito no botão abaixo e selecione um dos "Abrir link em uma nova guia" ou "Abrir link em nova janela".
[![Implantar para o Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)

2. Depois que o link for aberto no portal do Azure, você deve inserir valores para algumas das configurações: 
    - O nome do **grupo de recursos** já está definido no arquivo de parâmetros, portanto selecione **Criar novo** e digite `ra-single-vm-rg` na caixa de texto.
    - Selecione a região na caixa suspensa **local** .
    - Não edite o **Modelo raiz Uri** ou as caixas de texto de **Uri de raiz de parâmetro** .
    - Selecione o **Tipo de sistema operacional** na lista suspensa caixa, **linux**.
    - Examine os termos e condições, clique na caixa de seleção **para concordar com os termos e condições mencionadas acima** .
    - Clique no botão de **compra** .

3. Aguarde a implantação ser concluída.

4. Arquivos de parâmetro incluem um nome de usuário de administrador embutidos e a senha, e é altamente recomendável que você altere imediatamente ambos. Clique na máquina virtual chamada `ra-single-vm0 `no Portal do Azure. Clique em **Redefinir senha** na seção **suporte + solução de problemas** . Selecione **Redefinir a senha** na caixa de lista suspensa **modo** e, em seguida, selecione um novo **nome de usuário** e **senha**. Clique no botão de **atualização** para manter o novo nome de usuário e senha.

Para obter informações sobre outras maneiras para implantar essa arquitetura de referência, consulte o arquivo Leiame na [orientação-único-máquina virtual] [ github-folder] Github pasta.

## <a name="customize-the-deployment"></a>Personalizar a implantação

Para alterar a implantação para atender às suas necessidades, siga as instruções na [orientação-único-máquina virtual] [ github-folder] página.

## <a name="next-steps"></a>Próximas etapas

Para que o [SLA para máquinas virtuais] [ vm-sla] para aplicar, você deve implantar duas ou mais ocorrências em um conjunto de disponibilidade. Para obter mais informações, consulte [executando várias VMs no Azure][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: ../articles/virtual-machines/virtual-machines-windows-create-availability-set.md
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-linux]: ../articles/virtual-machines/virtual-machines-linux-azure-overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-linux-portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]: ../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]: ../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-linux-sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_0/
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Única arquitetura de Linux VM no Azure"

