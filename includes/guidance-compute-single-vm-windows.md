Este artigo descreve um conjunto de práticas comprovadas para executar uma máquina virtual do Windows (máquina virtual) no Azure, prestar atenção para escalabilidade, disponibilidade, capacidade de gerenciamento e segurança. 

> [AZURE.NOTE] Azure tem dois diferentes modelos de implantação: [Gerenciador de recursos do Azure] [ resource-manager-overview] e clássico. Este artigo usa o Gerenciador de recursos, a Microsoft recomenda para novas implantações.

Não recomendamos usando uma única VM para cargas de trabalho de produção, porque não há nenhum contrato de nível de serviço de tempo (SLA) para VMs único no Azure. Para obter o SLA, você deve implantar várias VMs em um [conjunto de disponibilidade][availability-set]. Para obter mais informações, consulte [executando várias VMs do Windows no Azure][multi-vm]. 

## <a name="architecture-diagram"></a>Diagrama de arquitetura

Provisionar uma máquina virtual no Azure envolve mais partes móveis que apenas a máquina virtual em si. Há elementos de computação, rede e armazenamento.

> Um documento do Visio que inclui este diagrama de arquitetura está disponível para download no [Centro de download da Microsoft][visio-download]. Este diagrama é em "computação - única página de máquina virtual.

![[0]][0]


- **Grupo de recursos.** Um [_grupo de recursos_] [ resource-manager-overview] é um recipiente que contém recursos relacionados. Crie um grupo de recursos para manter os recursos para esta máquina virtual.

- **VM**. Você pode provisionar uma máquina virtual em uma lista de imagens publicadas ou de um arquivo de disco rígido virtual (VHD) que você carregar ao armazenamento de blob do Microsoft Azure.

- **Disco do sistema operacional.** O disco de sistema operacional é um VHD armazenado no [armazenamento do Azure][azure-storage]. Isso significa que ele persista, mesmo se o computador host diminui.

- **Disco temporário.** A máquina virtual é criada com um disco temporário (o `D:` unidade no Windows). Este disco está armazenado em uma unidade física na máquina host. É _não_ salvas armazenamento do Azure e podem desaparecer durante a reinicialização e outros eventos de ciclo de vida de máquina virtual. Use este disco somente para dados temporários, como arquivos de página ou troca.

- **Discos de dados.** Um [disco de dados] [ data-disk] é um VHD persistente usado para dados de aplicativo. Discos de dados são armazenados no armazenamento do Azure, como o disco de sistema operacional.

- **Rede virtual (VNet) e sub-rede.** Cada máquina virtual no Azure é implantado em um VNet, que está dividida em sub-redes.

- **Endereço IP público.** Um endereço IP público é necessária para se comunicar com a máquina virtual&mdash;por exemplo pela área de trabalho remota (RDP).

- **Interface de rede (NIC)**. NIC habilita a máquina virtual para se comunicar com a rede virtual.

- **Grupo de segurança de rede (NSG)**. O [NSG] [ nsg] é usado para permitir/negar o tráfego de rede à sub-rede. Você pode associar um NSG com uma NIC individual ou com uma sub-rede. Se você associá-lo com uma sub-rede, as regras NSG aplicam a todas as VMs nessa sub-rede.
 
- **Diagnóstico.** Log de diagnóstico é essencial para gerenciar e solucionar problemas a máquina virtual.

## <a name="recommendations"></a>Recomendações

Azure oferece muitos recursos diferentes e tipos de recurso, portanto, essa arquitetura de referência pode ser provisionado muitas maneiras diferentes. Fornecemos um modelo do Gerenciador de recursos do Azure para instalar a arquitetura de referência que segue essas recomendações. Se você optar por criar sua própria arquitetura de referência que você deve seguir essas recomendações, a menos que tenha um requisito específico que não correspondam a uma recomendação.

### <a name="vm-recommendations"></a>Recomendações de máquina virtual

Recomendamos as DS-GS séries e porque esses tamanhos de máquina suportam [Premium armazenamento][premium-storage]. Selecione uma desses tamanhos de máquina, a menos que tenha uma carga de trabalho especializada como computação de alto desempenho. Para obter detalhes, consulte [tamanhos de máquina Virtual][virtual-machine-sizes]. Ao mover uma carga de trabalho existente para o Azure, comece com o tamanho de máquina virtual que aproxima aos servidores no local. Em seguida, medir o desempenho de sua carga de trabalho real relacionadas com CPU, memória e disco operações por segundo (IOPS) de entrada/saída e ajustar o tamanho, se necessário. Além disso, se você precisar várias placas de rede, lembre-se do limite de tamanho de cada NIC.  

Quando você provisionar a máquina virtual e outros recursos, especifique um local. Em geral, escolha um local mais próximo para seus usuários internos ou clientes. No entanto, nem todos os tamanhos de máquina virtual podem estar disponíveis em todos os locais. Para obter detalhes, consulte [serviços por região][services-by-region]. Para listar os tamanhos de máquina virtual disponíveis em um determinado local, execute o seguinte comando Azure interface de linha (comando):

```
    azure vm sizes --location <location>
```

Para obter informações sobre como escolher uma imagem de máquina virtual publicada, consulte [navegar e selecionar máquina virtual Azure imagens][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Recomendações de disco e de armazenamento

Para obter melhor desempenho de i / disco, recomendamos [Premium armazenamento][premium-storage], que armazena os dados em unidades de estado sólido (SSDs). Custo baseia-se no tamanho do disco provisionado. IOPS e produtividade também dependem de tamanho de disco, por isso quando você provisionar um disco, considere todos os três fatores (capacidade, IOPS e produtividade). 

Uma conta de armazenamento pode oferecer suporte a VMs de 1 a 20.

Adicione um ou mais discos de dados. Quando você cria um novo VHD, é formatado. Faça logon na máquina virtual para formatar o disco.

Se você tiver um grande número de discos de dados, lembre-se dos limites e/s totais da conta de armazenamento. Para obter mais informações, consulte [Limites de disco de máquina Virtual][vm-disk-limits].

Para obter melhor desempenho, crie uma conta de armazenamento separado para armazenar os logs de diagnóstico. Uma conta de armazenamento localmente redundante (LRS) padrão é suficiente para os logs de diagnóstico.

Quando possível, instale aplicativos em um disco de dados, não o disco de sistema operacional. No entanto, alguns aplicativos herdados talvez seja necessário instalar componentes na unidade c:. Nesse caso, você pode [redimensionar o disco de sistema operacional] [ resize-os-disk] usando o PowerShell.

### <a name="network-recommendations"></a>Recomendações de rede

O endereço IP público pode ser dinâmico ou estático. O padrão é dinâmico.

- Reservar um [endereço IP estático] [ static-ip] se você precisa de um endereço IP fixo que não serão alteradas &mdash; por exemplo, se você precisa criar um registro no DNS ou precisa o endereço IP a ser lista branca.

- Você também pode criar um nome de domínio totalmente qualificado (FQDN) para o endereço IP. Você pode registrar um [registro CNAME] [ cname-record] no DNS que aponta para o FQDN. Para obter mais informações, consulte [criar um nome totalmente qualificado domínio no portal do Azure][fqdn].

Todos os NSGs contêm um conjunto de [regras padrão][nsg-default-rules], incluindo uma regra que bloqueia todo o tráfego de entrada do Internet. As regras padrão não podem ser excluídas, mas outras regras podem substituí-los. Para permitir o tráfego de Internet, crie regras que permitem o tráfego de entrada para portas específicas &mdash; por exemplo, a porta 80 para HTTP.  

Para habilitar RDP, adicione uma regra NSG que permita o tráfego de entrada na porta TCP 3389.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

Você pode dimensionar uma máquina virtual para cima ou para baixo, [alterando o tamanho de máquina virtual][vm-resize]. 

Para dimensionar horizontalmente, coloque VMs duas ou mais uma disponibilidade definir atrás de um balanceador de carga. Para obter detalhes, consulte [executando várias VMs do Windows no Azure][multi-vm].

## <a name="availability-considerations"></a>Considerações de disponibilidade

Conforme mencionado acima, não há nenhum SLA para uma única VM. Para obter o SLA, você deve implantar várias VMs em um conjunto de disponibilidade.

Sua máquina virtual pode ser afetado por [manutenção planejada] [ planned-maintenance] ou [manutenção planejada][manage-vm-availability]. Você pode usar [logs de reinicialização de máquina virtual] [ reboot-logs] para determinar se uma reinicialização de máquina virtual foi causada por manutenção planejada.

VHDs contam com [O armazenamento do Azure][azure-storage], que é replicado para durabilidade e disponibilidade.

Para proteger contra perda de dados acidentais durante operações normais (por exemplo, por causa de erro de usuário), você também deve implementar backups no momento, usando o [blob instantâneos] [ blob-snapshot] ou outra ferramenta.

## <a name="manageability-considerations"></a>Considerações de capacidade de gerenciamento

**Grupos de recursos.** Colocar recursos agrupados que compartilham o mesmo ciclo de vida em um mesmo [grupo de recursos][resource-manager-overview]. Grupos de recursos permitem que você implantar e monitorar recursos como um grupo e acumular os custos por grupo de recursos de cobrança. Você também pode excluir recursos como um conjunto, que é muito útil para implantações de teste. Dê nomes significativos de recursos. Que torna mais fácil localizar um recurso específico e compreender sua função. Consulte [convenções de nomenclatura recomendadas para recursos Azure][naming conventions].

**Diagnóstico de máquina virtual.** Habilitar monitoramento e diagnóstico, incluindo métricas de integridade básica, logs de infraestrutura de diagnóstico e [diagnósticos de inicialização][boot-diagnostics]. Diagnósticos de inicialização podem ajudá-lo a diagnosticar uma falha na inicialização se sua máquina virtual entra em um estado não-inicializável. Para obter mais informações, consulte [Ativar o monitoramento e diagnóstico][enable-monitoring]. Usar a [Coleção de Log do Azure] [ log-collector] extensão para coletar logs de plataforma Windows Azure e carregue-os para o armazenamento do Azure.   

O seguinte comando CLI permite diagnósticos:

```
    azure vm enable-diag <resource-group> <vm-name>
```

**Interrompendo uma máquina virtual.** Azure faz uma distinção entre os estados "Parado" e "Deallocated". Cobrado quando o status de máquina virtual é "interrompido". Você não é cobrados quando a máquina virtual desalocada.

Use o seguinte comando CLI para desalocar uma máquina virtual:

```
    azure vm deallocate <resource-group> <vm-name>
```

Botão **Parar** no portal do Azure também desaloca a máquina virtual. No entanto, se você desligar através do sistema operacional enquanto conectado, a máquina virtual for interrompida, mas _não_ desalocadas, assim você será ainda cobrado.

**Excluindo uma máquina virtual.** Se você excluir uma máquina virtual, os VHDs não são excluídos. Isso significa que você pode excluir com segurança a máquina virtual sem perder dados. No entanto, você ainda será cobrado para armazenamento. Para excluir o VHD, exclua o arquivo do [armazenamento]de blob[blob-storage].

Para impedir exclusões acidentais, use um [bloqueio de recurso] [ resource-lock] para bloquear os recurso inteiro grupo ou bloquear recursos individuais, como a máquina virtual. 

## <a name="security-considerations"></a>Considerações de segurança

Usar [O Centro de segurança do Azure] [ security-center] para obter uma visão central do estado de segurança de seus recursos Azure. Centro de segurança monitora possíveis problemas de segurança como sistema atualizações, antimalware e oferece um panorama abrangente da integridade segurança de sua implantação. 

- Central de segurança é configurada por assinatura Azure. Habilite coleta de dados de segurança conforme descrito na [Central de segurança de uso].

- Quando a coleta de dados está habilitada, o Centro de segurança examina automaticamente qualquer VMs criadas sob essa assinatura.

**Gerenciamento de patches.** Se habilitada, a Central de segurança verifica se as atualizações de segurança e críticas estão ausentes. Usar [configurações de política de grupo] [ group-policy] sobre a máquina virtual para habilitar atualizações automáticas do sistema.

**Antimalware.** Se habilitada, a Central de segurança verifica se o software antimalware está instalado. Você também pode usar o Centro de segurança para instalar o software de antimalware de dentro do portal do Azure.

Use [o controle de acesso baseado em função] [ rbac] (RBAC) para controlar o acesso aos recursos do Azure que você implantar. RBAC permite que você atribuir funções de autorização aos membros da equipe DevOps. Por exemplo, a função de leitor pode exibir recursos Azure, mas não criar, gerenciar ou excluí-las. Algumas funções são específicas para tipos de determinado recurso Azure. Por exemplo, a função Colaborador de máquina Virtual pode reiniciar ou desalocar uma máquina virtual, redefinir a senha de administrador, criar uma nova VM e assim por diante. Outras [funções internas de RBAC] [ rbac-roles] que podem ser úteis para essa arquitetura de referência inclui [DevTest Labs usuário] [ rbac-devtest] e [Colaboradores de rede][rbac-network]. Um usuário pode ser atribuído a várias funções, e você pode criar funções personalizadas para permissões refinadas ainda mais.

> [AZURE.NOTE] RBAC não limita as ações que um usuário conectado a uma máquina virtual pode executar. Essas permissões são determinadas pelo tipo de conta no sistema operacional convidado.   

Para redefinir a senha de administrador local, execute o `vm reset-access` comando CLI do Azure.

```
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
```

Usar [logs de auditoria] [ audit-logs] para ver os provisionamento ações e outros eventos de máquina virtual.

Considere a possibilidade de [Criptografia de disco do Azure] [ disk-encryption] se você precisar criptografar o sistema operacional e dados discos. 

## <a name="solution-deployment"></a>Implantação da solução

Uma [implantação] [ github-folder] para uma referência de arquitetura que demonstra estas práticas recomendadas está disponível. Essa arquitetura de referência inclui uma rede virtual (VNet), o grupo de segurança de rede (NSG) e uma única máquina virtual (VM).

Há várias maneiras para implantar essa arquitetura de referência. A maneira mais fácil é, siga estas etapas: 

1. Clique com botão direito no botão abaixo e selecione um dos "Abrir link em uma nova guia" ou "Abrir link em nova janela".  
[![Implantar para o Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)

2. Depois que o link for aberto no portal do Azure, você deve inserir valores para algumas das configurações: 
    - O nome do **grupo de recursos** já está definido no arquivo de parâmetros, portanto selecione **Criar novo** e digite `ra-single-vm-rg` na caixa de texto.
    - Selecione a região na caixa suspensa **local** .
    - Não edite o **Modelo raiz Uri** ou as caixas de texto de **Uri de raiz de parâmetro** .
    - Selecione o **Tipo de sistema operacional** na lista suspensa de caixa, o **windows**.
    - Examine os termos e condições, clique na caixa de seleção **para concordar com os termos e condições mencionadas acima** .
    - Clique no botão de **compra** .

3. Aguarde a implantação ser concluída.

4. Arquivos de parâmetro incluem um nome de usuário de administrador embutidos e a senha, e é altamente recomendável que você altere imediatamente ambos. Clique na máquina virtual chamada `ra-single-vm0 `no Portal do Azure. Em seguida, clique em **Redefinir senha** na lâmina **suporte + solução de problemas** . Selecione **Redefinir a senha** na caixa de lista suspensa **modo** e, em seguida, selecione um novo **nome de usuário** e **senha**. Clique no botão de **atualização** para manter o novo nome de usuário e senha.

Para obter informações sobre outras maneiras para implantar essa arquitetura de referência, consulte o arquivo Leiame na [orientação-único-máquina virtual][github-folder]] Github pasta. 

## <a name="customize-the-deployment"></a>Personalizar a implantação

Se você precisar alterar a implantação para atender às suas necessidades, siga as instruções no [arquivo Leiame][github-folder]. 

## <a name="next-steps"></a>Próximas etapas

Para que o [SLA para máquinas virtuais] [ vm-sla] para aplicar, você deve implantar duas ou mais ocorrências de um conjunto de disponibilidade. Para obter mais informações, consulte [executando várias VMs no Azure][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: ../articles/virtual-machines/virtual-machines-windows-create-availability-set.md
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-windows-portal-create-fqdn.md
[github-folder]: http://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm
[group-policy]: https://technet.microsoft.com/en-us/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/en-us/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[resize-os-disk]: ../articles/virtual-machines/virtual-machines-windows-expand-os-disk.md
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[security-center]: https://azure.microsoft.com/en-us/services/security-center/
[select-vm-image]: ../articles/virtual-machines/virtual-machines-windows-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[Use a Central de segurança]: ../articles/security-center/security-center-get-started.md#use-security-center
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-windows-sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_0/
[0]: ./media/guidance-blueprints/compute-single-vm.png "Única arquitetura de máquina virtual do Windows no Azure"
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[blocks]: https://github.com/mspnp/template-building-blocks
