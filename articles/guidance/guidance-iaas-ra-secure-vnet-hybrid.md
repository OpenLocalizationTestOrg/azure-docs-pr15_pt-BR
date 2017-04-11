<properties
   pageTitle="Implementar uma arquitetura de rede de seguro híbrido no Azure | Microsoft Azure"
   description="Como implementar uma arquitetura de rede híbrido seguro no Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-dmz-between-azure-and-your-on-premises-datacenter"></a>Implementação de uma DMZ entre Azure e seu centro de dados local

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve as práticas recomendadas para implementar uma rede híbrido seguro que estende a uma rede local para o Azure. Essa arquitetura de referência implementa DMZ entre uma rede local e uma rede virtual Azure usando rotas definidas pelo usuário (UDRs). DMZ inclui rede altamente disponível dispositivos virtuais (NVAs) implementam a funcionalidade de segurança como firewalls e inspeção de pacote. Todo o tráfego de saída do VNet é encapsulado forçar com a Internet através da rede local, portanto, pode ser auditada. 

Essa arquitetura requer uma conexão ao data center local implementado usando um [gateway VPN][ra-vpn], ou uma [rota expressa] [ ra-expressroute] conexão.

> [AZURE.NOTE] Azure tem dois diferentes modelos de implantação: [Gerenciador de recursos] [ resource-manager-overview] e clássico. Esta arquitetura de referência usa o Gerenciador de recursos, a Microsoft recomenda para novas implantações.

Casos de uso típico para essa arquitetura incluem:

- Aplicativos híbridos onde executar cargas de trabalho parcialmente local e parcialmente no Azure.

- Infraestrutura Azure que rotear o tráfego de entrada de locais e na internet.

- Aplicativos necessários para tráfego de saída de auditoria. Isso geralmente é um requisito de regulamentação de muitos sistemas comerciais e pode ajudar a evitar divulgação pública de informações particulares.

## <a name="architecture-diagram"></a>Diagrama de arquitetura

O diagrama a seguir destaca os componentes importantes nesta arquitetura:

> Um documento do Visio que inclui este diagrama de arquitetura está disponível para download no [Centro de download da Microsoft][visio-download]. Este diagrama é na página "DMZ – particular".

[! [0]][0]

- **Rede local.** Esta é uma rede de computadores e dispositivos conectados por meio de uma privada rede local implementado em uma organização.

- **Rede virtual Azure (VNet).** O VNet hospedará os aplicativos e outros recursos em execução na nuvem.

- **Gateway.** O gateway fornece conectividade entre os roteadores na rede local e o VNet.

- **Dispositivo de rede virtual (NVA).** NVA é um termo genérico que descreve uma máquina virtual realizar tarefas como permitir ou negar acesso como um firewall, otimiza operações de WAN (incluindo a compactação de rede), roteamento personalizado ou outros tipos de funcionalidade de rede. 

- **Camada da Web, camada de negócios e sub-redes de camada de dados.** Estas são sub-redes hospedar o VMs e serviços que implementar um exemplo de aplicativo de nível 3 em execução na nuvem. Consulte [Executando o Windows VMs para uma arquitetura de N camadas no Azure] [ ra-n-tier] para obter mais informações.

- **Rotas definidas pelo usuário (UDR).** [Rotas definidas pelo usuário] [ udr-overview] definem o fluxo de tráfego IP dentro do Azure VNets. 

> [AZURE.NOTE] Dependendo dos requisitos da sua conexão de VPN, você pode configurar rotas de borda Gateway Protocol (BGP) como uma alternativa para usar UDRs para implementar as regras de encaminhamento que direcionar o tráfego de volta através da rede local.

- **Sub-rede de gerenciamento.** Esta sub-rede contém VMs que implementam gerenciamento e recursos de monitoramento para os componentes executando na VNet. 

## <a name="recommendations"></a>Recomendações

Azure oferece muitos recursos diferentes e tipos de recurso, portanto, essa arquitetura de referência pode ser provisionado muitas maneiras diferentes. Fornecemos um modelo do Gerenciador de recursos do Azure para instalar a arquitetura de referência que segue essas recomendações. Se você optar por criar sua própria arquitetura de referência que você deve seguir essas recomendações, a menos que tenha um requisito específico que não correspondam a uma recomendação.

### <a name="rbac-recommendations"></a>Recomendações de RBAC

Crie várias funções RBAC para gerenciar os recursos em seu aplicativo. Considere a criação de uma [função personalizada] do DevOps[ rbac-custom-roles] com permissões para administrar a infraestrutura para o aplicativo. Considere a criação de um centralizado administrador [função personalizada] [ rbac-custom-roles] para gerenciar recursos de rede e um administrador [função personalizada] de segurança separada[ rbac-custom-roles] para gerenciar recursos de rede segura como os NVAs. 

A função DevOps deve incluir permissões para implantar os componentes do aplicativo, bem como o monitor e reinicie VMs. A função de administrador IT centralizada deve incluir permissões para monitorar os recursos de rede. Nenhuma dessas funções devem ter acesso aos recursos NVA como isso deve ser restrito para a função administrador de segurança.

### <a name="resource-group-recommendations"></a>Recomendações do grupo de recursos

Azure recursos como VMs, VNets e balanceadores de carga podem ser gerenciados facilmente agrupando-as em grupos de recursos. Você pode atribuir as funções acima para cada grupo de recursos para restringir o acesso.

Recomendamos a criação dos seguintes procedimentos:

- Um grupo de recursos contendo as sub-redes (excluindo as VMs), NSGs e os recursos de gateway para se conectar à rede local. Atribua a função de administrador IT centralizada ao grupo de recursos.

- Um grupo de recursos contendo as VMs para os NVAs (incluindo o balanceador de carga), a caixa de salto e outras VMs de gerenciamento e a UDR para a sub-rede de gateway que força todo o tráfego através do NVAs. Atribua a função administrador de segurança ao grupo de recursos.

- Grupos de recursos separada para cada camada de aplicativo que contêm o balanceador de carga e VMs. Observe que esse grupo de recursos não deve incluir as sub-redes para cada nível. Atribua a função DevOps ao grupo de recursos.

### <a name="virtual-network-gateway-recommendations"></a>Recomendações de gateway de rede virtual

Tráfego de local passa para a VNet através de um gateway de rede virtual. Recomendamos que um [gateway VPN Azure] [ guidance-vpn-gateway] ou um [gateway de rota expressa do Azure][guidance-expressroute]. 

### <a name="nva-recommendations"></a>Recomendações NVA

NVAs fornecem diferentes serviços para gerenciar e monitorar o tráfego de rede. Do Azure Marketplace oferece várias NVAs de fornecedor de terceiros, incluindo:

- [Firewall de aplicativo da Web Barracuda] [ barracuda-waf] e [Barracuda NextGen Firewall][barracuda-nf]

- [Redes consistente VNS3 Firewall/roteador/VPN][vns3]

- [Fortinet FortiGate-máquina virtual][fortinet]

- [Firewall de aplicativo Web SecureSphere][securesphere]

- [Firewall de aplicativo Web DenyAll][denyall]

- [VSEC de ponto de verificação][checkpoint]

Se nenhuma das NVAs terceiros atender às suas necessidades, você pode criar um NVA personalizado usando VMs. Para obter um exemplo de criação de NVAs personalizados, consulte DMZ nesta arquitetura de referência que implementa a funcionalidade a seguir:

- Tráfego é roteado usando o [encaminhamento de IP] [ ip-forwarding] nas NICs NVA.

- Tráfego é permitido para passar o NVA somente se for apropriado. Cada VM NVA na arquitetura de referência é um roteador Linux simples com o tráfego de entrada recebida em rede interface *eth0*e regras de correspondência de tráfego de saída definidas pelo scripts personalizados enviados por meio de interface de rede *eth1*. 

- Tráfego roteado à sub-rede gerenciamento não passar os NVAs e os NVAs só podem ser configuradas da sub-rede gerenciamento. Se o tráfego para a sub-rede de gerenciamento é necessária para ser encaminhada pelos NVAs, não há nenhuma rota à sub-rede gerenciamento para corrigir os NVAs se eles falhar.  

- VMs para o NVA são incluídas em uma disponibilidade definir atrás de um balanceador de carga. O UDR na sub-rede gateway direciona solicitações NVA ao balanceador de carga.

Outra recomendação considerar estiver se conectando a vários NVAs série com cada NVA executando uma tarefa de segurança especializada. Isso permite que cada função de segurança a ser gerenciado em uma base por NVA. Por exemplo, um NVA implementar um firewall pode ser colocado na série com um NVA executando serviços de identidade. A desvantagem para facilitar o gerenciamento é a adição extra de saltos de rede que podem aumentar a latência, portanto, certifique-se de que isso não afeta o desempenho do aplicativo.

### <a name="nsg-recommendations"></a>Recomendações de NSG

O gateway VPN expõe um endereço IP público para a conexão à rede local. É recomendável criar um grupo de segurança de rede (NSG) para entrada NVA sub-rede Implementando regras para bloquear todo o tráfego não provenientes da rede local.

Também é recomendável que você implemente NSGs para cada sub-rede para fornecer um segundo nível de proteção contra tráfego de entrada, ignorando um NVA desabilitado ou configurado incorretamente. Por exemplo, a sub-rede de camada web na arquitetura de referência implementa um NSG com uma regra para ignorar todas as solicitações diferente daquelas recebido de rede local (192.168.0.0/16) ou o VNet e outra regra que ignora todas as solicitações não feitas na porta 80. 

### <a name="internet-access-recommendations"></a>Recomendações de acesso de Internet

[Forçar túnel] [ azure-forced-tunneling] tranlation endereço (NAT) de rede de todo o tráfego de saída de internet por meio de sua rede local usando o túnel VPN-to-site e a rota com a internet usando. Isso tanto impedirá acidental de quaisquer informações confidenciais armazenadas em sua camada de dados e também permite a inspeção e auditoria de todo o tráfego de saída.

> [AZURE.NOTE] Não bloquear completamente o tráfego de Internet dos níveis de web, de negócios e de aplicativos. Se esses níveis usam os serviços de PaaS Azure contam com endereços IP públicos para registro em log de diagnóstico de máquina virtual, baixe-o de extensões de máquina virtual e outros tipos de funcionalidade. Diagnóstico do Azure também requer que componentes podem ler e gravar a uma conta de armazenamento do Azure dependente de internet.

Ainda mais, recomendamos que você verifique se o tráfego de internet saída está encapsulado forçar corretamente. Se você estiver usando uma conexão VPN com o [serviço de acesso remoto e de roteamento] [ routing-and-remote-access-service] em um servidor local, use uma ferramenta como [WireShark] [ wireshark] ou [Analisador de mensagem do Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=44226).

### <a name="management-subnet-recommendations"></a>Recomendações de sub-rede de gerenciamento

A sub-rede de gerenciamento contém uma caixa de salto que executa o gerenciamento e a funcionalidade de monitoramento. Implemente as seguintes recomendações para a caixa de salto:
- Não crie um endereço IP público para a caixa de salto. 
- Crie uma rota para acessar a caixa de salto através do gateway de entrada e implementar um NSG na sub-rede gerenciamento apenas responder às solicitações do roteiro permitido.
- Restringir a execução de todas as tarefas de gerenciamento seguro para a caixa de salto.

### <a name="nva-recommendations"></a>Recomendações NVA

Incluir uma camada 7 NVA encerrar conexões de aplicativos no nível de NVA e manter afinidade com as camadas de back-end. Isso garante conectividade simétrica na qual o tráfego de resposta dos níveis de back-end retorna através do NVA.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

A arquitetura de referência implementa um balanceador de carga que direciona o tráfego de rede local para um pool de dispositivos NVA. Conforme discutido anteriormente, os dispositivos NVA são VMs executar regras de roteamento de tráfego de rede e são implantados em um [conjunto de disponibilidade][availability-set]. Este design permite que você monitorar a produtividade dos NVAs ao longo do tempo e adicionar dispositivos NVA em resposta a aumentos de carga.

O gateway padrão do SKU VPN oferece suporte a produtividade estável de até 100 Mbps. Alto desempenho SKU fornece até 200 Mbps. Para larguras de banda maiores, considere atualizar para um gateway rota expressa. Rota expressa fornece largura de banda até 10 Gbps latência mais baixa que uma conexão de VPN.

> [AZURE.NOTE] Os artigos [implementar uma arquitetura de rede híbrida com o Azure e local VPN] [ guidance-vpn-gateway] e [implementar uma arquitetura de rede híbrida com rota expressa do Azure] [ guidance-expressroute] descrevem problemas ao redor a escalabilidade de gateways Azure.

## <a name="availability-considerations"></a>Considerações de disponibilidade

A arquitetura de referência implementa um balanceador de carga distribuir solicitações do local a um pool de dispositivos NVA no Azure. Os dispositivos NVA são VMs executar regras de roteamento de tráfego de rede e são implantados em um [conjunto de disponibilidade][availability-set]. O balanceador de carga regularmente consulta um teste de integridade implementado em cada NVA e removerá qualquer NVAs não responde do pool. 

Se você estiver usando a rota expressa do Azure para fornecer conectividade entre a rede VNet e local, [configure um gateway VPN para oferecer failover] [ guidance-vpn-failover] se a conexão de rota expressa ficar indisponível.

Para obter informações específicas sobre como manter a disponibilidade para conexões VPN e rota expressa, consulte os artigos [implementar uma arquitetura de rede híbrida com o Azure e local VPN] [ guidance-vpn-gateway] e [implementar uma arquitetura de rede híbrida com rota expressa do Azure][guidance-expressroute].

## <a name="manageability-considerations"></a>Considerações de capacidade de gerenciamento

Todos os aplicativos e recursos monitoramento devem ser executadas pela caixa salto na sub-rede gerenciamento. Dependendo de seus requisitos do aplicativo, talvez você precise adicionar recursos de monitoramento adicionais na sub-rede gerenciamento, mas novamente qualquer um desses recursos adicionais devem ser acessado através da caixa de salto.

Se a conectividade de gateway da sua rede local para o Azure for pressionada, ainda pode chegar na caixa de salto Implantando um PIP, adicioná-lo à caixa de salto e remota da internet.

Sub-rede da cada camada na arquitetura de referência estiver protegida por regras NSG e pode ser necessário criar uma regra para abrir a porta 3389 para acesso RDP no Windows VMs ou 22 para acesso SSH em VMs Linux. Outras ferramentas de gerenciamento e monitoramento podem exigir regras para abrir portas adicionais.

Se você estiver usando a rota expressa para fornecer a conectividade entre o seu centro de dados local e o Azure, use o [Kit de ferramentas do Azure conectividade (AzureCT)] [ azurect] para monitorar e solucionar problemas de conexão.

> [AZURE.NOTE] Você pode encontrar informações adicionais dirigidas especificamente monitorar e gerenciar conexões VPN e rota expressa nos artigos [implementar uma arquitetura de rede híbrida com o Azure e local VPN] [ guidance-vpn-gateway] e [implementar uma arquitetura de rede híbrida com rota expressa do Azure][guidance-expressroute].

## <a name="security-considerations"></a>Considerações de segurança

Essa arquitetura de referência implementa vários níveis de segurança: 

### <a name="routing-all-on-premises-user-requests-through-the-nva"></a>Roteamento de todas as solicitações de usuário locais por meio do NVA

O UDR na sub-rede gateway bloqueia todas as solicitações de usuário diferente daquelas recebido de local. Os passos de UDR permitido solicitações para as NVAs na sub-rede DMZ particular e essas solicitações são passadas para o aplicativo se eles forem permitidos por regras NVA. Outras rotas podem ser adicionadas ao UDR, mas certifique-se de que eles não ignoram inadvertidamente a NVAs ou bloquear tráfego administrativo destinados a sub-rede de gerenciamento.

O balanceador de carga na frente do NVAs também funciona como um dispositivo de segurança ignorando tráfego em portas que não estão abertos em regras de balanceamento de carga. Os balanceadores de carga na arquitetura de referência escutam apenas solicitações de HTTP na porta 80 e HTTPS na porta 443. Qualquer regras adicionais para os balanceadores de carga devem ser documentadas e o tráfego deve ser monitorado para garantir que não há problemas de segurança.

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>Usando NSGs para bloquear/fase de tráfego entre os níveis de aplicativo

Cada uma das camadas da web, negócios e dados restringir o tráfego entre eles usando NSGs. Isto é, a camada de negócios usa um NSG para bloquear todo o tráfego que não se originam na camada da web e a camada de dados usa um NSG para bloquear todo o tráfego que não se originam na camada de negócios. Se você tiver um requisito para expandir as regras NSG para permitir acesso mais amplo a esses níveis, ponderar esses requisitos contra os riscos de segurança. Cada novo caminho de entrada representa uma oportunidade para danos de aplicativo ou perda de dados acidental ou proposital.

### <a name="devops-access"></a>Acesso de DevOps

Restringir as operações que DevOps pode executar em cada nível usando [RBAC] [ rbac] para gerenciar permissões. Ao conceder permissões, use o [princípio do privilégio mínimo][security-principle-of-least-privilege]. Faça todas as operações administrativas e execute auditorias regulares para garantir que as alterações de configuração foram planejadas.

> [AZURE.NOTE] Para informações mais ampla, exemplos e cenários sobre o gerenciamento de segurança de rede com o Azure, consulte [segurança de rede e serviços de nuvem da Microsoft][cloud-services-network-security]. Para obter informações detalhadas sobre como proteger recursos na nuvem, consulte [Introdução ao Microsoft Azure segurança][getting-started-with-azure-security]. Para obter detalhes adicionais sobre endereçamento preocupações com a segurança em uma conexão de gateway Azure, consulte [implementar uma arquitetura de rede híbrida com o Azure e local VPN] [ guidance-vpn-gateway] e [implementar uma arquitetura de rede híbrida com rota expressa do Azure][guidance-expressroute].

## <a name="solution-deployment"></a>Implantação da solução

Uma implantação para uma arquitetura de referência que implementa essas recomendações está disponível no Github. Essa arquitetura de referência inclui uma rede virtual (VNet), o grupo de segurança de rede (NSG), balanceador de carga e duas máquinas virtuais (VMs).

A arquitetura de referência pode ser implantada com Windows ou Linux VMs seguindo as instruções abaixo: 

1. Clique com botão direito no botão abaixo e selecione um dos "Abrir link em uma nova guia" ou "Abrir link em nova janela":  
[![Implantar para o Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet%2Fazuredeploy.json)

2. Depois que o link for aberto no portal do Azure, você deve inserir valores para algumas das configurações: 
    - O nome do **grupo de recursos** já está definido no arquivo de parâmetros, portanto selecione **Criar novo** e digite `ra-private-dmz-rg` na caixa de texto.
    - Selecione a região na caixa suspensa **local** .
    - Não edite o **Modelo raiz Uri** ou as caixas de texto de **Uri de raiz de parâmetro** .
    - Examine os termos e condições, clique na caixa de seleção **para concordar com os termos e condições mencionadas acima** .
    - Clique no botão de **compra** .

3. Aguarde a implantação ser concluída.

4. Arquivos de parâmetro incluem o nome de usuário de administrador embutidos e senha para todas as VMs e é altamente recomendável que você altere imediatamente ambos. Para cada máquina virtual na implantação, selecione-o no portal do Azure e clique em **Redefinir senha** na lâmina **suporte + solução de problemas** . Selecione **Redefinir a senha** na caixa de lista suspensa **modo** e, em seguida, selecione um novo **nome de usuário** e **senha**. Clique no botão de **atualização** para persistir.

## <a name="next-steps"></a>Próximas etapas

- Saiba como implementar um [DMZ entre Azure e a Internet](./guidance-iaas-ra-secure-vnet-dmz.md).
- Saiba como implementar uma [arquitetura de rede híbrido altamente disponível](./guidance-hybrid-network-expressroute-vpn-failover.md).

<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-create-availability-set.md
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[azure-forced-tunneling]: https://azure.microsoft.com/en-gb/documentation/articles/vpn-gateway-forced-tunneling-rm/
[barracuda-nf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/barracuda-ng-firewall/
[barracuda-waf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/
[checkpoint]: https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/
[cloud-services-network-security]: https://azure.microsoft.com/documentation/articles/best-practices-network-security/
[denyall]: https://azure.microsoft.com/marketplace/partners/denyall/denyall-web-application-firewall/
[fortinet]: https://azure.microsoft.com/marketplace/partners/fortinet/fortinet-fortigate-singlevmfortigate-singlevm/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[ip-forwarding]: ../virtual-network/virtual-networks-udr-overview.md#IP-forwarding
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[ra-n-tier]: ./guidance-compute-n-tier-vm.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[rbac-custom-roles]: ../active-directory/role-based-access-control-custom-roles.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[routing-and-remote-access-service]: https://technet.microsoft.com/library/dd469790(v=ws.11).aspx
[securesphere]: https://azure.microsoft.com/marketplace/partners/imperva/securesphere-waf-for-azr/
[security-principle-of-least-privilege]: https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vns3]: https://azure.microsoft.com/marketplace/partners/cohesive/cohesiveft-vns3-for-azure/
[wireshark]: https://www.wireshark.org/
[0]: ./media/blueprints/hybrid-network-secure-vnet.png "Híbrido arquitetura de rede protegida"
