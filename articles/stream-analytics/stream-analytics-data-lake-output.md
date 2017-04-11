<properties
    pageTitle="Armazenamento de Lucerne de dados de análise de fluxo de saída | Microsoft Azure"
    description="Configuração de autenticação e autorização um Azure Lucerne de repositório de dados em um trabalho de análise de fluxo"
    keywords=""
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="stream-analytics-data-lake-store-output"></a>Saída do fluxo Analytics dados Lucerne Store

Trabalhos de análise de fluxo oferecem suporte a vários métodos de saída, um sendo um [Repositório de Lucerne de dados do Azure](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lucerne Store é um repositório de hyper escala de toda a empresa para analítico cargas de trabalho de grande volume. Armazenamento de dados de Lucerne permite armazenar dados de qualquer tamanho, tipo e inclusão a velocidade para análise operacional e exploratório.

## <a name="authorize-a-data-lake-store-account"></a>Autorize uma conta de armazenamento de Lucerne de dados

1.  Quando o armazenamento de dados de Lucerne for selecionado como uma saída no portal de gerenciamento do Azure, você será solicitado para autorizar o uso de seu armazenamento de Lucerne dados existentes ou para solicitar acesso a visualização de repositório de dados Lucerne através do Portal de clássico do Azure.

    ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  

2.  Se você já tem acesso ao armazenamento de Lucerne de dados, clique em "Autorizar agora" e por um breve tempo uma página surgirá indicando "Redirecionamento autorização...". A página será fechada automaticamente e você verá a página que permitirá que você configurar a saída de dados Lucerne Store.

Se você não se inscreveu para dados Lucerne Store Preview, você pode visitar o link "Inscreva-se agora" para iniciar a solicitação ou siga [obter instruções iniciadas](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="configure-the-data-lake-store-output-properties"></a>Configurar as propriedades de saída de dados Lucerne Store

Uma vez que a conta de armazenamento de Lucerne dados autenticada, você pode configurar as propriedades para a saída de dados Lucerne Store. A tabela a seguir é a lista de nomes de propriedade e sua descrição para configurar a saída de dados Lucerne Store.

<table>
<tbody>
<tr>
<td><B>NOME DA PROPRIEDADE</B></td>
<td><B>DESCRIÇÃO</B></td>
</tr>
<tr>
<td>Alias de saída</td>
<td>Este é um nome amigável usado em consultas para direcionar a saída da consulta para esse armazenamento de Lucerne de dados.</td>
</tr>
<tr>
<td>Conta de armazenamento de Lucerne de dados</td>
<td>O nome da conta de armazenamento onde você está enviando a saída. Você será apresentada com uma lista de contas de armazenamento de Lucerne de dados à qual o usuário conectado ao portal tem acesso ao suspensa.</td>
</tr>
<tr>
<td>Padrão de prefixo do caminho [<I>opcional</I>]</td>
<td>O caminho de arquivo usado para gravar seus arquivos na conta do repositório de Lucerne dados especificado. <BR>{date}, {time}<BR>Exemplo 1: Pasta1/logs / {date} / {tempo}<BR>Exemplo 2: Pasta1/logs / {date}</td>
</tr>
<tr>
<td>Formato de data [<I>opcional</I>]</td>
<td>Se o token de data é usado no caminho do prefixo, você pode selecionar o formato de data em que os arquivos são organizados. Exemplo: DD/MM/AAAA</td>
</tr>
<tr>
<td>Formato de hora [<I>opcional</I>]</td>
<td>Se o token de tempo é usado no caminho do prefixo, especifique o formato de hora em que os arquivos são organizados. Atualmente o único valor com suporte é HH.</td>
</tr>
<tr>
<td>Formato de serialização de evento</td>
<td>Formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro.</td>
</tr>
<tr>
<td>Codificação</td>
<td>Se o formato CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Só aplicável para serialização CSV. Análise de fluxo oferece suporte a um número de delimitadores comuns para publicar série de dados CSV. Valores com suporte são vírgula, ponto e vírgula, espaço, tabulação e barra vertical.</td>
</tr>
<tr>
<td>Formato</td>
<td>Só aplicável para serialização JSON. Linha separada Especifica que a saída seja formatada fazendo com que cada objeto JSON separado por uma nova linha. Matriz Especifica que a saída seja formatada como uma matriz de objetos JSON.</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>Renovar dados Lucerne Store autorização

Atualmente, não há uma limitação onde o token de autenticação precisa ser manualmente atualizado cada 90 dias para todos os trabalhos com saída de dados Lucerne Store. Você também precisará autenticar novamente a sua conta de armazenamento de Lucerne de dados se você tiver alterado sua senha, desde que seu trabalho foi criado ou última autenticado. Um sintoma desse problema é nenhuma saída de trabalho e um erro em Logs de operação indicando a necessidade de autorização novamente.

Para resolver esse problema, interromper o seu trabalho em execução e vá para a saída de dados Lucerne Store. Clique no link "Renovação autorização" e por um breve tempo uma página surgirá indicando "Redirecionamento autorização...". A página fechará automaticamente e se bem-sucedida, indicará "Autorização tem foi renovada com êxito". Em seguida, você precisa clique em "Salvar" na parte inferior da página e pode prosseguir reiniciando seu trabalho desde a última vez interrompido para evitar a perda de dados.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)
