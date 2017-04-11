<properties
    pageTitle="Métodos de roteamento de tráfego de Gerenciador de tráfego - | Microsoft Azure"
    description="Este artigo ajudará você a entender os métodos de roteamento de tráfego diferente usados pelo Gerenciador de tráfego"
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

# <a name="traffic-manager-traffic-routing-methods"></a>Métodos de roteamento de tráfego do Gerenciador de tráfego

Gerenciador de tráfego Azure oferece suporte a três métodos de roteamento de tráfego para determinar como rotear o tráfego de rede para os vários pontos de extremidade do serviço. Gerenciador de tráfego aplica o método de roteamento de tráfego para cada consulta DNS que recebe. O método de roteamento de tráfego determina qual ponto de extremidade retornado na resposta DNS.

O suporte do Gerenciador de recursos do Azure para o Gerenciador de tráfego usa terminologia diferente que o modelo clássico de implantação. A tabela a seguir mostra as diferenças entre os termos do Gerenciador de recursos e clássico:

| Termos do Gerenciador de recursos | Termos clássico |
|-----------------------|--------------|
| Método de roteamento de tráfego | Método de balanceamento de carga |
| Método de prioridade | Método de failover |
| Método ponderado | Método de round robin |
| Método de desempenho | Método de desempenho |

Com base nos comentários do cliente, alteramos a terminologia para melhorar a clareza e reduzir a confusão comuns. Não há nenhuma diferença de funcionalidade.

Há três métodos de roteamento de tráfego disponíveis no Gerenciador de tráfego:

- **Prioridade:** Selecione 'Priority' quando desejar usar um ponto de extremidade do serviço principal para todo o tráfego e fornecer backups caso principal ou os pontos de extremidade de backup não estão disponíveis.
- **Ponderada:** Selecione 'ponderada' quando você quiser distribuir o tráfego em um conjunto de pontos de extremidade, seja uniforme ou acordo com pesos, que você definir.
- **Desempenho:** Selecione 'Desempenho' quando você tem pontos de extremidade em diferentes locais geográficos e deseja que os usuários finais para usar o ponto de extremidade "mais próximo" em termos da menor latência de rede.

Todos os perfis do Gerenciador de tráfego incluem monitoramento de integridade do ponto de extremidade e failover automático de ponto de extremidade. Para obter mais informações, consulte [Monitoramento de ponto de extremidade do Gerenciador de tráfego](traffic-manager-monitoring.md). Um único perfil do Gerenciador de tráfego pode usar apenas um método de roteamento de tráfego. Você pode selecionar um método de roteamento de tráfego diferente para o seu perfil a qualquer momento. As alterações são aplicadas em um minuto e sem tempo de inatividade é incorrido. Métodos de roteamento de tráfego podem ser combinados usando perfis do Gerenciador de tráfego aninhadas. Aninhamento permite sofisticadas e flexíveis configurações de roteamento de tráfego que atendem às necessidades dos aplicativos maiores e complexos. Para obter mais informações, consulte [perfis do Gerenciador de tráfego aninhadas](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Método de roteamento de tráfego de prioridade

Muitas vezes uma organização deseja fornecer confiabilidade para seus serviços Implantando um ou mais serviços de backup caso seu serviço primário falhar. O método de roteamento de tráfego de 'Priority' permite aos clientes Azure facilmente implementar esse padrão de failover.

![Gerenciador de tráfego Azure 'Priority' método de roteamento de tráfego][1]

O perfil do Gerenciador de tráfego contém uma lista de prioridades de pontos de extremidade do serviço. Por padrão, o Gerenciador de tráfego envia todo o tráfego para o principal ponto de extremidade (prioridade mais alta-). Se o ponto de extremidade primário não estiver disponível, o Gerenciador de tráfego rotear o tráfego para o segundo ponto de extremidade. Se ambas as extremidades primárias e secundárias não estiverem disponíveis, o tráfego vai para o terceiro e assim por diante. Disponibilidade do ponto de extremidade baseia-se no status configurado (ativado ou desativado) e o monitoramento de ponto de extremidade em andamento.

### <a name="configuring-endpoints"></a>Configurando pontos de extremidade

Com o Gerenciador de recursos do Azure, você configurar a prioridade de ponto de extremidade explicitamente usando a propriedade 'priority' para cada ponto de extremidade. Essa propriedade é um valor entre 1 e 1000. Valores mais baixos representam uma prioridade mais alta. Pontos de extremidade não podem compartilhar valores de prioridade. Definindo a propriedade é opcional. Quando omitido, uma prioridade padrão com base na ordem de ponto de extremidade é usada.

Com a interface de clássico, a prioridade de ponto de extremidade está configurada implicitamente. A prioridade baseia-se na ordem em que os pontos de extremidade estão listados na definição do perfil.

## <a name="weighted-traffic-routing-method"></a>Método de roteamento de tráfego ponderado

O método de roteamento de tráfego 'Ponderado' permite que você para distribuir o tráfego de maneira uniforme ou usar uma ponderação predefinida.

![Azure Gerenciador de tráfego 'Ponderada' roteamento de tráfego método][2]

O método de roteamento de tráfego ponderada, você pode atribuir um peso para cada ponto de extremidade na configuração de perfil do Gerenciador de tráfego. O peso é um inteiro de 1 a 1000. Este parâmetro é opcional. Se omitido, gerentes de tráfego usa um peso padrão de '1'.

Para cada consulta DNS recebida, o Gerenciador de tráfego escolhe aleatoriamente um ponto de extremidade disponível. A probabilidade de escolher um ponto de extremidade baseia-se nos pesos atribuídos a todos os pontos de extremidade disponíveis. Usando o mesmo peso em todos os resultados de pontos de extremidade em uma distribuição de tráfego par. Usando espessuras superiores ou inferiores em pontos de extremidade específicos faz com que esses pontos de extremidade a ser retornado mais ou menos frequência nas respostas DNS.

O método ponderado permite alguns cenários úteis:

- Atualização do aplicativo gradual: alocar uma porcentagem de tráfego para rotear para um novo ponto de extremidade e gradualmente aumentar o tráfego ao longo do tempo para 100%.
- Migração de aplicativos para o Azure: criar um perfil com pontos de extremidade do Azure e externos. Ajuste a espessura os pontos de extremidade preferir os pontos de extremidade de novo.
- Interromper de nuvem para capacidade adicional: expandir rapidamente uma implantação local na nuvem, colocando-o por trás de um perfil do Gerenciador de tráfego. Quando você precisar capacidade extra na nuvem, você pode adicionar ou ativar mais pontos de extremidade e especificar qual parte da tráfego vai para cada ponto de extremidade.

O novo portal Azure é compatível com a configuração de roteamento de tráfego ponderado. Espessuras não podem ser configuradas no portal do clássico. Você também pode configurar pesos usando o Gerenciador de recursos e versões clássicas do PowerShell do Azure, CLI e as APIs REST.

É importante entender o que os respostas DNS são armazenados em cache por clientes e por servidores DNS recursiva que os clientes usam para resolver nomes DNS. Esse cache pode ter um impacto em distribuições de tráfego ponderada. Quando o número de clientes e servidores DNS recursiva for grande, distribuição de tráfego funciona como esperado. No entanto, quando o número de clientes ou servidores DNS recursiva é pequeno, armazenamento em cache pode significativamente inclinar a distribuição de tráfego.

Casos comuns de uso incluem:

- Ambientes de desenvolvimento e testes
- Comunicações de aplicativo para
- Aplicativos destinados a uma estreita base de usuários que compartilham uma infraestrutura DNS recursiva comuns (por exemplo, funcionários de empresa conectando através de um proxy)

Esses efeitos de cache DNS são comuns a todos os tráfego baseado em DNS roteamento sistemas, não apenas Gerenciador de tráfego do Azure. Em alguns casos, explicitamente limpar o cache DNS pode fornecer uma solução alternativa. Em outros casos, um método de roteamento de tráfego alternativo pode ser mais apropriado.

## <a name="performance-traffic-routing-method"></a>Método de roteamento de tráfego de desempenho

Implantar pontos de extremidade em dois ou mais locais em todo o mundo pode melhorar a capacidade de resposta de muitos aplicativos rotear o tráfego para o local que ' mais se aproxime ' para você. O método de roteamento de tráfego de 'Desempenho' fornece esse recurso.

![Gerenciador de tráfego Azure 'Desempenho' método de roteamento de tráfego][3]

O ponto de extremidade 'mais se aproxime' não é necessariamente mais próximo medida pela distância geográfica. Em vez disso, o método de roteamento de tráfego de 'Desempenho' determina o ponto de extremidade mais próximo medir latência de rede. Gerenciador de tráfego mantém uma tabela de latência de Internet para controlar o tempo de ida e volta entre intervalos de endereços IP e cada centro de dados do Azure.

Gerenciador de tráfego procura o endereço IP de origem da solicitação DNS de entrada na tabela de latência da Internet. Gerenciador de tráfego escolhe um ponto de extremidade disponível no Azure data center que tem a menor latência para esse intervalo de endereços IP, em seguida, retorna o ponto de extremidade na resposta DNS.

Como explicado em [Como funciona o Gerenciador de tráfego](traffic-manager-how-traffic-manager-works.md), o tráfego não receber consultas DNS diretamente de clientes. Em vez disso, consultas DNS vêm do serviço DNS recursiva que os clientes estiverem configurados para usar. Portanto, o endereço IP usado para determinar o ponto de extremidade 'mais se aproxime' não é o endereço IP do cliente, mas é o endereço IP do serviço DNS recursiva. Na prática, esse endereço IP é um bom proxy para o cliente.

Gerenciador de tráfego atualiza a tabela de latência de Internet para a conta alterações na Internet global e novas regiões Azure regularmente. No entanto, desempenho do aplicativo varia de acordo com variações em tempo real de carga através da Internet. Roteamento de tráfego de desempenho não monitora carga em um ponto de extremidade do serviço específico. No entanto, se um ponto de extremidade ficar indisponível, Gerenciador de tráfego significa não nas respostas de consulta DNS.

Pontos de nota:

- Se o seu perfil contém vários pontos de extremidade na mesma região Azure, em seguida, Gerenciador de tráfego distribui tráfego uniformemente entre os pontos de extremidade disponíveis nessa região. Se você preferir uma distribuição de tráfego diferente dentro de uma região, você pode usar [perfis do Gerenciador de tráfego aninhadas](traffic-manager-nested-profiles.md).

- Se todos os pontos de extremidade habilitados em uma determinada região Azure estão degradados, o Gerenciador de tráfego distribui o tráfego entre todos os outros pontos de extremidade disponíveis em vez do ponto de extremidade mais próxima Avançar. Essa lógica impede que uma falha em cascata ocorrência pela sobrecarga não o ponto de extremidade mais próxima Avançar. Se você deseja definir uma sequência de failover preferencial, use [aninhada perfis do Gerenciador de tráfego](traffic-manager-nested-profiles.md).

- Ao usar o método de roteamento de tráfego de desempenho com pontos de extremidade externos ou pontos de extremidade aninhados, você precisa especificar o local desses pontos de extremidade. Escolha o Azure região mais próximo à sua implantação. Esses locais são os valores compatíveis com a tabela de latência de Internet.

- O algoritmo que escolhe o ponto de extremidade é determinante. Consultas DNS repetidas do mesmo cliente são direcionadas para o mesmo ponto de extremidade. Normalmente, clientes usam os servidores DNS recursiva diferente quando estiver viajando. O cliente pode ser roteado para um ponto de extremidade diferente. Roteamento também pode ser afetado por atualizações para a tabela de latência de Internet. Portanto, o método de roteamento de tráfego de desempenho não garante que um cliente sempre é roteado para o mesmo ponto de extremidade.

- Quando a tabela de latência de Internet forem alterados, você pode perceber que alguns clientes são direcionados para um ponto de extremidade diferente. Essa alteração de roteamento é mais precisa com base em dados de latência atuais. Essas atualizações são essenciais para manter a precisão do roteamento de tráfego de desempenho como Internet continuamente evolui.

## <a name="next-steps"></a>Próximas etapas

Aprenda a desenvolver aplicativos de alta disponibilidade usando [monitoramento de ponto de extremidade do Gerenciador de tráfego](traffic-manager-monitoring.md)

Saiba como [criar um perfil do Gerenciador de tráfego](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png
