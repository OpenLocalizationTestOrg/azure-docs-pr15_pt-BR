<properties 
    pageTitle="Painel de controle, Monitor, escala, configurar, conexões de híbrido nos serviços do BizTalk e | Microsoft Azure" 
    description="Saiba mais sobre os controles e monitorar o desempenho nas guias portal clássicos de Serviços BizTalk: Dashboard, Monitor, escala, configurar e híbrido conexões. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="mandia"/>




# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Examine as guias Dashboard, Monitor, escala, configurar e Conexão híbrido

Depois de criar seu BizTalk Service e implantar seu aplicativo, você pode alterar algumas das configurações BizTalk Service e monitorar o desempenho do aplicativo. 

Quando você abre o Azure portal clássico, são colocados automaticamente na guia **Todos os itens** . Para exibir seu BizTalk Service, selecione seu BizTalk Service na guia **Todos os itens** ou selecione a guia **Serviços BIZTALK** ; e, em seguida, selecione seu nome de BizTalk Service.

Isso abre uma nova janela com guias a seguir. Este tópico descreve essas guias.

## <a name="quick-start-quick-startquickstart"></a>Início rápido (![Início rápido][QuickStart])
Dependendo da edição de Serviços BizTalk, todas as opções listadas podem não estar disponíveis. 
<table border="1">
    <tr>
        <td><strong>Obtenha as ferramentas</strong></td>
        <td>Baixe o SDK dos serviços do BizTalk para instalar os modelos de projeto do Visual Studio em seu computador de desenvolvimento local. Esses modelos criam os <strong>Serviços do BizTalk</strong> (ponte) e os projetos de <strong>Artefatos do serviço BizTalk</strong> (transformação) Visual Studio que são implantados em seu BizTalk Service.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">Como faço para começar a usar o SDK dos serviços do Azure BizTalk</a> e <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">instalar o SDK dos serviços do Azure BizTalk</a> lista as etapas para começar.
        </td>
    </tr>
    <tr>
        <td><strong>Criar parceiro contratos</strong></td>
        <td>Abre o Portal de serviços do Azure BizTalk hospedado no Azure onde adicionar parceiros e criar X12, AS2 e os contratos de EDIFACT EDI.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurando componentes de EDI mensagens no Portal de serviços do BizTalk</a> lista as etapas para começar.
        </td>
    </tr>

<tr>
        <td><strong>Saiba mais sobre os serviços do BizTalk</strong></td>
        <td>Vá para o <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">Centro de aprendizagem</a> para saber mais sobre os serviços do Azure BizTalk.</td>
</tr>
</table>


Na barra de tarefas na parte inferior, você pode:

<table border="1">

<tr>
<td><strong>Gerenciar</strong> sua implantação do aplicativo</td>
<td>Abre o portal de serviços do Azure BizTalk. O Portal de serviços do BizTalk é a entrada da configuração de EDI, incluindo adicionando parceiros e criar X12, AS2 e os contratos de EDIFACT.
<br/><br/>
Esta é a mesma que <strong>Criar acordos de parceiro</strong> na guia de <strong>Início rápido</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurando componentes de EDI mensagens no Portal de serviços do BizTalk</a> fornece mais informações sobre o Portal de serviços do BizTalk.</td>
</tr>

<tr>
<td><strong>Informações de Conexão</strong> do Namespace de controle de acesso</td>
<td>Quando você selecionar informações de Conexão, o Namespace de controle de acesso, o emissor padrão e a chave padrão são exibidos. Você pode copiar esses valores.
<br/><br/>
Você também pode abrir o Portal de controle de acesso. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Criar um controle de acesso Namespace</a> fornece mais informações sobre o Portal de controle de acesso.</td>
</tr>

<tr>
<td><strong>Sincronizar teclas</strong> na conta de armazenamento</td>
<td>Quando você cria uma conta de armazenamento, uma chave primária e chave secundária são criados automaticamente. Essas chaves de criptografia controlam o acesso à sua conta de armazenamento. Seu BizTalk Service usa automaticamente a chave primária. <strong>Teclas de sincronização</strong> permitem aos usuários alternar entre a chave primária e a chave secundária sem interromper o BizTalk Service.
<br/><br/>
Por exemplo, deseja o BizTalk Service usar uma nova chave primária para a conta de armazenamento. Para fazer isso:
<br/><br/>
<ol>
<li>Selecione seu BizTalk Service e <strong>Teclas de sincronização</strong>. Selecione a chave secundária. Quando você fizer isso, o BizTalk Service inicia usando a chave secundária.</li>
<li>No portal do Azure clássico, selecione sua conta de armazenamento e gerar a chave primária. Lembre-se de que seu BizTalk Service está usando a tecla secundário.</li>
<li>Selecione seu BizTalk Service e <strong>Teclas de sincronização</strong>. Agora, selecione a chave primária. Esta é a nova chave primária é gerado novamente.</li>
<li>No portal do Azure clássico, selecione sua conta de armazenamento e gerar a chave secundária.</li>
</ol>
<br/>
Esse processo é chamado "teclas de sobreposição". O objetivo é permitir que os usuários alternar entre a chave primária e a chave secundária sem interromper o BizTalk Service.</td>
</tr>

<tr>
<td><strong>Excluir</strong> seu aplicativo</td>
<td>Quando você selecionar Excluir, seu BizTalk Service e todos os itens implantados são removidos.</td>
</tr>
</table>


## <a name="dashboard"></a>Painel de controle
Dependendo da edição de Serviços BizTalk, todas as opções listadas podem não estar disponíveis. 

Quando você seleciona o seu nome de BizTalk Service, a guia Painel é exibida. No painel de controle, você pode:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Visão geral de uso: Mostra o número de conexões de híbrido usados
Também exibe o uso de dados em GB. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Gráfico de métrico: Mostra uma lista fixa de métricas de desempenho
Essas métricas fornecem os valores em tempo real sobre a integridade do BizTalk Service. Você também pode escolher os valores **relativo** ou **absoluto** e o intervalo de tempo de **intervalo** das métricas que são exibidos no gráfico. 

Para obter uma descrição das seguintes métricas de desempenho, vá para [Métricas disponíveis](#Metrics) neste tópico.


##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Compreensão: Lista suas propriedades BizTalk Service

<table border="1">

<tr>
<td><strong>Atualizar credenciais de banco de dados de rastreamento</strong></td>
<td>Altera o nome de usuário e senha usados para efetuar login no banco de dados do controle.</td>
</tr>
<tr>
<td><strong>Atualizar um certificado SSL</strong></td>
<td>Pode atualizar o BizTalk Service para usar um certificado SSL diferente. Um certificado SSL auto-assinado é automaticamente criado quando você <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">criar o serviço BizTalk</a>.</td>
</tr>
<tr>
<td><strong>Baixar certificado</strong></td>
<td>Você pode baixar o certificado SSL usado pelo seu BizTalk Service para um computador local.</td>
</tr>
<tr>
<td><strong>Status</strong></td>
<td>Exibe o status atual de seu BizTalk Service. Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">Serviços BizTalk: gráfico de estado de serviço</a>. </td>
</tr>
<tr>
<td><strong>URL do serviço</strong></td>
<td>A URL de seu serviço BizTalk. Esta é a mesma que a <strong>URL de domínio</strong> inserido quando seu BizTalk Service é criado.</td>
</tr>
<tr>
<td><strong>Endereço IP público Virtual (VIP)</strong></td>
<td>O endereço IP atribuído ao seu BizTalk Service. Ele é usado para todos os pontos de entrada e é o endereço de origem para tráfego de saída. Este endereço IP pertence a sua BizTalk Service desde que ele é criado. Se você excluir o BizTalk Service, o endereço IP é atribuído à outra BizTalk Service.</td>
</tr>
<tr>
<td><strong>Namespace do ACS</strong></td>
<td>Autentica com o serviço BizTalk.</td>
</tr>
<tr>
<td><strong>Edition</strong></td>
<td>Listas a edição inserido quando o BizTalk Service é criado.</td>
</tr>
<tr>
<td><strong>Local</strong></td>
<td>Exibe a região geográfica que hospeda seu BizTalk Service.</td>
</tr>
<tr>
<td><strong>Criado</strong></td>
<td>Exibe a data e hora que do BizTalk Service foi criado.</td>
</tr>
<tr>
<td><strong>Banco de dados de acompanhamento</strong></td>
<td>O nome do banco de dados do Azure SQL que armazena as tabelas de controle usadas pelo seu BizTalk Service. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Requisitos explicadas</a> fornece detalhes sobre o banco de dados de controle.</td>
</tr>
<tr>
<td><strong>Armazenamento de monitoramento/arquivamento</strong></td>
<td>O nome da conta de armazenamento do Azure que armazena a saída monitoramento de seu BizTalk Service.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Requisitos explicadas</a> fornece detalhes sobre a conta de armazenamento.</td>
</tr>
<tr>
<td><strong>Nome da assinatura</strong></td>
<td>Lista a assinatura que hospeda seu BizTalk Service. A assinatura controla o acesso ao portal do Azure clássico.</td>
</tr>
<tr>
<td><strong>ID da assinatura</strong></td>
<td>Quando uma assinatura é criada, uma ID da assinatura é gerado automaticamente. Ao usar APIs REST, você talvez precise inserir a identificação de assinatura.</td>
</tr>
</table>

[Serviços BizTalk: provisionamento portal clássico do Azure usando](http://go.microsoft.com/fwlink/p/?LinkID=302280) lista as etapas para criar um BizTalk Service.


##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Gerenciar informações de Conexão, chaves de sincronização e excluir na barra de tarefas:

<table border="1">

<tr>
<td><strong>Gerenciar</strong> sua implantação do aplicativo</td>
<td>Abre o Portal de serviços do Azure BizTalk. O Portal de serviços do BizTalk é a entrada da configuração de EDI, incluindo adicionando parceiros e criar X12, AS2 e EDIFACT contratos.
<br/><br/>
Esta é a mesma que <strong>Criar acordos de parceiro</strong> na guia de <strong>Início rápido</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurando componentes de EDI mensagens no Portal de serviços do BizTalk</a> fornece mais informações sobre o Portal de serviços do BizTalk.</td>
</tr>
<tr>
<td><strong>Informações de Conexão</strong> do Namespace de controle de acesso</td>
<td>Exibe o Namespace de controle de acesso, o emissor padrão e valores de chave padrão; que podem ser copiados.
<br/><br/>
Você também pode abrir o Portal de controle de acesso. Este Portal de controle de acesso é a mesma que usando a opção Active Directory no painel de navegação à esquerda.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gerenciando o ACS Namespace</a> fornece mais informações sobre o Portal de controle de acesso.</td>
</tr>
<tr>
<td><strong>Sincronizar teclas</strong> na conta de armazenamento</td>
<td>Quando você cria uma conta de armazenamento, uma chave primária e chave secundária são criados automaticamente. Essas chaves de criptografia controlam o acesso à sua conta de armazenamento. Seu BizTalk Service usa automaticamente a chave primária. <strong>Teclas de sincronização</strong> permitem aos usuários alternar entre a chave primária e a chave secundária sem interromper o BizTalk Service.
<br/><br/>
Por exemplo, deseja o BizTalk Service usar uma nova chave primária para a conta de armazenamento. Para fazer isso:
<br/><br/>
<ol>
<li>Selecione seu BizTalk Service e <strong>Teclas de sincronização</strong>. Selecione a chave secundária. Quando você fizer isso, o BizTalk Service inicia usando a chave secundária.</li>
<li>No portal do Azure clássico, selecione sua conta de armazenamento e gerar a chave primária. Lembre-se de que seu BizTalk Service está usando a tecla secundário.</li>
<li>Selecione seu BizTalk Service e <strong>Teclas de sincronização</strong>. Agora, selecione a chave primária. Esta é a nova chave primária é gerado novamente.</li>
<li>No portal do Azure clássico, selecione sua conta de armazenamento e gerar a chave secundária.</li>
</ol>
<br/>
Esse processo é chamado "teclas de sobreposição". O objetivo é permitir que os usuários alternar entre a chave primária e a chave secundária sem interromper o BizTalk Service.</td>
</tr>

<tr>
<td><strong>Excluir</strong> seu aplicativo</td>
<td>Seu BizTalk Service e todos os itens implantados são removidos.</td>
</tr>
</table>


## <a name="monitor"></a>Monitor
Não se aplica à edição gratuito.

Quando você seleciona o seu nome de BizTalk Service, na guia Monitor está disponível e exibe o seguinte:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Gráfico de métrico: Exibe as métricas de desempenho selecionado
Essas métricas fornecem os valores em tempo real sobre a integridade do BizTalk Service. Você escolher quais métricas de desempenho são exibidas. Um máximo de seis métricas de desempenho pode ser exibido simultaneamente. 

Você também pode escolher os valores **relativo** ou **absoluto** e o intervalo de tempo de **intervalo** de métricas são exibidos. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Para remover ou exibir métricas no gráfico:
1. Selecione a guia **Monitor** .
2. Na barra de tarefas, selecione **Adicionar métricas** :  
![Selecione Adicionar métricas][AddMetrics]
3. Verifique as métricas de desempenho que você deseja exibir.
4. Selecione a marca de seleção para retornar para a guia **Monitor** .
5. Selecione o círculo ao lado da métrica para exibir o valor de métrica no gráfico.  

    Por exemplo, a métrica de **Uso da CPU** estiver esmaecida; a saída não é exibida no gráfico:  
![Métrica de uso da CPU está esmaecida][GrayedMetric]  

    Selecione o cinza círculo para habilitar a métrica de **Uso da CPU** exibir sua saída do gráfico:  
![Métrica de uso da CPU está habilitada][EnabledMetric]

6. Para remover uma métrica de gráfico de exibição e a lista, selecione **Excluir métrica** na barra de tarefas. Para adicionar a métrica voltar para a lista, selecione **Adicionar métricas** na barra de tarefas, verifique a métrica e selecione a marca de seleção para retornar para a guia **Monitor** . Selecione o cinza círculo para habilitar a métrica.

## <a name="Metrics"></a>Métricas de disponíveis
As seguintes contadores/métricas de desempenho estão disponíveis:

<table border="1">

<tr>
<td><strong>Latência de RountdTrip</strong></td>
<td>Exibe o tempo médio necessário em milissegundos (ms) para processar uma mensagem da hora em que a mensagem for recebida até que a mensagem é processada totalmente pelo BizTalk Service em todas as pontes. Somente as mensagens processadas com êxito são contadas.<br/><br/>
Quando os seguintes eventos ocorrem, um carimbo de hora é criado:
<ul>
<li>Mensagem insere o gateway</li>
<li>Mensagem é roteada para o destino</li>
<li>Resposta de destino for recebida</li>
<li>Resposta de confirmação de destino enviada para o gateway</li>
</ul>
<br/>
Esta métrica mostra o resultado do seguinte cálculo:
<br/><br/>
[Destino confirmação resposta enviada para o gateway] - [mensagem insere o gateway]</td>
</tr>
<tr>
<td><strong>Falhas na origem</strong></td>
<td>Exibe o número total de mensagens que falhou pelo BizTalk Service quando recebendo mensagens dos pontos de extremidade de origem.</td>
</tr>
<tr>
<td><strong>Uso da CPU</strong></td>
<td>Lista % tempo do processador média de todas as instâncias de função.</td>
</tr>
<tr>
<td><strong>Latência de processamento</strong></td>
<td>Exibe o tempo médio necessário em milissegundos (ms) para processar uma mensagem pelo BizTalk Service em todas as pontes, excluindo o tempo gasto em destinos. Somente as mensagens processadas com êxito são contadas.<br/><br/>
Quando cada um dos seguintes eventos ocorrer, um carimbo de hora é criado:

<ul>
<li>Mensagem insere o gateway</li>
<li>Mensagem é roteada para o destino</li>
<li>Resposta de destino for recebida</li>
<li>Resposta de confirmação de destino enviada para o gateway</li>
</ul>
<br/>Esta métrica mostra o resultado do seguinte cálculo:<br/><br/>
[Destino confirmação resposta enviada para o gateway] - [mensagem insere o gateway] - [resposta de destino for recebida] + [mensagem é roteada para o destino]</td>
</tr>
<tr>
<td><strong>Falhas no processo</strong></td>
<td>Exibe o número total de mensagens que falhou durante o processamento pelo BizTalk Service em todas as pontes dentro de um intervalo de tempo.</td>
</tr>
<tr>
<td><strong>Mensagens enviadas</strong></td>
<td>Exibe o número total de mensagens enviadas pelo BizTalk Service em todas as pontes dentro de um intervalo de tempo. Esta métrica é incrementada quando uma mensagem enviada de um pipeline atinge o destino de rota. Esta métrica não indica que uma mensagem é processada com êxito.<br/><br/>
Em um cenário de solicitação-resposta, a métrica é incrementada quando o destino de rota envia uma confirmação de recebimento voltar para o pipeline.</td>
</tr>
<tr>
<td><strong>Mensagens recebidas</strong></td>
<td>Exibe o número total de mensagens recebidas pelo BizTalk Service em todas as pontes dentro de um intervalo de tempo. Esta métrica é incrementada quando uma nova mensagem for recebida pelo pipeline.</td>
</tr>
<tr>
<td><strong>Mensagens no processo</strong></td>
<td>Exibe o número total de mensagens que estão sendo processadas pelo BizTalk Service dentro de um intervalo de tempo.</td>
</tr>
<tr>
<td><strong>Mensagens processadas</strong></td>
<td>Exibe o número total de mensagens com êxito processadas pelo BizTalk Service em todas as pontes dentro de um intervalo de tempo. Esta métrica é incrementada quando uma mensagem for recebida com êxito pelo pipeline e roteada com êxito para o destino.</td>
</tr>
</table>


## <a name="scale"></a>Escala
Na guia escala, você pode adicionar ou subtrair o número de unidades usadas pelo seu BizTalk Service. Por padrão, há uma unidade configurada. Unidades adicionais podem ser adicionadas dimensionar seu BizTalk Service. Quando você aumenta a escala, você está aumentando a produtividade. A quantidade de recursos também aumenta, incluindo pontes implantados, contratos, conexões de LOB e a capacidade de processamento. Por exemplo, você pode aumentar a escala de unidade de 1 a 2 unidades. Nessa situação, você pode implantar dobrar o número de pontes, duas vezes os contratos, duas vezes as conexões de LOB e duas vezes a capacidade de processamento.

Algumas edições BizTalk não oferecem uma opção de escala. Nessa situação, uma unidade é permitida. Para determinar quantas unidades sua edição pode ser dimensionada, consulte [Serviços BizTalk: gráfico das edições](biztalk-editions-feature-chart.md).

Aumentar o número de unidades pode afetar a preços. Se você aumentar as unidades, selecionar **Salvar** exibe uma mensagem que informa se a cobrança é afetada. Você optar por continuar. Quando você aumenta o número de unidades, as alterações de status de BizTalk Service do Active para atualização. No estado atualizando, seus BizTalk Service continua a executar.

[Serviços BizTalk: gráfico das edições](biztalk-editions-feature-chart.md) define uma unidade de"".


## <a name="configure"></a>Configurar
Não se aplicam às conexões de híbrido.

Define o Status de Backup nenhum ou automática. Quando definido como nenhum, nenhum backups são criados automaticamente. Quando definido como automático, você configurar o local de backup, a frequência do backup e por quanto tempo manter os arquivos de backup. 

[Serviços BizTalk: Backup e restauração](biztalk-backup-restore.md) fornece os detalhes. 


## <a name="HybridConnections"></a>Conexões de híbrido
Híbrido conexões se conectar a um aplicativo do Azure, como aplicativos Web ou Mobile no serviço de aplicativo do Azure, para um recurso de local que usa uma porta TCP estática, como o SQL Server, MySQL, APIs de Web HTTP e a maioria dos serviços da Web personalizado. Conexões de híbrido são gerenciados nos serviços do BizTalk no portal de clássico do Azure.

Para criar conexões de híbrido no serviço de aplicativo do Azure, consulte [acessar recursos usando conexões híbrido no serviço de aplicativo do Azure local](../app-service-web/web-sites-hybrid-connection-get-started.md).

Para criar ou gerenciar conexões de híbrido nos serviços do Azure BizTalk, consulte [Híbrida conexões](integration-hybrid-connection-overview.md).



## <a name="next"></a>Próximo
Agora que você estiver familiarizado com as diferentes guias, você pode aprender mais sobre os recursos de serviços do Azure BizTalk:

- [Serviços do BizTalk: otimização](biztalk-throttling-thresholds.md)  
- [BizTalk serviços: Nome do emissor e chave emissor](biztalk-issuer-name-issuer-key.md)  
- [BizTalk serviços: Backup e restauração](biztalk-backup-restore.md)

## <a name="see-also"></a>Consulte também
- [Conexões de híbrido](integration-hybrid-connection-overview.md)  
- [BizTalk serviços: Desenvolvedor, Basic, Standard e Premium edições gráfico](biztalk-editions-feature-chart.md)  
- [Serviços do BizTalk: Portal de clássico do Azure usando de provisionamento](biztalk-provision-services.md)  
- [BizTalk serviços: Gráfico de estado de serviço BizTalk](biztalk-service-state-chart.md)  
- [Como faço para começar a usar o SDK dos serviços do BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
 
