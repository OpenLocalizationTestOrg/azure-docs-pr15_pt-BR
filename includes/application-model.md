# <a name="compute"></a>Calcular

Azure permite implantar e monitorar o código do seu aplicativo em execução dentro de um data center Microsoft. Quando você cria um aplicativo e executá-lo no Azure, o código e configuração junto é chamado um Azure hospedado serviço. Serviços hospedados são fáceis de gerenciar, dimensionar para cima e para baixo, reconfigurar e atualizar com novas versões do código de seu aplicativo. Este artigo aborda o Azure hospedado no modelo de aplicativo de serviço.<a id="compare" name="compare"></a>

## Sumário<a id="_GoBack" name="_GoBack"></a>

-   [Benefícios do modelo de aplicativo do Azure][]
-   [Conceitos centrais de serviço hospedado][]
-   [Considerações de Design de serviço hospedado][]
-   [Projetar seu aplicativo de escala][]
-   [Definição de serviço hospedado e configuração][]
-   [O arquivo de definição de serviço][]
-   [O arquivo de configuração do serviço][]
-   [Criar e implantar um serviço hospedado][]
-   [Referências][]

## <a id="benefits"> </a>Benefícios de modelo de aplicativo do azure

Quando você implanta seu aplicativo como um serviço hospedado, Azure cria um ou mais VMs (máquinas virtuais) que contêm código do seu aplicativo e inicializa VMs em máquinas físicas que reside em um dos centros de dados do Azure. Como as solicitações de cliente para seu aplicativo hospedado inserir data center, um balanceador de carga distribui essas solicitações igualmente VMs. Enquanto o aplicativo está hospedado no Azure, ele obtém três benefícios importantes:

-   **Alta disponibilidade.** Alta disponibilidade significa que Azure garante que seu aplicativo está sendo executado tanto quanto possível e é possível responder a solicitações de cliente. Se seu aplicativo termina (devido a uma exceção não tratada, por exemplo), e em seguida, Azure detecta isso, e ela será automaticamente novamente iniciar o aplicativo. Se máquina seu aplicativo é executado em experiências algum tipo de falha de hardware, depois Azure também detectar isso e automaticamente criará uma nova VM em outra máquina física de trabalho e executar seu código de lá. Observação: Ordem seu aplicativo obter o contrato de nível de serviço da Microsoft de 99,95% disponível, você deve ter pelo menos duas VMs executando o código do seu aplicativo. Isso permite que uma máquina virtual processar solicitações de cliente enquanto Azure move seu código de uma máquina virtual falhou para uma máquina virtual novo, boa.

-   **Escalabilidade.** Azure permite facilmente e alterar dinamicamente o número de VMs executando o código do seu aplicativo para lidar com a carga real sendo colocada em seu aplicativo. Isso permite que você ajuste seu aplicativo para a carga de trabalho que seus clientes estiver colocando nele pagando somente pelo VMs necessárias quando precisar. Quando você quiser alterar o número de VMs, Azure responde em poucos minutos lhe permite alterar dinamicamente o número de VMs executando quantas vezes conforme desejado.

-   **Capacidade de gerenciamento.** Como Azure é uma plataforma como uma oferta de serviço (PaaS), ele gerencia a infraestrutura (o próprio hardware, energia e rede) necessária para manter essas máquinas em execução. Azure também gerencia a plataforma, que garante que um sistema operacional atualizado com todos os patches corretos e atualizações de segurança, bem como atualizações de componentes como o .NET Framework e o servidor de informações da Internet. Porque todas as VMs estiver executando o Windows Server 2008, o Azure fornece recursos adicionais, como monitoramento de diagnóstico, suporte de desktop remoto, firewalls e configuração de repositório de certificado. Todos esses recursos são fornecidos sem nenhum custo extra. Na verdade, quando você executar o aplicativo no Azure, a licença de sistema operacional (OS) do Windows Server 2008 está incluída. Desde que todas as VMs estiver executando o Windows Server 2008, qualquer código que é executado no Windows Server 2008 funciona bem quando executado no Azure.

## <a id="concepts"> </a>Hospedado conceitos centrais de serviço

Quando seu aplicativo for implantado como um serviço hospedado no Azure, ele é executado como uma ou mais *funções.* Uma *função* simplesmente se refere a configuração e arquivos de aplicativo. Você pode definir uma ou mais funções para seu aplicativo, cada uma com seu próprio conjunto de arquivos de aplicativo e configuração. Para cada função em seu aplicativo, você pode especificar o número de VMs ou *instâncias de função*, para executar. A figura a seguir mostram dois exemplos simples de um aplicativo modelados como um serviço hospedado usando funções e instâncias de função.

##### <a name="figure-1-a-single-role-with-three-instances-vms-running-in-an-azure-data-center"></a>Figura 1: Uma única função com três instâncias (VMs) em execução em uma central de dados do Azure

![imagem][0]

##### <a name="figure-2-two-roles-each-with-two-instances-vms-running-in-an-azure-data-center"></a>Figura 2: Duas funções, cada uma com duas instâncias (VMs), em execução em uma central de dados do Azure

![imagem][1]

Instâncias de função normalmente processam solicitações de clientes de Internet inserindo o Centro de dados por meio do que é chamado um *ponto de extremidade de entrada*. Uma única função pode ter pontos de extremidade de 0 ou mais entrados. Cada ponto de extremidade indica um protocolo (HTTP, HTTPS ou TCP) e uma porta. É comum para configurar uma função para ter dois pontos de extremidade de entrada: HTTP listening nas portas 80 e HTTPS escutar na porta 443. A figura a seguir mostra um exemplo de duas funções diferentes com diferentes pontos de extremidade entrados direciona solicitações de cliente para eles.

![imagem][2]

Quando você cria um serviço hospedado no Azure, ele é atribuído um endereço IP publicamente endereçamento que clientes podem usar para acessá-lo. Ao criar o serviço hospedado, você deve selecionar também um prefixo de URL que é mapeado para o endereço IP. Esse prefixo deve ser exclusivo, como você essencialmente é reserva o *prefixo*. cloudapp.net URL para que ninguém possa tê-lo. Os clientes se comunicar com seu instâncias de função usando a URL. Geralmente, você não será distribuir ou publicar o Azure *prefixo*. cloudapp.net URL. Em vez disso, você irá adquirir um nome DNS de seu registrador de DNS de escolha e configure seu nome DNS para redirecionar solicitações de cliente para a URL do Azure. Para obter mais detalhes, consulte [Configurar um nome de domínio personalizado no Azure][].

## <a id="considerations"> </a>Hospedado considerações de Design de serviço

Ao criar um aplicativo para executar em um ambiente de nuvem, há várias considerações para pensar sobre como latência, alta disponibilidade e escalabilidade.

Decidir onde localizar seu código de aplicativo é uma consideração importante durante a execução de um serviço hospedado no Azure. É comum para implantar seu aplicativo centros de dados que estão mais próximos aos clientes para reduzir a latência e obter o melhor desempenho possível.
No entanto, você pode escolher um data center mais próximo de sua empresa ou mais próximo aos seus dados se você tiver algumas preocupações jurisdictional ou legais sobre seus dados e onde ele está localizado. Há seis centros de dados em todo o mundo capaz de hospedar seu código do aplicativo. A tabela a seguir mostra os locais disponíveis:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**País/região**

</td>
<td style="width: 200px;">
**Regiões sub**

</td>
</tr>
<tr>
<td>
Estados Unidos

</td>
<td>
Sul Central & Norte Central

</td>
</tr>
<tr>
<td>
Europa

</td>
<td>
América do Norte e Oeste

</td>
</tr>
<tr>
<td>
Ásia

</td>
<td>
Sudeste & Leste

</td>
</tr>
</tbody>
</table>
Ao criar um serviço hospedado, selecione uma região sub indicando o local em que deseja que seu código seja executado.

Para obter alta disponibilidade e escalabilidade, é muito importante que os dados do aplicativo sejam mantidas em um repositório central acessível a várias instâncias de função. Para ajudar com isso, o Azure oferece várias opções de armazenamento como blobs, tabelas e banco de dados SQL. Consulte o artigo de [Ofertas de armazenamento de dados no Azure][] para obter mais informações sobre essas tecnologias de armazenamento. A figura a seguir mostra como o balanceador de carga dentro do Azure data center distribui solicitações de clientes para instâncias de função diferente todos têm acesso ao mesmo armazenamento de dados.

![imagem][3]

Geralmente, você deseja localizar o código do seu aplicativo e seus dados no mesmo data center, pois isso permite baixa latência (melhor desempenho) quando o código do seu aplicativo acessa os dados. Além disso, você não é cobrados para largura de banda quando dados são movidos em torno de dentro do mesmo data center.

## <a id="scale"> </a>Projetar seu aplicativo de escala

Às vezes, talvez você queira fazer um único aplicativo (como um site simples) e a hospedado no Azure. Mas frequentemente, seu aplicativo pode consistir em várias funções que todos trabalham juntos. Por exemplo, na figura abaixo, há duas instâncias da função do site, três instâncias da função de processamento de pedidos e uma instância da função gerador de relatórios. Essas funções estão funcionando juntos e o código para todos eles pode ser reunido e implantado como uma unidade única até o Azure.

![imagem][4]

O principal motivo para dividir um aplicativo em diferentes funções a cada em execução no seu próprio conjunto de instâncias de função (isto é, VMs) é dimensionar as funções de maneira independente. Por exemplo, durante as férias, muitos clientes podem estar adquirindo produtos da sua empresa, então você pode querer aumentar o número de instâncias de função executando sua função de site, bem como o número de instâncias de função executando sua função de processamento de pedidos. Após as férias, você pode obter muitas produtos devolvidos, para que você ainda pode precisar muitas instâncias de site, mas menos instâncias de processamento de pedidos. Durante o resto do ano, talvez seja necessário apenas instâncias alguns site e processamento de pedidos. Em toda a isso, talvez seja necessário apenas uma instância de gerador de relatórios. A flexibilidade de implantações baseado em função no Azure permite adaptar facilmente seu aplicativo às suas necessidades de negócios.

É comum ter a função instâncias dentro de seu serviço hospedado se comunicar com os outros. Por exemplo, a função de site aceita a ordem de um cliente, mas, em seguida, ele libera a ordem de processamento para as instâncias de função de processamento de pedidos. A melhor maneira de passar o formulário de trabalho de um conjunto de instâncias de função em outro conjunto de instâncias está usando a tecnologia de fila fornecida pelo Azure, o serviço de fila ou filas de barramento de serviço. O uso de uma fila é uma parte essencial da história. Fila permite que o serviço hospedado dimensionar suas funções independentemente permitindo que você saldo a carga de trabalho em relação custo. Se o número de mensagens na fila aumenta ao longo do tempo, você pode dimensionar o número de instâncias de função de processamento de pedidos. Se o número de mensagens na fila diminui ao longo do tempo, você pode dimensionar o número de instâncias de função de processamento de pedidos. Dessa forma, você está pagando somente para as instâncias necessárias para lidar com a carga de trabalho real.

Fila também fornece confiabilidade. Quando o dimensionamento para baixo o número de instâncias de função de processamento de pedidos, Azure decide quais instâncias para finalizar. Ele pode decidir finalizar uma instância que está no meio de uma mensagem de fila de processamento. No entanto, porque o processamento da mensagem não é concluído com êxito, a mensagem fica visível novamente para outra instância de função processamento de pedidos que coleta e processa-la. Devido a visibilidade da mensagem de fila, há garantia mensagens eventualmente obter processada. Fila também funciona como um balanceador de carga distribuindo efetivamente suas mensagens instâncias de todo e qualquer função que mensagens de solicitação dele.

Para as instâncias de função de site, você pode monitorar o tráfego aí neles e decidir dimensionar o número-las para cima ou para baixo também. Fila permite dimensionar o número de instâncias de função de site independentemente dos instâncias de função processamento de pedidos. Isso é muito poderoso e fornece muita flexibilidade. É claro que, se seu aplicativo consiste em funções adicionais, você pode adicionar filas adicionais como o canal para se comunicar entre eles para aproveitar a mesma escala e benefícios de custo.

## <a id="defandcfg"> </a>Hospedado definição de serviço e configuração

Implantar um serviço hospedado no Azure requer que você também tiver um arquivo de definição de serviço e um arquivo de configuração do serviço. Ambos os arquivos são arquivos XML e permitem especificar maneira declarativa opções de implantação de seu serviço hospedado. O arquivo de definição de serviço descreve todas as funções que compõem seu serviço hospedado e como eles se comunicar. O arquivo de configuração do serviço descreve o número de instâncias para cada função e configurações usadas para configurar cada instância de função.

## <a id="def"> </a>o arquivo de definição de serviço

Como mencionado anteriormente, a definição de serviço (CSDEF) o arquivo é um arquivo XML que descreve as várias funções que compõem seu aplicativo completo. O esquema completo para o arquivo XML pode ser encontrado aqui: [[http://msdn.microsoft.com/library/windowsazure/ee758711.aspx]].
O arquivo CSDEF contém um elemento WebRole ou WorkerRole para cada função que deseja em seu aplicativo. Implantar uma função como uma função da web (usando o elemento de WebRole) significa que o código será executado em uma instância de função que contém o Windows Server 2008 e servidor de informações da Internet (IIS).
Implantando uma função como uma função de trabalho (usando o elemento de WorkerRole) significa que a instância da função terá o Windows Server 2008 nele (IIS não será instalado).

Certamente, você pode criar e implantar uma função de trabalho que usa algum outro mecanismo para escutar solicitações de entrada da web (por exemplo, seu código poderia criar e usar um HttpListener .NET). Como as instâncias de função são todos executando o Windows Server 2008, seu código pode realizar operações normalmente disponíveis para um aplicativo em execução no Windows Server
2008.

Para cada função, você indica o tamanho desejado de máquina virtual que instâncias dessa função devem usar. A tabela a seguir mostra os vários tamanhos de máquina virtual disponíveis hoje e os atributos de cada:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**Tamanho de máquina virtual**

</td>
<td>
**CPU**

</td>
<td>
**RAM**

</td>
<td>
**Disco**

</td>
<td>
**Rede de pico e/s**

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extra pequeno**

</td>
<td>
1 x 1.0 GHz

</td>
<td>
768 MB

</td>
<td>
20GB

</td>
<td>
\~5 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Pequeno**

</td>
<td>
1 x 1,6 GHz

</td>
<td>
1,75 GB

</td>
<td>
225GB

</td>
<td>
\~100 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Média**

</td>
<td>
2 x 1,6 GHz

</td>
<td>
3,5 GB

</td>
<td>
490GB

</td>
<td>
\~200 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Grande**

</td>
<td>
4 x 1,6 GHz

</td>
<td>
7 GB

</td>
<td>
1TB

</td>
<td>
\~400 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extra grande**

</td>
<td>
8 x 1,6 GHz

</td>
<td>
14 GB

</td>
<td>
2TB

</td>
<td>
\~800 Mbps

</td>
</tr>
</tbody>
</table>
Cobrado por hora para cada máquina virtual use como uma instância da função e também cobrado para quaisquer dados que suas instâncias de função enviam fora do data center. Você não é cobrados para dados inserindo o Centro de dados. Para obter mais informações, consulte [] [Azure preços]. Em geral, é recomendável usar várias instâncias de função pequenas em vez de algumas instâncias grandes para que seu aplicativo seja mais flexível para falha. Afinal, as instâncias de função a menos que você tem, é mais desastroso uma falha em um para seu aplicativo geral. Além disso, como mencionado anteriormente, você deve implantar pelo menos duas instâncias para cada função para obter o contrato de nível de serviço de 99,95% Microsoft fornece.

O arquivo de definição (CSDEF) do serviço também é onde você deseja especificar muitos atributos sobre cada função em seu aplicativo. Aqui estão alguns dos itens mais úteis disponíveis para você:

-   **Certificados**. Você pode usar certificados para criptografar dados ou se o seu serviço da web oferece suporte a SSL. Todos os certificados que precisam ser carregado Azure. Para obter mais informações, consulte [Gerenciando certificados no Azure][]. Esta configuração de XML instala carregados anteriormente no certificados para o repositório de certificados da instância de função para que eles são utilizáveis por seu código do aplicativo.

-   **Nomes de configuração de configuração**. Os valores que deseja que seus aplicativos para ler enquanto executa em uma instância de função. O valor real das definições de configuração está definido no arquivo de configuração (CSCFG) do serviço que pode ser atualizado a qualquer momento sem precisar reimplantar seu código. Na verdade, você pode código os aplicativos de forma para detectar os valores de configuração alterada sem gerar qualquer tempo de inatividade.

-   **Pontos de extremidade de entrada**. Aqui você pode especificar qualquer HTTP, HTTPS ou TCP pontos de extremidade (com portas) que você deseja expor para o mundo exterior por meio de seu *prefixo*. cloadapp.net URL. Quando Azure implanta seu cargo, ele irá configurar o firewall na instância do função automaticamente.

-   **Pontos de extremidade internos**. Aqui você especifica HTTP ou TCP pontos de extremidade quaisquer desejado expostos para outras instâncias de função que são implantadas como parte do seu aplicativo. Pontos de extremidade internos permitir que todas as instâncias de função dentro de seu aplicativo para conversar com os outros, mas não estão acessíveis para quaisquer instâncias de função que estão fora de seu aplicativo.

-   **Importar módulos**. Opcionalmente, esses instalar componentes úteis em suas instâncias de função. Componentes existem para monitoramento diagnóstico, área de trabalho remota e Azure Connect (que permite sua instância de função acessar recursos de local por meio de um canal seguro).

-   **Armazenamento local**. Isso aloca uma subpasta na instância do função para seu aplicativo para usar. Ela é descrita mais detalhadamente no artigo [Ofertas de armazenamento de dados no Azure][] .

-   **Tarefas de inicialização**. Tarefas de inicialização oferecem uma maneira de instalar componentes de pré-requisito em uma instância da função como ele inicializado. As tarefas podem ser executados elevadas como um administrador se necessário.

## <a id="cfg"> </a>o arquivo de configuração do serviço

O arquivo de configuração (CSCFG) do serviço é um arquivo XML que descreve as configurações que podem ser alteradas sem reimplantar o aplicativo. O esquema completo para o arquivo XML pode ser encontrado aqui: [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][].
O arquivo CSCFG contém um elemento de função para cada função em seu aplicativo. Aqui estão alguns dos itens que você pode especificar no arquivo CSCFG:

-   **Versão do sistema operacional**. Este atributo permite que você selecione a versão do sistema operacional (OS) que deseja usada para todas as instâncias de função executando o código do seu aplicativo. Este sistema operacional é conhecido como o *sistema operacional convidado*, e cada nova versão inclui os últimos patches de segurança e atualizações disponíveis no momento em que o sistema operacional convidado é lançado. Se você definir o valor do atributo osVersion como "\*", então Azure atualizará automaticamente o sistema operacional convidado em cada uma das suas instâncias de função como estarão disponíveis novas versões de sistema operacional de convidado. No entanto, você pode deixar de usar as atualizações automáticas, selecionando um versão do sistema operacional de convidado específico. Por exemplo, definindo o atributo de osVersion para um valor de "WA-convidado-SO-2,8\_201109-01" faz com que todas as suas instâncias de função obter o que é descrito nesta página da web: [http://msdn.microsoft.com/library/hh560567.aspx][]. Para obter mais informações sobre versões de sistema operacional de convidado, consulte [Gerenciar atualizações para o sistema operacional convidados do Azure].

-   **Instâncias**. Valor do elemento indica o número de instâncias de função desejado provisionado executando o código para uma função em particular. Como você pode carregar um novo arquivo CSCFG no Azure (sem reimplantar seu aplicativo), é muito simple alterar o valor para esse elemento e carregar um novo arquivo CSCFG para aumentar ou diminuir o número de instâncias de função executando o código do seu aplicativo dinamicamente. Isso permite que você facilmente dimensionar seu aplicativo para cima ou para baixo até exigências de carga de trabalho real atender enquanto também controla quanto cobrado para executar as instâncias de função.

-   **Valores de configuração de configuração**. Este elemento indica valores configurações (conforme definido no arquivo CSDEF). Sua função pode ler esses valores enquanto ele é executado. Esses valores de configurações são normalmente utilizadas para cadeias de caracteres de conexão ao banco de dados do SQL ou ao armazenamento do Azure, mas eles podem ser usados para qualquer finalidade desejada.

## <a id="hostedservices"> </a>Criar e implantar um serviço hospedado

A criação de um serviço hospedado requer que você primeiro acesse o [Portal de gerenciamento do Azure] e provisionar um serviço hospedado, especificando um prefixo DNS e o Centro de dados você realmente deseja seu código em execução no. Em seguida, em seu ambiente de desenvolvimento, você criar seu arquivo de definição (CSDEF) de serviço, criar seu código de aplicativo e pacote (zip) todos esses arquivos em um arquivo de pacote (CSPKG) do serviço. Você também deve preparar seu arquivo de configuração (CSCFG) do serviço. Para implantar sua função, você carregar os arquivos CSPKG e CSCFG com a API de gerenciamento de serviço do Azure. Uma vez implantado, Azure, será provisionar instâncias de função no Centro de dados (com base em dados de configuração), extrair o código do seu aplicativo do pacote, copiá-lo para as instâncias de função e as instâncias de inicialização. Agora, seu código está em execução.

A figura a seguir mostra os arquivos CSPKG e CSCFG criados em seu computador de desenvolvimento. O arquivo CSPKG contém o arquivo CSDEF e o código de duas funções. Depois de carregar os arquivos CSPKG e CSCFG com a API de gerenciamento de serviço do Azure, o Azure cria instâncias de função no Centro de dados. Neste exemplo, o arquivo CSCFG indicado que Azure deve criar três instâncias de função \#1 e duas instâncias de função \#2.

![imagem][5]

Para obter mais informações sobre a implantação, atualizando e reconfiguração suas funções, consulte o artigo [Implantando e atualizando aplicativos do Azure][] .<a id="Ref" name="Ref"></a>

## <a id="references"> </a>Referências

-   [Criando um serviço hospedado para o Azure][]

-   [Gerenciando serviços hospedados no Azure][]

-   [Migrar aplicativos para o Azure][]

-   [Configurando um aplicativo do Azure][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Escrito por Jeffrey Richter (Wintellect)</p>

</div>

  [Benefícios do modelo de aplicativo do Azure]: #benefits
  [Conceitos centrais de serviço hospedado]: #concepts
  [Considerações de Design de serviço hospedado]: #considerations
  [Projetar seu aplicativo de escala]: #scale
  [Definição de serviço hospedado e configuração]: #defandcfg
  [O arquivo de definição de serviço]: #def
  [O arquivo de configuração do serviço]: #cfg
  [Criar e implantar um serviço hospedado]: #hostedservices
  [Referências]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configurando um nome de domínio personalizado no Azure]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
  [Ofertas de armazenamento de dados no Azure]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Azure Pricing]: http://www.windowsazure.com/pricing/calculator/
  [Gerenciando certificados no Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [Gerenciando atualizações para os convidados Azure SO]: http://msdn.microsoft.com/library/ee924680.aspx
  [Portal de gerenciamento do Azure]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Implantando e atualizando aplicativos do Azure]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
  [Criando um serviço hospedado para o Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Gerenciando serviços hospedados no Azure]: http://msdn.microsoft.com/library/gg433038.aspx
  [Migrar aplicativos para o Azure]: http://msdn.microsoft.com/library/gg186051.aspx
  [Configurando um aplicativo do Azure]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx
