<properties
   pageTitle="Introdução ao Microsoft Power BI inserida"
   description="Power BI inserida, adicione relatórios interativos do Power BI em seu aplicativo de inteligência de negócios"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="get-started-with-microsoft-power-bi-embedded"></a>Introdução ao Microsoft Power BI inserida

**Power BI incorporado** é um serviço Azure que permite que os desenvolvedores adicionem relatórios interativos do Power BI em seus próprios aplicativos. **Power BI incorporado** funciona com aplicativos existentes sem precisar reformulação ou alterando os maneira como os usuários entrar.

Recursos para o **Microsoft Power BI inserida** são provisionados através de do [Azure BRAÇO APIs](https://msdn.microsoft.com/library/mt712306.aspx). Nesse caso, o recurso que você provisionar é uma **Coleção de espaço de trabalho do Power BI**.

![](media\power-bi-embedded-get-started\introduction.png)

## <a name="create-a-workspace-collection"></a>Criar um conjunto de espaço de trabalho
Uma **Coleção de espaço de trabalho** é o recurso de Azure nível superior e um contêiner para o conteúdo que será incorporado em seu aplicativo. Uma **Coleção de espaço de trabalho** podem ser criadas de duas maneiras:

   -    Manualmente usando o Portal do Azure
   -    Programaticamente usando as APIs de Manager(ARM) de recurso do Azure

Vamos percorrer as etapas para criar uma **Coleção de espaço de trabalho** usando o Portal do Azure.

   1.   Abrir e entrar no **Portal do Azure**: [http://portal.azure.com](http://portal.azure.com).

   2.   Clique em **+ nova** no painel superior.

       ![](media\power-bi-embedded-get-started\create-workspace-1.png)

   3.   Em **dados + Analytics** clique **Power BI inserida**.
   4.   Na **Criação Blade**, insira as informações necessárias. Para **preços**, consulte [preços Power BI inserida](http://go.microsoft.com/fwlink/?LinkID=760527).

       ![](media\power-bi-embedded-get-started\create-workspace-2.png)

   5. Clique em **criar**.

A **Coleção de espaço de trabalho** levará alguns momentos para provisionar. Quando concluído, você será levado **Blade de conjunto de espaço de trabalho**.

   ![](media\power-bi-embedded-get-started\create-workspace-3.png)

A **Criação de Blade** contém as informações que você precisa chamar as APIs que criar espaços de trabalho e implantar conteúdo a elas.

<a name="view-access-keys"/>
## <a name="view-power-bi-api-access-keys"></a>Teclas de acesso de API do modo de exibição Power BI

Uma das partes mais importantes de informações necessárias para chamar as APIs REST do Power BI são as **Teclas de acesso**. Essas são usadas para gerar os **tokens de aplicativo** que são usados para autenticar suas solicitações de API. Para exibir as **Teclas de acesso**, clique em **Teclas de acesso** no **Blade de configurações**. Para obter mais informações sobre **tokens de aplicativo**, consulte [autenticando e autorizar com Power BI inserida](power-bi-embedded-app-token-flow.md).

   ![](media\power-bi-embedded-get-started\access-keys.png)

Você vai ' aviso que você tem duas chaves.

   ![](media\power-bi-embedded-get-started\access-keys-2.png)

Copie estas teclas e armazená-los com segurança em seu aplicativo. É muito importante que você trata essas chaves como faria com uma senha, pois será fornecem acesso a todo o conteúdo no seu **Conjunto de espaço de trabalho**.

Enquanto duas chaves estão listadas, apenas uma chave é necessária em um momento específico. A segunda chave é fornecida para que você periodicamente pode gerar chaves sem interromper o acesso ao serviço.

Agora que você tem uma instância do Power BI para seu aplicativo e **As teclas de acesso**, você pode importar um relatório para seu próprio aplicativo. Antes de aprender como importar um relatório, a próxima seção descreve criando conjuntos de dados do Power BI e relatórios para incorporar um aplicativo.

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app"></a>Criar conjuntos de dados do Power BI e relatórios para incorporar um aplicativo

Agora que você criou uma instância do Power BI para o seu aplicativo e tem **Teclas de acesso**, você precisará criar os conjuntos de dados do Power BI e relatórios que você deseja inserir. Conjuntos de dados e relatórios podem ser criados usando o **Power BI Desktop**. Você pode baixar o [Power BI Desktop para liberar](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/). Ou, para começar rapidamente, você pode baixar o [varejo análise amostra PBIX] (http://go.microsoft.com/fwlink/?LinkID=780547). Para saber mais sobre como usar a **Área de trabalho do Power BI**, consulte [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/en-us/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Com o **Power BI Desktop**, você se conectar à fonte de dados importando uma cópia dos dados no **Power BI Desktop** ou conectar-se diretamente à fonte de dados usando o **DirectQuery**.

Aqui estão as diferenças entre usar **importação** e **DirectQuery**.

|Importar | DirectQuery
|---|---
|Tabelas, colunas *e os dados* serão importadas ou copiadas para a **Área de trabalho do Power BI**. Enquanto você trabalha com visualizações, o **Power BI Desktop** consulta uma cópia dos dados. Para ver as alterações que ocorreram os dados subjacentes, você deve atualizar ou importar, um completo atual dataset novamente.|Somente *tabelas e colunas* são importadas ou copiado para a **Área de trabalho do Power BI**. Enquanto você trabalha com visualizações, o **Power BI Desktop** consulta a fonte de dados subjacente, o que significa sempre que estiver visualizando dados atuais.

Para obter mais informações sobre como se conectar à fonte de dados, consulte [conectar a uma fonte de dados](power-bi-embedded-connect-datasource.md).

Depois de salvar seu trabalho na **Área de trabalho do Power BI**, um arquivo PBIX é criado. Este arquivo contém seu relatório. Além disso, se você importar dados a PBIX contém o conjunto de dados completo, ou se você usar **DirectQuery**, o PBIX contém apenas um esquema de conjunto de dados. Você implanta o PBIX programaticamente em seu espaço de trabalho usando a [API de importação do Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [AZURE.NOTE] **Power BI inserido** tem APIs adicionais para alterar o servidor e o banco de dados que aponta para o conjunto de dados e definir uma credencial de conta de serviço que o dataset usará para se conectar ao seu banco de dados. Consulte [SetAllConnections de postagem](https://msdn.microsoft.com/library/mt711505.aspx) e [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="next-steps"></a>Próximas etapas
Nas etapas anteriores, você criou um conjunto de espaço de trabalho e o primeiro relatório e dataset. Agora é hora de aprender como escrever código para **Power BI inserida**. Para ajudá-lo a começar, criamos um aplicativo da web de exemplo: [começar a usar a amostra](power-bi-embedded-get-started-sample.md). O exemplo mostra como para:

  - Provisionar conteúdo
      - Criar um espaço de trabalho
      - Importar um arquivo PBIX
      - Atualizar cadeias de caracteres de conexão e definir credenciais para seus conjuntos de dados.

  - Incorporar com segurança um relatório

## <a name="see-also"></a>Consulte também
- [Começar a usar o exemplo](power-bi-embedded-get-started-sample.md)
- [Autenticação e autorização com o Power BI inserida](power-bi-embedded-app-token-flow.md)
- [Área de trabalho do Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
