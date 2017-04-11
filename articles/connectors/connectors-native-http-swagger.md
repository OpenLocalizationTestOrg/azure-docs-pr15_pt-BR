
<properties
    pageTitle="Adicionar a HTTP + Swagger ação nos aplicativos de lógica | Microsoft Azure"
    description="Visão geral do HTTP + Swagger ação e operações"
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-http--swagger-action"></a>Começar a usar o HTTP + Swagger ação

Com o HTTP + Swagger ação, você pode criar um conector de primeira classe em qualquer ponto de extremidade restante por meio de um [documento de Swagger](https://swagger.io). Você também pode estender um aplicativo de lógica para chamar qualquer ponto de extremidade restante uma experiência de Designer de aplicativo de lógica de primeira classe.

Para começar a usar o HTTP + Swagger ação em um aplicativo de lógica, consulte [criar um novo aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Use HTTP + Swagger como um disparador ou uma ação

O HTTP + Swagger acionador e ação funcionam da mesma forma a [ação de HTTP](connectors-native-http.md) , mas oferece uma melhor experiência de design mostrando a forma da API e saídas no designer do [Swagger metadados](https://swagger.io). Além disso, você pode usar HTTP + Swagger como um disparador. Se você quiser implementar um gatilho de sondagem, ele deverá seguir o padrão de pesquisa que está descrito na [criação de uma API personalizada para usar com aplicativos de lógica](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers).

[Saiba mais sobre ações e disparadores de aplicativo de lógica.](connectors-overview.md)

Aqui está um exemplo de como usar o HTTP + Swagger operação como uma ação em um fluxo de trabalho em um aplicativo de lógica.

1. Selecione o botão **Nova etapa** .
2. Selecione **Adicionar uma ação**.
3. Na caixa de pesquisa ação, digite **swagger** lista HTTP + Swagger ação.

    ![Selecione HTTP + Swagger ação](./media/connectors-native-http-swagger/using-action-1.png)

4. Digite a URL para um documento de Swagger:
    - Para trabalhar de Designer de aplicativo de lógica, a URL deve ser um ponto de extremidade HTTPS e habilitou CORS.
    - Se o documento de Swagger não atender a esse requisito, você pode usar o [Armazenamento do Azure com CORS habilitado](#hosting-swagger-from-storage) para armazenar um documento.
5. Clique em **próxima** para ler e renderizar do documento Swagger.
6. Adicione todos os parâmetros que são necessários para a chamada HTTP.

    ![Ação de HTTP](./media/connectors-native-http-swagger/using-action-2.png)

1. Clique em **Salvar** no canto superior esquerdo da barra de ferramentas e sua lógica aplicativo salvará os dois e publicar (Ativar).

### <a name="host-swagger-from-azure-storage"></a>Swagger de host de armazenamento do Azure

Talvez você queira fazer referência a um documento de Swagger que não está hospedado ou que não atender a requisitos de segurança e entre origens para o designer. Para resolver esse problema, você pode armazenar um documento Swagger no armazenamento do Azure e habilitar CORS referido no documento.  

Aqui estão as etapas para criar, configurar e armazenar documentos de Swagger no armazenamento do Azure:

1. [Criar uma conta de armazenamento do Azure com armazenamento de Blob do Azure](../storage/storage-create-storage-account.md). (Para fazer isso, defina permissões de **acesso público**).
2. Habilite CORS no blob. Você pode usar [Este script do PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) para configurar essa configuração automaticamente.
3. Carregue o arquivo Swagger em blob. Você pode fazer isso a partir do [portal do Azure](https://portal.azure.com) ou de uma ferramenta como o [Gerenciador de armazenamento do Azure](http://storageexplorer.com/).
1. Fazer referência a um link HTTPS para o documento no armazenamento de Blob do Azure. (O link segue o formato `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`.)



## <a name="technical-details"></a>Detalhes técnicos

A seguir estão os detalhes para os disparadores e as ações que este HTTP + Swagger suportes de conector.

## <a name="http--swagger-triggers"></a>HTTP + Swagger disparadores

Um disparador é um evento que pode ser usado para iniciar o fluxo de trabalho que é definido em um aplicativo de lógica. [Saiba mais sobre gatilhos.](connectors-overview.md) O HTTP + Swagger conector tem um disparador.

|Disparadores|Descrição|
|---|---|
|HTTP + Swagger|Fazer uma chamada de HTTP e retornar o conteúdo de resposta|

## <a name="http--swagger-actions"></a>HTTP + Swagger ações

Uma ação é uma operação que é executada pelo fluxo de trabalho que é definido em um aplicativo de lógica. [Saiba mais sobre ações.](connectors-overview.md) O HTTP + Swagger conector tem uma ação possível.

|Ação|Descrição|
|---|---|
|HTTP + Swagger|Fazer uma chamada de HTTP e retornar o conteúdo de resposta|

### <a name="action-details"></a>Detalhes de ação

O HTTP + Swagger conector vem com uma ação possível. A seguir é informações sobre cada uma das ações, seus campos obrigatórios e opcionais de entrada e os detalhes de saída correspondentes que estão associados ao seu uso.

#### <a name="http--swagger"></a>HTTP + Swagger

Fazer uma solicitação de saída HTTP com assistência de metadados de Swagger.
Um asterisco (*) significa um campo obrigatório.

|Nome para exibição|Nome da propriedade|Descrição|
|---|---|---|
|Método *|método|Verbo HTTP para usar.|
|URI *|URI|URI para a solicitação HTTP.|
|Cabeçalhos|cabeçalhos|Um objeto JSON dos cabeçalhos HTTP para incluir.|
|Corpo|corpo|O corpo da solicitação HTTP.|
|Autenticação|autenticação|Autenticação para usar para a solicitação. [Para obter mais detalhes, consulte HTTP](./connectors-native-http.md#authentication).|

**Detalhes de saída**

Resposta HTTP

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Cabeçalhos|objeto|Cabeçalhos de resposta|
|Corpo|objeto|Objeto de resposta|
|Código de status|int|Código de status HTTP|

### <a name="http-responses"></a>Respostas HTTP

Ao fazer chamadas a várias ações, você pode receber determinadas respostas. A seguir é uma tabela que descreve as descrições e respostas correspondentes.

|Nome|Descrição|
|---|---|
|200|Okey|
|202|Aceita|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido.|

---

## <a name="next-steps"></a>Próximas etapas

Experimente a plataforma e [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) agora. Você pode explorar os outros conectores disponíveis nos aplicativos de lógica examinando nossa [lista de APIs](apis-list.md).
