## <a name="peering-across-subscriptions"></a>Correspondência em assinaturas

Neste cenário, você criará uma correspondência entre duas VNets pertencentes a diferentes assinaturas.

![Cross subcenário](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

Correspondência de VNet depende de controle de acesso baseado em função (RBAC) para autorização. Cenário entre assinaturas, você precisa primeiro conceder permissão suficiente para os usuários que criará o link aos:

> [AZURE.NOTE] Se o mesmo usuário tem o privilégio sobre ambas as assinaturas, você pode pular etapa 1-4 abaixo.
