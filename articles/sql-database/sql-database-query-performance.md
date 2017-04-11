<properties 
   pageTitle="Visão de desempenho de consulta de banco de dados SQL Azure" 
   description="Monitoramento do desempenho de consulta identifica a maioria das consultas de consumo de CPU para um banco de dados do SQL Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="08/09/2016"
   ms.author="sstein"/>

# <a name="azure-sql-database-query-performance-insight"></a>Visão de desempenho de consulta de banco de dados SQL Azure


Gerenciamento e ajuste o desempenho de bancos de dados relacionais são uma tarefa desafiador que requer conhecimento significativo e investimento de tempo. Compreensão de desempenho de consulta permite gastam menos tempo de solução de problemas de desempenho de banco de dados, fornecendo o seguinte:

- Mais profunda percepção seu consumo de recursos (DTU) de bancos de dados. 
- As principais consultas por contagem de duração/CPU/execução, que potencialmente pode ser ajustado para melhorar o desempenho.
- A capacidade de analisar os detalhes de uma consulta, exibir seu texto e o histórico de utilização de recursos. 
- As anotações que mostram as ações executadas pelo [Supervisor de banco de dados do SQL Azure](sql-database-advisor.md) de ajuste de desempenho  



## <a name="prerequisites"></a>Pré-requisitos

- Compreensão de desempenho de consulta só está disponível com V12 de banco de dados do SQL Azure.
- Compreensão de desempenho de consulta requer que o [Repositório de consulta](https://msdn.microsoft.com/library/dn817826.aspx) está ativa em seu banco de dados. Se não estiver executando o repositório de consulta, o portal solicitará que você ativá-lo.

 
## <a name="permissions"></a>Permissões

As permissões de [controle de acesso baseado em função](../active-directory/role-based-access-control-configure.md) a seguir são necessárias para usar uma visão de desempenho de consulta: 

- São necessárias permissões de **leitor**, **proprietário**, **Colaborador**, **SQL DB Colaborador**ou **Colaborador do SQL Server** para exibir as consultas de consumo de recurso superior e gráficos. 
- São necessárias permissões de **proprietário**, **Colaborador**, **SQL DB Colaborador**ou **Colaborador do SQL Server** para exibir texto da consulta.



## <a name="using-query-performance-insight"></a>Usando uma visão de desempenho de consulta

Compreensão de desempenho de consulta é fácil de usar:

- Abra o [portal do Azure](https://portal.azure.com/) e localize o banco de dados que você deseja examinar. 
  - No menu do lado esquerdo, em suporte e solução de problemas, selecione "Visão de desempenho de consulta".
- Na primeira guia, examine a lista de principais consultas ocupa muitos recursos.
- Selecione uma consulta individual para exibir seus detalhes.
- Abra o [Supervisor de banco de dados do SQL Azure](sql-database-advisor.md) e verifique se todas as recomendações estão disponíveis.
- Use os controles deslizantes ou ícones para alterar o intervalo de observado de zoom.

    ![Painel de desempenho](./media/sql-database-query-performance/performance.png)

> [AZURE.NOTE] Algumas horas de dados precisa ser capturado pela loja de consulta de banco de dados SQL fornecer a obtenção de informações de desempenho de consulta. Se o banco de dados não tem nenhuma atividade ou repositório de consulta não estava ativo durante um determinado período de tempo, os gráficos estará vazios quando exibindo o período de tempo. Você pode habilitar o armazenamento de consulta a qualquer momento se ele não está sendo executado.   



## <a name="review-top-cpu-consuming-queries"></a>Revisar superior CPU consumir consultas

No [portal](http://portal.azure.com) , faça o seguinte:

1. Navegue até um banco de dados do SQL e clique em **todas as configurações** > **suporte + Solucionando problemas** > **ideias de desempenho de consulta**. 

    ![Compreensão de desempenho de consulta][1]

    Abre o modo de exibição de principais consultas e as principais consultas de consumo de CPU são listadas.

1. Clique ao redor do gráfico para obter detalhes.<br>A linha superior mostra geral % DTU para o banco de dados, enquanto as barras mostram % de CPU consumida pelas consultas selecionadas durante o intervalo selecionado (por exemplo, se a **semana passada** está selecionado cada barra representa um dia).

    ![principais consultas][2]

    A grade inferior representa informações agregadas para as consultas visíveis.

  - ID da consulta – identificador exclusivo da consulta no banco de dados.
  - CPU por consulta durante o intervalo de observáveis (depende até que a função de agregação).
  - Duração por consulta (depende até que a função de agregação).
  - Número total de execuções para uma consulta específica.

    Marque ou desmarque consultas individuais para incluir ou exclui-los do gráfico usando caixas de seleção.

1. Se seus dados se torna obsoletos, clique no botão **Atualizar** .
1. Você pode usar os controles deslizantes e botões para alterar o intervalo de Observação investigar picos de zoom:  ![configurações](./media/sql-database-query-performance/zoom.png)
1. Opcionalmente, se você quiser uma exibição diferente, você pode selecionar a guia **personalizada** e definir:
  
  - Métrica (CPU, duração, contagem de execução)
  - Intervalo de tempo (últimas 24 horas, semana, mês passado anterior). 
  - Número de consultas.
  - Função de agregação.

    ![Configurações](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Exibindo detalhes de consulta individual

Para exibir detalhes de consulta:

1. Clique em qualquer consulta na lista de consultas mais importantes.

    ![detalhes](./media/sql-database-query-performance/details.png)

1. O modo de exibição de detalhes é aberto e a contagem de duração/consumo/execução de CPU de consultas é dividida ao longo do tempo.
1. Clique ao redor do gráfico para obter detalhes.
  - Gráfico superior mostra linha com geral % DTU de banco de dados e as barras são % de CPU consumida pela consulta selecionada.
  - Segundo gráfico mostra duração total da consulta selecionada.
  - Gráfico de inferior mostra o número total de execuções pela consulta selecionada.
    
    ![detalhes da consulta][3]

1. Opcionalmente, use os controles deslizantes, botões de zoom ou clique em **configurações** para personalizar como os dados da consulta são exibidos ou escolha um período diferente.

## <a name="review-top-queries-per-duration"></a>Consultas mais importantes de revisão por duração

Na atualização recente de visão de desempenho de consulta, apresentamos duas novas métricas que podem ajudá-lo a identificar gargalos potenciais: contagem de duração e execução.<br>

Consultas de execução demorada têm o potencial maior de recursos mais longo de bloqueio, bloqueando a outros usuários e limitação escalabilidade. Eles também são os melhores candidatos para otimização.<br>

Para identificar consultas de execução demorada:

1. Abrir o guia de **personalizada** na visão de desempenho de consulta de banco de dados selecionado
1. Métricas de alteração seja **duração**
1. Selecione o número de consultas e intervalo de Observação
1. Selecione a função de agregação
  - **Soma** somará todos tempo de execução da consulta durante o intervalo de Observação inteiro.
  - **Max** localiza consultas que tempo de execução foi máximo no intervalo de Observação inteiro.
  - **Avg** localiza médio de tempo de execução de todas as execuções de consulta e mostrar a parte superior fora essas médias. 

    ![duração da consulta][4]

## <a name="review-top-queries-per-execution-count"></a>Consultas mais importantes de revisão por contagem de execução

Alto número de execuções pode não estar afetando próprio banco de dados e uso de recursos pode ser baixo, mas geral do aplicativo pode receber lenta.

Em alguns casos, a contagem de execução muito alto pode levar a aumentar da rede idas e vindas. Idas afetar significativamente o desempenho. Eles estão sujeitos a latência de rede e a latência do servidor downstream. 

Por exemplo, muitos sites orientados a dados muito acessar o banco de dados para cada solicitação de usuário. Enquanto o pool de Ajuda, o tráfego de rede maior e carga do servidor de banco de dados de processamento de conexão pode afetar o desempenho.  Conselhos geral é manter idas e vindas ao mínimo absoluto.

Para identificar frequentemente executadas consultas consultas ("informais"):

1. Abrir o guia de **personalizada** na visão de desempenho de consulta de banco de dados selecionado
1. Alterar métricas sejam **contagem de execução**
1. Selecione o número de consultas e intervalo de Observação

    ![Contagem de execução da consulta][5]

## <a name="understanding-performance-tuning-annotations"></a>Noções básicas sobre anotações de ajuste de desempenho 

Enquanto Explorando sua carga de trabalho em uma visão de desempenho de consulta, você poderá ter ícones com linha vertical na parte superior do gráfico.<br>

Esses ícones são anotações; eles representam desempenho afetando ações executadas pelo [Supervisor de banco de dados do SQL Azure](sql-database-advisor.md). Por anotação de focalização, você obtenha informações básicas sobre a ação:

![anotação de consulta][6]

Se você quiser saber mais ou aplicar recomendação de Supervisor, clique no ícone. Ele será aberto detalhes da ação. Se for uma recomendação ativa que pode aplicá-lo usando o comando imediatamente.

![detalhes de anotação de consulta][7]

### <a name="multiple-annotations"></a>Várias anotações. ###

É possível, que devido ao nível de zoom, as anotações que estejam próximos uns dos outros serão obter recolhidas em uma. Isso será representado por um ícone especial, clicando nela serão aberta blade novo onde a lista de agrupados anotações será mostrado.
Correlação consultas e ações de ajuste de desempenho pode ajudar a entender melhor sua carga de trabalho. 


##  <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Otimizar a configuração de repositório de consulta para uma visão de desempenho de consulta

Durante o uso de visão de desempenho de consulta, você poderá encontrar as seguintes mensagens de repositório de consulta:

- "Armazenamento de consulta não está corretamente configurado neste banco de dados. Clique aqui para saber mais."
- "Armazenamento de consulta não está corretamente configurado neste banco de dados. Clique aqui para alterar as configurações." 

Normalmente, essas mensagens aparecem quando o armazenamento de consulta não é capaz de coletar dados. 

Primeiro caso acontece quando o armazenamento de consulta está em estado somente leitura e parâmetros definidos ideal. Você pode corrigir isso aumento do tamanho do armazenamento de consulta ou desmarcando repositório de consulta.

![botão de qds][8]

Segundo caso acontece quando o armazenamento de consulta está desativado ou parâmetros não estão configurados corretamente. <br>Você pode alterar a política de retenção e captura e habilitar o armazenamento de consulta, executando comandos abaixo ou diretamente do portal:

![botão de qds][9]

### <a name="recommended-retention-and-capture-policy"></a>Política de retenção e captura recomendada

Há dois tipos de políticas de retenção:

- Tamanho com base – se definida como AUTOlimpará dados automaticamente quando o próximo tamanho máximo é alcançado.
- Com base no tempo - por padrão que será a definimos como 30 dias, significando que, se repositório de consulta será executado sem espaço, ele excluirá consultar informações mais de 30 dias

Capturar política poderia ser definida como:

- **Todas** – captura todas as consultas.
- **Automático** – raros consultas e consultas com duração de compilação e execução não significativos são ignoradas. Limites de duração de contagem, compilação e tempo de execução de execução internamente são determinados. Esta é a opção padrão.
- **Nenhum** – repositório de consulta interrompe a captura novas consultas, porém estatísticas de tempo de execução para consultas já capturadas são ainda coletadas.
    
Recomendamos definindo todas as diretivas para automático e política de tirar 30 dias:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
        
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Aumente o tamanho do armazenamento de consulta. Isso pode ser realizado se conectando a um banco de dados e emissão seguindo consulta:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Aplicar essas configurações eventualmente tornará o repositório de consulta coletando novas consultas, no entanto, se você não quiser aguardar limpe repositório de consulta. 
> [AZURE.NOTE] Execução da consulta seguinte excluirá todas as informações atuais na loja de consulta. 


    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Resumo

Compreensão de desempenho de consulta ajuda você a entender o impacto de sua carga de trabalho de consulta e como ele se relaciona a consumo de recursos de banco de dados. Com esse recurso, você irá Saiba mais sobre a parte superior consumir consultas e identificar facilmente aquelas corrigir antes que se tornem um problema.




## <a name="next-steps"></a>Próximas etapas

Para obter recomendações adicionais sobre como melhorar o desempenho do seu banco de dados do SQL, clique em [recomendações](sql-database-advisor.md) na lâmina **Ideias de desempenho de consulta** .

![Supervisor de desempenho](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

