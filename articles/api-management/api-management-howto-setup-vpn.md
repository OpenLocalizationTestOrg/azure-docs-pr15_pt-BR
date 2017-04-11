<properties
    pageTitle="Como configurar conexões VPN no gerenciamento de API do Azure"
    description="Saiba como configurar uma conexão VPN em gerenciamento de API do Azure e serviços web do access através dele."
    services="api-management"
    documentationCenter=""
    authors="antonba"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="antonba"/>

# <a name="how-to-setup-vpn-connections-in-azure-api-management"></a>Como configurar conexões VPN no gerenciamento de API do Azure

Suporte a VPN do gerenciamento de API permite que você se conecte seu gateway de gerenciamento de API para uma rede Virtual do Azure (clássico). Isso permite que clientes de gerenciamento de API conectar-se com segurança aos seus serviços da web de back-end que são locais ou caso contrário são inacessíveis à internet pública.

>[AZURE.NOTE] Gerenciamento de API Azure funciona com VNETs clássicos. Para obter informações sobre como criar uma VNET clássica, consulte [criar uma rede virtual (clássica) usando o Portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Para obter informações sobre conexão VNETs clássicos a VNETS ARM, consulte [Conectando-se VNets clássico para VNets novo](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="enable-vpn"> </a>Conexões VPN habilitar

>Conectividade VPN só está disponível nos níveis **Premium** e **desenvolvedor** . Para alternar para ele, abra seu serviço de gerenciamento de API no [Portal de clássico do Azure][] e abra a guia **escala** . Na seção **Geral** , selecione a camada Premium e clique em Salvar.

Para habilitar a conectividade VPN, abra seu serviço de gerenciamento de API no [Portal de clássico do Azure][] e alternar para a guia de **Configurar** . 

Na seção VPN, alterne **conexão VPN** para **em**.

![Configurar o guia de instância de gerenciamento de API][api-management-setup-vpn-configure]

Agora, você verá uma lista de todas as regiões onde seu serviço de gerenciamento de API está provisionado.

Selecione uma VPN e sub-rede para cada região. A lista de VPNs é preenchida com base em redes virtuais disponíveis em sua assinatura do Azure que estão na região que você está configurando a instalação.

![Selecione VPN][api-management-setup-vpn-select]

Clique em **Salvar** na parte inferior da tela. Não será capaz de executar outras operações no serviço de gerenciamento de API do Portal clássico do Azure enquanto está atualizando. O gateway de serviço permanecerá disponível e não devem ser afetadas chamadas de tempo de execução.

Observe que o endereço de VIP do gateway alterará cada vez VPN está ativado ou desativado.

## <a name="connect-vpn"> </a>Conectar a um serviço web atrás VPN

Quando seu serviço de gerenciamento de API estiver conectado à VPN, acessando web services dentro da rede virtual não será diferente de acessar serviços públicos. Basta digitar o endereço local ou o nome de host (se um servidor DNS foi configurado para a rede Virtual do Azure) do seu serviço da web no campo **URL do serviço Web** ao criar uma nova API ou editando uma existente.

![Adicionar API de VPN][api-management-setup-vpn-add-api]

## <a name="required-ports-for-api-management-vpn-support"></a>Portas necessárias para o suporte a VPN de gerenciamento de API

Quando uma instância de serviço de gerenciamento de API estiver hospedada em um VNET, as portas na tabela a seguir são usadas. Se essas portas estiverem bloqueadas, o serviço pode não funcionar corretamente. Ter um ou mais dessas portas bloqueados é o problema mais comum de configuração incorreta ao usar o gerenciamento de API com um VNET.

| Portas                      | Direção        | Protocolo de transporte | Finalidade                                                          | Origem / destino              |
|------------------------------|------------------|--------------------|------------------------------------------------------------------|-----------------------------------|
| 80, 443                      | Entrada          | TCP                | Comunicação de cliente para o gerenciamento de API                           | INTERNET / VIRTUAL_NETWORK        |
| 80, 443                       | Saída         | TCP                | Dependência de gerenciamento de API de armazenamento do Azure e barramento de serviço Azure | VIRTUAL_NETWORK / INTERNET        |
| 1433                         | Saída         | TCP                | Dependências de gerenciamento de API em SQL                               | VIRTUAL_NETWORK / INTERNET        |
| 9350, 9351, 9352, 9353, 9354 | Saída         | TCP                | Dependências de gerenciamento de API no barramento de serviço                       | VIRTUAL_NETWORK / INTERNET        |
| 5671                         | Saída         | AMQP               | Dependência de gerenciamento de API Log de evento política do Hub            | VIRTUAL_NETWORK / INTERNET        |
| 6381, 6382, 6383             | Entrada/saída | UDP                | Dependências de gerenciamento de API no Cache relacionada                       | VIRTUAL_NETWORK / VIRTUAL_NETWORK |
| 445                          | Saída         | TCP                | Dependência de gerenciamento de API em compartilhamento de arquivo Azure para GITO            | VIRTUAL_NETWORK / INTERNET        |

## <a name="custom-dns"> </a>Configuração de servidor DNS personalizada

Gerenciamento de API depende de um número de serviços do Azure. Quando uma instância de serviço de gerenciamento de API estiver hospedada em um VNET qual um servidor DNS personalizado é usado, ele precisa ser capaz de resolver nomes de host desses serviços Azure. Siga [Este](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) guia na configuração DNS personalizada.  

## <a name="related-content"> </a>Conteúdo relacionado


* [Criar uma rede virtual com uma conexão de VPN-to-site usando o Portal de clássico do Azure][]
* [Como usar o Inspetor de API para rastrear chamadas no gerenciamento de API do Azure][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Azure Portal clássico]: https://manage.windowsazure.com/

[Criar uma rede virtual com uma conexão de VPN-to-site usando o Portal de clássico do Azure]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[Como usar o Inspetor de API para rastrear chamadas no gerenciamento de API do Azure]: api-management-howto-api-inspector.md
