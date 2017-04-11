<properties
   pageTitle="Gerenciar pontos de extremidade no Gerenciador de tráfego do Azure | Microsoft Azure"
   description="Este artigo ajudará você a adicionar, remover, habilitar e desabilitar pontos de extremidade do Gerenciador de tráfego do Azure."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# <a name="add-disable-enable-or-delete-endpoints"></a>Adicionar, desativar, ativar ou excluir pontos de extremidade

O recurso de aplicativos Web do serviço de aplicativo do Azure já fornece failover e funcionalidade de roteamento de tráfego alternada para sites em um data center, independentemente do modo de site. Gerenciador de tráfego Azure permite especificar failover e roteamento de tráfego de alternada para serviços de nuvem e sites em diferentes dos data centers. A primeira etapa necessária para fornecer essa funcionalidade é adicionar o ponto de extremidade de site ou serviço de nuvem ao Gerenciador de tráfego.

>[AZURE.NOTE] Você não poderá adicionar locais externos ou perfis do Gerenciador de tráfego como pontos de extremidade no portal Azure clássico. Você deve usar a API REST [Criar definição](http://go.microsoft.com/fwlink/p/?LinkId=400772) ou o Windows PowerShell [Add-AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774).

Você também pode desativar pontos de extremidade individuais que fazem parte de um perfil do Gerenciador de tráfego. Pontos de extremidade incluem sites e serviços de nuvem. Desabilitar um ponto de extremidade deixa-lo como parte do perfil, mas o perfil atua como se o ponto de extremidade não está incluído nele. Esta ação é muito útil para remover temporariamente um ponto de extremidade que está no modo de manutenção ou sendo redistribuído. Depois que o ponto de extremidade estiver em funcionamento novamente, ele pode ser habilitado.

>[AZURE.NOTE] Desabilitar um ponto de extremidade tem nada fazer com seu estado de implantação no Azure. Um ponto de extremidade eficaz, permanecerá para cima e capaz de receber o tráfego mesmo quando desativado no Gerenciador de tráfego. Além disso, a desabilitação de um ponto de extremidade em um perfil não afeta seu status em outro perfil.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Para adicionar um ponto de extremidade de site ou serviço de nuvem


1. No painel Gerenciador de tráfego no portal de clássico do Azure, localize o perfil do Gerenciador de tráfego que contém as configurações de ponto de extremidade que você deseja modificar e clique na seta à direita do nome do perfil. Isso abrirá a página de configurações para o perfil.
2. Na parte superior da página, clique em **pontos de extremidade** para exibir os pontos de extremidade que já fazem parte da sua configuração.
3. Na parte inferior da página, clique em **Adicionar** para acessar a página **Adicionar pontos de extremidade do serviço** . Por padrão, a página lista os serviços de nuvem em **Pontos de extremidade do serviço**.
4. Para serviços de nuvem, selecione os serviços de nuvem na lista para habilitá-las como pontos de extremidade para este perfil. Limpar o nome de serviço de nuvem remove da lista de pontos de extremidade.
5. Sites, clique na lista suspensa **Tipo de serviço** e, em seguida, selecione **Web app**.
6. Selecione os sites na lista para adicioná-los como pontos de extremidade para este perfil. Limpar o nome de site removerá a lista de pontos de extremidade. Observe que você só pode selecionar um único site por Azure Data Center (também conhecido como uma região). Se você selecionar um site em um data center que hospeda vários sites, quando você seleciona o primeiro site, as outras pessoas no mesmo data center indisponíveis para seleção. Observe também que somente padrão sites estão listadas.
7. Após selecionar os pontos de extremidade para este perfil, clique na marca de seleção na parte inferior direita para salvar suas alterações.

>[AZURE.NOTE] Se você estiver usando o método de roteamento de tráfego de *Failover* , depois que você adicionar ou remover um ponto de extremidade, certifique-se de ajustar a lista de prioridade de Failover na página configuração para refletir a ordem de failover que deseja para sua configuração. Para obter mais informações, consulte [Configurar Failover roteamento de tráfego](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Para desabilitar um ponto extremo

1. No painel Gerenciador de tráfego no portal de clássico do Azure, localize o perfil do Gerenciador de tráfego que contém as configurações de ponto de extremidade que você deseja modificar e clique na seta à direita do nome do perfil. Isso abrirá a página de configurações para o perfil.
2. Na parte superior da página, clique em **pontos de extremidade** para exibir os pontos de extremidade incluídas em sua configuração.
3. Clique na extremidade que você deseja desabilitar e clique em **Desativar** na parte inferior da página.
4. Tráfego interromperá fluindo para o ponto de extremidade com base no DNS Time-to-Live (TTL) configurado para o nome de domínio do Gerenciador de tráfego. Você pode alterar o TTL da página de configuração do perfil do Gerenciador de tráfego.

## <a name="to-enable-an-endpoint"></a>Para habilitar um ponto extremo

1. No painel Gerenciador de tráfego no portal de clássico do Azure, localize o perfil do Gerenciador de tráfego que contém as configurações de ponto de extremidade que você deseja modificar e clique na seta à direita do nome do perfil. Isso abrirá a página de configurações para o perfil.
2. Na parte superior da página, clique em **pontos de extremidade** para exibir os pontos de extremidade incluídas em sua configuração.
3. Clique na extremidade que você deseja habilitar e clique em **Habilitar** na parte inferior da página.
4. Tráfego iniciará fluindo para o serviço como ditado pelo perfil.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Para excluir um ponto de extremidade de site ou serviço de nuvem


1. No painel Gerenciador de tráfego no portal de clássico do Azure, localize o perfil do Gerenciador de tráfego que contém as configurações de ponto de extremidade que você deseja modificar e clique na seta à direita do nome do perfil. Isso abrirá a página de configurações para o perfil.
2. Na parte superior da página, clique em **pontos de extremidade** para exibir os pontos de extremidade que já fazem parte da sua configuração.
3. Na página pontos de extremidade, clique no nome do ponto de extremidade que você deseja excluir do perfil.
4. Na parte inferior da página, clique em **Excluir**.

>[AZURE.NOTE] Você não pode excluir locais externos ou perfis do Gerenciador de tráfego como pontos de extremidade no portal Azure clássico. Você deve usar o Windows PowerShell. Para obter mais informações, consulte [Remover AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx).

## <a name="next-steps"></a>Próximas etapas

- [Configurar o método de roteamento de failover](traffic-manager-configure-failover-routing-method.md)
- [Configurar o método de roteamento de repetição alternada](traffic-manager-configure-round-robin-routing-method.md)
- [Configurar o método de roteamento de desempenho](traffic-manager-configure-performance-routing-method.md)
- [Solução de problemas Gerenciador de tráfego degradado estado](traffic-manager-troubleshooting-degraded.md)
- [Operações em Gerenciador de tráfego (referência de API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)
