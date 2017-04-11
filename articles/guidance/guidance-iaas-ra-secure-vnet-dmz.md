<properties
   pageTitle="Referência de arquitetura do Windows Azure - IaaS: Implementação uma DMZ entre Azure e a Internet | Microsoft Azure"
   description="Como implementar uma arquitetura de rede de seguro híbrida com acesso à Internet no Azure."
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

# <a name="implementing-a-dmz-between-azure-and-the-internet"></a>Implementação de uma DMZ entre Azure e a Internet

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve as práticas recomendadas para implementar uma rede híbrido seguro que estende a sua rede local que aceita o tráfego da rede da internet para o Azure. Essa arquitetura de referência estende a arquitetura descrita no artigo [implementação de uma DMZ entre Azure e data center local][implementing-a-secure-hybrid-network-architecture]. É recomendável que você leia esse documento e entender o que fazem referência a arquitetura antes de ler este documento.

> [AZURE.NOTE] Azure tem dois diferentes modelos de implantação: [Gerenciador de recursos] [ resource-manager-overview] e clássico. Esta arquitetura de referência usa o Gerenciador de recursos, a Microsoft recomenda para novas implantações. 

Casos de uso típico para essa arquitetura incluem:

- Aplicativos híbridos onde executar cargas de trabalho parcialmente local e parcialmente no Azure.

- Infraestrutura Azure que rotear o tráfego de entrada de locais e na internet.

## <a name="architecture-diagram"></a>Diagrama de arquitetura

O diagrama a seguir destaca os componentes importantes nesta arquitetura:

> Um documento do Visio que inclui este diagrama de arquitetura está disponível para download no [Centro de download da Microsoft][visio-download]. Este diagrama é na página "DMZ – pública".

[! [0]][0]

- **Endereço IP público (PIP).** Este é o endereço IP do ponto de extremidade público. Usuários externos conectados à Internet podem acessar o sistema através desse endereço.

- **Dispositivo de rede virtual (NVA).**  NVA é um termo genérico que descreve uma máquina virtual realizar tarefas como permitir ou negar acesso como um firewall, otimiza operações de WAN (incluindo a compactação de rede), roteamento personalizado ou outros tipos de funcionalidade de rede.

- **Balanceador de carga Azure.** Todas as solicitações de entrada da internet passar por esse balanceador de carga e são distribuídas para NVAs no DMZ público sub-rede entrada.

- **DMZ público sub-rede de entrada.** Esta sub-rede aceita solicitações de Balanceador de carga Azure. Solicitações de entrada são passadas para uma das NVAs no DMZ.

- **Sub-rede de saída DMZ público.** Solicitações que foram aprovadas pela NVA passam nesta sub-rede para o balanceador de carga interno para a camada da web.

## <a name="recommendations"></a>Recomendações

Azure oferece muitos recursos diferentes e tipos de recurso, portanto, essa arquitetura de referência pode ser provisionado muitas maneiras diferentes. Fornecemos um modelo do Gerenciador de recursos do Azure para instalar a arquitetura de referência que segue essas recomendações. Se você optar por criar sua própria arquitetura de referência que você deve seguir essas recomendações, a menos que tenha um requisito específico que não correspondam a uma recomendação.

### <a name="nva-recommendations"></a>Recomendações NVA

Implemente um conjunto de NVAs para tráfego de origem na internet e outro para tráfego local de origem. É um risco de segurança para usar apenas um conjunto de NVAs ambos porque esse projeto não fornece nenhuma perímetro de segurança entre os dois conjuntos de tráfego de rede. Ele é um benefício para usar este design porque ele reduz a complexidade da segurança regras de verificação e torna mais clara quais regras correspondem a cada solicitação de rede de entrada. Por exemplo, um conjunto de NVAs implementa regras para tráfego de internet apenas enquanto outro conjunto de regras de implementar NVAs local somente para tráfego.

Incluir uma camada 7 NVA encerrar conexões de aplicativos no nível de NVA e manter afinidade com as camadas de back-end. Isso garante conectividade simétrica na qual o tráfego de resposta dos níveis de back-end retorna através do NVA.  

### <a name="public-load-balancer-recommendations"></a>Recomendações de Balanceador de carga pública ###

Para manter a disponibilidade e escalabilidade, implantar DMZ público NVAs de entrada em um [conjunto de disponibilidade] [ availability-set] e usar um [internet opostas balanceador de carga] [ load-balancer] para distribuir solicitações de internet nas NVAs no conjunto de disponibilidade.  

Configure o balanceador de carga para aceitar solicitações somente as portas necessárias para o tráfego de internet. Por exemplo, restrinja as solicitações HTTP de entrada para a porta 80 e solicitações de entrada HTTPS a porta 443.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

Projetar sua infraestrutura com um opostas balanceador de carga na frente de entrada sub-rede DMZ pública desde o início da internet. Mesmo se initally sua arquitetura requer um único NVA, será mais fácil de dimensionar para várias NVAs no futuro se internet opostas balanceador de carga já está implantado.

## <a name="availability-considerations"></a>Considerações de disponibilidade

Internet opostas balanceador de carga requer cada NVA no DMZ público sub-rede entrada implementar um [teste de integridade][lb-probe]. Um teste de integridade que falha ao responder nesse ponto de extremidade é considerado indisponível e balanceador de carga direcionará solicitações para outros NVAs no mesmo conjunto de disponibilidade. Observe que se todos os NVAs falharem ao responder, seu aplicativo falhará portanto é importante ter configurado para DevOps alerta quando o número de instâncias NVA íntegros cai abaixo de um limite de monitoramento.

## <a name="manageability-considerations"></a>Considerações de capacidade de gerenciamento

Restringir a funcionalidade de monitoramento e gerenciamento para a entrada público DMZ NVA responder às solicitações da caixa de salto na sub-rede gerenciamento apenas. Conforme discutido na [implementação de uma DMZ entre Azure e data center local] [ implementing-a-secure-hybrid-network-architecture] do documento, defina uma rota de rede única da rede local através do gateway para a caixa de salto na sub-rede gerenciamento para restringir o acesso.

Se a conectividade de gateway da sua rede local para o Azure for pressionada, ainda pode chegar na caixa de salto Implantando um PIP, adicioná-lo à caixa de salto e remota da internet.

## <a name="security-considerations"></a>Considerações de segurança

Essa arquitetura de referência implementa vários níveis de segurança:

- Internet opostas balanceador de carga direciona solicitações para as NVAs na entrada público DMZ sub-rede somente e somente nas portas necessárias para o aplicativo.

- As regras de NSG de entrada e saída pública sub-rede DMZ impedem que o NVAs seja comprometido bloqueando solicitações que estejam fora as regras NSG.

- A configuração de roteamento de NAT para os NVAs direciona solicitações de entrada em portas 80 e 443 ao balanceador de carga de nível da web, mas ignora solicitações em todas as outras portas.

Observe que você deve registrar todas as solicitações de entrada em todas as portas. Auditoria regularmente os logs, prestar atenção às solicitações que estejam fora parâmetros esperados como eles podem indicar tentativas de intrusos.

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>Usando NSGs para bloquear/fase de tráfego entre os níveis de aplicativo

Cada uma das camadas da web, negócios e dados restringir o tráfego entre eles usando NSGs. Isto é, a camada de negócios usa um NSG para bloquear todo o tráfego que não se originam na camada da web e a camada de dados usa um NSG para bloquear todo o tráfego que não se originam na camada de negócios. Se você tiver um requisito para expandir as regras NSG para permitir acesso mais amplo a esses níveis, ponderar esses requisitos contra os riscos de segurança. Cada novo caminho de entrada representa uma oportunidade para danos de aplicativo ou perda de dados acidental ou proposital.

## <a name="solution-deployment"></a>Implantação da solução

Uma implantação para uma arquitetura de referência que implementa essas recomendações está disponível no Github. Essa arquitetura de referência inclui uma rede virtual (VNet), o grupo de segurança de rede (NSG), balanceador de carga e duas máquinas virtuais (VMs).

A arquitetura de referência pode ser implantada com Windows ou Linux VMs seguindo as instruções abaixo: 

1. Clique com botão direito no botão abaixo e selecione um dos "Abrir link em uma nova guia" ou "Abrir link em nova janela":  
[![Implantar para o Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2FvirtualNetwork.azuredeploy.json)

2. Depois que o link for aberto no portal do Azure, você deve inserir valores para algumas das configurações: 
    - O nome do **grupo de recursos** já está definido no arquivo de parâmetros, portanto selecione **Criar novo** e digite `ra-public-dmz-network-rg` na caixa de texto.
    - Selecione a região na caixa suspensa **local** .
    - Não edite o **Modelo raiz Uri** ou as caixas de texto de **Uri de raiz de parâmetro** .
    - Selecione o **Tipo de sistema operacional** na lista suspensa de caixa, **windows** ou **linux**.
    - Examine os termos e condições, clique na caixa de seleção **para concordar com os termos e condições mencionadas acima** .
    - Clique no botão de **compra** .

3. Aguarde a implantação ser concluída.

4. Clique com botão direito no botão abaixo e selecione um dos "Abrir link em uma nova guia" ou "Abrir link em nova janela":  
[![Implantar para o Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fworkload.azuredeploy.json)

5. Depois que o link for aberto no portal do Azure, você deve inserir valores para algumas das configurações: 
    - O nome do **grupo de recursos** já está definido no arquivo de parâmetros, portanto selecione **Criar novo** e digite `ra-public-dmz-wl-rg` na caixa de texto.
    - Selecione a região na caixa suspensa **local** .
    - Não edite o **Modelo raiz Uri** ou as caixas de texto de **Uri de raiz de parâmetro** .
    - Examine os termos e condições, clique na caixa de seleção **para concordar com os termos e condições mencionadas acima** .
    - Clique no botão de **compra** .

6. Aguarde a implantação ser concluída.

7. Clique com botão direito no botão abaixo e selecione um dos "Abrir link em uma nova guia" ou "Abrir link em nova janela":  
[![Implantar para o Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fsecurity.azuredeploy.json)

8. Depois que o link for aberto no portal do Azure, você deve inserir valores para algumas das configurações: 
    - O nome do **grupo de recursos** já está definido no arquivo de parâmetros, portanto selecione **Usar existente** e insira `ra-public-dmz-network-rg` na caixa de texto.
    - Selecione a região na caixa suspensa **local** .
    - Não edite o **Modelo raiz Uri** ou as caixas de texto de **Uri de raiz de parâmetro** .
    - Examine os termos e condições, clique na caixa de seleção **para concordar com os termos e condições mencionadas acima** .
    - Clique no botão de **compra** .

9. Aguarde a implantação ser concluída.

10. Arquivos de parâmetro incluem o nome de usuário de administrador embutidos e senha para todas as VMs e é altamente recomendável que você altere imediatamente ambos. Para cada máquina virtual na implantação, selecione-o no portal do Azure e clique em **Redefinir senha** na lâmina **suporte + solução de problemas** . Selecione **Redefinir a senha** na caixa de lista suspensa **modo** e, em seguida, selecione um novo **nome de usuário** e **senha**. Clique no botão de **atualização** para persistir.


<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[iptables]: https://help.ubuntu.com/community/IptablesHowTo
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md
[load-balancer]: ../load-balancer/load-balancer-internet-overview.md
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[0]: ./media/blueprints/hybrid-network-secure-vnet-dmz.png "Híbrido arquitetura de rede protegida"
