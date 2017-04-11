## <a name="dynamic-service-plan"></a>Plano de serviço dinâmico

No plano de dinâmico serviço, seus aplicativos de função serão atribuídos a uma instância de aplicativo de função. Se necessário mais instâncias serão adicionadas dinamicamente.
Essas instâncias podem abranger em vários recursos de computação, aproveitando ao máximo a infraestrutura do Azure disponível. Além disso, suas funções serão executadas em paralelo minimizando o tempo total necessário para processar solicitações. Tempo de execução para cada função é adicionado para cima, em segundos e agregado pelo aplicativo do função contendo. Com o custo controlada pelo número de instâncias, tamanho de memória e tempo de execução total, medida em gigabytes segundos. Isso é uma excelente opção se suas necessidades de computação são intermitentes ou os horários de trabalho tendem a ser muito curto que permite a você pagar somente para os recursos de computação quando efetivamente estão em uso.   

### <a name="memory-tier"></a>Nível de memória

Dependendo de sua função precisa você pode selecionar a quantidade de memória necessária para executá-las no aplicativo função (contêiner de funções).
As opções de tamanho de memória variam de **128 MB para 1536 MB**. O tamanho da memória selecionado corresponde ao conjunto de trabalho necessários para todas as funções que fazem parte de seu aplicativo de função. Se seu código exigir mais memória do que o tamanho selecionado, a instância do aplicativo de função será encerrada devido a falta de memória.

### <a name="scaling"></a>Dimensionamento

A plataforma de funções do Azure irá avaliar o tráfego precisa, com base nos disparadores configurados, decidir quando dimensionar para cima ou para baixo. O detalhamento de dimensionamento é o aplicativo de função. Dimensionamento, nesse caso, significa adicionando mais ocorrências de um aplicativo de função. Inversamente como tráfego falhar, instâncias do aplicativo de função são desativado-eventualmente dimensionamento para baixo para zero quando nenhum estiverem em execução.  

### <a name="resource-consumption-and-billing"></a>Consumo de recursos e de cobrança

Na dinâmica alocação de recursos de modo é feita diferente o plano de serviço de aplicativo padrão, portanto, o modelo de consumo também é diferente, permitindo a um modelo de "pagamento por uso". Consumo será relatado por aplicativo de função, somente para hora quando o código está sendo executado.  
Ele é computado multiplicando o tamanho de memória (em GB) pela quantidade total de tempo de execução (em segundos) para todas as funções executando dentro desse aplicativo de função. A unidade de consumo será **GB-s (Gigabyte segundos)**.