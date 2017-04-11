<properties
    pageTitle="Gerenciar perfis do Gerenciador de tráfego do Azure | Microsoft Azure"
    description="Este artigo ajuda você a criar, desabilitar, habilitar, excluir e exibir o histórico de um perfil do Gerenciador de tráfego do Azure."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="manage-an-azure-traffic-manager-profile"></a>Gerenciar um perfil de Gerenciador de tráfego do Azure

Perfis do Gerenciador de tráfego usam métodos de roteamento de tráfego para controlar a distribuição de tráfego para seus serviços de nuvem ou pontos de extremidade de site. Este artigo explica como criar e gerenciar esses perfis.

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>Crie um perfil de Gerenciador de tráfego usando a criação rápida

Você pode criar rapidamente um perfil do Gerenciador de tráfego, usando a criação rápida no portal de clássico do Azure. Criar rápida permite criar perfis com configurações básicas. No entanto, você não pode usar a criação rápida para configurações, como o conjunto de pontos de extremidade (sites e serviços de nuvem), a ordem de failover para o método de roteamento de tráfego de failover ou as configurações de monitoramento. Depois de criar seu perfil, você pode configurar essas configurações no portal de clássico do Azure. Gerenciador de tráfego oferece suporte a pontos de extremidade de até 200 por perfil. No entanto, a maioria dos cenários de uso exigem apenas alguns dos pontos de extremidade.

### <a name="to-create-a-traffic-manager-profile"></a>Para criar um perfil do Gerenciador de tráfego

1. **Implante os serviços de nuvem e os sites em seu ambiente de produção.** Para obter mais informações sobre os serviços de nuvem, consulte [Serviços de nuvem](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para obter mais informações sobre sites, consulte [sites](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Faça logon no portal do Azure clássico.** Clique em **novo** na parte inferior esquerda do portal, clique em **Serviços de rede > Gerenciador de tráfego**e clique em **Criar rápido** para começar a configurar seu perfil.
3. **Configure o prefixo DNS.** Dar a seu perfil do Gerenciador de tráfego um DNS exclusivo prefixo nome. Você pode especificar apenas o prefixo para um nome de domínio do Gerenciador de tráfego.
4. **Selecione a assinatura.** Selecione a assinatura Azure apropriada. Cada perfil está associado uma assinatura única. Se você tiver apenas uma assinatura, esta opção não aparecerá.
5. **Selecione o método de roteamento de tráfego.** Selecione o método de roteamento de tráfego no **tráfego de roteamento de política**. Para obter mais informações sobre métodos de roteamento de tráfego, consulte [sobre o Gerenciador de tráfego métodos de roteamento de tráfego](traffic-manager-routing-methods.md).
6. **Clique em "Criar" para criar o perfil**. Quando a configuração de perfil for concluída, você pode localizar seu perfil no painel Gerenciador de tráfego no portal de clássico do Azure.
7. **Configure pontos de extremidade, monitoramento e configurações adicionais no portal de clássico do Azure.** Apenas usando a criação rápida configura configurações básicas. É necessário definir configurações adicionais, como a lista de pontos de extremidade e a ordem de failover de ponto de extremidade.


## <a name="disable-enable-or-delete-a-profile"></a>Desabilitar, habilitar ou excluir um perfil

Você pode desativar um perfil existente para que o Gerenciador de tráfego não faz referência a solicitações de usuário para os pontos de extremidade configurados. Quando você desabilita um perfil do Gerenciador de tráfego, o perfil e as informações contidas no perfil permanecerão intactos e podem ser editados na interface do Gerenciador de tráfego.  Referências de currículo quando você habilitar novamente o perfil. Quando você cria um perfil do Gerenciador de tráfego no portal de clássico do Azure, ele é ativado automaticamente. Se você decidir que um perfil não é mais necessário, você poderá excluí-la.

### <a name="to-disable-a-profile"></a>Para desativar um perfil

1. Se você estiver usando um nome de domínio personalizado, altere o registro CNAME no seu servidor DNS da Internet para que ele não está mais aponta para o perfil do Gerenciador de tráfego.
2. Tráfego interrompe sendo direcionados para os pontos de extremidade por meio das configurações de perfil do Gerenciador de tráfego.
3. Selecione o perfil que você deseja desativar. Na página Gerenciador de tráfego, realce o perfil clicando na coluna ao lado do nome do perfil. Observação, clicando no nome do perfil ou na seta ao lado do nome abre a página de configurações para o perfil.
4. Após selecionar o perfil, clique em **Desabilitar** na parte inferior da página.

### <a name="to-enable-a-profile"></a>Para habilitar um perfil

1. Selecione o perfil que você deseja desativar. Na página Gerenciador de tráfego, realce o perfil clicando na coluna ao lado do nome do perfil. Observação, clicando no nome do perfil ou na seta ao lado do nome abre a página de configurações para o perfil.
2. Após selecionar o perfil, clique em **Habilitar** na parte inferior da página.
3. Se você estiver usando um nome de domínio personalizado, crie um registro de recurso CNAME no seu servidor DNS da Internet para apontar para o nome de domínio do seu perfil do Gerenciador de tráfego.
4. O tráfego será direcionado para os pontos de extremidade novamente.

### <a name="to-delete-a-profile"></a>Para excluir um perfil

1. Certifique-se de que o registro de recurso DNS no seu servidor DNS da Internet não usa mais um registro de recurso CNAME que aponta para o nome de domínio do seu perfil do Gerenciador de tráfego.
2. Selecione o perfil que você deseja desativar. Na página Gerenciador de tráfego, realce o perfil clicando na coluna ao lado do nome do perfil. Observação, clicando no nome do perfil ou na seta ao lado do nome abre a página de configurações para o perfil.
3. Após selecionar o perfil, clique em **Excluir** na parte inferior da página.

## <a name="view-traffic-manager-profile-change-history"></a>Histórico de alterações de perfil do Gerenciador de tráfego de exibição

Você pode exibir o histórico de alterações para um perfil do Gerenciador de tráfego no portal de clássico do Azure em serviços de gerenciamento.

### <a name="to-view-your-traffic-manager-change-history"></a>Para exibir seu gerente de tráfego do histórico de alterações

1. No painel esquerdo do Azure portal clássico, clique em **Serviços de gerenciamento**.
2. Na página serviços de gerenciamento, clique em **Logs de operação**.
3. Na página Logs de operação, você pode filtrar para exibir o histórico de alterações para um perfil do Gerenciador de tráfego. Depois de selecionar as opções de filtragem, clique na marca de seleção para exibir os resultados.

   - Para exibir as alterações para todos os seus perfis, selecione a assinatura e o intervalo de tempo e selecione **Gerenciador de tráfego** no **tipo de** menu de atalho.
   - Para filtrar por nome do perfil, digite o nome do perfil no campo **Nome de serviço** ou selecione-o no menu de atalho.
   - Para exibir detalhes de cada alteração individual, selecione a linha com a alteração que você deseja exibir e, em seguida, clique em **detalhes** na parte inferior da página. Na janela de **Detalhes da operação** , você pode exibir a representação XML do objeto API que foi criada ou atualizada como parte da operação.

## <a name="next-steps"></a>Próximas etapas

- [Adicionar um ponto extremo](traffic-manager-endpoints.md)
- [Configurar o método de roteamento de failover](traffic-manager-configure-failover-routing-method.md)
- [Configurar o método de roteamento de repetição alternada](traffic-manager-configure-round-robin-routing-method.md)
- [Configurar o método de roteamento de desempenho](traffic-manager-configure-performance-routing-method.md)
- [Aponte o domínio da Internet uma empresa para um nome de domínio do Gerenciador de tráfego](traffic-manager-point-internet-domain.md)
- [Solução de problemas Gerenciador de tráfego degradado estado](traffic-manager-troubleshooting-degraded.md)