<properties 
   pageTitle="Logs de personalizada na análise de Log | Microsoft Azure"
   description="Análise de log pode coletar eventos de arquivos de texto em computadores Windows e Linux.  Este artigo descreve como definir um novo log personalizado e detalhes dos registros criam no repositório OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="custom-logs-in-log-analytics"></a>Logs de personalizados em análise de Log

Fonte de dados Custom Logs na análise de Log permite que você colete eventos de arquivos de texto em computadores Windows e Linux. Muitos aplicativos registrem informações para arquivos de texto em vez de serviços de log padrão como log de eventos do Windows ou Syslog.  Depois de coletados, você pode analisar cada registro no log de em campos individuais usando o recurso de [Campos personalizados](log-analytics-custom-fields.md) de análise de Log.

![Conjunto de log personalizado](media/log-analytics-data-sources-custom-logs/overview.png)

Os arquivos de log a serem coletados devem corresponder aos seguintes critérios.

- O log deve ou ter uma única entrada por linha ou usar um carimbo de hora a correspondência de um dos seguintes formatos no início de cada entrada.

    AAAA-MM-DD HH <br>
  M/AAAA HH: MM: SS AM/PM <br>
  Seg DD, YYYY hh
    
- O arquivo de log não deve permitir atualizações circulares onde o arquivo será substituído com novas entradas. 

## <a name="defining-a-custom-log"></a>Definindo um log personalizado

Use o procedimento a seguir para definir um arquivo de log personalizado.  Role até o final deste artigo para instruções passo a passo de um exemplo de como adicionar um log personalizado.

### <a name="step-1-open-the-custom-log-wizard"></a>Etapa 1. Abrir o Assistente de Log personalizado

O Assistente de Log personalizado é executado no portal do OMS e permite que você defina um novo log personalizado para coletar.

1.  No portal do OMS, vá para **configurações**.
2.  Clique em **dados** e, em seguida, **Custom logs**.
3.  Por padrão, todas as alterações de configuração são enviadas automaticamente para todos os agentes.  Para agentes Linux, um arquivo de configuração é enviado para o coletor de dados de Fluentd.  Se você deseja modificar esse arquivo manualmente em cada agente Linux, desmarque a caixa *Aplicar abaixo de configuração para minha máquinas Linux*.
4.  Clique em **Add +** para abrir o Assistente de registro personalizado.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Etapa 2. Carregar e analisar um log de amostra

Inicie carregando uma amostra do log personalizado.  O assistente irá analisar e exibir as entradas neste arquivo validar.  Análise de log usará o delimitador que você especificar para identificar cada registro.

**Nova linha** é o delimitador padrão e é usado para arquivos de log que têm uma única entrada por linha.  Se a linha começa com uma data e hora em um dos formatos disponíveis, você pode especificar um delimitador de **carimbo de hora** que suporta entradas que se estendem por mais de uma linha. 

Se for usado um delimitador de carimbo de hora, em seguida, a propriedade TimeGenerated de cada registro armazenado no OMS será preenchida com a data/hora especificada para essa entrada no arquivo de log.  Se um novo delimitador de linha for usado, TimeGenerated é preenchida com data e hora que a análise de Log coletados a entrada. 

>[AZURE.NOTE]Análise de log atualmente trata a data/hora coletada de um log usando um delimitador de carimbo de hora como UTC.  Isso assim será alterado para usar o fuso horário no agente. 
 
1.  Clique em **Procurar** e navegue até um arquivo de exemplo.  Observe que isso pode botão pode estar identificado **Escolher arquivo** em alguns navegadores.
2.  Clique em **Avançar**. 
3.  O Assistente de Log personalizado carregue o arquivo e listar os registros que ele identifica.
4.  Altere o delimitador usado para identificar um novo registro e selecione o delimitador que melhor identifica os registros no seu arquivo de log.
5.  Clique em **Avançar**.

### <a name="step-3-add-log-collection-paths"></a>Etapa 3. Adicionar caminhos de conjunto de log

Você deve definir um ou mais caminhos no agente de onde ele pode localizar o log personalizado.  Você pode fornecer ou um caminho específico e um nome para o arquivo de log, ou você pode especificar um caminho com um caractere curinga para o nome.  Isso é compatível com aplicativos que cria um novo arquivo de cada dia ou quando um arquivo atinge um determinado tamanho.  Você também pode fornecer vários caminhos para um único arquivo de log.

Por exemplo, um aplicativo pode criar um arquivo de log cada dia com a data incluída no nome como log20100316.txt. Um padrão para tal um log pode ser *log\*. txt* que seria aplicado a qualquer arquivo de log seguindo o aplicativo do esquema de nomenclatura.

A tabela a seguir fornece exemplos de padrões válidos para especificar os arquivos de log diferente. 

| Descrição | Caminho |
|:--|:--|
| Todos os arquivos em *C:\Logs* com extensão. txt no agente do Windows | C:\Logs\\\*. txt |
| Todos os arquivos em *C:\Logs* com um nome começando com uma extensão. txt no agente do Windows e de log | C:\Logs\log\*. txt |
| Todos os arquivos em */var/log/audit* com extensão. txt no Linux agente | /var/log/Audit/*.txt |
| Todos os arquivos em */var/log/audit* com um nome começando com uma extensão. txt no Linux agente e de log | /var/log/Audit/log\*. txt |
  

1.  Selecione Windows ou Linux para especificar qual formato de caminho que você está adicionando.
2.  Digite o caminho e clique o **+** botão.
3.  Repita o processo para qualquer caminhos adicionais.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Etapa 4. Forneça um nome e uma descrição para o log

O nome que você especifica será usado para o tipo de log conforme descrito acima.  Ele sempre terminará com _CL para distingui-lo como um log personalizado.

1.  Digite um nome para o log.  O ** \_CI** sufixo é fornecido automaticamente.
2.  Adicione uma **Descrição**de opcional.
3.  Clique em **Avançar** para salvar a definição de log personalizado.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Etapa 5. Validar que estão sendo coletados os logs personalizados
Ele pode levar uma hora para os dados iniciais de um novo log personalizado apareçam na análise de Log.  Começarão a coleta de entradas dos logs encontrados no caminho especificado do ponto de que você definiu o log personalizado.  Ele não manterá as entradas que você carregou durante a criação de log personalizado, mas ele coletará entradas já existentes em arquivos de log que ele localiza.

Depois que a análise de Log inicia a coleta do log personalizado, seus registros estarão disponíveis com uma pesquisa de Log.  Use o nome que você deu o log personalizado como o **tipo** de sua consulta.

>[AZURE.NOTE] Se a propriedade Relats está ausente da pesquisa, você precisará fechar e reabrir o navegador.

### <a name="step-6-parse-the-custom-log-entries"></a>Etapa 6. Analisar as entradas de log personalizado

A entrada de log inteiro será armazenada em uma única propriedade chamada **Relats**.  Você provavelmente desejará separar os diferentes tipos de informações em cada entrada em propriedades individuais armazenados no registro.  Você fazer isso usando o recurso de [Campos personalizados](log-analytics-custom-fields.md) de análise de Log.

As etapas detalhadas para analisar a entrada de log personalizado não são fornecidas aqui.  Consulte a documentação de [Campos personalizados](log-analytics-custom-fields.md) para essas informações.

## <a name="disabling-a-custom-log"></a>Desabilitar um log personalizado

Você não pode remover uma definição de log personalizado quando ele é criado, mas você pode desabilitá-lo, removendo todos seus caminhos de conjunto.

1.  No portal do OMS, vá para **configurações**.
2.  Clique em **dados** e, em seguida, **Custom logs**.
3.  Ao lado da definição de log personalizado para desabilitar, clique em **detalhes** .
4.  Remova cada um dos caminhos de conjunto para a definição de log personalizado.


## <a name="data-collection"></a>Coleta de dados

Análise de log coletará novas entradas de cada log personalizado aproximadamente a cada 5 minutos.  O agente gravará seu lugar em cada arquivo de log que ela coleta de.  Se o agente de ficar offline por um período de tempo, em seguida, a análise de Log coletará entradas de onde parou, mesmo se essas entradas foram criadas enquanto o agente estava offline.

Todo o conteúdo da entrada de log é gravado uma única propriedade chamada **Relats**.  Você pode analisar isso em várias propriedades que podem ser analisadas e pesquisadas separadamente definindo [Campos personalizados](log-analytics-custom-fields.md) após ter criado o log personalizado.


## <a name="custom-log-record-properties"></a>Propriedades do registro de log personalizado

Registros de log personalizado têm um tipo com o nome de log que você fornecer e as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| TimeGenerated | Data e hora em que o registro foi coletado pela análise de Log.  Se o log usa um delimitador de hora com base este é o horário coletados da entrada. |
| SourceSystem  | Tipo de registro do agente foram coletado do. <br> Conectar OpsManager – agente do Windows, direta ou SCOM <br> Linux – todos os agentes de Linux  |
| Relats             | Texto completo da entrada coletado. |
| ManagementGroupName | Nome do grupo de gerenciamento de agentes SCOM.  Para outros agentes, isso é AOI -\<identificação de espaço de trabalho\> |


## <a name="log-searches-with-custom-log-records"></a>Pesquisas de log com registros de log personalizado

Registros de logs personalizados são armazenados no repositório OMS como registros de qualquer outra fonte de dados.  Eles terão um tipo correspondente ao nome que você fornecer quando você define o log, para que você pode usar a propriedade Type na sua pesquisa para recuperar registros coletados a partir de um log específico.

A tabela a seguir fornece exemplos diferentes de pesquisas de log que recuperam registros de logs personalizados.

| Consulta | Descrição |
|:--|:--|
| Tipo = MyApp_CL | Todos os eventos de um personalizado log nomeado MyApp_CL. |
| Tipo = MyApp_CL Severity_CF = erro | Todos os eventos de um log personalizado denominado MyApp_CL com um valor de *erro* em um campo personalizado chamado *Severity_CF*. |




## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Explicação passo a passo de amostra da adição de um log personalizado

A seção a seguir descreve um exemplo de criar um log personalizado.  O log de amostra sendo coletado tem uma única entrada em cada linha começando com uma data e hora e, em seguida, vírgula delimitada por campos para código, status e mensagem.  Várias entradas de amostra são mostradas abaixo.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Carregar e analisar um log de amostra

Estamos fornecem um dos arquivos de log e pode ver os eventos que ele vamos coletar.  Nesse caso a nova linha é um delimitador suficiente.  Se uma única entrada no log de poderia abranger várias linhas, então um delimitador de carimbo de hora precisaria ser usado.

![Carregar e analisar um log de amostra](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Adicionar caminhos de conjunto de log

Os arquivos de log estará localizados em *C:\MyApp\Logs*.  Um novo arquivo será criado cada dia com um nome que inclui a data em que o padrão *appYYYYMMDD.log*.  Um padrão suficiente para esse log seria *C:\MyApp\Logs\\\*. log*.

![Caminho de conjunto de log](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Forneça um nome e uma descrição para o log

Podemos usar um nome de *MyApp_CL* e digite uma **Descrição**.

![Nome de log](media/log-analytics-data-sources-custom-logs/log-name.png)


### <a name="validate-that-the-custom-logs-are-being-collected"></a>Validar que estão sendo coletados os logs personalizados

Podemos usar uma consulta de *tipo = MyApp_CL* retornar todos os registros do log de coletados.

![Consulta de log sem campos personalizados](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Analisar as entradas de log personalizado

Usamos campos personalizados para definir a *EventTime*, *código*, *Status*e campos de *mensagem* e podemos pode ver a diferença entre os registros que são retornados pela consulta.

![Consulta de log com campos personalizados](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>Próximas etapas

- Use [Campos personalizados](log-analytics-custom-fields.md) para analisar as entradas no log de personalizados em campos individuais.
- Saiba mais sobre [as pesquisas de log](log-analytics-log-searches.md) analisar os dados coletados de fontes de dados e soluções. 