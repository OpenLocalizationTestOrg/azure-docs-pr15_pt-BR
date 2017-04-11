<properties
    pageTitle="Tipos de ponto de extremidade do Gerenciador de tráfego | Microsoft Azure"
    description="Este artigo explica os diferentes tipos de pontos de extremidade que podem ser usados com o Gerenciador de tráfego do Azure"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="traffic-manager-endpoints"></a>Pontos de extremidade do Gerenciador de tráfego

Gerenciador de tráfego do Microsoft Azure permite que você controle como o tráfego de rede é distribuído para implantações de aplicativos em execução em centros de dados diferentes. Você configurar cada implantação do aplicativo como um 'ponto de extremidade' no Gerenciador de tráfego. Quando o Gerenciador de tráfego recebe uma solicitação DNS, ele escolhe um ponto de extremidade disponível para retornar na resposta DNS. Gerenciador de tráfego baseia a opção sobre o status atual do ponto de extremidade e o método de roteamento de tráfego. Para obter mais informações, consulte [Como funciona o Gerenciador de tráfego](traffic-manager-how-traffic-manager-works.md).

Há três tipos de ponto de extremidade suportada pelo Gerenciador de tráfego:

- **Pontos de extremidade Azure** são usados para serviços hospedados no Azure.
- **Pontos de extremidade externos** são usados para serviços hospedados fora do Azure, locais ou com um provedor de hospedagem diferentes.
- **Pontos de extremidade de aninhado** são usadas para combinar perfis do Gerenciador de tráfego para criar esquemas de roteamento de tráfego mais flexíveis para atender às necessidades de implantações maiores e mais complexas.

Não há nenhuma restrição como pontos de extremidade de diferentes tipos são combinados em um único perfil do Gerenciador de tráfego. Cada perfil pode conter qualquer combinação de tipos de ponto de extremidade.

As seções a seguir descrevem cada tipo de ponto de extremidade mais detalhadamente.

## <a name="azure-endpoints"></a>Pontos de extremidade do Azure

Pontos de extremidade Azure são usados para serviços baseados em Azure no Gerenciador de tráfego. Os seguintes tipos de recurso Azure são suportados:

- 'Clássico' IaaS VMs e PaaS serviços em nuvem.
- Aplicativos Web
- Recursos de PublicIPAddress (que pode ser conectada à VMs diretamente ou por meio de um balanceador de carga do Azure). O publicIpAddress deve ter um nome DNS atribuído a ser usada em um perfil do Gerenciador de tráfego.

Recursos de PublicIPAddress são recursos do Gerenciador de recursos do Azure. Eles não existem no modelo clássico de implantação. Assim que eles são experiências do Gerenciador de recursos do Azure somente com suporte no tráfego do gerente. Os outros tipos de ponto de extremidade são suportados pelo Gerenciador de recursos e o modelo de implantação clássico.

Ao usar os pontos de extremidade Azure, Gerenciador de tráfego detecta quando um 'Clássico' VM IaaS, serviço de nuvem ou um aplicativo Web for interrompido e iniciado. Esse status é refletido no status de ponto de extremidade. Para obter detalhes, consulte [monitoramento de ponto de extremidade do Gerenciador de tráfego](traffic-manager-monitoring.md#endpoint-and-profile-status) . Quando o serviço subjacente é interrompido, Gerenciador de tráfego não executará verificações de integridade do ponto de extremidade ou direcione o tráfego para o ponto de extremidade. Nenhum Gerenciador de tráfego cobranças eventos ocorrem para a instância parou. Quando o serviço é reiniciado, currículos de cobranças e o ponto de extremidade está qualificado para receber tráfego. Esta detecção não se aplica a pontos de extremidade de PublicIpAddress.

## <a name="external-endpoints"></a>Pontos de extremidade externos

Pontos de extremidade externos são usados para serviços fora do Azure. Por exemplo, um serviço hospedado no local ou com um provedor diferente. Pontos de extremidade externos podem ser usados individualmente ou combinados com pontos de extremidade do Azure no mesmo perfil Gerenciador de tráfego. Combinar Azure pontos de extremidade com pontos de extremidade externos habilita vários cenários:

- Em qualquer um modelo de ativa ou ativo-passivo de falha, use Azure para fornecer maior redundância para uma existente aplicativo local.
- Para reduzir a latência de aplicativo para usuários do mundo, estenda um aplicativo de local existente para locais geográficos adicionais no Azure. Para obter mais informações, consulte [Gerenciador de tráfego 'Desempenho' roteamento de tráfego](traffic-manager-routing-methods.md#performance-traffic-routing-method).
- Azure de uso para fornecer capacidade adicional para uma existente locais aplicativo, continuamente ou como uma solução 'intermitente à nuvem' para atender a um pico em demanda.

Em alguns casos, é útil para usar pontos de extremidade externos para fazer referência a serviços Azure (para obter exemplos, consulte as [perguntas Frequentes](#faq)). Nesse caso, verificações de integridade são cobradas a taxa de pontos de extremidade Azure, não a taxa de empresas externas. No entanto, ao contrário dos pontos de extremidade Azure, se você parar ou excluir o serviço subjacente, verificação de integridade cobrança continua até você desabilitar ou excluir o ponto de extremidade no Gerenciador de tráfego.

## <a name="nested-endpoints"></a>Pontos de extremidade aninhados

Pontos de extremidade aninhados combinam vários perfis do Gerenciador de tráfego para criar esquemas de roteamento de tráfego flexíveis e suportar as necessidades de implantações maiores e complexas. Com pontos de extremidade aninhado, um perfil 'filho' é adicionado como um ponto de extremidade para um perfil de 'pai'. Perfis do pai e filho podem conter outros pontos de extremidade de qualquer tipo, incluindo outros perfis aninhados. Para obter mais informações, consulte [perfis do Gerenciador de tráfego aninhadas](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web Apps como pontos de extremidade

Algumas considerações adicionais se aplicam ao configurar aplicativos Web como pontos de extremidade no Gerenciador de tráfego:

1. Somente Web Apps na SKU 'Padrão' ou acima são qualificadas para uso com o Gerenciador de tráfego. Tenta adicionar um aplicativo Web de uma SKU menor falha. Fazer downgrade o SKU de um aplicativo Web existente resulta no Gerenciador de tráfego não está mais enviando o tráfego para esse aplicativo Web.

2. Quando um ponto de extremidade recebe uma solicitação HTTP, ele usa o cabeçalho de 'host' na solicitação para determinar qual Web App deve atender a solicitação. O cabeçalho do host contém o nome DNS usado para iniciar a solicitação, por exemplo 'contosoapp.azurewebsites.net'. Para usar um nome diferente de DNS com seu aplicativo Web, o nome DNS deve ser registrado como um nome de domínio personalizado para o aplicativo. Ao adicionar um ponto de extremidade do Web App como um ponto de extremidade Azure, o nome DNS de perfil do Gerenciador de tráfego é registrado automaticamente para o aplicativo. Esse registro é removido automaticamente quando o ponto de extremidade seja excluído.

3. Cada perfil do Gerenciador de tráfego pode ter no máximo um Web App ponto de extremidade de cada região Azure. Para contornar para esta restrição, você pode configurar um aplicativo Web como um ponto de extremidade externo. Para obter mais informações, consulte as [perguntas Frequentes](#faq).

## <a name="enabling-and-disabling-endpoints"></a>Ativando e desativando os pontos de extremidade

Desabilitar um ponto de extremidade no Gerenciador de tráfego pode ser úteis para remover temporariamente o tráfego de um ponto de extremidade que está no modo de manutenção ou sendo redistribuído. Quando o ponto de extremidade estiver em execução novamente, ele pode ser habilitado novamente.

Pontos de extremidade podem ser habilitados e desabilitados através do portal do Gerenciador de tráfego, PowerShell, CLI ou API REST, todas elas são compatíveis com o Gerenciador de recursos e o modelo de implantação clássico.

>[AZURE.NOTE] Desabilitar um ponto de extremidade Azure tem nada fazer com seu estado de implantação no Azure. Um serviço Azure (como uma máquina virtual ou aplicativo Web permanece em execução e capaz de receber o tráfego mesmo quando desativado no Gerenciador de tráfego. O tráfego pode ser resolvido diretamente para a instância do serviço em vez de por meio do nome DNS de perfil do Gerenciador de tráfego. Para obter mais informações, consulte [como funciona o Gerenciador de tráfego](traffic-manager-how-traffic-manager-works.md).

A qualificação atual de cada ponto de extremidade para receber tráfego depende dos seguintes fatores:

- O status de perfil (ativado/desativado)
- O status de ponto de extremidade (ativado/desativado)
- Os resultados da integridade verifica esse ponto de extremidade

Para obter detalhes, consulte [monitoramento de ponto de extremidade do Gerenciador de tráfego](traffic-manager-monitoring.md#endpoint-and-profile-status).

>[AZURE.NOTE] Como gerente de tráfego funciona no nível do DNS, é possível influenciar conexões existentes com qualquer ponto de extremidade. Quando um ponto de extremidade não estiver disponível, o Gerenciador de tráfego direciona novas conexões com outro ponto de extremidade disponível. No entanto, o host atrás do ponto de extremidade desabilitado ou não íntegro pode continuar a receber tráfego via conexões existentes até as sessões são encerradas. Aplicativos devem limitar a duração de sessão para permitir o tráfego descarregar de conexões existentes.

Se todos os pontos de extremidade de um perfil estão desabilitados ou se o próprio perfil estiver desabilitado, o Gerenciador de tráfego envia uma resposta 'NXDOMAIN' a uma nova consulta DNS.

## <a name="faq"></a>Perguntas Freqüentes

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Pode usar o Gerenciador de tráfego com pontos de extremidade de várias assinaturas?

Não é possível ao Azure Web Apps usar pontos de extremidade de várias assinaturas. Azure Web Apps requer que qualquer nome de domínio personalizado usado com aplicativos Web é usado apenas dentro de uma única assinatura. Não é possível usar os aplicativos da Web de várias assinaturas com o mesmo nome de domínio.

Para outros tipos de ponto de extremidade, é possível usar o Gerenciador de tráfego com pontos de extremidade de mais de uma assinatura. Como configurar o Gerenciador de tráfego depende de se você estiver usando o modelo de implantação clássico ou a experiência do Gerenciador de recursos.

- No Gerenciador de recursos, pontos de extremidade de qualquer assinatura podem ser adicionados ao Gerenciador de tráfego, desde que a pessoa Configurando o perfil do Gerenciador de tráfego tem acesso de leitura para o ponto de extremidade. Essas permissões podem ser concedidas usando o [controle de acesso baseado em função do Gerenciador de recursos do Azure (RBAC)](../active-directory/role-based-access-control-configure.md).
- Na interface do modelo clássico de implantação, Gerenciador de tráfego requer os serviços de nuvem ou Web Apps configurado como pontos de extremidade Azure residem na mesma assinatura como o perfil do Gerenciador de tráfego. Pontos de extremidade de serviço de nuvem em outras assinaturas podem ser adicionados ao Gerenciador de tráfego como 'externos' pontos de extremidade. Esses pontos de extremidade externos são cobrados como pontos de extremidade Azure, em vez da taxa externa.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Pode usar o Gerenciador de tráfego com slots de 'Teste' de serviço de nuvem?

Sim. Serviço de nuvem 'teste' slots pode ser configurado no Gerenciador de tráfego como pontos de extremidade externos. Verificações de integridade são ainda cobrada de acordo com a taxa de pontos de extremidade do Azure. Porque o tipo de ponto de extremidade externa está em uso, alterações no serviço subjacente não são recebidas automaticamente. Com pontos de extremidade externos, Gerenciador de tráfego não pode detectar quando o serviço de nuvem é interrompido ou excluído. Portanto, o Gerenciador de tráfego continua cobrança para verificações de integridade até que o ponto de extremidade forem desabilitado ou excluído.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Gerenciador de tráfego dá suporte a IPv6 pontos de extremidade?

Gerenciador de tráfego atualmente não oferece IPv6 addressible servidores de nomes. No entanto, Gerenciador de tráfego ainda podem ser usado pela clientes IPv6 se conectando a pontos de extremidade de IPv6. Um cliente não faz solicitações DNS diretamente ao Gerenciador de tráfego. Em vez disso, o cliente usa um serviço DNS recursiva. Um cliente somente IPv6 envia solicitações para o serviço DNS recursiva via IPv6. Em seguida, o serviço de recursiva deve ser capaz de contatar os servidores de nomes do Gerenciador de tráfego usando IPv4.

Gerenciador de tráfego responde com o nome DNS do ponto de extremidade. Para oferecer suporte a um ponto de extremidade de IPv6, deve existir um registro de DNS AAAA apontando o nome DNS de ponto de extremidade para o endereço IPv6. Verificações de integridade do Gerenciador de tráfego só oferece suporte a endereços IPv4. O serviço precisa expor um ponto de extremidade de IPv4 no mesmo nome DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Pode usar o Gerenciador de tráfego com mais de um aplicativo Web na mesma região?

Normalmente, o Gerenciador de tráfego é usado para direcionar o tráfego para aplicativos implantados em diferentes regiões. No entanto, ele também pode ser usado onde um aplicativo tem mais de uma implantação na mesma região. Os pontos de extremidade do Gerenciador de tráfego Azure não permitir mais de um ponto de extremidade do Web App da mesma região Azure a ser adicionado ao mesmo perfil Gerenciador de tráfego.

As etapas a seguir fornecem uma solução alternativa para essa restrição:

1. Verifique se os pontos de extremidade estão em diferentes web app 'Escala unidades'. Um nome de domínio deve mapear para um único site em uma unidade de escala. Portanto, os dois aplicativos Web na mesma unidade de escala não pode compartilhar um perfil do Gerenciador de tráfego.
2. Adicione seu nome de domínio banido como um nome de host personalizado para cada aplicativo Web. Cada aplicativo Web deve estar em uma unidade de escala diferente. Todos os aplicativos Web deve pertencer a mesma assinatura.
3. Adicione um (e apenas um) ponto de extremidade do Web App no seu perfil do Gerenciador de tráfego, como um ponto de extremidade Azure.
4. Adicione cada ponto de extremidade do Web App adicional ao seu perfil do Gerenciador de tráfego como um ponto de extremidade externo. Pontos de extremidade externos só podem ser adicionados usando o modelo de implantação do Gerenciador de recursos.
5. Criar um registro CNAME DNS no seu domínio banido que aponta para o seu nome DNS de perfil do Gerenciador de tráfego (<> …. trafficmanager.net).
6. Acesse seu site via o nome de domínio banido, não o nome DNS do Gerenciador de tráfego perfil.

## <a name="next-steps"></a>Próximas etapas

- Saiba [como o Gerenciador de tráfego funciona](traffic-manager-how-traffic-manager-works.md).
- Saiba mais sobre o Gerenciador de tráfego [monitoramento de ponto de extremidade e failover automático](traffic-manager-monitoring.md).
- Saiba mais sobre o Gerenciador de tráfego [métodos de roteamento de tráfego](traffic-manager-routing-methods.md).
