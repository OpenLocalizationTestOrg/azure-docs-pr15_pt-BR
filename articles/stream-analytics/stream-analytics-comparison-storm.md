<properties
    pageTitle="Plataformas de análise: comparação de tempestade Apache para a análise de fluxo | Microsoft Azure"
    description="Obtenha orientação escolhendo uma plataforma de análise de nuvem usando uma comparação de tempestade Apache para a análise de fluxo. Compreenda as diferenças e recursos."
    keywords="plataforma de análise, plataformas de análise, plataforma de análise de nuvem, comparação de tempestade"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="help-choosing-a-streaming-analytics-platform-apache-storm-comparison-to-azure-stream-analytics"></a>Ajudar a escolher uma plataforma de análise streaming: comparação de tempestade Apache para análise de fluxo do Azure

Obtenha orientação escolhendo uma plataforma de análise de nuvem usando essa comparação de tempestade Apache para análise de fluxo de Azure. Compreenda as propostas de valor da análise de fluxo versus tempestade Apache como um serviço gerenciado no Azure HDInsight, para que possa escolher a solução certa para o seu negócio casos de uso.

Ambos os analytics plataformas fornecem benefícios de uma solução de PaaS, existem alguns recursos de diferenciar principais que diferenciação-los. Recursos, bem como as limitações desses serviços estão listadas abaixo para ajudar você será levado a solução que você precisa atingir suas metas.

## <a name="storm-comparison-to-stream-analytics-general-features"></a>Comparação com a análise de fluxo de tempestade: recursos gerais ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análise de fluxo Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache tempestade em HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Abrir origem</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Não, a análise de fluxo Azure é um proprietário de Microsoft oferta.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sim, tempestade Apache é uma tecnologia de Apache licenciado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Microsoft com suporte</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sim </p>
            </td>
            <td width="246" valign="top">
                <p>
Sim </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Requisitos de hardware</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Não há nenhum requisitos de hardware. A análise de fluxo Azure é um serviço do Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Não há nenhum requisitos de hardware. Tempestade Apache é um serviço do Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Unidade de nível superior</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Com a análise de fluxo Azure clientes implantar e monitoram trabalhos streaming.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Com Apache tempestade em clientes HDInsight implantar e monitorar um cluster inteiro, que pode hospedar vários trabalhos de tempestade bem como outras cargas de trabalho (lote incluindo).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Preço</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Análise de fluxo é o preço por volume de dados processados e o número de unidades de streaming (por hora que o trabalho está em execução) necessários.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/stream-analytics/">Obter mais informações sobre preços podem ser encontradas aqui.</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Para Apache tempestade em HDInsight, a unidade de compra é baseado em cluster e é cobrada com base no período de tempo que o cluster está em execução, independentemente de trabalhos implantados.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/hdinsight/">Obter mais informações sobre preços podem ser encontradas aqui.</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Criação em cada plataforma de análise##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análise de fluxo Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache tempestade em HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Recursos: SQL DSL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sim, um suporte de linguagem SQL fáceis de usar está disponível.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Não, os usuários devem escrever código no Java c# ou usar Trident APIs.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Recursos: Operadores Temporal</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Agregações em janelas e junções Temporais são suportados prontos para uso.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Operadores temporais devem ser implementado pelo usuário.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Experiência de desenvolvimento</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Interativo de criação e depuração experiência por meio do Portal do Azure em dados de exemplo.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Desenvolvimento, depuração e monitoração experiência são fornecido por meio do Visual Studio experiência dos usuários do .NET, enquanto para Java e outros desenvolvedores de idiomas pode usar o IDE de sua escolha.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Suporte a depuração</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Análise de fluxo oferece o status de trabalho básico e logs de operações como uma forma de depuração, mas atualmente não oferece flexibilidade no que acontece/como muito está incluído nos logs do ie: modo detalhado.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Logs detalhados estão disponíveis para fins de depuração. Há duas maneiras de superfície logs de usuário, por meio do visual Studio ou usuário pode RDP em cluster para acessar logs.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Suporte para UDF (funções definidas pelo usuário)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Atualmente não há nenhum suporte para UDFs.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
UDFs podem ser escritos em c#, Java ou o idioma de sua escolha.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Extensível - código personalizado</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Não há nenhum suporte para código extensível em análise de fluxo.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sim, há disponibilidade para escrever código personalizado nos c#, Java ou outros idiomas aceitos tempestade.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Fontes de dados e saídas##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análise de fluxo Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache tempestade em HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Fontes de dados de entrada</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>As fontes de entrada com suporte são Hubs de evento do Azure e Blobs do Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Há conectores disponíveis para Hubs de evento, barramento de serviço, Kafka, etc. Conectores sem suporte podem ser implementadas por meio de código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formatos de dados de entrada</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Formatos de entrada com suporte são Avro, JSON, CSV.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Qualquer formato pode ser implementado por meio de código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Saídas</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Um fluxo de trabalho pode ter várias saídas. Saídas compatíveis: Hubs de evento Azure, armazenamento de blob do Microsoft Azure, tabelas do Azure, DB do SQL Azure e PowerBI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Suporte para muitos saídas em uma topologia, cada saída pode ter lógica personalizada para processamento downstream. Prontos para tempestade inclui conectores para PowerBI, Hubs de evento do Azure, armazenamento de Blob do Azure, DocumentDB do Azure, SQL e HBase. Conectores sem suporte podem ser implementadas por meio de código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formatos de codificação de dados</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Análise de fluxo requer o formato de dados UTF-8 a ser utilizado.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Qualquer formato de codificação de dados pode ser implementado por meio de código personalizado.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Gerenciamento e operações##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análise de fluxo Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache tempestade em HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Modelo de implantação de trabalho</strong>
                </p>
                <p>
                    - <strong>Portal do Azure</strong>
                </p>
                <p>
                    - <strong>O Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Implantação é implementada por meio do Portal do Azure, PowerShell e APIs REST.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Depolyment é implementado por meio do Portal do Azure, PowerShell, Visual Studio e APIs REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Monitoramento (estatísticas, contadores, etc.)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Monitoramento é implementado por meio do Portal do Azure e APIs REST.
                </p>
                <p>
O usuário também pode configurar alertas Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Monitoramento é implementado por meio de tempestade UI e APIs REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Escalabilidade</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Número de unidades de Streaming para cada tarefa. Cada unidade de Streaming processa até 1 MB/s. Máximo de 50 unidades por padrão. Chamada para aumentar o limite.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Número de nós no cluster HDI tempestade. Sem limite no número de nós (limite superior definido pela sua cota Azure). Chamada para aumentar o limite.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Limites de processamento de dados</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Os usuários podem expandir ou reduzir o número de unidades Streaming para aumentar o processamento de dados ou otimizar custos.
                </p>
                <p>
Dimensionar até 1 GB/s </p>
            </td>
            <td width="246" valign="top">
                <p>
Usuário pode expandir ou reduzir o tamanho de cluster para atender às necessidades.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Parar/currículo</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Interromper e retomar a partir do último local interrompido.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Interromper e retomar a partir do último colocar parou com base na marca d'água.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Atualização de serviço e estrutura</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Correção automática sem tempo de inatividade.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Correção automática sem tempo de inatividade.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Continuidade de negócios por meio de um serviço altamente disponível com do SLA garantia</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
SLA de atividade de 99,9% </p>
                <p>
Recuperação automática de falhas </p>
                <p>
Recuperação de estado operadores temporais é interna.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
SLA de atividade de 99,9% do cluster tempestade. Tempestade Apache é uma plataforma de fluxo de tolerância a falhas, mas é responsabilidade dos clientes para garantir que seus trabalhos streaming executados sem interrupções.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Recursos avançados##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análise de fluxo Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache tempestade em HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Chegada de atrasadas e manipulação de eventos fora de ordem</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Diretivas configuráveis internas para reordenar, solte eventos ou ajustar a hora do evento.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Usuário deve implementar a lógica para manipular esse cenário.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Dados de referência</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Dados de referência disponíveis de Blobs do Azure com tamanho máximo de 100 MB de cache de pesquisa na memória. Atualização de dados de referência é gerenciado pelo serviço.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Não há limites de tamanho de dados. Conectores disponíveis para HBase, DocumentDB, SQL Server e do Azure. Conectores sem suporte podem ser implementadas por meio de código personalizado.
                </p>
                <p>
Atualização de dados de referência deve ser tratado pelo código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Integração com o aprendizado de máquina</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Configurando publicado modelos de aprendizado de máquina do Azure como funções durante a criação de trabalho ASA <a href="http://blogs.msdn.com/b/streamanalytics/archive/2015/05/24/real-time-scoring-of-streaming-data-using-machine-learning-models.aspx">(prévia particular)</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Disponível por meio de tempestade parafusos.
                </p>
            </td>
        </tr>
    </tbody>
</table>
