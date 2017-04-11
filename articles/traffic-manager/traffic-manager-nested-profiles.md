<properties
    pageTitle="Aninhada perfis do Gerenciador de tráfego | Microsoft Azure"
    description="Este artigo explica o recurso de 'Aninhado perfis' do Gerenciador de tráfego do Azure"
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

# <a name="nested-traffic-manager-profiles"></a>Perfis do Gerenciador de tráfego aninhadas

Gerenciador de tráfego inclui uma variedade de métodos de roteamento de tráfego que permitem que você controle como o Gerenciador de tráfego escolhe a extremidade que deve receber o tráfego de cada usuário final. Para obter mais informações, consulte [métodos de roteamento de tráfego do Gerenciador de tráfego](traffic-manager-routing-methods.md).

Cada perfil do Gerenciador de tráfego Especifica um único método de roteamento de tráfego. No entanto, há situações que exigem mais sofisticadas roteamento de tráfego que o roteamento fornecida por um único perfil do Gerenciador de tráfego. Você pode aninhar perfis do Gerenciador de tráfego para combinar as vantagens de mais de um método de roteamento de tráfego. Perfis aninhados permitem substituir o comportamento de tráfego gerenciador padrão de suporte maior e mais complexas implantações de aplicativos.

Os exemplos a seguir ilustram como usar perfis do Gerenciador de tráfego aninhada em vários cenários.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exemplo 1: Combinando 'Desempenho' e 'Ponderado' roteamento de tráfego

Suponha que você implantou um aplicativo no Azure regiões a seguir: Oeste EUA, Europa Ocidental e Leste Asiático. Você usa o método de roteamento de tráfego de 'Desempenho' de tráfego do gerente para distribuir tráfego para a região mais próxima ao usuário.

![Perfil do Gerenciador de tráfego único][1]

Agora, suponha que você deseja testar uma atualização para o seu serviço antes de implantá-la mais amplamente. Você deseja usar o método de roteamento de tráfego 'ponderado' para direcionar uma pequena porcentagem de tráfego para sua implantação de teste. Você pode configurar a implantação de teste junto com a implantação de produção existente na Europa Ocidental.

Você não pode combinar 'Ponderado' e ' desempenho em um único perfil-roteamento de tráfego. Para oferecer suporte a esse cenário, você criar um perfil do Gerenciador de tráfego usando os dois pontos de extremidade de Europa Oeste e o método de roteamento de tráfego de 'Ponderado'. Em seguida, adicione este perfil 'filho' como um ponto de extremidade para o perfil 'pai'. O perfil pai ainda usa o método de roteamento de tráfego de desempenho e contém as implantações globais como pontos de extremidade.

O diagrama a seguir ilustra este exemplo:

![Perfis do Gerenciador de tráfego aninhadas][2]

Nessa configuração, o tráfego orientado por meio do perfil pai distribui tráfego entre regiões normalmente. Na Europa Ocidental, o perfil aninhado distribui o tráfego para os pontos de extremidade de produção e de teste acordo com os pesos atribuídos.

Quando o perfil pai usa o método de roteamento de tráfego de 'Desempenho', cada ponto de extremidade deve ser atribuído a um local. O local é atribuído quando você configura o ponto de extremidade. Escolha o Azure região mais próximo à sua implantação. As regiões Azure são os valores de local compatíveis com a tabela de latência de Internet. Para obter mais informações, consulte [Gerenciador de tráfego 'Desempenho' método de roteamento de tráfego](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exemplo 2: Monitoramento de ponto de extremidade em perfis de aninhadas

Gerenciador de tráfego ativamente monitora a integridade de cada ponto de extremidade do serviço. Se um ponto de extremidade estiver com problemas, Gerenciador de tráfego direciona os usuários para pontos de extremidade alternativos para preservar a disponibilidade de seu serviço. Esse comportamento de monitoramento e failover do ponto de extremidade se aplica a todos os métodos de roteamento de tráfego. Para obter mais informações, consulte [Monitoramento de ponto de extremidade do Gerenciador de tráfego](traffic-manager-monitoring.md). Monitoramento de ponto de extremidade funciona de forma diferente para perfis aninhados. Com perfis aninhados, o perfil pai não executa verificações de integridade no filho diretamente. Em vez disso, a integridade dos pontos de extremidade do perfil de filho é usada para calcular a integridade geral do perfil filho. Essas informações de integridade são propagadas da hierarquia de perfil aninhadas. O perfil pai isso agregado integridade para determinar se direcione o tráfego para o perfil de filho. Consulte a seção [perguntas Frequentes](#faq) deste artigo para obter detalhes completos sobre o monitoramento de saúde dos perfis aninhados.

Retornando ao exemplo anterior, suponha que a distribuição de produção na Europa Oeste falhar. Por padrão, o perfil 'filho' direciona todo o tráfego para a implantação de teste. Se a implantação de teste também falhar, o perfil de pai determina que o perfil filho não deve receber tráfego desde que todos os pontos de extremidade de filho são não íntegros. Em seguida, o perfil pai distribui o tráfego para as outras regiões.

![Failover de perfil aninhada (comportamento padrão)][3]

Você pode ser satisfeito com essa organização. Ou você pode estar preocupado que todo o tráfego para Europa Oeste agora será a implantação de teste em vez de tráfego um subconjunto limitado. Independentemente da integridade da implantação do teste, você deseja alternar para as outras regiões quando a implantação de produção na Europa Oeste falha. Para habilitar esse failover, você pode especificar o parâmetro 'MinChildEndpoints' ao configurar o perfil filho como um ponto de extremidade no perfil pai. O parâmetro determina o número mínimo de pontos de extremidade disponíveis no perfil filho. O valor padrão é '1'. Para esse cenário, você pode definir o valor de MinChildEndpoints para 2. Abaixo esse limite, o perfil pai considera o perfil filho inteira está indisponível e direciona o tráfego a outros pontos de extremidade.

A figura a seguir ilustra essa configuração:

![Aninhada failover de perfil com 'MinChildEndpoints' = 2][4]

>[AZURE.NOTE]
>O método de roteamento de tráfego de 'Priority' distribui todo o tráfego para um único ponto de extremidade. Portanto, há pouco finalidade em uma configuração de MinChildEndpoints diferente de '1' perfil filho.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exemplo 3: Priorizamos regiões de failover em roteamento de tráfego de 'Desempenho'

O comportamento padrão para o método de roteamento de tráfego de 'Desempenho' destina-se evitar excesso carregar próximo ponto de extremidade e causando uma série de falhas em cascata. Quando um ponto de extremidade falha, todo o tráfego que faria foi direcionado para o ponto de extremidade será distribuído uniformemente para os outros pontos de extremidade em todas as regiões.

![Tráfego de 'Desempenho' roteamento com failover padrão][5]

No entanto, suponha que você prefere o failover de tráfego Europa Oeste EUA Oeste e apenas direcionar o tráfego para outras regiões quando ambas as extremidades estiverem disponíveis. Você pode criar esta solução usando um perfil de filho com o método de roteamento de tráfego de 'Priority'.

![Tráfego de 'Desempenho' roteamento com failover preferencial][6]

Como o ponto de extremidade da Europa Oeste tem prioridade maior que o ponto de extremidade do Oeste EUA, todo o tráfego é enviado para o ponto de extremidade da Europa Oeste quando ambas as extremidades estiverem online. Se o Europe Oeste falhar, seu tráfego será direcionado para Oeste EUA. Com o perfil aninhado, o tráfego é direcionado para da Ásia Oriental apenas quando Europa Oeste e Oeste EUA falham.

Você pode repetir esse padrão para todas as regiões. Substitua todos os três pontos de extremidade no perfil pai três perfis de filho, cada uma delas fornecendo uma sequência de failover prioridades.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exemplo 4: Controlando 'Desempenho' roteamento de tráfego entre vários pontos de extremidade na mesma região

Suponha que o método de roteamento de tráfego é usado em um perfil que tem mais de um ponto de extremidade de uma determinada região 'desempenho'. Por padrão, o tráfego direcionado a região é distribuído uniformemente por todos os pontos de extremidade disponíveis nessa região.

![Tráfego de 'Desempenho' roteamento de distribuição de tráfego do região (comportamento padrão)][7]

Em vez de adicionar vários pontos de extremidade na Europa Ocidental, esses pontos de extremidade são colocados em um perfil de filha separada. O perfil filho é adicionado ao pai o ponto de extremidade somente na Europa Ocidental. As configurações no perfil filho podem controlar a distribuição de tráfego com Europa Oeste habilitando o roteamento de tráfego baseado em prioridade ou ponderado dentro dessa região.

![Tráfego de 'Desempenho' roteamento com distribuição de tráfego do região personalizada][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exemplo 5: Configurações de monitoramento de por ponto de extremidade

Suponha que você está usando o Gerenciador de tráfego tranquilamente migrar tráfego de herdado local site para uma nova versão baseada em nuvem hospedada no Azure. Para o site herdado, você deseja usar a home page URI para monitorar a integridade do site. Mas para a nova versão baseada em nuvem, você está implementando uma página personalizada de monitoramento (caminho ' / monitor.aspx') que inclui verificações adicionais.

![Ponto de extremidade do Gerenciador de tráfego monitoramento (comportamento padrão)][9]

As configurações de monitoramento em um perfil de Gerenciador de tráfego aplicam a todos os pontos de extremidade dentro de um único perfil. Com perfis aninhados, você use um perfil diferente filho por site para definir diferentes configurações de monitoramento.

![Ponto de extremidade do Gerenciador de tráfego monitoramento com configurações de per ponto de extremidade][10]

## <a name="faq"></a>Perguntas Freqüentes

### <a name="how-do-i-configure-nested-profiles"></a>Como posso configurar perfis aninhados?

Perfis do Gerenciador de tráfego aninhados podem ser configurados usando o Gerenciador de recursos do Azure e as clássico APIs do restante do Azure, cmdlets do PowerShell do Azure e comandos do Azure entre plataformas. Eles também são suportados pelo novo portal do Azure. Eles não são suportados no portal do clássico.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Quantas camadas de aninhamento faz Gerenciador de tráfego oferece suporte?

Você pode aninhar perfis até 10 níveis de profundidade. 'Loop' não é permitido.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Pode misturar outros tipos de ponto de extremidade com perfis de filho aninhadas, no mesmo perfil Gerenciador de tráfego?

Sim. Não existem restrições sobre como combinar pontos de extremidade de diferentes tipos dentro de um perfil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Como o modelo de cobrança se aplicam aninhado perfis de?

Há não negativo preços impacto da usando perfis aninhados.

Gerenciador de tráfego cobrança tem dois componentes: verificações de integridade do ponto de extremidade e milhões de consultas DNS

- Verificações de integridade do ponto de extremidade: não há nenhum custo para um perfil de filho quando configurado como um ponto de extremidade em um perfil de pai. Monitorando os pontos de extremidade no perfil filho será cobrado no como de costume.
- Consultas DNS: cada consulta é contada apenas uma vez. Uma consulta em relação a um perfil de pai que retorna um ponto de extremidade de um perfil de filho é contada em relação ao perfil pai somente.

Para obter detalhes completos, consulte a [página do Gerenciador de tráfego de preços](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Existe um impacto de desempenho para perfis aninhados?

Não. Não há nenhum impacto no desempenho incorrido ao usar perfis aninhados.

Os servidores de nomes do Gerenciador de tráfego percorrer a hierarquia de perfil internamente ao processar cada consulta DNS. Uma consulta DNS para um perfil de pai pode receber uma resposta DNS com um ponto de extremidade de um perfil de filho. Um único registro CNAME é usado se você estiver usando um único perfil ou perfis aninhados. Não é necessário criar um registro CNAME para cada perfil na hierarquia.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Como o Gerenciador de tráfego calcular a integridade de um ponto de extremidade aninhada em um perfil pai?

O perfil pai não executa verificações de integridade no filho diretamente. Em vez disso, a integridade dos pontos de extremidade do perfil de filho são usados para calcular a integridade geral do perfil filho. Esta informação é propagada da hierarquia de perfil aninhadas para determinar a integridade do ponto de extremidade aninhada. O perfil pai usa esta integridade agregada para determinar se o tráfego pode ser direcionado ao filho.

A tabela a seguir descreve o comportamento do Gerenciador de tráfego verificações de integridade de um ponto de extremidade aninhado.

|Status de Monitor de perfil de filho|Status de Monitor de ponto de extremidade do pai|Anotações|
|---|---|---|
|Desativado. O perfil de filho foi desativado.|Interrompido|O estado de ponto de extremidade pai for interrompido, não desativado. O estado desativado é reservado para indicando que você tenha desativado o ponto de extremidade no perfil pai.|
|Degradado. Ponto de extremidade de perfil de pelo menos um filho está em um estado degradado.| Online: o número de pontos de extremidade Online no perfil filho pelo menos é o valor de MinChildEndpoints.<BR>CheckingEndpoint: o número de pontos de extremidade Online plus CheckingEndpoint no perfil filho pelo menos é o valor de MinChildEndpoints.<BR>Degradado: caso contrário.|Tráfego é roteado um ponto de extremidade de status CheckingEndpoint. Se MinChildEndpoints for definido muito alto, o ponto de extremidade sempre está danificado.|
|Online. Ponto de extremidade de perfil de pelo menos um filho é um estado Online. Nenhum ponto de extremidade está no estado degradado.|Consulte acima.||
|CheckingEndpoints. Ponto de extremidade de perfil de pelo menos um filho é 'CheckingEndpoint'. Não há pontos de extremidade são 'Online' ou 'Degradado'|Mesmo que acima.||
|Inativo. Todos os pontos de extremidade de perfil de filho estão desativado ou interrompido ou este perfil não tem nenhum ponto de extremidade.|Interrompido||


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [funcionamento do Gerenciador de tráfego](traffic-manager-how-traffic-manager-works.md)

Saiba como [criar um perfil do Gerenciador de tráfego](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png

