<properties
    pageTitle="Como usar a biblioteca de cliente de aplicativos de celular Android"
    description="Como usar o SDK cliente do Android para aplicativos do Azure móvel."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>


# <a name="how-to-use-the-android-client-library-for-mobile-apps"></a>Como usar a biblioteca de cliente Android para aplicativos móvel

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia mostra como usar o SDK para aplicativos móvel de cliente Android para implementar cenários comuns, como:

- Consultando dados (Inserir, atualizar e excluir).
- Autenticação.
- Tratamento de erros.
- Personalizando o cliente. 

Ele também faz uma análise aprofundada em código de cliente comum usado em aplicativos móveis mais.

Este guia se concentra nos SDK Android do lado do cliente.  Para saber mais sobre o SDK do lado do servidor para aplicativos Mobile, consulte [trabalhar com .NET back-end SDK] [ 10] ou [como usar o back-end node SDK][11].

## <a name="reference-documentation"></a>Documentação de referência

Você pode encontrar a [referência à API de Javadocs] [ 12] para a biblioteca de cliente Android no GitHub.

## <a name="supported-platforms"></a>Plataformas com suporte

O SDK do Azure Mobile aplicativos Android suporta níveis de API 19 a 24 (KitKat por meio de Nougat).  

A autenticação de "server-fluxo" usa uma exibição da Web para a interface do usuário apresentada. Se o dispositivo não for capaz de apresentar uma UI WebView, outros métodos de autenticação são necessários que está fora do escopo do produto.  Esse SDK não é adequado para o tipo de inspeção ou da mesma forma dispositivos restritos.

## <a name="setup-and-prerequisites"></a>Pré-requisitos e configuração

Conclua o tutorial de [início rápido de aplicativos Mobile](app-service-mobile-android-get-started.md) .  Esta tarefa garante que todos os pré-requisitos para desenvolvimento de aplicativos do Azure Mobile foram atendidos.  O início rápido também ajuda você a configurar sua conta e criar sua primeira back-end do aplicativo Mobile.

Se você decidir não concluir o tutorial de início rápido, execute as seguintes tarefas:

- [criar um back-end do aplicativo Mobile] [ 13] para usar com o seu aplicativo do Android.
- No Android Studio, [atualizar os arquivos de compilação Gradle](#gradle-build).
- [Ativar a permissão de internet](#enable-internet).

###<a name="gradle-build"></a>Atualizar o arquivo de compilação Gradle

Altere os dois arquivos de **build.gradle** :

1. Adicione este código ao arquivo de nível **build.gradle** de *projeto* dentro da marca de *buildscript* :

        buildscript {
            repositories {
                jcenter()
            }
        }

2. Adicione este código para o arquivo de nível **build.gradle** de *aplicativo de módulo* dentro da marca de *dependências* :

        compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

    Atualmente, a versão mais recente está 3.1.0. As versões com suporte são listadas [aqui][14].

###<a name="enable-internet"></a>Ativar a permissão de internet

Para acessar o Azure, seu aplicativo deve ter a permissão de INTERNET ativada. Se ele ainda não estiver habilitado, adicione a seguinte linha de código ao seu arquivo de **AndroidManifest.xml** :

    <uses-permission android:name="android.permission.INTERNET" />

## <a name="the-basics-deep-dive"></a>O detalhamento de Noções básicas

Esta seção descreve algumas do código no aplicativo do início rápido que diz respeito ao utilizar aplicativos do Azure Mobile.  

###<a name="data-object"></a>Definir classes de dados do cliente

Para acessar dados de tabelas do SQL Azure, defina classes de dados do cliente que correspondem às tabelas do aplicativo Mobile back-end. Exemplos neste tópico pressupõem uma tabela chamada **ToDoItem**, que tem as seguintes colunas:

- ID
- texto
- Concluir

O correspondente digitados objeto do lado do cliente:

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
    }

O código reside em um arquivo denominado **ToDoItem.java**.

Se sua tabela do SQL Azure contém mais colunas, você adiciona os campos correspondentes a essa classe.  Por exemplo, se o DTO (objeto de transferência de dados) tinha uma coluna de prioridade inteiro, em seguida, você pode adicionar este campo, juntamente com seus métodos getter e setter:

    private Integer priority;

    /**
    * Returns the item priority
    */
    public Integer getPriority() {
        return mPriority;
    }
    
    /**
    * Sets the item priority
    *
    * @param priority
    *            priority to set
    */
    public final void setPriority(Integer priority) {
        mPriority = priority;
    }

Para saber como criar tabelas adicionais no seu back-end aplicativos Mobile, consulte [como: definir um controlador de tabela] [ 15] (.NET back-end) ou [Definir tabelas usando um esquema dinâmico] [ 16] (Node back-end). Para um back-end Node, você também pode usar a configuração de **tabelas fácil** no [portal do Azure].

###<a name="create-client"></a>Como: criar o contexto de cliente

Este código cria o objeto de **MobileServiceClient** que é usado para acessar seu back-end do aplicativo Mobile. O código entra no `onCreate` método da classe de **atividade** especificada no *AndroidManifest.xml* como um ação **principal** e uma categoria de **INICIALIZADOR** . No código de início rápido, ele vai no arquivo **ToDoActivity.java** .

        MobileServiceClient mClient = new MobileServiceClient(
            "MobileAppUrl", // Replace with the Site URL
            this)

Nesse código, substitua `MobileAppUrl` com a URL do seu back-end do aplicativo móvel, que podem ser encontradas no [portal do Azure] na lâmina para seu back-end do aplicativo Mobile. Para essa linha de código para compilar, você também precisa adicionar a seguinte instrução **Importar** :

    import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>Como: criar uma referência de tabela

A maneira mais fácil para consultar ou modificar dados back-end é usando a caixa de seleção *digitada modelo de programação*, como Java é uma linguagem digitada. Esse modelo proporciona perfeita serialização JSON e desserialização usando o [gson] [ 3] biblioteca ao enviar dados entre tabelas e objetos de cliente no back-end SQL Azure.

Para acessar uma tabela, primeiro crie um [MobileServiceTable] [ 8] objeto chamando a **pode ser obtida** método na [MobileServiceClient][9].  Esse método tem duas sobrecargas:

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

No código a seguir, **mClient** é uma referência ao seu objeto MobileServiceClient.  A primeira sobrecarga é usada onde o nome da classe e o nome da tabela são os mesmos e é aquele usado no início rápido:

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

A segunda sobrecarga é usada quando o nome da tabela é diferente do nome da classe: o primeiro parâmetro é o nome da tabela.

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>Como: vincular dados a interface do usuário

Vinculação de dados envolve três componentes:

- A fonte de dados
- O layout da tela
- O adaptador une os dois.

Em nosso código de amostra, podemos retornar os dados da tabela do SQL Azure Mobile aplicativos **ToDoItem** em uma matriz. Esta atividade é um padrão comum para aplicativos de dados.  Consultas de banco de dados com frequência retornar um conjunto de linhas que o cliente obtém uma lista ou matriz. Neste exemplo, a matriz é a fonte de dados.

O código especifica um layout de tela que define o modo de exibição de dados que aparece no dispositivo.  Os dois estão vinculados junto com um adaptador, que é uma extensão nesse código do **ArrayAdapter&lt;ToDoItem&gt; ** classe.

#### <a name="layout"></a>Como: definir o Layout

O layout é definido por vários trechos de código XML. Dado um layout existente, o seguinte código representa **ListView** queremos preencher com nossos dados de servidor.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

No código anterior, o atributo de *listitem* Especifica a id do layout de uma linha individual na lista. Este código especifica uma caixa de seleção e seu texto associado e obtém criado uma vez para cada item na lista. Este layout não exibe o campo **id** e um layout mais complexo seria especificar campos adicionais na exibição. Este código é no arquivo **row_list_to_do.xml** .

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal">
        <CheckBox
            android:id="@+id/checkToDoItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/checkbox_text" />
    </LinearLayout>


#### <a name="adapter"></a>Como: definir o adaptador

Como a fonte de dados de nosso modo de exibição é uma matriz de **ToDoItem**, podemos subclasse nosso adaptador de um **ArrayAdapter&lt;ToDoItem&gt; ** classe. Essa subclasse produz um modo de exibição para cada **ToDoItem** usando o layout de **row_list_to_do** .

Em nosso código definimos a seguinte classe que é uma extensão do **ArrayAdapter&lt;E&gt; ** classe:

    public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

Substitua o método de **getView** adaptadores. Por exemplo:

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }

        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


        return row;
    }

Criamos uma instância dessa classe em nossa atividade da seguinte maneira:

    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

O segundo parâmetro para o construtor de ToDoItemAdapter é uma referência para o layout. Agora podemos criar uma instância **ListView** e atribuir o adaptador a **ListView**.

    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>A estrutura de API

Operações de tabela de aplicativos Mobile e chamadas de API personalizadas são assíncronas. Use os objetos [futuro] e [AsyncTask] para os métodos assíncronos envolvendo consultas, inserções, atualizações e exclusões. Usar futuros facilita realizar várias operações em um segmento de plano de fundo sem ter de lidar com vários retornos de chamadas aninhados.

Revise o arquivo de **ToDoActivity.java** do projeto do início rápido Android a partir do [portal do Azure] para obter um exemplo.

#### <a name="use-adapter"></a>Como: usar o adaptador

Agora você está pronto para usar associação de dados. O código a seguir mostra como acessar itens na tabela e preenche o adaptador local com os itens devolvidos.

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }

Ligue o adaptador qualquer momento que você modificar a tabela de **ToDoItem** . Como modificações terminar em cada registro por registro, você lidar com uma única linha em vez de um conjunto. Quando você insere um item, chame o método **add** no adaptador; ao excluir, chame o método **remove** .

##<a name="querying"></a>Como: consultar dados do seu back-end do aplicativo móvel

Esta seção descreve como emitir consultas no aplicativo móvel back-end, que inclui as seguintes tarefas:

- [Retornar todos os itens]
- [Filtrar os dados retornados]
- [Classificar dados retornado]
- [Retornar dados nas páginas]
- [Selecionar colunas específicas]
- [Concatenar métodos de consulta](#chaining)

### <a name="showAll"></a>Como: retornar todos os itens de uma tabela

A seguinte consulta retorna todos os itens na tabela **ToDoItem** .

    List<ToDoItem> results = mToDoTable.execute().get();

A variável de *resultados* retorna o conjunto de resultados da consulta como uma lista.

### <a name="filtering"></a>Como: filtrar dados retornados

A execução de consulta a seguir retorna todos os itens da tabela **ToDoItem** onde **concluída** é igual a **false**.

    List<ToDoItem> result = mToDoTable.where()
                                .field("complete").eq(false)
                                .execute().get();

**mToDoTable** é a referência à tabela de serviço móvel que criamos anteriormente.

Defina um filtro usando a chamada do método **onde** na referência de tabela. O método **onde** é seguido por um **campo** método seguido por um método que especifica o predicado lógico. Métodos de predicado possíveis incluem **eq** (igual a), **ne** (não igual), **gt** (maior que), **ge** (maior que ou igual a), **lt** (menor que), **entidade legal** (menor ou igual a). Esses métodos permitem que você compare os campos número e a cadeia de caracteres para valores específicos.

Você pode filtrar datas. Os seguintes métodos permitem que você compare o campo de data inteiro ou partes da data: **ano**, **mês**, **dia**, **hora**, **minuto**e **segundo**. O exemplo a seguir adiciona um filtro para itens cuja *Data de conclusão* é igual a 2013.

    mToDoTable.where().year("due").eq(2013).execute().get();

Os seguintes métodos oferece suporte a filtros complexos em campos de cadeia de caracteres: **startsWith**, **endsWith**, **concat**, **subcadeia**, **indexOf**, **Substituir**, **toLower**, **toUpper**, **Cortar**e **comprimento**. O exemplo a seguir filtra linhas da tabela onde a coluna de *texto* começa com "PRI0".

    mToDoTable.where().startsWith("text", "PRI0").execute().get();

Os seguintes métodos de operador são suportados em campos número: **Adicione**, **sub**, **mul**, **div**, **mod**, **plantas**, **teto**e **Arredondar**. O exemplo a seguir filtra linhas da tabela onde a **duração** é um número par.

    mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

Você pode combinar predicados com esses métodos lógicos: **e**, **ou** e **não**. O exemplo a seguir combina dois dos exemplos anteriores.

    mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
                .execute().get();

Agrupar e aninhar operadores lógicos:

    mToDoTable.where()
                .year("due").eq(2013)
                    .and
                (startsWith("text", "PRI0").or().field("duration").gt(10))
                .execute().get();

Para informações mais detalhadas sobre e exemplos de filtragem, consulte [Explorando a sofisticação do modelo de consulta de cliente Android](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Como: classificar dados retornados

O código a seguir retorna que todos os itens de uma tabela de **ToDoItems** classificado em ordem crescente por campo de *texto* . *mToDoTable* é a referência à tabela de dados back-end que você criou anteriormente:

    mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

O primeiro parâmetro do método **orderBy** é uma cadeia de caracteres igual ao nome do campo no qual classificar. O segundo parâmetro usa a enumeração **QueryOrder** para especificar se deseja classificar em ordem crescente ou decrescente.  Se você estiver filtrando usando o método ***onde*** , o método ***onde*** deve ser chamado antes do método ***orderBy*** .

### <a name="paging"></a>Como: retornar dados em páginas

O primeiro exemplo mostra como selecionar os primeiros cinco itens de uma tabela. A consulta retorna os itens de uma tabela de **ToDoItems**. **mToDoTable** é a referência à tabela de dados back-end que você criou anteriormente:

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Aqui está uma consulta que ignora os cinco primeiros itens e, em seguida, retorna os cinco próxima:

    mToDoTable.skip(5).top(5).execute().get();

### <a name="selecting"></a>Como: selecionar colunas específicas

O código a seguir ilustra como retornar todos os itens de uma tabela de **ToDoItems**, mas exibe apenas os campos **concluída** e **texto** . **mToDoTable** é a referência à tabela de dados back-end que criamos anteriormente.

    List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();

Os parâmetros para a função select são os nomes de cadeia de caracteres de colunas da tabela que você deseja retornar.

O método **select** precisa acompanhar métodos como **onde** e **orderBy**. Ele pode ser seguido por métodos de paginação como **superior**.

### <a name="chaining"></a>Como: concatenar métodos de consulta

Os métodos usados em consultas de tabelas de back-end podem ser concatenados. Encadeamento métodos de consulta permite que você selecione colunas específicas de linhas filtradas que são classificadas e paginação. Você pode criar filtros lógicos complexos.
Cada método de consulta retorna um objeto de consulta. Para encerrar a série de métodos e realmente executar a consulta, chame o método **Executar** . Por exemplo:

    mToDoTable.where()
        .year("due").eq(2013)
        .and().startsWith("text", "PRI0")
        .or().field("duration").gt(10)
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .top(20)
        .execute().get();

Os métodos de cadeia de consulta devem ser ordenados da seguinte maneira:

1. Métodos de filtragem (**onde**).
2. Métodos de classificação (**orderBy**).
3. Métodos de seleção (**Selecionar**).
4. métodos paginação (**Ignorar** e **superior**).

##<a name="inserting"></a>Como: inserir dados em back-end

Criar uma instância da classe *ToDoItem* e defina suas propriedades.

    ToDoItem item = new ToDoItem();
    item.text = "Test Program";
    item.complete = false;

Use **Insert ()** para inserir um objeto:

    ToDoItem entity = mToDoTable.insert(item).get();

As correspondências de entidade retornada os dados inseridos na tabela de back-end, incluída a identificação e qualquer outro conjunto de valores no back-end.

Tabelas de aplicativos Mobile requerem uma coluna de chave primária chamada **id**. Por padrão, esta coluna é uma cadeia de caracteres. O valor padrão da coluna ID é um GUID.  Você pode fornecer outros valores exclusivos, como endereços de email ou nomes de usuário. Quando um valor de cadeia de caracteres de identificação não for fornecido para um registro de inserido, back-end gera um novo GUID.

Valores de ID de cadeia de caracteres fornecem as seguintes vantagens:

+ IDs podem ser gerados sem fazer uma viagem ao banco de dados.
+ Registros são mais fáceis de mesclar a partir de diferentes tabelas ou bancos de dados.
+ Valores de ID se integram melhor a lógica de um aplicativo.

Valores de ID de cadeia de caracteres são **necessárias** para o suporte de sincronização offline.

##<a name="updating"></a>Como: atualizar dados em um aplicativo móvel

Para atualizar dados em uma tabela, passe o novo objeto para o método **Update ()** .

    mToDoTable.update(item).get();

Neste exemplo, o *item* é uma referência a uma linha na tabela *ToDoItem* , que teve algumas alterações feitas a ele.
A linha com a mesma **id** é atualizada.

##<a name="deleting"></a>Como: excluir dados em um aplicativo móvel

O código a seguir mostra como excluir dados de uma tabela, especificando o objeto de dados.

    mToDoTable.delete(item);

Você também pode excluir um item, especificando o campo **id** da linha para excluir.

    String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
    mToDoTable.delete(myRowId);

##<a name="lookup"></a>Como: procurar um item específico

Procure um item com um campo específico **id** com o método **Lookup ()** :

    ToDoItem result = mToDoTable
                        .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
                        .get();

##<a name="untyped"></a>Como: trabalhar com dados sem tipo

O modelo de programação sem tipo dá controle exato sobre serialização JSON.  Existem alguns cenários comuns onde você poderá usar um modelo de programação sem tipo. Por exemplo, se a tabela de back-end contiver várias colunas e você só precisará fazer referência um subconjunto das colunas.  O modelo digitado requer que você defina todos os aplicativos móveis colunas da tabela em sua classe de dados.  

A maioria das chamadas API para acessar dados são semelhante às digitadas chamadas programação. A principal diferença é no modelo sem chamar métodos no objeto **MobileServiceJsonTable** , em vez de no objeto **MobileServiceTable** .

### <a name="json_instance"></a>Como: criar uma instância de uma tabela sem tipo

Semelhante ao modelo de digitado, você comece obtendo uma referência de tabela, mas nesse caso é um objeto **MobileServicesJsonTable** . Obtenha a referência chamando a **pode ser obtida** método em uma instância do cliente:

    private MobileServiceJsonTable mJsonToDoTable;
    //...
    mJsonToDoTable = mClient.getTable("ToDoItem");

Depois de ter criado uma instância do **MobileServiceJsonTable**, praticamente tem a mesma API disponível como com o modelo de programação digitado. Em alguns casos, os métodos levar um parâmetro sem tipo em vez de um parâmetro digitado.

### <a name="json_insert"></a>Como: inserir em uma tabela sem tipo

O código a seguir mostra como fazer um insert. A primeira etapa é criar uma [JsonObject][1], que faz parte do [gson] [ 3] biblioteca.

    JsonObject jsonItem = new JsonObject();
    jsonItem.addProperty("text", "Wake up");
    jsonItem.addProperty("complete", false);

Em seguida, Use **Insert ()** para inserir o objeto sem na tabela.

    mJsonToDoTable.insert(jsonItem).get();

Se você precisar obter a ID do objeto inserido, use o método **getAsJsonPrimitive()** .

    jsonItem.getAsJsonPrimitive("id").getAsInt());

### <a name="json_delete"></a>Como: excluir de uma tabela sem tipo

O código a seguir mostra como excluir uma instância, nesse caso, a mesma instância de um **JsonObject** que foi criado no exemplo anterior *Inserir* . O código é a mesma que com as maiusculas e minúsculas digitada, mas o método tem uma assinatura diferente, desde que ela faz referência a um **JsonObject**.

         mToDoTable.delete(jsonItem);

Você também pode excluir uma instância diretamente usando sua ID:

         mToDoTable.delete(ID);

### <a name="json_get"></a>Como: retornar todas as linhas de uma tabela sem tipo

O código a seguir mostra como recuperar uma tabela inteira. Como você está usando uma tabela de JSON, você pode seletivamente recuperar apenas algumas das colunas da tabela.

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

O mesmo conjunto de filtragem, filtragem e paginação métodos que estão disponíveis para o modelo digitado estão disponíveis para o modelo sem tipo também.

##<a name="custom-api"></a>Como: chamar uma API personalizada

Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapear para uma inserção, atualizar, excluir ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definindo cabeçalhos de mensagem HTTP e definir um formato de corpo da mensagem diferente JSON.

Em um cliente Android, você chamar o método de **invokeApi** para chamar o ponto de extremidade de API personalizado. O exemplo a seguir mostra como chamar um ponto de extremidade de API chamado **completeAll**, que retorna uma classe coleção chamada **MarkAllResult**.

    public void completeItem(View view) {

        ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

            Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }

                @Override
                public void onSuccess(MarkAllResult result) {
                    createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
                    refreshItemsFromTable();
                }
            });
        }

O método **invokeApi** é chamado no cliente, que envia uma solicitação de POSTAGEM para a nova API personalizada. O resultado retornado pela API personalizada é exibido em uma caixa de diálogo de mensagem, como erros. Outras versões do **invokeApi** permitem que você enviar um objeto no corpo da solicitação opcionalmente, especificar o método de HTTP e enviar parâmetros de consulta com a solicitação. Sem versões do **invokeApi** são fornecidas também.

##<a name="authentication"></a>Como: adicionar autenticação ao seu aplicativo

Tutoriais já descrevem detalhadamente como adicionar esses recursos.

Serviço de aplicativo oferece suporte a [autenticação de usuários do aplicativo](app-service-mobile-android-get-started-users.md) usando uma variedade de provedores de identidade externa: Facebook, Google, Account da Microsoft, Twitter e Azure Active Directory. Você pode definir permissões em tabelas para restringir o acesso para operações específicas somente para usuários autenticados. Você também pode usar a identidade dos usuários autenticados implementar regras de autorização no seu back-end.

Dois fluxos de autenticação com suporte: um fluxo de **servidor** e um fluxo de **cliente** . O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface de web de provedores de identidade.  Nenhum SDKs adicionais são necessários para implementar a autenticação de fluxo de servidor. Autenticação de fluxo de servidor não fornece uma integração profunda no dispositivo móvel e só é recomendada para prova de cenários de conceito.

O fluxo de cliente possibilita maior integração com recursos específicos para dispositivos como o logon único, pois depende de SDKs fornecidos pelo provedor de identidade.  Por exemplo, você pode integrar o SDK do Facebook ao seu aplicativo móvel.  O cliente móvel troca no aplicativo do Facebook e confirma seu logon antes de troca de volta para seu aplicativo móvel.

Quatro etapas são necessárias para habilitar a autenticação em seu aplicativo:

- Registre seu aplicativo para autenticação com um provedor de identidade.
- Configure seu back-end do serviço de aplicativo.
- Restringir permissões de tabela para usuários autenticados somente no serviço de aplicativo back-end.
- Adicione código de autenticação para o seu aplicativo.

Você pode definir permissões em tabelas para restringir o acesso para operações específicas somente para usuários autenticados. Você também pode usar o SID de um usuário autenticado para modificar solicitações.  Para obter mais informações, examine [começar com a autenticação] e a documentação do servidor SDK como.

### <a name="caching"></a>Como: adicionar código de autenticação para o seu aplicativo

O código a seguir inicia um processo de logon do fluxo de servidor usando o provedor do Google:

    MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Obtenha o ID do usuário conectado de um **MobileServiceUser** usando o método **getUserId** . Para obter um exemplo de como usar futuros para chamar o logon assíncrono APIs, consulte [Introdução ao autenticação].

### <a name="caching"></a>Como: Cache tokens de autenticação

Cache tokens de autenticação requer que você armazenar a ID de usuário e o símbolo de autenticação localmente no dispositivo. Na próxima vez que o aplicativo for iniciado, você verificará o cache e se esses valores estiverem presentes, você pode ignorar o procedimento de login e realimentar o cliente com esses dados. No entanto, esses dados são confidenciais e ele deve ser armazenado criptografados para segurança, caso o telefone for roubado.

Você pode ver um exemplo de como concluído tokens de autenticação de cache na [seção de tokens de autenticação de Cache][7].

Quando você tenta usar um token expirado, você recebe uma resposta *401 não autorizado* . Você pode manipular os erros de autenticação usando filtros.  Filtros de interceptam solicitações no back-end do serviço de aplicativo. O código de filtro testa a resposta para uma 401, aciona o processo de entrada e, em seguida, currículos solicitação que gerou a 401. 

## <a name="adal"></a>Como: autenticar usuários com a biblioteca de autenticação do Active Directory

Você pode usar a biblioteca de autenticação do Active Directory (ADAL) entre os usuários em seu aplicativo usando o Active Directory do Azure. Usando um logon de fluxo de cliente geralmente é preferível usando o `loginAsync()` métodos como ela fornece uma aparência mais nativa do UX e permite a personalização adicional.

1. Configure seu back-end do aplicativo móvel para entrar no AAD seguindo o tutorial de [como configurar o serviço de aplicativo para o login no Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) . Certifique-se concluir a etapa opcional de registro de um aplicativo cliente nativo.

2. Instale o ADAL modificando seu arquivo de build.gradle para incluir as seguintes definições:

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

3. Adicione o seguinte código ao seu aplicativo, fazendo as seguintes substituições:

* Substitua o nome do locatário na qual você provisionado seu aplicativo **Inserir-autoridade-HERE** . O formato deve ser https://login.windows.net/contoso.onmicrosoft.com. Este valor pode ser copiado a partir da guia de domínio no seu Azure Active Directory no [Azure clássico portal].

* Substitua **Inserir-recurso-ID-HERE** com a ID do cliente para seu back-end do aplicativo móvel. Você pode obter a ID do cliente da guia **Avançado** em **Configurações do Azure Active Directory** no portal.

* Substitua **Inserir-cliente-ID-HERE** com a ID do cliente que você copiou do aplicativo cliente nativo.

* Substitua **Inserir-REDIRECT-URI-HERE** com ponto de extremidade de _/.auth/login/done_ do seu site, usando o esquema HTTPS. Este valor deve ser semelhante ao _https://contoso.azurewebsites.net/.auth/login/done_.

        private AuthenticationContext mContext;

        private void authenticate() {
            String authority = "INSERT-AUTHORITY-HERE";
            String resourceId = "INSERT-RESOURCE-ID-HERE";
            String clientId = "INSERT-CLIENT-ID-HERE";
            String redirectUri = "INSERT-REDIRECT-URI-HERE";
            try {
                mContext = new AuthenticationContext(this, authority, true);
                mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
            } catch (Exception exc) {
                exc.printStackTrace();
            }
        }

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    Log.d(TAG, "Cancelled");
                } else {
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    Log.d(TAG, "Token is empty");
                } else {
                    try {
                        JSONObject payload = new JSONObject();
                        payload.put("access_token", result.getAccessToken());
                        ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                        Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                            @Override
                            public void onFailure(Throwable exc) {
                                exc.printStackTrace();
                            }
                            @Override
                            public void onSuccess(MobileServiceUser user) {
                                Log.d(TAG, "Login Complete");
                            }
                        });
                    }
                    catch (Exception exc){
                        Log.d(TAG, "Authentication error:" + exc.getMessage());
                    }
                }
            }
        };

        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
            }
        }

## <a name="how-to-add-push-notification-to-your-app"></a>Como: adicionar notificação de envio para o seu aplicativo

Você pode [ler uma visão geral] [ 6] que descreve como Hubs de notificação do Microsoft Azure oferece suporte a uma ampla variedade de notificações de envio.  [Neste]tutorial[5], uma notificação de envio é enviada para todos os dispositivos sempre que um registro é inserido.

## <a name="how-to-add-offline-sync-to-your-app"></a>Como: Adicionar sincronização offline para o seu aplicativo

O tutorial de início rápido contém código que implementa sincronização offline. Procure o prefixo com comentários de código:

    // Offline Sync

Por uncommenting as seguintes linhas de código, você pode implementar sincronização offline, e você pode adicionar código semelhante a outros códigos de aplicativos Mobile.

##<a name="customizing"></a>Como: personalizar o cliente

Há várias maneiras para você personalizar o comportamento padrão do cliente.

### <a name="headers"></a>Como: personalizar solicitações de cabeçalhos

Configure um **ServiceFilter** para adicionar um cabeçalho HTTP personalizado a cada solicitação:

    private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                    ServiceFilterRequest request,
                    NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
                    request.addHeader("My-Header", "Value");                    }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>Como: Personalizar serialização

O cliente pressupõe que os nomes de tabela, nomes de coluna e dados digita no back-end todos correspondem exatamente os objetos de dados definidos no cliente. Pode haver vários motivos por que os nomes de cliente e servidor não podem ser uma correspondência. No seu cenário, talvez você queira fazer os seguintes tipos de personalizações:

- Os nomes de coluna usados na tabela de serviço de aplicativo não correspondem aos nomes que você está usando no cliente.
- Use uma tabela de serviço de aplicativo que tenha um nome diferente que a classe que ele mapeia no cliente.
- Ative o uso de maiusculas automática de propriedade.
- Adicione propriedades complexas a um objeto.

### <a name="columns"></a>Como: mapear nomes de cliente e servidor diferentes

Suponha que seu código de cliente Java usa nomes de estilo Java padrão para as propriedades do objeto **ToDoItem** , como as seguintes propriedades:

- mId
- mText
- mComplete
- MDURAÇÃO

Serialize os nomes de cliente em nomes JSON que correspondem aos nomes de coluna da tabela **ToDoItem** no servidor. O código a seguir usa a [gson] [ 3] biblioteca para anotar as propriedades:

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="table"></a>Como: mapear nomes de tabela diferente entre o cliente e o back-end

Mapear o nome de tabela do cliente para o nome de uma tabela de serviços móveis diferentes usando uma substituição do [getTable()] [ 4] método:

    mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="conversions"></a>Como: automatizar mapeamentos de nomes de coluna

Você pode especificar uma estratégia de conversão que se aplica a cada coluna usando o [gson] [ 3] API. A biblioteca de cliente Android usa [gson] [ 3] os bastidores serializar objetos Java dados JSON antes que os dados são enviados para o serviço de aplicativo do Azure.  O código a seguir usa o método **setFieldNamingStrategy()** para definir a estratégia. Este exemplo exclui o caractere inicial (um "m") e, em seguida, minúsculas o próximo caractere, para cada nome de campo. Por exemplo, ele seria transformar "mId" em "id".

    client.setGsonBuilder(
        MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(new FieldNamingStrategy() {
            public String translateName(Field field) {
                String name = field.getName();
                return Character.toLowerCase(name.charAt(1))
                    + name.substring(2);
                }
            });

Este código deve ser executado antes de usar o **MobileServiceClient**.

### <a name="complex"></a>Como: armazenar uma propriedade de objeto ou matriz em uma tabela

Até agora, nossos exemplos de serialização envolvido tipos primitivos como inteiros e cadeias de caracteres.  Tipos primitivos facilmente serializar em JSON.  Se quisermos adicionar um objeto complexo que não serializar automaticamente a JSON, precisamos fornecem o método de serialização JSON.  Para ver um exemplo de como fornecer serialização JSON personalizada, examine a postagem no blog [Personalizando serialização usando a biblioteca de gson no cliente de serviços de celular Android][2].

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Retornar todos os itens]: #showAll
[Filtrar os dados retornados]: #filtering
[Classificar dados retornado]: #sorting
[Retornar dados nas páginas]: #paging
[Selecionar colunas específicas]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Portal do Azure]: https://portal.azure.com
[Introdução ao autenticação]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Futuro]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html